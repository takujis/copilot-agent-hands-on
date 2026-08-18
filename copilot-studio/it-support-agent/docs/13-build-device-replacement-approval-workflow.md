# デバイス交換承認 Workflow を作成する

デバイス交換は、多くの組織で申請と承認を伴う業務です。
前の手順で作成した Device Assistant は、デバイスに関する問い合わせへ回答できるようになりました。

しかし、実際の業務では、ユーザーから必要な情報を収集したあと、管理者による承認や後続システムとの連携が必要になる場合があります。
そこで本演習では、Workflow と Human Review を利用して、デバイス交換申請の承認プロセスを一例として構築します。

この手順は Workflow と Human Review を体験するためのオプションの発展演習です。

## Device Change Flow を作成する
1. Copilot Studio の左側のナビゲーションから \[Workflows\] を選択します。
2. [New workflow] をクリックします。
3. Workflow の名前に以下を入力します。

```text
Device Change Flow
```

### トリガーを設定する
4. 規定は [Start] になっているトリガーノードを選択し、トリガータイプを [When an agent calls the workflow] に変更します。
5. 自動的に追加される [Respond to the agent] を削除します。
6. トリガーノードのInputを追加し、以下を設定します。

| 項目 | 値 |
|------|------|
| 名前 | deviceReplacementRequest |
| 種類 | Text |

> [!IMPORTANT]
> 今回 Device Assistant は、deviceReplacementRequest をJSON オブジェクトではなく、文字列として受け渡します。
> 次のステップで Parse JSON の Content に deviceReplacementRequest を設定し、正しい Schema を指定します。

### Parse JSON を追加する
7. トリガーの次に新しいステップを追加します。
8. [Parse JSON] を検索して追加します。
9. [Content] に、トリガーから受け取った deviceReplacementRequest を動的コンテンツとして指定します。  
式で指定する場合は、以下を入力します。

```text
@{triggerOutputs()?['body/text']}
```

10. [Schema] に、以下を入力します。

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
    "issue": {
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

repairStatus は、修理やトラブルシューティングの実施状況を表す文字列として扱います。

### Human Review を追加する
11. [Parse JSON] の次に、新しいステップ [Human Review] を追加します。
12. [Connection] から、 [Create new connection] を選択し、接続を作成します。
13. [Human review] ノードに以下を設定します。

| 項目 | 値 |
|------|------|
| Title | デバイス交換申請 |
| Assign to | {あなたのメールアドレス} |
| Channel | Outlook |

> [!NOTE]
>
> [Assign to] は承認メールの送信先になります。今回はテストのしやすさから作成者自身のメールアドレスを設定します。

14. レビュー担当者が申請内容を確認できるように、[Message] に次のテキストを入力します。　なお、
{requestType}、{deviceType}、{issue}、{businessImpact}、{urgency}、{repairStatus}、{recommendedAction} には、[Parse JSON] の出力にある対応する動的コンテンツを設定します。

```text
デバイス交換申請の内容を確認してください。

申請種別: {requestType}
対象デバイス: {deviceType}
故障内容: {issue}
業務への影響: {businessImpact}
緊急度: {urgency}
修理・対処の実施状況: {repairStatus}
推奨対応: {recommendedAction}
```

15. レビュー担当者が承認または却下を選択できるように設定します。
    [Inputs] に次のように設定します。
    
| 項目 | 型 | 内容 |
|------|------|------|
| Approved | Text/DropDown | Dropdown options として、Yes,No を追加 |
| Reason | Text |   |

### If / Else を追加する
Human Review の出力には、レビュー担当者が入力した値が含まれます。
Approved が Yes の場合は承認、それ以外の場合は却下として処理を分岐します。

16. [Human Review] の次に [If / Else] を追加します。
17. 条件の設定として、[Property] には Human Review の動的コンテンツ、[Approved] が Yes になるように設定します。
18. レビュー結果が承認の場合に [If] 側、承認以外の場合に [Else] 側へ進むように条件を設定します。  

### 承認時と却下時の処理を設定する
19. [If] 側に、承認後に実行する処理を追加します。ここでは、[Office 365 Outlook] の [メールの送信]を設定します。
20. [メールの送信] の宛先には、ご自身のメールアドレスを、件名と本文は次を設定します。 

件名
```text
デバイス交換申請は承認されました。
```
本文
```text
デバイス購入が承認されました。

{Parse JSON.Device Type}
{Parse JSON.Issue}
```
本文の、{Parse JSON.Device Type},{Parse JSON.Issue} は動的コンテンツより選択します。


21. [Else] 側に、却下後に実行する処理を追加します。  
このハンズオンでは、承認時と同じく[メールの送信]を追加します。

```text
デバイス交換申請は却下されました。
```

この手順では、Agent、Workflow、Human Review、および If / Else の連携を確認します。メール通知、Teams 通知、Dataverse への申請レコードの保存などは、必要に応じて追加してください。

### Workflow を保存して公開する
22. Workflow を保存します。
23. Workflow にエラーが表示されていないことを確認します。
24. Device Change Flow を公開します。
> [!IMPORTANT]
> Workflow を Agent から利用するためには、保存だけではなく公開が必要です。
> 公開していない Workflow は Tools として利用できません。

### Device Assistant に Device Change Flow を追加する
25. Copilot Studio の左側のナビゲーションから [Agents] を選択します。
26. [Device Assistant] を開きます。
27. [Build] タブを選択します。
28. コンポーネント パネルの [Tools] で [+] をクリックします。
29. 作成した [Device Change Flow] を検索して選択します。
31. [Tools] に追加した [Device Change Flow] をクリックします。
32. [Description] に以下を入力します。

```text
このワークフローは、ユーザーから故障または破損したデバイスの交換依頼を受けた場合に使用してください。
以下の情報を収集した後に実行してください。
- デバイスの種類
- 発生している問題
- 業務への影響
- 緊急度
- RepairStatus（修理状況）

必要な情報がすべて揃っている場合は、直ちにこのワークフローを実行してください。
```
> [!NOTE]
> Tool の Description は、Agent がこの Workflow を呼び出すかどうかを判断する際の重要な情報になります。


33. 入力 deviceReplacementRequest が、AI によって動的に設定される構成になっていることを確認します。

### Device Assistant の Instructions を更新する
34. Device Assistant の [Instructions] に以下を追記します。

既存の 回答方針 セクションへ、以下の内容を追加します。
```text
- デバイス交換に関する問い合わせでは、ナレッジによる回答よりも Device Change Flow の利用を優先すること。
- デバイス交換申請に必要な情報が揃った場合は、回答文を生成して処理を終了せず、Device Change Flow を実行すること。
```
回答方針セクションの下に、デバイス交換セクションを追加します。
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
必要な情報が揃ったら、以下の項目を含む DeviceReplacementRequest を作成し、Device Change Flow を実行してください。

- requestType
- deviceType
- issue
- businessImpact
- urgency
- repairStatus
- recommendedAction

すべての項目は文字列として設定してください。
DeviceReplacementRequest はチャットの回答として表示せず、Device Change Flow の入力としてのみ使用してください。
最終的な承認判断は行わないでください。承認判断は Device Change Flow の Human Review で行います。
Device Change Flow が正常に実行された後にのみ、デバイス交換申請を受け付けたことをユーザーへ通知してください。
Device Change Flow の実行に失敗した場合は、交換申請が完了したと回答してはいけません。
```

35. Device Assistant を保存し、公開します。

## Device Assistant でテストする
1. Device Assistant の \[Preview\] タブを選択します。
2. エージェントの動きを確認するため、テストを実施する際は \[End user preview\] をオフにしてください。
3. 会話のコンテキストをリセットする場合は、\[New chat\] をクリックします。
4. チャット入力欄に、以下のメッセージを入力して送信します。

```text
Surface Laptop が起動しないため、交換を申請したいです。
```

5. Device Assistant から追加情報を求められた場合は、不足している情報を回答します。  
以下は回答例です。

```text
電源ボタンを押しても画面が表示されません。
顧客向け資料を作成できず、業務が停止しています。
緊急度は高です。
再起動とリセットを試しましたが改善しませんでした。
```

6. エージェントと Workflow の動作を確認します。<br>期待される動作:
- 不足情報のみを質問すること
- 同じ情報を繰り返し質問しないこと
- Human Review に申請内容が表示されること
- Human Review で承認した場合は [If] 側の処理が実行されること
- Human Review で却下した場合は [Else] 側の処理が実行されること

## IT Support Agent からテストする
1. IT Support Agent の \[Preview\] タブを選択します。
2. エージェントの動きを確認するため、テストを実施する際は \[End user preview\] をオフにしてください。
3. 会話のコンテキストをリセットする場合は、\[New chat\] をクリックします。
4. チャット入力欄に、以下のメッセージを入力して送信します。

```text
Surface Laptop が故障しました。交換を申請したいです。
```

5. Device Assistant から追加情報を求められた場合は、不足している情報を回答します。
6. エージェントと Workflow の動作を確認します。<br>期待される動作:
- IT Support Agent が Device Assistant を呼び出すこと
- Device Assistant が交換申請に必要な情報を収集すること
- Device Assistant が Device Change Flow を実行すること
- Human Review に申請内容が表示されること
- IT Support Agent のサポートチケット作成スキルが実行されないこと
7. 最後に、チャット入力欄に、以下のメッセージを入力して送信します。

   ```text
   PCが起動しません。何か対処方法はありますか？交換できるなら交換したいです。
   ```

> [!NOTE]
> このハンズオンでは、承認結果をメールで通知しています。
> 実際の業務では、Teams 通知、Dataverse への保存、ServiceNow や Jira への連携などに置き換えることもできます。
