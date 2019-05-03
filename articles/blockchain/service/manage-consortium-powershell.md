---
title: Azure Blockchain consortium správa pomocí Powershellu
description: Jak spravovat členy consortium služba Blockchain v Azure pomocí Powershellu
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bef0c5d776e8ab6424b8604a49782088c45b0538
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028227"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Spravovat členy consortium ve službě Azure Blockchain pomocí Powershellu

Prostředí PowerShell můžete použít ke správě blockchain consortium členy pro vaši službu Azure Blockchain. Členové s oprávněním správce můžete pozvat, přidat, odebrat a změní rolí pro všechny účastníky v blockchainu consortium. Členové s oprávněním uživatel můžete zobrazit všechny účastníky, kterými blockchain consortium a můžete změnit jejich zobrazovaného jména člena.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření blockchainové členu pomocí webu Azure portal](create-member.md)
* Další informace o konsorcia, členy a uzlů najdete v tématu [consortium služby Azure Blockchain](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="install-powershell-module"></a>Nainstalujte modul prostředí PowerShell

Nainstalujte balíček Microsoft.AzureBlockchainService.ConsortiumManagement.PS z Galerie prostředí PowerShell.

```powershell
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="establish-a-web3-connection"></a>Navázání připojení Web3

Můžete spravovat členy consortium, budete muset Web3 připojení na koncový bod služby Azure Blockchain člena. Tento skript můžete použít k nastavení globálních proměnných, které se dá použít při volání rutiny pro správu consortium.

```powershell
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Nahraďte \<heslo účtu člen\> heslem účtu člen jste použili při vytváření člena.

Najdete ostatní hodnoty na webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do vaší výchozí člen služby Azure Blockchain **přehled** stránky.

    ![Přehled členů](./media/manage-consortium-powershell/member-overview.png)

    Nahraďte \<členský účet\>, a \<RootContract adresu\> s hodnotami z portálu.

1. Adresa koncového bodu, vyberte **transakce uzly** a vyberte uzel transakce.
1. Vyberte **připojovací řetězce**.

    ![Připojovací řetězce](./media/manage-consortium-powershell/connection-strings.png)

    Nahraďte \<adresu koncového bodu\> s hodnotou od **HTTPS (přístupový klíč 1)** nebo **HTTPS (přístupový klíč 2)**.

## <a name="network-and-smart-contract-management"></a>Síť a správu inteligentní kontraktu

Použití sítě a rutiny inteligentní smlouvy k navázání připojení k vaší blockchain koncový bod chytrých kontraktů za správu consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Se připojí k consortium správu inteligentní smluv, které se používají ke správě a vynucení členů v rámci konsorcia.

```powershell
Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| RootContractAddress | Adresa kontraktu kořenové chytrých kontraktů consortium správy | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

**Příklad**

```powershell
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Tuto rutinu použijte k vytvoření objektu pro uložení účet pro správu informace o vzdáleném uzlu.

```powershell
Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adresa účtu člen Blockchain | Ano |
| ManagedAccountPassword | Účet adresy a hesla | Ano |

**Příklad**

```powershell
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Naváže připojení ke koncovému bodu vzdáleného volání Procedur uzlu transakce.

```powershell
New-Web3Connection [-RemoteRPCEndpoint <String>]
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adresa koncového bodu člen Blockchain | Ano |


**Příklad**

```powershell
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Správa členů Consortium

Správa členů v rámci konsorcia pomocí rutin správy člen consortium. Dostupné akce závisí na vaší roli consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Získá podrobnosti o členovi nebo seznam členů konsorcia.

```powershell
Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Název | Jméno člena služba Blockchain v Azure, budete chtít načíst podrobnosti o. Pokud zadáte název členu, budou vráceny podrobnosti člena. Pokud název je vynechán, vrátí se seznam všech členů consortium. | Ne |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

**Příklad**

```powershell
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Příklad výstupu**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Odebrat BlockchainMember

Odebere člena blockchain.

```powershell
Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Název | Název člena odebrat | Ano |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

**Příklad**

```powershell
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Nastaví blockchain atributy členu, včetně zobrazovaný název a consortium role.

Consortium správci můžou nastavit **DisplayName** a **Role** pro všechny členy. Consortium člena k roli uživatele můžete změnit pouze své vlastní členské zobrazovaný název.

```powershell
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Název | Název člena blockchain | Ano |
| Zobrazovaný název | Nové zobrazované jméno | Ne |
| AccountAddress | Adresa účtu | Ne |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client |  Objekt Web3Client získané z New-Web3Connection| Ano |

**Příklad**

```powershell
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Správa pozvánku Consortium člena

Pomocí rutiny správy pozvání W3C člena můžete spravovat consortium člen pozvánky. Dostupné akce závisí na vaší roli consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Nové členy pro konsorcia Pozvěte.

```powershell
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure pozvaných členů | Ano |
| Role | Consortium role. Hodnoty mohou být správce nebo uživatele. Správce je role správce consortium. Uživatel je roli člena consortium. | Ano |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

**Příklad**

```powershell
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Načte nebo Vypíše stav žádosti člen consortium.

```powershell
Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure pozvaných členů. Pokud je zadané ID předplatného, budou vráceny Podrobnosti pozvánky ID předplatného. Pokud ID předplatného je vynechán, se vrátí seznam všech členských pozvánky. | Ne |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

**Příklad**

```powershell
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Příklad výstupu**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Odebrat BlockchainMemberInvitation

Odvolá pozvání členů consortium.

```powershell
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena odvolat | Ano |
| Členové | Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

**Příklad**

```powershell
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Nastaví **Role** existující pozvání. Pouze správci consortium můžou měnit pozvánky.

```powershell
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure pozvaných členů | Ano |
| Role | Nová role consortium pro pozvánku. Hodnoty mohou být **uživatele** nebo **správce** | Ano |
| Členové |  Členové objektu získané z ConsortiumManagementContracts importu | Ano |
| Web3Account | Objekt Web3Account získané z Web3Account importu | Ano |
| Web3Client | Objekt Web3Client získané z New-Web3Connection | Ano |

**Příklad**

```powershell
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Další postup

Další informace o konsorcia, členy a uzlů najdete v tématu:

> [!div class="nextstepaction"]
> [Azure Blockchain služby W3C](consortium.md)