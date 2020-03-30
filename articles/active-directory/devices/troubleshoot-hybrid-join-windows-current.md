---
title: Poradce při potížích s hybridními zařízeními azure active directory
description: Řešení potíží s hybridní službou Azure Active Directory se připojilo k zařízením s Windows 10 a Windows Server 2016.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331772"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Poradce při potížích s hybridními zařízeními azure active directory 

Obsah tohoto článku je použitelný pro zařízení se systémem Windows 10 nebo Windows Server 2016.

Ostatní klienti s Windows najdete v článku [Poradce při potížích s hybridním zařízením Azure Active Directory, která se připojila k zařízením nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md).

Tento článek předpokládá, že jste [nakonfigurovali hybridní zařízení Azure Active Directory připojená](hybrid-azuread-join-plan.md) k podpoře následujících scénářů:

- Podmíněný přístup založený na zařízení
- [Podnikový roaming nastavení](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello pro firmy](../active-directory-azureadjoin-passport-deployment.md)

Tento dokument obsahuje pokyny pro řešení potíží k řešení potenciálních problémů. 

Pro Windows 10 a Windows Server 2016 hybridní připojení Azure Active Directory podporuje aktualizaci Windows 10 z listopadu 2015 a vyšší.

## <a name="troubleshoot-join-failures"></a>Poradce při potížích s připojením

### <a name="step-1-retrieve-the-join-status"></a>Krok 1: Načtení stavu spojení 

**Chcete-li načíst stav spojení:**

1. Otevření příkazového řádku jako správce
2. Zadejte `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Krok 2: Vyhodnocení stavu spojení 

Zkontrolujte následující pole a ujistěte se, že mají očekávané hodnoty:

#### <a name="domainjoined--yes"></a>DoménaSpojená : ANO  

Toto pole označuje, zda je zařízení připojeno k místní službě Active Directory či nikoli. Pokud je hodnota **NE**, zařízení nelze provést hybridní připojení Azure AD.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NE  

Toto pole označuje, jestli je zařízení registrované ve službě Azure AD jako osobní zařízení (označené jako *připojení k pracovnímu prostředí).* Tato hodnota by měla být **NE** pro počítač spojený s doménou, který je také hybridní Azure AD připojen. Pokud je hodnota **ANO**, pracovní nebo školní účet byl přidán před dokončením hybridní připojení Azure AD. V tomto případě je účet ignorován při použití verze výroční aktualizace systému Windows 10 (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined : ANO  

Toto pole označuje, zda je zařízení připojeno. Hodnota bude **ANO,** pokud je zařízení zařízení zařízení připojil Azure AD zařízení nebo hybridní zařízení připojil Azure AD.
Pokud je hodnota **NE**, připojení k Azure AD ještě není dokončena. 

Pokračujte dalšími kroky pro další řešení potíží.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Krok 3: Najděte fázi, ve které se připojení nezdařilo, a kód chyby

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 a vyšší

Vyhledejte podsekci "Předchozí registrace" v části Diagnostická data ve výstupu stavu spojení. Tato část se zobrazí pouze v případě, že zařízení je připojen k doméně a nelze hybridní připojení Azure AD.
Pole "Fáze chyby" označuje fázi selhání spojení, zatímco kód client errorcode označuje kód chyby operace Spojení.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Starší verze Windows 10

Pomocí protokolů Prohlížeče událostí vyhledejte kód fáze a chyby pro selhání spojení.

1. Otevřete protokoly událostí **Registrace uživatelského zařízení** v prohlížeči událostí. Nachází se v části **Přihlásit** > aplikace a služby**Registrace** > **uživatelského zařízení systému** **Microsoft Windows** > 
2. Vyhledejte události s následujícími id události 304, 305, 307.

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Krok 4: Zkontrolujte možné příčiny a řešení z níže uvedených seznamů

#### <a name="pre-check-phase"></a>Fáze předběžné kontroly

Možné důvody selhání:

- Zařízení nemá žádný zorný dohled na řadičdomény.
   - Zařízení musí být v interní síti organizace nebo v síti VPN se síťovým zorným polem k místnímu řadiči domény služby Active Directory (AD).

#### <a name="discover-phase"></a>Fáze objevování

Možné důvody selhání:

- Objekt SCP (Service Connection Point) nesprávně nakonfiguroval/nemohl číst objekt SCP z řadiče domény.
   - Platný objekt SCP je povinný v doménové struktuře služby AD, do které zařízení patří, který odkazuje na ověřený název domény ve službě Azure AD.
   - Podrobnosti naleznete v části [Konfigurace spojovacího bodu služby](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Nepodařilo se připojit a načíst metadata zjišťování z koncového bodu zjišťování.
   - Zařízení by mělo mít `https://enterpriseregistration.windows.net`přístup k přístupu v kontextu SYSTEM, ke zjištění koncových bodů registrace a autorizace. 
   - Pokud místní prostředí vyžaduje odchozí proxy server, musí správce IT zajistit, aby účet počítače zařízení byl schopen zjistit a tiše ověřit odchozí proxy server.
- Nepodařilo se připojit ke koncovému bodu sféry uživatele a provést zjišťování sféry. (Windows 10 verze 1809 a novější)
   - Zařízení by mělo mít `https://login.microsoftonline.com`přístup k aplikaci SYSTEM, aby provedlo zjišťování sféry pro ověřenou doménu a určilo typ domény (spravované/federované).
   - Pokud místní prostředí vyžaduje odchozí proxy server, musí správce IT zajistit, aby kontext SYSTEM v zařízení mohl zjistit a tiše ověřit odchozí proxy server.

**Běžné kódy chyb:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Důvod: Nelze číst objekt SCP a získat informace o tenantovi Azure AD.
   - Řešení: Viz část [Konfigurace spojovacího bodu služby](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Důvod: Selhání obecného zjišťování. Nepodařilo se získat metadata zjišťování z DRS.
   - Řešení: Najděte níže uvedenou dílčí chybu a prozkoumat další.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - Důvod: Při provádění zjišťování byl časový režim operace.
   - Řešení: Ujistěte se, že `https://enterpriseregistration.windows.net` je přístupný v kontextu SYSTEM. Další informace naleznete v části [Požadavky na připojení k síti](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Důvod: Selhání zjišťování obecné sféry. Nepodařilo se určit typ domény (spravované/federované) z SLUŽBY STS. 
   - Řešení: Najděte níže uvedenou dílčí chybu a prozkoumat další.

**Běžné kódy dílčích chyb:**

Chcete-li najít kód dílčí chyby pro kód chyby zjišťování, použijte jednu z následujících metod.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 a vyšší

Vyhledejte "TEST zjišťování DRS" v části Diagnostická data ve výstupu stavu spojení. Tato část se zobrazí pouze v případě, že zařízení je připojen k doméně a nelze hybridní připojení Azure AD.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Starší verze Windows 10

Pomocí protokolů Prohlížeče událostí vyhledejte fázový a chybový kód pro selhání spojení.

1. Otevřete protokoly událostí **Registrace uživatelského zařízení** v prohlížeči událostí. Nachází se v části **Přihlásit** > aplikace a služby**Registrace** > **uživatelského zařízení systému** **Microsoft Windows** > 
2. Podívejte se na události s následujícími id událostí 201

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Chyby sítě

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Důvod: Nelze navázat spojení se serverem.
   - Řešení: Zajistěte připojení k síti k požadovaným prostředkům společnosti Microsoft. Další informace naleznete v tématu [Požadavky na připojení k síti](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Důvod: Obecný časový čas sítě.
   - Řešení: Zajistěte připojení k síti k požadovaným prostředkům společnosti Microsoft. Další informace naleznete v tématu [Požadavky na připojení k síti](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Důvod: Síťový zásobník nemohl dekódovat odpověď ze serveru.
   - Řešení: Ujistěte se, že síťový proxy server nenarušuje a nemění odpověď serveru.

###### <a name="http-errors"></a>Chyby protokolu HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Důvod: Objekt SCP nakonfigurovaný s nesprávným ID klienta. Nebo nebyly nalezeny žádné aktivní odběry v tenantovi.
   - Řešení: Ujistěte se, že objekt SCP je nakonfigurovaný se správným ID klienta Azure AD a aktivními předplatnými nebo v tenantovi.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Důvod: HTTP 503 ze serveru DRS.
   - Řešení: Server není momentálně k dispozici. budoucí pokusy o připojení budou pravděpodobně úspěšné, jakmile je server opět online.

###### <a name="other-errors"></a>Další chyby

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Důvod: Odpověď serveru JSON nelze analyzovat. Pravděpodobně kvůli proxy návratu HTTP 200 s HTML auth stránku.
   - Řešení: Pokud místní prostředí vyžaduje odchozí proxy server, musí správce IT zajistit, aby kontext SYSTEM v zařízení mohl zjistit a tiše ověřit odchozí proxy server.

#### <a name="authentication-phase"></a>Fáze ověřování

Platí pouze pro účty federované domény.

Důvody selhání:

- Nelze získat token aplikace Access tiše pro prostředek DRS.
   - Zařízení s Windows 10 získávají ověřovací token z federační služby pomocí integrovaného ověřování systému Windows do aktivního koncového bodu WS-Trust. Podrobnosti: [Konfigurace služby Federation Service](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Běžné kódy chyb:**

Pomocí protokolů Prohlížeče událostí vyhledejte kód chyby, kód dílčí chyby, kód chyby serveru a chybovou zprávu serveru.

1. Otevřete protokoly událostí **Registrace uživatelského zařízení** v prohlížeči událostí. Nachází se v části **Přihlásit** > aplikace a služby**Registrace** > **uživatelského zařízení systému** **Microsoft Windows** > 
2. Vyhledejte události s následující událostíID 305

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Chyby konfigurace

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Důvod: Ověřovací protokol není WS-Trust.
   - Řešení: Místní zprostředkovatel identity musí podporovat ws-trust 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Důvod: Místní federační služba nevrátila odpověď XML.
   - Řešení: Ujistěte se, že koncový bod MEX vrací platný XML. Ujistěte se, že proxy server nenarušuje a vrací odpovědi bez xml.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Důvod: Nelze zjistit koncový bod pro ověřování uživatelského jména a hesla.
   - Řešení: Zkontrolujte nastavení místního zprostředkovatele identity. Ujistěte se, že jsou povoleny koncové body WS-Trust a ujistěte se, že odpověď MEX obsahuje tyto správné koncové body.

##### <a name="network-errors"></a>Chyby sítě

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Důvod: Obecný časový čas sítě.
   - Řešení: Ujistěte se, že `https://login.microsoftonline.com` je přístupný v kontextu SYSTEM. Ujistěte se, že místní zprostředkovatel identity je přístupný v kontextu SYSTEM. Další informace naleznete v tématu [Požadavky na připojení k síti](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Důvod: Připojení s koncovým bodem auth bylo přerušeno.
   - Řešení: Opakujte akci po nějaké akci nebo zkuste se připojit z alternativního stabilního síťového umístění.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Důvod: Certifikát zabezpečení transportní vrstvy (TLS), dříve známý jako SSL (Secure Sockets L), nelze ověřit.
   - Řešení: Zkontrolujte zkosení času klienta. Opakujte akci po nějaké akci nebo zkuste se připojit z alternativního stabilního síťového umístění. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Důvod: Pokus o `https://login.microsoftonline.com` připojení k programu se nezdařil.
   - Řešení: Zkontrolujte `https://login.microsoftonline.com`síťové připojení k programu .

##### <a name="other-errors"></a>Další chyby

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Důvod: Token SAML od místního zprostředkovatele identity nebyl přijat službou Azure AD.
   - Řešení: Zkontrolujte nastavení federačního serveru. Vyhledejte kód chyby serveru v protokolech ověřování.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Důvod: Odpověď serveru WS-Trust ohlásila výjimku poruchy a nepodařilo se mu získat kontrolní výraz
   - Řešení: Zkontrolujte nastavení federačního serveru. Vyhledejte kód chyby serveru v protokolech ověřování.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Důvod: Při pokusu o získání přístupového tokenu z koncového bodu tokenu byla přijata chyba.
   - Řešení: Vyhledejte základní chybu v protokolu ADAL. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Důvod: Obecné selhání ADAL
   - Řešení: Vyhledejte kód dílčí chyby nebo kód chyby serveru z protokolů ověřování.
    
#### <a name="join-phase"></a>Připojit se k fázi

Důvody selhání:

Najděte typ registrace a vyhledejte kód chyby z níže uvedeného seznamu.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 a vyšší

Vyhledejte podsekci "Předchozí registrace" v části Diagnostická data ve výstupu stavu spojení. Tato část se zobrazí pouze v případě, že zařízení je připojen k doméně a nelze hybridní připojení Azure AD.
Pole Typ registrace označuje typ provedeného spojení.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Starší verze Windows 10

Pomocí protokolů Prohlížeče událostí vyhledejte fázový a chybový kód pro selhání spojení.

1. Otevřete protokoly událostí **Registrace uživatelského zařízení** v prohlížeči událostí. Nachází se v části **Přihlásit** > aplikace a služby**Registrace** > **uživatelského zařízení systému** **Microsoft Windows** > 
2. Vyhledejte události s následujícími id událostí 204

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>Chyby PROTOKOLU HTTP vrácené ze serveru DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Důvod: Byla přijata chybová odpověď z protokolu DRS s chybovým kódem ErrorCode: "DirectoryError"
   - Řešení: Z možných důvodů a řešení se podívejte na kód chyby serveru.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Důvod: Byla přijata chybová odpověď z drs s ErrorCode: "AuthenticationError" a ErrorSubCode není "DeviceNotFound". 
   - Řešení: Z možných důvodů a řešení se podívejte na kód chyby serveru.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Důvod: Byla přijata chybová odpověď z protokolu DRS s chybovým kódem ErrorCode: "DirectoryError"
   - Řešení: Z možných důvodů a řešení se podívejte na kód chyby serveru.

##### <a name="tpm-errors"></a>Chyby čipu TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Důvod: Operace čipu TPM se nezdařila nebo byla neplatná.
   - Rozlišení: Pravděpodobně kvůli chybné bitové kopii sysprep. Ujistěte se, že počítač, ze kterého byla vytvořena image sysprep není Azure AD připojen, hybridní Azure AD připojen, nebo Azure AD registrované.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Důvod: Obecná chyba čipu TPM. 
   - Řešení: Zakázání čipu TPM na zařízeních s touto chybou. Windows 10 verze 1809 a vyšší automaticky detekuje selhání čipu TPM a dokončí hybridní připojení Azure AD bez použití čipu TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Důvod: Čip TPM v režimu FIPS není aktuálně podporován.
   - Řešení: Zakázání čipu TPM na zařízeních s touto chybou. Windows 1809 automaticky detekuje selhání čipu TPM a dokončí hybridní připojení Azure AD bez použití čipu TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Důvod: Čip TPM uzamčen.
   - Řešení: Přechodná chyba. Počkejte na dobu cooldownu. Pokus o připojení po určité době by měl být úspěšný. Více informací naleznete v článku [Základy TPM](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Chyby sítě

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Důvod: Obecný časový čas sítě při pokusu o registraci zařízení na DRS
   - Řešení: Zkontrolujte `https://enterpriseregistration.windows.net`připojení k síti .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Důvod: Název serveru nebo adresa nelze přeložit.
   - Řešení: Zkontrolujte `https://enterpriseregistration.windows.net`připojení k síti . Ujistěte se, že rozlišení DNS pro název hostitele je přesné v n /w a na zařízení.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Důvod: Připojení se serverem bylo abnormálně ukončeno.
   - Řešení: Opakujte akci po nějaké akci nebo zkuste se připojit z alternativního stabilního síťového umístění.

##### <a name="federated-join-server-errors"></a>Federované chyby serveru spojení

| Kód chyby serveru | Chybová zpráva serveru | Možné důvody | Řešení |
| --- | --- | --- | --- |
| Chyba adresáře | Váš požadavek je dočasně omezen. Prosím, zkuste po 300 sekundách. | Očekávaná chyba. Možná kvůli několika žádostem o registraci v rychlém sledu. | Opakovat spojení po období přebíjení |

##### <a name="sync-join-server-errors"></a>Synchronizace chyb serveru spojení

| Kód chyby serveru | Chybová zpráva serveru | Možné důvody | Řešení |
| --- | --- | --- | --- |
| Chyba adresáře | AADSTS90002: <UUID> Tenant nebyl nalezen. K této chybě může dojít, pokud neexistují žádné aktivní odběry pro klienta. Obraťte se na správce předplatného. | ID klienta v objektu SCP je nesprávné | Ujistěte se, že objekt SCP je nakonfigurovaný se správným ID klienta Azure AD a aktivními předplatnými a prezentuje se v tenantovi. |
| Chyba adresáře | Objekt zařízení podle daného ID nebyl nalezen. | Očekávaná chyba pro synchronizační spojení. Objekt zařízení se nesynchronizoval ze služby AD do služby Azure AD. | Počkejte na dokončení synchronizace Azure AD Connect a další pokus o připojení po dokončení synchronizace vyřeší problém |
| Chyba ověřování | Ověření sid cílového počítače | Certifikát na zařízení Azure AD neodpovídá certifikátu použitému k podepsání objektu blob během připojení synchronizace. Tato chyba obvykle znamená, že synchronizace ještě nebyla dokončena. |  Počkejte na dokončení synchronizace Azure AD Connect a další pokus o připojení po dokončení synchronizace vyřeší problém |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Krok 5: Shromažďování protokolů a kontaktování podpory společnosti Microsoft

Získejte veřejné skripty zde: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Otevřete příkazový řádek `start_ngc_tracing_public.cmd`správce a spusťte aplikaci .
2. Proveďte kroky k reprodukci problému.
3. Ukončení spouštění skriptu `stop_ngc_tracing_public.cmd`protokolování spuštěním příkazu .
4. Zip a poslat protokoly pod pro `%SYSTEMDRIVE%\TraceDJPP\*` analýzu.

## <a name="troubleshoot-post-join-issues"></a>Poradce při potížích po připojení

### <a name="retrieve-the-join-status"></a>Načíst stav spojení 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: ANO a AzureADPrt: ANO
  
Tato pole označují, zda se uživatel úspěšně ověřil ve službě Azure AD při přihlášení k zařízení. Pokud jsou hodnoty **NE**, může to být způsobeno:

- Chybný klíč úložiště v čipu TPM přidruženém k zařízení při registraci (zkontrolujte KeySignTest při spuštění se zvýšenými oprávněními).
- Alternativní ID přihlášení
- Proxy HTTP nebyl a nalezen.

## <a name="known-issues"></a>Známé problémy
- V části Nastavení -> účty -> přístup u práce nebo školy, hybridní Azure AD připojená zařízení může zobrazit dva různé účty, jeden pro Azure AD a jeden pro místní AD, při připojení k mobilním hotspotům nebo externím WiFi sítím. Toto je pouze problém s uznatým prostředím a nemá žádný vliv na funkčnost. 
 
## <a name="next-steps"></a>Další kroky

Pokračovat v [odstraňování potíží pomocí příkazu dsregcmd](troubleshoot-device-dsregcmd.md)

Dotazy najdete v [nejčastějších dotazech ke správě zařízení.](faq.md)
