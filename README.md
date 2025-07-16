# HEWProject
-----

# 総合開発規約 (C++ / DirectX)

このドキュメントは、本プロジェクトにおける開発の一貫性、可読性、生産性を向上させるためのコーディング規約および開発ワークフローを定めるものだ。プロジェクトに参加するすべての開発者は、この規約を守ること。

-----

## 第1部: 基本コーディング規約

コードベース全体の統一性を保つため、以下の規約を定める。

### 命名規則

1.  **変数・関数**
    **型名の省略形 + パスカルケース** (`prefixPascalCase`) を基本とする。これにより、変数名から型と役割を即座に把握できる。

    | 型         | 省略形プレフィックス | 例                               |
    | :--------- | :------------------- | :------------------------------- |
    | `int`      | `n`                  | `int nPlayerScore;`              |
    | `float`    | `f`                  | `float fElapsedTime;`            |
    | `bool`     | `b`                  | `bool bIsInitialized;`           |
    | `char*`, `string` | `sz` or `str` | `string strPlayerName;`          |
    | ポインタ   | `p`                  | `Player* pCurrentPlayer;`        |
    | `vector`   | `vec`                | `vector<int> vecItemIds;`        |
    | 関数       | `Fn`                 | `void FnInitializeGame();`       |

2.  **その他の識別子**

| 識別子           | 命名規則                     | 例                           |
| :--------------- | :--------------------------- | :--------------------------- |
| **メンバ変数** | `m_` + 上記の変数規則        | `int m_nPlayerHealth;`           |
| **定数** | `k` + パスカルケース         | `const int kMaxPlayerCount = 4;` |
| **マクロ** | アッパースネークケース       | `MY_MACRO`                   |
| **クラス・構造体** | パスカルケース               | `PlayerController`, `GameData`   |
| **列挙型** | パスカルケース               | `GameState`                  |
| **列挙子** | `k` + パスカルケース         | `kTitleScreen`, `kInGame`        |
| **名前空間** | 小文字のスネークケース       | `game_logic`                 |
| **ファイル名** | パスカルケース (`.h`, `.cpp`) | `PlayerController.h`, `PlayerController.cpp` |

### フォーマット

  - **インデント**: **タブ**。
  - **行長**: 最大**80文字**。
  - **波括弧**: `if`, `else`, `while` などで処理が1行でも、波括弧`{}`は**省略しない**。

### 禁止事項

  - **例外処理 (`try-catch`)**: **使用禁止**。エラーは戻り値（`HRESULT`やステータスオブジェクト）で処理する。
  - **RTTI (`dynamic_cast`, `typeid`)**: **使用禁止**。仮想関数や他のデザインパターンで代替する。

### ヘッダーファイル

  - **インクルードガード**: すべてのヘッダーファイル (`.h`, `.hpp`) には、多重インクルードを防止する `#define` ガードを必ず記述する。
  - **自己完結**: ヘッダーは、それ自身が必要とする他のすべてのヘッダーをインクルードし、単体でコンパイル可能である必要がある。

-----

## 第2部: 環境・ライブラリ固有の追加/上書き規約

基本規約に加え、開発環境と使用ライブラリに特化した以下のルールを適用する。

### A. Visual Studio 固有規約 📐

IDEの設定を統一し、コード品質を担保する。

1.  **EditorConfigによるフォーマット統一**
    リポジトリのルートに `.editorconfig` ファイルを配置し、全開発者のエディタ設定を自動で統一する。

    ```ini
    # .editorconfig
    # https://editorconfig.org

    root = true

    [*]
    indent_style = tab
    end_of_line = lf
    charset = utf-8-bom
    trim_trailing_whitespace = true
    insert_final_newline = true
    ```

2.  **警告レベルの最大化**
    プロジェクト設定で警告レベルを **`/W4`** 以上に設定し、**「警告をエラーとして扱う (`/WX`)」を有効**にする。これにより、コンパイラが指摘する潜在的な問題を放置できなくなり、バグの早期発見に繋がる。

3.  **ソリューションのフォルダ構成**
    物理的なディレクトリ構造と、ソリューションエクスプローラー上のフィルター構成を一致させ、プロジェクトの全体像を把握しやすくする。

    ```
    /solution_root
    │  .gitignore
    │  .editorconfig
    │  README.md
    │  MyProject.sln
    │
    └─ /src
        │  Main.cpp
        ├─ /Engine
        └─ /Gameplay
    ```

### B. GitHub 開発ワークフロー規約 🐙

バージョン管理と共同作業を円滑に進めるためのルール。

1.  **.gitignoreの設定**
    VisualStudioが生成する一時ファイルやビルド成果物をリポジトリに含めないよう、GitHub公式の [VisualStudio用.gitignoreテンプレート](https://www.google.com/search?q=https://github.com/github/gitignore/blob/main/VisualStudio.gitignore) をベースとして使用する。

2.  **ブランチ戦略 (GitHub Flow)**

      - **`main`**: **常にリリース可能な安定版**。このブランチへの直接のコミットは**禁止**。すべての変更はPull Requestを通じてマージする。
      - **`feature/feature-name`**: 機能追加、バグ修正、リファクタリングなど、あらゆる作業を行うためのブランチ。`main`ブランチから作成し、作業が完了したら`main`へのPull Requestを作成する。

3.  **コミットメッセージ (Conventional Commits)**
    コミット履歴の可読性を高めるため、メッセージの先頭に以下の種別（type）を付ける。

      - `feat:`: 新機能の追加
      - `fix:`: バグ修正
      - `refactor:`: 機能的変更を伴わないコードの修正
      - `style:`: コードフォーマットの修正（空白、セミコロンなど）
      - `docs:`: ドキュメントの更新
      - `chore:`: ビルドプロセスや補助ツールの変更

    <!-- end list -->

    ```bash
    # 良いコミットメッセージの例
    git commit -m "feat: プレイヤーにジャンプ機能を追加"
    git commit -m "fix: 特定条件下で発生する描画の不具合を修正"

    # 悪いコミットメッセージの例
    git commit -m "修正"
    ```

4.  **Pull Request (PR)**

      - 作業が完了したら`main`ブランチへのPull Requestを作成する。
      - PRの本文には、「**何をしたか (What)**」「**なぜしたか (Why)**」「**どうやってテストしたか (How)**」を明記する。
      - マージする前に、最低1人以上の他の開発者によるレビューと承認を必須とする。

### C. DirectX 固有規約 🎮

DirectX APIを扱うコードには、以下の命名規則と作法を**上書き適用**する。

1.  **COMインターフェースの命名**
    COMインターフェースポインタには、`p`プレフィックスを必ず付ける。これはMicrosoftの慣習に従う。

    ```cpp
    // 基本規約 + COM規約
    ID3D12Device* pDevice;
    ID3D12CommandQueue* pCommandQueue;
    ```

2.  **リソース変数の命名**
    DirectXリソース（バッファ、テクスチャ等）の変数名は、リソースの種類がわかるプレフィックスまたはサフィックスを付ける。

    ```cpp
    ID3D12Resource* pVertexBuffer;
    ID3D12Resource* pPlayerTexture;
    D3D12_VERTEX_BUFFER_VIEW stVertexBufferView; // 構造体(struct)なのでstプレフィックス
    ```

3.  **エラーハンドリング (`HRESULT`)**
    DirectX APIが返す `HRESULT` は**絶対に無視しない**。`SUCCEEDED()` / `FAILED()`マクロで必ず成否をチェックする。デバッグビルドでは、チェックを自動化するヘルパーマクロの使用を強く推奨する。

    ```cpp
    // ヘルパーマクロの定義例
    #if defined(_DEBUG)
    #include <cassert>
    #define HR(x) \
        { \
            HRESULT hr = (x); \
            if (FAILED(hr)) { \
                assert(!"HRESULT failed."); \
            } \
        }
    #else
    #define HR(x) (x)
    #endif

    // ヘルパーマクロの使用例
    HR(pDevice->CreateCommittedResource(...));
    ```

4.  **HLSL (シェーダーコード)**
    シェーダーコード (`.hlsl`) はC++とは別の規約を設ける。プロジェクト内で統一すること。

      - **グローバル変数/定数バッファ**: `g_camelCase` or `g_PascalCase` (例: `gWorldViewProjection`)
      - **テクスチャ/サンプラー**: `PascalCase` (例: `AlbedoTexture`, `LinearSampler`)
      - **関数**: `PascalCase`
