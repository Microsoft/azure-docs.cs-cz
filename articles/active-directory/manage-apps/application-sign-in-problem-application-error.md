---
title: Chyba na stránce aplikace po přihlášení | Dokumentace Microsoftu
description: Jak řešit problémy pomocí přihlášení Azure AD při samotná aplikace generuje chybu
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
ms.openlocfilehash: 4cce49509a452153815c845d9ab72a1b4a8a5b7f
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356378"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Chyba na stránce aplikace po přihlášení

V tomto scénáři přihlášení uživatele v Azure AD, ale aplikace se zobrazuje chyba neumožňuje uživateli úspěšně dokončit tok přihlášení. V tomto scénáři aplikace nepřijímá odpovědi problém ve službě Azure AD.

Existuje několik možných důvodů, proč aplikace nepřijala odpověď ze služby Azure AD. Pokud chyby v aplikaci není dostatečně vymazat vědět, co chybí v odpovědi, pak:

-   Pokud aplikace je Galerie Azure AD, ověření, jste provedli všechny kroky v následujícím článku [ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Pomocí některého nástroje, například [Fiddler](http://www.telerik.com/fiddler) zaznamenat požadavek SAML, odpověď SAML a tokenu SAML.

-   Sdílejte odpověď SAML se na dodavatele aplikace a vědět, co chybí.

## <a name="missing-attributes-in-the-saml-response"></a>Chybějící atributy v odpověď SAML

Chcete-li přidat atribut v konfiguraci Azure AD se odešle odpověď na Azure AD, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Klikněte na tlačítko **zobrazit a upravit všechny ostatní uživatele atributů v rámci** **atributy uživatele** části, chcete-li upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   * Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   * Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

9.  Uložte konfiguraci.

Při příštím přihlášení uživatele k aplikaci Azure AD pošle nový atribut v odpověď SAML.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>Aplikace očekává, že jiný identifikátor uživatele value nebo format

Přihlášení do aplikace se nezdařilo vzhledem k tomu, že odpověď SAML chybí atributům, jako je role, nebo protože aplikace je očekáván jiný formát pro atribut EntityID.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Přidáte atribut v konfiguraci aplikace Azure AD:

Chcete-li změnit hodnotu identifikátoru uživatele, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  V části **atributy uživatele**, vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu.

## <a name="change-entityid-user-identifier-format"></a>Změna formátu EntityID (identifikátor uživatele)

Pokud aplikace očekává, že jiný formát pro atribut EntityID. Potom nebudete moci vybrat formát EntityID (identifikátor uživatele), který Azure AD se odesílá do aplikace v odpovědi po ověření uživatele.

Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě hodnoty vybrané nebo formátu požadovaná aplikací v SAML AuthRequest. Další informace najdete v článku [protokol jednotné přihlašování SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>Aplikace očekává, že jiný podpis metody pro odpověď SAML

Chcete-li změnit, které části tokenu SAML se službou Azure Active Directory digitálně podepsané. Postupujte následovně:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Klikněte na tlačítko **zobrazit pokročilé nastavení podepisování certifikátu** pod **podpisový certifikát SAML** oddílu.

9.  Vyberte příslušné **podepisování možnost** očekává aplikací:

  * Podepsat odpověď SAML

  * Podepsat odpověď a kontrolní výraz SAML

  * Podepsat kontrolní výraz SAML

Při příštím přihlášení do aplikace, služby Azure AD podepsat část odpověď SAML vybrali.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>Aplikace očekává podpisový algoritmus SHA-1

Ve výchozím nastavení Azure AD podepisuje pomocí algoritmu většina zabezpečení tokenu SAML. Změna přihlašování algoritmus SHA-1 se nedoporučuje, pokud požadované aplikací.

Chcete-li změnit podpisový algoritmus, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Klikněte na tlačítko **zobrazit pokročilé nastavení podepisování certifikátu** pod **podpisový certifikát SAML** oddílu.

9.  Vyberte algoritmus SHA-1, v **podpisový algoritmus**.

Při příštím přihlášení do aplikace, služby Azure AD přihlášení pomocí algoritmu SHA-1 tokenu SAML.

## <a name="next-steps"></a>Další postup
[Ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
