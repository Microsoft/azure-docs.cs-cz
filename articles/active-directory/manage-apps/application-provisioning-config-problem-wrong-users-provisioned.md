---
title: Pro aplikaci Galerie Azure AD se zřídila nesprávná sada uživatelů
description: Přečtěte si, jak zjistit, proč se v aplikaci zřizuje jiná sada uživatelů než ty, které jste očekávali.
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
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77e8f31bd0adeabea79cd11553fd77a3e5ab6404
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275777"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Pro aplikaci Galerie Azure AD se zřizuje nesprávná sada uživatelů.

Kteří uživatelé jsou zřízeni v aplikaci, jsou primárně řízeni, kteří uživatelé a skupiny jsou **přiřazeni** k aplikaci.

Následující zdroje vám pomohou zjistit, které uživatele a skupiny byly přiřazeny k aplikaci v rámci Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Přiřaďte uživatele přímo jako správce.

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Chcete-li otevřít podokno **Přidat přiřazení** , klikněte na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** .

9. Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, typ v jiném **celý název** nebo **e-mailová adresa** do **hledat podle názvu nebo e-mailová adresa** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním tohoto uživatele do **vybrané** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Pokud je zřizování nakonfigurováno a je pro aplikaci již spuštěno, noví uživatelé by měli být zřízeni do aplikace přibližně po dobu 10 minut. Podrobnosti najdete v **protokolech auditu** .

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Přiřazení skupiny k aplikaci přímo jako správce

Chcete-li přiřadit jednu nebo více skupin k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Chcete-li otevřít podokno **Přidat přiřazení** , klikněte na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** .

9. Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **název celé skupiny** zájem o přiřazení do skupiny **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **skupiny** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo, které chcete přidat uživatele do skupiny **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jednu skupinu**, typ v jiném **název celé skupiny** do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole a Klikněte na zaškrtávací políčko k přidání do této skupiny **vybrané** seznamu.

13. Když jste hotovi s výběrem skupin, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit ke zvoleným skupinám, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko a přiřazení aplikace k vybraným skupinám.

Pokud je zřizování nakonfigurováno a je pro aplikaci již spuštěno, měly by být noví uživatelé obsaženi v rámci skupiny zřízeni v přibližně 10 minutách. Podrobnosti najdete v **protokolech auditu** .

>[!IMPORTANT]
>Zřizování názvu skupiny a podrobností skupiny, a to i u členů, pokud jsou podporovány pro některé aplikace. Tuto funkci můžete povolit nebo zakázat povolením nebo zakázáním **mapování** pro objekty skupiny zobrazené na kartě **zřizování** . 
>
>

Pokud jsou skupiny zřizování povoleny, nezapomeňte zkontrolovat mapování atributů, aby se zajistilo, že se pro "odpovídající ID" používá příslušné pole. Tímto shodným ID může být zobrazované jméno nebo e-mailový alias. Skupina a její členové se nezřídí, pokud je vlastnost odpovídajícího typu prázdná nebo není naplněná pro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)
