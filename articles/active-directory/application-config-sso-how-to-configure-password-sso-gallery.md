---
title: Postup konfigurace hesel jednotné přihlašování pro aplikaci Galerie Azure AD | Microsoft Docs
description: Postup konfigurace aplikace pro zabezpečení založené na heslech jednotné přihlašování, když je již uveden v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8e1d8471d2feb838a6ba3eb08eedc3ca4d30ab07
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Postup konfigurace hesel jednotné přihlašování pro aplikaci Galerie Azure AD

Když přidáte aplikace [Azure AD Application Gallery](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery), máte možnost volby způsob uživatelům se přihlásit k dané aplikaci. Tato volba kdykoli můžete nakonfigurovat tak, že vyberete **jednotné přihlašování** navigační položka na podniková aplikace v [portál Azure](https://portal.azure.com/).

Jeden z jedné přihlašování metod k dispozici je [založené na heslech jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) možnost. To je skvělý způsob, jak začít rychle integrace aplikací do Azure AD a umožňuje:

-   Povolit **jednotné přihlašování pro vaše uživatele** bezpečně ukládání a přehrání uživatelských jmen a hesel pro aplikaci jste integrované s Azure AD

-   **Podpora aplikací, které vyžadují více polí přihlášení** pro aplikace, které vyžadují více než jen uživatelské jméno a heslo pole pro přihlášení

-   **Přizpůsobení popisků** vstupní pole uživatelské jméno a heslo se uživatelům zobrazí na [Panel přístupu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) při jejich zadat své přihlašovací údaje

-   Povolit vaše **uživatelé** zajistit vlastní uživatelská jména a hesla pro všechny existující účty aplikace zadávání v ručně na [aplikace přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Povolit **členem obchodní skupině** a zadat uživatelská jména a hesla přiřazen k uživateli pomocí [samoobslužného přístup k aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funkce

-   Povolit **správce** k určení uživatelských jmen a hesel přiřazen k uživateli pomocí přihlašovacích údajů aktualizace funkcí při [přiřazení uživatele k aplikaci](#assign-a-user-to-an-application-directly)

-   Povolit **správce** k určení sdílené uživatelské jméno nebo heslo použité pro skupinu uživatelů pomocí přihlašovacích údajů aktualizace funkcí při [přiřazení skupiny k aplikaci](#assign-an-application-to-a-group-directly)

Následující část popisuje, jak můžete povolit [založené na heslech jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) k aplikaci, která se již [Azure AD Application Gallery](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

## <a name="overview-of-steps-required"></a>Přehled kroků potřebných
Pro konfiguraci aplikace z Galerie Azure AD, které potřebujete:

-   [Přidat aplikaci z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace aplikace pro heslo jednotné přihlašování](#configure-the-application-for-password-single-sign-on)

-   [Přiřazení aplikace k uživatele nebo skupinu](#assign-the-application-to-a-user-or-a-group)

    -   [Přiřazení uživatele k aplikaci přímo](#assign-a-user-to-an-application-directly)

    -   [Přiřadit přímo aplikaci pro skupinu](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte takto:

1.  Otevřete [portál Azure](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  klikněte **přidat** v pravém horním rohu na tlačítko **podnikové aplikace, které** podokně.

6.  V **zadejte název** textbox z **přidat z Galerie** části, zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace, můžete změnit její název ze **název** textové pole.

9.  Klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

Po krátké době najdete v podokně Konfigurace aplikace.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotné přihlašování

Pokud chcete konfigurovat jednotné přihlašování pro aplikace, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Vyberte režim **založené na heslech přihlášení.**

9.  [Přiřazení uživatelů k aplikaci](#assign-a-user-to-an-application-directly).

10. Kromě toho můžete taky zadat přihlašovací údaje jménem uživatele výběrem řádky uživatelů a kliknete na **pověření aktualizace** a zadání uživatelského jména a hesla jménem uživatele. Uživatelé, jinak se výzva k zadání sami přihlašovací údaje při spuštění.

## <a name="assign-a-user-to-an-application-directly"></a>Přiřazení uživatele k aplikaci přímo

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele k ze seznamu.

7.  Po načtení aplikace, klikněte na **uživatelů a skupin** z levé navigační nabídky.

8.  Klikněte na tlačítko **přidat** tlačítko na **uživatelů a skupin** seznamu otevřete **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele vás zajímá přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu na nich **políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo pro přidání uživatelů do uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, zadejte v jiném **celý název** nebo **e-mailová adresa** do **vyhledávání podle názvu nebo e-mailová adresa** pole pro vyhledávání a klikněte na zaškrtávací políčko, chcete-li přidat tento uživatel **vybrané** seznamu.

13. Po dokončení výběru uživatelů klikněte na **vyberte** tlačítko, které chcete přidat do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** selektor v **přidat přiřazení** podokně vyberte roli přiřadit uživatele, který jste vybrali.

15. Klikněte **přiřadit** tlačítko přiřadit aplikace pro vybraného uživatele.

## <a name="assign-an-application-to-a-group-directly"></a>Přiřadit přímo aplikaci pro skupinu

Jeden nebo více skupin přiřadit přímo k aplikaci, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele k ze seznamu.

7.  Po načtení aplikace, klikněte na **uživatelů a skupin** z levé navigační nabídky.

8.  Klikněte na tlačítko **přidat** tlačítko na **uživatelů a skupin** seznamu otevřete **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

10. Zadejte **název úplné skupiny** vás zajímá přiřazení do skupiny **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **skupiny** v seznamu na nich **políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo pro přidání uživatelů do skupiny **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jednu skupinu**, typ v jiném **název úplné skupiny** do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole a Klikněte na zaškrtávací políčko k přidání do této skupiny **vybrané** seznamu.

13. Po dokončení výběru skupiny klikněte na **vyberte** tlačítko, které chcete přidat do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** klikněte **vybrat roli** selektor v **přidat přiřazení** podokně vyberte role pro přiřazení do skupin, které jste vybrali.

15. Klikněte **přiřadit** tlačítko přiřazení aplikace k vybraným skupinám.

Po krátké době uživatele, které jste vybrali moci spustit tyto aplikace na přístupovém panelu.

## <a name="next-steps"></a>Další postup
[Zadejte jednotné přihlašování pro vaše aplikace s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
