# 環境を準備する

Copilot Studio でエージェントを作成するために環境を準備します。

## Power Platform 環境を作成する

1. Power Platform 管理センターにアクセスします。

   ```text
   https://admin.powerplatform.microsoft.com
   ```

2. 左側のナビゲーションから [管理] をクリックし、[管理] > [環境] で [新規] をクリックします。

3. [新しい環境] ペインで、以下の内容を入力あるいは選択し、[次へ] をクリックします。

   | 項目 | 値 |
   |---|---|
   | 種類 | 開発者 |
   | 地域 | 日本 |
   | 名前 | 任意の名前 (例: dev-name) |

4. [既定の設定を変更する] を展開し、[Dataverse データ ストアを追加しますか?] を [はい] にし、[次へ] をクリックします。

5. [Dataverse を追加] ペインで、既定値のまま [保存] をクリックします。

6. 新しく作成された環境が管理センターに表示されます。状態が [準備完了] になると、環境が使用できるようになります。

   この環境は以降の手順で使用します。

## Copilot Studio で Power Platform 環境を選択する

作成した Power Platform 環境が Copilot Studio で選択できることを確認します。

1. Copilot Studio にアクセスします。

   ```text
   https://copilotstudio.microsoft.com
   ```

2. 環境セレクターをクリックし、前の手順で作成した Power Platform 環境を選択します。

3. 環境セレクターで対象の環境が選択されていることを確認します。

> [!NOTE]
>
> 環境が表示されない場合は、以下の URL に直接アクセスしてください。
>
> ```text
> https://copilotstudio.microsoft.com/environments/<環境 ID>/
> ```
>
> Power Platform 管理センターで対象の環境を開き、[環境 ID] の値を確認して `<環境 ID>` を置き換えてください。