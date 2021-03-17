---
title: Začínáme s portálem Azure Enterprise
description: Tento článek vysvětluje, jak zákazníci se smlouvou Azure Enterprise (Azure EA) používají portál Azure Enterprise.
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 88bb4c24489b973aa4230ba6f2b34756f83c7dda
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030684"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Začínáme s portálem Azure Enterprise

Tento článek pomáhá přímým i nepřímým zákazníkům se smlouvou Azure Enterprise (Azure EA) začít používat [portál Azure Enterprise](https://ea.azure.com). Získáte základní informace o:

- Struktuře portálu Azure Enterprise
- Rolích používaných na portálu Azure Enterprise
- Vytvoření předplatného
- Analýze nákladů na portálu Azure Enterprise a webu Azure Portal

## <a name="get-started-with-ea-onboarding"></a>Začínáme s onboardingem EA

Tady najdete [průvodce onboardingem účtu Azure EA (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).

Podívejte se na toto video, které předvádí celý postup onboardingu portálu Azure Enterprise:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>Principy rolí uživatelů EA a úvod do hierarchie uživatelů

Zákazníci Azure se smlouvou Enterprise (EA) můžou přiřazovat pět různých rolí správce, které jim v organizaci usnadní spravovat využívání a výdaje:

- Podnikový správce
- Podnikový správce (jen pro čtení)
- Správce oddělení
- Správce oddělení (jen pro čtení)
- Vlastník účtu

Každá role má proměnlivou úroveň uživatelských omezení a oprávnění. Další informace najdete v tématu [Organizační struktura a oprávnění podle role](./understand-ea-roles.md#organization-structure-and-permissions-by-role).

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>Aktivace registrace, vytvoření předplatného a další úlohy správy

Další informace o aktivaci registrace, vytvoření oddělení nebo předplatného, přidání správců a vlastníků účtů a dalších úloh správy najdete v tématu [Správa portálu Azure EA](./ea-portal-administration.md).

Pokud se chcete dozvědět víc o převodu předplatného Enterprise na předplatné s průběžnými platbami, přečtěte si téma [Převody Azure Enterprise](./ea-transfers.md).

## <a name="view-usage-summary-and-download-reports"></a>Zobrazení souhrnu využití a stahování sestav

Můžete spravovat své faktury Azure EA a pracovat s nimi. Faktura slouží jako vaše vyúčtování a měli byste zkontrolovat její přesnost.

Pokud chcete zobrazit souhrn využití, stahovat sestavy a spravovat faktury za registraci, přečtěte si téma [Faktury za smlouvy Azure Enterprise](./ea-portal-enrollment-invoices.md).

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>Teď, když jste se seznámili se základy, jsou zde některé další odkazy, které vám pomůžou s onboardingem.

Článek [Ceny Azure EA](./ea-pricing-overview.md) poskytuje podrobnosti o tom, jak se počítá využití, a prochází poplatky za různé služby Azure v rámci smlouvy Enterprise, kde jsou výpočty složitější.

Pokud chcete zjistit, jak vám rezervace Azure umožní ušetřit peníze za rezervované instance virtuálních počítačů v rámci smlouvy Enterprise, přečtěte si článek [Rezervované instance virtuálních počítačů Azure EA](./ea-portal-vm-reservations.md).

Informace o tom, která rozhraní REST API se mají použít s vaší registrací Azure Enterprise, a vysvětlení, jak vyřešit běžné problémy s rozhraními REST API, najdete v článku [Rozhraní Azure Enterprise REST API](./ea-portal-rest-apis.md).

Článek [Smlouvy k Azure EA a jejich změny](./ea-portal-agreements.md) popisuje, jak mohou smlouvy Azure EA a jejich změny ovlivnit váš přístup ke službám Azure, jejich používání a úhrady za ně.

Článek [Azure Marketplace](./ea-azure-marketplace.md) vysvětluje, jak partneři a zákazníci EA mohou zobrazovat poplatky za Marketplace a povolit nákupy na Azure Marketplace.

Vysvětlení k běžným úlohám, které provádí správce partnerů na portálu Azure EA, najdete v článku [Správa portálu Azure EA pro partnery](./ea-partner-portal-administration.md).

## <a name="get-started-on-azure-ea---faq"></a>Začínáme s Azure EA – Nejčastější dotazy

Tato část obsahuje podrobnosti o typických dotazech zákazníků během procesu onboardingu.  

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Omylem jsem přidružil(a) svůj stávající účet Azure k registraci Azure EA. Následkem toho se mi ztratil měsíční kredit. Mohu měsíční kredit získat zpět?

Pokud jste se přihlásili jako vlastník účtu Azure EA pomocí stejných přihlašovacích údajů, které používáte pro Visual Studio Subscription, můžete obnovit svoje výhody Azure služby Visual Studio Subscription provedením jedné z následujících akcí:

- Po odebrání nebo přesunutí všech přidružených předplatných Azure odstraňte roli vlastníka účtu z portálu Azure Enterprise. Potom si znovu zaregistrujte jednotlivé výhody Azure pro Visual Studio.
- Odstraňte předplatitele sady Visual Studio z lokality správy na webu VLSC a znovu přiřaďte předplatné k účtu, ale tentokrát s jinými přihlašovacími údaji. Potom si znovu zaregistrujte jednotlivé výhody Azure pro Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Jaký typ předplatného mám vytvořit?

Portál Azure Enterprise nabízí podnikovým zákazníkům dva typy předplatného:

- Microsoft Azure Enterprise, které je ideální pro:
  - Veškeré produkční využití
  - Nejlepší ceny v závislosti na útratě za infrastrukturu

  Pokud potřebujete další informace, [kontaktujte obchodní oddělení Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Enterprise pro vývoj/testování, které je ideální pro:
  - Všechny týmové úlohy vývoje/testování
  - Úlohy vývoje/testování se středním až vysokým zatížením
  - Přístup ke speciálním imagím MSDN a preferenčním sazbám za služby

  Další informace najdete v tématu, které se věnuje [nabídce Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Název mého předplatného je stejný jako název nabídky. Mám název předplatného změnit na nějaký smysluplný název pro naši organizaci?

Při vytváření předplatného se jako výchozí název používá typ nabídky, kterou zvolíte. Doporučujeme změnit název předplatného na název, který vám usnadní sledování předplatného.

Změna názvu:

1. Přihlaste se k webu [https://account.windowsazure.com](https://account.windowsazure.com).
1. Vyberte seznam předplatných.
1. Vyberte předplatné, které chcete upravit.
1. Vyberte ikonu **Manage Subscription** (Spravovat předplatné).
1. Upravte podrobnosti o předplatném.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Jak můžu sledovat vzniklé náklady podle nákladového střediska?

Pokud chcete sledovat náklady podle nákladového střediska, musíte definovat nákladové středisko na jedné z následujících úrovní:

- Oddělení
- Účet
- Předplatné

Podle potřeby můžete stejné nákladové středisko použít ke sledování využití a nákladů souvisejících s konkrétním nákladovým střediskem.

Například pokud chcete sledovat náklady na speciální projekt, na kterém se podílí více oddělení, můžete ke sledování využití a nákladů definovat nákladové středisko na úrovni předplatného.

Nákladové středisko není možné definovat na úrovni služby. V případě, že chcete sledovat využití na úrovni služby, můžete využít funkci _značek_, která je dostupná na úrovni služby.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Jak můžu sledovat využití a útratu v různých odděleních organizace?

V rámci registrace Azure EA můžete vytvořit libovolný počet oddělení. Abyste mohli správně sledovat využití, zajistěte, aby se předplatná mezi odděleními nesdílela.

Po vytvoření oddělení a předplatných uvidíte v sestavě využití data. Tyto informace vám pomohou sledovat využití a spravovat náklady a útratu na úrovni oddělení.

K datům využití se můžete dostat také v rozhraní API pro generování sestav. Podrobné informace a ukázkový kód najdete v tématu [Rozhraní Azure Enterprise REST API](./ea-portal-rest-apis.md).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Mohu nastavit kvótu útraty a dostávat upozornění, když se budu blížit limitu?

Můžete nastavit kvótu útraty na úrovni oddělení a systém vás automaticky upozorní, když vaše limity útraty dosáhnou 50 %, 75 %, 90 % a 100 % definované kvóty.

Pokud chcete definovat kvótu útraty, vyberte oddělení a potom ikonu úprav. Po úpravě podrobností limitu útraty vyberte **Save** (Uložit).

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>K implementaci řízení přístupu na základě role a sledování využití jsem použil(a) skupiny prostředků. Jak mohu zobrazit související podrobnosti o využití?

Informace o používání _skupin prostředků_ a _značek_ se sledují na úrovni služby a jsou k dispozici v souboru s podrobnými informacemi o využití (CSV), který si můžete stáhnout. Na portálu Azure Enterprise přejděte na [stažení sestavy využití](https://ea.azure.com/report/downloadusage).

K informacím o využití se dostanete také přes rozhraní API. Podrobné informace a ukázkový kód najdete v tématu [Rozhraní Azure Enterprise REST API](./ea-portal-rest-apis.md).

> [!NOTE]
> Značky můžete používat pouze u prostředků, které podporují operace Azure Resource Manageru. Pokud jste vytvořili virtuální počítač, virtuální síť nebo úložiště prostřednictvím modelu nasazení Classic (například přes portál Classic), nemůžete pro tyto prostředky použít značky. Pokud chcete zajistit podporu označování, musíte tyto prostředky nasadit znovu prostřednictvím Resource Manageru. Všechny ostatní prostředky označování podporují.

### <a name="can-i-perform-analyses-using-power-bi"></a>Můžu provádět analýzy s využitím Power BI?

Ano. Balíček obsahu Microsoft Azure Enterprise pro Power BI umožňuje:

- rychle importovat a analyzovat využití Azure u podnikové registrace,
- zjistit, které oddělení, účet nebo předplatné se na využití podílelo nejvíce,
- zjistit, kterou službu vaše organizace nejvíce používala,
- sledovat útratu a trendy používání.

Použití Power BI:

1. Přejděte na web Power BI.
1. Přihlaste se pomocí platného pracovního nebo školního účtu.

   Může se jednat o stejný pracovní nebo školní účet, který používáte pro přístup k registraci prostřednictvím portálu Azure Enterprise, nebo jiný pracovní nebo školní účet.
1. Na řídicím panelu služeb vyberte dlaždici Microsoft Azure Enterprise a vyberte **Připojit**.
1. Na obrazovce **Připojit k Azure Enterprise** zadejte:
    - Adresu URL prostředí Azure: [https://ea.azure.com](https://ea.azure.com)
    - Počet měsíců: hodnota v rozmezí 1 až 36
    - Číslo registrace: číslo vaší registrace
1. Vyberte **Další**.
1. Do pole **Klíč účtu** zadejte klíč rozhraní API.

   Klíč rozhraní API najdete na portálu Azure Enterprise. Pod kartou **Download Usage** (Stáhnout využití) vyberte **API Access Key** (Přístupový klíč rozhraní API). Zkopírujte ho a vložte do pole **Klíč účtu** v Power BI.

V závislosti na velikosti datové sady může načtení dat do Power BI trvat 5 až 30 minut.

Generování sestav Power BI je k dispozici pro přímé a nepřímé zákazníky a partnery Azure EA, kteří můžou zobrazit fakturační údaje.

## <a name="next-steps"></a>Další kroky

- Správci na portálu Azure Enterprise by si měli přečíst téma [Správa portálu Azure Enterprise](ea-portal-administration.md), kde se seznámí s běžnými úlohami správy.
- Pokud potřebujete pomoc při řešení potíží s portálem Azure Enterprise, přečtěte si téma [Řešení potíží s přístupem k portálu Azure Enterprise](ea-portal-troubleshoot.md).