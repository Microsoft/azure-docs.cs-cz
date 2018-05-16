---
title: Rozhraní API Azure fakturace | Microsoft Docs
description: Další informace o využití fakturace Azure a RateCard rozhraní API, které poskytují přehled o využívání prostředků Azure a trendy.
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: mobandyo
ms.openlocfilehash: e26d8ad1f08eb711dc22b45d48f8dc326d8c17c7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Prostřednictvím kódu programu pohled na použití Azure pomocí rozhraní API Správce Azure fakturace
Používáte rozhraní API Správce Azure fakturace k získání dat využití a prostředků do vašeho nástrojů pro analýzu dat upřednostňované. Rozhraní API RateCard a využití prostředků Azure vám může pomoct přesně předpovědět a náklady na správu. Rozhraní API jsou implementovány jako poskytovatel prostředků a součástí řady rozhraní API vystavené pomocí Správce prostředků Azure.  

## <a name="azure-invoice-download-api-preview"></a>Rozhraní API stažení faktury Azure (Preview)
Jednou [výslovný souhlas byl dokončení](billing-manage-access.md#opt-in), stažení faktury pomocí verze preview [faktury API](/rest/api/billing). Funkce patří:

* **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com) nebo pomocí [rutin prostředí Azure PowerShell](/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k data o využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure.
* **Datum filtrování** -použití `$filter` parametr chcete všechny faktury v obráceném chronologickém pořadí podle data vystavení faktury konec období. 

> [!NOTE]
> Tato funkce je v první verzi preview a mohou podléhat změny zpětně kompatibilní. V současné době není k dispozici pro některé nabídky předplatného (EA, CSP AIO není podporována.) a Azure v Německu.

## <a name="azure-resource-usage-api-preview"></a>Rozhraní API (Preview) pro využití prostředků Azure.
Použití Azure [API využití prostředků](https://msdn.microsoft.com/library/azure/mt219003) načíst vaše data Odhadované využití platformy Azure. Rozhraní API obsahuje:

* **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com) nebo pomocí [rutin prostředí Azure PowerShell](/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k data o využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure.
* **Hodinové nebo denní agregace** – volající můžete určit, jestli chtějí jejich Azure využití dat každou hodinu intervalů nebo denních intervalů. Výchozí hodnota je denně.
* **Instance metadat (zahrnuje značky prostředku)** – získání podrobností na úrovni instance jako identifikátor uri prostředku plně kvalifikovaný (/subscriptions/ {id předplatného} /..), informace o skupině prostředků a značky prostředku. Tato metadata vám deterministicky a prostřednictvím kódu programu přidělit využití podle značky, pomůže pro případy použití mezi poplatků.
* **Metadata prostředků** – prostředek podrobnosti například měření název, kategorie měření, měřicí dílčí kategorie, jednotky a oblast poskytnout volající lépe porozumět tomu, co se spotřebovala. Pracujeme také zarovnat prostředků metadata terminologie přes portál Azure, Azure použití sdíleného svazku clusteru, EA fakturace CSV a dalších činnostech veřejné umožňují vazbu mezi data v prostředí.
* **Použití pro jiné nabídka typy** – data o využití je k dispozici pro typy nabídek jako průběžné platby, MSDN, peněžních závazků, peněžního kreditu, který a EA, s výjimkou [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>RateCard prostředků Azure rozhraní API (Preview)
Použití [rozhraní API služby Azure prostředků RateCard](https://msdn.microsoft.com/library/azure/mt219005) získat seznam dostupných prostředků Azure a odhadovanou informace o cenách pro jednotlivé. Rozhraní API obsahuje:

* **Řízení přístupu Azure na základě rolí** -konfigurace zásad přístupu na [portál Azure](https://portal.azure.com) nebo pomocí [rutin prostředí Azure PowerShell](/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k RateCard data. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure.
* **Podpora průběžné platby, MSDN, peněžních závazků a peněžního kreditu, který nabízí (EA a [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) není podporován)** – tato rozhraní API poskytuje Azure míra nabídka úroveň informace.  Volající toto rozhraní API musí projít informace nabídka získat podrobnosti zdroje a sazby. Nemohli jsme aktuálně zajistit EA sazby, protože nabízí EA přizpůsobili sazby za registraci. 

## <a name="scenarios"></a>Scénáře
Tady jsou některé scénáře, které lze vytvořit pomocí kombinace využití a rozhraní API RateCard:

* **Azure tráví v měsíci** – pomocí kombinace využití serveru a rozhraní API RateCard získat lepší přehled o vašem cloudu tráví v měsíci. Můžete analyzovat kbelíků hodinové a denní využití a poplatků odhad.
* **Nastavení výstrah** – použijte využití a rozhraní API RateCard získat odhadovaný cloudu využívání a poplatky, a nastavte založené na prostředcích nebo peněžní na základě výstrahy.
* **Předpovídat faktury** – Get odhadované spotřeby a cloud tráví a použít algoritmy strojového učení k předvídání co kusovníku bude na konci fakturačního cyklu.
* **Předběžné spotřeba analýza nákladů** – použít rozhraní API RateCard odhadnout, kolik vaše faktura by byl očekávané využití při přesunutí úlohy do Azure. Pokud máte existující úlohy v ostatních cloudů nebo privátní cloudy, můžete namapovat vaše použití s Azure tráví sazby získat lepší odhad Azure. Tento odhad dává možnost otáčení na nabídka a porovnání a kontrast mezi typy různých nabídka nad rámec průběžné platby, jako peněžních závazků a peněžního kreditu. Rozhraní API také vám umožní zobrazit náklady rozdíly podle oblasti a umožňuje provádět analýzy citlivostních náklady který vám pomůže provádět rozhodnutí o nasazení.
* **Analýz** -
  
  * Můžete určit, zda je cenově výhodnější spuštění úloh v jiné oblasti nebo na jinou konfiguraci prostředků Azure. Náklady na prostředků Azure se můžou lišit podle na oblast Azure, kterou používáte.
  * Můžete také určit, pokud jiný typ nabídky Azure poskytuje lepší rychlost na prostředek služby Azure.
  
## <a name="partner-solutions"></a>Partnerská řešení
[Cloud Cruiser a fakturace integrace rozhraní API aplikace Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) popisuje, jak [Express Cloud Cruiser pro Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) funguje přímo z portálu Windows Azure Pack (WAP). Můžete spravovat bezproblémově provozu a finanční aspekty Microsoft Azure privátní nebo hostované veřejného cloudu z jediného uživatelského rozhraní.   

## <a name="next-steps"></a>Další postup
* Podívejte se na ukázky kódu na Githubu:
  * [Ukázka kódu faktury rozhraní API](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Ukázka kódu rozhraní API pro využití](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Ukázka kódu rozhraní API RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Další informace o Azure Resource Manager najdete v tématu [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md). 



