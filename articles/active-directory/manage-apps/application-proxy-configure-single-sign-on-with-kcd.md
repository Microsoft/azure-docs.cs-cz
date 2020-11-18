---
title: Jednotné přihlašování (SSO) založené na protokolu Kerberos v Azure Active Directory s využitím proxy aplikací
description: Popisuje, jak zajistit jednotné přihlašování pomocí Proxy aplikací služby Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: e43ad9dedf4212e9b30a08f0c978cb8d1a86776c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657410"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>Omezené delegování protokolu Kerberos pro jednotné přihlašování (SSO) k vašim aplikacím pomocí proxy aplikací

Můžete zajistit jednotné přihlašování pro místní aplikace publikované prostřednictvím proxy aplikací, které jsou zabezpečené pomocí integrovaného ověřování systému Windows. Tyto aplikace vyžadují pro přístup lístek protokolu Kerberos. Proxy aplikace používá k podpoře těchto aplikací vynucené delegování protokolu Kerberos (KCD). 

Můžete povolit jednotné přihlašování k aplikacím pomocí integrovaného ověřování systému Windows (IWA) tím, že ve službě Active Directory udělíte oprávnění konektor proxy aplikací k zosobnění uživatelů. Konektory používají toto oprávnění k posílání a přijímání tokenů jménem.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak jednotné přihlašování pomocí KCD funguje
Tento diagram vysvětluje tok, když se uživatel pokusí o přístup k místní aplikaci, která používá IWA.

![Diagram toku ověřování Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci prostřednictvím proxy aplikace.
2. Proxy aplikace přesměruje požadavek na služby ověřování Azure AD na předběžné ověření. V tomto okamžiku Azure AD aplikuje jakékoli použitelné zásady ověřování a autorizace, jako je například vícefaktorové ověřování. Pokud je uživatel ověřený, Azure AD vytvoří token a odešle ho uživateli.
3. Uživatel předává token do proxy aplikace.
4. Proxy aplikace ověří token a načte z něj hlavní název uživatele (UPN) a pak konektor vyžádá hlavní název uživatele (UPN) a hlavní název služby (SPN) prostřednictvím dvojího ověřeného zabezpečeného kanálu.
5. Konektor provádí vyjednávání omezeného delegování protokolu Kerberos (KCD) s místní službou AD a zosobňuje uživatele k získání tokenu protokolu Kerberos do aplikace.
6. Služba Active Directory odesílá token protokolu Kerberos pro aplikaci konektoru.
7. Konektor odešle původní požadavek na aplikační server pomocí tokenu protokolu Kerberos, který přijal ze služby AD.
8. Aplikace pošle odpověď konektoru, která se pak vrátí službě proxy aplikací a nakonec uživateli.

## <a name="prerequisites"></a>Požadavky
Než začnete s jednotným přihlašováním pro aplikace IWA, ujistěte se, že je vaše prostředí připravené s následujícími nastaveními a konfiguracemi:

* Vaše aplikace, jako jsou webové aplikace SharePoint, jsou nastavené na používání integrovaného ověřování systému Windows. Další informace najdete v tématu [Povolení podpory pro ověřování protokolem Kerberos](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759186(v=ws.11))nebo pro SharePoint v tématu [Plánování ověřování protokolem Kerberos v SharePointu 2013](/SharePoint/security-for-sharepoint-server/kerberos-authentication-planning).
* Všechny vaše aplikace mají [hlavní názvy služeb](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Server, na kterém je spuštěný konektor, a server, na kterém je spuštěná aplikace, jsou připojené k doméně a součástí stejné domény nebo důvěřujících domén. Další informace o připojení k doméně najdete v tématu [připojení počítače k doméně](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807102(v=ws.11)).
* Server, na kterém je spuštěn konektor, má přístup ke čtení atributu TokenGroupsGlobalAndUniversal pro uživatele. Toto výchozí nastavení může být ovlivněné zabezpečením v prostředí.

### <a name="configure-active-directory"></a>Konfigurace Active Directory
Konfigurace služby Active Directory se liší v závislosti na tom, jestli je konektor proxy aplikací a aplikační server ve stejné doméně, nebo ne.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Konektor a aplikační server ve stejné doméně
1. Ve službě Active Directory přejdete na **nástroje**  >  **Uživatelé a počítače**.
2. Vyberte server, na kterém je spuštěný konektor.
3. Klikněte pravým tlačítkem a vyberte **vlastnosti**  >  **delegování**.
4. Vyberte **Důvěřovat tomuto počítači pro delegování pouze určeným službám**. 
5. Vyberte možnost pro **použití libovolného protokolu pro ověřování**.
6. V části **služby, kterým může tento účet prezentovat delegovaná pověření** přidejte hodnotu pro identitu hlavního názvu služby aplikačního serveru. To umožňuje konektoru proxy aplikací zosobnit uživatele v rámci služby AD proti aplikacím definovaným v seznamu.

   ![Konektor – počítače okno Vlastnosti snímek obrazovky](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Konektor a aplikační server v různých doménách
1. Seznam požadavků pro práci s KCD napříč doménami najdete v tématu [omezené delegování protokolu Kerberos napříč doménami](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831477(v=ws.11)).
2. `principalsallowedtodelegateto`Pro povolení delegování ověřování protokolem Kerberos z proxy aplikace (konektor) použijte vlastnost účtu služby (počítač nebo vyhrazený uživatelský účet) webové aplikace. Aplikační server běží v kontextu `webserviceaccount` a delegování serveru je `connectorcomputeraccount` . Spusťte níže uvedené příkazy na řadiči domény (se spuštěným systémem Windows Server 2012 R2 nebo novějším) v doméně nástroje `webserviceaccount` . Pro oba účty použijte ploché názvy (jiné než UPN).

   Pokud `webserviceaccount` je účet počítače, použijte tyto příkazy:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Pokud `webserviceaccount` je to uživatelský účet, použijte tyto příkazy:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Konfigurace jednotného přihlašování 
1. Publikujte aplikaci podle pokynů popsaných v tématu [publikování aplikací pomocí proxy aplikací](application-proxy-add-on-premises-application.md). Nezapomeňte vybrat **Azure Active Directory** jako **metodu předběžného ověření**.
2. Jakmile se vaše aplikace objeví v seznamu podnikových aplikací, vyberte ji a klikněte na **jednotné přihlašování**.
3. Nastavte režim jednotného přihlašování na **integrované ověřování systému Windows**.  
4. Zadejte **interní hlavní název aplikace** aplikačního serveru. V tomto příkladu je hlavní název služby (SPN) pro naši publikovanou aplikaci http/www. contoso. com. Tento název SPN musí být v seznamu služeb, ke kterým může konektor prezentovat delegovaná pověření. 
5. Vyberte **Delegovanou identitu přihlášení**, kterou má konektor používat jménem uživatelů. Další informace najdete v tématu [práce s různými místními a cloudovou identitou](#working-with-different-on-premises-and-cloud-identities) .

   ![Rozšířená konfigurace aplikace](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>Jednotné přihlašování pro aplikace, které nejsou v systému Windows

Tok delegování protokolu Kerberos ve službě Azure Proxy aplikací služby AD se spustí, když Azure AD ověří uživatele v cloudu. Jakmile požadavek dorazí do místního prostředí, konektor Azure Proxy aplikací služby AD vydá jménem uživatele lístek protokolu Kerberos, který bude spolupracovat s místní službou Active Directory. Tento proces se označuje jako omezené delegování pomocí protokolu Kerberos (KCD). 

V další fázi se do aplikace back-end pošle požadavek pomocí tohoto lístku Kerberos. 

Existuje několik mechanismů definujících způsob odeslání lístku protokolu Kerberos v takových požadavcích. Většina serverů s jiným systémem než Windows očekává, že ji obdrží ve formě tokenu SPNEGO. Tento mechanismus je podporovaný v Azure Proxy aplikací služby AD, ale ve výchozím nastavení je zakázaný. Konektor se dá nakonfigurovat pro SPNEGO nebo standardní token protokolu Kerberos, ale ne pro obojí.

Pokud nakonfigurujete počítač konektoru pro SPNEGO, ujistěte se, že jsou všechny ostatní konektory v této skupině konektorů nakonfigurované také pomocí nástroje SPNEGO. Aplikace, které očekávají standardní token protokolu Kerberos, by se měly směrovat přes jiné konektory, které nejsou nakonfigurované pro SPNEGO. Některé webové aplikace přijímají oba formáty bez nutnosti změny v konfiguraci. 
 

Povolení SPNEGO:

1. Otevřete příkazový řádek, který se spouští jako správce.
2. Na příkazovém řádku spusťte na serverech konektoru následující příkazy, které vyžadují SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Aplikace pro jiné než Windows jsou obvykle uživatelská jména nebo názvy účtů SAM místo e-mailových adres domény. Pokud se tato situace vztahuje na vaše aplikace, musíte nakonfigurovat pole delegovaná identita přihlášení, aby se připojily vaše cloudové identity k identitám vaší aplikace. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Práce s různými místními a cloudovou identitou
Proxy aplikací předpokládá, že uživatelé mají přesně stejnou identitu v cloudu i v místním prostředí. V některých prostředích se ale v důsledku podnikových zásad nebo závislostí aplikací můžou organizace pro přihlášení použít alternativní ID. V takových případech můžete i nadále používat KCD pro jednotné přihlašování. Nakonfigurujte **identitu delegovaného přihlášení** pro každou aplikaci a určete, která identita se má použít při jednotném přihlašování.  

Tato možnost umožňuje mnoha organizacím, které mají různé místní i cloudové identity, přihlašování pomocí jednotného přihlašování z cloudu k místním aplikacím, aniž by uživatelé museli zadávat jiná uživatelská jména a hesla. To zahrnuje organizace, které:

* Mít více domén interně ( joe@us.contoso.com , joe@eu.contoso.com ) a jednu doménu v cloudu ( joe@contoso.com ).
* Název domény, který není směrovatelný, interně ( joe@contoso.usa ) a v cloudu musí být jiný.
* Nepoužívejte názvy domén interně (Jana)
* Používejte jiné aliasy místně a v cloudu. Například joe-johns@contoso.com vs. joej@contoso.com  

Pomocí proxy aplikací můžete vybrat identitu, kterou chcete použít k získání lístku protokolu Kerberos. Toto nastavení je na aplikaci. Některé z těchto možností jsou vhodné pro systémy, které nepodporují formát e-mailové adresy. jiné jsou navržené pro alternativní přihlášení.

![Snímek pro delegovaný přihlašovací parametr identity](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Pokud se použije delegovaná identita přihlášení, hodnota nemusí být jedinečná napříč všemi doménami nebo doménovými strukturami ve vaší organizaci. Tomuto problému se můžete vyhnout tak, že tyto aplikace publikujete dvakrát pomocí dvou různých skupin konektorů. Vzhledem k tomu, že každá aplikace má jinou cílovou skupinu uživatelů, můžete připojit své konektory k jiné doméně.

Pokud se pro identitu přihlášení používá **místní název účtu SAM** , musí se počítač, který je hostitelem konektoru, přidat do domény, ve které se nachází uživatelský účet.

### <a name="configure-sso-for-different-identities"></a>Konfigurace jednotného přihlašování pro různé identity
1. Nakonfigurujte nastavení Azure AD Connect tak, aby byla hlavní identitou e-mailová adresa (e-mail). Tato operace se provádí v rámci procesu přizpůsobení změnou pole **hlavní název uživatele** v nastavení synchronizace. Tato nastavení také určují, jak se uživatelé přihlašují k Office 365, Windows10 zařízením a dalším aplikacím, které používají Azure AD jako úložiště identit.  
   ![Snímek obrazovky s identifikací uživatelů – rozevírací seznam hlavního názvu uživatele](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. V nastavení konfigurace aplikace pro aplikaci, kterou chcete upravit, vyberte **identitu delegovaného přihlášení** , která se má použít:

   * Hlavní název uživatele (například joe@contoso.com )
   * Alternativní hlavní název uživatele (například joed@contoso.local )
   * Část hlavního názvu uživatele (například Jana) s uživatelským jménem
   * Část uživatelského jména alternativního hlavního názvu uživatele (například joed)
   * Místní název účtu SAM (závisí na konfiguraci řadiče domény)

### <a name="troubleshooting-sso-for-different-identities"></a>Řešení potíží s SSO pro různé identity
Pokud v procesu jednotného přihlašování dojde k chybě, zobrazí se v protokolu událostí konektorového počítače, jak je vysvětleno v tématu [řešení potíží](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
V některých případech se ale požadavek úspěšně pošle do back-endu aplikace, zatímco tato aplikace reaguje v různých dalších odpovědích HTTP. Řešení potíží s těmito případy by mělo začít kontrolou čísla události 24029 na počítači konektoru v protokolu událostí relace proxy aplikace. Identita uživatele, která se použila pro delegování, se zobrazí v poli "uživatel" v podrobnostech události. Pokud chcete zapnout protokol relace, vyberte **Zobrazit protokoly analýzy a ladění** v nabídce zobrazení prohlížeče událostí.

## <a name="next-steps"></a>Další kroky

* [Postup konfigurace aplikace proxy aplikací pro použití omezeného delegování protokolu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Řešení potíží s proxy aplikace](application-proxy-troubleshoot.md)