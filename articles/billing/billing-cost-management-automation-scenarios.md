---
title: Azure fakturace a náklady na správu automatizace scénáře | Microsoft Docs
description: Zjistěte, jak se běžné fakturace a správa, jsou scénáře nákladů namapován na jiný rozhraní API.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: ac0c4c0bad98c3057fb554e6011f1c601cd97dee
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851239"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Fakturace a cenově scénáře automatizace správy

Časté scénáře pro správu místa fakturace a náklady jsou identifikovány a namapované na různé rozhraní API, která lze použít v těchto scénářích. Souhrn rozhraní API dostupná a funkce, které nabízejí naleznete pod scénáři a mapování rozhraní API. 

## <a name="common-scenarios"></a>Obvyklé scénáře 

Můžete použít fakturaci a Správa nákladů souvisejících rozhraní API v různých scénářích odpovědí náklady a využití otázky.  Přehled běžných scénářů najdete níže.

- **Fakturovat odsouhlasení** -nebyla Microsoft účtují na správnou částku?  Co je můj faktury a můžete vypočítat ho mne?

- **Mezi poplatky** – nyní, lze zjistit, kolik I se vám byly účtovány poplatky, který v mé organizaci potřebuje věnovat?

- **Náklady optimalizace** -lze zjistit, kolik I jste naúčtována, ale, jak lze získat další mimo peníze I mě výdaje na Azure?

- **Sledování nákladů** – chcete vidět, kolik jsem výdaje a pomocí Azure v čase. Jaké jsou trendy? Jak může I to lépe?

- **Azure tráví v měsíci** – kolik je můj aktuálního měsíce je tráví k datu? Je nutné provést úpravy v mé výdaje a využití Azure? Když v měsíci se I využívání Azure nejvíce?

- **Nastavení výstrah** – chcete nastavit založené na prostředcích spotřeby nebo peněžní na základě výstrahy.

## <a name="scenario-to-api-mappings"></a>Scénář mapování rozhraní API

|         Rozhraní API nebo scénáře        | Odsouhlasení faktury    | Mezi poplatky    | Optimalizace nákladů    | Sledování nákladů    | MID měsíc tráví    | Výstrahy    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Rozpočty                     |                           |                  |           X          |                  |                    |     X     |
| Tržišť                |             X             |         X        |           X          |         X        |          X         |     X     |
| Ceník                 |             X             |         X        |           X          |         X        |          X         |           |
| Rezervace doporučení |                           |                  |           X          |                  |                    |           |
| Podrobnosti o rezervaci         |                           |                  |           X          |         X        |                    |           |
| Rezervace souhrny       |                           |                  |           X          |         X        |                    |           |
| Využití: podrobnosti               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fakturační období             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nehodnocené využití               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scénář pro rozhraní API mapování níže nezahrnují rozhraní API spotřeba Enterprise. Pokud je to možné, využívat prosím obecné API spotřeba vyřešit net nové vývojové scénáře postoupíte.

## <a name="api-summaries"></a>Rozhraní API souhrny

### <a name="consumption"></a>Využití
(*Webové přímé + podnikoví zákazníci pro všechna rozhraní API s výjimkou těchto volané odhlašování níže*)

-   **Rozpočty** (*podnikoví zákazníci pouze*): použití [rozpočty API](https://docs.microsoft.com/rest/api/consumption/budgets) k vytváření rozpočtů náklady nebo využití pro zdroje, skupiny prostředků nebo fakturace měřidla.  Po vytvoření rozpočty výstrahy lze konfigurovat pro upozornění při překročení prahové hodnoty nároky definované uživatelem. Akce lze nakonfigurovat také nastat, když se dosáhne objemu nároky.
-   **Tržišť**: použití [API Marketplace poplatky](https://docs.microsoft.com/rest/api/consumption/marketplaces) získat data o využití a poplatků na všechny prostředky Marketplace (Azure 3. stran nabídek). Tato data lze přidat až náklady na všechny všechny prostředky Marketplace nebo prozkoumat náklady a způsob použití na konkrétní prostředky.
-   **Ceník** (*podnikoví zákazníci pouze*): podnikoví zákazníci mohou pomocí [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) načíst jejich vlastní ceny pro všechny měřidla. Podniky tyto údaje můžete použít v kombinaci s informace o využití využití podrobnosti a tržišť provést výpočet nákladů na použití dat o využití a marketplace. 
-   **Rezervace doporučení**: použití [rezervace doporučení API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) získat doporučení pro nákup virtuálních počítačů vyhrazená instance. Doporučení jsou navrženy pro umožňuje zákazníkům k analýze úsporu nákladů očekávané a nákupu objemy.
-   **Podrobnosti o rezervaci**: použití [rezervace podrobnosti API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) zobrazíte informace o na dříve zakoupené rezervace virtuálních počítačů, jako je například kolik energie zarezervovala a kolik se ve skutečnosti používá. Můžete zobrazit data na podrobné úrovni za virtuálních počítačů.
-   **Rezervace souhrny**: použití [rezervace souhrny API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) zobrazíte souhrnné informace na dříve zakoupené rezervace virtuálních počítačů, jako je například kolik energie zarezervovala a kolik se ve skutečnosti používá v agregační funkci. 
-   **Podrobnosti o použití**: použití [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/consumption/usagedetails) Získejte zdarma a použití na všechny Azure 1 prostředky výrobců. Informace jsou ve formě podrobných záznamů využití, které jsou jednou vygenerované aktuálně za metr za den. Informace slouží k přidání s náklady mezi všechny prostředky nebo prozkoumat náklady a způsob použití na konkrétní prostředky.
-   **RateCard**: přímé webové zákazníci mohou používat [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx) získat míry měření. Vrácené informace ten pak může použít k výpočtu ručně očekávané faktury s jejich informace o využití prostředků. 
-   **Bez hodnocení využití**: můžete použít [nehodnocené využití rozhraní API](https://msdn.microsoft.com/library/azure/mt219003.aspx) se získat informace o nezpracovanou využití, před všechny měření nebo poplatků provádí Azure.

### <a name="billing"></a>Fakturace
-   **Fakturační období**: použití [fakturační období API](https://docs.microsoft.com/rest/api/billing/billingperiods) k určení fakturační období pro analýzu, společně s faktury ID, která pro toto období. Faktury ID, která lze použít s rozhraním API faktury níže. 
-   **Faktury**: použití [faktury API](https://docs.microsoft.com/rest/api/billing/invoices) získat adresu URL pro stažení faktury dané fakturační období ve formátu PDF.

### <a name="enterprise-consumption"></a>Spotřeba Enterprise
*(Všechny rozhraní API Enterprise pouze)*

-   **Souhrn vyrovnávání**: použití [souhrn rozhraní API vyrovnávání](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) získat měsíční souhrnné informace o zůstatky, nové nákupy, poplatky za služby Azure Marketplace, úpravy a Nadlimitní poplatky. Můžete získat tyto informace pro aktuální fakturační období nebo libovolného období v minulosti. Podniky tyto údaje můžete použít k porovnání s ručně počítané souhrnné poplatky. Toto rozhraní API neposkytuje informace specifické pro prostředek nebo agregované zobrazení náklady.
-   **Podrobnosti o použití**: použití [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) získat 1 strany podrobné informace o Azure využití pro aktuální měsíc, konkrétní fakturační období nebo vlastní datum období. Podniky tyto údaje můžete použít k výpočtu ručně faktury na základě míry a spotřeba a můžete také použít oddělení nebo organizace informace obsažené do atribut náklady v organizacích. Data poskytuje konkrétní prostředky zobrazení využití nebo náklady.
-   **Marketplace úložiště poplatků**: použití [Marketplace úložiště poplatků API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) získat podrobné informace o Azure využití pro aktuální měsíc, konkrétní fakturační období nebo vlastní datum období 3. stran. Podniky tyto údaje můžete použít k výpočtu ručně faktury na základě míry a spotřeba a můžete také použít oddělení nebo organizace informace obsažené do atribut náklady v organizacích. Zřizování úložiště Marketplace rozhraní API obsahuje konkrétní prostředky zobrazení využití nebo náklady.
-   **Ceník**: [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) umožňuje použít rychlost pro každé monitorování dané registraci a fakturační období. Tyto informace míry lze v kombinaci s informace o využití využití podrobnosti a tržišť ručně vypočítat očekávané faktury.
-   **Fakturační období**: použití [fakturační období API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) získat seznam fakturační období společně s vlastností odkazuje na trasu rozhraní API pro čtyři sady dat Enterprise rozhraní API, která se vztahují na tento fakturační období - BalanceSummary, UsageDetails, Marketplace poplatky a ceník.
-   **Vyhrazená Instance doporučení**: [vyhrazenou instanci doporučení API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) zjistí zákazníka 7 dnů, 30 dnů nebo 60 dní využití virtuálních počítačů a nabízí doporučení jediný a sdílet nákupu. Vyhrazená instance, kterou rozhraní API umožňuje zákazníkům k analýze očekává úsporu nákladů a doporučených částek nákupu.

## <a name="next-steps"></a>Další kroky

- Informace o použití rozhraní API Správce Azure fakturace k prostřednictvím kódu programu pohled na použití Azure najdete v tématu [fakturace přehled rozhraní API služby Azure](billing-usage-rate-card-overview.md).