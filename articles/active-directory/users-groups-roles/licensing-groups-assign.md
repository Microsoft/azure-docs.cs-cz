---
title: Přiřazení licencí ke skupině ve službě Azure Active Directory | Dokumentace Microsoftu
description: Jak se přiřazují licence uživatelům prostřednictvím Azure Active Directory skupinu licensing
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 10/29/2018
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e08ca3453cc43fa0f35102ca5563b4b07ce45dea
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215000"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Přiřazení licencí pro uživatele na základě členství ve skupinách v Azure Active Directory

Tento článek vás provede přiřazení licence na produkty pro skupinu uživatelů ve službě Azure Active Directory (Azure AD) a pak ověřuje, že máte licenci správně.

V tomto příkladu tenant obsahuje skupinu zabezpečení s názvem **Personální oddělení**. Tato skupina obsahuje všechny členové oddělení lidských zdrojů (přibližně 1 000 uživatelů). Chcete přiřadit licence Office 365 Enterprise E3 celé oddělení. Služba Yammer Enterprise, která je součástí produktu musí dočasně zakázané, dokud oddělení připravený začít používat. Chcete nasadit řešení Enterprise Mobility + Security koupím na stejnou skupinu uživatelů.

> [!NOTE]
> Některé služby Microsoft nejsou dostupné ve všech umístěních. Předtím, než je možné přiřadit licence pro uživatele, Správce musí mít vlastnost Usage umístění na uživatele.

> Přiřazení licencí skupiny zdědí všechny uživatele bez zadaného místa využití umístění adresáře. Pokud máte uživatele v několika umístěních, doporučujeme, abyste jako součást vytváření toku uživatele ve službě Azure AD (např. pomocí AAD Connect konfigurace) –, který zajišťuje výsledek přiřazení licence je vždy správný a uživatelé neobdrží vždy nastavena místo využívání služby v umístění, které nejsou povoleny.

## <a name="step-1-assign-the-required-licenses"></a>Krok 1: Přiřazení požadované licence

1. Přihlaste se k [ **webu Azure portal** ](https://portal.azure.com) s účtem správce. Pokud chcete spravovat licence, musí být účet jako správce pro účet role nebo uživatele globální správce.

2. Vyberte **všechny služby** v levém navigačním podokně a pak vyberte **Azure Active Directory**. Můžete přidat k oblíbeným položkám v tomto podokně nebo ji připnout k řídicímu panelu portálu.

3. Na **Azure Active Directory** vyberte **licence** a otevřete tak podokno, kde můžete zobrazit a spravovat všechny provozovatelné produkty v tenantovi.

4. V části **všechny produkty**, vyberte Office 365 Enterprise E3 a Enterprise Mobility + Security tak, že vyberete názvů produktů. Chcete-li spustit přiřazení, vyberte **přiřadit** v horní části podokna.

   ![Všechny produkty, přiřazení licence](./media/licensing-groups-assign/all-products-assign.png)

5. Na **přiřadit licence** podokně klikněte na tlačítko **uživatelů a skupin** otevřít **uživatelů a skupin** podokně. Vyhledávání pro název skupiny *Personální oddělení*, vyberte skupinu a pak je potřeba ověřit klepnutím na **vyberte** v dolní části podokna.

   ![Vybrat skupinu](./media/licensing-groups-assign/select-a-group.png)

6. Na **přiřadit licence** podokně klikněte na tlačítko **(volitelné) přiřazení možností**, zobrazuje všechny plány služby, které jsou součástí tyto dva produkty, které jsme vybrali dříve. Najít **Yammer Enterprise** a znovu je zapnout **vypnout** zakázat tuto službu z licenci na produkt. Potvrďte kliknutím **OK** v dolní části **možnosti přiřazení**.

   ![Možnosti přiřazení](./media/licensing-groups-assign/assignment-options.png)

7. Přiřazení dokončíte tak, že v dolní části podokna **Přiřadit licenci** kliknete na **Přiřadit**.

8. Oznámení se zobrazí v pravém horním rohu, zobrazuje stav a výsledek procesu. Pokud přiřazení do skupiny nebylo možné dokončit (například z důvodu již dříve existující licence ve skupině), klikněte na oznámení zobrazíte podrobnosti o tomto selhání.

Nyní jsme zadání šablonu licence pro skupinu oddělení lidských zdrojů. Zpracovat všechny stávající členy této skupiny se spustil proces na pozadí ve službě Azure AD. Tato počáteční operace může trvat nějakou dobu, v závislosti na aktuální velikost skupiny. Další krok popisuje, jak ověřit, že proces byl dokončen a určit, pokud další pozornost je potřeba vyřešit problémy.

> [!NOTE]
> Můžete spustit stejný přiřazení z alternativního umístění: **uživatelů a skupin** ve službě Azure AD. Přejděte na **Azure Active Directory** > **uživatelů a skupin** > **všechny skupiny**. Vyhledejte skupinu, vyberte ho a přejděte **licence** kartu. **Přiřadit** tlačítka na panelu se otevře podokno přiřazení licencí.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Krok 2: Ověřte, že byla dokončena počáteční přiřazení

1. Přejděte na **Azure Active Directory** > **uživatelů a skupin** > **všechny skupiny**. Vyhledejte **Personální oddělení** skupiny, které licence byly přiřazeny.

2. Na **Personální oddělení** skupiny podokně, vyberte **licence**. To vám umožňuje rychle potvrzení v případě, že licence byla plně přiřazené uživatelům a nejsou žádné chyby, které je třeba prozkoumat. Je k dispozici následující informace:

   - Seznam licence na produkty, které momentálně nejsou přiřazené ke skupině. Vyberte položku k zobrazení konkrétních služeb, které byly povoleny a provést změny.

   - Stav poslední změny licencí, které byly provedeny do skupiny (pokud jsou zpracovávány změny nebo pokud dokončení zpracování pro všechny členy uživatelů).

   - Informace o uživatelích, kteří jsou v chybovém stavu protože licence se nepovedlo přiřadit k nim.

   ![Možnosti přiřazení](./media/licensing-groups-assign/assignment-errors.png)

3. Si zobrazili podrobnější informace o licenci zpracování v rámci **Azure Active Directory** > **uživatelů a skupin** > *název skupiny*  >  **Protokoly auditu**. Mějte na paměti následující činnosti:

   - Aktivita: **Start použití skupinové licence pro uživatele**. To se zaprotokoluje, když systém vybere změnu přiřazení licence skupině a spustí jeho použití na všechny členy. Obsahuje informace o změně, která byla vytvořena.

   - Aktivita: **ukončení použití skupinové licence pro uživatele**. To se zaprotokoluje, když systém dokončí zpracování všichni uživatelé ve skupině. Obsahuje souhrn počtu uživatelů, kteří byly úspěšně zpracovány a kolik uživatelů nešlo přiřadit skupiny licencí.

   [V tomto tématu](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) Další informace o použití protokoly auditu chcete analyzovat změny provedené na základě skupin licencí.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Krok 3: Kontrola problémů licence a jejich řešení

1. Přejděte na **Azure Active Directory** > **uživatelů a skupin** > **všechny skupiny**a najít **Personální oddělení** Skupina, která licence byly přiřazeny.
2. Na **Personální oddělení** skupiny podokně, vyberte **licence**. Oznámení o podokně ukazují, že jsou 10 uživatelů, které nelze přiřadit licence. Kliknutím se otevře seznam všech uživatelů v licencování chybový stav pro tuto skupinu.
3. **Neúspěšných přiřazení** sloupec víme, že obě licence na produkty nebylo možné přiřadit uživatelům. **Nejčastější příčiny selhání** sloupec obsahuje příčinu selhání. V tomto případě má **konfliktní plány služeb**.

   ![Neúspěšné přiřazení](./media/licensing-groups-assign/failed-assignments.png)

4. Vyberte uživatele, aby otevřel **licence** podokně. V tomto podokně se zobrazí všechny licence, které jsou aktuálně přiřazeny uživateli. V tomto příkladu má uživatel licenci Office 365 Enterprise E1, která byla zděděna od **veřejného terminálu uživatelé** skupiny. To je v konfliktu s licencí E3, systém se pokusil použít z **Personální oddělení** skupiny. V důsledku toho žádná z dané skupiny licencí bylo přiřazeno uživateli.

   ![Zobrazení licencí pro uživatele](./media/licensing-groups-assign/user-license-view.png)

5. Pokud chcete tento konflikt vyřešit, odeberte uživatele z **veřejného terminálu uživatelé** skupiny. Poté, co Azure AD zpracovává této změny **Personální oddělení** správně přiřazení licencí.

   ![Správně přiřazenou licenci](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>Další postup

Další informace o sadě pro správu licencí pomocí skupin funkcí, naleznete v následujících článcích:

* [Co je skupina založená na licencování v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
* [Migrace uživatelů mezi licencemi produktů pomocí licencování pro skupiny ve službě Azure Active Directory](licensing-groups-change-licenses.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](../active-directory-licensing-group-advanced.md)
* [Příklady prostředí PowerShell pro licencování na základě skupiny v Azure Active Directory](licensing-ps-examples.md)
