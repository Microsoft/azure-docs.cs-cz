---
title: Jednotné přihlašování pomocí Proxy aplikace | Microsoft Docs
description: Popisuje, jak poskytnout jednotné přihlašování pomocí proxy aplikace služby Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 8e3cc261576e38cc304dc740f89582f7fd857e1a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293030"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Omezené delegování Kerberos pro jednotné přihlašování pro vaše aplikace s Proxy aplikace

Můžete zadat jednotné přihlašování pro místní aplikace publikované prostřednictvím Proxy aplikace, které jsou zabezpečené integrované ověřování systému Windows. Tyto aplikace vyžadují lístek protokolu Kerberos pro přístup. Proxy aplikací používá protokol Kerberos vynuceným delegování použitím (KCD) pro podporu těchto aplikací. 

Můžete povolit jednotné přihlašování pro vaše aplikace používá integrované ověřování systému Windows (IWA) tím, že oprávnění konektory Proxy aplikace ve službě Active Directory k zosobnění uživatelů. Konektory použijte toto oprávnění posílat a přijímat tokeny jejich jménem.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak jednotné přihlašování s použitím KCD funguje
Tento diagram popisuje tok, když se uživatel pokusí o přístup k místní aplikaci, která používá integrované ověřování systému Windows.

![Microsoft AAD ověřování vývojový diagram](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci prostřednictvím Proxy aplikace.
2. Proxy aplikace přesměruje požadavek na službám Azure AD authentication preauthenticate. V tomto okamžiku Azure AD platí všechny použitelné ověřování a zásady autorizace, jako například vícefaktorové ověřování. Pokud je uživatel ověřen, Azure AD vytvoří token a odešle ji pro uživatele.
3. Uživatel předá token Proxy aplikace.
4. Proxy aplikací ověří token a načte z něj hlavní název uživatele (UPN) a poté odešle požadavek, název UPN a hlavní název služby (SPN) do konektoru prostřednictvím obousměrně ověřené zabezpečeného kanálu.
5. Konektor provádí vyjednávání protokolu Kerberos vynuceným delegování použitím (KCD) se místní AD, zosobňování uživatele získat token protokolu Kerberos pro aplikaci.
6. Služby Active Directory odešle token protokolu Kerberos pro aplikaci do konektoru.
7. Konektor odešle původní žádost na server aplikace pomocí protokolu Kerberos tokenu obdrženého ze služby Active Directory.
8. Aplikace odešle odpověď na tento konektor, který je pak vrácen do Proxy aplikace služby a v neposlední řadě pro uživatele.

## <a name="prerequisites"></a>Požadavky
Před zahájením práce s jednotné přihlašování pro aplikace, integrované ověřování systému Windows, zkontrolujte, zda že je prostředí připravené následující nastavení a konfigurace:

* Aplikace, jako jsou aplikace webové služby SharePoint, nastaveny na používání integrovaného ověřování systému Windows. Další informace najdete v tématu [povolení podpory pro ověřování protokolem Kerberos](https://technet.microsoft.com/library/dd759186.aspx), nebo SharePoint najdete v tématu [plánování pro ověřování protokolem Kerberos v SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Všechny aplikace mají [hlavní názvy služby](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Server se službou konektoru a server se službou aplikace jsou připojené k doméně a součástí stejné domény nebo důvěryhodné domény. Další informace o připojení k doméně, najdete v části [připojit počítač k doméně](https://technet.microsoft.com/library/dd807102.aspx).
* Serveru se spuštěným konektorem má přístup ke čtení atribut TokenGroupsGlobalAndUniversal pro uživatele. Toto výchozí nastavení může mít vliv zabezpečení posílení zabezpečení prostředí.

### <a name="configure-active-directory"></a>Konfigurace Active Directory
Konfigurace služby Active Directory se liší v závislosti na tom, jestli vaše konektor Proxy aplikace a aplikační servery jsou ve stejné doméně, nebo ne.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Konektor a aplikační server ve stejné doméně
1. Ve službě Active Directory, přejděte na **nástroje** > **uživatelé a počítače**.
2. Vyberte server se službou konektoru.
3. Klikněte pravým tlačítkem a vyberte **vlastnosti** > **delegování**.
4. Vyberte **důvěřovat tomuto počítači pro delegování pouze určeným službám**. 
5. V části **služby, ke kterým se tento účet může prokázat delegovanými přihlašovacími údaji** přidejte hodnotu pro identitu služby SPN aplikačního serveru. To umožňuje konektoru Proxy aplikace k zosobnění uživatelů ve službě Active Directory před aplikací definované v seznamu.

   ![Snímek obrazovky okna vlastností konektoru SVR](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Konektor a aplikační server v různých doménách
1. Seznam požadavků pro práci s použitím KCD napříč doménami, najdete v části [omezené delegování Kerberos mezi doménami](https://technet.microsoft.com/library/hh831477.aspx).
2. Použití `principalsallowedtodelegateto` vlastnost na serveru konektoru pro povolení Proxy aplikace pro delegování pro Connector server. Aplikační server `sharepointserviceaccount` a delegování server je `connectormachineaccount`. Pro systém Windows 2012 R2 tento kód použijte jako příklad:

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Sharepointserviceaccount může být účet počítače aktualizace Service Pack nebo účet služby, pod kterým běží fond aplikací aktualizace Service Pack.

## <a name="configure-single-sign-on"></a>Konfigurovat jednotné přihlašování 
1. Publikování aplikace podle pokynů v tématu [publikování aplikací pomocí Proxy aplikace](application-proxy-publish-azure-portal.md). Je nutné vybrat **Azure Active Directory** jako **metoda předběžného ověření**.
2. Když vaše aplikace se zobrazí v seznamu podnikové aplikace, vyberte ho a klikněte na **jednotného přihlašování**.
3. Nastavit režim přihlašování **integrované ověřování systému Windows**.  
4. Zadejte **interní aplikace SPN** aplikačního serveru. V tomto příkladu je název SPN pro naše publikovaná aplikace http/www.contoso.com. Tento hlavní název služby musí být v seznamu služeb, ke kterým můžete konektor prokázat delegovanými přihlašovacími údaji. 
5. Vyberte **delegované Identity přihlášení** pro konektor používat jménem uživatele. Další informace najdete v tématu [práce s jinou místní a cloudové identity](#Working-with-different-on-premises-and-cloud-identities)

   ![Konfigurace pokročilé aplikace](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Jednotné přihlašování pro aplikace systému Windows

Tok delegování protokolu Kerberos v Azure AD Application Proxy spustí, když služba Azure AD ověřuje uživatele v cloudu. Jakmile požadavek dorazí na místě, konektor proxy aplikace služby Azure AD vydá lístek protokolu Kerberos jménem uživatele interakcí s místní služby Active Directory. Tento proces se označuje jako použitím protokolu Kerberos omezené delegování (KCD). V další fázi posílá požadavek back-end aplikace s Tento lístek protokolu Kerberos. 

Existuje několik protokolů, které definovat, jak k posílání takových požadavků. Většina serverů jiný systém než Windows očekávat vyjednat s SPNEGO. Tento protokol je podporována v Azure AD Application Proxy, ale ve výchozím nastavení vypnutá. Server může být nakonfigurovaný pro SPNEGO nebo použitím standardní KCD, ale ne pomocí obou.

Pokud nakonfigurujete konektor počítač pro SPNEGO, ujistěte se, zda jsou všechny konektory v dané skupině konektor také nakonfigurovány s SPNEGO. Aplikace byla očekávána standardní použitím KCD by ho směrovat pomocí jiných konektorů, kteří nejsou nakonfigurovaní pro SPNEGO.
 

Chcete-li povolit SPNEGO:

1. Otevřete příkazový řádek, který běží jako správce.
2. Z příkazového řádku spusťte následující příkazy na serverech konektoru, které je třeba SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Další informace o protokolu Kerberos najdete v tématu [všechny budete chtít vědět o použitím protokolu Kerberos omezené delegování (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Aplikace pro Windows bez obvykle uživatelská jména uživatelů a názvy účtů SAM místo domény e-mailové adresy. Pokud se tato situace se vztahuje na aplikace, musíte nakonfigurovat pole identity delegované přihlášení pro připojení k vaší identity aplikace cloudové identity. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Práce s jinou místní a cloudové identity
Proxy aplikací předpokládá, že uživatelé mají stejnou identitu v cloudu a místně. Pokud není tento případ, stále můžete použitím KCD pro jednotné přihlašování. Konfigurace **delegované identity přihlášení** pro každou aplikaci, aby zadat identity, které má být použita při provádění jednotné přihlašování.  

Tato možnost umožňuje řada organizací, které mají různé místní a cloudové identity mít jednotného přihlašování z cloudu na místní aplikace bez nutnosti uživatelům zadat jiný uživatelských jmen a hesel. To zahrnuje organizace který:

* Interně mají několik domén (joe@us.contoso.com, joe@eu.contoso.com) a jednu doménu v cloudu (joe@contoso.com).
* Interně mít název směrovat domény (joe@contoso.usa) a právní jeden v cloudu.
* Nepoužívejte názvy domén interně (Jan)
* Použít různé aliasy místní i v cloudu. Například joe-johns@contoso.com vs. joej@contoso.com  

Pomocí Proxy aplikace můžete vybrat které identity používat k získání lístku protokolu Kerberos. Toto nastavení je na aplikaci. Některé z těchto možností jsou vhodné pro systémy, které nepřijímá formát e-mailové adresy, jiné jsou navržené pro alternativní přihlášení.

![Snímek obrazovky parametr identity delegované přihlášení](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Pokud se používá k přihlášení delegované identity, nemusí být hodnota jedinečná napříč doménami nebo doménovými strukturami ve vaší organizaci. Tento problém se můžete vyhnout tím, že publikujete tyto aplikace dvakrát pomocí dvou různých skupin konektor. Vzhledem k tomu, že každá aplikace má cílovou skupinu jiného uživatele, můžete jeho konektory spojit do jiné domény.

### <a name="configure-sso-for-different-identities"></a>Konfigurovat jednotné přihlašování pro různé identity
1. Konfigurace nastavení Azure AD Connect, hlavní identita je e-mailovou adresu (e-mailu). Provádí se jako součást procesu přizpůsobit změnou **hlavní název uživatele** pole v nastavení synchronizace. Tato nastavení taky určit, jak se uživatelé přihlásí k Office 365, Windows10 zařízení a dalších aplikací, které používají Azure AD jako své úložiště identit.  
   ![Identifikace uživatelů – snímek obrazovky - rozevírací hlavní název uživatele](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. V nastavení konfigurace aplikace pro aplikaci, kterou chcete upravit, vyberte **delegované Identity přihlášení** který se má použít:

   * Hlavní název uživatele (například joe@contoso.com)
   * Alternativní hlavní název uživatele (například joed@contoso.local)
   * Uživatelské jméno součástí hlavní název uživatele (například Jan)
   * Uživatelské jméno součástí alternativní hlavní název uživatele (například joed)
   * Název účtu SAM místní (závisí na konfiguraci řadiče domény)

### <a name="troubleshooting-sso-for-different-identities"></a>Řešení potíží s jednotného přihlašování pro různé identity
Pokud dojde k chybě při procesu jednotné přihlašování, zobrazí se v protokolu událostí počítače konektor jak je popsáno v [Poradce při potížích s](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Ale v některých případech je požadavek úspěšně odeslán do back-end aplikace během této aplikace reaguje v různých dalších odpovědi HTTP. Řešení potíží s těchto případech by se měl spustit tak, že prověří číslo události 24029 na počítači konektor v protokolu událostí relaci Proxy aplikace. V poli "user" v rámci podrobnosti události se zobrazí identitu uživatele, která byla použita pro delegování. Chcete-li protokolu relace zapnout, vyberte **ukazují analytické a ladicí protokoly** v nabídce zobrazení Prohlížeč událostí.

## <a name="next-steps"></a>Další postup

* [Postup konfigurace aplikace Proxy aplikace pro použití omezeného delegování protokolu Kerberos](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Řešení problémů, které máte s pomocí Proxy aplikace](application-proxy-troubleshoot.md)


Nejnovější novinky a aktualizace naleznete na [blogu proxy aplikace](http://blogs.technet.com/b/applicationproxyblog/)

