---
title: Řešení potíží s hybridní Azure Active Directory připojené zařízení s Windows 10 a Windows Server 2016 | Microsoft Docs
description: Řešení potíží s hybridní Azure Active Directory připojené zařízení s Windows 10 a Windows Server 2016.
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
ms.openlocfilehash: 40303d72f4facc6cd8c762f2542c48df3e370c0a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713724"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Řešení potíží s hybridní Azure Active Directory připojené zařízení s Windows 10 a Windows Server 2016 

V tomto článku se vztahuje na následující klienty:

-   Windows 10
-   Windows Server 2016

Pro ostatní klienty systému Windows najdete v části [nižší úrovně zařízení připojená k hybridní Poradce při potížích s Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-legacy.md).

Tento článek předpokládá, že máte [nakonfigurované hybridní Azure Active Directory zařízení připojená k](device-management-hybrid-azuread-joined-devices-setup.md) k podporují následující scénáře:

- Podmíněný přístup využívající zařízení

- [Enterprise cestovní nastavení](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello pro firmy](active-directory-azureadjoin-passport-deployment.md)


Tento dokument obsahuje pokyny k odstraňování problémů, o tom, jak vyřešit potenciální problémy. 


Pro Windows 10 a Windows Server 2016, hybridní připojení k Azure Active Directory podporuje Windows 10. listopadu 2015 aktualizace a vyšší. Doporučujeme používat výročí aktualizace.

## <a name="step-1-retrieve-the-join-status"></a>Krok 1: Načíst stav spojení 

**Načíst stav připojení:**

1. Otevřete příkazový řádek jako správce

2. Typ   **/dsregcmd status**



    +----------------------------------------------------------------------+
    | Stav zařízení |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: Žádné DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 kryptografický otisk: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: TpmProtected zprostředkovatel kryptografických služeb platformy Microsoft: Ano KeySignTest:: musíte spustit zvýšenými k testování.
                  Rozšíření IDP: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ == JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn: ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn: ms-drs:enterpriseregistration.windows.net DomainJoined: Ano DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
    | Stav uživatele |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizace WamDefaultId: https://login.microsoft.com WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} AzureAdPrt (AzureAd): Ano



## <a name="step-2-evaluate-the-join-status"></a>Krok 2: Vyhodnocení stavu připojení k 

Zkontrolujte následující pole a ujistěte se, že mají očekávaných hodnot:

### <a name="azureadjoined--yes"></a>AzureAdJoined: Ano  

Toto pole určuje, zda je připojení zařízení s Azure AD. Pokud je hodnota **ne**, nebyl ještě dokončen připojení ke službě Azure AD. 

**Možné příčiny:**

- Ověřování počítače, a připojte se nezdařilo.

- V organizaci, která nemůže být zjištěny počítače je proxy server HTTP

- Počítač se nemůže připojit k ověření Azure AD nebo Azure DRS pro registraci

- Počítač nemusí být v interní síti organizace nebo na VPN s přímé směrem pohledu na místní řadič domény AD.

- Pokud má počítač čip TPM, může být ve špatném stavu.

- Může být chybnou konfigurací v služeb v dokumentu si předtím poznamenali, budete muset znovu ověřit. Běžných příkladů:

    - Federační server nemá WS-Trust koncových bodů povoleno

    - Federační server nepovoluje příchozí ověřování z počítačů ve vaší síti použití integrovaného ověřování systému Windows.

    - Neexistuje žádný objekt spojovací bod služby, který odkazuje na název ověřené domény ve službě Azure AD v doménové struktuře AD, kam počítač patří do

---

### <a name="domainjoined--yes"></a>DomainJoined: Ano  

Toto pole určuje, zda je připojení zařízení do služby Active Directory v místě nebo ne. Pokud je hodnota **ne**, zařízení nelze provést připojení k hybridní Azure AD.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: Ne  

Toto pole určuje, jestli je zařízení zaregistrované v Azure AD jako osobní zařízení (označen jako *připojené k síti na pracovišti*). Tato hodnota by měla být **ne** pro počítač připojený k doméně, která je také připojené k hybridní Azure AD. Pokud je hodnota **Ano**, pracovní nebo školní účet byl přidán před dokončením připojení k hybridní Azure AD. V takovém případě účtu se ignoruje při použití výročí aktualizovanou verzi Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Ano a AzureADPrt: Ano
  
Tato pole označuje, zda byl uživatel úspěšně ověřen do služby Azure AD při přihlášení k zařízení. Pokud jsou hodnoty **ne**, může to být kvůli:

- Klíč chybný úložiště (STK) v čipu TPM, které jsou přidružené k zařízení na základě zápisu (Kontrola KeySignTest při spuštění, které se zvýšenými oprávněními).

- Alternativním přihlašovacím ID

- Server Proxy protokolu HTTP nebyl nalezen.

## <a name="next-steps"></a>Další postup

Otázky, najdete v článku [nejčastější dotazy ke správě zařízení](device-management-faq.md) 