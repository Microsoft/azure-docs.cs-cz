---
title: Přehled rozhraní API pro Azure spotřeber | Microsoft Docs
description: Přečtěte si, jak rozhraní API pro využití Azure poskytují programový přístup k datům o nákladech a využití vašich prostředků Azure.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 2a2922daef5e75af41252119f9a467fae976bcda
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443261"
---
# <a name="azure-consumption-api-overview"></a>Přehled rozhraní API pro využití Azure 

Rozhraní API služby Azure Consumption poskytuje programový přístup k informacím o nákladech a využití pro vaše prostředky Azure. Tato rozhraní API aktuálně podporují pouze podnikové registrace a odběry web Direct (s několika výjimkami). Rozhraní API se průběžně aktualizují, aby podporovala jiné typy předplatných Azure.

Rozhraní API služby Azure Consumption poskytuje přístup pro:
- Podnikoví a weboví přímí zákazníci 
    - Využití: podrobnosti 
    - Poplatky za marketplace 
    - Doporučení pro rezervace 
    - Podrobnosti rezervace 
    - Přehledy rezervace 
- Jenom pro podnikové zákazníky 
    - Ceník 
    - Rozpočty 
    - Zůstatky 

## <a name="usage-details-api"></a>Rozhraní API podrobností využití

Použijte rozhraní API s podrobnostmi o využití k získání poplatků a dat o využití pro všechny prostředky Azure od 1. května. Informace jsou ve formě záznamů s podrobnostmi o využití, které jsou aktuálně vydávány na měřiči na jeden prostředek za den. Informace se dají použít k přidání nákladů napříč všemi prostředky nebo k prošetření nákladů na používání určitých prostředků. 

Toto rozhraní API zahrnuje:

-   **Data spotřeby na úrovni měřičů** – Podívejte se na data, včetně nákladů na využití, měřiče, který vydává poplatky, a na jaký prostředek Azure se účtuje. Všechny záznamy podrobností o využití se mapují na denní interval.
-   **Access Control na základě rolí Azure** – konfigurace zásad přístupu v [Azure Portal](https://portal.azure.com), rutiny [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , které určují, kteří uživatelé nebo aplikace budou mít přístup k datům o použití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** – vystřihujte výsledek rozhraní API a zmenšete sadu podrobných záznamů využití pomocí následujících filtrů:
    - Využití – začátek/využití
    - Skupina prostředků
    - Název prostředku
-   **Agregace dat** – použití OData k použití výrazů k agregaci podrobností o využití pomocí značek nebo vlastností filtru
-   **Použití pro různé typy nabídek** – podrobné informace o využití jsou aktuálně k dispozici pro zákazníky v organizacích Enterprise a web Direct.

Další informace najdete v technické specifikaci [rozhraní API s podrobnostmi o využití](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>API pro poplatky na Marketplace

Použijte rozhraní API pro poplatky na webu Marketplace, abyste získali data o nákladech a využití na všech prostředcích Marketplace (nabídky Azure třetích stran). Tato data se dají použít k přidání nákladů na všechny prostředky na webu Marketplace nebo k prošetření nákladů na používání určitých prostředků. 

Toto rozhraní API zahrnuje:

-   **Data spotřeby na úrovni měřičů** – Podívejte se na data, včetně nákladů na využití Marketplace, měřiče, který vydává poplatky, a na to, k jakým prostředkům se vztahují poplatky. Všechny záznamy podrobností o využití se mapují na denní interval.
-   **Access Control na základě rolí Azure** – konfigurace zásad přístupu v [Azure Portal](https://portal.azure.com), rutiny [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , které určují, kteří uživatelé nebo aplikace budou mít přístup k datům o použití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** – vystřihujte výsledek rozhraní API na menší sadu záznamů Marketplace pomocí následujících filtrů:
    - Využití – konec spuštění/použití
    - Skupina prostředků
    - Název prostředku
-   **Použití pro různé typy nabídek** – informace na Marketplace jsou aktuálně k dispozici pro zákazníky v organizacích Enterprise a web Direct.

Další informace najdete v technické specifikaci pro [rozhraní API pro poplatky na webu Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Vyrovnávání rozhraní API

Podnikoví zákazníci mohou použít rozhraní API pro vyrovnávání salda k získání měsíčního souhrnu informací o zůstatcích, nových nákupech, Azure Marketplacech poplatků za služby, úpravách a překročení limitů. Tyto informace můžete získat pro aktuální fakturační období nebo libovolné období v minulosti. Podniky mohou tato data použít k porovnání s ručně vypočtenými souhrnnými poplatky. Toto rozhraní API neposkytuje informace specifické pro prostředky a agregované zobrazení nákladů. 

Toto rozhraní API zahrnuje:

-   **Access Control na základě rolí Azure** – konfigurace zásad přístupu v [Azure Portal](https://portal.azure.com), rutiny [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , které určují, kteří uživatelé nebo aplikace budou mít přístup k datům o použití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Pouze Podnikoví zákazníci** Toto rozhraní API je dostupné jenom pro zákazníky se smlouvou EA. 
    - Aby mohli zákazníci volat toto rozhraní API, musí mít oprávnění Enterprise Admin. 

Další informace najdete v technické specifikaci pro vyrovnávání [rozhraní API](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>Rozhraní API pro rozpočty

Podnikoví zákazníci můžou pomocí tohoto rozhraní API vytvářet náklady nebo rozpočty využití pro prostředky, skupiny prostředků nebo měřiče účtování. Po určení těchto informací je možné nastavit upozorňování na upozornění, když se překročí uživatelsky definované prahové hodnoty rozpočtu. 

Toto rozhraní API zahrnuje:

-   **Access Control na základě rolí Azure** – konfigurace zásad přístupu v [Azure Portal](https://portal.azure.com), rutiny [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , které určují, kteří uživatelé nebo aplikace budou mít přístup k datům o použití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Jenom Podnikoví zákazníci** – toto rozhraní API je dostupné jenom pro zákazníky se smlouvou EA.
-   **Konfigurovatelná oznámení** – Určete uživatele, kteří mají být upozorněni, když je rozpočet Trip.
-   **Využití nebo rozpočty založené na nákladech** – podle potřeby můžete vytvořit rozpočet na základě spotřeby nebo nákladů podle potřeb vašeho scénáře.
-   **Filtrování** – filtrování rozpočtu na menší podmnožinu prostředků pomocí následujících konfigurovatelných filtrů
    - Skupina prostředků
    - Název prostředku
    - Metr
-   **Konfigurovatelné rozpočtová časová období** – určete, jak často se má rozpočet resetovat a jak dlouho je rozpočet platný.

Další informace najdete v technické specifikaci [rozhraní API](https://docs.microsoft.com/rest/api/consumption/budgets)pro rozpočty.

## <a name="reservation-recommendations-api"></a>Rozhraní API doporučení rezervací

Pomocí tohoto rozhraní API získáte doporučení k nákupu rezervovaných instancí virtuálních počítačů. Doporučení jsou navržena tak, aby zákazníci mohli analyzovat očekávané úspory nákladů a částky nákupu. 

Toto rozhraní API zahrnuje:

-   **Access Control na základě rolí Azure** – konfigurace zásad přístupu v [Azure Portal](https://portal.azure.com), rutiny [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , které určují, kteří uživatelé nebo aplikace budou mít přístup k datům o použití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** – přizpůsobení výsledků doporučení pomocí následujících filtrů:
    - Scope
    - Lookback období
-   Informace o **rezervacích pro různé typy nabídek** – informace o rezervacích jsou aktuálně k dispozici pro zákazníky v organizacích Enterprise a web Direct.

Další informace najdete v technické specifikaci [rozhraní API pro rezervaci](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations)s doporučeními.

## <a name="reservation-details-api"></a>Rozhraní API podrobností rezervací

Pomocí rozhraní API s podrobnostmi rezervací si můžete zobrazit informace o dříve zakoupených rezervacích virtuálních počítačů, jako je počet rezervovaných spotřebování a množství, které je skutečně využíváno. Data můžete zobrazit na základě podrobností na úrovni virtuálních počítačů. 

Toto rozhraní API zahrnuje:

-   **Access Control na základě rolí Azure** – konfigurace zásad přístupu v [Azure Portal](https://portal.azure.com), rutiny [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , které určují, kteří uživatelé nebo aplikace budou mít přístup k datům o použití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** – vystřihujte výsledek rozhraní API na menší sadu rezervací pomocí následujícího filtru:
    - Rozsah dat
-   Informace o **rezervacích pro různé typy nabídek** – informace o rezervacích jsou aktuálně k dispozici pro zákazníky v organizacích Enterprise a web Direct.

Další informace najdete v technické specifikaci [rozhraní API pro podrobnosti rezervací](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Rozhraní API Shrnutí rezervací

Pomocí tohoto rozhraní API si můžete zobrazit agregované informace o dříve zakoupených rezervacích virtuálních počítačů, jako je počet rezervovaných spotřeb a jejich skutečné využití v agregaci. 

Toto rozhraní API zahrnuje:

-   **Access Control na základě rolí Azure** – konfigurace zásad přístupu v [Azure Portal](https://portal.azure.com), rutiny [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , které určují, kteří uživatelé nebo aplikace budou mít přístup k datům o použití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Filtrování** – při použití denního zrnitosti podle následujícího filtru upravte výsledky.
    - Datum využití
-   Informace o **rezervacích pro různé typy nabídek** – informace o rezervacích jsou aktuálně k dispozici pro zákazníky v organizacích Enterprise a web Direct.
-   **Denní nebo měsíční agregace** – volající můžou určit, jestli mají svá souhrnná data rezervace v denním nebo měsíčním zrnitosti.

Další informace najdete v technické specifikaci [rozhraní API pro souhrny rezervací](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Rozhraní API ceníku
Podnikoví zákazníci můžou pomocí tohoto rozhraní API načíst své vlastní ceny pro všechny měřiče. Podniky mohou tuto kombinaci využít společně s podrobnostmi o využití a informacemi o využití Marketplace k provádění výpočtů nákladů pomocí dat využití a Marketplace. 

Toto rozhraní API zahrnuje:

-   **Access Control na základě rolí Azure** – konfigurace zásad přístupu v [Azure Portal](https://portal.azure.com), rutiny [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , které určují, kteří uživatelé nebo aplikace budou mít přístup k datům o použití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure. 
-   **Jenom Podnikoví zákazníci** – toto rozhraní API je dostupné jenom pro zákazníky se smlouvou EA. Zákazníci používající web Direct by měli použít rozhraní RateCard API k získání cen. 

Další informace najdete v technické specifikaci pro [rozhraní API ceníku](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scénáře

Zde jsou některé ze scénářů, které lze provést prostřednictvím rozhraní API pro vykonávání:

-   **Odsouhlasení faktury** – od Microsoftu se účtují správné množství?  Co je moje faktura a můžu ji vypočítat?
-   **Křížové poplatky** – teď, když mám vědět, kolik jsem se mi účtuje, kdo musí zaplatit?
-   **Optimalizace nákladů** – nevím, kolik jsem vám bylo účtováno... Jak můžu využít peníze, které jsem stráví v Azure?
-   **Sledování nákladů** – Chci zjistit, kolik stojí a Azure využívá v průběhu času. Co jsou trendy? Jak se dá lépe dělat?
-   Náklady na **Azure v průběhu měsíce** – kolik je moje útrata v aktuálním měsíci aktuální? Musím v naší útratě a používání Azure dělat nějaké úpravy? Když v měsíci spotřebováváte Azure nejvíc?
-   **Nastavte upozornění** – chci nastavit spotřebu na základě prostředků nebo peněžní výstrahy na základě rozpočtu.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak pomocí rozhraní API pro fakturaci Azure programově získat přehled o využití Azure, najdete v tématu [Přehled rozhraní API pro fakturaci Azure](billing-usage-rate-card-overview.md).



