---
title: Vysvětlení podrobných informací o využití a poplatcích | Microsoft Docs
description: Naučte se číst podrobné informace o využití a poplatcích a rozumět jim.
author: bandersmsft
manager: micflan
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 93fad58e971d761f626b79dc68f9576ccc2e1892
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993996"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Vysvětlení termínů v souboru s využitím a poplatky Azure

Soubor s podrobnými informacemi o využití a poplatcích obsahuje využití podle denní sazby na základě sjednaných sazeb, nákupy (například rezervace, poplatky Marketplace) a refundace pro zadané období.
Poplatky nezahrnují kredity, daně nebo jiné poplatky či slevy.
Následující tabulka ukazuje, které poplatky jsou pro jednotlivé typy účtu zahrnuté.

Typ účtu | Využití Azure | Využití Marketplace | Nákupy | Refundace
--- | --- | --- | --- | ---
Smlouva Enterprise (EA) | Ano | Ano | Ano | Ne
Smlouva se zákazníkem Microsoftu (MCA) | Ano | Ano | Ano | Ano
Průběžné platby (PAYG) | Ano | Ano | Ne | Ne

Další informace o objednávkách Marketplace (označovaných také jako externí služby) najdete v tématu [Vysvětlení poplatků za externí služby Azure](understand-azure-marketplace-charges.md).

Pokyny pro stažení najdete v tématu, které vysvětluje, [jak získat fakturu a informace o denním využívání Azure](../manage/download-azure-invoice-daily-usage-date.md).
Soubor CSV s využitím a poplatky můžete otevřít v Microsoft Excelu nebo v jiné tabulkové aplikaci.

## <a name="list-of-terms-and-descriptions"></a>Seznam termínů a popisů

Následující tabulka popisuje důležité termíny používané v nejnovější verzi souboru s využitím a poplatky Azure.
Seznam zahrnuje účty s průběžnými platbami (PAYG), smlouvu Enterprise (EA) a smlouvu se zákazníkem Microsoftu (MCA).

Doba účinnosti | Typ účtu | Popis
--- | --- | ---
AccountName | EA, PAYG | Zobrazovaný název účtu registrace EA nebo fakturačního účtu PAYG.
AccountOwnerId<sup>1</sup> | EA, PAYG | Jedinečný identifikátor pro účet registrace EA nebo fakturační účet PAYG.
AdditionalInfo | Všechno | Metadata konkrétních služeb. Například typ image u virtuálního počítače.
BillingAccountId<sup>1</sup> | Všechno | Jedinečný identifikátor kořenového fakturačního účtu.
BillingAccountName | Všechno | Název fakturačního účtu.
BillingCurrency | Všechno | Měna přidružená k fakturačnímu účtu.
BillingPeriod | EA, PAYG | Fakturační období poplatku.
BillingPeriodEndDate | Všechno | Koncové datum fakturačního období.
BillingPeriodStartDate | Všechno | Počáteční datum fakturačního období.
BillingProfileId<sup>1</sup> | Všechno | Jedinečný identifikátor registrace EA, předplatného PAYG, fakturačního profilu MCA nebo konsolidovaného účtu AWS.
BillingProfileName | Všechno | Název registrace EA, předplatného PAYG, fakturačního profilu MCA nebo konsolidovaného účtu AWS.
ChargeType | Všechno | Označuje, jestli poplatek představuje využití (**Usage**), nákup (**Purchase**) nebo refundaci (**Refund**).
ConsumedService | Všechno | Název služby, ke které je poplatek přidružen.
CostCenter<sup>1</sup> | EA, MCA | Nákladové středisko definované pro předplatné pro sledování nákladů (k dispozici pouze v otevřených fakturačních obdobích pro účty MCA).
Náklady | EA, PAYG | Viz CostInBillingCurrency.
CostInBillingCurrency | MCA | Náklady na poplatek ve fakturační měně před kredity nebo daněmi.
CostInPricingCurrency | MCA | Náklady na poplatek v měně pro ceny před kredity nebo daněmi.
Měna | EA, PAYG | Viz BillingCurrency.
Date<sup>1</sup> | Všechno | Datum využití nebo nákupu poplatku.
EffectivePrice | Všechno | Smíšená jednotková cena pro období. Smíšené ceny jsou průměrem všech výkyvů v jednotkové ceně, jako je například odstupňované vrstvení, které snižuje cenu při nárůstu množství v průběhu času.
ExchangeRateDate | MCA | Datum stanovení směnného kurzu.
ExchangeRatePricingToBilling | MCA | Směnný kurz používaný k převodu nákladů v měně pro ceny na fakturační měnu.
Frequency | Všechno | Určuje, jestli se očekává opakování poplatku. Poplatky se můžou účtovat jednorázově (**OneTime**), opakovat se na měsíční, nebo roční bázi (**Recurring**), nebo vycházet z využití (**UsageBased**).
InvoiceId | PAYG, MCA | Jedinečné ID dokumentu uvedené v souboru PDF faktury.
InvoiceSection | MCA | Viz InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Jedinečný identifikátor oddílu pro fakturaci EA nebo MCA.
InvoiceSectionName | EA, MCA | Název oddílu pro fakturaci EA nebo MCA.
IsAzureCreditEligible | Všechno | Určuje, jestli má poplatek nárok na platby za použití kreditů Azure (hodnoty: true, false).
Umístění | MCA | Umístění datacentra, ve kterém prostředek běží.
MeterCategory | Všechno | Název klasifikační kategorie měřiče. Například *Cloud Services* a *sítě*.
MeterId<sup>1</sup> | Všechno | Jedinečný identifikátor měřiče.
MeterName | Všechno | Název měřiče.
MeterRegion | Všechno | Název umístění datacentra pro služby s cenami založenými na umístění. Viz Location.
MeterSubCategory | Všechno | Název kategorie dílčí klasifikace měřiče.
OfferId<sup>1</sup> | Všechno | Název zakoupené nabídky.
PartNumber<sup>1</sup> | EA, PAYG | Identifikátor používaný k získání cen konkrétního měřiče.
PlanName | EA, PAYG | Název plánu Marketplace.
PreviousInvoiceId | MCA | Odkaz na původní fakturu, pokud tato řádková položka představuje refundaci.
PricingCurrency | MCA | Měna použitá při hodnocení na základě sjednaných cen.
Produkt | Všechno | Název produktu
ProductId<sup>1</sup> | MCA | Jedinečný identifikátor pro produkt.
ProductOrderId | Všechno | Jedinečný identifikátor objednávky produktu.
ProductOrderName | Všechno | Jedinečný název objednávky produktu.
Název vydavatele | Všechno | Název vydavatele služeb z Marketplace.
PublisherType | Všechno | Typ vydavatele (hodnoty: **Azure**, **AWS**, **Marketplace**)
Množství | Všechno | Počet zakoupených nebo spotřebovaných jednotek.
ReservationId | EA, MCA | Jedinečný identifikátor zakoupené instance rezervace.
ReservationName | EA, MCA | Název zakoupené instance rezervace.
ResourceGroup | Všechno | Název [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), ve které se prostředek nachází.
ResourceId<sup>1</sup> | Všechno | Jedinečný identifikátor prostředku [Azure Resource Manageru](https://docs.microsoft.com/rest/api/resources/resources).
ResourceLocation | Všechno | Umístění datacentra, ve kterém prostředek běží. Viz Location.
ResourceName | EA, PAYG | Název prostředku.
ResourceType | MCA | Typ instance prostředku.
ServiceFamily | MCA | Řada služeb, do které služba patří.
ServiceInfo1 | Všechno | Metadata konkrétních služeb.
ServiceInfo2 | Všechno | Starší pole s volitelnými metadaty konkrétní služby.
ServicePeriodEndDate | MCA | Koncové datum období hodnocení, které má nastavené a uzamčené ceny za využívanou nebo zakoupenou službu.
ServicePeriodStartDate | MCA | Počáteční datum období hodnocení, které má nastavené a uzamčené ceny za využívanou nebo zakoupenou službu.
SubscriptionId<sup>1</sup> | Všechno | Jedinečný identifikátor předplatného Azure.
SubscriptionName | Všechno | Název předplatného Azure.
Tags<sup>1</sup> | Všechno | Značky přiřazené k prostředku. Nezahrnuje značky skupiny prostředků. Dá se použít k seskupení nebo distribuci nákladů pro interní vrácení peněz. Další informace najdete v článku o [uspořádání prostředků Azure pomocí značek](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Doba účinnosti | Všechno | Zobrazí období platnosti nabídky. Například: v případě rezervovaných instancí se jako výraz zobrazí 12 měsíců. Pro jednorázové nákupy nebo opakované nákupy je obdobím 1 měsíc (SaaS, podpora Marketplace). Toto neplatí pro využití Azure.
UnitOfMeasure | Všechno | Měrná jednotka pro fakturaci služby. Například služby výpočetních prostředků se účtují po hodinách
UnitPrice | EA, PAYG | Cena za jednotku pro poplatek.

_<sup>**1**</sup> Pole použitá k vytvoření jedinečného ID pro jeden záznam nákladů_.

Všimněte si, že některá pole se mohou lišit v použití velkých a malých písmen a mezer mezi typy účtů.
Starší verze souborů s využitím u průběžných plateb mají samostatné oddíly pro výpis a denní využití.

### <a name="list-of-terms-from-older-apis"></a>Seznam termínů ze starších rozhraní API
Následující tabulka mapuje termíny používané ve starších rozhraních API na nové termíny. Popisy najdete v tabulce uvedené výše.

Starý termín | Nový termín
--- | ---
ConsumedQuantity | Množství
IncludedQuantity | Nevztahuje se
InstanceId | ResourceId
Sazba | EffectivePrice
Jednotka | UnitOfMeasure
UsageDate | Datum
UsageEnd | Datum
UsageStart | Datum


## <a name="ensure-charges-are-correct"></a>Ujistěte se, že jsou poplatky správné.

Pokud chcete získat další podrobné informace o využití a poplatcích, přečtěte si článek vysvětlující fakturu [s průběžnými platbami](review-individual-bill.md) nebo fakturu [pro smlouvu se zákazníkem Microsoftu](review-customer-agreement-bill.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Zobrazení a stažení faktury za Microsoft Azure](download-azure-invoice.md)
- [Zobrazení a stažení využití a poplatků za Microsoft Azure](download-azure-daily-usage.md)
