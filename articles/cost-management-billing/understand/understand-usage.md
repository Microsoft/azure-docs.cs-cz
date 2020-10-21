---
title: Vysvětlení podrobných informací o využití a poplatcích
description: Naučte se číst soubor s podrobnými informacemi o využití a poplatcích a rozumět jim. Projděte si seznam termínů používaných v tomto souboru a jejich popisů.
author: bandersmsft
ms.reviewer: micflan
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 559fe43bbd9b8951a97733ae1250046f65f10404
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149354"
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

Označení | Typ účtu | Popis
--- | --- | ---
AccountName | EA, PAYG | Zobrazovaný název účtu registrace EA nebo fakturačního účtu PAYG.
AccountOwnerId<sup>1</sup> | EA, PAYG | Jedinečný identifikátor pro účet registrace EA nebo fakturační účet PAYG.
AdditionalInfo | Vše | Metadata konkrétních služeb. Například typ image u virtuálního počítače.
BillingAccountId<sup>1</sup> | Vše | Jedinečný identifikátor kořenového fakturačního účtu.
BillingAccountName | Vše | Název fakturačního účtu.
BillingCurrency | Vše | Měna přidružená k fakturačnímu účtu.
BillingPeriod | EA, PAYG | Fakturační období poplatku.
BillingPeriodEndDate | Vše | Koncové datum fakturačního období.
BillingPeriodStartDate | Vše | Počáteční datum fakturačního období.
BillingProfileId<sup>1</sup> | Vše | Jedinečný identifikátor registrace EA, předplatného PAYG, fakturačního profilu MCA nebo konsolidovaného účtu AWS.
BillingProfileName | Vše | Název registrace EA, předplatného PAYG, fakturačního profilu MCA nebo konsolidovaného účtu AWS.
ChargeType | Vše | Označuje, jestli poplatek představuje využití (**Usage**), nákup (**Purchase**) nebo refundaci (**Refund**).
ConsumedService | Vše | Název služby, ke které je poplatek přidružen.
CostCenter<sup>1</sup> | EA, MCA | Nákladové středisko definované pro předplatné pro sledování nákladů (k dispozici pouze v otevřených fakturačních obdobích pro účty MCA).
Náklady | EA, PAYG | Viz CostInBillingCurrency.
CostInBillingCurrency | MCA | Náklady na poplatek ve fakturační měně před kredity nebo daněmi.
CostInPricingCurrency | MCA | Náklady na poplatek v měně pro ceny před kredity nebo daněmi.
Měna | EA, PAYG | Viz BillingCurrency.
Date<sup>1</sup> | Vše | Datum využití nebo nákupu poplatku.
EffectivePrice | Vše | Smíšená jednotková cena pro období. Smíšené ceny jsou průměrem všech výkyvů v jednotkové ceně, jako je například odstupňované vrstvení, které snižuje cenu při nárůstu množství v průběhu času.
ExchangeRateDate | MCA | Datum stanovení směnného kurzu.
ExchangeRatePricingToBilling | MCA | Směnný kurz používaný k převodu nákladů v měně pro ceny na fakturační měnu.
Frequency | Vše | Určuje, jestli se očekává opakování poplatku. Poplatky se můžou účtovat jednorázově (**OneTime**), opakovat se na měsíční, nebo roční bázi (**Recurring**), nebo vycházet z využití (**UsageBased**).
InvoiceId | PAYG, MCA | Jedinečné ID dokumentu uvedené v souboru PDF faktury.
InvoiceSection | MCA | Viz InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Jedinečný identifikátor oddílu pro fakturaci EA nebo MCA.
InvoiceSectionName | EA, MCA | Název oddílu pro fakturaci EA nebo MCA.
IsAzureCreditEligible | Vše | Udává, jestli se může poplatek platit z kreditů Azure (hodnoty: True, False).
Umístění | MCA | Umístění datacentra, ve kterém prostředek běží.
MeterCategory | Vše | Název klasifikační kategorie měřiče. Například *Cloud Services* a *sítě*.
MeterId<sup>1</sup> | Vše | Jedinečný identifikátor měřiče.
MeterName | Vše | Název měřiče.
MeterRegion | Vše | Název umístění datacentra pro služby s cenami založenými na umístění. Viz Location.
MeterSubCategory | Vše | Název kategorie dílčí klasifikace měřiče.
OfferId<sup>1</sup> | Vše | Název zakoupené nabídky.
PayGPrice | Vše | Maloobchodní cena za prostředek.
PartNumber<sup>1</sup> | EA, PAYG | Identifikátor používaný k získání cen konkrétního měřiče.
PlanName | EA, PAYG | Název plánu Marketplace.
PreviousInvoiceId | MCA | Odkaz na původní fakturu, pokud tato řádková položka představuje refundaci.
PricingCurrency | MCA | Měna použitá při hodnocení na základě sjednaných cen.
PricingModel | Vše | Identifikátor, který určuje, jak se měřič účtuje. (hodnoty: On Demand, Reservation, Spot)
Produkt | Vše | Název produktu
ProductId<sup>1</sup> | MCA | Jedinečný identifikátor pro produkt.
ProductOrderId | Vše | Jedinečný identifikátor objednávky produktu.
ProductOrderName | Vše | Jedinečný název objednávky produktu.
Název vydavatele | Vše | Název vydavatele služeb z Marketplace.
PublisherType | Vše | Typ vydavatele (hodnoty: **Azure**, **AWS**, **Marketplace**).
Množství | Vše | Počet zakoupených nebo spotřebovaných jednotek.
ReservationId | EA, MCA | Jedinečný identifikátor zakoupené instance rezervace.
ReservationName | EA, MCA | Název zakoupené instance rezervace.
ResourceGroup | Vše | Název [skupiny prostředků](../../azure-resource-manager/management/overview.md), ve které se prostředek nachází. Ne všechny poplatky pocházejí z prostředků nasazených ve skupinách prostředků. Poplatky, které nemají skupinu prostředků, se zobrazí jako null/prázdné, **Ostatní** nebo **Nelze použít**.
ResourceId<sup>1</sup> | Vše | Jedinečný identifikátor prostředku [Azure Resource Manageru](/rest/api/resources/resources).
ResourceLocation | Vše | Umístění datacentra, ve kterém prostředek běží. Viz Location.
ResourceName | EA, PAYG | Název prostředku. Ne všechny poplatky pocházejí z nasazených prostředků. Poplatky, které nemají typ prostředku, se zobrazí jako null/prázdné, **Ostatní** nebo **Nelze použít**.
ResourceType | MCA | Typ instance prostředku. Ne všechny poplatky pocházejí z nasazených prostředků. Poplatky, které nemají typ prostředku, se zobrazí jako null/prázdné, **Ostatní** nebo **Nelze použít**.
ServiceFamily | MCA | Řada služeb, do které služba patří.
ServiceInfo1 | Vše | Metadata konkrétních služeb.
ServiceInfo2 | Vše | Starší pole s volitelnými metadaty konkrétní služby.
ServicePeriodEndDate | MCA | Koncové datum období hodnocení, které má nastavené a uzamčené ceny za využívanou nebo zakoupenou službu.
ServicePeriodStartDate | MCA | Počáteční datum období hodnocení, které má nastavené a uzamčené ceny za využívanou nebo zakoupenou službu.
SubscriptionId<sup>1</sup> | Vše | Jedinečný identifikátor předplatného Azure.
SubscriptionName | Vše | Název předplatného Azure.
Tags<sup>1</sup> | Vše | Značky přiřazené k prostředku. Nezahrnuje značky skupiny prostředků. Dá se použít k seskupení nebo distribuci nákladů pro interní vrácení peněz. Další informace najdete v článku o [uspořádání prostředků Azure pomocí značek](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Označení | Vše | Zobrazí období platnosti nabídky. Příklad: V případě rezervovaných instancí se jako období zobrazuje 12 měsíců. Pro jednorázové nákupy nebo opakované nákupy je obdobím 1 měsíc (SaaS, podpora Marketplace). Toto neplatí pro využití Azure.
UnitOfMeasure | Vše | Měrná jednotka pro fakturaci služby. Například výpočetní služby se účtují po hodinách.
UnitPrice | EA, PAYG | Cena za jednotku pro poplatek.

_<sup>**1**</sup> Pole použitá k vytvoření jedinečného ID pro jeden záznam nákladů_.

Všimněte si, že některá pole se mohou lišit v použití velkých a malých písmen a mezer mezi typy účtů.
Starší verze souborů s využitím u průběžných plateb mají samostatné oddíly pro výpis a denní využití.

### <a name="list-of-terms-from-older-apis"></a>Seznam termínů ze starších rozhraní API
Následující tabulka mapuje termíny používané ve starších rozhraních API na nové termíny. Popisy najdete v tabulce uvedené výše.

Starý termín | Nový termín
--- | ---
ConsumedQuantity | Množství
IncludedQuantity | –
InstanceId | ResourceId
Sazba | EffectivePrice (Platná cena)
Jednotka | UnitOfMeasure
UsageDate | Datum
UsageEnd | Datum
UsageStart | Datum


## <a name="ensure-charges-are-correct"></a>Ujistěte se, že jsou poplatky správné.

Pokud chcete získat další podrobné informace o využití a poplatcích, přečtěte si článek vysvětlující fakturu [s průběžnými platbami](review-individual-bill.md) nebo fakturu [pro smlouvu se zákazníkem Microsoftu](review-customer-agreement-bill.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Zobrazení a stažení faktury za Microsoft Azure](download-azure-invoice.md)
- [Zobrazení a stažení informací o využití a poplatcích Microsoft Azure](download-azure-daily-usage.md)