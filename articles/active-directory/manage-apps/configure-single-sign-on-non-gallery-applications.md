---
title: Aplikace s jednotným přihlašováním SAML – Microsoft Identity Platform | Microsoft Docs
description: Konfigurace jednotného přihlašování (SSO) k aplikacím mimo galerii v platformě Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1604e132cb77fbb2a2a1033a1f23f70dd3e6b8b9
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355960"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Konfigurace jednotného přihlašování k aplikacím mimo galerii založeného na SAML

Když do podnikových aplikací Azure AD [přidáte aplikaci Galerie](add-gallery-app.md) nebo [webovou aplikaci mimo galerii](add-non-gallery-app.md) , jedna z možností jednotného přihlašování, kterou máte k dispozici, je [jednotné přihlašování založené na SAML](what-is-single-sign-on.md#saml-sso). Pokud je to možné, vyberte SAML vždy u aplikací, které se ověřují pomocí některého z protokolů SAML. Pomocí jednotného přihlašování SAML Azure AD ověřuje aplikace pomocí účtu Azure AD uživatele. Azure AD komunikuje s přihlašovacími informacemi k aplikaci prostřednictvím protokolu připojení. Můžete mapovat uživatele na konkrétní aplikační role na základě pravidel, která definujete v deklaracích SAML. Tento článek popisuje, jak nakonfigurovat jednotné přihlašování založené na SAML pro aplikaci mimo galerii. 

> [!NOTE]
> Přidávání aplikace v galerii? Podrobné pokyny k instalaci najdete v [seznamu výukových kurzů pro aplikace SaaS](../saas-apps/tutorial-list.md) .

Pokud chcete pro aplikaci mimo galerii nakonfigurovat jednotné přihlašování SAML bez psaní kódu, musíte mít předplatné Azure AD a aplikace musí podporovat SAML 2,0. Další informace o verzích Azure AD najdete na stránce [ceny Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Než začnete

Pokud se aplikace nepřidala do tenanta Azure AD, přečtěte si téma [Přidání aplikace mimo galerii](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Krok 1. Úprava základní konfigurace SAML

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce cloudové aplikace nebo správce aplikace pro vašeho TENANTA Azure AD.

2. Přejděte na **Azure Active Directory**  >  **podnikové aplikace** a vyberte aplikaci ze seznamu. 
   
   - Pokud chcete aplikaci vyhledat, v nabídce **Typ aplikace** vyberte **všechny aplikace**a pak vyberte **použít**. Do vyhledávacího pole zadejte název aplikace a pak vyberte aplikaci z výsledků.

3. V části **Spravovat** vyberte **jednotné přihlašování**. 

   - Všimněte si, že existují některé scénáře, kdy možnost **jednotného přihlašování** nebude k dispozici. Pokud se například aplikace zaregistrovala pomocí **Registrace aplikací** pak je funkce jednotného přihlašování nakonfigurovaná na portálu pro **registraci aplikací** a v rámci **podnikových aplikací**se nezobrazí v navigaci. Další scénáře, kdy v navigaci chybí **jednotné přihlašování** , patří mezi ně, pokud je aplikace hostovaná v jiném tenantovi nebo pokud váš účet nemá požadovaná oprávnění (globální správce, správce cloudové aplikace, Správce aplikací nebo vlastník instančního objektu). Oprávnění mohou také způsobit situaci, kdy můžete otevřít **jednotné přihlašování** , ale nebudete je moci uložit. Další informace o rolích pro správu Azure AD najdete v tématu ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

4. Vyberte **SAML**. Zobrazí se stránka **nastavit jednotné přihlašování pomocí SAML-Preview** .

   ![Krok 1 – Úprava základní konfigurace SAML](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Pokud chcete upravit základní možnosti konfigurace SAML, vyberte ikonu **Upravit** (tužka) v pravém horním rohu **základní sekce konfigurace SAML** .

1. Zadejte následující nastavení. Měli byste získat hodnoty od dodavatele aplikace. Můžete ručně zadat hodnoty nebo nahrát soubor metadat a extrahovat tak hodnotu polí.

    | Základní nastavení konfigurace SAML | Iniciováno zprostředkovatelem přihlašování | Iniciováno pomocí IdP | Popis |
    |:--|:--|:--|:--|
    | **Identifikátor (ID entity)** | Vyžaduje se pro některé aplikace | Vyžaduje se pro některé aplikace | Aplikace jednoznačně identifikuje. Azure AD odešle identifikátor do aplikace jako parametr cílové skupiny tokenu SAML. Očekává se, že aplikace ověří tuto aplikaci. Tato hodnota se také zobrazuje jako ID entity ve všech metadatech SAML poskytovaných aplikací. Zadejte adresu URL, která používá následující vzor: ' https:// <subdomain> . contoso.com ' *tuto hodnotu můžete najít jako element **vystavitele** v **AuthnRequest** (žádost SAML) odeslanou aplikací.* |
    | **Adresa URL odpovědi** | Vyžadováno | Vyžadováno | Určuje, kde aplikace očekává přijetí tokenu SAML. Adresa URL odpovědi se také označuje jako adresa URL ACS (Assertion Consumer Service). Pomocí polí další adresa URL odpovědi můžete zadat několik adres URL odpovědi. Můžete například potřebovat další adresy URL pro odpovědi pro více subdomén. Nebo pro účely testování můžete najednou zadat několik adres URL odpovědí (místní hostitel a veřejné adresy URL). |
    | **Přihlašovací adresa URL** | Vyžadováno | Nezadávejte | Když uživatel otevře tuto adresu URL, poskytovatel služeb ho přesměruje do Azure AD, kde se uživatel ověří a přihlásí. Azure AD používá adresu URL ke spuštění aplikace ze sady Office 365 nebo přístupového panelu Azure AD. Pokud je toto pole prázdné, Azure AD provede přihlášení iniciované IdP, když uživatel spustí aplikaci z Office 365, přístupového panelu Azure AD nebo adresy URL jednotného přihlašování Azure AD.|
    | **Stav přenosu** | Volitelné | Volitelné | Určuje pro aplikaci, kam má přesměrovat uživatele po dokončení ověřování. Obvykle je hodnota platnou adresou URL pro aplikaci. Některé aplikace ale používají toto pole odlišně. Další informace vám sdělí dodavatel aplikace.
    | **Odhlašovací adresa URL** | Volitelné | Volitelné | Slouží k odeslání odpovědí na odhlášení SAML zpět do aplikace.

Další informace najdete v tématu [protokol SAML jednotného přihlašování](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Krok 2. Konfigurace uživatelských atributů a deklarací identity 

Když se uživatel do aplikace ověří, služba Azure AD vydá aplikaci token SAML s informacemi (nebo deklaracemi identity) o uživateli, který je jednoznačně identifikoval. Ve výchozím nastavení obsahují tyto informace uživatelské jméno, e-mailovou adresu, jméno a příjmení. Tyto deklarace může být nutné přizpůsobit, pokud například aplikace vyžaduje konkrétní hodnoty deklarace identity nebo jiný formát **názvu** než uživatelské jméno. Požadavky na aplikace pro galerii jsou popsány v [kurzech specifických pro aplikaci](../saas-apps/tutorial-list.md)nebo můžete požádat dodavatele aplikace. Obecné kroky pro konfiguraci atributů a deklarací identity uživatele jsou popsané níže.

1. V části **atributy a deklarace identity uživatele** vyberte v pravém horním rohu ikonu pro **Úpravy** (tužka).

   ![Krok 2 – konfigurace uživatelských atributů a deklarací identity](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Ověřte **hodnotu identifikátoru názvu**. Výchozí hodnota je *User. Principal*. Identifikátor uživatele jednoznačně identifikuje každého uživatele v rámci aplikace. Pokud je například uživatelským jménem i jedinečným identifikátorem e-mailová adresa, nastavte tuto hodnotu na *user.mail*.

3. Chcete-li změnit **hodnotu identifikátoru názvu**, vyberte pro pole **hodnota identifikátoru názvu** ikonu **Upravit** (tužka). V případě potřeby proveďte příslušné změny formátu identifikátoru a zdroje. Podrobnosti najdete v tématu [editace NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid). Až budete hotovi, uložte změny. 
 
4. Pokud chcete nakonfigurovat deklarace identity skupiny, vyberte pro **skupiny vrácené v poli deklarace** ikonu pro **Úpravy** . Podrobnosti najdete v tématu [Konfigurace deklarací identity skupiny](../hybrid/how-to-connect-fed-group-claims.md).

5. Pokud chcete přidat deklaraci identity, vyberte v horní části stránky **Přidat novou deklaraci identity** . Zadejte **název** a vyberte příslušný zdroj. Pokud vyberete zdroj **atributu** , budete muset zvolit **zdrojový atribut** , který chcete použít. Pokud vyberete zdroj **překladu** , budete muset zvolit **transformaci** a **parametr 1** , který chcete použít. Podrobnosti najdete v tématu [Přidání deklarací specifických pro aplikaci](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Až budete hotovi, uložte změny. 

6. Vyberte **Uložit**. Nová deklarace identity se zobrazí v tabulce.

   > [!NOTE]
   > Další způsoby přizpůsobení tokenu SAML z Azure AD do vaší aplikace najdete v následujících zdrojích.
   >- Pokud chcete vytvořit vlastní role prostřednictvím Azure Portal, přečtěte si téma [Konfigurace deklarací identity rolí](../develop/active-directory-enterprise-app-role-management.md).
   >- Informace o přizpůsobení deklarací prostřednictvím PowerShellu najdete v tématu [přizpůsobení deklarací – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Postup úpravy manifestu aplikace pro konfiguraci volitelných deklarací identity aplikace najdete v tématu [konfigurace volitelných deklarací identity](../develop/active-directory-optional-claims.md).
   >- Informace o nastavení zásad životnosti tokenů pro aktualizační tokeny, přístupové tokeny, tokeny relací a tokeny ID najdete v tématu [Konfigurace životností tokenů](../develop/active-directory-configurable-token-lifetimes.md). Nebo pokud chcete omezit ověřovací relace přes podmíněný přístup Azure AD, přečtěte si téma [Možnosti správy relace ověřování](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Krok 3. Správa podpisového certifikátu SAML

Azure AD pomocí certifikátu podepisuje tokeny SAML, které posílá do aplikace. Tento certifikát potřebujete k nastavení vztahu důvěryhodnosti mezi službou Azure AD a aplikací. Podrobnosti o formátu certifikátu najdete v dokumentaci SAML aplikace. Další informace najdete v tématu [Správa certifikátů pro federované jednotné přihlašování](manage-certificates-for-federated-single-sign-on.md) a [Rozšířené možnosti podepisování certifikátů v tokenu SAML](certificate-signing-options.md).

Z Azure AD si můžete stáhnout aktivní certifikát ve formátu Base64 nebo RAW přímo z hlavního **nastavení jednotné přihlašování se** stránkou SAML. Případně můžete získat aktivní certifikát stažením souboru XML s metadaty aplikace nebo pomocí adresy URL federačních metadat aplikace. Pokud chcete zobrazit, vytvořit nebo stáhnout svoje certifikáty (aktivní nebo neaktivní), postupujte podle těchto kroků.

1. Přejít do části **podpisový certifikát SAML** . 

   ![Krok 3 – Správa podpisového certifikátu SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Ověřte, že certifikát obsahuje:

   - *Požadované datum vypršení platnosti.* V budoucnu můžete nakonfigurovat datum vypršení platnosti až na tři roky.
   - *Stav aktivní pro požadovaný certifikát.* Pokud je stav **neaktivní**, změňte stav na **aktivní**. Chcete-li změnit stav, klikněte pravým tlačítkem na řádek požadovaného certifikátu a vyberte možnost **Označit certifikát jako aktivní**.
   - *Správná možnost podepisování a algoritmus.*
   - *Správná e-mailová adresa (y) oznámení.* Když se aktivní certifikát blíží datu vypršení platnosti, Azure AD pošle oznámení na e-mailovou adresu nakonfigurovanou v tomto poli.

2. Chcete-li stáhnout certifikát, vyberte jednu z možností pro Formát Base64, nezpracovaný formát nebo XML federačních metadat. Azure AD taky poskytuje **adresu URL federačních metadat aplikace** , kde můžete získat přístup k metadatům specifickým pro aplikaci ve formátu `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

3. Pokud chcete spravovat, vytvořit nebo importovat certifikát, vyberte ikonu **Upravit** (tužka) v pravém horním rohu oddílu **podpisového certifikátu SAML** .

   ![Podpisový certifikát SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Proveďte některou z následujících akcí:

   - Chcete-li vytvořit nový certifikát, vyberte možnost **nový certifikát**, vyberte **Datum vypršení platnosti**a pak vyberte **Uložit**. Certifikát aktivujete tak, že vyberete kontextovou nabídku (**...**) a vyberete **nastavit certifikát jako aktivní**.
   - Pokud chcete nahrát certifikát s privátním klíčem a přihlašovacími údaji PFX, vyberte **importovat certifikát** a vyhledejte certifikát. Zadejte **heslo PFX**a pak vyberte **Přidat**.  
   - Pokud chcete nakonfigurovat rozšířené možnosti podepisování certifikátů, použijte následující možnosti. Popisy těchto možností najdete v článku [Rozšířené možnosti pro podepsání certifikátu](certificate-signing-options.md) .
      - V rozevíracím seznamu **možnost podepisování vyberte možnost** **podepsat odpověď SAML**, **podepsat kontrolní výraz SAML**nebo **podepsat odpověď a kontrolní výraz SAML**.
      - V rozevíracím seznamu **algoritmus podepisování** vyberte **SHA-1** nebo **SHA-256**.
   - Pokud chcete informovat další lidi, když se aktivní certifikát blíží datu vypršení platnosti, zadejte e-mailové adresy do polí **e-mailových adres pro oznámení** .

4. Pokud jste provedli změny, v horní části části **podpisový certifikát SAML** vyberte **Save (Uložit** ). 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Krok 4. Nastavení aplikace pro použití Azure AD

V části **nastavení \<applicationName> ** jsou uvedené hodnoty, které je třeba v aplikaci nakonfigurovat tak, aby používaly Azure AD jako zprostředkovatele identity SAML. Požadované hodnoty se liší v závislosti na aplikaci. Podrobnosti najdete v dokumentaci SAML aplikace. Pokud chcete najít dokumentaci, klikněte na záhlaví **nastavení \<application name> ** a vyberte **Zobrazit podrobné pokyny**. Dokumentace se zobrazí na stránce **Konfigurace přihlášení** . Tato stránka vás provede vyplněním **přihlašovací adresy URL**, **identifikátoru Azure AD**a **adres URL pro odhlášení** v záhlaví pro **nastavení \<application name> ** .

1. Přejděte dolů do části **nastavení \<applicationName> ** . 
   
   ![Krok 4 nastavení aplikace](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Podle potřeby Zkopírujte hodnotu z každého řádku v této části a postupujte podle pokynů pro konkrétní aplikaci pro přidání hodnoty do aplikace. V případě aplikací galerie můžete zobrazit dokumentaci tak, že vyberete **Zobrazit podrobné pokyny**. 
   - Hodnoty **adresy** URL pro přihlášení a adresy **URL pro odhlášení** se obě překládají na stejný koncový bod, což je koncový bod pro zpracování požadavků SAML pro vaši instanci Azure AD. 
   - **Identifikátor Azure AD** je hodnota **vystavitele** v tokenu SAML, který je vydaný pro aplikaci.
2. Po vložení všech hodnot do příslušných polí vyberte **Uložit**.

## <a name="step-5-validate-single-sign-on"></a>Krok 5. Ověřit jednotné přihlašování

Jakmile nakonfigurujete aplikaci tak, aby používala Azure AD jako zprostředkovatele identity založeného na SAML, můžete otestovat nastavení a zjistit, jestli pro váš účet funguje jednotné přihlašování. 

2. Posuňte se do části **ověření jednotného přihlašování <applicationName> ** .

   ![Krok 5: ověření jednotného přihlašování](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Vyberte **Ověřit**. Zobrazí se možnosti testování.

4. Vyberte **Přihlásit se jako aktuální uživatel**. 

Pokud je přihlášení úspěšné, budete připraveni přiřadit uživatele a skupiny do aplikace SAML.
Pokud se zobrazí chybová zpráva, proveďte následující kroky:

1. Zkopírujte podrobnosti a vložte je do pole **Jak chyba vypadá?**.

    ![Získání postupu řešení](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Vyberte možnost **získat pokyny k řešení**. Zobrazí se pokyny k hlavní příčině a řešení.  V tomto příkladu uživatel nebyl přiřazen k aplikaci.

3. Přečtěte si pokyny k řešení a pokud je to možné, vyřešte problém.

4. Spusťte test znovu, dokud se úspěšně nedokončí.

Další informace najdete v tématu [ladění jednotného přihlašování založeného na SAML pro aplikace v Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)
