---
title: Potíže při přihlašování k aplikaci mimo Galerii konfigurované pro federované jednotné přihlašování | Dokumentace Microsoftu
description: Pokyny pro specifické problémy mohou nastat při přihlašování k aplikaci konfigurované pro založené na SAML federované jednotné přihlašování s Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: e908e4434645429d97fd38a65f08537f0c9cf5e2
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365452"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Potíže při přihlašování k aplikaci mimo Galerii konfigurované pro federované jednotné přihlašování

Chcete-li vyřešit váš problém, zkontrolujte konfiguraci aplikace ve službě Azure AD následujícím způsobem:

-   Jste provedli všechny kroky konfigurace pro aplikaci Galerie Azure AD.

-   Identifikátor a adresa URL odpovědi, které jsou nakonfigurované v adresáři AAD odpovídat jejich očekávané hodnoty v aplikaci

-   Uživatelé mají přiřazená aplikace

## <a name="application-not-found-in-directory"></a>Aplikace nebyla nalezena v adresáři

*Chyba AADSTS70001: Aplikaci s identifikátorem "https://contoso.com' nebyl nalezen v adresáři*.

**Možná příčina**

Atribut odesílá z aplikace do služby Azure AD v požadavku SAML vystavitele neodpovídá hodnotu Identifier nakonfigurovanou v aplikaci Azure AD.

**Řešení**

Ujistěte se, že atribut Issuer v požadavku SAML se shodovaly s identifikátorem hodnotu nakonfigurovanou v Azure AD:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  <span id="_Hlk477190042" class="anchor"></span>Přejděte na **domény a adresy URL** oddílu. Ověřte, že je hodnota v textovém poli identifikátor odpovídající hodnota pro identifikátor hodnotu zobrazují v chybě.

Po aktualizaci hodnota identifikátoru ve službě Azure AD a odešle hodnotu ho je odpovídající aplikace v požadavku SAML, by měl být schopni se přihlásit k aplikaci.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adresa pro odpovědi neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci. 

*Chyba AADSTS50011: Adresa pro odpovědihttps://contoso.com' neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci* 

**Možná příčina** 

Hodnota AssertionConsumerServiceURL v požadavku SAML neodpovídá hodnotu adresy URL odpovědi nebo vzor nakonfigurované ve službě Azure AD. Hodnota AssertionConsumerServiceURL v požadavku SAML je adresa URL se zobrazí v chybě. 

**Řešení** 

Ujistěte se, že hodnota AssertionConsumerServiceURL v požadavku SAML se shodovaly s příslušnou odpovědní adresu URL hodnotu nakonfigurovanou v Azure AD. 
 
1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.** 

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo. 

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky. 

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory. 

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací. 

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**
  
6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Přejděte na **domény a adresy URL** oddílu. Ověřit nebo aktualizujte hodnotu v textovém poli Adresa URL odpovědi tak, aby odpovídala hodnotě AssertionConsumerServiceURL v požadavku SAML.

  * Pokud textové pole adresy URL odpovědi nevidíte, vyberte **zobrazit pokročilé nastavení URL** zaškrtávací políčko. 

Poté, co jste aktualizovali hodnotu adresy URL odpovědi ve službě Azure AD a odešle hodnotu ho je odpovídající aplikace v požadavku SAML, by měl být schopni se přihlásit k aplikaci.

## <a name="user-not-assigned-a-role"></a>Uživatel není přiřazený k roli

*Chyba AADSTS50105: Přihlášeného uživatele 'brian@contoso.com"není přiřazen k roli pro aplikaci*

**Možná příčina**

Uživateli nebyl udělen přístup k aplikaci ve službě Azure AD.

**Řešení**

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** levé navigační nabídce aplikace.

8.  Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, typ v jiném **celý název** nebo **e-mailová adresa** do **hledat podle názvu nebo e-mailová adresa** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním tohoto uživatele do **vybrané** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="not-a-valid-saml-request"></a>Není platný SAML požadavek

*Chyba AADSTS75005: Požadavek není platná zpráva protokolu typu Saml2.*

**Možná příčina**

Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Mezi běžné problémy patří:

-   Chybí povinná pole. v požadavku SAML

-   Požadavek SAML zakódoval metodu

**Řešení**

1.  Zaznamenejte požadavek SAML. postupujte podle kurzu [ladění založené na SAML jednotného přihlašování k aplikacím ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) informace o zaznamenání požadavku SAML.

2.  Obraťte se na dodavatele aplikace a sdílené složky:

    -   Požadavek SAML

    -   [Protokol požadavky služby Azure AD Single Sign-on SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

By měl ověřit podporují implementace Azure AD SAML pro jednotné přihlašování.

## <a name="no-resource-in-requiredresourceaccess-list"></a>V seznamu requiredResourceAccess se žádný prostředek

*Chyba AADSTS65005: Klientská aplikace požadovala přístup k prostředku "00000002-0000-0000-c000-000000000000'. Tento požadavek se nezdařil, protože klient nebyl zadán tento prostředek ve svém seznamu requiredResourceAccess*.

**Možná příčina**

Objekt aplikace je poškozený.

**Řešení**

Chcete-li problém vyřešit, odeberte z adresáře aplikace. Pak přidejte a změnit konfiguraci aplikace, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Klikněte na tlačítko **odstranit** v levé horní části aplikace **přehled** podokně.

8.  Aktualizace služby Azure AD a přidat aplikaci z Galerie Azure AD. Pak nakonfigurujte aplikaci znovu.

Po změně konfigurace aplikace, by měl být schopni se přihlásit k aplikaci.

## <a name="certificate-or-key-not-configured"></a>Certifikát nebo klíč není nakonfigurováno

Chyba AADSTS50003: Žádný podpisový klíč nakonfigurován.

**Možná příčina**

Poškozený objekt aplikace a služby Azure AD nerozpoznal certifikátu nakonfigurovanému pro aplikaci.

**Řešení**

Pokud chcete odstranit a vytvořit nový certifikát, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Klikněte na tlačítko **vytvořit nový certifikát** pod **podpisového certifikátu SAML** oddílu.

9.  Vyberte datum vypršení platnosti. Potom klikněte na **uložit.**

10. Zkontrolujte **nastavit nový certifikát jako aktivní** přepsat aktivní certifikát. Potom klikněte na **Uložit** v horní části podokna a přijměte aktivaci certifikátu výměny.

11. V části **podpisový certifikát SAML** klikněte na tlačítko **odebrat** odebrat **nepoužitý** certifikátu.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problém při přizpůsobování deklarací identity SAML, odesílá se do aplikace

Informace o přizpůsobení deklarací identity atributu SAML odeslán do vaší aplikace najdete v tématu [mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Protokol požadavky služby Azure AD Single Sign-on SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
