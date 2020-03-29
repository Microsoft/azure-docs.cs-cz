---
title: Přiřazení licencí skupině – Azure Active Directory | Dokumenty společnosti Microsoft
description: Jak přiřadit licence uživatelům pomocí licencování skupin azure active directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253062"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Přiřazení licencí uživatelům podle členství ve skupině Azure Active Directory

Tento článek vás provede přiřazením licencí produktů skupině uživatelů a ověřením, že mají správně licenci ve službě Azure Active Directory (Azure AD).

V tomto příkladu klient obsahuje skupinu zabezpečení s názvem **HR oddělení**. Do této skupiny patří všichni členové personálního oddělení (přibližně 1 000 uživatelů). Chcete přiřadit licence Office 365 Enterprise E3 celému oddělení. Služba Yammer Enterprise, která je součástí produktu, musí být dočasně zakázána, dokud nebude oddělení připraveno k jeho používání. Chcete také nasadit licence Enterprise Mobility + Security pro stejnou skupinu uživatelů.

> [!NOTE]
> Některé služby Microsoft nejsou dostupné ve všech umístěních. Před přiřazením licence uživateli musí správce zadat vlastnost Umístění použití uživatele.
>
> Pro přiřazení skupinové licence zdědí umístění adresáře všichni uživatelé bez zadaného umístění použití. Pokud máte uživatele na více místech, doporučujeme vždy nastavit umístění využití jako součást toku vytváření uživatelů ve službě Azure AD (např. prostřednictvím konfigurace AAD Connect) – což zajišťuje, že výsledek přiřazení licence je vždy správný a uživatelé neobdrží služby v místech, která nejsou povolena.

## <a name="step-1-assign-the-required-licenses"></a>Krok 1: Přiřazení požadovaných licencí

1. Přihlaste se k [**Centru pro správu Azure AD**](https://aad.portal.azure.com) pomocí účtu správce licencí. Chcete-li spravovat licence, musí být účtem správce licence, správce uživatelů nebo globální správce.

1. Výběrem **možnosti Licence** otevřete stránku, kde můžete zobrazit a spravovat všechny licencovatelné produkty v tenantovi.

1. V části **Všechny produkty**vyberte vyberte vyberte office 365 Enterprise E5 i Enterprise Mobility + Security E3 výběrem názvů produktů. Chcete-li zahájit přiřazení, vyberte **Přiřadit** v horní části stránky.

   ![Vyberte produkty, které mají přiřazovat licence.](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Na stránce **Přiřadit licenci** vyberte **Uživatelé a skupiny,** chcete-li otevřít seznam uživatelů a skupin.

1. Vyberte uživatele nebo skupinu a potom výběr potvrďte pomocí tlačítka **Vybrat** v dolní části stránky.

1. Na stránce **Přiřadit licenci** klikněte na **Možnosti přiřazení**, který zobrazuje všechny plány služeb zahrnuté ve dvou produktech, které jsme vybrali dříve. Najděte **Yammer Enterprise** a **vypněte ho,** abyste tuto službu zakázali z licence produktu. Potvrďte klepnutím na **tlačítko OK** v dolní části **možnosti licence**.

   ![vybrat plány služeb pro licence](./media/licensing-groups-assign/assignment-options.png)
  
1. Pokud chcete přiřazení dokončit, klikněte na stránce **Přiřadit licenci** v dolní části stránky na **Přiřadit.**

1. V pravém horním rohu se zobrazí oznámení, které zobrazuje stav a výsledek procesu. Pokud přiřazení ke skupině nebylo možné dokončit (například z důvodu již existujících licencí ve skupině), klepnutím na tlačítko oznámení zobrazíte podrobnosti o chybě.

Při přiřazování licencí ke skupině Azure AD zpracuje všechny existující členy této skupiny. Tento proces může nějakou dobu trvat a měnit se podle velikosti skupiny. Další krok popisuje, jak ověřit, že proces byl dokončen a zjistit, zda je třeba další pozornost k vyřešení problémů.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Krok 2: Ověření, zda bylo počáteční přiřazení dokončeno

1. Přejděte na**skupiny služby** **Azure Active Directory** > . Vyberte skupinu, ke které byly přiřazeny licence.

1. Na stránce **skupiny**vyberte Licence . To vám umožní rychle potvrdit, zda byly licence plně přiřazeny uživatelům a zda existují nějaké chyby, které je třeba prohledat. K dispozici jsou tyto informace:

   - Licence služeb, které jsou aktuálně přiřazeny ke skupině. Výběrem položky zobrazíte konkrétní služby, které byly povoleny, a provedete změny.

   - Aktualizace stavu nejnovějších změn licencí, které jsou k dispozici v případě, že jsou změny zpracovávány nebo pokud bylo zpracování dokončeno pro všechny členy uživatele.

   - Informace o přiřazeníuživatelských licencích, která jsou v chybovém stavu.

   ![chyby licencí a stav licence](./media/licensing-groups-assign/assignment-errors.png)

1. Podrobnější informace o zpracování licencí v části Uživatelé **služby Azure active Directory** > **a protokoly** > *názvů* > skupin skupin**y Audit .** Zkontrolujte následující činnosti:

   - Aktivita: `Start applying group based license to users`. To je zaznamenána, když systém vyzvedne změnu přiřazení licence ve skupině a začne ji aplikovat na všechny členy uživatele. Obsahuje informace o změně, která byla provedena.

   - Aktivita: `Finish applying group based license to users`. To je zaznamenána po dokončení zpracování systému všechny uživatele ve skupině. Obsahuje souhrn počtu uživatelů, kteří byli úspěšně zpracováni a kolik uživatelů nelze přiřadit skupinové licence.

   Další informace o tom, jak lze protokoly auditu použít k analýze změn provedených pomocí skupinových licencí, naleznete v [této části.](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity)

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Krok 3: Kontrola problémů s licencí a jejich řešení

1. Přejděte do skupin **služby Azure Active Directory** > **a**vyhledejte skupinu, ke které byly přiřazeny licence.
1. Na stránce **skupiny**vyberte Licence . Oznámení v horní části stránky ukazuje, že existuje 10 uživatelů, kterým nelze přiřadit licence. Otevřete jej, chcete-li zobrazit seznam všech uživatelů ve stavu chyby licencování pro tuto skupinu.
1. Sloupec **Neúspěšná přiřazení** nám říká, že obě licence produktu nelze přiřadit uživatelům. **Hlavní důvod selhání** sloupec obsahuje příčinu selhání. V tomto případě je **konfliktní plány služeb**.

   ![licence, které nelze přiřadit](./media/licensing-groups-assign/failed-assignments.png)

1. Vyberte uživatele, chcete-li otevřít stránku **Licence** uživatele. Na této stránce jsou zobrazeny všechny licence, které jsou aktuálně přiřazeny uživateli. V tomto příkladu má uživatel licenci Office 365 Enterprise E1, která byla zděděna ze **skupiny Uživatelé veřejného terminálu.** To je v rozporu s licencí E3, kterou se systém pokusil použít ze skupiny **hr oddělení.** V důsledku toho nebyla uživateli přiřazena žádná z licencí z této skupiny.

   ![Zobrazit všechny konflikty licencí pro uživatele](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Chcete-li tento konflikt vyřešit, odeberte uživatele ze skupiny **Uživatelé veřejného terminálu.** Po Azure AD zpracovává změny, hr **oddělení** licence jsou správně přiřazeny.

## <a name="next-steps"></a>Další kroky

Další informace o sadě funkcí pro přiřazení licencí pomocí skupin naleznete v následujících článcích:

- [Co je skupinové licencování ve službě Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
- [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
- [Jak migrovat uživatele mezi licencemi produktů pomocí skupinových licencí ve službě Azure Active Directory](licensing-groups-change-licenses.md)
- [Další scénáře licencování na základě skupin v Azure Active Directory](../active-directory-licensing-group-advanced.md)
- [Příklady Prostředí PowerShell pro skupinové licencování ve službě Azure Active Directory](licensing-ps-examples.md)
