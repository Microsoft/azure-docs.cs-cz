---
title: Získat využití Azure s Azure API pro fakturaci. | Dokumentace Microsoftu
description: Přečtěte si o Azure Billing využití a RateCard API, které slouží k poskytování přehledů o spotřebě prostředků Azure a trendy.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: erikre
ms.custom: seodec18
ms.openlocfilehash: 9e3ad2327297e0403b279ad40db2186fcb55ae93
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080975"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Pomocí Azure API pro fakturaci prostřednictvím kódu programu získáte přehled o využívání služeb Azure
Pomocí Azure API pro fakturaci. data o využití a prostředků o přijetí změn do nástroje pro vaše preferované datové analýzy. Rozhraní API využití a ceníku prostředků Azure vám pomohou přesně odhadnout a spravovat vaše náklady. Rozhraní API se implementují jako poskytovatele prostředků a součástí rodiny API pomocí Azure Resource Manageru.  

> [!div class="nextstepaction"]
> [Pomozte vylepšit dokumentaci k fakturaci Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="azure-invoice-download-api-preview"></a>Rozhraní API pro stažení faktury Azure (Preview)
Jednou [vyjádřit výslovný souhlas se kompletní](billing-manage-access.md#opt-in), stahování faktur ve verzi preview [faktury API](/rest/api/billing). Mezi funkce patří:

* **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com) nebo prostřednictvím [rutin prostředí Azure PowerShell](/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k data o využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure.
* **Datum filtrování** – použití `$filter` parametr zobrazíte všechny faktury v obráceném pořadí časovém období koncové datum faktury. 

> [!NOTE]
> Tato funkce je v první verzi preview a může být v souladu s změny zpětně kompatibilní. V současné době není k dispozici pro některé nabídky předplatného (EA, CSP, AIO nepodporuje) a Azure Germany.

## <a name="azure-resource-usage-api-preview"></a>Využití prostředků Azure, rozhraní API (Preview)
Použití Azure [rozhraní API využití prostředků](https://msdn.microsoft.com/library/azure/mt219003) k přesunu dat Odhadované využití Azure. Toto rozhraní API zahrnuje:

* **Řízení přístupu Azure na základě rolí** – Konfigurace zásady přístupu na [webu Azure portal](https://portal.azure.com) nebo prostřednictvím [rutin prostředí Azure PowerShell](/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k data o využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure.
* **Hodinové nebo denní agregace** – volající můžete určit, zda chtějí svá data využití Azure každou hodinu intervalů nebo denních intervalů. Výchozí hodnota je denně.
* **Instance metadata (včetně značky prostředku)** – získání podrobností na úrovni instance jako prostředek plně kvalifikovaný identifikátor uri (/subscriptions/ {id předplatného} /..), informace o skupinách prostředků a značky prostředku. Tato metadata vám deterministicky a programově přidělit využití podle klíčových slov, pro případy použití, jako jsou různé účtování.
* **Resource metadata** – podrobnosti o prostředku, například název měřiče, kategorie měřiče, podkategorie měřiče, jednotky a oblasti dejte volající lépe pochopit, co spotřebovával. Spolupracujeme také zarovnat terminologie resource metadata ve na webu Azure portal, Azure použití sdíleného svazku clusteru, EA fakturace sdíleného svazku clusteru a další veřejné webové prostředí, vám umožní korelovat data napříč prostředím.
* **Využití pro typy jinou nabídku** – data o využití je k dispozici pro typy nabídek jako průběžné platby MSDN, peněžního závazku, peněžní kredit ve výši a EA, s výjimkou [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Prostředek Azure RateCard API (Preview)
Použití [Azure Resource RateCard API](https://msdn.microsoft.com/library/azure/mt219005) zobrazíte seznam dostupných prostředků Azure a odhadovaných informace o cenách pro všechny. Toto rozhraní API zahrnuje:

* **Řízení přístupu Azure na základě rolí** – konfigurace zásad přístupu na [webu Azure portal](https://portal.azure.com) nebo prostřednictvím [rutin prostředí Azure PowerShell](/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k RateCard data. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář, vlastník nebo Přispěvatel získat přístup k datům využití ke konkrétnímu předplatnému Azure.
* **Podpora pro průběžné platby, MSDN, peněžního závazku a nabídek peněžních kreditů (EA a [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) nepodporuje)** -toto rozhraní API poskytuje informace sazeb Azure úrovně nabídky.  Volající toto rozhraní API musíte předat informace o nabídce zobrazíte podrobnosti o prostředku a kurzy. Aktuálně nám poskytnout sazby EA, protože nabídky EA upravili sazby za registraci. 

## <a name="scenarios"></a>Scénáře
Tady jsou některé scénáře, které jsou možné kombinací využití a RateCard API:

* **Výdaje za Azure za daný měsíc** – použijte kombinaci využití a RateCard API získat lepší přehled o své cloudové výdaje za daný měsíc. Můžete analyzovat po hodinách a denní sektorů odhadne využití a poplatků.
* **Nastavení výstrah** – využít k získání odhadovaných cloudové využití a poplatky za a nastavit výstrahy na základě prostředků nebo peněžní na využití a RateCard API.
* **Předpověď vyúčtování** – Get Odhadované využití a cloudové výdaje a použití algoritmů strojového učení k předpovědi, co bude na faktuře na konci fakturačního cyklu.
* **Předběžné spotřeby analýza nákladů** – použijte RateCard API předpovědět, kolik vaše faktura se pro očekávané využití při přesun úloh do Azure. Pokud máte existující úlohy v jiných cloudech nebo privátních cloudů, využití s Azure můžete také namapovat sazby získat lepší odhad Azure výdajů. Tento odhad poskytuje schopnost zaměření na nabídku a porovnat a kontrast mezi typy jinou nabídku nad rámec s průběžnými platbami, jako je peněžních závazků a peněžního kreditu. Rozhraní API také nabízí možnost zobrazit rozdíly náklady podle oblasti a umožňuje provádět analýzy what-if nákladů pro vám pomůže zajistit rozhodnutí o nasazení.
* **Analýza "co kdyby"** -
  
  * Můžete určit, zda je cenově výhodnější ke spouštění úloh v jiné oblasti nebo na jinou konfiguraci prostředků Azure. Náklady na prostředky Azure může lišit v závislosti na oblasti Azure, které používáte.
  * Můžete také určit, pokud jiný typ nabídky Azure poskytuje lepší rychlost v prostředku Azure.
  
## <a name="partner-solutions"></a>Partnerská řešení
[Integrace rozhraní API pro fakturaci Microsoft Azure a řešení Cloud Cruiseru](billing-usage-rate-card-partner-solution-cloudcruiser.md) popisuje, jak [sada Express for Azure od Cloud Cruiseru](http://www.cloudcruiser.com/partners/microsoft/) pracuje přímo z portálu Windows Azure Pack (WAP). Provozní i finanční aspekty privátního nebo hostovaného veřejného cloudu Microsoft Azure můžete bez problémů spravovat pomocí jednoho uživatelského rozhraní.   

## <a name="next-steps"></a>Další postup
* Prohlédněte si ukázky kódu na Githubu:
  * [Vzorový kód pro rozhraní API pro faktury](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Vzorový kód pro rozhraní API služby Usage](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Vzorový kód pro rozhraní API služby RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Další informace o Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md). 



