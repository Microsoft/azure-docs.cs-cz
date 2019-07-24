---
title: Konfigurace jednotného přihlašování – Azure Active Directory | Microsoft Docs
description: Tento kurz využívá Azure Portal ke konfiguraci jednotného přihlašování založeného na SAML pro aplikaci s využitím Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: c5e8ed4a78fccce4f3a5c631a99a8729114e5722
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422597"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Jak nakonfigurovat jednotné přihlašování založené na SAML

Po přidání aplikace do podnikových aplikací Azure AD nakonfigurujete nastavení jednotného přihlašování. Tento článek popisuje, jak nakonfigurovat jednotné přihlašování založené na SAML pro aplikaci mimo galerii. 

> [!NOTE]
> Chcete přidat aplikaci Galerie? Podrobné pokyny k instalaci najdete v [seznamu výukových kurzů pro aplikace SaaS](../saas-apps/tutorial-list.md) .

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci mimo galerii *bez psaní kódu*, je nutné mít předplatné nebo Azure AD Premium a aplikace musí podporovat SAML 2,0. Další informace o verzích Azure AD najdete na stránce [ceny Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Před zahájením

- Pokud se aplikace nepřidala do tenanta Azure AD, přečtěte si téma [Přidání aplikace Galerie](add-gallery-app.md) nebo [Přidání aplikace mimo galerii](add-non-gallery-app.md).
- Chcete-li získat správné informace pro následující nastavení, obraťte se na dodavatele aplikace:

    | Základní nastavení konfigurace SAML | Iniciováno zprostředkovatelem přihlašování | Iniciováno pomocí IdP | Popis |
    |:--|:--|:--|:--|
    | Identifikátor (ID entity) | Vyžaduje se pro některé aplikace | Vyžaduje se pro některé aplikace | Jednoznačně identifikuje aplikaci, pro kterou se konfiguruje jednotné přihlašování. Azure AD odešle identifikátor do aplikace jako parametr cílové skupiny tokenu SAML. Očekává se, že aplikace ověří tuto aplikaci. Tato hodnota se také zobrazuje jako ID entity ve všech metadatech SAML poskytovaných aplikací. *Tuto hodnotu můžete najít jako element vystavitele v **AuthnRequest** (žádost SAML) odesílaný aplikací.* |
    | Adresa URL odpovědi | volitelná, | Požadováno | Určuje, kde aplikace očekává přijetí tokenu SAML. Adresa URL odpovědi se také označuje jako adresa URL ACS (Assertion Consumer Service). |
    | Přihlašovací adresa URL | Požadováno | Nezadávejte | Když uživatel otevře tuto adresu URL, poskytovatel služeb ho přesměruje do Azure AD, kde se uživatel ověří a přihlásí. Azure AD používá adresu URL ke spuštění aplikace ze sady Office 365 nebo přístupového panelu Azure AD. Pokud je prázdné, služba Azure AD spoléhá na zprostředkovatele identity, aby spouštěla jednotné přihlašování, když uživatel spustí aplikaci.|
    | Stav přenosu | volitelná, | volitelná, | Určuje pro aplikaci, kam má přesměrovat uživatele po dokončení ověřování. Obvykle je hodnota platnou adresou URL pro aplikaci. Některé aplikace ale používají toto pole odlišně. Další informace vám sdělí dodavatel aplikace.
    | Adresa URL – odhlášení | volitelná, | volitelná, | Slouží k odeslání odpovědí na odhlášení SAML zpět do aplikace.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Krok 1. Úprava základní konfigurace SAML

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce cloudové aplikace nebo správce aplikace pro vašeho TENANTA Azure AD.

1. Přejděte na **Azure Active Directory** > **podnikové aplikace** a vyberte aplikaci ze seznamu. 
   
   - Pokud chcete aplikaci vyhledat, v nabídce **Typ aplikace** vyberte **všechny aplikace**a pak vyberte **použít**. Do vyhledávacího pole zadejte název aplikace a pak vyberte aplikaci z výsledků.

1. V části **Spravovat** vyberte **jednotné přihlašování**. 

1. Vyberte **SAML**. Zobrazí se stránka **nastavit jednotné přihlašování pomocí SAML-Preview** .

1. Pokud chcete upravit základní možnosti konfigurace SAML, vyberte ikonu **Upravit** (tužka) v pravém horním rohu **základní sekce konfigurace SAML** .

     ![Konfigurace certifikátů](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. Do příslušných polí zadejte informace popsané v části [než začnete](#before-you-begin) .

1. V horní části stránky vyberte **Save (Uložit**).

## <a name="step-2-configure-user-attributes-and-claims"></a>Krok 2. Konfigurace uživatelských atributů a deklarací identity 

Aplikace může vyžadovat konkrétní atributy uživatele nebo deklarace identity v tokenu SAML, který obdrží z Azure AD, když se uživatel přihlásí. Například je možné zadat konkrétní identifikátory URI deklarace identity nebo hodnoty deklarací identity nebo může být nutné, aby **název** byl jiný než uživatelské jméno uložené na platformě Microsoft identity. Požadavky na aplikace pro galerii jsou popsány v [kurzech specifických pro aplikaci](../saas-apps/tutorial-list.md)nebo můžete požádat dodavatele aplikace. Obecné kroky pro konfiguraci atributů a deklarací identity uživatele jsou popsané níže.

1. V části **atributy a deklarace identity uživatele** vyberte v pravém horním rohu ikonu pro **Úpravy** (tužka).

1. Ověřte **hodnotu identifikátoru názvu**. Výchozí hodnota je *User. Principal*. Identifikátor uživatele jednoznačně identifikuje každého uživatele v rámci aplikace. Pokud je například uživatelským jménem i jedinečným identifikátorem e-mailová adresa, nastavte tuto hodnotu na *user.mail*.

1. Chcete-li změnit **hodnotu identifikátoru názvu**, vyberte pro pole **hodnota identifikátoru názvu** ikonu **Upravit** (tužka). V případě potřeby proveďte příslušné změny formátu identifikátoru a zdroje. Podrobnosti najdete v tématu [editace NameId](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Až budete hotovi, uložte změny. 
 
1. Pokud chcete nakonfigurovat deklarace identity skupiny, vyberte pro **skupiny vrácené v poli deklarace** ikonu pro **Úpravy** . Podrobnosti najdete v tématu [Konfigurace deklarací identity skupiny](../hybrid/how-to-connect-fed-group-claims.md).

3. Pokud chcete přidat deklaraci identity, vyberte v horní části stránky **Přidat novou deklaraci identity** . Zadejte **název** a vyberte příslušný zdroj. Pokud vyberete zdroj **atributu** , budete muset zvolit **zdrojový atribut** , který chcete použít. Pokud vyberete zdroj **překladu** , budete muset zvolit **transformaci** a **parametr 1** , který chcete použít. Podrobnosti najdete v tématu [Přidání deklarací specifických pro aplikaci](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Až budete hotovi, uložte změny. 

4. Vyberte **Uložit**. Nová deklarace identity se zobrazí v tabulce.

   > [!NOTE]
   > Další způsoby přizpůsobení tokenu SAML z Azure AD do vaší aplikace najdete v následujících zdrojích.
   >- Pokud chcete vytvořit vlastní role prostřednictvím Azure Portal, přečtěte si téma [Konfigurace deklarací identity rolí](../develop/active-directory-enterprise-app-role-management.md).
   >- Informace o přizpůsobení deklarací prostřednictvím PowerShellu najdete v tématu [přizpůsobení deklarací – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Postup úpravy manifestu aplikace pro konfiguraci volitelných deklarací identity aplikace najdete v tématu [konfigurace volitelných deklarací identity](../develop/active-directory-optional-claims.md).
   >- Informace o nastavení zásad životnosti tokenů pro aktualizační tokeny, přístupové tokeny, tokeny relací a tokeny ID najdete v tématu [Konfigurace životností tokenů](../develop/active-directory-configurable-token-lifetimes.md). Nebo pokud chcete omezit ověřovací relace přes podmíněný přístup Azure AD, přečtěte si téma [Možnosti správy relace ověřování](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Krok 3. Správa podpisového certifikátu SAML

Azure AD pomocí certifikátu podepisuje tokeny SAML, které posílá do aplikace. Na stránce **nastavit jednotné přihlašování pomocí SAML** můžete zobrazit nebo stáhnout aktivní certifikát. Můžete také aktualizovat, vytvořit nebo importovat certifikát. V případě aplikací galerie jsou v dokumentaci SAML aplikace k dispozici podrobnosti o formátu certifikátu (viz [kurz pro konkrétní aplikaci](../saas-apps/tutorial-list.md)). 

1. Přejít do části **podpisový certifikát SAML** . V závislosti na typu aplikace uvidíte možnosti stažení certifikátu ve formátu base64, nezpracovaném formátu nebo v XML metadatech federace. Azure AD taky poskytuje **adresu URL federačních metadat aplikace** , kde můžete získat přístup k metadatům specifickým pro aplikaci `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`ve formátu.

1. Chcete-li spravovat, vytvořit nebo importovat certifikát, vyberte ikonu **Upravit** (tužka) v pravém horním rohu části **podpisový certifikát SAML** a proveďte některou z následujících akcí:

   - Chcete-li vytvořit nový certifikát, vyberte možnost **nový certifikát**, vyberte **Datum vypršení platnosti**a pak vyberte **Uložit**. Certifikát aktivujete tak, že vyberete kontextovou nabídku ( **...** ) a vyberete **nastavit certifikát jako aktivní**.
   - Pokud chcete nahrát certifikát s privátním klíčem a přihlašovacími údaji PFX, vyberte **importovat certifikát** a vyhledejte certifikát. Zadejte **heslo PFX**a pak vyberte **Přidat**.  
   - Pokud chcete nakonfigurovat rozšířené možnosti podepisování certifikátů, použijte následující možnosti. Popisy těchto možností najdete v článku [Rozšířené možnosti pro podepsání certifikátu](certificate-signing-options.md) .
      - V rozevíracím seznamu **možnost podepisování vyberte možnost** **podepsat odpověď SAML**, **podepsat kontrolní výraz SAML**nebo **podepsat odpověď a kontrolní výraz SAML**.
      - V rozevíracím seznamu **algoritmus podepisování** vyberte **SHA-1** nebo **SHA-256**.
   - Pokud chcete informovat další lidi, když se aktivní certifikát blíží datu vypršení platnosti, zadejte e-mailové adresy do polí **e-mailových adres pro oznámení** .

1. V části **podpisový certifikát SAML** vyberte **Save (Uložit** ). 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Krok 4. Nastavení aplikace pro použití Azure AD

V části **nastavit \<> ApplicationName** jsou uvedeny hodnoty, které je třeba v aplikaci nakonfigurovat, aby se služba Azure AD používala jako poskytovatel identity SAML. Požadované hodnoty se liší v závislosti na aplikaci. Podrobnosti najdete v dokumentaci SAML aplikace.

1. Přejděte dolů do části **nastavení \<> ApplicationName** . 
2. Podle potřeby Zkopírujte hodnotu z každého řádku v této části a postupujte podle pokynů pro konkrétní aplikaci pro přidání hodnoty do aplikace. V případě aplikací galerie můžete zobrazit dokumentaci tak, že vyberete **Zobrazit podrobné pokyny**. 
   - Hodnoty **adresy** URL pro přihlášení a adresy **URL** pro odhlášení se obě překládají na stejný koncový bod, což je koncový bod pro zpracování požadavků SAML pro vaši instanci Azure AD. 
   - **Identifikátor Azure AD** je hodnota vystavitele v  tokenu SAML, který je vydaný pro aplikaci.
1. Po vložení všech hodnot do příslušných polí vyberte **Uložit**.

## <a name="step-5-validate-single-sign-on"></a>Krok 5. Ověřit jednotné přihlašování

Jste připraveni otestovat nastavení a zjistit, jestli pro vás používá jednotné přihlašování, správce.  

1. Otevřete nastavení jednotného přihlašování pro vaši aplikaci. 
2. Posuňte se do části **ověření jednotného přihlašování <applicationName>**  . Pro tento kurz se v této části říká **Nastavení GitHub-test**.
3. Vyberte **test**. Zobrazí se možnosti testování.
4. Vyberte **Přihlásit se jako aktuální uživatel**. 

Pokud je přihlášení úspěšné, budete připraveni přiřadit uživatele a skupiny do aplikace SAML.
Pokud se zobrazí chybová zpráva, proveďte následující kroky:

1. Zkopírujte podrobnosti a vložte je do pole **Jak chyba vypadá?** .

    ![Pokyny k řešení získáte, když použijete pole "co vypadá chyba jako".](media/configure-single-sign-on-portal/error-guidance.png)

1. Vyberte možnost **získat pokyny k řešení**. Zobrazí se pokyny k hlavní příčině a řešení.  V tomto příkladu uživatel nebyl přiřazen k aplikaci.
1. Přečtěte si pokyny k řešení a pokud je to možné, vyřešte problém.
1. Spusťte test znovu, dokud se úspěšně nedokončí.

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](configure-automatic-user-provisioning-portal.md)
