flowchart TD
    %% === PDF読み込み ===
    A[PDF読み込み<br>/mnt/data/通信線配線表.pdf] --> B[テキスト行抽出]

    %% === 上段処理 ===
    subgraph 上段情報抽出
        direction TB
        B --> C[空行除去 + linesリスト作成]
        C --> D[フッター抽出（図面名）<br>lines[-1]]
        C --> E[PKGが出るまで上段行抽出]
        E --> F[tokens作成 → upper_infoに格納<br>(ビル, フロア, 群, 架番号, 装置, 装置番号)]
    end

    %% === 下段処理 ===
    subgraph 下段情報抽出
        direction TB
        C --> G[PKG行検索 + 下段処理開始]
        G --> H[各レコード取得<br>PKG, SLOT, 対向装置, 対向番号, 対向PKG, 対向SLOT, 備考]
        H --> I[上段情報 + フッターを追加して1レコード作成]
    end

    %% === 出力処理 ===
    I --> J[DataFrame化（pandas）]
    J --> K[Excel出力 / JSON出力]

    %% === 色分け（flowchartのみ適用） ===
    classDef upper fill:#cce5ff,stroke:#333,stroke-width:1px;
    classDef lower fill:#d4edda,stroke:#333,stroke-width:1px;
    classDef output fill:#fff3cd,stroke:#333,stroke-width:1px;

    class C,D,E,F upper
    class G,H,I lower
    class J,K output

    %% === データ構造：flowchart ノードで表現 ===
    L["
    Record構造体
    --------------
    PKG: str
    SLOT: str
    対向装置: str
    対向装置番号: str
    対向PKG: str
    対向SLOT: str
    備考: str
    ビル: str
    フロア: str
    群: str
    架番号: str
    装置: str
    装置番号: str
    図面名: str
    "]:::record

    classDef record fill:#f9f,stroke:#333,stroke-width:1px;

    I --> L
    L --> J
