# デバイス交換承認ワークフローを作成する (オプション)

デバイス交換は、組織によって申請と承認のプロセスが必要になる場合があります。

前の手順で作成した Device Assistant は、デバイスに関する問い合わせに回答できるようになりました。

しかし、実際の業務では、ユーザーから必要な情報を収集した後に、管理者による承認や後続システムとの連携が必要になる場合があります。

そこで、この手順では、人による確認を含むデバイス交換申請の承認ワークフローを構築します。

## Device Replacement Request Flow を作成する

1. Copilot Studio の左側のナビゲーションから \[Workflows\] を選択します。

2. [New workflow] をクリックします。

   または、[New workflow] のドロップダウン メニューから [Workflow] (Uses credits) をクリックします。

3. Workflow の名前に以下を入力します。

```text
Device Replacement Request Flow
```

### トリガーを設定する

4. 既定では [Start] になっているトリガーノードを選択し、トリガー タイプを [When an agent calls the workflow] に変更します。

5. 自動的に追加される [Respond to the agent] ノードを削除します。

6. トリガー ノードで以下の Input を追加します。

| 項目 | 値 |
|------|------|
| 名前 | deviceReplacementReq |
| 種類 | Text |

> [!NOTE]
>
> `deviceReplacementReq` は Text 型の入力として Device Assistant から JSON データを受け取ります。
>
> 次のステップで Parse JSON を使用し、`deviceReplacementReq` に含まれる JSON データをオブジェクトとして扱えるようにします。

### [Parse JSON] アクションを追加する

トリガーの次に新しいステップを追加し、[Parse JSON] アクションを追加します。

7. 新しいステップとして、[Parse JSON] を検索して追加します。

8. [Content] に、トリガーから受け取った `deviceReplacementReq` を動的コンテンツとして挿入します。 

式で指定する場合は、以下を入力します。

```text
@{triggerOutputs()?['body/text']}
```

> [!NOTE]
>
> 動的コンテンツは、右側の動的コンテンツ アイコン (⚡) から選択して挿入します。

9. [Schema] に、以下を入力します。

```json
{
  "type": "object",
  "properties": {
    "requestType": {
      "type": "string"
    },
    "deviceType": {
      "type": "string"
    },
    "issueDescription": {
      "type": "string"
    },
    "businessImpact": {
      "type": "string"
    },
    "urgency": {
      "type": "string"
    },
    "repairStatus": {
      "type": "string"
    },
    "recommendedAction": {
      "type": "string"
    }
  }
}
```

`repairStatus` は、修理やトラブルシューティングの実施状況を表す文字列として扱います。

### [Human review] アクションを追加する

[Parse JSON] アクションの次に新しいステップを追加し、[Human review] アクションを追加します。

10. 新しいステップとして、[Human review] を検索して追加します。

11. [Connection] から、[Create new connection] を選択し、接続を作成します。

12. [Human review] ノードに以下を設定します。

| 項目 | 値 |
|------|------|
| Title | デバイス交換申請 |
| Assign to | {あなたのメールアドレス} |
| Channel | Outlook |

> [!NOTE]
>
> [Assign to] は承認メールの送信先になります。今回はテストのしやすさから作成者自身のメールアドレスを設定します。

13. 担当者が申請内容を確認できるように、[Message] に次のテキストを入力します。

```text
デバイス交換申請の内容を確認してください。

申請種別: {requestType}
対象デバイス: {deviceType}
故障内容: {issueDescription}
業務への影響: {businessImpact}
緊急度: {urgency}
修理・対処の実施状況: {repairStatus}
推奨対応: {recommendedAction}
```

`{requestType}`、`{deviceType}`、`{issueDescription}`、`{businessImpact}`、`{urgency}`、`{repairStatus}`、`{recommendedAction}` の箇所には、[Parse JSON] の出力から対応する動的コンテンツを挿入します。

14. [Inputs] に以下を設定し、担当者が承認または却下を選択し、その理由を入力できるようにします。

| 項目 | 型 | 内容 |
|------|------|------|
| Approved | Text | Dropdown options として、`Yes`、`No` を追加 |
| Reason | Text |   |

> [!NOTE]
>
> Dropdown を追加するには、右の [...] > [Add dropdown] をクリックします。
>
> `Approved` の Dropdown options として `Yes` と `No` を追加します。

### 条件 [If/Else] を追加する

Human review の結果として取得される `Approved` の値に応じて処理を分岐するために、[Human review] アクションの次に新しいステップを追加し、条件 [If/Else] を追加します。

15. 新しいステップとして、[If/Else] を検索して追加します。

16. [Condition] の設定で、[Property] に Human review の出力に含まれる `Approved` を設定し、[Operator] に [Equals]、[Value] に `Yes` を入力して設定します。

### 承認時と却下時の処理を設定する

条件 [If/Else] の各分岐に、承認時および却下時に実行する処理を追加します。

17. [If] の後のステップとして、承認後に実行する処理を追加します。[Office 365 Outlook] の [メールの送信] を設定します。

18. [メールの送信] アクションの [宛先] には、簡易な動作確認のため、ご自身のメールアドレスを設定します。

19. [件名] と [本文] は以下の内容を設定します。

件名

```text
デバイス交換申請が承認されました。
```

本文

```text
デバイス交換申請が承認されました。

{deviceType}
{issueDescription}
{Reason}
```

`{deviceType}`、`{issueDescription}`、`{Reason}` の箇所には、[Parse JSON] および [Human review] の出力から対応する動的コンテンツを設定します。

20. [Else] の後のステップとして、却下後に実行する処理を追加します。承認時と同じく [Office 365 Outlook] の [メールの送信] を設定します。

21. [メールの送信] アクションの [宛先] には、簡易な動作確認のため、ご自身のメールアドレスを設定します。

22. [件名] と [本文] は以下の内容を設定します。

件名

```text
デバイス交換申請が却下されました。
```

本文

```text
デバイス交換申請が却下されました。

{deviceType}
{issueDescription}
{Reason}
```

`{deviceType}`、`{issueDescription}`、`{Reason}` の箇所には、[Parse JSON] および [Human review] の出力から対応する動的コンテンツを設定します。

> [!NOTE]
>
> この手順では、Workflow における Human review および If/Else の動作を確認するために、承認時および却下時にメールを送信する処理を追加しています。
>
> メール通知の代わりに、Teams 通知、Dataverse への申請レコードの保存などを追加することもできます。

### Workflow を保存して公開する

23. Workflow を保存します。Workflow にエラーが表示されていないことを確認します。

24. [Publish] をクリックして Device Replacement Request Flow を公開します。

> [!IMPORTANT]
> 
> Workflow を Agent から利用するためには、公開が必要です。公開していない Workflow は Tools として利用できません。

## Device Assistant に Device Replacement Request Flow を追加する

1. [Device Assistant] を開き、[Build] タブを選択します。

2. コンポーネント パネルの [Tools] で [+] をクリックします。

3. 作成した [Device Replacement Request Flow] を検索して選択します。

4. [Tools] に追加した [Device Replacement Request Flow] をクリックします。

5. [Description] に以下を入力します。

```text
このワークフローは、ユーザーから故障または破損したデバイスの交換依頼を受けた場合に使用してください。
以下の情報を収集した後に実行してください。
- デバイスの種類
- 発生している問題
- 業務への影響
- 緊急度
- 修理・トラブルシューティングの実施状況 (repairStatus)

必要な情報がすべて揃っている場合は、直ちにこのワークフローを実行してください。
```

> [!NOTE]
> 
> Tool の Description は、Agent がこの Workflow を呼び出すかどうかを判断する際の重要な情報になります。


6. 左の [Inputs] タブで、入力 [deviceReplacementReq] が AI によって動的に設定される構成になっていることを確認します。

7. [Save] をクリックします。

### Device Assistant の Instructions を更新する

8. Device Assistant の [Instructions] に以下を追記します。

既存の「回答方針」セクションの末尾に、以下の内容を追加します。

```text
- デバイス交換に関する問い合わせでは、ナレッジによる回答よりも Device Replacement Request Flow の利用を優先すること。
- デバイス交換申請に必要な情報が揃った場合は、回答文を生成して処理を終了せず、Device Replacement Request Flow を実行すること。
```

「回答方針」セクションの下に、デバイス交換セクションとして以下の内容を追加します。

```text
# デバイス交換

デバイス交換に関する問い合わせでは、交換申請に必要な情報を収集してください。
以下の情報を収集します。

- 対象デバイス
- 故障内容
- 業務への影響
- 緊急度
- 修理またはトラブルシューティングの実施状況

必要な情報が不足している場合は、不足している情報のみをユーザーへ質問してください。
必要な情報が揃ったら、以下の項目を含む deviceReplacementReq を作成し、Device Replacement Request Flow を実行してください。

- requestType
- deviceType
- issueDescription
- businessImpact
- urgency
- repairStatus
- recommendedAction

すべての項目は文字列として設定してください。
deviceReplacementReq はチャットの回答として表示せず、Device Replacement Request Flow の入力としてのみ使用してください。
最終的な承認判断は行わないでください。承認判断は Device Replacement Request Flow の Human review で行います。
Device Replacement Request Flow が正常に実行された後にのみ、デバイス交換申請を受け付けたことをユーザーへ通知してください。
Device Replacement Request Flow の実行に失敗した場合は、交換申請が完了したと回答してはいけません。
```

9. Device Assistant を保存します。

## エージェントをテストする

### Device Assistant でテストする

1. Device Assistant の \[Preview\] タブを選択します。

> [!NOTE]
>
> - エージェントの動きを確認するため、テストを実施する際は [End user preview] をオフにしてください。
>
> - 会話のコンテキストをリセットする場合は、[New chat] をクリックします。

2. チャット入力欄に、以下のメッセージを入力して送信します。

```text
Surface Laptop が起動しないため、交換を申請したいです。
```

3. Device Assistant が不足している情報について質問した場合は、回答します。

回答例:

```text
電源ボタンを押しても画面が表示されません。
顧客向け資料を作成できず、業務が停止しています。
緊急度は高です。
再起動とリセットを試しましたが改善しませんでした。
```

4. エージェントが交換申請を実行します。エージェントと Workflow の動作を確認します。

   また、送信された承認依頼メールに対して承認または却下を行い、その後の動作を確認します。

   > 期待される動作:
   > - 交換申請に必要な情報について質問されること
   > - 既に提供した情報について繰り返し質問されないこと
   > - Human review の承認依頼メールが送信され、申請内容が含まれていること
   > - Human review の承認依頼メールで承認した場合は、承認メールが送信されること
   > - Human review の承認依頼メールで却下した場合は、却下メールが送信されること

   > 内部動作:
   > - Device Assistant から Device Replacement Request Flow が実行されること

### IT Support Agent からテストする

このテストは、Device Assistant の動作確認と公開が完了した後に実施します。

1. IT Support Agent の \[Preview\] タブを選択します。

> [!NOTE]
>
> - エージェントの動きを確認するため、テストを実施する際は [End user preview] をオフにしてください。
>
> - 会話のコンテキストをリセットする場合は、[New chat] をクリックします。

2. チャット入力欄に、以下のメッセージを入力して送信します。

```text
Surface Laptop が故障しました。交換を申請したいです。
```

3. Device Assistant が不足している情報について質問した場合は、回答します。

4. エージェントが交換申請を実行します。エージェントと Workflow の動作を確認します。

   また、送信された承認依頼メールに対して承認または却下を行い、その後の動作を確認します。

   > 期待される動作:
   > - 交換申請に必要な情報について質問されること
   > - 既に提供した情報について繰り返し質問されないこと
   > - Human review の承認依頼メールが送信され、申請内容が含まれていること
   > - Human review の承認依頼メールで承認した場合は、承認メールが送信されること
   > - Human review の承認依頼メールで却下した場合は、却下メールが送信されること

   > 内部動作:
   > - IT Support Agent が Device Assistant を呼び出すこと
   > - Device Assistant から Device Replacement Request Flow が実行されること
   > - IT Support Agent のサポートチケット作成スキルが実行されないこと