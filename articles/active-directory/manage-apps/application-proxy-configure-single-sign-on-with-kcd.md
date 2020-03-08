---
title: Jednotné přihlašování s Proxy aplikací | Dokumentace Microsoftu
description: Popisuje, jak poskytovat jednotné přihlašování pomocí Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5948fba67d3f071d77192f9ad89bc696fdc0c3cc
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669046"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Omezené delegování protokolu Kerberos pro jednotné přihlašování do aplikací pomocí Proxy aplikace

Můžete poskytnout jednotné přihlašování pro místní aplikace publikované prostřednictvím Proxy aplikací, které jsou zabezpečené pomocí integrované ověřování Windows. Tyto aplikace vyžadují lístek protokolu Kerberos pro přístup. Proxy aplikací používá protokol Kerberos omezené delegování (KCD) pro podporu těchto aplikací. 

Můžete povolit jednotné přihlašování pro vaše aplikace pomocí integrovaného ověřování Windows (IWA) tak, že udělíte oprávnění konektory Proxy aplikací ve službě Active Directory zosobňovalo. Konektory použít toto oprávnění odesílat a přijímat tokeny jejich jménem.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak jednotné přihlašování s KCD funguje
Tento diagram vysvětluje tok, když se uživatel pokusí o přístup k místní aplikaci, která používá IWA.

![Vývojový diagram ověřování Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci prostřednictvím proxy aplikace.
2. Proxy aplikace přesměruje požadavek preauthenticate ověřování služby Azure AD. V tuto chvíli Azure AD použije všechny příslušné ověřování a zásady autorizace, jako je například vícefaktorové ověřování. Pokud uživatel je ověřen, Azure AD vytvoří token a odešle se uživateli.
3. Uživatel se předá token Proxy aplikací.
4. Proxy aplikace ověří token a načte z něj hlavní název uživatele (UPN) a pak konektor vyžádá hlavní název uživatele (UPN) a hlavní název služby (SPN) prostřednictvím dvojího ověřeného zabezpečeného kanálu.
5. Konektor provádí vyjednávání omezeného delegování protokolu Kerberos (KCD) s místní službou AD a zosobňuje uživatele k získání tokenu protokolu Kerberos do aplikace.
6. Služby Active Directory odešle token protokolu Kerberos pro použití konektoru.
7. Konektor odesílá původní požadavek na server aplikace pomocí token protokolu Kerberos, které získala ze služby AD.
8. Aplikace odešle odpověď na konektoru, který je pak vrácen do Proxy aplikace služby a nakonec na uživatele.

## <a name="prerequisites"></a>Předpoklady
Před zahájením práce s jednotné přihlašování pro aplikace IWA, ujistěte se, že je prostředí připravené s následující nastavení a konfigurace:

* Vaše aplikace, jako je SharePoint Web apps, jsou nastaveny na používat integrované ověřování Windows. Další informace najdete v tématu [Povolení podpory pro ověřování protokolem Kerberos](https://technet.microsoft.com/library/dd759186.aspx)nebo pro SharePoint v tématu [Plánování ověřování protokolem Kerberos v SharePointu 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Všechny vaše aplikace mají [hlavní názvy služeb](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Server se službou konektoru a server se službou aplikace jsou připojené k doméně a součástí stejné doméně nebo důvěryhodné domény. Další informace o připojení k doméně najdete v tématu [připojení počítače k doméně](https://technet.microsoft.com/library/dd807102.aspx).
* Server se službou konektoru má přístup k číst TokenGroupsGlobalAndUniversal atribut pro uživatele. Toto výchozí nastavení může být má vliv, zabezpečení, posílení zabezpečení prostředí.

### <a name="configure-active-directory"></a>Konfigurace Active Directory
Konfigurace služby Active Directory se liší v závislosti na tom, zda váš konektor Proxy aplikací a aplikační servery jsou ve stejné doméně nebo ne.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Konektor a aplikačního serveru ve stejné doméně
1. Ve službě Active Directory přejdete na **nástroje** > **Uživatelé a počítače**.
2. Vyberte server se službou konektoru.
3. Klikněte pravým tlačítkem a vyberte **vlastnosti** > **delegování**.
4. Vyberte **Důvěřovat tomuto počítači pro delegování pouze určeným službám**. 
5. Vyberte možnost **použít libovolný protokol pro ověřování**.
6. V části **služby, kterým může tento účet prezentovat delegovaná pověření** přidejte hodnotu pro identitu hlavního názvu služby aplikačního serveru. To umožňuje konektoru Proxy aplikace k zosobnění uživatelů ve službě AD aplikací definované v seznamu.

   ![Snímek obrazovky okna Vlastnosti Connector 1JÁDROVÉ](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Konektor a aplikační server v různých doménách
1. Seznam požadavků pro práci s KCD napříč doménami najdete v tématu [omezené delegování protokolu Kerberos napříč doménami](https://technet.microsoft.com/library/hh831477.aspx).
2. Pro povolení delegování ověřování protokolem Kerberos z proxy aplikace (konektor) použijte vlastnost `principalsallowedtodelegateto` účtu služby (počítač nebo vyhrazený uživatelský účet domény) webové aplikace. Aplikační server je spuštěný v kontextu `webserviceaccount` a delegování serveru je `connectorcomputeraccount`. Spusťte níže uvedené příkazy na řadiči domény (se spuštěným systémem Windows Server 2012 R2 nebo novějším) v doméně `webserviceaccount`. Pro oba účty použijte ploché názvy (jiné než UPN).

   Pokud je `webserviceaccount` účet počítače, použijte tyto příkazy:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Pokud je `webserviceaccount` uživatelským účtem, použijte tyto příkazy:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Konfigurace jednotného přihlašování 
1. Publikujte aplikaci podle pokynů popsaných v tématu [publikování aplikací pomocí proxy aplikací](application-proxy-add-on-premises-application.md). Nezapomeňte vybrat **Azure Active Directory** jako **metodu předběžného ověření**.
2. Jakmile se vaše aplikace objeví v seznamu podnikových aplikací, vyberte ji a klikněte na **jednotné přihlašování**.
3. Nastavte režim jednotného přihlašování na **integrované ověřování systému Windows**.  
4. Zadejte **interní hlavní název aplikace** aplikačního serveru. V tomto příkladu je název SPN pro naše publikované aplikace http/www.contoso.com. Tento hlavní název služby musí být v seznamu služeb, ke kterým může tento konektor předložit delegovaná pověření. 
5. Vyberte **delegovanou identitu přihlášení** , kterou má konektor použít jménem uživatelů. Další informace najdete v tématu [práce s různými místními a cloudovou identitou](#working-with-different-on-premises-and-cloud-identities) .

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

Další informace o protokolu Kerberos najdete v tématu [vše, co chcete dozvědět o omezeném delegování protokolu Kerberos (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Aplikace Windows bez obvykle uživatelská jména uživatele nebo názvy účtů SAM místo domény e-mailové adresy. Pokud se tato situace se vztahuje na aplikace, musíte nakonfigurovat pole identity delegované přihlašovací jméno pro připojení cloudové identity pro vaše aplikace identity. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Práce s různé místní a cloudové identity
Proxy aplikací se předpokládá, že uživatelé mají přesně stejnou identitu v cloudu i lokálně. V některých prostředích se ale v důsledku podnikových zásad nebo závislostí aplikací můžou organizace pro přihlášení použít alternativní ID. V takových případech můžete i nadále používat KCD pro jednotné přihlašování. Nakonfigurujte **identitu delegovaného přihlášení** pro každou aplikaci a určete, která identita se má použít při jednotném přihlašování.  

Tato možnost umožňuje mnoho organizací, které mají různé místní a cloudové identity mít jednotné přihlašování z cloudu k místním aplikacím, aniž by uživatelé museli zadat různá uživatelská jména a hesla. Jedná se o organizace, který:

* Více domén interně (joe@us.contoso.com, joe@eu.contoso.com) a jedna doména v cloudu (joe@contoso.com).
* Název domény, který není směrovatelný, interně (joe@contoso.usa) a v cloudu musí být jiný.
* Nepoužívejte názvy domén interně (Jan)
* Používejte jiné aliasy místně a v cloudu. Například joe-johns@contoso.com vs. joej@contoso.com  

Pomocí Proxy aplikace můžete zvolit, která identita se má použít k získání lístku protokolu Kerberos. Toto nastavení je na aplikaci. Některé z těchto možností jsou vhodné pro systémy, které nepřijímá formát e-mailové adresy, ostatní jsou navrženy pro alternativní přihlašovací jméno.

![Snímek obrazovky parametr identity delegované přihlášení](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Pokud se používá delegovaná identita přihlášení, tato hodnota nemusí být jedinečný mezi doménami nebo doménovými strukturami ve vaší organizaci. Tento problém můžete vyhnout publikování těchto aplikací dvakrát pomocí dvou různých skupin konektoru. Protože každá aplikace má cílovou skupinu jiného uživatele, musíte připojit své konektory do jiné domény.

### <a name="configure-sso-for-different-identities"></a>Konfigurace jednotného přihlašování pro různé identity
1. Nakonfigurujte nastavení služby Azure AD Connect, abyste hlavní identita je e-mailovou adresu (e-mailu). Tato operace se provádí v rámci procesu přizpůsobení změnou pole **hlavní název uživatele** v nastavení synchronizace. Tato nastavení také určují, jak se uživatelé přihlásí k Office 365, zařízení s Windows 10 a další aplikace, které používají službu Azure AD jako jejich úložiště identit.  
   ![určení obrazovky uživatele – rozevírací seznam hlavní název uživatele](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. V nastavení konfigurace aplikace pro aplikaci, kterou chcete upravit, vyberte **identitu delegovaného přihlášení** , která se má použít:

   * Hlavní název uživatele (například joe@contoso.com)
   * Alternativní hlavní název uživatele (například joed@contoso.local)
   * Uživatelské jméno součást hlavní název uživatele (například joe)
   * Uživatelské jméno část alternativní hlavní název uživatele (například joed)
   * Název účtu SAM místní (závisí na konfiguraci řadiče domény)

### <a name="troubleshooting-sso-for-different-identities"></a>Řešení potíží s jednotného přihlašování pro různé identity
Pokud v procesu jednotného přihlašování dojde k chybě, zobrazí se v protokolu událostí konektorového počítače, jak je vysvětleno v tématu [řešení potíží](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Ale v některých případech je požadavek úspěšně odeslán do back-end aplikace během této aplikace odpovědi v různých dalších odpovědi protokolu HTTP. Řešení potíží s těchto případech byste začít kontrolou číslo události 24029 na počítači se konektor Proxy aplikací protokol události relace. Identita uživatele, která byla použita pro delegování se zobrazí v poli "user" v rámci podrobnosti o události. Pokud chcete zapnout protokol relace, vyberte **Zobrazit protokoly analýzy a ladění** v nabídce zobrazení prohlížeče událostí.

## <a name="next-steps"></a>Další kroky

* [Postup konfigurace aplikace proxy aplikací pro použití omezeného delegování protokolu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Řešení problémů, které máte s proxy aplikací](application-proxy-troubleshoot.md)


Nejnovější novinky a aktualizace naleznete na [blogu proxy aplikace](https://blogs.technet.com/b/applicationproxyblog/)
