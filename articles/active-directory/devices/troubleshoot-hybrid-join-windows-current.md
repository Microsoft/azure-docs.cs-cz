---
title: Zařízení s Windows 10 a Windows serveru 2016 připojená k řešení potíží s hybridní služby Azure Active Directory | Dokumentace Microsoftu
description: Řešení potíží s hybridní služby Azure Active Directory připojené zařízení s Windows 10 a Windows serveru 2016.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 584a36e766e9e054af2ad1ce4f2105108fe80525
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414907"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Zařízení s Windows 10 a Windows serveru 2016 připojená k řešení potíží s hybridní služby Azure Active Directory 

V tomto článku se vztahuje na následující klienty:

-   Windows 10
-   Windows Server 2016

Pro ostatní klienty Windows najdete v článku [zařízení s nižší úrovně připojená k hybridní Poradce při potížích s Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md).

Tento článek předpokládá, že máte [nakonfigurované hybridní služby Azure Active Directory zařízení připojená k](hybrid-azuread-join-plan.md) pro podporu následujících scénářů:

- Podmíněný přístup podle zařízení

- [Podnikový roaming nastavení](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello pro firmy](../active-directory-azureadjoin-passport-deployment.md)


Tento dokument obsahuje pokyny k odstraňování problémů, o tom, jak vyřešit potenciální problémy. 


Pro Windows 10 a Windows Server 2016, hybridní připojení k Azure Active Directory podporuje Windows 10 November 2015 Update a vyšší. Doporučujeme používat Anniversary update.

## <a name="step-1-retrieve-the-join-status"></a>Krok 1: Načíst stav spojení 

**Pokud chcete načíst stav připojení:**

1. Otevřete příkazový řádek jako správce

2. Typ **dsregcmd/status**



    +----------------------------------------------------------------------+
    | Stav zařízení |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: Žádné DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 kryptografický otisk: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: TpmProtected zprostředkovatele kryptografických služeb platformy Microsoft: Ano KeySignTest:: musíte spustit se zvýšenými oprávněními k testování.
                  Zprostředkovatele identity: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ == JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn: ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn: ms-drs:enterpriseregistration.windows.net DomainJoined: Ano DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
    | Stav uživatele |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizace WamDefaultId: https://login.microsoft.com WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} AzureAdPrt (Azure AD): Ano



## <a name="step-2-evaluate-the-join-status"></a>Krok 2: Vyhodnoťte stav spojení 

Zkontrolujte následující pole a ujistěte se, že očekávané hodnoty:

### <a name="azureadjoined--yes"></a>AzureAdJoined: Ano  

Toto pole indikuje, jestli zařízení je spojen s Azure AD. Pokud je hodnota **ne**, nebyl dosud dokončen připojení ke službě Azure AD. 

**Možné příčiny:**

- Ověřování počítače pro spojení se nezdařilo.

- Proxy server HTTP je v organizaci, který nelze zjistit počítače

- Počítač nemá přístup k ověřování Azure AD nebo Azure DRS pro registraci.

- Počítač nemusí být v interní síti organizace nebo VPN s přímým přístupem na dohled místní řadič domény služby AD.

- Pokud má počítač čip TPM, může být ve špatném stavu.

- Může být chybná konfigurace ve službách v dokumentu jste si předtím poznamenali, že budete muset znovu ověřit. Obvyklými příklady jsou:

    - Federační server nemá povolené koncové body WS-Trust

    - Federační server nepovoluje příchozí ověřování z počítačů ve vaší síti pomocí integrovaného ověřování Windows.

    - Neexistuje žádný spojovacího bodu služby objektů, který odkazuje na název ověřené domény ve službě Azure AD v doménové struktuře AD, kam počítač patří do

---

### <a name="domainjoined--yes"></a>DomainJoined: Ano  

Toto pole indikuje, jestli zařízení je připojen k místní Active Directory, nebo ne. Pokud je hodnota **ne**, zařízení nelze provést připojení k hybridní službě Azure AD.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: Ne  

Toto pole indikuje, jestli je zařízení zaregistrované ve službě Azure AD jako osobní zařízení (označené jako *připojená k pracovišti*). Tato hodnota by měla být **ne** pro počítač připojený k doméně, která je také připojená k hybridní službě Azure AD. Pokud je hodnota **Ano**, pracovní nebo školní účet byl přidán před dokončením připojení k hybridní službě Azure AD. V takovém případě účtu se ignoruje při použití verze Anniversary Update sady Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Ano a AzureADPrt: Ano
  
Tato pole označuje, zda uživatel se úspěšně ověřil do služby Azure AD při přihlašování k zařízení. Pokud jsou hodnoty **ne**, může být vypršení platnosti:

- Klíč chybný úložiště (STK) zařízení TPM přidružený k zařízení při registraci (Kontrola KeySignTest při spuštění se zvýšenými oprávněními).

- Alternativním přihlašovacím ID

- Proxy server HTTP nebyla nalezena.

## <a name="next-steps"></a>Další postup

Dotazy, najdete v článku [nejčastější dotazy ke správě zařízení](faq.md) 