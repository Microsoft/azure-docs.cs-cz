---
title: Podrobné informace o využití a poplatky za | Dokumentace Microsoftu
description: Zjistěte, jak číst a podrobné informace o využití a nákladů
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 2eb9f8e19be2a7b6220bc34bf4ce0c72c4ac0b4f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275070"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Vysvětlení podmínky v souboru využití a poplatky za Azure

Podrobné využití a poplatků soubor obsahuje denního využití hodnocených podle vyjednávaný sazby, nákup (například rezervace, poplatky webu Marketplace) a finanční prostředky zadanou dobu.
Poplatky za nezahrnují kredity, daní, nebo jiné poplatky ani slevy.
Následující tabulka popisuje které poplatky jsou zahrnuté pro každý typ účtu.

Typ účtu | Využití Azure | Využití webu Marketplace | Nákup | Vrácení peněz
--- | --- | --- | --- | ---
Smlouva Enterprise (EA) | Ano | Ano | Ano | Ne
Smlouva se zákazníkem Microsoftu (MCA) | Ano | Ano | Ano | Ano
Průběžných plateb (PAYG) | Ano | Ne | Ne | Ne

Další informace o objednávkách Marketplace (označované také jako externí služba) najdete v tématu [vysvětlení Azure poplatků za externí služby](billing-understand-your-azure-marketplace-charges.md).

Zobrazit [jak získat Azure billing invoice a denní data o využití](billing-download-azure-invoice-daily-usage-date.md) pokyny ke stažení.
Je k dispozici ve formátu souboru hodnot oddělených čárkami (CSV), který můžete otevřít v aplikaci tabulky soubor využití a poplatků.

## <a name="list-of-terms-and-descriptions"></a>Seznam termínů a popisy

Následující tabulka popisuje důležité termíny používané v nejnovější verzi Azure file využití a poplatků.
Seznam popisuje průběžných plateb (PAYG), smlouvy Enterprise (EA) a účty Microsoft zákazníka smlouvy (MCA).

Termín | Typ účtu | Popis
--- | --- | ---
AccountName | EA | Zobrazovaný název registrace účtu.
AccountOwnerId | EA | Jedinečný identifikátor pro účet pro zápis.
AdditionalInfo | Vše | Metadata konkrétních služeb. Například typ image u virtuálního počítače.
BillingAccountId | EA, MCA | Jedinečný identifikátor pro kořenovou fakturační účet.
BillingAccountName | EA, MCA | Název fakturačního účtu.
BillingCurrency | EA, MCA | Měně přidružené k fakturačnímu účtu.
BillingPeriod | EA | Fakturační období poplatek.
BillingPeriodEndDate | EA, MCA | Koncové datum fakturačního období.
BillingPeriodStartDate | EA, MCA | Počáteční datum fakturačního období.
BillingProfileId | EA, MCA | Jedinečný identifikátor registraci smlouvy Enterprise nebo MCA fakturační profil.
BillingProfileName | EA, MCA | Název registraci smlouvy Enterprise nebo MCA fakturační profil.
ChargeType | EA, MCA | Určuje, zda se příslušný poplatek představuje využití (**využití**), nákup (**zakoupit**), nebo refundaci (**refundaci**).
ConsumedQuantity | PRŮBĚŽNÉ PLATBY | Podívejte se na množství.
ConsumedService | Vše | Název služby pro příslušný poplatek souvisí s.
Náklady | EA | Zobrazit CostInBillingCurrency.
Nákladové středisko | EA, MCA | Nákladové středisko definované pro předplatné pro sledování nákladů (k dispozici pouze v open fakturačních období pro MCA účty).
CostInBillingCurrency | MCA | Náklady na poplatek za fakturační měna před kredity nebo daně.
CostInPricingCurrency | MCA | Náklady na poplatku za v cenách měně před kredity nebo daně.
Měna | PRŮBĚŽNÉ PLATBY | Zobrazit BillingCurrency.
Datum | EA, MCA | Používání nebo zakoupení datum poplatku.
ExchangeRateDate | MCA | Datum, kdy bylo vytvořeno kurzu.
ExchangeRatePricingToBilling | MCA | Směnného kurzu slouží k převodu náklady v cenách měně fakturační Měna.
Frekvence | EA, MCA | Určuje, zda částkou, která má opakujte. Poplatky za buď může dojít po (**OneTime**), opakování na základě měsíční nebo roční (**periodický**), nebo v závislosti na využití (**UsageBased**).
includedQuantity | PRŮBĚŽNÉ PLATBY | Množství měřiče, které je zdarma v aktuálním fakturačním období.
InstanceId | PAGY | Zobrazit ID prostředku.
InvoiceId | EA, MCA | ID dokumentu jedinečné uvedené na faktuře PDF.
invoiceSection | MCA | See InvoiceSectionName.
InvoiceSectionId | EA, MCA | Jedinečný identifikátor pro EA oddělení nebo MCA části faktury.
InvoiceSectionName | EA, MCA | Název smlouvy EA oddělení nebo MCA části faktury.
IsAzureCreditEligible | EA, MCA | Určuje, zda je příslušný poplatek nárok na používání kreditů Azure se budou hradit (hodnoty: Hodnota TRUE, False).
IsEstimated | Vše | Určuje, zda fakturačního období uzavření nebo dokončení. Využití dat můžete změnit v průběhu fakturačního období, dokud se vygeneruje faktury. Tyto záznamy jsou označeny jako "odhadované".
Location | EA, MCA | Umístění datového centra, ve kterém prostředek běží.
meterCategory | Vše | Název kategorie klasifikace pro měřidla. Například *cloudových služeb* a *sítě*.
ID měřiče | Vše | Jedinečný identifikátor pro měřidla.
meterName | Vše | Název měřidla.
MeterRegion | Vše | Název umístění datacentra u služeb, jejichž cena podle umístění. Podívejte se na umístění.
meterSubCategory | Vše | Název dílčí klasifikace v rámci kategorie měřiče.
offerId | EA, MCA | Název nabídky zakoupili.
PartNumber | EA | Identifikátor sloužící k získání konkrétní měřičů, ceny.
PlanName | EA | Název plánu služby webu Marketplace.
PreviousInvoiceId | MCA | Odkaz na původní faktury, pokud je tato položka řádku refundaci.
pricingCurrency | MCA | Měna použitá při hodnocení podle vyjednávaný ceny.
Product | MCA | Zobrazit ProductName.
productId | EA, MCA | Jedinečný identifikátor pro produkt.
ProductName | EA | Název produktu.
productOrderId | EA, MCA | Jedinečný identifikátor pro produkt pořadí.
productOrderName | EA, MCA | Jedinečný název pro objednávku produktu.
Název vydavatele | EA, MCA | Vydavatele služby webu Marketplace.
PublisherType | EA, MCA | Typ aplikace publisher (hodnoty: firstParty thirdPartyReseller, thirdPartyAgency).
Množství | EA, MCA | Počet jednotek zakoupili nebo spotřebě.
Sazba | PRŮBĚŽNÉ PLATBY | Zobrazit UnitPrice.
ReservationId | EA, MCA | Jedinečný identifikátor pro zakoupené rezervované instance.
ReservationName | EA, MCA | Název instance zakoupenou rezervaci.
ResourceGroupId | EA, MCA | Jedinečný identifikátor [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) prostředek je v.
ResourceGroupName | EA, MCA | Název [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) prostředek je v.
ResourceId | EA, MCA | Jedinečný identifikátor [Azure Resource Manageru](https://docs.microsoft.com/rest/api/resources/resources) prostředků.
resourceLocation | EA, MCA | Umístění datového centra, ve kterém prostředek běží. Podívejte se na umístění.
ResourceName | EA | Název prostředku.
ResourceType | MCA | Typ instance prostředku.
serviceFamily | EA, MCA | Řada služby, které patří službě.
ServiceInfo1 | Vše | Metadata konkrétních služeb.
ServiceInfo2 | Vše | Starší verze pole s volitelná metadata konkrétních služeb.
ServicePeriodEndDate | MCA | Datum ukončení hodnocení období, které definované a uzamčen, ceny za služby využité nebo co si.
ServicePeriodStartDate | MCA | Počáteční datum platnosti hodnocení, které definované a uzamčen, ceny za služby využité nebo co si.
SubscriptionId | Vše | Jedinečný identifikátor pro předplatné.
subscriptionName | Vše | Název předplatného.
Tags | Vše | Značky přiřazenou k prostředku. Neobsahuje značky skupiny prostředků. Slouží k seskupení nebo rozdělit náklady na interní vrácení peněz. Další informace najdete v tématu [uspořádání prostředků Azure pomocí značek](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Jednotka | PRŮBĚŽNÉ PLATBY | Zobrazit UnitOfMeasure.
unitOfMeasure | Vše | Jednotka měření pro fakturaci pro službu. Například výpočetní služby se účtují po hodinách.
UnitPrice | EA | Cena za jednotku pro příslušný poplatek.
UsageDate | PRŮBĚŽNÉ PLATBY | Podívejte se na data.

Všimněte si, že některá pole se může lišit v malá a velká písmena a mezery mezi typy účtů.
Starší verze souborů s průběžnými platbami využití mají samostatnými sekcemi pro příkazu a denního využití.

## <a name="ensure-that-your-charges-are-correct"></a>Ujistěte se, že jsou správné poplatky

Další informace o podrobné informace o využití a nákladů, přečtěte si o tom, jak pochopit vaše [s průběžnými platbami](./billing-understand-your-bill.md) nebo [smlouvy zákazníka se společností Microsoft](billing-mca-understand-your-bill.md) faktury.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- [Zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazovat a stahovat využití Microsoft Azure a poplatky za](billing-download-azure-daily-usage.md)
