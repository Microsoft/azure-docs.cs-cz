---
title: Nastavení služby SharePoint Online a Exchange Online pro podmíněný přístup k Azure Active Directory | Microsoft Docs
description: Zjistěte, jak nastavit SharePoint Online a Exchange Online pro podmíněný přístup k Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 398b69769cebb9d54121e357faed06294499bd61
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723775"
---
# <a name="set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Nastavení služby SharePoint Online a Exchange Online pro Azure Active Directory podmíněného přístupu 

S [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), můžete řídit přístup uživatelů k cloudových aplikací. Pokud chcete používat podmíněný přístup k řízení přístupu na SharePoint a Exchange online, budete muset:

- Zkontrolujte, zda je podporováno váš scénář podmíněného přístupu
- Zabránit obcházení vynucení zásad podmíněného přístupu klientských aplikací.   

Tento článek vysvětluje, jak můžete vyřešit obou případech.


## <a name="what-you-need-to-know"></a>Co potřebujete vědět

Azure AD podmíněného přístupu můžete použít k ochraně aplikací cloudu při pokusu o ověření pochází z:

- Webový prohlížeč

- Klientskou aplikaci, která používá [moderní ověřování](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Některé cloudové aplikace také podporují starší verze ověřovací protokoly. To platí, například pro SharePoint Online a Exchange Online. Když klientské aplikace můžete použít starší verze ověřovací protokol pro přístup k cloudové aplikace, Azure AD nelze vynutit zásady podmíněného přístupu na tento pokus o přístup. Abyste zabránili obcházení vynucení zásad klientskou aplikaci, byste měli zkontrolovat, zda je možné pouze povolit moderní ověřování na ohroženým cloudových aplikací. 

Příklady pro klienta, které aplikace podmíněného přístupu se nevztahuje na jsou:

- Office 2010 a starší

- Office 2013, když není povolené moderní ověřování



 
## <a name="control-access-to-sharepoint-online"></a>Řízení přístupu k SharePoint Online

Kromě moderní ověřování SharePoint Online také podporuje starší verze ověřovací protokoly. Pokud jsou povolené starší verze ověřovací protokoly, zásad podmíněného přístupu pro službu SharePoint nejsou vynucená pro klienty, kteří nepoužívají moderní ověřování.

Starší verze ověřovací protokoly pro přístup k Sharepointu můžete zakázat pomocí **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** rutiny: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Řízení přístupu na Exchange Online

Při nastavování zásad podmíněného přístupu pro Exchange Online, je třeba zkontrolujte následující položky:

- Exchange ActiveSync

- Starší verze ověřovací protokoly



### <a name="exchange-activesync"></a>Exchange ActiveSync

I když protokolu Exchange Active Sync podporuje moderní ověřování, existují určitá omezení týkající se podpory pro scénáře přístupu podmíněného:

- Můžete konfigurovat pouze podmínku platformy zařízení  

    ![Platformy zařízení](./media/active-directory-conditional-access-no-modern-authentication/05.png)

- Nastavení požadavku vícefaktorového ověřování není podporováno  

    ![Podmíněný přístup](./media/active-directory-conditional-access-no-modern-authentication/01.png)

Chcete-li efektivně chránit přístup k Exchangi Online z protokolu Exchange ActiveSync, můžete:

- Nakonfigurujte zásady podmíněného přístupu podporovaných pomocí následujících kroků:

    a. Vyberte právě **Office 365 Exchange Online** jako cloudové aplikace.  

    ![Podmíněný přístup](./media/active-directory-conditional-access-no-modern-authentication/04.png)

    b. Vyberte **protokolu Exchange Active Sync** jako **klientskou aplikaci**a potom vyberte **uplatňovat zásady jenom na podporovaných platformách**.  

    ![Platformy zařízení](./media/active-directory-conditional-access-no-modern-authentication/03.png)

- Blok protokolu Exchange ActiveSync pomocí pravidel Active Directory Federation Services (AD FS).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Starší verze ověřovací protokoly

Kromě moderní ověřování Exchange Online také podporuje starší verze ověřovací protokoly. Pokud jsou povolené starší verze ověřovací protokoly, nejsou vynucená vaše zásady podmíněného přístupu pro Exchange Online pro klienty, kteří nepoužívají moderní ověřování.

Starší verze ověřovací protokoly pro Exchange Online můžete zakázat nastavením pravidla služby AD FS. Tato zablokuje přístup z:

- Starší klienty Office, jako je například Office 2013, která nemají povoleno moderní ověřování 

- Starší verze systému Office


## <a name="set-up-ad-fs-rules"></a>Nastavit pravidla služby AD FS

Pokud chcete povolit nebo blokovat přenosů dat na úrovni služby AD FS můžete použít následující autorizační pravidla vystavování. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Blok starší verze provoz z extranetu

Použitím následující tři pravidla: 

- Povolíte přístup pro:
    - Provoz protokolu Exchange ActiveSync
    - Komunikace prohlížeče
    - Provoz moderní ověřování
- Blokovat přístup pro: 
    - Starší verze klienta aplikace z extranetu

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

### <a name="block-legacy-traffic-from-anywhere"></a>Starší verze blokuje komunikaci odkudkoli.

Použitím následující tři pravidla:

- Povolíte přístup pro: 
    - Provoz protokolu Exchange ActiveSync
    - Komunikace prohlížeče
    - Provoz moderní ověřování
- Blokovat přístup pro: 
    - Starší verze aplikace z libovolného místa

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

Další informace najdete v tématu [podmíněný přístup v Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Pokyny týkající se konfigurace pravidla deklarací identity, najdete v části [konfigurovat pravidla deklarace identity](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






