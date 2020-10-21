---
title: Získání informací o využití Azure pomocí rozhraní API pro fakturaci Azure
description: Seznamte se s rozhraními API pro fakturaci využití Azure a RateCard, která poskytují přehled o spotřebě prostředků Azure a trendech.
author: tonguyen
ms.reviewer: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: b3ae2b8323c9f278dcec432dfaac05e9fcfb4b49
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132104"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Programové získání přehledu o využití Azure pomocí rozhraní API pro fakturaci Azure
Rozhraní API pro fakturaci Azure můžete využít k předání dat o využití a prostředcích do vašeho upřednostňovaného nástroje pro datové analýzy. Rozhraní API využití a ceníku prostředků Azure vám pomohou přesně odhadnout a spravovat vaše náklady. Tato rozhraní API se implementují jako poskytovatel prostředků a jsou součástí řady rozhraní API, která zveřejňuje Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>Azure Invoice Download API (Preview)
Jakmile se [dokončí udělení přístupu](manage-billing-access.md#opt-in), můžete si pomocí verze Preview rozhraní [API Faktury](/rest/api/billing) stahovat faktury. Toto jsou některé z dostupných funkcí:

* **Řízení přístupu na základě role v Azure (Azure RBAC)** – Prostřednictvím konfigurace zásad přístupu na webu [Azure Portal](https://portal.azure.com) nebo v [rutinách Azure PowerShell](/powershell/azure/) můžete určit, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Pokud chcete, aby volající získal přístup k datům využití pro určité předplatné Azure, přidejte ho do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel.
* **Filtrování podle data** – pomocí parametru `$filter` můžete načíst všechny faktury v obráceném chronologickém pořadí podle koncového data období faktury.

> [!NOTE]
> Tato funkce je v první verzi Preview a může u ní dojít ke zpětně nekompatibilním změnám. V současné době není dostupná pro určité nabídky předplatného (smlouva Enterprise, program CSP a Azure v rámci licenčního programu Open se nepodporují) a v Azure Germany.

## <a name="azure-resource-usage-api-preview"></a>Azure Resource Usage API (Preview)
Pomocí [rozhraní API Využití prostředků Azure](/previous-versions/azure/reference/mt219003(v=azure.100)) můžete získat údaje o odhadované spotřebě Azure. Rozhraní API nabízí tyto funkce:

* **Řízení přístupu na základě role v Azure (Azure RBAC)** – Prostřednictvím konfigurace zásad přístupu na webu [Azure Portal](https://portal.azure.com) nebo v [rutinách Azure PowerShell](/powershell/azure/) můžete určit, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Pokud chcete, aby volající získal přístup k datům využití pro určité předplatné Azure, přidejte ho do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel.
* **Hodinová nebo denní agregace** – volající můžou určit, jestli chtějí údaje o využití Azure zobrazit v hodinových, nebo denních intervalech. Výchozí nastavení je denní interval.
* **Metadata instance (včetně značek prostředků)** – můžete získat podrobnosti na úrovni instance, třeba kvalifikovaný identifikátor URI prostředku (/subscriptions/{id-předplatného}/…), informace o skupině prostředků nebo značky prostředků. Tato metadata pomáhají deterministicky a programově přidělovat využití pomocí značek pro potřeby různých případů použití, jako je rozúčtování poplatků.
* **Metadata prostředků** – podrobnosti o prostředcích, jako je název měřiče, kategorie měřiče, podkategorie měřiče, jednotka a oblast, poskytují volajícímu lepší přehled o spotřebovaných prostředcích. Pracujeme také na sjednocení terminologie metadat prostředků na webu Azure Portal, v souboru CSV s informacemi o využití Azure, v souboru CSV o fakturaci smluv EA a v dalších veřejných prostředích, abyste viděli souvislosti mezi daty z různých prostředí.
* **Využití u různých typů nabídek** – informace o využití jsou dostupné pro různé typy nabídek, například nabídky s průběžnými platbami, MSDN, peněžní závazek, peněžní kredit a smlouvy EA, s výjimkou [CSP](/partner-center).

## <a name="azure-resource-ratecard-api-preview"></a>Azure Resource RateCard API (Preview)
Pomocí [rozhraní API RateCard pro prostředky Azure](/previous-versions/azure/reference/mt219005(v=azure.100)) získáte seznam dostupných prostředků Azure a informace o odhadovaných cenách každého z nich. Rozhraní API nabízí tyto funkce:

* **Řízení přístupu na základě role v Azure (Azure RBAC)** – Prostřednictvím konfigurace zásad přístupu na webu [Azure Portal](https://portal.azure.com) nebo v [rutinách Azure PowerShellu](/powershell/azure/) můžete určit, kteří uživatelé nebo aplikace můžou získat přístup k datům RateCard. Volající musí k ověření používat standardní tokeny Azure Active Directory. Pokud chcete, aby volající získal přístup k datům využití pro určité předplatné Azure, přidejte ho do role Čtenář, Vlastník nebo Přispěvatel.
* **Podpora nabídek s průběžnými platbami, MSDN, s peněžním závazkem a s peněžním kreditem (nepodporují se programy EA a [CSP](/partner-center))** – toto rozhraní API poskytuje informace o sazbách na úrovni nabídky.  Volající tohoto rozhraní API musí k zobrazení podrobností o prostředcích a sazeb zadat informace o nabídce. V tuto chvíli nemůžeme poskytovat sazby pro smlouvy EA, protože nabídky smluv EA mají vlastní sazby.

## <a name="scenarios"></a>Scénáře
Tady je několik scénářů, ve kterých můžete využít kombinaci rozhraní API s informacemi o využití a RateCard:

* **Náklady na Azure v průběhu měsíce** – díky kombinaci rozhraní API s informacemi o využití a RateCard můžete získat lepší přehled o útratě v cloudu během měsíce. Můžete analyzovat využití v hodinových a denních intervalech a odhady nákladů.
* **Nastavení výstrah** – pomocí rozhraní API s informacemi o využití a RateCard můžete získat odhad spotřeby cloudu a poplatků a nastavit výstrahy založené na prostředcích nebo peněžních částkách.
* **Předpověď faktury** – můžete získat odhad spotřeby a útraty v cloudu a pomocí algoritmů strojového učení předvídat, jaká bude výše faktury na konci fakturačního období.
* **Analýza nákladů před spotřebou** – pomocí rozhraní API RateCard můžete předpovídat, jaká bude výše faktury za očekávané využití, když přesunete své úlohy do Azure. Pokud máte stávající úlohy v jiných cloudech nebo privátních cloudech, můžete také namapovat jejich využívání na sazby Azure, a získat tak lepší odhad nákladů na Azure. Tento odhad vám poskytne lepší přehled nabídek a umožní vám porovnat různé typy nabídek nad rámec nabídek s průběžnými platbami, například nabídek s peněžním závazkem nebo peněžním kreditem. Rozhraní API vám taky umožní zobrazit rozdíly v nákladech podle oblastí a provést analýzu potenciálních nákladů, abyste mohli lépe rozhodovat o nasazení.
* **Analýza potenciálních nákladů** -

  * Můžete zjistit, jestli je cenově výhodnější spouštět úlohy v jiné oblasti nebo s jinou konfigurací prostředku Azure. Náklady na prostředky Azure se můžou lišit v závislosti na oblasti Azure, kterou používáte.
  * Můžete také zjistit, jestli by vám jiný typ nabídky Azure neposkytl lepší sazbu pro určitý prostředek Azure.


## <a name="next-steps"></a>Další kroky
* Podívejte se na ukázky kódu na GitHubu:
  * [Vzorový kód pro rozhraní API pro faktury](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Vzorový kód pro rozhraní API služby Usage](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Vzorový kód pro rozhraní API služby RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Další informace o Azure Resource Manageru najdete v tématu [Přehled Azure Resource Manageru](../../azure-resource-manager/management/overview.md).