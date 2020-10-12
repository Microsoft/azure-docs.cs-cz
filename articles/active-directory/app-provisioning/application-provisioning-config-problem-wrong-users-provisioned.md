---
title: Pro aplikaci Galerie Azure AD se zřídila nesprávná sada uživatelů
description: Přečtěte si, jak zjistit, proč se v aplikaci zřizuje jiná sada uživatelů než ty, které jste očekávali.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 5a109f1a06e7ee8aff3e455c009217ff670d2781
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84782207"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Pro aplikaci Galerie Azure AD se zřizuje nesprávná sada uživatelů.

Kteří uživatelé jsou zřízeni v aplikaci, jsou primárně řízeni, kteří uživatelé a skupiny jsou **přiřazeni** k aplikaci.

Následující zdroje vám pomohou zjistit, které uživatele a skupiny byly přiřazeny k aplikaci v rámci Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Přiřaďte uživatele přímo jako správce.

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ze které chcete uživatele přiřadit.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Chcete-li otevřít podokno **Přidat přiřazení** , klikněte na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** .

9. v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a **příjmení** nebo **e-mailovou adresu** uživatele, kterého se zajímáte o přiřazení.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **uživatele** v seznamu. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga uživatele přidáte uživatele do **vybraného** seznamu.

12. **Volitelné:** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e-mailová** adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** kliknutím na výběr **role** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte vybraným uživatelům.

Pokud je zřizování nakonfigurováno a je pro aplikaci již spuštěno, noví uživatelé by měli být zřízeni do aplikace přibližně po dobu 10 minut. Podrobnosti najdete v **protokolech auditu** .

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Přiřazení skupiny k aplikaci přímo jako správce

Chcete-li přiřadit jednu nebo více skupin k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ze které chcete uživatele přiřadit.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Chcete-li otevřít podokno **Přidat přiřazení** , klikněte na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** .

9. v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte **úplný název skupiny** , které vás zajímá.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **skupinu** v seznamu. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga skupiny přidáte uživatele do **vybraného** seznamu.

12. **Volitelné:** Chcete-li **Přidat více než jednu skupinu**, zadejte do pole **Hledat podle jména nebo e-mailové adresy** další **název celé skupiny** a kliknutím na zaškrtávací políčko přidejte tuto skupinu do **vybraného** seznamu.

13. Po dokončení výběru skupin klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** Pokud chcete vybrat roli, kterou chcete přiřadit vybraným skupinám, klikněte v podokně **Přidat přiřazení** na výběr **role vybrat** .

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte k vybraným skupinám.

Pokud je zřizování nakonfigurováno a je pro aplikaci již spuštěno, měly by být noví uživatelé obsaženi v rámci skupiny zřízeni v přibližně 10 minutách. Podrobnosti najdete v **protokolech auditu** .

>[!IMPORTANT]
>Zřizování názvu skupiny a podrobností skupiny, a to i u členů, pokud jsou podporovány pro některé aplikace. Tuto funkci můžete povolit nebo zakázat povolením nebo zakázáním **mapování** pro objekty skupiny zobrazené na kartě **zřizování** . 
>
>

Pokud jsou skupiny zřizování povoleny, nezapomeňte zkontrolovat mapování atributů, aby se zajistilo, že se pro "odpovídající ID" používá příslušné pole. Tímto shodným ID může být zobrazované jméno nebo e-mailový alias. Skupina a její členové se nezřídí, pokud je vlastnost odpovídajícího typu prázdná nebo není naplněná pro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování a rušení uživatelů pro aplikace SaaS pomocí Azure Active Directory](user-provisioning.md)
