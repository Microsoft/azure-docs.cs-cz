---
title: 'Azure AD Connect: Konfigurace oprávnění účtu konektoru služba AD DS | Microsoft Docs'
description: Tento dokument popisuje, jak nakonfigurovat účet konektoru služba AD DS pomocí nového modulu PowerShellu pro ADSyncConfig.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62bfc528886767bc09159ca2a2696c8c9264b307
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349935"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Konfigurace oprávnění účtu konektoru služby AD DS 

Modul PowerShell s názvem [ADSyncConfig. psm1](reference-connect-adsyncconfig.md) byl představený pomocí buildu 1.1.880.0 (vydaný v srpnu 2018), který obsahuje kolekci rutin, které vám pomůžou nakonfigurovat správná oprávnění služby Active Directory pro nasazení Azure AD Connect. 

## <a name="overview"></a>Přehled 
Následující rutiny PowerShellu se dají použít k nastavení oprávnění služby Active Directory pro účet konektoru služba AD DS pro každou funkci, kterou vyberete, aby se povolila v Azure AD Connect. Aby nedošlo k potížím, měli byste předem připravit oprávnění služby Active Directory, kdykoli budete chtít nainstalovat Azure AD Connect pomocí vlastního doménového účtu pro připojení k doménové struktuře. Tento modul ADSyncConfig lze také použít ke konfiguraci oprávnění po nasazení Azure AD Connect.

![Přehled účtu služby AD DS](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Pro Azure AD Connect expresní instalaci se automaticky generovaný účet (MSOL_nnnnnnnnnn) vytvoří ve službě Active Directory se všemi potřebnými oprávněními, takže nemusíte používat tento modul ADSyncConfig, pokud jste neblokovali dědění oprávnění na organizační jednotky nebo na konkrétní objekty služby Active Directory, které chcete synchronizovat do Azure AD. 
 
### <a name="permissions-summary"></a>Souhrn oprávnění 
Následující tabulka poskytuje souhrn oprávnění vyžadovaných pro objekty služby Active Directory: 

| Funkce | Oprávnění |
| --- | --- |
| funkce MS-DS-ConsistencyGuid |Oprávnění ke čtení a zápisu do atributu ms-DS-ConsistencyGuid dokumentovaného v tématu [Koncepty návrhu – použití MS-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizace hodnot hash hesel |<li>Replikovat změny adresáře</li>  <li>Replikovat všechny změny adresáře |
| Hybridní nasazení Exchange |Oprávnění ke čtení a zápisu k atributům popsaným v tématu [Exchange Hybrid zpětný zápis](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) pro uživatele, skupiny a kontakty. |
| Veřejná složka pošty Exchange |Oprávnění ke čtení pro atributy dokumentované ve [veřejné složce Exchange mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) pro veřejné složky. | 
| Zpětný zápis hesla |Oprávnění ke čtení a zápisu k atributům popsaným v tématu [Začínáme se správou hesel](../authentication/tutorial-enable-sspr-writeback.md) pro uživatele. |
| Zpětný zápis zařízení |Oprávnění ke čtení a zápisu pro objekty zařízení a kontejnery dokumentované ve [zpětném zápisu zařízení](how-to-connect-device-writeback.md). |
| Zpětný zápis skupin |Čtení, vytváření, aktualizace a odstraňování objektů skupin pro synchronizované **skupiny sady Office 365**.|

## <a name="using-the-adsyncconfig-powershell-module"></a>Použití modulu PowerShellu pro ADSyncConfig 
Modul ADSyncConfig vyžaduje [pro služba AD DS nástroje pro vzdálenou správu serveru (RSAT)](/windows-server/remote/remote-server-administration-tools) , protože závisí na modulu a nástrojích prostředí PowerShell pro služba AD DS. Pokud chcete nainstalovat RSAT pro služba AD DS, otevřete okno Windows PowerShellu s názvem spustit jako správce a proveďte následující: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurace](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Můžete také zkopírovat soubor **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** na řadič domény, který už má nainstalované nástroje pro vzdálenou správu pro služba AD DS a použít tento modul PowerShellu.

Pokud chcete začít používat ADSyncConfig, musíte načíst modul v okně Windows PowerShellu: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Pokud chcete kontrolovat všechny rutiny zahrnuté v tomto modulu, můžete zadat:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Zaškrtnout](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Každá rutina má stejné parametry pro zadání účtu konektoru služba AD DS a přepínače AdminSDHolder. Pokud chcete zadat účet konektoru služba AD DS, můžete zadat název účtu a doménu nebo jenom rozlišující název účtu (DN).

třeba

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Ani

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Ujistěte se, že jste nahradili `<ADAccountName>` `<ADDomainName>` a `<ADAccountDN>` se správnými hodnotami pro vaše prostředí.

V případě, že nechcete měnit oprávnění pro kontejner AdminSDHolder, použijte přepínač `-SkipAdminSdHolders` . 

Ve výchozím nastavení se všechny rutiny Set permissions pokusí nastavit služba AD DS oprávnění v kořenu každé domény v doménové struktuře, což znamená, že uživatel, který spouští relaci PowerShellu, vyžaduje v každé doméně v doménové struktuře práva správce domény.  Z důvodu tohoto požadavku se doporučuje použít Správce podnikové sítě z kořenového adresáře doménové struktury. Pokud má nasazení Azure AD Connect více konektorů služba AD DS, bude nutné spustit stejnou rutinu v každé doménové struktuře, která má konektor služba AD DS. 

Můžete také nastavit oprávnění pro konkrétní organizační jednotku nebo služba AD DS objekt pomocí parametru `-ADobjectDN` následovaného rozlišujícím názvem cílového objektu, kde chcete nastavit oprávnění. Při použití cílového ADobjectDN bude rutina nastavovat oprávnění pouze pro tento objekt, nikoli pro kořen domény nebo kontejner AdminSDHolder. Tento parametr může být užitečný, když máte určité organizační jednotky nebo služba AD DS objekty, u kterých je dědičnost oprávnění zakázaná (viz téma Vyhledání služba AD DS objektů s děděním oprávnění zakázané). 

Výjimkou těchto běžných parametrů je `Set-ADSyncRestrictedPermissions` rutina, která se používá k nastavení oprávnění pro účet služba AD DSového konektoru a `Set-ADSyncPasswordHashSyncPermissions` rutina, protože oprávnění požadovaná pro synchronizaci hodnoty hash hesla se nastavují jenom v kořenu domény, takže tato rutina nezahrnuje `-ObjectDN` `-SkipAdminSdHolders` parametry nebo.

### <a name="determine-your-ad-ds-connector-account"></a>Určení účtu konektoru služba AD DS 
V případě, že je již Azure AD Connect nainstalován a chcete zjistit, co je účet služba AD DS Connector, který je aktuálně používán Azure AD Connect, můžete spustit rutinu: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Vyhledání objektů služba AD DS s děděním oprávnění zakázáno 
V případě, že chcete zjistit, zda služba AD DS objekt s děděním oprávnění zakázán, můžete spustit: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Ve výchozím nastavení tato rutina bude hledat pouze organizační jednotky s zakázanou dědičností, ale můžete zadat jiné třídy služba AD DS objektů v `-ObjectClass` parametru nebo použít * pro všechny třídy objektů následujícím způsobem: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Zobrazení služba AD DS oprávnění objektu 
Pomocí níže uvedené rutiny můžete zobrazit seznam oprávnění aktuálně nastavených pro objekt služby Active Directory zadáním jeho rozlišujícího typu: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Konfigurace oprávnění účtu konektoru služby AD DS 
 
### <a name="configure-basic-read-only-permissions"></a>Konfigurace základních oprávnění Read-Only 
Chcete-li nastavit základní oprávnění jen pro čtení pro účet služba AD DSho konektoru, když nepoužíváte žádnou Azure AD Connect funkci, spusťte příkaz: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ani 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Tato rutina nastaví následující oprávnění: 
 

|Typ |Name |Access |Platí pro| 
|-----|-----|-----|-----|
|Povolit |Účet konektoru služba AD DS |Číst všechny vlastnosti |Podřízené objekty zařízení| 
|Povolit |Účet konektoru služba AD DS|Číst všechny vlastnosti |Odvozené objekty InetOrgPerson| 
|Povolit |Účet konektoru služba AD DS |Číst všechny vlastnosti |Objekty potomkového počítače| 
|Povolit |Účet konektoru služba AD DS |Číst všechny vlastnosti |Odvozené objekty foreignSecurityPrincipal| 
|Povolit |Účet konektoru služba AD DS |Číst všechny vlastnosti |Podřízené objekty skupiny| 
|Povolit |Účet konektoru služba AD DS |Číst všechny vlastnosti |Podřízené objekty uživatele| 
|Povolit |Účet konektoru služba AD DS |Číst všechny vlastnosti |Podřízené objekty kontaktu| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Konfigurace služby MS-DS-Consistency-GUID oprávnění 
Pokud chcete nastavit oprávnění pro účet konektoru služba AD DS při použití atributu ms-DS-Consistency-GUID jako zdrojového ukotvení (označovaného taky jako možnost "nechat Azure spravovat zdrojovou kotvu pro mě"), spusťte: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

ani 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Tato rutina nastaví následující oprávnění: 

|Typ |Name |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit|Účet konektoru služba AD DS|Vlastnost čtení/zápisu|Podřízené objekty uživatele|

### <a name="permissions-for-password-hash-synchronization"></a>Oprávnění pro synchronizaci hodnot hash hesel 
Pokud chcete nastavit oprávnění pro účet konektoru služba AD DS při synchronizaci hodnot hash hesel, spusťte: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


ani 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Tato rutina nastaví následující oprávnění: 

|Typ |Name |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služba AD DS |Replikují se změny adresáře. |Pouze tento objekt (doménový kořen)| 
|Povolit |Účet konektoru služba AD DS |Replikují se všechny změny adresáře. |Pouze tento objekt (doménový kořen)| 
  
### <a name="permissions-for-password-writeback"></a>Oprávnění ke zpětnému zápisu hesla 
Pokud chcete nastavit oprávnění pro účet služba AD DSového konektoru při použití zpětného zápisu hesla, spusťte příkaz: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ani

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Tato rutina nastaví následující oprávnění: 

|Typ |Name |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služba AD DS |Resetování hesla |Podřízené objekty uživatele| 
|Povolit |Účet konektoru služba AD DS |Zápis vlastnosti lockoutTime |Podřízené objekty uživatele| 
|Povolit |Účet konektoru služba AD DS |Zápis vlastnosti pwdLastSet |Podřízené objekty uživatele| 

### <a name="permissions-for-group-writeback"></a>Oprávnění pro zpětný zápis skupin 
Pokud chcete nastavit oprávnění pro účet služba AD DSového konektoru při použití zpětného zápisu skupiny, spusťte příkaz: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
ani 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Tato rutina nastaví následující oprávnění: 

|Typ |Name |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služba AD DS |Obecný čtení a zápis |Všechny atributy skupiny a podobjektů typu objektu| 
|Povolit |Účet konektoru služba AD DS |Vytvořit nebo odstranit podřízený objekt |Všechny atributy skupiny a podobjektů typu objektu| 
|Povolit |Účet konektoru služba AD DS |Odstranit nebo odstranit objekty stromu|Všechny atributy skupiny a podobjektů typu objektu|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Oprávnění pro hybridní nasazení systému Exchange 
Pokud chcete nastavit oprávnění pro účet služba AD DSového konektoru při použití hybridního nasazení Exchange, spusťte: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ani 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Tato rutina nastaví následující oprávnění:  
 

|Typ |Name |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služba AD DS |Čtení a zápis všech vlastností |Podřízené objekty uživatele| 
|Povolit |Účet konektoru služba AD DS |Čtení a zápis všech vlastností |Odvozené objekty InetOrgPerson| 
|Povolit |Účet konektoru služba AD DS |Čtení a zápis všech vlastností |Podřízené objekty skupiny| 
|Povolit |Účet konektoru služba AD DS |Čtení a zápis všech vlastností |Podřízené objekty kontaktu| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Oprávnění pro veřejné složky pošty Exchange (Preview) 
Pokud chcete nastavit oprávnění pro účet služba AD DSového konektoru při používání funkce veřejné složky Exchange mail, spusťte: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ani 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Tato rutina nastaví následující oprávnění: 

|Typ |Name |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služba AD DS |Číst všechny vlastnosti |Odvozené objekty PublicFolder| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Omezení oprávnění pro účet konektoru služba AD DS 
Tento skript PowerShellu bude zpřísnit oprávnění pro účet konektoru služby AD, který jste zadali jako parametr. Oprávnění k zpřísnění zahrnuje následující kroky: 

- Zakázat dědění pro zadaný objekt 
- Odeberte všechny položky ACE u konkrétního objektu, s výjimkou ACE specifických pro sebe jako pro sebe, aby výchozí oprávnění zůstala beze změny. 
 
  Parametr-ADConnectorAccountDN je účet služby AD, jehož oprávnění je potřeba zpřísnit. Obvykle se jedná o účet domény MSOL_nnnnnnnnnnnn, který je nakonfigurovaný v konektoru služba AD DS (viz určení účtu konektoru služby služba AD DS). Parametr-Credential je nutný k určení účtu správce, který má potřebná oprávnění k omezení oprávnění služby Active Directory pro cílový objekt AD. Většinou se jedná o podnik nebo správce domény.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Příklad: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Tato rutina nastaví následující oprávnění: 

|Typ |Name |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |SYSTEM |Úplné řízení |Tento objekt 
|Povolit |Enterprise Admins |Úplné řízení |Tento objekt 
|Povolit |Domain Admins |Úplné řízení |Tento objekt 
|Povolit |Administrators |Úplné řízení |Tento objekt 
|Povolit |Podnikové řadiče domény |Vypsat obsah |Tento objekt 
|Povolit |Podnikové řadiče domény |Číst všechny vlastnosti |Tento objekt 
|Povolit |Podnikové řadiče domény |Oprávnění ke čtení |Tento objekt 
|Povolit |Authenticated Users |Vypsat obsah |Tento objekt 
|Povolit |Authenticated Users |Číst všechny vlastnosti |Tento objekt 
|Povolit |Authenticated Users |Oprávnění ke čtení |Tento objekt 

## <a name="next-steps"></a>Další kroky
- [Azure AD Connect: Účty a oprávnění](reference-connect-accounts-permissions.md)
- [Expresní instalace](how-to-connect-install-express.md)
- [Vlastní instalace](how-to-connect-install-custom.md)
- [Referenční informace k ADSyncConfig](reference-connect-adsyncconfig.md)