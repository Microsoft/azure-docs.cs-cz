---
title: 'Azure AD Connect: Konfigurace oprávnění účtu konektoru služby AD DS | Dokumenty společnosti Microsoft'
description: Tento dokument podrobně popisuje konfiguraci účtu konektoru služby AD DS pomocí nového modulu Prostředí PowerShell ADSyncConfig
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72515817"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Konfigurace oprávnění účtu konektoru služby AD DS 

Modul prostředí PowerShell s názvem [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) byl představen s sestavením 1.1.880.0 (vydaném v srpnu 2018), který obsahuje kolekci rutin, které vám pomohou nakonfigurovat správná oprávnění služby Active Directory pro nasazení služby Azure AD Connect. 

## <a name="overview"></a>Přehled 
Následující rutiny prostředí PowerShell lze použít k nastavení oprávnění služby Active Directory pro účet konektoru služby AD DS pro každou funkci, kterou vyberete, abyste ji povolili v Azure AD Connect. Chcete-li zabránit problémům, měli byste předem připravit oprávnění služby Active Directory, kdykoli budete chtít nainstalovat službu Azure AD Connect pomocí vlastního účtu domény pro připojení k doménové struktuře. Tento modul ADSyncConfig lze také použít ke konfiguraci oprávnění po nasazení služby Azure AD Connect.

![přehled účtu ad ds](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Pro instalaci služby Azure AD Connect Express se ve službě Active Directory vytvoří automaticky generovaný účet (MSOL_nnnnnnnnnn) se všemi potřebnými oprávněními, takže není nutné používat tento modul ADSyncConfig, pokud jste nezablokovali oprávnění dědičnost na organizační jednotky nebo na konkrétní objekty služby Active Directory, které chcete synchronizovat s Azure AD. 
 
### <a name="permissions-summary"></a>Souhrn oprávnění 
V následující tabulce je uveden souhrn oprávnění požadovaných pro objekty ad: 

| Funkce | Oprávnění |
| --- | --- |
| funkce ms-DS-ConsistencyGuid |Oprávnění ke čtení a zápisu atributu ms-DS-ConsistencyGuid dokumentovaného v [konceptech návrhu – použití ms-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizace hash hesla |<li>Replikovat změny adresáře</li>  <li>Replikovat všechny změny adresáře |
| Hybridní nasazení Exchange |Oprávnění ke čtení a zápisu k atributům dokumentovaným v [hybridním zpětném zápisu exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) pro uživatele, skupiny a kontakty. |
| Veřejná složka exchange mailu |Oprávnění ke čtení atributů zdokumentovaných ve [veřejné složce Pošty Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) pro veřejné složky. | 
| Zpětný zápis hesla |Oprávnění ke čtení a zápisu k atributům zdokumentovaným v [článku Začínáme se správou hesel](../authentication/howto-sspr-writeback.md) pro uživatele. |
| Zpětný zápis zařízení |Oprávnění ke čtení a zápisu objektům zařízení a kontejnerům dokumentovaným v [zpětném zápisu zařízení](how-to-connect-device-writeback.md). |
| Zpětný zápis skupin |Číst, vytvářet, aktualizovat a odstraňovat objekty skupinpro synchronizované **skupiny Office 365**.  Další informace naleznete v [tématu Skupinový zpětný zápis](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Použití modulu PowerShell ADSyncConfig 
Modul ADSyncConfig vyžaduje [nástroje pro vzdálenou správu serveru (RSAT) pro službu AD DS,](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) protože závisí na modulu a nástrojích prostředí AD DS PowerShell. Chcete-li nainstalovat službu RSAT pro službu AD DS, otevřete okno prostředí Windows PowerShell s příkazem Spustit jako správce a spusťte: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurace](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Soubor **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** můžete také zkopírovat do řadiče domény, který již má nainstalovanou službu RSAT pro službu AD DS, a použít odtud tento modul prostředí PowerShell.

Chcete-li začít používat nástroj ADSyncConfig, musíte modul načíst v okně prostředí Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Chcete-li zkontrolovat všechny rutiny obsažené v tomto modulu, můžete zadat:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Zaškrtnout](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Každá rutina má stejné parametry pro zadání účtu konektoru služby AD DS a přepínače AdminSDHolder. Chcete-li zadat účet konektoru služby AD DS, můžete zadat název účtu a doménu nebo pouze rozlišující název účtu (DN),

třeba

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Nebo;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Ujistěte se, `<ADDomainName>` `<ADAccountDN>` že nahradit `<ADAccountName>`, a správné hodnoty pro vaše prostředí.

V případě, že nechcete měnit oprávnění v kontejneru AdminSDHolder, použijte přepínač `-SkipAdminSdHolders`. 

Ve výchozím nastavení se všechny rutiny nastavených oprávnění pokusí nastavit oprávnění služby AD DS v kořenovém adresáři každé domény v doménové struktuře, což znamená, že uživatel, který používá relaci prostředí PowerShell, vyžaduje práva správce domény pro každou doménu v doménové struktuře.  Z důvodu tohoto požadavku se doporučuje použít správce rozlehlé sítě z kořenového adresáře doménové struktury. Pokud vaše nasazení Služby Azure AD Connect obsahuje více konektorů služby AD DS, bude nutné spustit stejnou rutinu v každé doménové struktuře, která má konektor služby AD DS. 

Můžete také nastavit oprávnění pro konkrétní objekt ou nebo AD DS pomocí parametru `-ADobjectDN` následovaného DN cílového objektu, kde chcete nastavit oprávnění. Při použití cílového adobjectdn, rutina nastaví oprávnění pouze pro tento objekt a nikoli na kořenové domény nebo AdminSDHolder kontejneru. Tento parametr může být užitečný, pokud máte určité objekty ounebo objektů AD DS, které mají zakázáno dědičnost oprávnění (viz Vyhledání objektů ad DS se zakázaným dědičností oprávnění) 

Výjimky z těchto běžných `Set-ADSyncRestrictedPermissions` parametrů jsou rutina, která slouží k nastavení oprávnění pro samotný `Set-ADSyncPasswordHashSyncPermissions` účet konektoru služby AD DS, a rutina, protože oprávnění požadovaná `-ObjectDN` pro `-SkipAdminSdHolders` synchronizaci hash hesel jsou nastavena pouze v kořenovém adresáři domény, proto tato rutina neobsahuje parametry nebo.

### <a name="determine-your-ad-ds-connector-account"></a>Určení účtu konektoru ad DS 
V případě, že je azure ad connect už nainstalovaný a chcete zkontrolovat, co je účet konektoru služby AD DS, který se aktuálně používá ve službě Azure AD Connect, můžete rutinu spustit: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Vyhledání objektů ve snaze ad DS se zakázaným dědičností oprávnění 
V případě, že chcete zkontrolovat, zda existuje nějaký objekt ad DS se zakázaným dědičností oprávnění, můžete spustit: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Ve výchozím nastavení bude tato rutina hledat pouze vou s zakázanou dědičností, `-ObjectClass` ale můžete zadat další třídy objektů AD DS v parametru nebo použít '*' pro všechny třídy objektů, a to následovně: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Zobrazení oprávnění objektu ve službě AD DS 
Pomocí níže uvedené rutiny můžete zobrazit seznam oprávnění aktuálně nastavených u objektu služby Active Directory poskytnutím funkce Rozlišit: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Konfigurace oprávnění účtu konektoru služby AD DS 
 
### <a name="configure-basic-read-only-permissions"></a>Konfigurace základních oprávnění jen pro čtení 
Chcete-li nastavit základní oprávnění jen pro čtení pro účet konektoru služby AD DS, když nepoužíváte žádnou funkci Azure AD Connect, spusťte: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


nebo; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Tato rutina nastaví následující oprávnění: 
 

|Typ |Name (Název) |Access |Platí pro| 
|-----|-----|-----|-----|
|Povolit |Účet konektoru ad ds |Čtení všech vlastností |Objekty podřízeného zařízení| 
|Povolit |Účet konektoru ad ds|Čtení všech vlastností |Potomek InetOrgPerson objekty| 
|Povolit |Účet konektoru ad ds |Čtení všech vlastností |Objekty potomka počítače| 
|Povolit |Účet konektoru ad ds |Čtení všech vlastností |Potomek cizích objektů SecurityPrincipal| 
|Povolit |Účet konektoru ad ds |Čtení všech vlastností |Objekty skupiny potomků| 
|Povolit |Účet konektoru ad ds |Čtení všech vlastností |Objekty uživatele potomků| 
|Povolit |Účet konektoru ad ds |Čtení všech vlastností |Objekty kontaktů potomků| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Konfigurace oprávnění ms-DS-konzistence-guid 
Chcete-li nastavit oprávnění pro účet konektoru služby AD DS při použití atributu ms-Ds-Consistency-Guid jako zdrojové kotvy (aka možnost "Let Azure manage the source anchor for me" – spusťte: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

nebo; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Tato rutina nastaví následující oprávnění: 

|Typ |Name (Název) |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit|Účet konektoru ad ds|Čtení a zápis, vlastnost|Objekty uživatele potomků|

### <a name="permissions-for-password-hash-synchronization"></a>Oprávnění pro synchronizaci hodnot hash hesel 
Chcete-li nastavit oprávnění pro účet konektoru služby AD DS při použití synchronizace hodnot hash hesel, spusťte: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


nebo; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Tato rutina nastaví následující oprávnění: 

|Typ |Name (Název) |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru ad ds |Replikace změn adresáře |Pouze tento objekt (kořenový adresář domény)| 
|Povolit |Účet konektoru ad ds |Replikace adresáře změní vše |Pouze tento objekt (kořenový adresář domény)| 
  
### <a name="permissions-for-password-writeback"></a>Oprávnění pro zpětný zápis hesla 
Chcete-li nastavit oprávnění pro účet konektoru služby AD DS při použití zpětného zápisu hesla, spusťte: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


nebo;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Tato rutina nastaví následující oprávnění: 

|Typ |Name (Název) |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru ad ds |Resetování hesla |Objekty uživatele potomků| 
|Povolit |Účet konektoru ad ds |Zapsat vlastnost lockoutTime |Objekty uživatele potomků| 
|Povolit |Účet konektoru ad ds |Vlastnost zápisu pwdLastSet |Objekty uživatele potomků| 

### <a name="permissions-for-group-writeback"></a>Oprávnění pro skupinový zpětný zápis 
Chcete-li nastavit oprávnění pro účet konektoru služby AD DS při použití zpětného zápisu skupiny, spusťte: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
nebo; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Tato rutina nastaví následující oprávnění: 

|Typ |Name (Název) |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru ad ds |Obecný čtení a zápis |Všechny atributy skupiny typů objektů a podobjektů| 
|Povolit |Účet konektoru ad ds |Vytvořit nebo odstranit podřízený objekt |Všechny atributy skupiny typů objektů a podobjektů| 
|Povolit |Účet konektoru ad ds |Odstranit nebo odstranit objekty stromu|Všechny atributy skupiny typů objektů a podobjektů|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Oprávnění pro hybridní nasazení Exchange 
Chcete-li nastavit oprávnění pro účet konektoru služby AD DS při použití hybridního nasazení Exchange, spusťte: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


nebo; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Tato rutina nastaví následující oprávnění:  
 

|Typ |Name (Název) |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru ad ds |Čtení a zápis všech vlastností |Objekty uživatele potomků| 
|Povolit |Účet konektoru ad ds |Čtení a zápis všech vlastností |Potomek InetOrgPerson objekty| 
|Povolit |Účet konektoru ad ds |Čtení a zápis všech vlastností |Objekty skupiny potomků| 
|Povolit |Účet konektoru ad ds |Čtení a zápis všech vlastností |Objekty kontaktů potomků| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Oprávnění pro veřejné složky pošty Exchange (preview) 
Chcete-li nastavit oprávnění pro účet konektoru služby AD DS při použití funkce Exchange Mail Public Folders, spusťte: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


nebo; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Tato rutina nastaví následující oprávnění: 

|Typ |Name (Název) |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru ad ds |Čtení všech vlastností |Objekty Descendant PublicFolder| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Omezení oprávnění pro účet konektoru ad ds 
Tento skript prostředí PowerShell zpřísní oprávnění pro účet konektoru služby AD, který je k dispozici jako parametr. Oprávnění k dotažení zahrnují následující kroky: 

- Zakázat dědičnost zadaného objektu 
- Odebrat všechny ACE na konkrétní objekt, s výjimkou ACE specifické pro SELF, jak chceme zachovat výchozí oprávnění beze změny, pokud jde o SELF. 
 
  Parametr -ADConnectorAccountDN je účet AD, jehož oprávnění je třeba zpřísnit. Obvykle se jedná o účet domény MSOL_nnnnnnnnnnnn, který je nakonfigurován v konektoru služby AD DS (viz Určení účtu konektoru služby AD DS). Parametr -Credential je nezbytný k určení účtu správce, který má potřebná oprávnění k omezení oprávnění služby Active Directory pro cílový objekt Služby AD. Obvykle se jedná o správce rozlehlé sítě nebo domény.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Příklad: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Tato rutina nastaví následující oprávnění: 

|Typ |Name (Název) |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |SYSTEM |Úplné řízení |Tento objekt 
|Povolit |Enterprise Admins |Úplné řízení |Tento objekt 
|Povolit |Domain Admins |Úplné řízení |Tento objekt 
|Povolit |Správci |Úplné řízení |Tento objekt 
|Povolit |Podnikové řadiče domény |Obsah seznamu |Tento objekt 
|Povolit |Podnikové řadiče domény |Číst všechny vlastnosti |Tento objekt 
|Povolit |Podnikové řadiče domény |Oprávnění ke čtení |Tento objekt 
|Povolit |Authenticated Users |Obsah seznamu |Tento objekt 
|Povolit |Authenticated Users |Číst všechny vlastnosti |Tento objekt 
|Povolit |Authenticated Users |Oprávnění ke čtení |Tento objekt 

## <a name="next-steps"></a>Další kroky
- [Azure AD Connect: Účty a oprávnění](reference-connect-accounts-permissions.md)
- [Expresní instalace](how-to-connect-install-express.md)
- [Vlastní instalace](how-to-connect-install-custom.md)
- [Referenční informace k ADSyncConfig](reference-connect-adsyncconfig.md)

