---
title: Spravovat členy ve consortium služba Blockchain v Azure pomocí Azure Powershellu
description: Zjistěte, jak můžete spravovat členy consortium služba Blockchain v Azure pomocí Azure Powershellu.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66493635"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Spravovat členy consortium ve službě Azure Blockchain s použitím prostředí PowerShell

Prostředí PowerShell můžete použít ke správě blockchain consortium členy pro vaši službu Azure Blockchain. Členové, kteří mají oprávnění správce můžete pozvat, přidat, odebrat a změnit role pro všechny účastníky v blockchainu consortium. Členové, kteří mají oprávnění uživatelů můžete zobrazit všechny účastníky, kterými blockchain consortium a změnit jejich zobrazovaného jména člena.

## <a name="prerequisites"></a>Požadavky

* Vytvoření členu blockchain s použitím [webu Azure portal](create-member.md).
* Další informace o konsorcia, členy a uzlů najdete v tématu [služby Azure Blockchain consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Cloud Shell můžete také otevřít v na samostatné kartě prohlížeče tak, že přejdete do [shell.azure.com/powershell](https://shell.azure.com/powershell). Vyberte **kopírování** kopírování bloky kódu, vložte ho do Cloud Shellu a vyberte **Enter** ho spustit.

## <a name="install-the-powershell-module"></a>Nainstalujte modul prostředí PowerShell

Nainstalujte balíček Microsoft.AzureBlockchainService.ConsortiumManagement.PS z Galerie prostředí PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Nastavit předvolby informace

Další informace získáte při provádění rutiny nastavením proměnné předvoleb informace. Ve výchozím nastavení *$InformationPreference* je nastavena na *SilentlyContinue*.

Podrobnější informace z rutiny nastavte prioritu v Powershellu takto:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Navázání připojení Web3

Můžete spravovat členy consortium, připojení Web3 do vašeho koncového bodu služby Blockchain člena. Tento skript můžete použít k nastavení globálních proměnných pro volání rutiny pro správu consortium.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Nahraďte *\<heslo účtu člen\>* heslem účtu člena, který jste použili při vytvoření člena.

Najdete ostatní hodnoty na webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte na svůj výchozí služba Blockchain člen **přehled** stránky.

    ![Přehled členů](./media/manage-consortium-powershell/member-overview.png)

    Nahraďte *\<členský účet\>* a *\<RootContract adresu\>* s hodnotami z portálu.

1. Adresa koncového bodu, vyberte **transakce uzly**a pak vyberte **výchozí transakce uzel**. Výchozí uzel má stejný název jako člen blockchain.
1. Vyberte **připojovací řetězce**.

    ![Připojovací řetězce](./media/manage-consortium-powershell/connection-strings.png)

    Nahraďte *\<adresu koncového bodu\>* s hodnotou od **HTTPS (přístupový klíč 1)** nebo **HTTPS (přístupový klíč 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Správa sítě a chytrých kontraktů

Použití sítě a rutiny inteligentní smlouvy k navázání připojení na koncový bod blockchain chytrých kontraktů za správu consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Tuto rutinu použijte k připojení k řízení consortium chytrých kontraktů. Tyto smlouvy se používají ke správě a vynucení členů v rámci konsorcia.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| RootContractAddress | Kořenové smlouvy adresu chytrých kontraktů consortium správy | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Tuto rutinu použijte k vytvoření objektu pro uložení informací pro účet pro správu vzdáleného uzlu.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adresa účtu člen Blockchain | Ano |
| ManagedAccountPassword | Účet adresy a hesla | Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Tuto rutinu použijte k navázání připojení ke koncovému bodu vzdáleného volání Procedur uzlu transakce.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adresa koncového bodu člen Blockchain | Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Spravovat členy W3C

Správa členů v rámci konsorcia pomocí rutin správy člen consortium. Dostupné akce závisí na vaší roli consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Pomocí této rutiny můžete získat podrobnosti o členovi nebo seznam členů konsorcia.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Název | Název člena Blockchain služby, který chcete načíst podrobnosti o. Při zadání názvu se vrátí podrobnosti člena. Pokud název je vynechán, vrátí seznam všech členů consortium. | Ne |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad:

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

### <a name="remove-blockchainmember"></a>Odebrat BlockchainMember

Tuto rutinu použijte k odebrání člena blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Name | Název člena odebrat | Ano |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Tuto rutinu použijte k nastavení blockchain atributy členu, včetně zobrazovaný název a roli consortium.

Consortium správci můžou nastavit **DisplayName** a **Role** pro všechny členy. Consortium člena k roli uživatele můžete změnit pouze své vlastní členské zobrazovaný název.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Name | Název člena blockchain | Ano |
| displayName | Nové zobrazované jméno | Ne |
| AccountAddress | Adresa účtu | Ne |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client |  Objekt Web3Client získané z New-Web3Connection| Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Správa členů consortium pozvánky

Rutiny pro správu pozvání W3C člen použijte ke správě consortium členy pozvánky. Dostupné akce závisí na vaší roli consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Tuto rutinu použijte k zvát nové členy pro konsorcia.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena pozvat | Ano |
| Role | Consortium role. Hodnoty mohou být správce nebo uživatele. Správce je role správce consortium. Uživatel je roli člena consortium. | Ano |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Tuto rutinu použijte k načtení nebo zobrazit stav člena konsorcia pozvánky.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena se pozvat. Pokud ID předplatného, ID je k dispozici, vrátí předplatné Podrobnosti pozvánky. Pokud ID předplatného je vynechán, vrátí seznam všech členských pozvánky. | Ne |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Příklad výstupu

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Odebrat BlockchainMemberInvitation

Tuto rutinu použijte k odvolat pozvánku členem consortium.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena odvolat | Ano |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Tuto rutinu použijte k nastavení **Role** existující pozvání. Pouze správci consortium můžou měnit pozvánky.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena pozvat | Ano |
| Role | Nová role consortium pro pozvánku. Hodnoty mohou být **uživatele** nebo **správce**. | Ano |
| Členové |  Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

#### <a name="example"></a>Příklad:

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Další postup

Další informace o konsorcia, členy a uzlů najdete v tématu:

> [!div class="nextstepaction"]
> [Azure Blockchain služby W3C](consortium.md)
