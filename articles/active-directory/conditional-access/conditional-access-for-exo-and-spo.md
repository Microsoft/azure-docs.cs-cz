---
title: Nastavení Sharepointu Online a Exchange Online pro podmíněný přístup Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu SharePoint Online a Exchange Online pro podmíněný přístup Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dad650ac1cc7ab8822925a4a9759b68ee0d0088
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185989"
---
# <a name="how-to-set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Jak: Nastavení Sharepointu Online a Exchange Online pro podmíněný přístup Azure Active Directory 

S [podmíněného přístupu Azure Active Directory (Azure AD)](overview.md), můžete řídit přístup uživatelů k vašim cloudovým aplikacím. Pokud chcete používat podmíněný přístup pro řízení přístupu k Sharepointu a Exchange online, budete muset:

- Zkontrolujte, zda je podporováno váš scénář podmíněného přístupu
- Klientské aplikace bránit obcházení vynucení zásad podmíněného přístupu.   

Tento článek vysvětluje, jak můžete řešit oba případy.


## <a name="what-you-need-to-know"></a>Co je potřeba vědět

Podmíněný přístup Azure AD můžete použít k ochraně aplikací cloudu při pokusu o ověření se segmenty Convenience:

- Webový prohlížeč

- Klientskou aplikaci, která používá [moderní ověřování](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Některé cloudové aplikace také podporují starší verze ověřovacích protokolů. To platí, například pro SharePoint Online a Exchange Online. Když klientská aplikace můžete používat starší ověřovací protokol pro přístup ke cloud app, Azure AD nemůže vynutit zásady podmíněného přístupu na tento pokus o přístup. Abyste zabránili obcházení vynucení zásad klientskou aplikaci, byste měli zkontrolovat, jestli je možné jenom povolení moderního ověřování v ovlivněných cloudových aplikací. 

Příkladem aplikací podmíněného přístupu se nedá použít u klienta jsou:

- Office 2010 a starší

- Office 2013, pokud není povolené moderní ověřování



 
## <a name="control-access-to-sharepoint-online"></a>Řízení přístupu k Sharepointu Online

Kromě moderní ověřování SharePoint Online také podporuje starší verze ověřovacích protokolů. Pokud jsou povolena starší verze ověřovací protokoly, zásad podmíněného přístupu pro SharePoint nejsou vynucena pro klienty, které nepoužívají moderní ověřování.

Protokoly pro starší verze ověřování pro přístup ke službě SharePoint můžete zakázat s použitím **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** rutiny: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Řízení přístupu k Exchangi Online

Když nastavíte zásady podmíněného přístupu pro Exchange Online, budete muset zkontrolovat následující:

- Exchange ActiveSync

- Starší verze ověřovacích protokolů



### <a name="exchange-activesync"></a>Exchange ActiveSync

I když protokolu Exchange Active Sync podporuje moderní ověřování, narazíte na určitá omezení ohledně podpory pro scénáře přístupu podmíněného:

- Můžete konfigurovat pouze podmínku platformy zařízení  

    ![Platformy zařízení](./media/conditional-access-for-exo-and-spo/05.png)

- Nastavení požadavku vícefaktorového ověřování se nepodporuje  

    ![Podmíněný přístup](./media/conditional-access-for-exo-and-spo/01.png)

Chcete-li účinně chránit přístup k Exchangi Online z protokolu Exchange ActiveSync, můžete:

- Konfigurace zásad podporovaných podmíněného přístupu pomocí následujících kroků:

    a. Vyberte právě **Office 365 Exchange Online** jako cloudové aplikace.  

    ![Podmíněný přístup](./media/conditional-access-for-exo-and-spo/04.png)

    b. Vyberte **protokolu Exchange Active Sync** jako **klientskou aplikaci**a pak vyberte **použít zásady jenom na podporovaných platformách**.  

    ![Platformy zařízení](./media/conditional-access-for-exo-and-spo/03.png)

- Blokovat, Exchange ActiveSync s využitím pravidel Active Directory Federation Services (AD FS).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Starší verze ověřovacích protokolů

Kromě moderní ověřování Exchange Online také podporuje starší verze ověřovacích protokolů. Pokud jsou povolené protokoly starší verze ověřování, zásad podmíněného přístupu pro Exchange Online vynucovat u klientů, které nepoužívají moderní ověřování.

Starší verze ověřovací protokoly můžete zakázat pro Exchange Online tak, že nastavíte pravidla služby AD FS. Tato blokuje přístup ze:

- Starší klienti Office, jako je například Office 2013, které nemají povolené moderní ověřování 

- Starší verze systému Office


## <a name="set-up-ad-fs-rules"></a>Nastavte pravidla služby AD FS

Následující pravidla autorizace vystavení slouží k povolení nebo blokování provozu na úrovni služby AD FS. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Blok starší verze provozu z extranetu

S použitím následující tři pravidla: 

- Můžete povolit přístup pro:
    - Provoz protokolu Exchange ActiveSync
    - Provoz prohlížeče
    - Provoz moderní ověřování
- Můžete zablokovat přístup pro: 
    - Starší verzi klientských aplikací z extranetu

**Pravidlo 1:**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Pravidlo 2:**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Pravidlo 3:**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>Blokuje komunikaci starších verzí z libovolného místa

S použitím následující tři pravidla:

- Můžete povolit přístup pro: 
    - Provoz protokolu Exchange ActiveSync
    - Provoz prohlížeče
    - Provoz moderní ověřování
- Můžete zablokovat přístup pro: 
    - Starší verze aplikací z libovolného místa

##### <a name="rule-1"></a>Pravidlo 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Pravidlo 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Pravidlo 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [co je podmíněný přístup v Azure Active Directory](overview.md).

Pokyny týkající se konfigurace pravidla deklarací identity, najdete v části [konfigurovat pravidla deklarace identity](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






