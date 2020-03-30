---
title: Saml jednotné přihlašování - aplikace bez galerie - Platforma identit Microsoft | Dokumenty společnosti Microsoft
description: Konfigurace jednotného přihlašování (SSO) pro aplikace bez galerie v platformě identit Microsoftu (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244209"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Konfigurace jednotného přihlašování saml do aplikací bez galerie

Když do aplikací Azure AD Enterprise [připřidáte aplikaci galerie](add-gallery-app.md) nebo [webovou aplikaci,](add-non-gallery-app.md) která není galerie, je jednou z možností jednotného přihlášení, které máte k dispozici, [jednotné přihlašování na základě SAML](what-is-single-sign-on.md#saml-sso). Zvolte SAML, kdykoli je to možné pro aplikace, které se ověřují pomocí jednoho z protokolů SAML. S saml jednotné přihlašování Azure AD ověřuje k aplikaci pomocí účtu Azure AD uživatele. Azure AD sděluje přihlašovací informace do aplikace prostřednictvím protokolu připojení. Uživatele můžete mapovat na konkrétní role aplikace na základě pravidel, která definujete v deklaracích SAML. Tento článek popisuje, jak nakonfigurovat jednotné přihlašování na saml pro aplikaci bez galerie. 

> [!NOTE]
> Přidávání aplikace v galerii? Podrobné pokyny k nastavení najdete v [seznamu výukových programů pro aplikace SaaS](../saas-apps/tutorial-list.md)

Chcete-li nakonfigurovat jednotné přihlašování SAML pro aplikaci bez galerie bez psaní kódu, musíte mít předplatné spolu s licencí Azure AD Premium a aplikace musí podporovat SAML 2.0. Další informace o verzích Azure AD najdete na webu [stanovení cen Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Než začnete

Pokud aplikace nebyla přidána do vašeho klienta Azure AD, přečtěte si další informace [o přidání aplikace bez galerie](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Krok 1. Úprava základní konfigurace SAML

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce cloudových aplikací nebo správce aplikace pro vašeho klienta Azure AD.

2. Přejděte do aplikací **Azure Active Directory** > **Enterprise** a vyberte ji ze seznamu. 
   
   - Chcete-li aplikaci vyhledat, vyberte v nabídce **Typ aplikace** **všechny aplikace**a pak vyberte **Použít**. Do vyhledávacího pole zadejte název aplikace a vyberte ji z výsledků.

3. V části **Správa** vyberte **Jednotné přihlašování**. 

4. Vyberte **možnost SAML**. Zobrazí se stránka **Nastavit jednotné přihlašování pomocí saml - preview.**

   ![Krok 1 Úprava základní konfigurace SAML](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Chcete-li upravit základní možnosti konfigurace SAML, vyberte ikonu **Upravit** (tužku) v pravém horním rohu oddílu **Základní konfigurace SAML.**

1. Zadejte následující nastavení. Měli byste získat hodnoty od dodavatele aplikace. Můžete ručně zadat hodnoty nebo nahrát soubor metadat a extrahovat hodnotu polí.

    | Základní nastavení konfigurace SAML | Iniciováno zprostředkovatelem přihlašování | Iniciováno pomocí IdP | Popis |
    |:--|:--|:--|:--|
    | **Identifikátor (ID entity)** | Vyžaduje se pro některé aplikace | Vyžaduje se pro některé aplikace | Jednoznačně identifikuje aplikaci. Azure AD odešle identifikátor do aplikace jako parametr cílové skupiny tokenu SAML. Očekává se, že aplikace ji ověří. Tato hodnota se také zobrazuje jako ID entity ve všech metadatech SAML poskytovaných aplikací. Zadejte adresu URL, která používá<subdomain>následující vzor: 'https:// .contoso.com' *Tuto hodnotu můžete najít jako element **Vystavitr** v **Požadavku AutohnRequest** (požadavek SAML) odeslaného aplikací.* |
    | **Adresa URL odpovědi** | Požaduje se | Požaduje se | Určuje, kde aplikace očekává přijetí tokenu SAML. Adresa URL odpovědi se také označuje jako adresa URL ACS (Assertion Consumer Service). Pomocí polí adresy URL pro další odpověď můžete zadat více adres URL odpovědí. Můžete například potřebovat další adresy URL odpovědí pro více subdomén. Nebo pro účely testování můžete zadat více adres URL odpovědí (místní hostitel a veřejné adresy URL) najednou. |
    | **Přihlašovací adresa URL** | Požaduje se | Nezadávat | Když uživatel otevře tuto adresu URL, poskytovatel služeb ho přesměruje do Azure AD, kde se uživatel ověří a přihlásí. Azure AD používá adresu URL ke spuštění aplikace z Office 365 nebo přístupového panelu Azure AD. Když prázdné, Azure AD provede přihlášení iniciované idP, když uživatel spustí aplikaci z Office 365, přístupového panelu Azure AD nebo adresy URL Azure AD.|
    | **Stav přenosu** | Nepovinné | Nepovinné | Určuje pro aplikaci, kam má přesměrovat uživatele po dokončení ověřování. Obvykle je hodnota platná adresa URL aplikace. Některé aplikace však toto pole používají odlišně. Další informace vám sdělí dodavatel aplikace.
    | **Adresa URL odhlášení** | Nepovinné | Nepovinné | Slouží k odeslání odpovědí odhlášení SAML zpět do aplikace.

Další informace naleznete [v tématu Single sign-on SAML protocol](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Krok 2. Konfigurace uživatelských atributů a deklarací identity 

Když se uživatel ověří v aplikaci, Azure AD vydá aplikaci token SAML s informacemi (nebo deklaracemi identity) o uživateli, který je jednoznačně identifikuje. Ve výchozím nastavení tyto informace zahrnují uživatelské jméno uživatele, e-mailovou adresu, jméno a příjmení. Tyto deklarace identity může být nutné přizpůsobit, pokud například aplikace vyžaduje určité hodnoty deklarací identity nebo jiný formát **názvu** než uživatelské jméno. Požadavky na aplikace galerie jsou popsány v [kurzech specifických pro aplikaci](../saas-apps/tutorial-list.md), nebo se můžete zeptat dodavatele aplikace. Obecné kroky pro konfiguraci uživatelských atributů a deklarací jsou popsány níže.

1. V části **Atributy uživatele a deklarace vyberte** v pravém horním rohu ikonu **Upravit** (tužku).

   ![Krok 2 Konfigurace uživatelských atributů a deklarací identity](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Ověřte **hodnotu identifikátoru názvu**. Výchozí hodnota je *user.principalname*. Identifikátor uživatele jednoznačně identifikuje každého uživatele v rámci aplikace. Pokud je například uživatelským jménem i jedinečným identifikátorem e-mailová adresa, nastavte tuto hodnotu na *user.mail*.

3. Chcete-li upravit **hodnotu identifikátoru názvu**, vyberte ikonu **Upravit** (tužku) pro pole **Hodnota identifikátoru názvu.** Podle potřeby proveďte příslušné změny ve formátu identifikátoru a zdroji. Podrobnosti naleznete v [tématu Editace NázvuId](../develop/active-directory-saml-claims-customization.md#editing-nameid). Po dokončení uložte změny. 
 
4. Chcete-li konfigurovat deklarace skupin, vyberte ikonu **Upravit** pro **skupiny vrácené v poli deklarace identity.** Podrobnosti naleznete v [tématu Konfigurace deklarací skupiny](../hybrid/how-to-connect-fed-group-claims.md).

5. Pokud chcete přidat deklaraci, vyberte **Přidat novou deklaraci v** horní části stránky. Zadejte **název** a vyberte příslušný zdroj. Pokud vyberete zdroj **atributu,** budete muset vybrat **atribut Zdroj,** který chcete použít. Pokud vyberete zdroj **překladu,** budete muset zvolit **transformace** a **parametr 1,** který chcete použít. Podrobnosti naleznete [v tématu Přidání deklarací specifických pro aplikaci](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Po dokončení uložte změny. 

6. Vyberte **Uložit**. Nový nárok se zobrazí v tabulce.

   > [!NOTE]
   > Další způsoby přizpůsobení tokenu SAML z Azure AD do vaší aplikace najdete v následujících prostředcích.
   >- Pokud chcete vytvořit vlastní role na webu Azure Portal, přečtěte si informace [o konfiguraci deklarací identity rolí](../develop/active-directory-enterprise-app-role-management.md).
   >- Pokud chcete deklarace identity přizpůsobit pomocí PowerShellu, [přečtěte si tématu Přizpůsobení deklarací identity – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Chcete-li upravit manifest aplikace tak, aby nakonfiguroval volitelné deklarace identity pro vaši aplikaci, přečtěte si informace [o konfiguraci volitelných deklarací identity](../develop/active-directory-optional-claims.md).
   >- Pokud chcete nastavit zásady životnosti tokenů tokenů pro obnovovací tokeny, přístupové tokeny, tokeny relace a tokeny ID, přečtěte si seznam [konfigurací životnosti tokenů](../develop/active-directory-configurable-token-lifetimes.md). Nebo chcete omezit relace ověřování prostřednictvím podmíněného přístupu Azure AD, přečtěte si informace [o možnostech správy relací ověřování](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Krok 3. Správa podpisového certifikátu SAML

Azure AD používá certifikát k podepsání tokenů SAML, které odesílá do aplikace. Tento certifikát potřebujete k nastavení vztahu důvěryhodnosti mezi službou Azure AD a aplikací. Podrobnosti o formátu certifikátu naleznete v dokumentaci aplikace SAML. Další informace naleznete v [tématu Správa certifikátů pro federované](manage-certificates-for-federated-single-sign-on.md) možnosti jednotného přihlášení a [rozšířeného podepisování certifikátů v tokenu SAML](certificate-signing-options.md).

Z Azure AD, můžete stáhnout aktivní certifikát v Base64 nebo raw formátu přímo z hlavní **nastavit jednotné přihlašování s SAML** stránky. Případně můžete získat aktivní certifikát stažením souboru XML metadat aplikace nebo pomocí adresy URL metadat federace aplikací. Chcete-li zobrazit, vytvořit nebo stáhnout certifikáty (aktivní nebo neaktivní), postupujte takto.

1. Přejděte do části **podpisový certifikát SAML.** 

   ![Krok 3 Správa podpisového certifikátu SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Ověřte, zda certifikát:

   - *Požadované datum vypršení platnosti.* Datum vypršení platnosti můžete nakonfigurovat až na tři roky do budoucna.
   - *Stav aktivní pro požadovaný certifikát.* Pokud je stav **Neaktivní**, změňte stav na **Aktivní**. Chcete-li změnit stav, klepněte pravým tlačítkem myši na řádek požadovaného certifikátu a vyberte **možnost Vytvořit certifikát aktivní**.
   - *Správná možnost podepisování a algoritmus.*
   - *Správná e-mailová adresa oznámení (adresy).* Když se aktivní certifikát blíží datu vypršení platnosti, Azure AD odešle oznámení na e-mailovou adresu nakonfigurovanou v tomto poli.

2. Chcete-li certifikát stáhnout, vyberte jednu z možností pro formát Base64, formát Raw nebo xml metadat federace. Azure AD také poskytuje **adresu URL metadat federace aplikací,** kde máte přístup `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`k metadatům specifickým pro aplikaci ve formátu .

3. Chcete-li spravovat, vytvářet nebo importovat certifikát, vyberte ikonu **Upravit** (tužku) v pravém horním rohu části **Podpisový certifikát SAML.**

   ![Podpisový certifikát SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Prováďte některou z následujících akcí:

   - Chcete-li vytvořit nový certifikát, vyberte **nový certifikát**, vyberte datum **vypršení platnosti**a pak vyberte **Uložit**. Chcete-li certifikát aktivovat, vyberte kontextovou nabídku (**...**) a vyberte **Možnost Vytvořit certifikát aktivní**.
   - Chcete-li nahrát certifikát s pověřeními soukromého klíče a pfx, vyberte **importovat certifikát** a vyhledejte certifikát. Zadejte **heslo PFX**a pak vyberte **Přidat**.  
   - Chcete-li konfigurovat rozšířené možnosti podepisování certifikátů, použijte následující možnosti. Popis těchto možností naleznete v článku Rozšířené [možnosti podepisování certifikátů.](certificate-signing-options.md)
      - V rozevíracím seznamu **Možnost podepisování** zvolte **Podepsat odpověď SAML**, **Podepsat kontrolní výraz SAML**nebo **Podepsat odpověď a kontrolní výraz SAML**.
      - V rozevíracím seznamu **Algoritmus podepisování** zvolte **SHA-1** nebo **SHA-256**.
   - Pokud chcete upozornit další uživatele, když se aktivní certifikát blíží datu vypršení platnosti, zadejte e-mailové adresy do polí **E-mailové adresy oznámení.**

4. Pokud jste provedli změny, vyberte **Uložit** v horní části oddílu **podpisový certifikát SAML.** 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Krok 4. Nastavení aplikace pro použití Azure AD

V části **Nastavit \<název_aplikace>** uvádí hodnoty, které je třeba nakonfigurovat v aplikaci, takže bude používat Azure AD jako zprostředkovatele identity SAML. Požadované hodnoty se liší podle aplikace. Podrobnosti naleznete v dokumentaci aplikace SAML. Chcete-li najít dokumentaci, přejděte na název ** \<aplikace nastavit>** záhlaví a vyberte **zobrazit podrobné pokyny**. Dokumentace se zobrazí na stránce **Konfigurovat přihlášení.** Tato stránka vás provede vyplněním **přihlašovací adresy URL**, **identifikátoru Azure AD**a hodnoty **adresy URL odhlášení** v záhlaví **Nastavit název \<aplikace>.**

1. Posuňte se dolů do oddílu **Nastavit \<název aplikace>.** 
   
   ![Krok 4 Nastavení aplikace](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Zkopírujte hodnotu z každého řádku v této části podle potřeby a postupujte podle pokynů pro konkrétní aplikaci pro přidání hodnoty do aplikace. U aplikací pro galerii můžete zobrazit dokumentaci výběrem **možnosti Zobrazit podrobné pokyny**. 
   - Hodnoty **adresy URL přihlášení** a **odhlášení adresy URL** přeložit na stejný koncový bod, což je koncový bod pro zpracování požadavků SAML pro vaši instanci Azure AD. 
   - **Identifikátor Azure AD** je hodnota **vystavittele** v tokenu SAML vydaného aplikaci.
2. Po vložení všech hodnot do příslušných polí vyberte **Uložit**.

## <a name="step-5-validate-single-sign-on"></a>Krok 5. Ověřit jednotné přihlašování

Jakmile nakonfigurujete aplikaci tak, aby používala Azure AD jako poskytovatele identity založeného na SAML, můžete otestovat nastavení a zjistit, jestli jednotné přihlašování funguje pro váš účet. 

2. Přejděte do části **Ověřit <applicationName> jednotné přihlašování pomocí.**

   ![Krok 5 Ověření jednotného přihlášení](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Vyberte **Ověřit**. Zobrazí se možnosti testování.

4. Vyberte **Přihlásit se jako aktuální uživatel**. 

Pokud je přihlášení úspěšné, můžete k aplikaci SAML přiřadit uživatele a skupiny.
Pokud se zobrazí chybová zpráva, proveďte následující kroky:

1. Zkopírujte podrobnosti a vložte je do pole **Jak chyba vypadá?**.

    ![Získání postupu řešení](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Vyberte **možnost Získat pokyny k rozlišení**. Zobrazí se hlavní příčina a pokyny k řešení.  V tomto příkladu uživatel nebyl přiřazen k aplikaci.

3. Přečtěte si pokyny k řešení a pak, pokud je to možné, problém opravte.

4. Spusťte test znovu, dokud se úspěšně nedokončí.

Další informace najdete v tématu [Ladění jednotného přihlašování na základě SAML k aplikacím ve službě Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)
