---
title: Správa členů konsorcia Azure Blockchain Service – PowerShell
description: Zjistěte, jak spravovat členy konsorcia Azure Blockchain Service pomocí Azure PowerShellu.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505977"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Správa členů konsorcia ve službě Azure Blockchain service pomocí PowerShellu

Pomocí PowerShellu můžete spravovat členy blockchainového konsorcia pro vaši službu Azure Blockchain. Členové, kteří mají oprávnění správce, mohou pozvat, přidat, odebrat a změnit role pro všechny účastníky konsorcia blockchain. Členové, kteří mají uživatelská oprávnění, mohou zobrazit všechny účastníky blockchainového konsorcia a změnit jejich zobrazované jméno členů.

## <a name="prerequisites"></a>Požadavky

* Vytvořte člena blockchainu pomocí [portálu Azure](create-member.md).
* Další informace o konsorciích, členech a uzlech najdete v [tématu konsorcium služby Azure Blockchain Service](consortium.md).

## <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Cloud shell můžete otevřít také na samostatné kartě prohlížeče tak, že přejdete na [shell.azure.com/powershell](https://shell.azure.com/powershell). Vyberte **Kopírovat,** chcete-li zkopírovat bloky kódu, vložte je do prostředí Cloud Shell a vyberte **Enter,** chcete-li ho spustit.

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Nainstalujte balíček Microsoft.AzureBlockchainService.ConsortiumManagement.PS z Galerie prostředí PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Nastavení předvolby informací

Další informace můžete získat při provádění rutin nastavením proměnné předvoleb informací. Ve výchozím nastavení je *hodnota $InformationPreference* nastavena na *hodnotu SilentlyContinue*.

Pro podrobnější informace z rutin nastavte předvolbu v prostředí PowerShell takto:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Navázání připojení Web3

Chcete-li spravovat členy konsorcia, vytvořte připojení Web3 ke koncovému bodu člena služby Blockchain Service. Tento skript můžete použít k nastavení globálních proměnných pro volání rutin správy konsorcia.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Nahraďte * \<\> heslo členského účtu* heslem členského účtu, které jste použili při vytváření člena.

Další hodnoty najdete na webu Azure Portal:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na výchozí stránku **Přehled** členů služby Blockchain.

    ![Přehled členů](./media/manage-consortium-powershell/member-overview.png)

    Nahraďte * \<členský účet\> * a * \<adresu\> RootContract* hodnotami z portálu.

1. Pro adresu koncového bodu vyberte **Uzly transakcí**a pak vyberte **výchozí uzel transakce**. Výchozí uzel má stejný název jako člen blockchainu.
1. Vyberte **připojovací řetězce**.

    ![Připojovací řetězce](./media/manage-consortium-powershell/connection-strings.png)

    * \<Nahraďte\> adresu koncového bodu* hodnotou z **protokolu HTTPS (přístupový klíč 1)** nebo **HTTPS (přístupový klíč 2).**

## <a name="manage-the-network-and-smart-contracts"></a>Správa síťových a inteligentních kontraktů

Pomocí rutin sítě a inteligentních smluv můžete navázat připojení k inteligentním kontraktům koncového bodu blockchainu, které jsou zodpovědné za správu konsorcia.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Pomocí této rutiny se můžete připojit k inteligentním kontraktům správy konsorcia. Tyto smlouvy se používají ke správě a vynucení členů v rámci konsorcia.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Adresa RootContract | Adresa root kontraktu inteligentních smluv pro správu konsorcia | Ano |
| Klient Web3 | Objekt Web3Client získaný z připojení New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Tato rutina slouží k vytvoření objektu pro uložení informací pro účet správy vzdáleného uzlu.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Adresa spravovaného účtu | Adresa členského účtu blockchainu | Ano |
| Klient ManagedAccountPassword | Heslo adresy účtu | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Nové připojení Web3

Tato rutina slouží k navázání připojení ke koncovému bodu vzdáleného volání procedur uzlu transakce.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Vzdálený koncový bod RPC | Adresa koncového bodu člena blockchainu | Ano |

#### <a name="example"></a>Příklad

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Správa členů konsorcia

Rutiny správy členů konsorcia slouží ke správě členů v rámci konsorcia. Dostupné akce závisí na roli konsorcia.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Pomocí této rutiny získat podrobnosti o členech nebo seznam členů konsorcia.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Name (Název) | Název člena služby Blockchain, o kterém chcete načíst podrobnosti. Po zadání názvu vrátí podrobnosti člena. Pokud je název vynechán, vrátí seznam všech členů konsorcia. | Ne |
| Členové | Objekt členů získaný z importu ConsortiumManagementContracts | Ano |
| Klient Web3 | Objekt Web3Client získaný z připojení New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

[Navázání připojení Web3](#establish-a-web3-connection) pro nastavení proměnné $ContractConnection.

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

### <a name="remove-blockchainmember"></a>Odstranit-BlockchainMember

Pomocí této rutiny odeberte člena blockchainu.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Name (Název) | Název člena, který chcete odebrat | Ano |
| Členové | Objekt členů získaný z importu ConsortiumManagementContracts | Ano |
| Web3Účet | Objekt Web3Account získaný z importu Web3Account | Ano |
| Klient Web3 | Objekt Web3Client získaný z připojení New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

[Navázání připojení Web3](#establish-a-web3-connection) pro nastavení proměnných $ContractConnection a $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Člen set-blockchainu

Tato rutina slouží k nastavení atributů členů blockchainu, včetně zobrazované ho názvu a role konsorcia.

Správci konsorcia mohou nastavit **DisplayName** a **Role** pro všechny členy. Člen konsorcia s rolí uživatele může změnit pouze zobrazovaný název svého vlastního člena.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| Name (Název) | Název člena blockchainu | Ano |
| DisplayName | Nový zobrazovaný název | Ne |
| Adresa účtu | Adresa účtu | Ne |
| Členové | Objekt členů získaný z importu ConsortiumManagementContracts | Ano |
| Web3Účet | Objekt Web3Account získaný z importu Web3Account | Ano |
| Klient Web3 |  Objekt Web3Client získaný z připojení New-Web3Connection| Ano |

#### <a name="example"></a>Příklad

[Navázání připojení Web3](#establish-a-web3-connection) pro nastavení proměnných $ContractConnection a $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Správa pozvánek členů konsorcia

Pomocí rutin správy členů konsorcia můžete spravovat pozvánky členů konsorcia. Dostupné akce závisí na roli konsorcia.

### <a name="new-blockchainmemberinvitation"></a>Pozvánka na nové holicí pozice

Pomocí této rutiny můžete pozvat nové členy do konsorcia.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena, který má pozvat | Ano |
| Role | Role konsorcia. Hodnoty mohou být ADMIN nebo USER. ADMIN je role správce konsorcia. USER je role člena konsorcia. | Ano |
| Členové | Objekt členů získaný z importu ConsortiumManagementContracts | Ano |
| Web3Účet | Objekt Web3Account získaný z importu Web3Account | Ano |
| Klient Web3 | Objekt Web3Client získaný z připojení New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

[Navázání připojení Web3](#establish-a-web3-connection) pro nastavení proměnných $ContractConnection a $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Pozvánka na přístup k blockchainu

Tato rutina slouží k načtení nebo vypsat stav pozvánky člena konsorcia.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena pozvat. Pokud je k dispozici ID předplatného, vrátí podrobnosti pozvánky ID předplatného. Pokud id předplatného je vynechán, vrátí seznam všech pozvánek členů. | Ne |
| Členové | Objekt členů získaný z importu ConsortiumManagementContracts | Ano |
| Klient Web3 | Objekt Web3Client získaný z připojení New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

[Navázání připojení Web3](#establish-a-web3-connection) pro nastavení proměnné $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Příklad výstupu

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Odstranit-BlockchainMemberInvitation

Tato rutina slouží k odvolání pozvánky člena konsorcia.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena, který má být odvolán | Ano |
| Členové | Objekt členů získaný z importu ConsortiumManagementContracts | Ano |
| Web3Účet | Objekt Web3Account získaný z importu Web3Account | Ano |
| Klient Web3 | Objekt Web3Client získaný z připojení New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

[Navázání připojení Web3](#establish-a-web3-connection) pro nastavení proměnných $ContractConnection a $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Pozvánka na set-blockchain

Tato rutina slouží k nastavení **role** pro existující pozvánku. Pozvánky mohou měnit pouze správci konsorcia.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Popis | Požaduje se |
|-----------|-------------|:--------:|
| SubscriptionId | ID předplatného Azure člena, který má pozvat | Ano |
| Role | Nová role konsorcia pro pozvání. Hodnoty mohou být **UŽIVATEL** nebo **ADMIN**. | Ano |
| Členové |  Objekt členů získaný z importu ConsortiumManagementContracts | Ano |
| Web3Účet | Objekt Web3Account získaný z importu Web3Account | Ano |
| Klient Web3 | Objekt Web3Client získaný z připojení New-Web3Connection | Ano |

#### <a name="example"></a>Příklad

[Navázání připojení Web3](#establish-a-web3-connection) pro nastavení proměnných $ContractConnection a $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Další kroky

Další informace o konsorciích, členech a uzlech najdete v [tématu Konsorcium služby Azure Blockchain Service.](consortium.md)
