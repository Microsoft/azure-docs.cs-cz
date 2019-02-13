---
title: Jednotné přihlašování s Proxy aplikací | Dokumentace Microsoftu
description: Popisuje, jak poskytovat jednotné přihlašování pomocí Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19ea76f28995dfa7f7dd5a6f280f8319f5b4ca26
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180756"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Omezené delegování protokolu Kerberos pro jednotné přihlašování do aplikací pomocí Proxy aplikace

Můžete poskytnout jednotné přihlašování pro místní aplikace publikované prostřednictvím Proxy aplikací, které jsou zabezpečené pomocí integrované ověřování Windows. Tyto aplikace vyžadují lístek protokolu Kerberos pro přístup. Proxy aplikací používá protokol Kerberos omezené delegování (KCD) pro podporu těchto aplikací. 

Můžete povolit jednotné přihlašování pro vaše aplikace pomocí integrovaného ověřování Windows (IWA) tak, že udělíte oprávnění konektory Proxy aplikací ve službě Active Directory zosobňovalo. Konektory použít toto oprávnění odesílat a přijímat tokeny jejich jménem.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak jednotné přihlašování s KCD funguje
Tento diagram popisuje tok, když se uživatel pokusí o přístup k místní aplikaci, která používá IWA.

![Vývojový diagram ověřování Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci pomocí služby Proxy aplikací.
2. Proxy aplikace přesměruje požadavek preauthenticate ověřování služby Azure AD. V tuto chvíli Azure AD použije všechny příslušné ověřování a zásady autorizace, jako je například vícefaktorové ověřování. Pokud uživatel je ověřen, Azure AD vytvoří token a odešle se uživateli.
3. Uživatel se předá token Proxy aplikací.
4. Proxy aplikací ověří token a načte z něj hlavní název uživatele (UPN) a potom odešle požadavek, hlavní název uživatele a hlavní název služby (SPN) konektoru prostřednictvím obousměrně ověřeného zabezpečeného kanálu.
5. Konektor provádí vyjednávání protokolu Kerberos omezené delegování (KCD) se v místním AD zosobňování uživatele získá token protokolu Kerberos k aplikaci.
6. Služby Active Directory odešle token protokolu Kerberos pro použití konektoru.
7. Konektor odesílá původní požadavek na server aplikace pomocí token protokolu Kerberos, které získala ze služby AD.
8. Aplikace odešle odpověď na konektoru, který je pak vrácen do Proxy aplikace služby a nakonec na uživatele.

## <a name="prerequisites"></a>Požadavky
Před zahájením práce s jednotné přihlašování pro aplikace IWA, ujistěte se, že je prostředí připravené s následující nastavení a konfigurace:

* Vaše aplikace, jako je SharePoint Web apps, jsou nastaveny na používat integrované ověřování Windows. Další informace najdete v tématu [povolit podporu pro ověřování protokolem Kerberos](https://technet.microsoft.com/library/dd759186.aspx), nebo pro SharePoint najdete v tématu [plánování ověřování protokolem Kerberos v Sharepointu 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Všechny aplikace mají [hlavní názvy služby](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Server se službou konektoru a server se službou aplikace jsou připojené k doméně a součástí stejné doméně nebo důvěryhodné domény. Další informace o připojení k doméně najdete v tématu [připojit počítač k doméně](https://technet.microsoft.com/library/dd807102.aspx).
* Server se službou konektoru má přístup k číst TokenGroupsGlobalAndUniversal atribut pro uživatele. Toto výchozí nastavení může být má vliv, zabezpečení, posílení zabezpečení prostředí.

### <a name="configure-active-directory"></a>Konfigurace Active Directory
Konfigurace služby Active Directory se liší v závislosti na tom, zda váš konektor Proxy aplikací a aplikační servery jsou ve stejné doméně nebo ne.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Konektor a aplikačního serveru ve stejné doméně
1. Ve službě Active Directory, přejděte na **nástroje** > **uživatelé a počítače služby**.
2. Vyberte server se službou konektoru.
3. Klikněte pravým tlačítkem a vyberte **vlastnosti** > **delegování**.
4. Vyberte **důvěřovat tomuto počítači pro delegování pouze určeným službám**. 
5. V části **služby, ke kterým může tento účet předložit delegovaná pověření** přidejte hodnotu hlavního názvu služby identity aplikačního serveru. To umožňuje konektoru Proxy aplikace k zosobnění uživatelů ve službě AD aplikací definované v seznamu.

   ![Snímek obrazovky okna Vlastnosti Connector 1JÁDROVÉ](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Konektor a aplikační server v různých doménách
1. Seznam požadovaných součástí pro práci s KCD napříč doménami, najdete v části [omezeného delegování protokolu Kerberos napříč doménami](https://technet.microsoft.com/library/hh831477.aspx).
2. Použití `principalsallowedtodelegateto` vlastnost na serveru konektoru pro povolení Proxy aplikace pro delegování pro server konektoru. Aplikační server je `sharepointserviceaccount` a delegování server `connectormachineaccount`. Pro Windows 2012 R2 tento kód použijte jako příklad:

```powershell
$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
```

`sharepointserviceaccount` může být účet počítače aktualizace Service PACKU nebo účet služby, pod kterým běží fond aplikací aktualizace Service PACKU.

## <a name="configure-single-sign-on"></a>Konfigurace jednotného přihlašování 
1. Publikování aplikace podle pokynů v tématu [publikování aplikací pomocí Proxy aplikace](application-proxy-add-on-premises-application.md). Je nutné vybrat **Azure Active Directory** jako **metoda předběžného ověření**.
2. Poté, co vaše aplikace se zobrazí v seznamu podnikových aplikací, vyberte ho a klikněte na tlačítko **jednotného přihlašování**.
3. Nastavit režim jednotného přihlašování **integrované ověřování Windows**.  
4. Zadejte **vnitřní hlavní název služby aplikace** aplikačního serveru. V tomto příkladu je název SPN pro naše publikované aplikace http/www.contoso.com. Tento hlavní název služby musí být v seznamu služeb, ke kterým může tento konektor předložit delegovaná pověření. 
5. Zvolte **delegovaná identita přihlášení** pro konektor nekonfigurovali používání jménem uživatele. Další informace najdete v tématu [práce s různé místní a cloudové identity](#Working-with-different-on-premises-and-cloud-identities)

   ![Konfigurace pokročilých aplikace](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Jednotné přihlašování pro aplikace bez Windows

Tok delegování protokolu Kerberos v Azure AD Application Proxy spustí, když služba Azure AD ověřuje uživatele v cloudu. Jakmile dorazí požadavek v místním, vydá konektoru Proxy aplikací Azure AD lístek protokolu Kerberos jménem uživatele to interakcí se místní služby Active Directory. Tento proces se označuje jako Kerberos omezené delegování (KCD). V další fázi posílá požadavek back-end aplikace s Tento lístek protokolu Kerberos. 

Existuje několik protokolů, které definují, jak odesílat žádosti. Pro vyjednávání s SPNEGO očekávat, že většina jiných Windows serverů. Tento protokol je podporována v Azure AD Application Proxy, ale je ve výchozím nastavení zakázané. Server může být nakonfigurované pro SPNEGO nebo standardní KCD, ale ne obě možnosti současně.

Pokud konfigurujete konektor počítače pro SPNEGO, ujistěte se, že všechny konektory v dané skupině konektoru jsou také nakonfigurovanou SPNEGO. Aplikace očekává standardní KCD by ho směrovat pomocí jiných konektorů, které nejsou konfigurovány pro SPNEGO.
 

Pokud chcete povolit SPNEGO:

1. Otevřete příkazový řádek, který běží jako správce.
2. Z příkazového řádku spusťte následující příkazy na servery konektoru, které potřebují SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Další informace o protokolu Kerberos najdete v tématu [všechny potřebujete vědět o Kerberos omezené delegování (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Aplikace Windows bez obvykle uživatelská jména uživatele nebo názvy účtů SAM místo domény e-mailové adresy. Pokud se tato situace se vztahuje na aplikace, musíte nakonfigurovat pole identity delegované přihlašovací jméno pro připojení cloudové identity pro vaše aplikace identity. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Práce s různé místní a cloudové identity
Proxy aplikací se předpokládá, že uživatelé mají přesně stejnou identitu v cloudu i lokálně. Pokud to není tento případ, můžete stále použít KCD pro jednotné přihlašování. Konfigurace **delegovaná identita přihlášení** pro každou aplikaci, aby zadat, která identita se má použít při provádění jednotného přihlašování.  

Tato možnost umožňuje mnoho organizací, které mají různé místní a cloudové identity mít jednotné přihlašování z cloudu k místním aplikacím, aniž by uživatelé museli zadat různá uživatelská jména a hesla. Jedná se o organizace, který:

* Interně mají několik domén (joe@us.contoso.com, joe@eu.contoso.com) a jednu doménu v cloudu (joe@contoso.com).
* Mít název domény nesměrovatelných interně (joe@contoso.usa) a právní jeden v cloudu.
* Nepoužívejte názvy domén interně (Jan)
* Používat různé aliasy v místním prostředí i v cloudu. Například joe-johns@contoso.com vs. joej@contoso.com  

Pomocí Proxy aplikace můžete zvolit, která identita se má použít k získání lístku protokolu Kerberos. Toto nastavení je na aplikaci. Některé z těchto možností jsou vhodné pro systémy, které nepřijímá formát e-mailové adresy, ostatní jsou navrženy pro alternativní přihlašovací jméno.

![Snímek obrazovky parametr identity delegované přihlášení](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Pokud se používá delegovaná identita přihlášení, tato hodnota nemusí být jedinečný mezi doménami nebo doménovými strukturami ve vaší organizaci. Tento problém můžete vyhnout publikování těchto aplikací dvakrát pomocí dvou různých skupin konektoru. Protože každá aplikace má cílovou skupinu jiného uživatele, musíte připojit své konektory do jiné domény.

### <a name="configure-sso-for-different-identities"></a>Konfigurace jednotného přihlašování pro různé identity
1. Nakonfigurujte nastavení služby Azure AD Connect, abyste hlavní identita je e-mailovou adresu (e-mailu). To se provádí jako součást procesu vlastní tak, že změníte **hlavní název uživatele** v nastavení synchronizace. Tato nastavení také určují, jak se uživatelé přihlásí k Office 365, zařízení s Windows 10 a další aplikace, které používají službu Azure AD jako jejich úložiště identit.  
   ![Identifikace uživatele – snímek obrazovky – rozevírací seznam hlavní název uživatele](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. V nastavení konfigurace aplikace pro aplikaci, byste chtěli upravit, vyberte **delegovaná identita přihlášení** má být použit:

   * Hlavní název uživatele (například joe@contoso.com)
   * Alternativní hlavní název uživatele (například joed@contoso.local)
   * Uživatelské jméno součást hlavní název uživatele (například joe)
   * Uživatelské jméno část alternativní hlavní název uživatele (například joed)
   * Název účtu SAM místní (závisí na konfiguraci řadiče domény)

### <a name="troubleshooting-sso-for-different-identities"></a>Řešení potíží s jednotného přihlašování pro různé identity
Pokud dojde k chybě v procesu jednotného přihlašování, zobrazí se v protokolu událostí počítač konektor jak je vysvětleno v [Poradce při potížích s](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Ale v některých případech je požadavek úspěšně odeslán do back-end aplikace během této aplikace odpovědi v různých dalších odpovědi protokolu HTTP. Řešení potíží s těchto případech byste začít kontrolou číslo události 24029 na počítači se konektor Proxy aplikací protokol události relace. Identita uživatele, která byla použita pro delegování se zobrazí v poli "user" v rámci podrobnosti o události. Chcete-li zapnout relace protokolu, vyberte **zobrazit analytické a ladit protokoly** v nabídce Zobrazit prohlížeč událostí.

## <a name="next-steps"></a>Další postup

* [Jak nakonfigurovat aplikaci Proxy aplikací pro použití omezeného delegování protokolu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Řešení potíží s problémy, se kterými máte potíže s Proxy aplikací](application-proxy-troubleshoot.md)


Nejnovější novinky a aktualizace naleznete na [blogu proxy aplikace](https://blogs.technet.com/b/applicationproxyblog/)

