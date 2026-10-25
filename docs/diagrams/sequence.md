# Sequence diagram

Main flow of v1: from a source to a dashboard, including the duplicate case.

```mermaid
sequenceDiagram
    participant S as Source
    participant C as Collector
    participant A as Ingestion API
    participant K as Kafka
    participant Q as Kafka consumer
    participant F as Flink
    participant P as PostgreSQL
    participant G as Grafana

    C->>S: Fetch new items (scheduled run)
    S-->>C: Items

    loop For each item
        C->>A: POST /articles
        A->>P: Look up article by URL
        P-->>A: Known or unknown

        alt Article already known (FR-03)
            A-->>C: Already stored
            Note over A,P: Nothing is published or stored
        else New article
            A->>K: Publish article event
            A-->>C: Accepted
            par Storage
                K->>Q: Article event
                Q->>P: Insert article
            and Trends
                K->>F: Article event
                F->>P: Write trends (day and week windows)
            end
        end
    end

    Note over G,P: Later, when the reader opens a dashboard
    G->>P: Query trends
    P-->>G: Results
```

Notes:

- Until sprint 5, the API writes directly to PostgreSQL instead of publishing to Kafka.
- Grafana pulls data from PostgreSQL when a dashboard is opened; nothing is pushed to it.