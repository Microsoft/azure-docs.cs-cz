---
title: Přehled využití platformy Azure API | Microsoft Docs
description: Zjistěte, jak rozhraní API Správce Azure spotřeba poskytují programový přístup k náklady a data o využití pro vašich prostředků Azure.
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
ms.openlocfilehash: 69e148089f48ebe04faceff87b08eb3f7127f8a3
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248363"
---
# <a name="azure-consumption-api-overview"></a>Přehled využití platformy Azure API 

Rozhraní API Azure spotřeba poskytují programový přístup k náklady a data o využití pro vašich prostředků Azure. Tato rozhraní API aktuálně podporují pouze podnikové registrace a přímé odběry z webu (s několika výjimkami). Rozhraní API se průběžně aktualizují pro podporu dalších typů předplatných Azure.

Rozhraní API Azure spotřeba poskytnout přístup k:
- Enterprise a webové přímého zákazníkům 
    - Využití: podrobnosti 
    - Poplatky za Marketplace. 
    - Rezervace doporučení 
    - Podrobnosti o rezervaci 
    - Rezervace souhrny 
- Podnikoví zákazníci pouze 
    - Ceník 
    - Rozpočty 
    - Vyrovnává 

## <a name="usage-details-api"></a>Podrobnosti o použití rozhraní API

Použít rozhraní API podrobnosti o využití se získat data o využití a zdarma pro všechny Azure 1 prostředky výrobců. Informace jsou ve formě záznamů podrobnosti o využití, které jsou jednou vygenerované aktuálně za metr jednotlivých prostředků za den. Informace lze přidat až náklady na všechny všechny prostředky nebo prozkoumat náklady nebo využití na konkrétní prostředky. 

Rozhraní API obsahuje:

-   **Měřicí datům o spotřebě úroveň** – viz data, včetně využití náklady, měřidlo emitování poplatků, a jaké prostředků Azure zřizování se vztahuje k. Všechny záznamy podrobnosti o využití mapují denní sady.
-   **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určete, které Uživatelé nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure. 
-   **Filtrování** -Trim sadu na menší sadu záznamů podrobnosti o využití pomocí následující filtry výsledků vaše rozhraní API:
    - Využití koncového / start využití
    - Skupina prostředků
    - Název prostředku
-   **Agregace dat** -použití OData týkají podrobnosti o použití agregační výrazy podle značky nebo filtrování vlastnosti
-   **Použití pro jiné nabídka typy** -podrobné informace o využití je aktuálně dostupné pro zákazníky, Enterprise a webové Direct.

Další informace najdete v technické specifikaci pro [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Poplatky Marketplace rozhraní API

Použít rozhraní API Marketplace poplatky získat data o využití a poplatků na všechny prostředky Marketplace (Azure 3. stran nabídek). Tato data lze přidat až náklady na všechny všechny prostředky Marketplace nebo prozkoumat náklady nebo využití na konkrétní prostředky. 

Rozhraní API obsahuje:

-   **Měřicí datům o spotřebě úroveň** – viz data, včetně využití marketplace náklady, měřidlo emitování poplatků, a jaké prostředků zřizování se vztahuje k. Všechny záznamy podrobnosti o využití mapují denní sady.
-   **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určete, které Uživatelé nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure. 
-   **Filtrování** -Trim sadu na menší sadu záznamů marketplace pomocí následující filtry výsledků vaše rozhraní API:
    - Využití zahájení / ukončení využití
    - Skupina prostředků
    - Název prostředku
-   **Použití pro jiné nabídka typy** -informace z webu Marketplace je aktuálně dostupné pro zákazníky, Enterprise a webové Direct.

Další informace najdete v technické specifikaci pro [API Marketplace poplatky](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Rozhraní API zůstatky

Podnikoví zákazníci mohou pomocí rozhraní API vyrovnává získat měsíční souhrnné informace o zůstatky, nové nákupy, poplatky za služby Azure Marketplace, úpravy a Nadlimitní poplatky. Můžete získat tyto informace pro aktuální fakturační období nebo libovolného období v minulosti. Podniky tyto údaje můžete použít k porovnání s ručně počítané souhrnné poplatky. Toto rozhraní API neposkytuje informace specifické pro daný prostředek a agregované zobrazení náklady. 

Rozhraní API obsahuje:

-   **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určete, které Uživatelé nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure. 
-   **Podnikové zákazníky pouze** toto rozhraní API je k dispozici zákazníkům EA. 
    - Zákazníci musí mít oprávnění správce podniku volat toto rozhraní API 

Další informace najdete v technické specifikaci pro [zůstatky API](https://docs.microsoft.com/rest/api/consumption/getbalancesbybillingaccount).

## <a name="budgets-api"></a>Rozpočty rozhraní API

Podnikoví zákazníci mohou pomocí toto rozhraní API pro vytvoření rozpočty náklady nebo využití pro zdroje, skupiny prostředků nebo fakturace měřidla. Po určil tyto informace můžete pro upozornění při překročení prahové hodnoty uživatelem definované nároky nakonfigurovat výstrahy. 

Rozhraní API obsahuje:

-   **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určete, které Uživatelé nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure. 
-   **Podnikové zákazníky pouze** – tato rozhraní API je k dispozici zákazníkům EA.
-   **Konfigurovat oznámení** -zadat uživatele chcete být upozorněni, když je přerušovačů rozpočtu.
-   **Využití nebo náklady na základě rozpočty** -vytvořit rozpočet na základě spotřeby nebo náklady podle potřeb vašeho scénáře.
-   **Filtrování** -filtrovat rozpočet na menší podmnožinu prostředků pomocí následující konfigurovat filtry
    - Skupina prostředků
    - Název prostředku
    - Měření
-   **Konfigurovat nároky časových období** – zadejte, jak často by se obnovit rozpočtu a jak dlouho je platný pro rozpočtu.

Další informace najdete v technické specifikaci pro [rozpočty API](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Rezervace doporučení rozhraní API

Použijte toto rozhraní API k získání doporučení pro nákup virtuálních počítačů vyhrazená instance. Doporučení jsou navrženy pro umožňuje zákazníkům k analýze úsporu nákladů očekávané a nákupu objemy. 

Rozhraní API obsahuje:

-   **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určete, které Uživatelé nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure. 
-   **Filtrování** -přizpůsobit doporučení výsledky pomocí následující filtry:
    - Rozsah
    - Lookback období
-   **Informace o rezervaci pro různé nabídka typy** -informace o vyhrazení je aktuálně dostupné pro zákazníky, Enterprise a webové Direct.

Další informace najdete v technické specifikaci pro [rezervace doporučení API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Podrobnosti o rezervaci rozhraní API

Použití, které rozhraní API rezervace podrobnosti zobrazíte informace o dříve koupili rezervace virtuálních počítačů, jako je například kolik energie zarezervovala a kolik se ve skutečnosti používá. Zobrazí se data v na virtuální počítač úrovně podrobností. 

Rozhraní API obsahuje:

-   **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určete, které Uživatelé nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure. 
-   **Filtrování** -Trim vaše rozhraní API sady výsledků do menší sadu rezervace pomocí filtru následující:
    - Rozsah dat
-   **Informace o rezervaci pro různé nabídka typy** -informace o vyhrazení je aktuálně dostupné pro zákazníky, Enterprise a webové Direct.

Další informace najdete v technické specifikaci pro [rezervace podrobnosti API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Rezervace souhrny rozhraní API

Použití, které toto rozhraní API zobrazíte souhrnné informace na dříve koupili rezervace virtuálních počítačů, jako je například kolik energie zarezervovala a kolik se ve skutečnosti se používá v souhrnném. 

Rozhraní API obsahuje:

-   **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určete, které Uživatelé nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure. 
-   **Filtrování** -přizpůsobit výsledky při použití denní intervalem s filtrem následující:
    - Datum využití
-   **Informace o rezervaci pro různé nabídka typy** -informace o vyhrazení je aktuálně dostupné pro zákazníky, Enterprise a webové Direct.
-   **Denní nebo měsíční agregace** – volající můžete určit, zda chtějí jejich rezervace souhrnná data v intervalem denní nebo měsíční.

Další informace najdete v technické specifikaci pro [rezervace souhrny API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Rozhraní API list cena
Zákazník Enterprise můžete použít toto rozhraní API k načtení jejich vlastní ceny pro všechny měřidla. Podniky můžete použít v kombinaci s informace o využití využití podrobnosti a tržišť provést výpočet nákladů na použití dat o využití a marketplace. 

Rozhraní API obsahuje:

-   **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určete, které Uživatelé nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure. 
-   **Podnikové zákazníky pouze** – tato rozhraní API je k dispozici zákazníkům EA. Webové přímého zákazníkům by měl použít rozhraní API RateCard získat ceny. 

Další informace najdete v technické specifikaci pro [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scénáře

Tady jsou některé scénáře, které jsou zpřístupněny prostřednictvím spotřeby rozhraní API:

-   **Fakturovat odsouhlasení** -nebyla Microsoft účtují na správnou částku?  Co je můj faktury a můžete vypočítat ho mne?
-   **Mezi poplatky** – nyní, lze zjistit, kolik I se vám byly účtovány poplatky, který v Moje organizace potřebuje věnovat?
-   **Náklady optimalizace** -lze zjistit, kolik I jste naúčtována... jak lze získat další mimo peníze, které I mě výdaje na Azure?
-   **Sledování nákladů** -chcete vidět, kolik jsem výdaje a pomocí Azure v čase. Jaké jsou trendy? Jak může I to lépe?
-   **Azure tráví v měsíci** – kolik je můj aktuálního měsíce je tráví k datu? Je nutné provést úpravy v mé výdaje a využití Azure? Když v měsíci se I využívání Azure nejvíce?
-   **Nastavení výstrah** peněžní na základě výstrahy podle rozpočet – nebo chcete nastavit prostředků na základě spotřeby.

## <a name="next-steps"></a>Další kroky

- Informace o použití rozhraní API Správce Azure fakturace k prostřednictvím kódu programu pohled na použití Azure najdete v tématu [fakturace přehled rozhraní API služby Azure](billing-usage-rate-card-overview.md).



