# 1. 事前準備

## 1.1. 必要なライセンス / サービス

| 項目 | 内容 |
| --- | --- |
| 必須 | Power Apps (開発者プランあり) |
| 必須 | Microsoft Copilot Studio (試用版あり) |

> **Note**
> 演習の一部でエージェントの公開を行いますが、Copilot Studio 試用版ライセンスではエージェントを公開することはできません (該当箇所はスキップ可)。

Microsoft 365 テナントをお持ちでない場合は、[Microsoft 365 開発者プログラム](https://developer.microsoft.com/microsoft-365/dev-program) に参加するか (要件を満たす場合のみ。詳細は [こちら](https://learn.microsoft.com/ja-jp/office/developer-program/microsoft-365-developer-program))、[1 か月の無料試用版](https://learn.microsoft.com/ja-jp/microsoft-365/business-premium/m365bp-trial-playbook-microsoft-business-premium?view=o365-worldwide) (リンクは Microsoft 365 Business Premium) にサインアップするなどして、テナントを準備します。自社のテナントと区別すべく、シークレット / InPrivate ウィンドウでブラウザを使用してください。

## 1.2. Power Apps 開発者プランにサインアップする

1. Power Apps <https://powerapps.microsoft.com/free/> に移動し、[無料で試す] を選択します。
2. [始めましょう] で、テキスト ボックスにメール アドレスを入力します。
3. 同意ボックスをオンにして、[無料で始める] を選択します。
4. パスワードを入力し、[サインイン] を選択します。

## 1.3. Copilot Studio 試用版へサインアップする

1. Copilot Studio <https://copilotstudio.microsoft.com> に移動します。
2. サインインを求められたら、メール アドレスとパスワードを入力し、サインインします。
3. 国 / リージョンを選択し、[無料試用版を開始する] を選択します。
4. [Copilot Studio へようこそ!] というポップアップが表示された場合は、[スキップ] を選択します。

## 1.4. Power Platform 環境を作成する

1. Power Platform 管理センター <https://admin.powerplatform.microsoft.com/> にアクセスします。
2. 左側のナビゲーションから [管理] をクリックし、[管理] > [環境] で [+ 新規] をクリックします。
3. [新しい環境] ウィンドウで、以下の内容を入力あるいは選択します。

   | 項目 | 値 |
   | --- | --- |
   | 名前 | DevEnv (あるいは任意の名前) |
   | 種類 | [開発者] |
   | 地域 | [米国] または [日本] |

   > **Note**
   > 同じ組織内の複数名でこの演習を実施する際は、区別がつきやすいように環境の名前を設定することをおすすめします (例: `DevEnv_name`)。

4. [既定の設定を変更する] を展開し、[Dataverse データストアを追加しますか] で [はい] を選択し、[次へ]、[保存] をクリックします。

   > **画面の見え方**
   > [新しい環境] ウィンドウでは、名前・種類・地域の入力欄の下に [既定の設定を変更する] という折りたたみ項目があります。ここを展開しないと Dataverse の設定項目は表示されません。
5. 新しく作成された環境は、管理センターに一覧表示されます。状態が [準備完了] になると、環境を使用する準備が整います。この環境は以降の演習で使用します。

## 1.5. Power Platform 環境を選択する

作成した Power Platform 環境が Copilot Studio で選択できることを確認します。

1. Copilot Studio <https://copilotstudio.microsoft.com> に移動します。

   > **Note**
   > 新 UI (GitHub Copilot Harness) の画面が表示された場合は、右上の [New experience] のトグルボタンをオフにし、適宜フィードバックを送信すれば旧 UI (Standard Harness) へ戻ります。

2. 右上の [環境] をクリックし、[環境を選択] ウィンドウが表示されたら、前の手順で作成した Power Platform 環境を選択します。
3. 右上の [環境] で該当の環境が選択されていることを確認します。

> **画面の見え方**
> [環境] はページ右上のコマンドバーにあり、現在選択されている環境名が表示されます。クリックすると環境の一覧がパネルで開き、選択後は右上の表示が選んだ環境名に変わります。

---

次へ: [2. Dataverse テーブルを作成する](10-create-table.md)
