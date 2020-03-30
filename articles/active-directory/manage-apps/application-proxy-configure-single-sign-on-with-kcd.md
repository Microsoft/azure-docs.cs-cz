---
title: Jednotné přihlášení pomocí proxy aplikace | Dokumenty společnosti Microsoft
description: Popisuje, jak poskytnout jednotné přihlášení pomocí proxy aplikace Azure AD.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253452"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos Omezená delegování pro jednotné přihlašování k aplikacím pomocí proxy aplikace

Můžete poskytnout jednotné přihlášení pro místní aplikace publikované prostřednictvím proxy aplikace, které jsou zabezpečeny integrovaným ověřováním systému Windows. Tyto aplikace vyžadují lístek Kerberos pro přístup. Proxy aplikace používá k podpoře těchto aplikací delegování s omezeným protokolem Kerberos (KCD). 

Jednotné přihlašování k aplikacím pomocí integrovaného ověřování systému Windows (IWA) můžete povolit udělením oprávnění konektorů proxy aplikace ve službě Active Directory k vydávání uživatelů. Konektory používají toto oprávnění k odesílání a přijímání tokenů jejich jménem.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak funguje jednotné přihlašování pomocí KCD
Tento diagram vysvětluje tok, když se uživatel pokusí o přístup k místní aplikaci, která používá IWA.

![Diagram tok ověřování Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci prostřednictvím proxy aplikace.
2. Proxy aplikace přesměruje požadavek na ověřovací služby Azure AD k předběžnému ověření. V tomto okamžiku Azure AD použije všechny příslušné zásady ověřování a autorizace, jako je například vícefaktorové ověřování. Pokud je uživatel ověřen, Azure AD vytvoří token a odešle jej uživateli.
3. Uživatel předá token proxy aplikace.
4. Proxy aplikace ověří token a načte z něj hlavní jméno uživatele (UPN) a potom konektor vytáhne hlavní název hlavního názvu uživatele (SPN) prostřednictvím duálně ověřeného zabezpečeného kanálu.
5. Konektor provádí vyjednávání delegování s omezeným i protokolem Kerberos (KCD) s místním službou AD, zosobňující uživatele, aby získal token protokolu Kerberos do aplikace.
6. Služba Active Directory odešle token protokolu Kerberos pro aplikaci do konektoru.
7. Konektor odešle původní požadavek na aplikační server pomocí tokenu Kerberos, který obdržel ze služby AD.
8. Aplikace odešle odpověď konektoru, který je pak vrácen službě Proxy aplikace a nakonec uživateli.

## <a name="prerequisites"></a>Požadavky
Než začnete s jedním přihlášením pro aplikace IWA, ujistěte se, že je vaše prostředí připravené s následujícími nastaveními a konfiguracemi:

* Vaše aplikace, jako jsou sharepointové webové aplikace, jsou nastavené na integrované ověřování windows. Další informace najdete [v tématu Povolení podpory pro ověřování pomocí protokolu Kerberos](https://technet.microsoft.com/library/dd759186.aspx)nebo SharePoint u [tématu Plán ověřování kerberos v SharePointu 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Všechny vaše aplikace mají [hlavní názvy služeb](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Server se spuštěnou konektorem a server se spuštěnou aplikací jsou připojeny k doméně a jsou součástí stejné domény nebo důvěřujících domén. Další informace o připojení k doméně naleznete [v tématu Připojení počítače k doméně](https://technet.microsoft.com/library/dd807102.aspx).
* Server se spuštěnou konektorem má přístup ke čtení atributu TokenGroupsGlobalAndUniversal pro uživatele. Toto výchozí nastavení mohlo být ovlivněno posílením zabezpečení prostředí.

### <a name="configure-active-directory"></a>Konfigurace Active Directory
Konfigurace služby Active Directory se liší v závislosti na tom, zda se konektor proxy aplikace a aplikační server nacházejí ve stejné doméně či nikoli.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Konektor a aplikační server ve stejné doméně
1. Ve službě Active Directory přejděte na **nástroj** > **uživatelé a počítače**.
2. Vyberte server se spuštěnou konektorem.
3. Klepněte pravým tlačítkem myši a vyberte**delegování** **vlastností** > .
4. Vyberte **Důvěřovat tomuto počítači pro delegování pouze určeným službám**. 
5. Vyberte možnost pro **použití libovolného protokolu pro ověřování**.
6. V části **Služby, do kterých může tento účet prezentovat delegovaná pověření,** přidejte hodnotu identity hlavního názvového centra aplikačního serveru. To umožňuje konektoru proxy aplikace zosobnit uživatele ve službě AD proti aplikacím definovaným v seznamu.

   ![Snímek obrazovky okna Vlastnosti konektoru SVR](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Konektor a aplikační server v různých doménách
1. Seznam předpokladů pro práci s kcd napříč doménami naleznete v [tématu Kerberos Constrained Delegation across domains](https://technet.microsoft.com/library/hh831477.aspx).
2. Pomocí `principalsallowedtodelegateto` vlastnosti účtu služby (počítače nebo vyhrazeného uživatelského účtu domény) webové aplikace povolte delegování ověřování protokolem Kerberos z proxy aplikace (konektoru). Aplikační server je spuštěn v `webserviceaccount` kontextu a delegující `connectorcomputeraccount`server je . Spusťte níže uvedené příkazy na řadiči domény (se systémem Windows Server `webserviceaccount`2012 R2 nebo novějším) v doméně aplikace . Pro oba účty použijte ploché názvy (jiné než upn).

   Pokud `webserviceaccount` se jedná o účet počítače, použijte tyto příkazy:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Pokud `webserviceaccount` se jedná o uživatelský účet, použijte tyto příkazy:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Konfigurace jednotného přihlašování 
1. Publikujte svou žádost podle pokynů popsaných v části [Publikovat aplikace pomocí proxy aplikace](application-proxy-add-on-premises-application.md). Nezapomeňte vybrat **službu Azure Active Directory** jako **metodu předběžného ověřování**.
2. Po zobrazení aplikace v seznamu podnikových aplikací ji vyberte a klepněte **na položku Jednotné přihlášení**.
3. Nastavte režim jednotného přihlašování na **integrované ověřování systému Windows**.  
4. Zadejte hlavní název **služby interní aplikace** aplikačního serveru. V tomto příkladu je hlavní název služby pro naši publikovanou aplikaci http/www.contoso.com. Tento hlavní seznam spn musí být v seznamu služeb, ke kterým může konektor prezentovat delegovaná pověření. 
5. Zvolte **delegované přihlašovací identity** pro konektor použít jménem vašich uživatelů. Další informace najdete [v tématu Práce s různými místními a cloudovými identitami.](#working-with-different-on-premises-and-cloud-identities)

   ![Pokročilá konfigurace aplikací](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Připojetí k alekům pro aplikace, které nejsou pro Windows

Tok delegování protokolu Kerberos v proxy aplikace Azure AD se spustí, když Azure AD ověří uživatele v cloudu. Jakmile žádost dorazí místně, konektor proxy aplikace Azure AD vydá lístek protokolu Kerberos jménem uživatele interakcí s místní službou Active Directory. Tento proces se označuje jako kerberos omezené delegování (KCD). V další fázi je požadavek odeslán do back-endové aplikace s tímto lístkem protokolu Kerberos. 

Existuje několik protokolů, které definují, jak tyto požadavky odeslat. Většina serverů, které nejsou systémy Windows, očekává, že budou vyjednávat s SPNEGO. Tento protokol je podporován v proxy aplikace Azure AD, ale ve výchozím nastavení je zakázán. Server lze nakonfigurovat pro SPNEGO nebo standardní KCD, ale ne obojí.

Pokud nakonfigurujete počítač s konektory pro SPNEGO, ujistěte se, že všechny ostatní konektory v této skupině konektorů jsou také konfigurovány s SPNEGO. Aplikace, které očekávají standardní kcd, by měly být směrovány přes jiné konektory, které nejsou nakonfigurovány pro SPNEGO.
 

Chcete-li povolit SPNEGO:

1. Otevřete příkazový řádek, který běží jako správce.
2. Na příkazovém řádku spusťte následující příkazy na serverech konektoru, které potřebují SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Další informace o protokolu Kerberos naleznete [v tématu Vše, co chcete vědět o delegování omezeného protokolu Kerberos (KCD).](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)

Aplikace, které nejsou součástí Windows, obvykle používají uživatelská jména nebo názvy účtů SAM namísto e-mailových adres domény. Pokud se tato situace týká vašich aplikací, musíte nakonfigurovat delegované pole přihlašovací identity pro připojení cloudových identit k vašim identitám aplikace. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Práce s různými místními a cloudovými identitami
Proxy aplikace předpokládá, že uživatelé mají přesně stejnou identitu v cloudu a místně. Ale v některých prostředích, z důvodu podnikových zásad nebo závislostí aplikací, organizace mohou mít použít alternativní ID pro přihlášení. V takových případech můžete stále používat KCD pro jednotné přihlašování. Nakonfigurujte **delegovanou přihlašovací identitu** pro každou aplikaci a určete, která identita má být použita při jednotném přihlašování.  

Tato funkce umožňuje mnoha organizacím, které mají různé místní a cloudové identity mít s jednotnou připojování z cloudu na místní aplikace bez nutnosti uživatelé zadávat různá uživatelská jména a hesla. To zahrnuje organizace, které:

* Mít více domén interně (joe@us.contoso.com, joe@eu.contoso.com) ajoe@contoso.comjednu doménu v cloudu ( ).
* Mít nesměrovatelný název doményjoe@contoso.usainterně ( ) a právní v cloudu.
* Nepoužívejte názvy domén interně (joe)
* Používejte různé aliasy v místním prostředí a v cloudu. Například joe-johns@contoso.com vs.joej@contoso.com  

Pomocí proxy aplikace můžete vybrat identitu, kterou chcete použít k získání lístku protokolu Kerberos. Toto nastavení je pro všechny aplikace. Některé z těchto možností jsou vhodné pro systémy, které nepřijímají formát e-mailové adresy, jiné jsou určeny pro alternativní přihlášení.

![Snímek obrazovky s parametrem delegované přihlašovací identity](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Pokud se používá delegovaná přihlašovací identita, hodnota nemusí být jedinečná ve všech doménách nebo doménových strukturách ve vaší organizaci. Tomuto problému se můžete vyhnout tak, že tyto aplikace publikujete dvakrát pomocí dvou různých skupin konektorů. Vzhledem k tomu, že každá aplikace má jinou cílovou skupinu uživatelů, můžete připojit její konektory k jiné doméně.

### <a name="configure-sso-for-different-identities"></a>Konfigurace automatického připojetí pro různé identity
1. Nakonfigurujte nastavení Azure AD Connect tak, aby hlavní identitou byla e-mailová adresa (pošta). To se provádí jako součást procesu přizpůsobení změnou pole **Hlavní jméno uživatele** v nastavení synchronizace. Tato nastavení také určují, jak se uživatelé přihlašují k Office365, zařízením s Windows10 a dalším aplikacím, které používají Azure AD jako úložiště identit.  
   ![Identifikace uživatelů snímek obrazovky – rozevírací soubor Hlavní jméno uživatele](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. V nastavení konfigurace aplikace pro aplikaci, kterou chcete upravit, vyberte **delegovanou přihlašovací identitu,** která se má použít:

   * Hlavní jméno uživatele (například joe@contoso.com)
   * Alternativní uživatelské hlavní jméno joed@contoso.local(například )
   * Uživatelské jméno část uživatelského hlavního jména (například joe)
   * Uživatelské jméno část alternativní uživatelské hlavní jméno (například joed)
   * Název místního účtu SAM (závisí na konfiguraci řadiče domény)

### <a name="troubleshooting-sso-for-different-identities"></a>Poradce při potížích s přizpůsobuje služby ASO pro různé identity
Pokud dojde k chybě v procesu služby SSO, zobrazí se v protokolu událostí počítače konektoru, jak je [vysvětleno](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)v řešení potíží .
Ale v některých případech je požadavek úspěšně odeslán do back-endové aplikace, zatímco tato aplikace odpovídá v různých jiných odpovědích HTTP. Řešení těchto případů by mělo začít zkoumáním události číslo 24029 v počítači konektoru v protokolu událostí relace proxy aplikace. Identita uživatele, která byla použita pro delegování, se zobrazí v poli "uživatel" v podrobnostech události. Chcete-li protokol relací zapnout, vyberte možnost **Zobrazit analytické a ladicí protokoly** v nabídce zobrazení prohlížeče událostí.

## <a name="next-steps"></a>Další kroky

* [Konfigurace aplikace proxy aplikace pro použití delegování s omezeným postupem kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Řešení potíží s proxy aplikace](application-proxy-troubleshoot.md)


Nejnovější novinky a aktualizace naleznete na [blogu proxy aplikace](https://blogs.technet.com/b/applicationproxyblog/)
