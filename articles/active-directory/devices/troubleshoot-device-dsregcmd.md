---
title: Poradce při potížích s použitím příkazu dsregcmd – Azure Active Directory
description: Použití výstupu z dsregcmd k pochopení stavu zařízení ve službě Azure AD
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
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128764"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Řešení potíží se zařízeními pomocí příkazu dsregcmd

Nástroj dsregcmd /status musí být spuštěn jako uživatelský účet domény.

## <a name="device-state"></a>Stav zařízení

V této části jsou uvedeny parametry stavu připojení zařízení. V následující tabulce jsou uvedena kritéria pro zařízení, které má být v různých stavech spojení.

| AzureAdJoined | EnterpriseJoined | K doméně připojeno | Stav zařízení |
| ---   | ---   | ---   | ---   |
| ANO | NO | NO | Připojeno pro Azure AD |
| NO | NO | ANO | Připojená doména |
| ANO | NO | ANO | Hybridní a dlativní aplikace |
| NO | ANO | ANO | Místní drs spojené |

> [!NOTE]
> Stav Připojení k pracovnímu prostředí (registrovaný v Azure AD) se zobrazí v části Stav uživatele.

- **AzureAdJoined:** - Nastavte na "ANO", pokud je zařízení připojeno k Azure AD. "NE" jinak.
- **EnterpriseJoined:** - Nastavte na "ANO", pokud je zařízení připojeno k místnímu DRS. Zařízení nemůže být enterprisejoined a AzureAdJoined.
- **DomainJoinedJoined:** - Nastavte na "ANO", pokud je zařízení připojeno k doméně (AD).
- **DomainName:** - Nastavte název domény, pokud je zařízení připojeno k doméně.

### <a name="sample-device-state-output"></a>Ukázkový výstup stavu zařízení

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

Zobrazí se jenom v případě, že je zařízení Azure AD připojen nebo hybridní Azure AD připojil (není Azure AD registrované). V této části jsou uvedeny údaje identifikující zařízení uložené v cloudu.

- **DeviceId:** - jedinečné ID zařízení v tenantovi Azure AD
- **Otisk palce:** - Otisk palce certifikátu zařízení 
- **DeviceCertificateValidity:** - Platnost certifikátu zařízení
- **KeyContainerId:** - ContainerId soukromého klíče zařízení přidruženého k certifikátu zařízení
- **KeyProvider:** - KeyProvider (Hardware/Software) slouží k uložení soukromého klíče zařízení.
- **TpmProtected:** - "ANO", pokud je soukromý klíč zařízení uložen v hardwarovém čipu TPM.

### <a name="sample-device-details-output"></a>Ukázkový výstup podrobností o zařízení

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

Zobrazí se jenom v případě, že je zařízení Azure AD připojen nebo hybridní Azure AD připojil (není Azure AD registrované). V této části jsou uvedeny běžné podrobnosti o tenantovi, když se zařízení připojí k Azure AD.

> [!NOTE]
> Pokud jsou adresy URL MDM v této části prázdné, znamená to, že MDM nebyl nakonfigurován nebo aktuální uživatel není v rozsahu registrace MDM. Zkontrolujte nastavení mobility ve službě Azure AD a zkontrolujte konfiguraci MDM.

> [!NOTE]
> I když vidíte adresy URL MDM to neznamená, že zařízení je spravováno MDM. Informace se zobrazí, pokud má klient konfiguraci MDM pro automatický zápis i v případě, že samotné zařízení není spravováno. 

### <a name="sample-tenant-details-output"></a>Ukázkový výstup podrobností o tenantovi

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

V této části je uveden stav různých atributů uživatele aktuálně přihlášeného k zařízení.

> [!NOTE]
> Příkaz musí být spuštěn v kontextu uživatele, aby bylo možné načíst platný stav.

- **NgcSet:** - Pokud je pro aktuálního přihlášeného uživatele nastaven klíč Windows Hello, nastavte na "ANO".
- **NgcKeyId:** - ID klávesy Windows Hello, pokud je nastavena pro aktuálního přihlášeného uživatele.
- **CanReset:** - Označuje, pokud může být klávesa Windows Hello resetována uživatelem. 
- **Možné hodnoty:** - DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive, nebo Unknown if error. 
- **WorkplaceJoined:** - Nastavte na "ANO", pokud registrované účty Azure AD byly přidány do zařízení v aktuálním kontextu NTUSER.
- **WamDefaultSet:** - Pokud je pro přihlášeného uživatele vytvořen výchozí webový účet WAM, nastavte na hodnotu "ANO". Toto pole může zobrazit chybu, pokud je dsreg /status spuštěn z příkazového řádku se zvýšenými oprávněními. 
- **WamDefaultAuthority:** - Nastavte na "organizace" pro Azure AD.
- **WamDefaultId:** -https://login.microsoft.comVždy " pro Azure AD.
- **WamDefaultGUID:** - IDENTIFIKÁTOR GUID poskytovatele WAM (Azure AD/Microsoft account) pro výchozí wam webúčet. 

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

## <a name="sso-state"></a>Stav stesa nebo sondy

Tato část může být ignorována pro zařízení registrovaná azure ad.

> [!NOTE]
> Příkaz musí být spuštěn v kontextu uživatele, aby bylo možné načíst platný stav pro tohoto uživatele.

- **AzureAdPrt:** - Nastavte na "ANO", pokud PRT je k dispozici na zařízení pro přihlášeného uživatele.
- **AzureAdPrtUpdateTime:** - Nastavte čas v utc, kdy byla naposledy aktualizována PRT.
- **AzureAdPrtExpiryTime:** - Nastavte čas v utc, kdy prt vyprší, pokud není obnovena.
- **AzureAdPrtAuthority:** – adresa URL autority Azure AD
- **EnterprisePrt:** - Pokud má zařízení PRT z místního serveru ADFS, nastavte na "ANO". Pro hybridní zařízení Azure AD připojenzařízení zařízení může mít PRT z Azure AD a místní AD současně. Místní připojená zařízení budou mít jenom enterprise PRT.
- **EnterprisePrtUpdateTime:** - Nastavte čas v utc, kdy byla naposledy aktualizována enterprise PRT.
- **EnterprisePrtExpiryTime:** - Nastavte čas v UTC, kdy prt vyprší, pokud není obnovena.
- **EnterprisePrtAuthority:** - Adresa URL autority ADFS

### <a name="sample-sso-state-output"></a>Ukázkový výstup stavu spřimisování sdílčího sdílčího

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

### <a name="pre-join-diagnostics"></a>Diagnostika před připojením

Tato část se zobrazí pouze v případě, že zařízení je připojen k doméně a nelze hybridní připojení Azure AD.

Tato část provádí různé testy, které pomáhají diagnostikovat selhání spojení. Tato část také obsahuje podrobnosti o předchozí (?). Tyto informace zahrnují chybovou fázi, kód chyby, ID požadavku serveru, stav odpovědi serveru http, chybovou zprávu s odpovědí serveru.

- **Kontext uživatele:** - kontext, ve kterém jsou spuštěny diagnostiky. Možné hodnoty: SYSTEM, UN-ELEVATED User, ELEVATED User. 

   > [!NOTE]
   > Vzhledem k tomu, že skutečné spojení se provádí v kontextu SYSTEM, spuštění diagnostiky v kontextu SYSTEM je nejblíže k scénáři skutečné spojení. Chcete-li spustit diagnostiku v kontextu SYSTÉMU, musí být příkaz dsregcmd /status spuštěn z příkazového řádku se zvýšenými oprávněními.

- **Čas klienta:** - Systémový čas v UTC.
- **Test připojení služby AD:** - Test provede test připojení k řadiči domény. Chyba v tomto testu pravděpodobně způsobí chyby spojení ve fázi předběžné kontroly.
- **Test konfigurace služby AD:** - Test přečte a ověří, zda je objekt SCP správně nakonfigurován v místní doménové struktuře služby AD. Chyby v tomto testu by pravděpodobně vést k join chyby ve fázi zjišťování s kódem chyby 0x801c001d.
- **Test zjišťování DRS:** - Test získá koncové body DRS z koncového bodu metadat zjišťování a provede požadavek na sféru uživatele. Chyby v tomto testu by pravděpodobně vést k join chyby ve fázi zjišťování.
- **Test připojení DRS:** - Test provádí základní test připojení ke koncovému bodu DRS.
- **Test pořízení tokenu:** – test se pokusí získat ověřovací token Azure AD, pokud je klient uživatele federovaný. Chyby v tomto testu by pravděpodobně vést k join chyby ve fázi auth. Pokud se nezdaří auth synchronizační spojení se pokusí jako záložní, pokud záložní je explicitně zakázána s níže nastavení klíče registru.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Záložní sync-join:** - Nastavte na "Povoleno", pokud výše uvedený klíč registru, aby se zabránilo záložní synchronizovat spojení s selhání matný, není k dispozici. Tato možnost je dostupná v systému Windows 10 1803 a novějším.
- **Předchozí registrace:** - Čas předchozího pokusu o připojení došlo. Jsou zaznamenány pouze neúspěšné pokusy o připojení.
- **Chyba fáze:** - fáze spojení, ve kterém byla přerušena. Možné hodnoty jsou pre-check, discover, auth, join.
- **Chyba klientaKód:** - Vrácen kód chyby klienta (HRESULT).
- **Chybový kód serveru:** - Kód chyby serveru, pokud byl požadavek odeslán na server a server odpověděl zpět s kódem chyby. 
- **Zpráva serveru:** - Zpráva serveru vrácena spolu s kódem chyby.
- **Stav https:** - Stav http vrácený serverem.
- **ID požadavku:** - Id požadavku klienta odeslané na server. Užitečné korelovat s protokoly na straně serveru.

### <a name="sample-pre-join-diagnostics-output"></a>Ukázkový výstup diagnostiky před připojením

Následující příklad ukazuje selhání diagnostického testu s chybou zjišťování.

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

Následující příklad ukazuje, že diagnostické testy jsou předávání, ale pokus o registraci se nezdařil o chybu adresáře, která se očekává pro synchronizaci spojení. Po dokončení úlohy synchronizace Azure AD Connect se zařízení bude moct připojit.

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

Tato část zobrazuje výstup kontrol y příčetnosti prováděných na zařízení připojovat se ke cloudu.

- **AadRecoveryEnabled:** - Pokud "ANO", klíče uložené v zařízení nejsou použitelné a zařízení je označeno pro obnovení. Další přihlášení spustí tok obnovení a znovu zaregistruje zařízení.
- **KeySignTest:** - Pokud "PROŠEL" klíče zařízení jsou v dobrém zdravotním stavu. Pokud KeySignTest selže, zařízení bude obvykle označenpro obnovení. Další přihlášení spustí tok obnovení a znovu zaregistruje zařízení. Pro hybridní zařízení azure ad připojena obnovení je tichý. Zatímco Azure AD připojen nebo Azure AD registrované, zařízení vyzve k ověření uživatele obnovit a znovu zaregistrovat zařízení v případě potřeby. **KeySignTest vyžaduje zvýšená oprávnění.**

#### <a name="sample-post-join-diagnostics-output"></a>Ukázkový výstup diagnostiky po připojení

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Kontrola předpokladů ngc

Tato část provádí perquisite kontroly pro zřizování Windows Hello pro firmy (WHFB). 

> [!NOTE]
> Pokud uživatel již úspěšně nakonfiguroval WHFB, nemusí se zobrazit podrobnosti o předběžné kontrole společnosti NGC v dsregcmd /status.

- **IsDeviceJoined:** - Nastavte na "ANO", pokud je zařízení připojeno k Azure AD.
- **IsUserAzureAD:** - Nastavte na "ANO", pokud je přihlášený uživatel přítomen ve službě Azure AD .
- **PolicyEnabled:** - Nastavte na "ANO", pokud je v zařízení povolena zásada WHFB.
- **PostLogonEnabled:** - Nastavte na "ANO", pokud je zápis WHFB spuštěn nativně platformou. Pokud je nastavena na "NE", znamená to, že registrace Windows Hello pro firmy je spuštěna vlastním mechanismem
- **DeviceEligible:** - Nastavte na "ANO", pokud zařízení splňuje hardwarový požadavek pro registraci s WHFB.
- **SessionIsNotRemote:** - Nastavte na "ANO", pokud je aktuální uživatel přihlášen přímo k zařízení a ne vzdáleně.
- **CertEnrollment:** - Specifické pro nasazení certifikátu WHFB, označující autoritu pro zápis certifikátu pro WHFB. Nastavte na "oprávnění pro zápis", pokud je zdrojem zásad WHFB zásady skupiny, "správa mobilních zařízení", pokud je zdrojem MDM. "žádný" jinak
- **AdfsRefreshToken:** - Specifické pro nasazení důvěryhodnosti certifikátu WHFB. K dispozici pouze v případě, že CertEnrollment je "oprávnění pro zápis". Označuje, zda má zařízení pro uživatele podnikový PRT.
- **AdfsRaIsReady:** - Specifické pro nasazení certifikátu WHFB.  K dispozici pouze v případě, že CertEnrollment je "oprávnění pro zápis". Nastavte na "ANO", pokud služba ADFS v metadatech zjišťování uvedla, že podporuje WHFB *a* zda je k dispozici šablona přihlašovacího certifikátu.
- **LogonCertTemplateReady:** - Specifické pro nasazení důvěryhodnosti certifikátu WHFB. K dispozici pouze v případě, že CertEnrollment je "oprávnění pro zápis". Pokud je stav přihlašovacího certifikátu platný a pomáhá řešit potíže s protokolem ADFS RA, nastavte na "ANO".
- **PreReqResult:** - Poskytuje výsledek všech WHFB předpoklad hodnocení. Nastavte na "Bude zřízení", pokud WHFB zápis by být spuštěn jako post-přihlášení úkol, když uživatel přihlásí příště.

### <a name="sample-ngc-prerequisite-check-output"></a>Ukázkový výstup předpokladu NGC

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

Dotazy najdete v [nejčastějších dotazech ke správě zařízení.](faq.md)
