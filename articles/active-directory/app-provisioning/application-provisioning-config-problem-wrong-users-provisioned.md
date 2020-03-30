---
title: Do aplikace Azure AD Gallery se zřát nesprávná sada uživatelů
description: Zjistěte, jak zjistit, proč se do aplikace zřává jiná sada uživatelů, než jste očekávali.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c94388011605da73666e82011bb8ef56d2af8d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522777"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nesprávná sada uživatelů se zřaží do aplikace Azure AD Gallery

Kteří uživatelé jsou zřízeni do aplikace je primárně řídí, které uživatelé a skupiny byly **přiřazeny** k aplikaci.

Pomocí následujících prostředků se dozvíte, jak zkontrolovat, kteří uživatelé a skupiny byly přiřazeny k aplikaci ve službě Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Přiřazení uživatele přímo jako správce

Chcete-li k aplikaci přiřadit jednoho nebo více uživatelů přímo, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** z levé navigační nabídky aplikace.

8. Podokno **Přidat přiřazení otevřete** kliknutím na tlačítko **Přidat** nad seznamem **Uživatelé a skupiny.**

9. V podokně **Přidat přiřazení** klikněte na volič uživatelů **a skupin.**

10. Zadejte **celé jméno** nebo **e-mailovou adresu** uživatele, kterého chcete přiřadit do vyhledávacího pole **vyhledávání podle jména nebo e-mailové adresy.**

11. Najeďte na **uživatele** v seznamu a zvedejte **zaškrtávací políčko**. Kliknutím na zaškrtávací políčko vedle profilové fotky nebo loga uživatele přidáte uživatele do seznamu **Vybrané.**

12. **Nepovinné:** Pokud chcete **přidat více uživatelů**, zadejte do vyhledávacího pole Hledat podle jména nebo **e-mailové adresy** jiné celé **jméno** nebo **e-mailovou adresu** a kliknutím na toto políčko přidejte tohoto uživatele do seznamu **Vybrané.**

13. Po dokončení výběru uživatelů je klepnutím na tlačítko **Vybrat** přidejte do seznamu uživatelů a skupin, které mají být přiřazeny k aplikaci.

14. **Volitelné:** Kliknutím na volič **role v** podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Klepnutím na tlačítko **Přiřadit** přiřadíte aplikaci vybraným uživatelům.

Pokud je zřizování nakonfigurované a již spuštěné pro aplikaci, noví uživatelé by měli být zřízeni do aplikace přibližně za 10 minut. Podrobnosti naleznete v **protokolech auditu.**

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Přiřazení skupiny přímo k aplikaci jako správce

Chcete-li aplikaci přiřadit jednu nebo více skupin přímo, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** z levé navigační nabídky aplikace.

8. Podokno **Přidat přiřazení otevřete** kliknutím na tlačítko **Přidat** nad seznamem **Uživatelé a skupiny.**

9. V podokně **Přidat přiřazení** klikněte na volič uživatelů **a skupin.**

10. Zadejte **úplný název skupiny** skupiny, kterou chcete přiřadit do vyhledávacího pole **Hledat podle jména nebo e-mailové adresy.**

11. Najeďte přes **skupinu** v seznamu a zvedejte **zaškrtávací políčko**. Kliknutím na zaškrtávací políčko vedle profilové fotky nebo loga skupiny přidáte uživatele do seznamu **Vybrané.**

12. **Nepovinné:** Pokud chcete **přidat více než jednu skupinu**, zadejte do vyhledávacího pole Hledat podle názvu nebo **e-mailové adresy** jiný úplný název **skupiny** a kliknutím na toto políčko přidejte tuto skupinu do seznamu **Vybrané.**

13. Po dokončení výběru skupin je klepnutím na tlačítko **Vybrat** přidejte do seznamu uživatelů a skupin, které mají být přiřazeny k aplikaci.

14. **Volitelné:** Klepnutím na volič **role v** podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným skupinám.

15. Klepnutím na tlačítko **Přiřadit** přiřadíte aplikaci vybraným skupinám.

Pokud je zřizování nakonfigurované a již spuštěné pro aplikaci, noví uživatelé obsažené ve skupině by měly být zřízeny do aplikace přibližně za 10 minut. Podrobnosti naleznete v **protokolech auditu.**

>[!IMPORTANT]
>Zřizování názvu skupiny a podrobnosti skupiny, kromě členů, pokud je podporovánpro některé aplikace. Tuto funkci můžete povolit nebo zakázat povolením nebo zakázáním mapování **pro** objekty skupiny zobrazené na kartě **Zřizování.** 
>
>

Pokud zřizování skupiny je povolena, nezapomeňte zkontrolovat mapování atributů, aby zajistily příslušné pole se používá pro "odpovídající ID". Toto odpovídající ID může být zobrazované jméno nebo e-mailový alias. Skupina a její členové nejsou zřízeny, pokud odpovídající vlastnost je prázdný nebo není naplněnpro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)
