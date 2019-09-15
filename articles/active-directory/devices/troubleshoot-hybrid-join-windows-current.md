---
title: Řešení potíží se zařízeními s připojením Hybrid Azure Active Directory – Azure Active Directory
description: Řešení potíží se zařízeními s Windows 10 a Windows serverem 2016, která jsou připojená k hybridní Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4a0f00c8bcf511f220d3e0df81adac1e9ff0d4
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995218"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Řešení potíží s hybridními Azure Active Directory připojenými zařízeními 

Obsah tohoto článku se vztahuje na zařízení s Windows 10 nebo Windows Server 2016.

Další klienty se systémem Windows najdete v článku [řešení potíží s hybridními Azure Active Directory připojenými zařízením nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md).

V tomto článku se předpokládá, že jste [nakonfigurovali zařízení se hybridem Azure Active Directory připojená](hybrid-azuread-join-plan.md) k podpoře následujících scénářů:

- Podmíněný přístup podle zařízení
- [Podnikový roaming nastavení](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello pro firmy](../active-directory-azureadjoin-passport-deployment.md)

Tento dokument poskytuje pokyny k odstraňování potíží, které vám pomohou vyřešit potenciální problémy. 

V případě systémů Windows 10 a Windows Server 2016 se v programu Hybrid Azure Active Directory JOIN podpora Windows 10 listopadu 2015 Update a vyšší.

## <a name="troubleshoot-join-failures"></a>Řešení chyb připojení

### <a name="step-1-retrieve-the-join-status"></a>Krok 1: Načtení stavu připojení 

**Načtení stavu připojení:**

1. Otevřete příkazový řádek jako správce.
2. Typ `dsregcmd /status`

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

### <a name="step-2-evaluate-the-join-status"></a>Krok 2: Vyhodnotit stav připojení 

Zkontrolujte následující pole a ujistěte se, že mají očekávané hodnoty:

#### <a name="domainjoined--yes"></a>DomainJoined : ANO  

Toto pole indikuje, jestli je zařízení připojené k místní službě Active Directory, nebo ne. Pokud je hodnota **ne**, zařízení nemůže provést hybridní připojení k Azure AD.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

Toto pole indikuje, jestli je zařízení zaregistrované ve službě Azure AD jako osobní zařízení (označené jako *připojené*k pracovišti). Tato hodnota by neměla **být pro počítač** připojený k doméně, který je taky připojený k hybridní službě Azure AD. Pokud je hodnota **Ano**, přidal se pracovní nebo školní účet před dokončením připojení k hybridní službě Azure AD. V takovém případě se účet ignoruje při použití aktualizace výročí Windows 10 (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined : ANO  

Toto pole indikuje, jestli je zařízení připojené ke službě Azure AD. Pokud je hodnota **ne**, připojení ke službě Azure AD ještě není dokončené. 

Další kroky k odstraňování potíží můžete pokračovat v dalších krocích.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Krok 3: Najděte fázi, ve které selhalo připojení, a kód chyby.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 a novější

Vyhledejte v části "předchozí registrace" v oddílu diagnostická data ve výstupu stavu připojení.
Pole chybová fáze označuje fázi selhání spojení, zatímco ' Client ErrorCode ' označuje kód chyby operace JOIN.

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

Pomocí protokolů Prohlížeč událostí vyhledejte fázi a chybový kód pro selhání spojení.

1. V prohlížeči událostí otevřete protokoly událostí **registrace zařízení uživatele** . Umístění v **části aplikace a služby protokol** > **registrace uživatelských zařízení** systému**Microsoft** > **Windows** > 
2. Vyhledejte události s následujícím identifikátory EventID rozmezí 304, 305, 307.

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Krok 4: Vyhledat možné příčiny a řešení ze seznamů níže

#### <a name="pre-check-phase"></a>Předběžná kontrolní fáze

Možné příčiny selhání:

- Zařízení nemá žádný pohled na řadič domény.
   - Zařízení musí být v interní síti organizace nebo na VPN se síťovou linkou, která je v místním řadiči domény Active Directory (AD).

#### <a name="discover-phase"></a>Fáze zjišťování

Možné příčiny selhání:

- Objekt spojovacího bodu služby (SCP) nesprávně nakonfigurovaný/nelze číst objekt SCP z řadiče domény.
   - V doménové struktuře služby Active Directory je vyžadován platný objekt SCP, ke kterému patří zařízení, které odkazuje na ověřený název domény ve službě Azure AD.
   - Podrobnosti najdete v části [Konfigurace spojovacího bodu služby](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Nepovedlo se připojit a načíst metadata zjišťování z koncového bodu zjišťování.
   - Zařízení by mělo mít přístup `https://enterpriseregistration.windows.net`v kontextu systému, aby bylo možné zjistit koncové body registrace a autorizace. 
   - Pokud místní prostředí vyžaduje odchozí proxy server, správce IT musí zajistit, aby počítačový účet zařízení mohl zjišťovat a tiše ověřit u odchozího proxy serveru.
- Nepovedlo se připojit ke koncovému bodu sféry uživatele a provádět zjišťování sféry. (Jenom Windows 10 verze 1809 a novější)
   - Zařízení by mělo mít přístup `https://login.microsoftonline.com`v kontextu systému, aby bylo možné provést zjišťování sféry pro ověřenou doménu a určit typ domény (spravované/federované).
   - Pokud místní prostředí vyžaduje odchozí proxy server, správce IT musí zajistit, aby kontext systému v zařízení mohl zjišťovat a tiše ověřovat na odchozím proxy serveru.

**Běžné kódy chyb:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Brání Nelze načíst objekt SCP a získat informace o tenantovi Azure AD.
   - Řešení: Přečtěte si část [Konfigurace spojovacího bodu služby](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Brání Obecná chyba zjišťování Nepovedlo se získat metadata zjišťování z DRS.
   - Řešení: Najděte níže uvedenou dílčí chybu a prozkoumejte ji podrobněji.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Brání Při provádění zjišťování vypršel časový limit operace.
   - Řešení: Ujistěte se `https://enterpriseregistration.windows.net` , že je přístupná v kontextu systému. Další informace najdete v části požadavky na [připojení k síti](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Brání Došlo k chybě zjišťování obecné sféry. Nepovedlo se určit typ domény (spravované nebo federované) ze služby STS. 
   - Řešení: Najděte níže uvedenou dílčí chybu a prozkoumejte ji podrobněji.

**Kódy běžných chyb:**

Chcete-li najít kód pro chybu zjišťování kódu chyby, použijte jednu z následujících metod.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 a novější

V části diagnostická data ve výstupu stavu připojení vyhledejte test DRS Discovery.

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

Pomocí protokolů Prohlížeč událostí vyhledejte fázi a kód chyby připojení.

1. V prohlížeči událostí otevřete protokoly událostí **registrace zařízení uživatele** . Umístění v **části aplikace a služby protokol** > **registrace uživatelských zařízení** systému**Microsoft** > **Windows** > 
2. Vyhledejte události s následujícím identifikátory EventID rozmezí 201

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Chyby sítě

- **WININET_E_CANNOT_CONNECT** (0x80072EFD/-2147012867)
   - Brání Nepovedlo se navázat spojení se serverem.
   - Řešení: Zajistěte síťové připojení k požadovaným prostředkům společnosti Microsoft. Další informace najdete v tématu [požadavky na připojení k síti](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Brání Obecný časový limit sítě
   - Řešení: Zajistěte síťové připojení k požadovaným prostředkům společnosti Microsoft. Další informace najdete v tématu [požadavky na připojení k síti](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072F8F/-2147012721)
   - Brání Síťovému zásobníku se nepovedlo dekódovat odpověď ze serveru.
   - Řešení: Ujistěte se, že síťový proxy server nekoliduje a nemění odpověď serveru.

###### <a name="http-errors"></a>Chyby protokolu HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Brání Objekt spojovacího bodu služby byl nakonfigurován s chybným ID tenanta. V tenantovi se nenašly žádné aktivní odběry.
   - Řešení: Ujistěte se, že je objekt SCP nakonfigurovaný se správným ID tenanta Azure AD a s aktivními předplatnými nebo v tenantovi.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Brání HTTP 503 ze serveru DRS
   - Řešení: Server je momentálně nedostupný. budoucí pokusy o připojení budou po návratu serveru do režimu online nejspíš úspěšné.

###### <a name="other-errors"></a>Další chyby

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Brání Odpověď serveru JSON se nedala analyzovat. Důvodem je, že proxy vrací HTTP 200 se stránkou ověřování HTML.
   - Řešení: Pokud místní prostředí vyžaduje odchozí proxy server, správce IT musí zajistit, aby kontext systému v zařízení mohl zjišťovat a tiše ověřovat na odchozím proxy serveru.

#### <a name="authentication-phase"></a>Fáze ověřování

Platí pouze pro účty federované domény.

Důvody pro selhání:

- Nepovedlo se získat přístupový token pro prostředek DRS v tichém režimu.
   - Zařízení s Windows 10 získávají ověřovací token ze služby Federation Service pomocí integrovaného ověřování systému Windows na aktivní koncový bod WS-Trust. Podrobnosti: [Konfigurace služba FS (Federation Service)](hybrid-azuread-join-manual.md##set-up-issuance-of-claims)

**Běžné kódy chyb:**

Použijte protokoly Prohlížeč událostí k vyhledání kódu chyby, kódu chyby, kódu chyby serveru a chybové zprávy serveru.

1. V prohlížeči událostí otevřete protokoly událostí **registrace zařízení uživatele** . Umístění v **části aplikace a služby protokol** > **registrace uživatelských zařízení** systému**Microsoft** > **Windows** > 
2. Vyhledejte události s následujícím ID události 305

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Chyby konfigurace

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Brání Ověřovací protokol není WS-Trust.
   - Řešení: Místní zprostředkovatel identity musí podporovat WS-Trust. 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Brání Místní služba FS (Federation Service) nevrátila odpověď XML.
   - Řešení: Zajistěte, aby koncový bod MEX vrátil platný kód XML. Zajistěte, aby proxy nenarušil a vracel odpovědi, které nejsou ve formátu XML.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Brání Nepovedlo se zjistit koncový bod pro ověřování uživatelského jména a hesla.
   - Řešení: Ověřte nastavení místních zprostředkovatelů identity. Zajistěte, aby byly povoleny koncové body WS-Trust a aby odpověď MEX obsahovala tyto správné koncové body.

##### <a name="network-errors"></a>Chyby sítě

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Brání Obecný časový limit sítě
   - Řešení: Ujistěte se `https://login.microsoftonline.com` , že je přístupná v kontextu systému. Zajistěte, aby byl místní zprostředkovatel identity přístupný v kontextu systému. Další informace najdete v tématu [požadavky na připojení k síti](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Brání Spojení s koncovým bodem ověřování bylo přerušeno.
   - Řešení: Zkuste to za chvíli znovu nebo se pokuste připojit z alternativního umístění stabilní sítě.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Brání Certifikát SSL (Secure Sockets Layer) (SSL) odeslaný serverem nemohl být ověřen.
   - Řešení: Ověřte, jestli je čas klienta zkosený. Zkuste to za chvíli znovu nebo se pokuste připojit z alternativního umístění stabilní sítě. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Brání Pokus o připojení `https://login.microsoftonline.com` se nezdařil.
   - Řešení: Ověřte síťové připojení k `https://login.microsoftonline.com`.

##### <a name="other-errors"></a>Další chyby

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Brání Služba Azure AD nepřijala token SAML od místního zprostředkovatele identity.
   - Řešení: Ověřte nastavení federačního serveru. Vyhledejte kód chyby serveru v protokolech ověřování.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Brání Odpověď serveru WS-Trust ohlásila výjimku selhání a nepodařilo se jí získat kontrolní výraz.
   - Řešení: Ověřte nastavení federačního serveru. Vyhledejte kód chyby serveru v protokolech ověřování.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Brání Při pokusu o získání přístupového tokenu z koncového bodu tokenu byla přijata chyba.
   - Řešení: Vyhledejte základní chybu v protokolu ADAL. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Brání Obecné selhání ADAL
   - Řešení: Vyhledejte kód chyby nebo kód chyby serveru z protokolů ověřování.
    
#### <a name="join-phase"></a>Spojovací fáze

Důvody pro selhání:

Vyhledejte typ registrace a vyhledejte kód chyby ze seznamu níže.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 a novější

Vyhledejte v části "předchozí registrace" v oddílu diagnostická data ve výstupu stavu připojení.
Pole registrace typu označuje typ provedeného spojení.

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

Pomocí protokolů Prohlížeč událostí vyhledejte fázi a kód chyby připojení.

1. V prohlížeči událostí otevřete protokoly událostí **registrace zařízení uživatele** . Umístění v **části aplikace a služby protokol** > **registrace uživatelských zařízení** systému**Microsoft** > **Windows** > 
2. Vyhledejte události s následujícím identifikátory EventID rozmezí 204

![Událost protokolu selhání](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>Chyby HTTP vrácené ze serveru DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Brání Byla přijata chybová odpověď z DRS s kódem chyby: DirectoryError
   - Řešení: Možné příčiny a řešení najdete v kódu chyby serveru.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Brání Byla přijata chybová odpověď z DRS s kódem chyby: "AuthenticationError" a ErrorSubCode není "DeviceNotFound". 
   - Řešení: Možné příčiny a řešení najdete v kódu chyby serveru.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Brání Byla přijata chybová odpověď z DRS s kódem chyby: DirectoryError
   - Řešení: Možné příčiny a řešení najdete v kódu chyby serveru.

##### <a name="tpm-errors"></a>Chyby čipu TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Brání Operace čipu TPM se nezdařila nebo byla neplatná.
   - Řešení: Pravděpodobně kvůli chybné imagi nástroje Sysprep. Ujistěte se, že počítač, ze kterého byla vytvořená image nástroje Sysprep, není připojený k Azure AD, připojili jste k hybridní službě Azure AD nebo zaregistrovali Azure AD.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Brání Obecná chyba čipu TPM 
   - Řešení: Zakáže čip TPM na zařízeních s touto chybou. Windows 10 verze 1809 a vyšší automaticky detekuje selhání čipu TPM a dokončuje připojení k hybridní službě Azure AD bez použití čipu TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Brání ČIP TPM v režimu FIPS není v současné době podporován.
   - Řešení: Zakáže čip TPM na zařízeních s touto chybou. Windows 1809 automaticky detekuje selhání čipu TPM a dokončuje připojení k hybridní službě Azure AD bez použití čipu TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Brání ČIP TPM byl uzamčen.
   - Řešení: Přechodná chyba. Počkejte na cooldown období. Pokus o připojení po určité době by měl být úspěšný. Další informace najdete v článku [základy čipu TPM](https://docs.microsoft.com/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering) .

##### <a name="network-errors"></a>Chyby sítě

- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Brání Celkový časový limit sítě při pokusu o registraci zařízení na DRS
   - Řešení: Ověřte síťové připojení k `https://enterpriseregistration.windows.net`.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072EE7/-2147012889)
   - Brání Název nebo adresu serveru nelze přeložit.
   - Řešení: Ověřte síťové připojení k `https://enterpriseregistration.windows.net`. Zajistěte, aby překlad názvů DNS pro název hostitele byl přesný v n/w a na zařízení.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Brání Připojení k serveru se neobvykle ukončilo.
   - Řešení: Zkuste to za chvíli znovu nebo se pokuste připojit z alternativního umístění stabilní sítě.

##### <a name="federated-join-server-errors"></a>Chyby serveru federovaného připojení

| Kód chyby serveru | Chybová zpráva serveru | Možné důvody | Řešení |
| --- | --- | --- | --- |
| DirectoryError | Vaše žádost se dočasně omezuje. Zkuste to prosím po 300 sekundách. | Očekávaná chyba. Je možné, že v důsledku rychlé úspěšnosti je potřeba provést několik žádostí o registraci. | Opakovaný pokus o připojení po cooldown období |

##### <a name="sync-join-server-errors"></a>Chyby serveru připojit se k synchronizaci

| Kód chyby serveru | Chybová zpráva serveru | Možné důvody | Řešení |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: Tenant <UUID> se nenašel. K této chybě může dojít, pokud pro tenanta nejsou k dispozici žádná aktivní předplatná. Obraťte se na správce předplatného. | ID tenanta v objektu SCP je nesprávné. | Ujistěte se, že je objekt SCP nakonfigurovaný se správným ID tenanta Azure AD a s aktivním předplatným a že je přítomen v tenantovi. |
| DirectoryError | Objekt zařízení se zadaným ID nebyl nalezen. | Očekávaná chyba připojení synchronizace. Objekt zařízení se nesynchronizoval ze služby AD do Azure AD. | Počkejte, než se Azure AD Connect synchronizace dokončí, a při dalším pokusu o připojení se problém vyřeší. |
| AuthenticationError | Ověření identifikátoru SID cílového počítače | Certifikát na zařízení Azure AD se neshoduje s certifikátem použitým k podepsání objektu BLOB během připojení synchronizace. Tato chyba obvykle znamená, že synchronizace ještě nebyla dokončena. |  Počkejte, než se Azure AD Connect synchronizace dokončí, a při dalším pokusu o připojení se problém vyřeší. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Krok 5: Shromažďovat protokoly a kontaktovat podpora Microsoftu

Získejte veřejné skripty tady: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Otevřete příkazový řádek správce a spusťte `start_ngc_tracing_public.cmd`příkaz.
2. Proveďte kroky pro reprodukování problému.
3. Zastavte skript protokolování spuštěním příkazu `stop_ngc_tracing_public.cmd`.
4. Soubory zip a odesílají protokoly `%SYSTEMDRIVE%\TraceDJPP\*` pro účely analýzy.

## <a name="troubleshoot-post-join-issues"></a>Řešení problémů po připojení

### <a name="retrieve-the-join-status"></a>Načtení stavu připojení 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: Ano a AzureADPrt: ANO
  
Tato pole označují, jestli se uživatel úspěšně ověřil do služby Azure AD při přihlášení k zařízení. Pokud jsou hodnoty **ne**, může to být způsobeno:

- Chybný klíč úložiště v čipu TPM přidruženého k zařízení při registraci (při spuštění se zvýšenými oprávněními zkontrolujte KeySignTest).
- Alternativní ID přihlášení
- Proxy server HTTP se nenašel.

## <a name="known-issues"></a>Známé problémy
- V části Nastavení-> účty – > přístup do práce nebo do školy můžou zařízení připojená k hybridní službě Azure AD zobrazovat dva různé účty, jednu pro Azure AD a jednu pro místní službu AD, když jsou připojeni k mobilním hotspotům nebo externím sítím Wi-Fi. Toto je jenom problém s uživatelským rozhraním a nemá žádný vliv na funkčnost. 
 
## <a name="next-steps"></a>Další postup

Pokračování [v řešení potíží pomocí příkazu dsregcmd](troubleshoot-device-dsregcmd.md)

Otázky najdete v tématu [Nejčastější dotazy ke správě zařízení](faq.md) .
