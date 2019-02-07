---
title: Postup konfigurace hesel jednotného přihlašování pro aplikaci mimo Galerii applicationn | Dokumentace Microsoftu
description: Jak nakonfigurovat vlastní aplikaci mimo galerii pro zabezpečení založené na heslech jednotného přihlašování, když není uvedená v galerii aplikací Azure AD
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: barbkess
ms.openlocfilehash: 934996f1573520a6fba92ce09f8a14fc4795de6c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812455"
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Postup konfigurace hesel jednotného přihlašování pro aplikaci mimo Galerii

Kromě možností najdete v galerii aplikací Azure AD, máte také možnost přidávat **aplikaci mimo galerii** při požadovanou aplikaci není v seznamu uvedeno. Díky této funkci můžete přidat jakékoli aplikace, která již existuje ve vaší organizaci nebo jakékoli aplikaci třetí strany, který můžete použít od dodavatele, který je již součástí [Galerie aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jakmile přidáte aplikaci mimo galerii, pak můžete nakonfigurovat jednotné přihlašování metodu tato aplikace používá tak, že vyberete **Single Sign-on** navigační položka na podniková aplikace v [webuAzureportal](https://portal.azure.com/).

Jedním z Single Sign-on metody k dispozici, je [založené na heslech Single Sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) možnost. S **přidat aplikaci mimo galerii** prostředí, můžete integrovat všechny aplikace, který vykreslí založeného na HTML uživatelské jméno a heslo vstupní pole, i když není v naší sadě předem integrovaných aplikací.

Způsob, jakým tento postup funguje, je pomocí stránky automatizované získávání dat technologie, která je součástí přístupový Panel rozšíření, která umožňuje automaticky detekovat uživatelské jméno a heslo vstupní pole, uložit je bezpečně pro vaši instanci konkrétní aplikaci. Bezpečně přehrát uživatelských jmen a hesel, která se těchto polí, když uživatel přejde na tuto aplikaci na přístupovém panelu aplikací.

To je skvělý způsob, jak začít rychle integrace jakékoliv aplikaci do služby Azure AD a umožňuje:

-   Integrace **libovolné aplikace na světě** v tenantu Azure AD, pokud je vhodné uživatelské jméno a heslo vstupní pole HTML

-   Povolit **jednotné přihlašování pro vaše uživatele** bezpečně ukládat a přehráním uživatelských jmen a hesel pro aplikaci jste integrovali se službou Azure AD

-   **Automaticky rozpoznat vstup** pole pro libovolnou aplikaci a umožňují ručně zjišťovat těchto polí pomocí rozšíření prohlížeče přístupového panelu, v případě, že automatické zjišťování je nenajde

-   **Podpora aplikací, které vyžadují více polí přihlášení** pro aplikace, které vyžadují víc než jenom pole jméno a heslo pro přihlášení

-   **Přizpůsobení popisků** vstupní pole uživatelské jméno a heslo se uživatelům zobrazí na [přístupového panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) při zadání přihlašovacích údajů

-   Povolit vaše **uživatelé** poskytnout vlastní uživatelská jména a hesla pro zadávání v ručně na existující účty aplikace [přístupového panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Povolit **členem skupiny business** zadat uživatelská jména a hesla, které jsou přiřazeny uživateli přes [samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funkce

-   Povolit **správce** k určení uživatelských jmen a hesel uživateli přidělena pomocí funkce aktualizace přihlašovacích údajů při přiřazení uživatele k aplikaci

-   Povolit **správce** k určení sdílené uživatelské jméno nebo heslo používané skupinou uživatelů pomocí přihlašovacích údajů pro aktualizace funkcí při [přiřazení skupiny k aplikaci](#assign-an-application-to-a-group-directly)

Následující část popisuje, jak můžete zajistit [založené na heslech Single Sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) do všech aplikací, přidat direktivu using **přidat aplikaci mimo galerii** prostředí.

## <a name="overview-of-steps-required"></a>Přehled kroků potřebných

Konfigurace aplikace z Galerie Azure AD, které je potřeba:

-   [Přidat aplikaci mimo Galerii](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro heslo jednotného přihlašování](#configure-the-application-for-password-single-sign-on)

-   Přiřazení aplikace k uživateli nebo skupině

    -   [Přiřadit uživatele k aplikaci přímo](#assign-a-user-to-an-application-directly)

    -   [Přímo přiřadit aplikace do skupiny](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo Galerii

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte takto:

1.  Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  Klikněte na tlačítko **aplikace mimo galerii**.

7.  Zadejte název vaší aplikace v **název** textového pole. Vyberte **přidat.**

Po krátké době budete moci zobrazit podokno konfiguračních vaší aplikace.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte režim **přihlašování na základě heslo.**

9.  Zadejte **přihlašovací adresa URL**. Toto je adresa URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení k aplikaci. Ujistěte se, že pole přihlášení jsou viditelné na adrese URL.

10. Přiřazení uživatelů k aplikaci.

11. Kromě toho můžete také zadat přihlašovací údaje jménem uživatele výběr řádků uživatele a kliknutím na **aktualizaci přihlašovacích údajů** a zadáním uživatelského jména a hesla jménem uživatele. V opačném případě uživatelé vyzváni k zadání samotné přihlašovací údaje při spuštění.


## <a name="assign-a-user-to-an-application-directly"></a>Přiřadit uživatele k aplikaci přímo

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** levé navigační nabídce aplikace.

8.  Otevřete **přidat přiřazení** podokně klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu.

9.  Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, typ v jiném **celý název** nebo **e-mailová adresa** do **hledat podle jména nebo e-mailové adresy** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním tohoto uživatele do **vybrané** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

## <a name="assign-an-application-to-a-group-directly"></a>Přímo přiřadit aplikace do skupiny

Jeden nebo více skupin přiřadit přímo k aplikaci, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** levé navigační nabídce aplikace.

8.  Otevřete **přidat přiřazení** podokně klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu.

9.  Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **název celé skupiny** zájem o přiřazení do skupiny **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **skupiny** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo, které chcete přidat uživatele do skupiny **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jednu skupinu**, typ v jiném **název celé skupiny** do **hledat podle jména nebo e-mailové adresy** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním této skupiny Chcete **vybrané** seznamu.

13. Když jste hotovi s výběrem skupin, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit ke zvoleným skupinám, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko a přiřazení aplikace k vybraným skupinám.

Po krátké době uživatele, které jste vybrali dát spustit tyto aplikace na přístupovém panelu.

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
