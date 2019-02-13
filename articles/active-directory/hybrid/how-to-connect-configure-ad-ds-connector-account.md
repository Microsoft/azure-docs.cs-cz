---
title: 'Azure AD Connect: Konfigurace oprávnění účtu AD DS konektorů | Dokumentace Microsoftu'
description: Tento dokument podrobně popisuje, jak nakonfigurovat účet konektoru služby AD DS pomocí nového modulu ADSyncConfig PowerShell
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d39305b4a8fafbd2fe2f1ac101f92597d6a3e4a0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189039"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Konfigurace oprávnění účtu AD DS konektoru 

Modul prostředí PowerShell s názvem [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) byla zavedena v systému sestavení 1.1.880.0 (vydané spolu. srpna 2018), který obsahuje sadu rutin, které vám pomohou nakonfigurovat správné oprávnění služby Active Directory pro vaši službu Azure AD Připojení nasazení. 

## <a name="overview"></a>Přehled 
Následující rutiny prostředí PowerShell slouží k nastavení oprávnění služby Active Directory účtu AD DS konektor pro jednotlivé funkce, kterou Pokud chcete povolit ve službě Azure AD Connect. Pokud chcete zabránit případné problémy, připravte si oprávnění služby Active Directory předem pokaždé, když chcete nainstalovat Azure AD Connect s použitím vlastní doménový účet pro připojení k doménové struktuře. Tento modul ADSyncConfig lze také nakonfigurovat oprávnění po nasazení služby Azure AD Connect.

![](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Pro instalaci Azure AD Connect Express automaticky generované účet (MSOL_nnnnnnnnnn) se vytvoří ve službě Active Directory s všechna nezbytná oprávnění, takže není potřeba tento modul ADSyncConfig použít, pokud není po zablokování oprávnění Dědičnost v organizační jednotky nebo na konkrétní objekty služby Active Directory, které chcete synchronizovat se službou Azure AD. 
 
### <a name="permissions-summary"></a>Souhrn oprávnění 
Následující tabulka obsahuje souhrn oprávnění vyžadovaných pro objekty AD: 

| Funkce | Oprávnění |
| --- | --- |
| MS-DS-ConsistencyGuid funkce |Oprávnění k zápisu na atribut ms-DS-ConsistencyGuid dokumentovány v článku [koncepty návrhu – použití ms-DS-ConsistencyGuid jako parametru sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizace hodnot hash hesel |<li>Replikace změn adresáře</li>  <li>Replikace adresáře se změní všechny |
| Hybridní nasazení systému Exchange |Oprávnění k zápisu do atributy uvedené v [zpětný zápis hybridní Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) pro uživatele, skupiny nebo kontakty. |
| Veřejné složky pošty Exchange |Oprávnění ke čtení pro atributy uvedené v [veřejné složky pošty Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) u veřejných složek. | 
| Zpětný zápis hesla |Oprávnění k zápisu do atributy uvedené v [Začínáme se správou hesel](../authentication/howto-sspr-writeback.md) pro uživatele. |
| Zpětný zápis zařízení |Oprávnění k zápisu do zařízení objektů a kontejnerů, které jsou popsány v [zpětný zápis zařízení](how-to-connect-device-writeback.md). |
| Zpětný zápis skupin |Přečtěte si, vytvoření, aktualizace a odstranění skupiny objektů, pro synchronizována **skupiny Office 365**.  Další informace najdete v části [zpětný zápis skupin](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Pomocí modulu ADSyncConfig PowerShell 
Modul ADSyncConfig vyžaduje [vzdálenou správu serveru (RSAT) pro službu AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) protože závisí na modulu Powershellu pro AD DS a nástroje. K instalaci pro vzdálenou správu serveru pro službu AD DS, otevřete okno Windows Powershellu s "Spustit jako správce" a spusťte: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurace](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Můžete také zkopírovat soubor **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** k řadiči domény, která už má pro vzdálenou správu serveru pro službu AD DS nainstalovaná a používat tento modul prostředí PowerShell z něj.

Pokud chcete začít používat ADSyncConfig budete muset načíst modul v okně Windows Powershellu: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Chcete-li zkontrolovat všechny rutiny zahrnuté v tomto modulu můžete zadat:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Zaškrtnout](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Každá rutina má stejné parametry k zadání účtu AD DS konektoru a k přepnutí AdminSDHolder. K určení účtu AD DS konektor, můžete zadat název účtu a doménu nebo jenom účet rozlišující název (DN),

například: 

`Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName ADaccount -ADConnectorAccountDomain Contoso`

Nebo; 

`Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADaccount,OU=AADconnect,DC=Contoso,DC=com'`

V případě, že nechcete měnit oprávnění ke kontejneru AdminSDHolder, použijte přepínač `-SkipAdminSdHolders`. 

Ve výchozím nastavení všechny rutiny sady oprávnění se pokusí nastavit v kořenovém adresáři každé domény v doménové struktuře, což znamená, že uživatel, který spustil relaci prostředí PowerShell vyžaduje oprávnění správce domény pro každou doménu v doménové struktuře služby AD DS oprávnění.  Kvůli tomuto požadavku se doporučuje použít správce rozlehlé sítě od kořene doménové struktury. Pokud má vaše nasazení služby Azure AD Connect více konektorů AD DS, bude nutný ke spuštění stejné rutiny v každé doménové struktuře, která má konektor služby AD DS. 

Můžete také nastavit oprávnění pro konkrétní objekt organizační jednotky nebo službě AD DS pomocí parametru `-ADobjectDN` následovaný rozlišující název cílového objektu, ve které chcete nastavit oprávnění. Při použití cílového ADobjectDN, rutina bude pouze a ne na kořenovou doménu nebo kontejner AdminSDHolder nastavit oprávnění u tohoto objektu. Tento parametr může být užitečné, když máte některé organizační jednotky nebo objektů služby AD DS, že dědičnost oprávnění zakázali (viz vyhledejte AD DS objekty s oprávnění dědičnost vypnuta) 

Výjimky z těchto společné parametry jsou `Set-ADSyncRestrictedPermissions` rutiny, která se používá k nastavení oprávnění pro účet konektoru AD DS, a `Set-ADSyncPasswordHashSyncPermissions` rutiny, protože oprávnění požadovaná pro synchronizace hodnot Hash hesel se nastavit pouze na kořen domény, proto Tato rutina se nenachází `-ObjectDN` nebo `-SkipAdminSdHolders` parametry.

### <a name="determine-your-ad-ds-connector-account"></a>Určení vašeho konektoru služby AD DS účtu 
V případě služby Azure AD Connect je již nainstalována a chcete zjistit, co je účet AD DS konektoru aktuálně používaných službou Azure AD Connect, můžete spustit rutinu: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Najít objekty služby AD DS se dědičnost oprávnění zakázáno 
V případě, že budete chtít zkontrolovat, zda je libovolný objekt služby AD DS se dědičnost oprávnění zakázaná, můžete spustit: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Ve výchozím nastavení, tato rutina bude pouze hledat organizační jednotky s zakázáno dědičnosti, ale můžete zadat jiné třídy objektu služby AD DS v `-ObjectClass` parametru nebo použijte "*" pro všechny třídy, objektu následujícím způsobem: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Zobrazit služby AD DS oprávnění objektu 
Chcete-li zobrazit seznam aktuálně nastavená u objektu služby Active Directory zadáním jeho DistinguishedName oprávnění můžete použít následující rutinu: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Konfigurace oprávnění účtu AD DS konektoru 
 
### <a name="configure-basic-read-only-permissions"></a>Nakonfigurujte základní oprávnění jen pro čtení 
Pokud chcete nastavit základní oprávnění jen pro čtení pro účet konektoru služby AD DS, bez použití jakékoli funkce Azure AD Connect, spusťte: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Nebo; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Tato rutina se nastavit následující oprávnění: 
 

|Type |Název |Access |Platí pro| 
|-----|-----|-----|-----|
|Povolit |Účet konektoru služby AD DS |Číst všechny vlastnosti |Objekty odvozené zařízení| 
|Povolit |Účet konektoru služby AD DS|Číst všechny vlastnosti |Odvozené objekty InetOrgPerson| 
|Povolit |Účet konektoru služby AD DS |Číst všechny vlastnosti |Odvozené objekty počítače| 
|Povolit |Účet konektoru služby AD DS |Číst všechny vlastnosti |Objekty odvozené sady cizích objektů zabezpečení| 
|Povolit |Účet konektoru služby AD DS |Číst všechny vlastnosti |Objekty potomkem skupiny| 
|Povolit |Účet konektoru služby AD DS |Číst všechny vlastnosti |Následné uživatelské objekty| 
|Povolit |Účet konektoru služby AD DS |Číst všechny vlastnosti |Odvozené objekty kontaktu| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Nakonfigurovat oprávnění, MS-DS-konzistence – Guid 
Pokud chcete nastavit oprávnění pro účet konektoru služby AD DS při použití atributu ms-Ds-konzistence – identifikátor Guid jako zdrojové ukotvení (označuje se také jako "Nechat Azure spravovat zdrojové ukotvení mi" možnost), spusťte: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Nebo; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Tato rutina se nastavit následující oprávnění: 

|Type |Název |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit|Účet konektoru služby AD DS|Vlastnost pro čtení/zápis|MS-DS-konzistence – Guid|Následné uživatelské objekty|

### <a name="permissions-for-password-hash-synchronization"></a>Oprávnění pro synchronizaci hodnot Hash hesel 
Pokud chcete nastavit oprávnění pro účet konektoru služby AD DS při použití synchronizace hodnot Hash hesel, spusťte: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Nebo; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Tato rutina se nastavit následující oprávnění: 

|Type |Název |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služby AD DS |Replikace změn adresáře |Pouze tento objekt (kořenové domény)| 
|Povolit |Účet konektoru služby AD DS |Replikace změn adresáře vše |Pouze tento objekt (kořenové domény)| 
  
### <a name="permissions-for-password-writeback"></a>Oprávnění pro zpětný zápis hesla 
Pokud chcete nastavit oprávnění pro účet konektoru služby AD DS při použití zpětný zápis hesla, spusťte: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Nebo;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Tato rutina se nastavit následující oprávnění: 

|Type |Název |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služby AD DS |Resetování hesla |Následné uživatelské objekty| 
|Povolit |Účet konektoru služby AD DS |Zapsat vlastnost lockoutTime |Následné uživatelské objekty| 
|Povolit |Účet konektoru služby AD DS |Zapsat vlastnost pwdLastSet |Následné uživatelské objekty| 

### <a name="permissions-for-group-writeback"></a>Oprávnění pro zpětný zápis skupin 
Pokud chcete nastavit oprávnění pro účet konektoru služby AD DS při použití zpětný zápis skupin, spusťte: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Nebo; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Tato rutina se nastavit následující oprávnění: 

|Type |Název |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služby AD DS |Obecný čtení/zápisu |Všechny atributy skupiny typ objektu a podobjektů| 
|Povolit |Účet konektoru služby AD DS |Vytvoření nebo odstranění podřízený objekt |Všechny atributy skupiny typ objektu a podobjektů| 
|Povolit |Účet konektoru služby AD DS |Odstranit, odstranění stromu objektů|Všechny atributy skupiny typ objektu a podobjektů|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Oprávnění pro hybridní nasazení systému Exchange 
Pokud chcete nastavit oprávnění pro účet konektoru služby AD DS při použití hybridní nasazení systému Exchange, spusťte: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Nebo; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Tato rutina se nastavit následující oprávnění:  
 

|Type |Název |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služby AD DS |Čtení a zápis všech vlastností |Následné uživatelské objekty| 
|Povolit |Účet konektoru služby AD DS |Čtení a zápis všech vlastností |Odvozené objekty InetOrgPerson| 
|Povolit |Účet konektoru služby AD DS |Čtení a zápis všech vlastností |Objekty potomkem skupiny| 
|Povolit |Účet konektoru služby AD DS |Čtení a zápis všech vlastností |Odvozené objekty kontaktu| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Oprávnění pro veřejné složky pošty Exchange (Preview) 
Pokud chcete nastavit oprávnění pro účet konektoru služby AD DS při použití funkce veřejné složky pošty Exchange, spusťte: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Nebo; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Tato rutina se nastavit následující oprávnění: 

|Type |Název |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |Účet konektoru služby AD DS |Číst všechny vlastnosti |Odvozené objekty PublicFolder| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Omezit oprávnění v konektoru služby AD DS účtu 
Tento skript Powershellu se zvýšit oprávnění pro účet AD Connector zadat jako parametr. Upevňování oprávnění zahrnuje následující kroky: 

- Zakázat dědičnosti na zadaný objekt 
- Odeberte všechny položky řízení přístupu pro daný objekt, s výjimkou ACE konkrétní na sebe sama tak, aby zachovat beze změny výchozích oprávnění, pokud jde o sama na sebe. 
 
 Parametr - ADConnectorAccountDN je účet AD, jehož oprávnění musí být zvýšit. Toto je obvykle MSOL_nnnnnnnnnnnn účtu domény, který je nakonfigurovaný v konektoru služby AD DS (určení účtu AD DS konektoru najdete v článku). -Credential parametr je nutné zadat účet správce, který má dostatečná oprávnění k omezení oprávnění služby Active Directory AD cílového objektu. To je obvykle Enterprise nebo správce domény.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Příklad: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Tato rutina se nastavit následující oprávnění: 

|Type |Název |Access |Platí pro|
|-----|-----|-----|-----| 
|Povolit |SYSTÉM |Úplné řízení |Tento objekt 
|Povolit |Enterprise Admins |Úplné řízení |Tento objekt 
|Povolit |Domain Admins |Úplné řízení |Tento objekt 
|Povolit |Správci |Úplné řízení |Tento objekt 
|Povolit |Enterprise Domain Controllers |Seznam obsahu |Tento objekt 
|Povolit |Enterprise Domain Controllers |Číst všechny vlastnosti |Tento objekt 
|Povolit |Enterprise Domain Controllers |Oprávnění ke čtení |Tento objekt 
|Povolit |Ověření uživatelé |Seznam obsahu |Tento objekt 
|Povolit |Ověření uživatelé |Číst všechny vlastnosti |Tento objekt 
|Povolit |Ověření uživatelé |Oprávnění ke čtení |Tento objekt 

## <a name="next-steps"></a>Další kroky
- [Azure AD Connect: Účty a oprávnění](reference-connect-accounts-permissions.md)
- [Expresní instalace](how-to-connect-install-express.md)
- [Vlastní instalace](how-to-connect-install-custom.md)
- [Odkaz na ADSyncConfig](reference-connect-adsyncconfig.md)

