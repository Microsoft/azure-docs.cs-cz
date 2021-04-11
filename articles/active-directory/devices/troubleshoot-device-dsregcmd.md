---
title: Řešení potíží pomocí příkazu dsregcmd-Azure Active Directory
description: Pochopení stavu zařízení ve službě Azure AD pomocí výstupu z dsregcmd
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ced48308673c6688def7b949fc225eeb5b2ced4
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551726"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Řešení potíží se zařízeními pomocí příkazu dsregcmd

Nástroj dsregcmd/status musí být spuštěn jako uživatelský účet domény.

## <a name="device-state"></a>Stav zařízení

V této části jsou uvedené parametry stavu připojení zařízení. V následující tabulce jsou uvedená kritéria pro zařízení v různých stavech spojení.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Stav zařízení |
| ---   | ---   | ---   | ---   |
| ANO | NO | NO | Připojeno k Azure AD |
| NO | NO | ANO | Připojeno k doméně |
| ANO | NO | ANO | Připojeno k hybridní službě AD |
| NO | ANO | ANO | Připojené k místnímu DRS |

> [!NOTE]
> V části "stav uživatele" se zobrazí stav Workplace Join (registrovaný Azure AD).

- **AzureAdJoined:** -Pokud je zařízení připojené ke službě Azure AD, nastavte na hodnotu Ano. "Ne" jinak.
- **EnterpriseJoined:** -Pokud je zařízení připojené k místnímu DRS, nastavte hodnotu Ano. Zařízení nemůže být současně EnterpriseJoined i AzureAdJoined.
- **DomainJoined:** -Pokud je zařízení připojené k doméně (AD), nastavte na hodnotu Ano.
- **DomainName:** – nastavte název domény, pokud je zařízení připojené k doméně.

### <a name="sample-device-state-output"></a>Ukázka výstupu stavu zařízení

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Podrobnosti o zařízení

Zobrazuje se jenom v případě, že je zařízení připojené k Azure AD nebo je připojené k hybridní službě Azure AD (není zaregistrované Azure AD). V této části jsou uvedené informace o identifikaci zařízení, které jsou uložené v cloudu.

- **DeviceID:** – jedinečné ID zařízení v TENANTOVI Azure AD
- **Kryptografický otisk:** – kryptografický otisk certifikátu zařízení 
- **DeviceCertificateValidity:** -platnost certifikátu zařízení
- **KeyContainerId:** -ContainerId privátní klíč zařízení přidružený k certifikátu zařízení
- Klíč **:** -poskytuje (hardware/software), který se používá k uložení privátního klíče zařízení.
- **TpmProtected:** -"Ano", pokud je privátní klíč zařízení uložený v HARDWAROVÉM čipu TPM.

### <a name="sample-device-details-output"></a>Ukázka výstupu podrobností o zařízení

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Podrobnosti o tenantovi

Zobrazuje se jenom v případě, že je zařízení připojené k Azure AD nebo je připojené k hybridní službě Azure AD (není zaregistrované Azure AD). Tato část obsahuje informace o běžných klientech, když je zařízení připojené ke službě Azure AD.

> [!NOTE]
> Pokud jsou adresy URL MDM v této části prázdné, znamená to, že MDM buď není nakonfigurovaný, nebo aktuální uživatel není v oboru registrace MDM. Zkontrolujte nastavení mobility ve službě Azure AD a zkontrolujte konfiguraci MDM.

> [!NOTE]
> I když se zobrazují adresy URL MDM, neznamená to, že zařízení spravuje MDM. Tyto informace se zobrazí, pokud má tenant konfiguraci MDM pro automatický zápis i v případě, že samotné zařízení není spravované. 

### <a name="sample-tenant-details-output"></a>Výstup podrobností o ukázkovém tenantovi

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Stav uživatele

V této části je uveden seznam stavů různých atributů pro uživatele, který je aktuálně přihlášený k zařízení.

> [!NOTE]
> Příkaz se musí spustit v uživatelském kontextu, aby se načetl platný stav.

- **NgcSet:** -nastavte na hodnotu Ano, pokud je pro aktuálně přihlášený uživatel nastaven klíč Windows Hello.
- **NgcKeyId:** -ID klíče Windows Hello, pokud je nastaven pro aktuálně přihlášeného uživatele.
- **CanReset:** -označuje, jestli uživatel může resetovat klíč Windows Hello. 
- **Možné hodnoty:** -DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive nebo Unknown, pokud se jedná o chybu. 
- **WorkplaceJoined:** -nastavte na hodnotu Ano, pokud se do zařízení v aktuálním kontextu Ntuser přidaly účty registrované službou Azure AD.
- **WamDefaultSet:** -nastavte na hodnotu Ano, pokud je pro přihlášeného uživatele vytvořen výchozí účet WAM. V tomto poli se může zobrazit chyba, pokud se dsreg/status spouští z příkazového řádku se zvýšenými oprávněními. 
- **WamDefaultAuthority:** -pro Azure AD nastavte na "organizace".
- **WamDefaultId:** – Always " https://login.microsoft.com " pro Azure AD.
- **WamDefaultGUID:** – GUID poskytovatele WAM (Azure AD/účet Microsoft) pro výchozí webaccount WAM. 

### <a name="sample-user-state-output"></a>Ukázkový výstup stavu uživatele

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Stav jednotného přihlašování

Tato část se dá pro zařízení registrovaná v Azure AD ignorovat.

> [!NOTE]
> Příkaz se musí spustit v uživatelském kontextu, aby se načetl platný stav pro tohoto uživatele.

- **AzureAdPrt:** -Pokud je v zařízení pro přihlášeného uživatele k DISpozici PRT, nastavte na hodnotu Ano.
- **AzureAdPrtUpdateTime:** -nastaví se na čas ve standardu UTC, kdy se poslední aktualizace PRT.
- **AzureAdPrtExpiryTime:** -nastavte na čas ve standardu UTC, kdy platnost PRT vyprší, pokud není obnovena.
- **AzureAdPrtAuthority:** – adresa URL autority Azure AD
- **EnterprisePrt:** -Pokud má zařízení PRT z místní služby AD FS, nastavte na hodnotu Ano. Zařízení připojená k hybridní službě Azure AD by mohla mít PRT z Azure AD i z místní služby AD současně. Místní připojená zařízení budou mít jenom PRT Enterprise.
- **EnterprisePrtUpdateTime:** -nastavte čas ve standardu UTC, kdy se naposledy aktualizoval podniková PRT.
- **EnterprisePrtExpiryTime:** -nastavte na čas ve standardu UTC, kdy platnost PRT vyprší, pokud není obnovena.
- **EnterprisePrtAuthority:** – adresa URL pro autoritu ADFS

### <a name="sample-sso-state-output"></a>Ukázka výstupu stavu jednotného přihlašování

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Diagnostická data

### <a name="pre-join-diagnostics"></a>Diagnostika před spojením

Tato část se zobrazí jenom v případě, že je zařízení připojené k doméně a nemůže se připojit k hybridní službě Azure AD.

Tato část provádí různé testy, které vám pomůžou diagnostikovat selhání připojení. Tato část obsahuje také podrobnosti o předchozí (?). Tyto informace zahrnují chybovou fázi, kód chyby, ID žádosti serveru, stav HTTP odpovědi serveru, chybová zpráva odpovědi serveru.

- **Kontext uživatele:** – kontext, ve kterém se Diagnostika spouští. Možné hodnoty: SYSTEM, unzvýšeného uživatele, ZVÝŠENý uživatel. 

   > [!NOTE]
   > Vzhledem k tomu, že se skutečné spojení provádí v kontextu systému, je spuštění diagnostiky v kontextu systému nejblíže vašemu skutečnému scénáři spojení. Chcete-li spustit diagnostiku v kontextu systému, je třeba spustit příkaz dsregcmd/status z příkazového řádku se zvýšenými oprávněními.

- **Čas klienta:** – systémový čas ve standardu UTC.
- **Test připojení AD:** -test provede test připojení k řadiči domény. Chyba v tomto testu bude nejspíš způsobit chyby spojení v předběžné kontrolní fázi.
- **Test konfigurace služby AD:** -test načte a ověří, jestli je objekt SCP správně nakonfigurovaný v místní doménové struktuře AD. Chyby v tomto testu by mohly vést k chybám spojení ve fázi zjišťování s kódem chyby 0x801c001d.
- **Test zjišťování DRS:** -test Získá koncové body DRS z koncového bodu metadat zjišťování a provede požadavek na sféru uživatele. Chyby v tomto testu by mohly vést k chybám spojení ve fázi zjišťování.
- **Test připojení DRS:** -test provede základní test připojení ke koncovému bodu DRS.
- **Test získání tokenu:** -test se pokusí získat ověřovací token Azure AD, pokud je tenant uživatele federované. Chyby v tomto testu by mohly vést k chybám spojení ve fázi ověřování. Pokud se v případě, že není výslovně zakázaná záloha pomocí níže uvedených nastavení klíče registru, pokusí se pokusit se připojit k synchronizaci synchronizace se nezdaří.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Záloha na synchronizaci – připojení:** -nastavit na povoleno, pokud se výše uvedený klíč registru nestane, aby se záloha synchronizoval s chybami ověřování, není k dispozici. Tato možnost je k dispozici v systému Windows 10 1803 a novějším.
- **Předchozí registrace:** čas, kdy došlo k předchozímu pokusu o spojení. Protokolují se jenom neúspěšné pokusy o připojení.
- **Chybová fáze:** – fáze spojení, ve které byla přerušena. Možné hodnoty jsou předběžné kontroly, zjišťování, ověřování, spojení.
- **Klient ErrorCode:** -vrácený kód chyby klienta (HRESULT).
- **Server ErrorCode:** -kód chyby serveru, pokud byl požadavek odeslán na server a Server vrátil zpět chybový kód. 
- **Zpráva serveru:** -zpráva serveru vrácená spolu s kódem chyby.
- **Stav protokolu https:** -stav HTTP vrácený serverem.
- **ID žádosti:** – požadavek klienta byl odeslán na server. To je užitečné pro korelaci s protokoly na straně serveru.

### <a name="sample-pre-join-diagnostics-output"></a>Ukázka předběžného připojení výstup diagnostiky

Následující příklad ukazuje selhání testu diagnostiky s chybou zjišťování.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

Následující příklad ukazuje testy diagnostiky, ale pokus o registraci se nezdařil s chybou adresáře, která se očekává pro připojení synchronizace. Jakmile se úloha synchronizace Azure AD Connect dokončí, zařízení se bude moci připojit.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnostika po připojení

Tato část zobrazuje výstup kontrol správnosti provedených v zařízení připojeném ke cloudu.

- **AadRecoveryEnabled:** -Pokud ano, klíče uložené v zařízení nejsou použitelné a zařízení je označené pro obnovení. Při příštím přihlášení se spustí tok obnovení a zařízení se znovu zaregistruje.
- **KeySignTest:** -if "Pass" klíče zařízení jsou v dobrém stavu. Pokud KeySignTest selže, zařízení se obvykle označí pro obnovení. Při příštím přihlášení se spustí tok obnovení a zařízení se znovu zaregistruje. Pro zařízení připojená k hybridní službě Azure AD je obnovení tiché. Když jste připojeni k Azure AD nebo zaregistrovali Azure AD, zařízení zobrazí výzvu k ověření uživatele, aby v případě potřeby obnovila a znovu zaregistrovala zařízení. **KeySignTest vyžaduje zvýšená oprávnění.**

#### <a name="sample-post-join-diagnostics-output"></a>Ukázkový výstup diagnostiky po připojení

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Kontrola požadavků služby NGC

Tato část provádí kontroly požadovaných součástí pro zřizování Windows Hello pro firmy (WHFB). 

> [!NOTE]
> V dsregcmd/status se nemusí zobrazit podrobnosti o kontrole požadavků služby NGC, pokud už uživatel úspěšně nakonfiguroval WHFB.

- **IsDeviceJoined:** -Pokud je zařízení připojené ke službě Azure AD, nastavte na hodnotu Ano.
- **IsUserAzureAD:** -Pokud se přihlášený uživatel nachází v Azure AD, nastavte na hodnotu Ano.
- **PolicyEnabled:** -Pokud je na zařízení povolená zásada WHFB, nastavte na hodnotu Ano.
- **PostLogonEnabled:** -nastavte na hodnotu Ano, pokud se registrace WHFB spustí nativně platformou. Pokud je nastavená na ne, znamená to, že registrace Windows Hello pro firmy se aktivuje vlastním mechanismem.
- **DeviceEligible:** -nastavte na hodnotu Ano, pokud zařízení splňuje požadavky na hardware pro registraci v WHFB.
- **SessionIsNotRemote:** -nastavte na hodnotu Ano, pokud je aktuální uživatel přihlášen přímo k zařízení, nikoli vzdáleně.
- **CertEnrollment:** specifické pro WHFB nasazení důvěryhodných certifikátů, které označuje autoritu pro zápis certifikátu pro WHFB. Nastavte na "autorita pro registraci", pokud je zdroj zásad WHFB Zásady skupiny, "Správa mobilních zařízení", pokud je zdrojem MDM. None (žádné) jinak
- **AdfsRefreshToken:** specifické pro WHFB nasazení důvěryhodných certifikátů. K dispozici pouze v případě, že CertEnrollment je "autorita pro registraci". Určuje, jestli má zařízení PRT Enterprise pro uživatele.
- **AdfsRaIsReady:** specifické pro WHFB nasazení důvěryhodných certifikátů.  K dispozici pouze v případě, že CertEnrollment je "autorita pro registraci". Nastavte na hodnotu Ano, pokud se služba AD v metadatech zjišťování označila jako WHFB *a* je dostupná šablona přihlašovacího certifikátu.
- **LogonCertTemplateReady:** specifické pro WHFB nasazení důvěryhodných certifikátů. K dispozici pouze v případě, že CertEnrollment je "autorita pro registraci". Nastavte na Ano, pokud je šablona přihlašovacího certifikátu platná a pomáhá řešit problémy s RA v AD FS.
- **PreReqResult:** – poskytuje výsledek pro všechna vyhodnocení požadavků WHFB. Pokud se uživatel poprvé přihlásí, bude se tato možnost zřídit, pokud se registrace WHFB spustí jako úloha po přihlášení.

### <a name="sample-ngc-prerequisite-check-output"></a>Ukázka výstupu kontroly požadavků služby NGC

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Další kroky

Otázky najdete v tématu [Nejčastější dotazy ke správě zařízení](faq.yml) .
