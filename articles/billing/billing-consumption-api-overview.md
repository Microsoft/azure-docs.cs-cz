---
title: Přehled konektoru Azure consumption API | Dokumentace Microsoftu
description: Zjistěte, jak Azure Consumption API poskytují programový přístup k nákladů a data o využití pro vaše prostředky Azure.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: 88b7909e78f8bd36ce456eee60587acbbb94b6cd
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286744"
---
# <a name="azure-consumption-api-overview"></a>Přehled rozhraní API konektoru Azure consumption 

Rozhraní Azure Consumption API poskytují programový přístup k nákladů a využití dat pro prostředky Azure. Tato rozhraní API aktuálně podporují pouze prováděcí smlouvy Enterprise a předplatná Web Direct (s několika výjimkami). Rozhraní API se průběžně aktualizuje, aby podporoval další typy předplatných Azure.

Azure Consumption API poskytují přístup k:
- Přímí zákazníci se organizace a Web 
    - Využití: podrobnosti 
    - Poplatky za Marketplace 
    - Doporučení pro rezervaci 
    - Podrobnosti o rezervaci 
    - Souhrny rezervace 
- Jenom pro podnikové zákazníky 
    - Ceník 
    - Rozpočty 
    - Zůstatky 

## <a name="usage-details-api"></a>Podrobnosti o použití rozhraní API

Použít rozhraní API podrobnosti využití zobrazíte data o využití a poplatků pro všechny Azure 1 stran prostředky. Informace jsou ve formě záznamů podrobnosti o využití, které jsou aktuálně, protože ho jednou za měřiče za prostředek za den. Informace slouží k přidání s náklady přes všechny prostředky nebo prozkoumat náklady a využití na konkrétní prostředky. 

Toto rozhraní API zahrnuje:

-   **Monitorovat Data o spotřebě úroveň** – zobrazit data, včetně využití nákladů, měřič generování poplatek, a jaké prostředků Azure sazba se vztahuje k. Všechny záznamy využití mapují na denní interval.
-   **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** -Trim vaše rozhraní API výsledek hodnotu menší sadu protokolovaných podrobných záznamů využití pomocí následující filtry:
    - Využití end / start využití
    - Skupina prostředků
    - Název prostředku
-   **Agregace dat** -OData pomocí použití výrazů podrobnosti o použití agregace podle klíčových slov nebo filtrovat vlastnosti
-   **Využití pro typy jinou nabídku** – podrobné informace o využití je momentálně dostupná pro zákazníky s Enterprise nebo Web Direct.

Další informace najdete v tématu technické specifikace [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Poplatky za Marketplace rozhraní API

Použití rozhraní API poplatky webu Marketplace získat data o využití a poplatků pro všechny prostředky Marketplace (Azure nabídky 3. stran). Tato data je možné přidat vyšší náklady napříč všechny prostředky Marketplace nebo prozkoumat náklady a využití na konkrétní prostředky. 

Toto rozhraní API zahrnuje:

-   **Monitorovat Data o spotřebě úroveň** – zobrazit data, včetně využití webu marketplace, náklady, měřič generování poplatek, a jaký prostředek sazba se vztahuje k. Všechny záznamy využití mapují na denní interval.
-   **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** -Trim vaše rozhraní API výsledek hodnotu menší sadu záznamů marketplace pomocí následující filtry:
    - Využití zahájení / ukončení využití
    - Skupina prostředků
    - Název prostředku
-   **Využití pro typy jinou nabídku** -informace z Marketplace je momentálně dostupná pro zákazníky s Enterprise nebo Web Direct.

Další informace najdete v tématu technické specifikace [API poplatky webu Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Zůstatky rozhraní API

Podnikoví zákazníci mohou pomocí rozhraní API vyrovnává získat měsíční souhrnné informace o zůstatcích, nové nákupy, poplatků za služby Azure Marketplace, úpravy a poplatky za Nadlimitní využití. Můžete získat tyto informace pro aktuální fakturační období nebo období v minulosti. Podniky můžou tato data použít k porovnání s ručně počítané souhrnné náklady. Toto rozhraní API neposkytuje informace specifické podle prostředků a agregované zobrazení nákladů. 

Toto rozhraní API zahrnuje:

-   **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Jenom zákazníci Enterprise** toto rozhraní API je k dispozici zákazníků EA. 
    - Zákazníci musí mít oprávnění správce podniku pro volání tohoto rozhraní API 

Další informace najdete v tématu technické specifikace [zůstatky API](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>Rozpočty rozhraní API

Podnikoví zákazníci slouží k vytvoření rozpočty nákladech nebo využití pro prostředky, skupiny prostředků nebo měřiče toto rozhraní API. Jakmile tyto informace bylo zjištěno, výstrahy lze nastavit upozornění při překročení prahové hodnoty definované uživatelem rozpočtu. 

Toto rozhraní API zahrnuje:

-   **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Jenom zákazníci Enterprise** -toto rozhraní API je k dispozici zákazníků EA.
-   **Konfigurovatelnými oznámeními** -zadat uživatele, která vás upozorní, když se přepne rozpočtu.
-   **Využití nebo na základě rozpočty nákladů** – vytvořit váš rozpočet na základě spotřeby nebo náklady podle potřeb vašeho scénáře.
-   **Filtrování** – filtrování vašemu rozpočtu na menší podmnožinu prostředků pomocí následujících konfigurovatelné filtrů
    - Skupina prostředků
    - Název prostředku
    - Měření
-   **Konfigurovatelné rozpočtu časových obdobích** – určete, jak často by měla resetovat rozpočtu a jak dlouho je platný pro rozpočtu.

Další informace najdete v tématu technické specifikace [rozpočty API](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Rezervace rozhraní Recommendations API

Pomocí tohoto rozhraní API pro doporučení k nákupu rezervovaných instancí virtuálních počítačů. Doporučení jsou navrženy pro toto řešení umožňuje zákazníkům analyzovat očekávaných úspor a nákup částky. 

Toto rozhraní API zahrnuje:

-   **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** – přizpůsobení výsledků doporučení pomocí následující filtry:
    - Rozsah
    - Lookback období
-   **Informace o rezervaci pro jinou nabídku typy** – informace o rezervaci je momentálně dostupná pro zákazníky s Enterprise nebo Web Direct.

Další informace najdete v tématu technické specifikace [rozhraní API pro doporučování rezervace](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Podrobnosti o rezervaci rozhraní API

Použití, které rozhraní API podrobnosti o rezervaci zobrazíte informace o dříve koupili rezervace virtuálních počítačů jako je například zarezervovala k jak velkému využití a kolik se ve skutečnosti používá. Můžete zobrazit data v jeden virtuální počítač úrovně podrobností. 

Toto rozhraní API zahrnuje:

-   **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** -Trim vaše rozhraní API výsledek hodnotu menší sadu protokolovaných rezervace pomocí filtru, který následující:
    - Rozsah dat
-   **Informace o rezervaci pro jinou nabídku typy** – informace o rezervaci je momentálně dostupná pro zákazníky s Enterprise nebo Web Direct.

Další informace najdete v tématu technické specifikace [API podrobnosti o rezervaci](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Rezervace souhrny rozhraní API

Použití, které toto rozhraní API zobrazíte souhrnné informace na dříve koupili rezervace virtuálních počítačů jako je například zarezervovala k jak velkému využití a kolik se ve skutečnosti používá v agregaci. 

Toto rozhraní API zahrnuje:

-   **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** -přizpůsobit výsledky při používání denním intervalem s následujícím filtru:
    - Datum využití
-   **Informace o rezervaci pro jinou nabídku typy** – informace o rezervaci je momentálně dostupná pro zákazníky s Enterprise nebo Web Direct.
-   **Denní nebo měsíční agregace** – volajícím můžete určit, zda chtějí své rezervace souhrnná data v denní nebo měsíční interval.

Další informace najdete v tématu technické specifikace [rezervace souhrny API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Rozhraní API tabulky cena
Podnikový zákazník může pomocí tohoto rozhraní API k načtení jejich vlastních cen pro všechny měřiče. Podniky může být využit v kombinaci s informace o využití využití. Podrobnosti a tržišť s řešeními náklady na výpočty pomocí data o využití a Web marketplace. 

Toto rozhraní API zahrnuje:

-   **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Jenom zákazníci Enterprise** -toto rozhraní API je k dispozici zákazníků EA. Přímé zákazníky webu používali RateCard API k získání ocenění. 

Další informace najdete v tématu technické specifikace [rozhraní API tabulky Cena](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scénáře

Tady jsou některé scénáře, které jsou zpřístupněny prostřednictvím využití rozhraní API:

-   **Vyrovnání faktury** -nebyla Microsoft účtovat úroveň?  Co je vyúčtování a můžete I vypočítat sám?
-   **Poplatky za různé** – teď, když mi vědět, kolik se mi účtuje, kdo v organizaci musí platit?
-   **Optimalizace nákladů** -vím, kolik se mi jsme se naúčtuje... Jak můžu získat mnohem víc z peníze, které trávím na Azure?
-   **Sledování nákladů** – chci zobrazit, kolik jsem výdaje a pomocí Azure v čase. Co jsou trendy? Jak můžou můžu provádět lepší?
-   **Výdaje za Azure za daný měsíc** – jaká je moje aktuální měsíc je výdaje na data? Je potřeba provést nějaké úpravy v Moje výdaje a/nebo využití Azure? Pokud v měsíci teď můžu využívání Azure na maximum?
-   **Nastavení výstrah** – chci nastavení spotřeby prostředků nebo peněžní na základě výstrahy podle rozpočtu.

## <a name="next-steps"></a>Další kroky

- Informace o používání Azure API pro fakturaci prostřednictvím kódu programu získat přehled o využívání služeb Azure najdete v tématu [přehled rozhraní API pro fakturaci Azure](billing-usage-rate-card-overview.md).



