---
title: Správa členů ve službě Azure blockchain Service Consortium pomocí Azure PowerShell
description: Naučte se spravovat členy konsorcia služeb Azure blockchain pomocí Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 9f76597a91c0e22f57d1ba66ff1a16eea9002af0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250089"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Správa členů konsorcia ve službě Azure blockchain pomocí prostředí PowerShell

Pomocí PowerShellu můžete spravovat členy blockchain Consortium pro službu Azure blockchain. Členové, kteří mají oprávnění správce, mohou pozvat, přidat, odebrat a změnit role pro všechny účastníky v blockchain Consortium. Členové, kteří mají uživatelská oprávnění, mohou zobrazit všechny účastníky v blockchain Consortium a změnit jejich zobrazovaný název členů.

## <a name="prerequisites"></a>Požadavky

* Vytvořte člen blockchain pomocí [Azure Portal](create-member.md).
* Další informace o konsorciech, členech a uzlech najdete v tématu [konsorcium služby Azure blockchain](consortium.md).

## <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Cloud Shell můžete také otevřít na samostatné kartě prohlížeče tak, že kliknete na [Shell.Azure.com/PowerShell](https://shell.azure.com/powershell). Vyberte **Kopírovat** , chcete-li zkopírovat bloky kódu, vložte je do Cloud Shell a vyberte **ENTER** pro spuštění.

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Nainstalujte balíček Microsoft.AzureBlockchainService.ConsortiumManagement.PS z Galerie prostředí PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Nastavení předvolby informace

Při provádění rutin můžete získat další informace nastavením proměnné předvoleb informace. Ve výchozím nastavení je *$InformationPreference* nastavená na *SilentlyContinue*.

Chcete-li získat podrobnější informace z rutin, nastavte v PowerShellu předvolby následujícím způsobem:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Navázat připojení Web3

Ke správě členů konsorcia vytvořte Web3 připojení ke koncovému bodu člena služby blockchain. Tento skript můžete použít k nastavení globálních proměnných pro volání rutin správy konsorcia.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Nahraďte  *\<heslo\> členského účtu* heslem členského účtu, který jste použili při vytváření člena.

Vyhledejte další hodnoty v Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejít na výchozí stránku **přehledu** členů služby blockchain

    ![Přehled členů](./media/manage-consortium-powershell/member-overview.png)

    Nahraďte  *\<členský\> účet* a *adresuRootContracthodnotamizportálu.\> \<*

1. V poli adresa koncového bodu vyberte **uzly transakce**a pak vyberte **Výchozí uzel transakce**. Výchozí uzel má stejný název jako člen blockchain.
1. Vyberte **připojovací řetězce**.

    ![Připojovací řetězce](./media/manage-consortium-powershell/connection-strings.png)

    Nahraďte  *\<adresu\> koncového bodu* hodnotou z **https (přístupová klávesa 1)** nebo **https (přístupový klíč 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Správa sítě a inteligentních kontraktů

Pomocí rutin sítě a inteligentních kontraktů navažte připojení ke inteligentním kontraktům blockchain koncového bodu, které jsou zodpovědné za správu konsorcia.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Pomocí této rutiny se můžete připojit ke inteligentním smlouvám správy konsorcia. Tyto smlouvy se používají ke správě a prosazování členů v rámci konsorcia.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| RootContractAddress | Adresa kořenového kontraktu inteligentních kontraktů správy konsorcia | Ano |
| Web3Client | Objekt Web3Client získaný z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import – Web3Account

Pomocí této rutiny můžete vytvořit objekt, který bude obsahovat informace pro účet správy vzdáleného uzlu.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adresa členského účtu blockchain | Ano |
| ManagedAccountPassword | Heslo pro adresu účtu | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Tuto rutinu použijte k navázání připojení ke koncovému bodu RPC uzlu transakce.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adresa koncového bodu člena blockchain | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Správa členů konsorcia

Používejte rutiny správy členů konsorcia ke správě členů v rámci konsorcia. Dostupné akce závisí na vaší roli konsorcia.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Pomocí této rutiny můžete získat podrobnosti o členovi nebo seznam členů konsorcia.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| Name | Název člena služby blockchain, o kterém chcete načíst podrobnosti. Při zadání názvu vrátí podrobnosti člena. Pokud je název vynechán, vrátí seznam všech členů konsorcia. | Ne |
| Members | Objekt Members získaný z import-ConsortiumManagementContracts | Ano |
| Web3Client | Objekt Web3Client získaný z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Příklad výstupu

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Pomocí této rutiny můžete odebrat člena blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| Name | Název člena, který se má odebrat | Ano |
| Members | Objekt Members získaný z import-ConsortiumManagementContracts | Ano |
| Web3Account | Objekt Web3Account získaný z importu-Web3Account | Ano |
| Web3Client | Objekt Web3Client získaný z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Tuto rutinu použijte k nastavení atributů členů blockchain, včetně zobrazovaného jména a role konsorcia.

Správci konsorcia můžou nastavit vlastnosti **DisplayName** a **role** pro všechny členy. Člen konsorcia s rolí uživatele může měnit pouze zobrazovaný název vlastního člena.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| Name | Název členu blockchain | Ano |
| DisplayName | Nové zobrazované jméno | Ne |
| AccountAddress | Adresa účtu | Ne |
| Members | Objekt Members získaný z import-ConsortiumManagementContracts | Ano |
| Web3Account | Objekt Web3Account získaný z importu-Web3Account | Ano |
| Web3Client |  Objekt Web3Client získaný z New-Web3Connection| Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Spravovat pozvánky členů konsorcia

Pomocí rutin správy pozvání členů konsorcia spravujte pozvánky členů konsorcia. Dostupné akce závisí na vaší roli konsorcia.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Tuto rutinu použijte k pozvání nových členů do konsorcia.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena, který chcete pozvat | Ano |
| Role | Role konsorcia Hodnoty mohou být ADMIN nebo USER. Správce je role správce konsorcia. UŽIVATEL je role člena konsorcia. | Ano |
| Members | Objekt Members získaný z import-ConsortiumManagementContracts | Ano |
| Web3Account | Objekt Web3Account získaný z importu-Web3Account | Ano |
| Web3Client | Objekt Web3Client získaný z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Pomocí této rutiny můžete načíst nebo vypsat stav pozvánky člena konsorcia.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure, které má člen pozvat. Pokud je zadané ID předplatného, vrátí Podrobnosti pozvánky ID předplatného. Pokud je ID předplatného vynecháno, vrátí seznam všech pozvání členů. | Ne |
| Members | Objekt Members získaný z import-ConsortiumManagementContracts | Ano |
| Web3Client | Objekt Web3Client získaný z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Příklad výstupu

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Tuto rutinu použijte k odvolání pozvánky člena konsorcia.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena, který se má odvolat | Ano |
| Members | Objekt Members získaný z import-ConsortiumManagementContracts | Ano |
| Web3Account | Objekt Web3Account získaný z importu-Web3Account | Ano |
| Web3Client | Objekt Web3Client získaný z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Tuto rutinu použijte k nastavení **role** pro existující pozvánku. Pouze správci konsorcia mohou měnit pozvánky.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požadováno |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena, který chcete pozvat | Ano |
| Role | Nová role konsorcia pro pozvánku Hodnotou může být **uživatel** nebo **správce**. | Ano |
| Members |  Objekt Members získaný z import-ConsortiumManagementContracts | Ano |
| Web3Account | Objekt Web3Account získaný z importu-Web3Account | Ano |
| Web3Client | Objekt Web3Client získaný z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Další kroky

Další informace o konsorciích, členech a uzlech naleznete v tématu:

> [!div class="nextstepaction"]
> [Konsorcium služeb Azure blockchain](consortium.md)
