---
title: Azure Policy の JSON サンプル - 許可されている仮想ネットワーク ゲートウェイ SKU | Microsoft Docs
description: この JSON サンプル ポリシーでは、仮想ネットワーク ゲートウェイが適切な SKU とゲートウェイの種類を使用することを必須にします。
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 62502c3d88b1dde13f7ea5d77dc0f3bed5f19b65
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602052"
---
# <a name="allowed-virtual-network-gateway-skus"></a>許可されている仮想ネットワーク ゲートウェイ SKU

このポリシーでは、仮想ネットワーク ゲートウェイが適切な SKU とゲートウェイの種類を使用することを必須にします。 承認された SKU の配列と、承認されたゲートウェイの種類の配列を指定します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>サンプル テンプレート

[!code-json[main](../../../policy-templates/samples/Network/virtual-network-gateway-skus/azurepolicy.json "Allowed Virtual Network Gateway SKUs")]

このテンプレートは、[Azure Portal](#deploy-with-the-portal)、[PowerShell](#deploy-with-powershell)、または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイできます。

## <a name="deploy-with-the-portal"></a>ポータルでのデプロイ

[![Azure へのデプロイ](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fvirtual-network-gateway-skus%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "virtual-network-gateway-skus" -DisplayName "Allowed Virtual Network Gateway SKUs" -description "This policy enables you to specify a set of virtual network gateway SKUs that your organization can deploy." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfAllowedSKUs <Allowed SKUs> -gatewayType <Gateway Type> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'virtual-network-gateway-skus' --display-name 'Allowed Virtual Network Gateway SKUs' --description 'This policy enables you to specify a set of virtual network gateway SKUs that your organization can deploy.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "virtual-network-gateway-skus"
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

- 他の例については、「[Azure Policy のサンプル](../json-samples.md)」をご確認ください。