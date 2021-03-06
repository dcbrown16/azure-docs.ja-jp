- VNet は、Batch アカウントと同じ Azure **リージョン**と**サブスクリプション**に存在する必要があります。

- 仮想マシン構成で作成されたプールでは、Azure Resource Manager ベースの VNet のみサポートされます。 クラウド サービス構成で作成されたプールでは、クラシック VNet のみがサポートされます。
  
- クラシック VNet を使用するには、`MicrosoftAzureBatch` サービス プリンシパルに、指定された VNet に対する `Classic Virtual Machine Contributor` ロールベースのアクセス制御 (RBAC) のロールが付与されている必要があります。 Azure Resource Manager ベースの VNET を使用するためには、VNET にアクセスするためのアクセス許可とサブネットに VM をデプロイするためのアクセス許可が必要です。

- プールに指定されたサブネットには、プールの対象となる VM 数 (つまり、プールの `targetDedicatedNodes` および `targetLowPriorityNodes` プロパティの合計) に対応できる十分な未割り当て IP アドレスが必要です。 サブネットの未割り当て IP アドレスが十分でない場合、プールによってコンピューティング ノードが部分的に割り当てられ、サイズ変更エラーが発生します。 

- Azure VNet にデプロイされている仮想マシン構成のプールによって、追加の Azure ネットワーク リソースが自動的に割り当てられます。 VNet 内のプール ノード 50 個ごとに次のリソースが必要です。1 つのネットワーク セキュリティ グループ、1 つのパブリック IP アドレス、1 つのロード バランサー。 これらのリソースは、Batch プールの作成時に提供される仮想ネットワークを含むサブスクリプション内の[クォータ](../articles/batch/batch-quota-limit.md)によって制限されます。

- コンピューティング ノードのタスクのスケジュールを設定できるように、Batch サービスからの通信を VNet で許可する必要があります。 これを検証するには、VNet に、関連付けられたネットワーク セキュリティ グループ (NSG) があるかどうかをチェックします。 指定したサブネット内のコンピューティング ノードとの通信が NSG によって拒否された場合、Batch サービスによって、コンピューティング ノードの状態が**使用不可**に設定されます。 

- 指定した VNet が、ネットワーク セキュリティ グループ (NSG) やファイアウォールに関連付けられている場合は、次の表に示すように受信ポートと送信ポートを構成します。


  |    宛先ポート    |    送信元 IP アドレス      |   発信元ポート    |    Batch によって NSG が追加されるか    |    VM を使用するうえで必須か    |    ユーザーの操作   |
  |---------------------------|---------------------------|----------------------------|----------------------------|-------------------------------------|-----------------------|
  |   <ul><li>仮想マシン構成で作成されたプールの場合: 29876、29877</li><li>クラウド サービス構成で作成されたプールの場合: 10100、20100、30100</li></ul>        |    * (より強固なセキュリティを確保する場合は、Batch サービスの IP アドレス)。 Batch サービスの IP アドレスの一覧を取得するには、Azure サポートにお問い合わせください。 | * または 443 |    はい。 VM にアタッチされたネットワーク インターフェイス (NIC) レベルで NSG が追加されます。 Batch サービス ロールの IP アドレスを送信元とするトラフィックだけが、これらの NSG によって許可されます。 Web 全体でこれらのポートを開放しても、NIC でトラフィックがブロックされます。 |    [はい]  |  Batch で許可されるのは、Batch の IP アドレスだけであるため、NSG を自分で指定する必要はありません。 <br /><br /> ただし、あえて NSG を指定する場合は必ず、受信トラフィックに対してこれらのポートを開放してください。 <br /><br /> 独自の NSG で送信元 IP として「*」を指定した場合でも Batch によって、VM にアタッチされた NIC レベルで NSG が追加されます。 |
  |    3389 (Windows)、22 (Linux)               |    デバッグ目的で使用されるユーザーのマシン (VM にリモートでアクセスするために使用)。    |   *  | いいえ                                     |    いいえ                     |    VM へのリモート アクセス (RDP または SSH) を許可する場合は、NSG を追加します。   |                                


  |    送信ポート    |    変換先    |    Batch によって NSG が追加されるか    |    VM を使用するうえで必須か    |    ユーザーの操作    |
  |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
  |    443    |    Azure Storage    |    いいえ     |    [はい]    |    NSG を追加する場合は、送信トラフィックに対してこのポートが開放されていることを確認してください。    |

   また、VNet を提供するカスタムの DNS サーバーが Azure Storage エンドポイントを解決できることを確認します。 具体的には、フォーム `<account>.table.core.windows.net`、`<account>.queue.core.windows.net`、`<account>.blob.core.windows.net` の URL を解決できる必要があります。 

   Resource Manager ベースの NSG を追加する場合は、[サービス タグ](../articles/virtual-network/security-overview.md#service-tags)を使用して、送信接続用の特定のリージョンの Storage IP アドレスを選択できます。 Storage IP アドレスは、Batch アカウントおよび VNet と同じリージョンである必要があることに注意してください。 サービス タグは、現在、選択された Azure リージョンでプレビュー段階にあります。