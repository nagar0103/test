flowchart TD

    %% === PDF読み込み ===
    A[PDF読み込み\n/mnt/data/通信線配線表.pdf]
    A --> B

    B[テキスト行抽出]

    %% === 上段処理 ===
    subgraph 上段情報抽出
        direction TB

        B --> C
        C[空行除去 + lines作成]

        C --> D
        D[フッター抽出（図面名）\nlines[-1]]

        C --> E
        E[PKGまで上段行抽出]

        E --> F
        F[tokens生成 → upper_info格納\n(ビル/フロア/群/架/装置/番号)]
    end

    %% === 下段処理 ===
    subgraph 下段情報抽出
        direction TB

        C --> G
        G[PKG行検索\n下段処理開始]

        G --> H
        H[各レコード取得\nPKG/SLOT/対向装置/番号/対向PKG/SLOT/備考]

        H --> I
        I[上段情報 + 図面名付加\n1レコード作成]
    end

    %% === 出力処理 ===
    I --> J
    J[DataFrame化（pandas）]

    J --> K
    K[Excel / JSON 出力]

    %% === 色分け ===
    classDef upper fill:#cce5ff,stroke:#333,stroke-width:1px;
    classDef lower fill:#d4edda,stroke:#333,stroke-width:1px;
    classDef output fill:#fff3cd,stroke:#333,stroke-width:1px;

    class C,D,E,F upper
    class G,H,I lower
    class J,K output

    %% === データ構造 ===
    I --> L
    L[Record構造体\nPKG/SLOT/対向装置/対向番号/\n対向PKG/対向SLOT/備考/\nビル/フロア/群/架/装置/番号/図面名]

    L --> J
