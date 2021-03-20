---
title: Vytvoření členu služby Azure blockchain – Azure PowerShell
description: Vytvoření členu služby Azure blockchain pro konsorcium blockchain pomocí Azure PowerShell.
ms.date: 9/22/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: b57c44e79d599ab41b2c3356ee337811acdf639d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948336"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Rychlý Start: Vytvoření člena blockchain služby Azure blockchain pomocí Azure PowerShell

V tomto rychlém startu nasadíte nového člena blockchain a konsorcia v Azure blockchain Service pomocí Azure PowerShell.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> I když je modul PowerShellu **AZ. blockchain** ve verzi Preview, musíte ho z rutiny nainstalovat samostatně z nástroje AZ PowerShell Module `Install-Module` . Až bude tento modul PowerShellu všeobecně dostupný, bude součástí budoucna k tomu, že vydává verze modulu PowerShell a jsou dostupné nativně z Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Registrace poskytovatele prostředků

Pokud používáte službu Azure blockchain poprvé, je nutné zaregistrovat poskytovatele prostředků **Microsoft. blockchain** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Zvolit konkrétní předplatné Azure

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definování proměnných

Opakovaně budete používat několik informací. Vytvořte proměnné pro uložení informací.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../../azure-resource-manager/management/overview.md) pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků na základě názvu v `$resourceGroupName` proměnné v oblasti určené v `$location` proměnné.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Vytvoření členu blockchain

Člen služby Azure Blockchain je blockchain uzel v privátní síti konsorcia blockchain.
Při zřizování člena můžete vytvořit nebo připojit se k síti konsorcia. Pro síť konsorcia potřebujete alespoň jednoho člena. Počet členů blockchain, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchain nebo mohou sdílet členy s ostatními účastníky. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).

Existuje několik parametrů a vlastností, které je třeba předat. Nahraďte ukázkové parametry hodnotami.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| Parametr | Popis |
|---------|-------------|
| **ResourceGroupName** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. Použijte skupinu prostředků, kterou jste vytvořili v předchozí části.
| **Název** | Jedinečný název, který identifikuje svého člena blockchain služby Azure blockchain. Název se používá pro adresu veřejného koncového bodu. Například, `myblockchainmember.blockchain.azure.com`.
| **Umístění** | Oblast Azure, ve které je vytvořen člen blockchain. Například, `westus2`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. Funkce nemusí být k dispozici v některých oblastech. Azure blockchain Data Manager je k dispozici v následujících oblastech Azure: Východní USA a Západní Evropa.
| **Heslo** | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování.
| **Protokol** | Protokol blockchain V současné době je protokol _kvora_ podporován.
| **Konsorcium** | Název konsorcia, která se má připojit nebo vytvořit Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).
| **ConsortiumManagementAccountPassword** | Heslo účtu konsorcia se označuje také jako heslo k členskému účtu. Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
| **Skladové** | Typ vrstvy **S0** pro Standard nebo **B0** pro Basic. Využijte _základní_ vrstvu pro vývoj, testování a kontrolu konceptů. Použijte úroveň _Standard_ pro nasazení produkčních úrovní. Úroveň _Standard_ byste měli použít také v případě, že používáte blockchain data Manager nebo odesíláte velký objem privátních transakcí. Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována.

Vytvoření člena blockchain a podpůrných prostředků trvá přibližně 10 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člen blockchain, který jste vytvořili pro další rychlý Start nebo kurz. Pokud už je nepotřebujete, můžete prostředky odstranit odstraněním `myResourceGroup` skupiny prostředků, kterou jste vytvořili pro rychlý Start.

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto článku, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člen služby Azure blockchain a nový konsorcium. Vyzkoušejte si další rychlý Start pro použití Azure blockchain Development Kit pro Ethereem pro připojení k členu služby Azure blockchain.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k připojení ke službě Azure blockchain](connect-vscode.md)