---
title: Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci Galerie Azure AD | Microsoft Docs
description: Jak nakonfigurovat aplikaci pro zabezpečené jednotné přihlašování založené na heslech, když je už uvedená v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: eb37fe247901b799a845ce75723a4a6b535cbb28
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422577"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci Galerie Azure AD

Když přidáte aplikaci z [Galerie aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), můžete zvolit, jak chcete, aby se uživatelé k této aplikaci přihlásili. Tuto volbu můžete kdykoli nakonfigurovat tak, že v [Azure Portal](https://portal.azure.com/)vyberete navigační položku **jednotného přihlašování** pro podnikovou aplikaci.

Jednou z dostupných metod jednotného přihlašování je možnost [jednotného přihlašování založené](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) na heslech. Jedná se o skvělý způsob, jak rychle začít s integrací aplikací do Azure AD a umožní vám:

-   Povolení **jednotného přihlašování pro uživatele** díky bezpečnému ukládání a přehrávání uživatelských jmen a hesel pro aplikaci, kterou jste integrací se službou Azure AD.

-   **Podpora aplikací, které vyžadují více** polí přihlašování pro aplikace, které pro přihlášení vyžadují víc než jenom uživatelské jméno a heslo

-   **Přizpůsobení popisků** vstupních polí uživatelského jména a hesla, která se uživatelům zobrazí na [přístupovém panelu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) při zadání přihlašovacích údajů

-   Umožněte **uživatelům** zadání vlastních uživatelských jmen a hesel pro všechny existující účty aplikací, které na [přístupovém panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) zadávají ručně.

-   Umožňuje **členům obchodní skupiny** zadat uživatelská jména a hesla přiřazená uživateli pomocí funkce [Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) .

-   Umožňuje **Správci** zadat uživatelská jména a hesla přiřazená uživateli pomocí funkce aktualizovat přihlašovací údaje při [přiřazování uživatele k aplikaci](#assign-a-user-to-an-application-directly) .

-   Umožňuje **Správci** zadat sdílené uživatelské jméno nebo heslo používané skupinou uživatelů pomocí funkce aktualizovat přihlašovací údaje při [přiřazování skupiny k aplikaci](#assign-an-application-to-a-group-directly) .

Následující část popisuje, jak můžete povolit [jednotné přihlašování založené](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) na heslech k aplikaci, která je už v [galerii aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-steps-required"></a>Přehled požadovaných kroků
Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   [Přidání aplikace z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace aplikace pro jednotné přihlašování k heslům](#configure-the-application-for-password-single-sign-on)

-   Přiřazení aplikace uživateli nebo skupině

    -   [Přiřazení uživatele k aplikaci přímo](#assign-a-user-to-an-application-directly)

    -   [Přiřazení aplikace ke skupině přímo](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle těchto kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce** .

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  Do textového pole **Zadejte název** z části **Přidat z Galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **název** .

9.  Kliknutím na tlačítko **Přidat** přidejte aplikaci.

Po krátké době uvidíte podokno konfigurace aplikace.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

Pro konfiguraci jednotného přihlašování pro aplikaci použijte následující postup:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Vyberte možnost režim **přihlašování založené na heslech.**

9. [Přiřaďte uživatele k aplikaci](#assign-a-user-to-an-application-directly).

10. Kromě toho můžete přihlašovací údaje jménem uživatele zadat také tak, že vyberete řádky uživatelů a kliknete na **Aktualizovat přihlašovací údaje** a zadáte uživatelské jméno a heslo jménem uživatele. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů sami při spuštění.

## <a name="assign-a-user-to-an-application-directly"></a>Přiřazení uživatele k aplikaci přímo

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9. Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele **vybrané** seznamu.

12. **Volitelné** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e** -mailová adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

## <a name="assign-an-application-to-a-group-directly"></a>Přiřazení aplikace ke skupině přímo

Chcete-li přiřadit jednu nebo více skupin k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9. Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **název celé skupiny** zájem o přiřazení do skupiny **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **skupiny** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo, které chcete přidat uživatele do skupiny **vybrané** seznamu.

12. **Volitelné** Chcete-li **Přidat více než jednu skupinu**, zadejte do pole **Hledat podle jména nebo e-mailové adresy** další **název celé skupiny** a kliknutím na zaškrtávací políčko přidejte tuto skupinu do **vybraného** seznamu.

13. Když jste hotovi s výběrem skupin, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit ke zvoleným skupinám, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko a přiřazení aplikace k vybraným skupinám.

Po krátké době uživatelé, které jste vybrali, budou moci spouštět tyto aplikace na přístupovém panelu.

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
