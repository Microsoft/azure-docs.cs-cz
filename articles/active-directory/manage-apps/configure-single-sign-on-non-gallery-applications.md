---
title: Jednotné přihlašování – aplikace mimo Galerii – platforma identit Microsoft | Dokumentace Microsoftu
description: Nakonfigurovat jednotné přihlašování (SSO) tak, aby aplikace mimo galerii v Microsoft identity platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95b7cbcf4e485dc93d49b9559dcb7d0d4f597ebe
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550351"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Nakonfigurovat jednotné přihlašování do aplikace mimo galerii v platforma identit Microsoft

Tento článek se týká funkce, která umožňuje správci nakonfigurovat jednotné přihlašování pro aplikace v galerii aplikací Microsoft identity platform chybí *bez psaní kódu*.

Pokud místo toho hledáte informace pro vývojáře o tom, jak integrace vlastních aplikací s Azure AD prostřednictvím kódu, přečtěte si téma [scénáře ověřování pro službu Azure AD](../develop/authentication-scenarios.md).

Poskytuje seznam aplikací, které jsou známé pro podporu formou jednotné přihlašování s platformou identity Microsoft, jak je popsáno v galerii aplikací Microsoft identity platform [v tomto článku](what-is-single-sign-on.md). Jakmile (jako IT specialista nebo systémový integrátor ve vaší organizaci) najdete aplikace, kterou chcete připojit, můžete začít podle podrobných pokynů uvedené na webu Azure Portal můžete povolit jednotné přihlašování.

Následující možnosti jsou dostupné, podle vaší licenční smlouvě. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory/).

- Samoobslužná integrace aplikace, která používají moderní protokol jako [OpenId Connect a OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) k ověření uživatelů a získání tokenů pro [Microsoft Graphu](https://graph.microsoft.com).
- Samoobslužná integrace každou aplikaci, která podporuje [zabezpečení kontrolního výrazu SAML (Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) zprostředkovatele identity (iniciovaného Zprostředkovatelem přihlašování nebo zahájené pomocí IdP)
- Samoobslužná integrace webové aplikace, který je založený na jazyce HTML přihlašovací stránky pomocí [jednotného přihlašování pomocí hesla](what-is-single-sign-on.md#password-based-sso)
- Samoobslužná služba připojení aplikací, které používají [systém pro správu identit mezi doménami (SCIM) je protokol pro zřizování uživatelů](use-scim-to-provision-users-and-groups.md)
- Možnost přidání odkazů do libovolné aplikace v [Spouštěči aplikací Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [přístupový panel služby Azure AD](what-is-single-sign-on.md#linked-sign-on)

Mezi Samoobslužná integrace software jako služba (SaaS) aplikace, který používáte, můžete tyto možnosti patří i v případě, že nemá nikdo připojili aplikaci zatím do Galerie aplikací Azure AD. Další funkce je samoobslužné integrace třetích stran webovou aplikaci, která vaší organizaci s nasazeným na servery, které sami kontrolujete, buď v cloudu nebo místně.

Také *šablony pro integraci aplikací*, tyto funkce nabízejí body připojení založené na standardech pro aplikace, které podporují ověřování pomocí formulářů, SAML nebo SCIM. Možnosti zahrnují flexibilní možnosti a nastavení pro kompatibilitu s širokou řadu aplikací.

## <a name="adding-an-unlisted-application"></a>Přidání neuvedené aplikace

Platforma identit Microsoft poskytuje dva mechanismy pro registraci aplikace.

Aplikace, která používá moderní protokol například [OpenId Connect a OAuth](../develop/active-directory-v2-protocols.md) k ověření uživatelů je registrována pomocí [portál pro registraci aplikací](../develop/quickstart-register-app.md).

K registraci aplikací s použitím všechny ostatní typy [podporované mechanismy ověřování](what-is-single-sign-on.md), třeba [SAML](../develop/single-sign-on-saml-protocol.md) protokolu, použijte **podnikové aplikace** okno pro připojení je s platformou identity Microsoft.

K připojení neuvedené aplikace pomocí šablony integrace aplikace, proveďte tyto kroky:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu Microsoft identity platform správce.
2. Vyberte **podnikové aplikace** > **novou aplikaci**.
3. (Volitelné, ale doporučené) V **přidat z Galerie** vyhledávací pole, zadejte zobrazovaný název aplikace. Pokud se daná aplikace se zobrazí ve výsledcích hledání, vyberte ho a přeskočit zbývající část tohoto postupu.
4. Vyberte **aplikace mimo galerii**. **Přidat vlastní aplikaci** se zobrazí stránka.

   ![Přidání aplikace](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Zadejte zobrazovaný název pro novou aplikaci.
6. Vyberte **Přidat**.

Tak, že přidáte aplikaci tak, poskytují předem integrovaných aplikací fungují na podobném principu obrazovku k dispozici. Nejprve vyberte **jednotného přihlašování** z postranního panelu aplikace. Na další stránku (**vybrat jedinou metodu přihlašování**) nabízí možnosti pro konfiguraci jednotného přihlašování:

- **SAML**
- **Založené na heslech**
- **Propojené**

![Vyberte metodu jednotné přihlašování](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Další informace o těchto možnostech najdete v následující části tohoto článku.

## <a name="saml-based-single-sign-on"></a>Založené na SAML jednotného přihlašování

Vyberte **SAML** možnost nakonfigurovat ověřování pro aplikaci na základě SAML. (Tato možnost vyžaduje, aby podporoval aplikace SAML 2.0.) **Nastavte si jednotné přihlašování pomocí SAML** se zobrazí stránka.

![Nastavení jednotného přihlašování pomocí SAML](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Tato stránka obsahuje pět různých položek:

| Číslo nadpisu | Název záhlaví | Souhrnné informace o této položce naleznete v tématu: |
| --- | --- | --- |
| 1 | **Konfigurace základního SAML** | [Zadejte základní konfiguraci SAML](#enter-basic-saml-configuration) |
| 2 | **Atributy uživatele a deklarace identity** | [Zkontrolovat nebo přizpůsobit deklarací identity vystavených v tokenu SAML](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **Podpisový certifikát SAML** | [Zkontrolujte data vypršení platnosti certifikátu, stav a e-mailové oznámení](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Nastavit \<název aplikace >** | [Nastavení cílové aplikace](#set-up-target-application) |
| 5 | **Otestovat jednotné přihlašování s \<název aplikace >** | [Testování aplikace SAML](#test-the-saml-application) |

Nyní můžete shromážděte informace o tom, jak používat funkce SAML aplikace, než budete pokračovat. Dokončování následujících částí nakonfigurovat jednotné přihlašování mezi aplikací a službou Azure AD.

### <a name="enter-basic-saml-configuration"></a>Zadejte základní konfiguraci SAML

Nastavení Azure AD, přejít na **základní konfiguraci SAML** nadpis a vyberte jeho **upravit** ikonu (tužky). Můžete ručně zadat hodnoty nebo nahrát soubor metadat a extrahovat hodnotu pole.

![Základní konfigurace SAML](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Následující dvě pole jsou požadovány:

- **Identifikátor**. Tato hodnota by měla jednoznačné identifikaci aplikace, pro kterou jednotného přihlašování je konfigurován. Tuto hodnotu jako lze najít **vystavitele** prvek **AuthnRequest** (požadavku SAML) odeslané aplikací pomocí. Tato hodnota se rovněž zobrazuje jako **Entity ID** v veškerá metadata SAML poskytovaný aplikací. V dokumentaci aplikace SAML podrobnosti toho, co její **Entity ID** nebo **cílovou skupinu** hodnotu.

  Následující kód ukazuje jak **identifikátor** nebo **vystavitele** se zobrazí v požadavku SAML, které aplikace odesílá do služby Azure AD:

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **Adresa URL pro odpověď**. Adresa URL odpovědi je, kde se očekává, že aplikace přijímat tokenu SAML. Tato adresa URL se také označuje jako adresu URL pro kontrolní výraz consumer service (ACS). Naleznete v dokumentaci aplikace SAML podrobnosti o tom, co je jeho token odpověď SAML adresu URL nebo adresa URL služby ACS.

  Pokud chcete nakonfigurovat více adres URL odpovědí, můžete použít následující skript prostředí PowerShell.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

Následující tři pole jsou volitelná:

- **Přihlašovací adresa URL (iniciovaného Zprostředkovatelem přihlašování pouze)** . Tato hodnota označuje, kde uživatel přejde za účelem přihlášení k této aplikaci. Pokud aplikace provádí iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování, pak když uživatel přejde na tuto adresu URL této uložené Procedury provede nezbytné přesměrování do služby Azure AD k ověření a přihlášení uživatele. Pokud chcete zadat toto pole, Azure AD používá tuto adresu URL ke spuštění aplikace z Office 365 a Azure AD přístupového panelu. Vynecháte-li toto pole, Azure AD místo toho provádí zahájené pomocí IdP přihlašování při spuštění aplikace z Office 365, Azure AD přístupového panelu nebo adresu URL jednotného přihlašování k Azure AD (který můžete zkopírovat z **řídicí panel** stránky).

- **Stav přenosu**. Stav přenosu můžete zadat v SAML dáte pokyn, aby aplikace where přesměrovat uživatele po ověření. Hodnota je obvykle adresa nebo adresa URL cestu, která uživatelé přejdou do určitého umístění v rámci aplikace.

- **Odhlašovací adresa URL**. Tato hodnota se používá k odeslání odpovědi odhlašování SAML zpět do aplikace.

Další informace najdete v tématu [jednotné přihlašování – protokol SAML](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Zkontrolovat nebo přizpůsobit deklarací identity vystavených v tokenu SAML

Když se uživatel přihlásí k aplikaci, Azure AD vydá aplikace tokenu SAML informace (nebo deklarace identity) informace o uživateli, který jednoznačně identifikuje je. Ve výchozím nastavení tyto informace zahrnují uživatelské jméno, e-mailová adresa, jméno a příjmení uživatele.

K zobrazení nebo úpravám deklarací identity odesílají v tokenu SAML pro aplikaci:

- Přejděte **atributy uživatele a deklarace identity** nadpis a vyberte **upravit** ikonu. **Atributy uživatele a deklarace identity** se zobrazí stránka.

![Atributy uživatele a deklarace identity](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Je třeba upravit deklarací identity vystavených v tokenu SAML pro dva důvody:

- Aplikace vyžaduje jinou sadu identifikátorů URI nebo hodnoty deklarací.
- Vaše aplikace vyžaduje **název hodnota identifikátoru** za něco jiného než uživatelské jméno (také označované jako hlavní název uživatele) uložených v Microsoft identity platform.

Další informace najdete v tématu [jak: Přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Zkontrolujte data vypršení platnosti certifikátu, stav a e-mailové oznámení

Při vytváření galerie nebo mimo Galerii aplikací Azure AD vytvoří certifikát specifické pro aplikaci, jejíž platnost vyprší tří let od data jejího vytvoření. Je nutné tento certifikát k nastavení vztahu důvěryhodnosti mezi službami Azure AD a aplikace. Podrobnosti o formát certifikátu najdete v dokumentaci k vaší aplikace SAML.

Ze služby Azure AD si můžete stáhnout aktivní certifikát ve formátu Base64 nebo Raw přímo z hlavní **nastavte si jednotné přihlašování pomocí SAML** stránky. Alternativně můžete získat aktivní certifikát stáhnout tento soubor metadat XML aplikace nebo adresa URL federačních metadat aplikace.

Pokud chcete zobrazit, vytvořit nebo stáhnout certifikáty (aktivní nebo neaktivní), přejděte na **podpisový certifikát SAML** nadpis a vyberte **upravit** ikonu. **Podpisový certifikát SAML** se zobrazí.

![Podpisový certifikát SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Ověřte, zda je certifikát:

- *Datum vypršení platnosti požadované.* Do budoucna můžete nakonfigurovat datum vypršení platnosti až tři roky.
- *Stav active pro požadovaný certifikát.* Pokud je stav **neaktivní**, změňte stav na **aktivní**. Chcete-li změnit stav, klikněte pravým tlačítkem na požadovaný certifikát řádku a vyberte **aktivovat certifikát**.
- *Správnou volbu pro podepisování a algoritmus.*
- *Správné oznámení e-mailové adresy.* Pokud aktivní certifikát blíží datum vypršení platnosti, Azure AD pošle oznámení na e-mailovou adresu nakonfigurovanou v tomto poli.  

Další informace najdete v tématu [Správa certifikátů pro federované jednotné přihlašování](manage-certificates-for-federated-single-sign-on.md) a [pokročilé možnosti v tokenu SAML podepisování certifikátů](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Nastavení cílové aplikace

Konfigurace aplikace pro jednotné přihlašování, vyhledejte dokumentaci k aplikaci. Můžete vyhledat dokumentaci, přejděte na **nastavení \<název aplikace >** nadpis a vyberte **zobrazit podrobný**. V dokumentaci se zobrazí v **nakonfigurovat přihlašování** stránky. Této stránce vás povede při vyplňování **přihlašovací adresa URL**, **Azure AD identifikátor**, a **odhlašovací adresa URL** hodnoty v **nastavení \<název aplikace >** záhlaví.

Požadované hodnoty liší v závislosti na aplikaci. Podrobnosti najdete v dokumentaci k vaší aplikace SAML. **Přihlašovací adresa URL** a **odhlašovací adresa URL** hodnoty i přeložit na stejný koncový bod, což je koncový bod zpracování požadavku SAML pro vaši instanci služby Azure AD. **Azure AD identifikátor** je hodnota **vystavitele** v tokenu SAML vydaném pro aplikaci.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Přiřazení uživatelů a skupin pro aplikace SAML

Po dokončení konfigurace aplikace pro používání služby Azure AD jako zprostředkovatele identity založené na SAML, je téměř připraveni k testování. Jako ovládací prvek zabezpečení jenom služby Azure AD vydá token umožňuje uživateli přihlásit do aplikace, pokud Azure AD udělil přístup pro uživatele. Uživatelé mohou získat přístup přímo nebo prostřednictvím členství ve skupině.

Přiřazení nové uživatele nebo skupinu do vaší aplikace:

1. Na bočním panelu aplikace vyberte **uživatelů a skupin**. **\<Název aplikace >-uživatelů a skupin** se zobrazí stránka, která obsahuje aktuální seznam přiřazení uživatelé a skupiny.
2. Vyberte **přidat uživatele**. **Přidat přiřazení** zobrazí se stránka.
3. Vyberte **uživatelům a skupinám (\<číslo > vybrané)** . **Uživatelů a skupin** se zobrazí stránka zobrazuje seznam dostupných uživatelů a skupin.
4. Typ nebo přejděte k vyhledání uživatele nebo skupinu, kterou chcete přiřadit ze seznamu.
5. Vyberte jednotlivé uživatele nebo skupiny, kterou chcete přidat a pak vyberte **vyberte** tlačítko. **Uživatelů a skupin** stránky zmizí.
6. V **přidat přiřazení** stránce **přiřadit**. **\<Název aplikace >-uživatelů a skupin** se zobrazí stránka s dalšími uživateli v seznamu zobrazí.

   ![Aplikace uživatele a skupiny](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

Z tohoto seznamu můžete:

- Odebrání uživatele.
- Upravte jejich role.
- Aktualizujte přihlašovací údaje (uživatelské jméno a heslo) tak, aby se uživatel může ověřit pro tuto aplikaci z přístupového panelu uživatele.

Můžete upravit nebo odebrat víc uživatelů nebo skupin po jednom.

Přiřazení uživatele umožňuje Azure AD k vydání tokenu uživatele. Navíc způsobí, že dlaždice pro tuto aplikaci zobrazit uživatele přístupovém panelu. Dlaždici aplikace se také zobrazí ve Spouštěči aplikací Office 365, pokud uživatel používá Office 365.

> [!NOTE]
> Můžete nahrát logo dlaždice aplikace s využitím **nahrajte Logo** tlačítko **konfigurovat** kartu pro aplikaci.

### <a name="test-the-saml-application"></a>Testování aplikace SAML

Před testováním aplikace SAML, musíte mít již nastavení aplikace s Azure AD a přiřadili aplikace uživatelům nebo skupinám. K otestování aplikace SAML, vyberte **jednotného přihlašování**, které vrátíte **přihlašování na základě SAML** stránky. (Pokud jinou metodu jednotného přihlašování v platnosti, vyberte **změnit režim přihlašování** > **SAML** příliš.) Pak v **otestovat jednotné přihlašování s \<název_aplikace >** záhlaví, vyberte **Test**. Další informace najdete v tématu [založené na SAML ladit jednotné přihlašování k aplikacím v Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Heslo jednotného přihlašování

Tuto možnost použijte ke konfiguraci [založené na heslech jednotného přihlašování](what-is-single-sign-on.md) pro webovou aplikaci s HTML přihlašovací stránky. Jednotné přihlašování založené na heslech, také označuje jako heslo vaulting, umožňuje spravovat přístup uživatelů a hesel do webové aplikace, které nepodporují federace identit. Také je užitečná v případech, kdy je potřeba několik uživatelé sdílejí jeden účet, jako například účtům sociálních médií aplikace vaší organizace.

Po výběru **založené na heslech**, výzva k zadání adresy URL vaší aplikace webové přihlašovací stránky.

![Založené na heslech jednotného přihlašování](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Proveďte tyto kroky:

1. Zadejte adresu URL. Tento řetězec musí být na stránce, který obsahuje vstupní pole uživatelské jméno.
2. Vyberte **Uložit**. Azure AD se pokusí analyzovat přihlašovací stránku pro zadání uživatelského jména a hesla vstup.
3. Pokud Azure AD v analýze pokus selže, vyberte **konfigurovat \<název_aplikace > Nastavení hesla jednotného přihlašování** zobrazíte **nakonfigurovat přihlašování** stránky. (Pokud tento pokus úspěšný, můžete ignorovat zbývající část tohoto postupu.)
4. Vyberte **ručně zjišťovat pole pro přihlášení**. Zobrazí se další pokyny popisující Ruční zjišťování pole.

   ![Ruční konfigurace založené na heslech jednotného přihlašování](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Vyberte **zachytit pole pro přihlášení**. Stránka stavu zachytávání se otevře na nové kartě zprávou, která **zachycují metadata právě probíhá**.
6. Pokud **vyžaduje přístup k panelu rozšíření** na nové kartě, zobrazí se pole vyberte **nainstalovat** k instalaci **Moje aplikace zabezpečené přihlašování rozšíření** rozšíření prohlížeče. (Rozšíření prohlížeče vyžaduje Microsoft Edge, Chrome nebo Firefox.) Potom nainstalovat, spustit a povolit rozšíření a aktualizujte stránku zachycení stavu.

   Rozšíření prohlížeče pak otevře jinou kartu, která zobrazuje zadaná adresa URL.
7. Na kartě s zadaná adresa URL projděte si proces přihlašování. Vyplňte pole uživatelské jméno a heslo a zkuste se přihlásit. (Není nutné zadat správné heslo.)

   Zobrazí výzva s dotazem k uložení zaznamenané pole přihlášení.
8. Vyberte **OK**. Na kartě se zavře, rozšíření prohlížeče aktualizuje zachycení stavové stránce se zprávou **metadat byl aktualizován pro aplikaci**a také kartu zavře prohlížeče.
9. Ve službě Azure AD **nakonfigurovat přihlašování** stránce **Ok, povedlo se přihlásit k aplikaci úspěšně**.
10. Vyberte **OK**.

Po zachycení přihlašovací stránku, může přiřadit uživatele a skupiny, a můžete nastavit zásady přihlašovacích údajů, stejně jako normální [heslem jednotného přihlašování aplikace](what-is-single-sign-on.md).

> [!NOTE]
> Můžete nahrát logo dlaždice aplikace s využitím **nahrajte Logo** tlačítko **konfigurovat** kartu pro aplikaci.

## <a name="existing-single-sign-on"></a>Existující jednotné přihlašování

Vyberte tuto možnost, chcete-li přidat odkaz na aplikaci ve vaší organizaci přístupový Panel Azure AD nebo Office 365 na portálu. Tuto metodu můžete použít k přidání odkazů na vlastní webové aplikace, které používají Active Directory Federation Services (nebo jiné služby federation service) místo Azure AD pro ověřování. Nebo můžete přidat přímé odkazy na konkrétní stránky SharePoint nebo jiných webových stránek, které chcete zobrazit na přístupových panelech uživatele.

Po výběru **propojené**, zobrazí výzva k zadání adresy URL aplikace propojení. Zadejte adresu URL a vyberte **Uložit**. Může přiřazení uživatelů a skupin k aplikaci, což způsobí, že aplikace bude zobrazovat v [Spouštěči aplikací Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [přístupový panel služby Azure AD](end-user-experiences.md) pro tyto uživatele.

> [!NOTE]
> Můžete nahrát logo dlaždice aplikace s využitím **nahrajte Logo** tlačítko **konfigurovat** kartu pro aplikaci.

## <a name="related-articles"></a>Související články

- [Postup: Přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md)
- [Ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Platforma identit Microsoft (dříve Azure Active Directory pro vývojáře)](../develop/index.yml)
