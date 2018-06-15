---
title: Přiřazení licencí do skupiny ve službě Azure Active Directory | Microsoft Docs
description: Přiřazení licencí uživatelům prostřednictvím Azure Active Directory skupiny licencí
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
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 091750d7cadd460ac46d3293951bf4423383c3c5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763029"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Přiřazení licencí pro uživatele na základě členství ve skupině v Azure Active Directory

Tento článek vás provede přiřazování licencí produktu pro skupinu uživatelů ve službě Azure Active Directory (Azure AD) a potom ověření, že máte licenci správně.

V tomto příkladu klienta obsahuje skupiny zabezpečení s názvem **Personální oddělení**. Tato skupina zahrnuje všechny členové oddělení lidských zdrojů (přibližně 1 000 uživatelů). Chcete přiřadit licence Office 365 Enterprise E3 celý oddělení. Služba Yammer Enterprise, který je součástí produktu musí být dočasně zakázáno, dokud oddělení je připraven k jej začít používat. Chcete také nasadit Enterprise Mobility + Security licence na stejnou skupinu uživatelů.

> [!NOTE]
> Některé služby společnosti Microsoft nejsou k dispozici ve všech umístěních. Předtím, než je možné přiřadit licence pro uživatele, Správce musí určovat vlastnost umístění využití na uživatele.

> Pro přiřazení skupiny licencí zdědí všechny uživatele bez využití umístění zadané umístění adresáře. Pokud máte uživatele na více místech, doporučujeme, abyste jako součást vaší toku vytvoření uživatele ve službě Azure AD (např. přes AAD Connect konfigurace) –, které zajišťuje, aby výsledek přiřazení licence je vždy správný a uživatelé neobdrží vždy nastavit umístění využití služby v umístění, které nejsou povoleny.

## <a name="step-1-assign-the-required-licenses"></a>Krok 1: Přiřaďte požadované licence

1. Přihlaste se k [ **portál Azure** ](https://portal.azure.com) s účtem správce. Ke správě licencí, musí být účet globálního správce roli nebo uživatele účtu správce a.

2. Vyberte **všechny služby** v levém navigačním podokně a potom vyberte **Azure Active Directory**. Můžete přidat k oblíbeným položkám v tomto podokně nebo připnout na řídicí panel portálu.

3. Na **Azure Active Directory** podokně, vyberte **licence** otevřete podokno, kde můžete zobrazit a spravovat všechny produkty provozovatelný v klientovi.

4. V části **všechny produkty**, vyberte Office 365 Enterprise E3 a Enterprise Mobility + Security výběrem názvy produktů. Chcete-li spustit přiřazení, vyberte **přiřadit** v horní části podokna.

   ![Všechny produkty, přiřadit licence](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. Na **přiřazení licence** podokně klikněte na tlačítko **uživatelů a skupin** otevřete **uživatelů a skupin** podokně. Vyhledávání pro název skupiny *Personální oddělení*, vyberte skupinu a je nutné potvrdit kliknutím **vyberte** v dolní části podokna.

   ![Vybrat skupinu](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. Na **přiřazení licence** podokně klikněte na tlačítko **přiřazení možností (volitelné)**, zobrazuje všechny plány služby, které jsou součástí tyto dva produkty, které jsme vybrali dříve. Najít **Yammer Enterprise** a zapnout ho **vypnout** zakázat tuto službu z platnost licence produktu. Potvrďte kliknutím **OK** v dolní části **přiřazení možností**.

   ![Možnosti přiřazení](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. K dokončení přiřazení, na **přiřazení licence** podokně klikněte na tlačítko **přiřadit** v dolní části podokna.

8. V pravém horním rohu, které se zobrazuje stav a výsledek procesu se zobrazí oznámení. Pokud přiřazení do skupiny nebylo možné dokončit (například z důvodu předchozích licencí ve skupině), klikněte na oznámení. Chcete-li zobrazit podrobnosti o tomto selhání.

Nyní jsme jste zadali šablona licence pro skupinu oddělení lidských zdrojů. Byl spuštěn proces na pozadí ve službě Azure AD zpracovat všechny stávající členy této skupiny. Tato počáteční operace může trvat delší dobu, v závislosti na aktuální velikost skupiny. Další krok popisuje, jak určit, pokud další pozornost je nezbytné k řešení problémů a ověřte, že je proces dokončen.

> [!NOTE]
> Stejné přiřazení můžete spustit z alternativního umístění: **uživatelů a skupin** ve službě Azure AD. Přejděte na **Azure Active Directory** > **uživatelů a skupin** > **všechny skupiny**. Vyhledejte skupině, vyberte ho a přejděte na **licence** kartě. **Přiřadit** tlačítko horní části podokna otevření podokna přiřazení licence.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Krok 2: Ověření, že byla dokončena počáteční přiřazení

1. Přejděte na **Azure Active Directory** > **uživatelů a skupin** > **všechny skupiny**. Vyhledejte **Personální oddělení** skupinu, která licencí bylo přiřazeno k.

2. Na **Personální oddělení** skupiny podokně, vyberte **licence**. Díky tomu můžete rychle potvrďte Pokud licence byla plně přiřadit uživatelům, a pokud nejsou žádné chyby, které potřebujete, aby viděl. Je k dispozici následující informace:

   - Seznam licence na produkty, které jsou přiřazeny ke skupině. Vyberte položku, chcete-li zobrazit konkrétní služby, které jsou zapnuty a provést změny.

   - Stav nejnovější změny licencí, které byly provedeny do skupiny (pokud jsou zpracovávány změny nebo pokud dokončení zpracování pro všechny členy uživatele).

   - Informace o uživatelích, kteří jsou v chybovém stavu protože nebylo možné přiřadit licence na je.

   ![Možnosti přiřazení](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Podrobnější informace o licenci zpracování v rámci **Azure Active Directory** > **uživatelů a skupin** > *název skupiny*  >  **Protokoly auditu**. Všimněte si následujících aktivit:

   - Aktivita: **spustit použití skupinu na základě licencí pro uživatele**. To se protokolují, když systému převezme změnit přiřazení licence ve skupině a spustí jeho použití na všechny uživatele členy. Obsahuje informace o změně, která byla vytvořená.

   - Aktivita: **dokončit použití skupinu na základě licencí pro uživatele**. To se protokolují, když server dokončí zpracování všechny uživatele ve skupině. Obsahuje souhrn počet uživatelů, kteří byly úspěšně zpracovány a kolik uživatelů nebylo možné přiřadit skupinu licencí.

   [Tato část](./active-directory-licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) Další informace o použití protokolů auditu k analýze změny provedené na základě skupin licencí.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Krok 3: Zkontrolujte licenční problémy a jejich řešení

1. Přejděte na **Azure Active Directory** > **uživatelů a skupin** > **všechny skupiny**a najít **Personální oddělení** Skupina, která licencí bylo přiřazeno k.
2. Na **Personální oddělení** skupiny podokně, vyberte **licence**. Oznámení nad v podokně zobrazí, že jsou 10 uživatelů, které nebylo možné přiřadit licence do. Na něj kliknete, otevře se seznam všech uživatelů v licencování chybový stav pro tuto skupinu.
3. **Se nezdařilo přiřazení** sloupec víme, že oba licence k produktům nebylo možné přiřadit uživatelům. **Top důvodem selhání** sloupec obsahuje příčinu selhání. V takovém případě má **plány služby konfliktní**.

   ![Přiřazení se nezdařila](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Vybrat uživatele a otevřete **licence** podokně. Toto podokno zobrazuje všechny licence, které jsou přiřazeny uživateli. V tomto příkladu uživatel má licenci Office 365 Enterprise E1, která byla zděděna od **celoobrazovkovém uživatelé** skupiny. Tato možnost v konfliktu s E3 licenci, která systém se pokusil použít z **Personální oddělení** skupiny. V důsledku toho žádné licence z dané skupiny byl přiřazen uživateli.

   ![Zobrazení licence pro uživatele](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Pokud chcete tento konflikt vyřešit, odeberte uživatele z **celoobrazovkovém uživatelé** skupiny. Po Azure AD zpracovává změnu, **Personální oddělení** správně přiřazené licence.

   ![Správně přiřazenou licenci](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Další postup

Další informace o funkci nastavit pro správu licencí prostřednictvím skupiny, najdete v následujících článcích:

* [Co je na základě skupin licencování v Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identifikace a řešení potíží s licencí pro skupinu v Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Postup migrace na základě skupiny licencí v Azure Active Directory pro jednotlivé licencovaní uživatelé](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory na základě skupin licencí další scénáře](active-directory-licensing-group-advanced.md)
