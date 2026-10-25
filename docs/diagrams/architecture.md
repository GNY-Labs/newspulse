# Architecture diagram

Target architecture for v1 (sprints 0 to 8), with later versions shown as placeholders.

```mermaid
flowchart LR
    subgraph sources[External sources]
        hn[Hacker News]
        rss[RSS/Atom feeds]
        gh[GitHub releases]
    end

    subgraph v1[NewsPulse v1]
        collectors[Collectors]
        api[Ingestion API]
        kafka[[Kafka]]
        consumer[Kafka consumer]
        flink[Flink trend jobs]
        pg[(PostgreSQL)]
    end

    subgraph obs[Observability]
        alloy[Alloy]
        prom[Prometheus]
        loki[Loki]
        grafana[Grafana]
    end

    subgraph later[Later versions]
        ui[Reading interface - v2]
        clf[Classifier - v2]
        tr[Translation - v3]
    end

    hn --> collectors
    rss --> collectors
    gh --> collectors

    collectors -->|POST /articles| api
    api --> kafka
    kafka --> consumer
    consumer --> pg
    kafka --> flink
    flink -->|trends| pg
    api -.->|direct write, until sprint 5| pg

    v1 -->|metrics| prom
    v1 -->|logs| alloy
    alloy --> loki
    prom --> grafana
    loki --> grafana
    pg -->|trends, releases| grafana

    pg -.-> ui
    pg -.-> clf
    pg -.-> tr

    style later stroke-dasharray: 5 5
```

Notes:

- Until sprint 5, the ingestion API writes directly to PostgreSQL (dotted arrow). From sprint 5, articles go through Kafka (FR-09).
- The "Later versions" box is a placeholder: it shows where v2 and v3 plug in, without detailing them. It is updated at the end of each version.