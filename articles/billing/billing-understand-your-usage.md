---
title: Podrobné informace o využití a nákladech | Microsoft Docs
description: Přečtěte si, jak číst a porozumět detailnímu využití a poplatkům.
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
ms.openlocfilehash: 1afd78a0525be523f677cf0146c8a120155130ea
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554682"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Pochopení podmínek v souboru využití a poplatků za Azure

Podrobný soubor využití a poplatků obsahuje denní jmenovité využití na základě sjednaných sazeb, nákupů (například rezervací, poplatků za tržišti) a refundací za zadané období.
Poplatky nezahrnují kredity, daně nebo jiné poplatky nebo slevy.
Následující tabulka popisuje, které poplatky jsou zahrnuté pro každý typ účtu.

Typ účtu | Využití Azure | Využití Marketplace | Knize | Náhrad
--- | --- | --- | --- | ---
Smlouva Enterprise (EA) | Ano | Ano | Ano | Ne
Smlouva se zákazníkem Microsoftu (MCA) | Ano | Ano | Ano | Ano
Průběžné platby (PAYG) | Ano | Ano | Ne | Ne

Další informace o objednávkách Marketplace (označovaných také jako externí služby) najdete v tématu [vysvětlení poplatků za externí služby Azure](billing-understand-your-azure-marketplace-charges.md).

Pokyny ke stažení najdete v tématu [Jak získat fakturační a denní data o využití Azure](billing-download-azure-invoice-daily-usage-date.md).
Soubor CSV s využitím a poplatky můžete otevřít v Microsoft Excelu nebo v jiné aplikaci tabulkového prostředí.

## <a name="list-of-terms-and-descriptions"></a>Seznam pojmů a popisů

Následující tabulka popisuje důležité výrazy používané v nejnovější verzi souboru využití Azure a poplatků.
Seznam obsahuje účty s průběžnými platbami (PAYG), smlouva Enterprise (EA) a Microsoft Customer Agreement (MCA).

Termín | Typ účtu | Popis
--- | --- | ---
Název účtu | EA, PAYG | Zobrazovaný název účtu registrace EA nebo fakturačního účtu PAYG
AccountOwnerId | EA, PAYG | Jedinečný identifikátor účtu registrace EA nebo fakturačního účtu PAYG
Další informace | Vše | Metadata specifická pro službu. Například typ obrázku pro virtuální počítač.
BillingAccountId | Vše | Jedinečný identifikátor kořenového fakturačního účtu.
BillingAccountName | Vše | Název fakturačního účtu.
BillingCurrency | Vše | Měna přidružená k fakturačnímu účtu.
BillingPeriod | EA, PAYG | Fakturační období poplatků.
BillingPeriodEndDate | Vše | Koncové datum fakturačního období.
BillingPeriodStartDate | Vše | Počáteční datum fakturačního období.
BillingProfileId | Vše | Jedinečný identifikátor registrace EA, předplatného PAYG, fakturačního profilu MCA nebo konsolidovaného účtu AWS
BillingProfileName | Vše | Název registrace EA, předplatného PAYG, fakturačního profilu MCA nebo konsolidovaného účtu AWS.
ChargeType | Vše | Uvádí, zda poplatek představuje využití (**použití**), nákup (**Nákup**) nebo refundace (refundace).
Využívaná služba | Vše | Název služby, ke které je poplatek přidružen
Nákladové středisko | EA, MCA | Nákladové středisko definované pro předplatné za sledování nákladů (k dispozici pouze v otevřených fakturačních obdobích pro účty MCA).
Náklady | EA, PAYG | Viz CostInBillingCurrency.
CostInBillingCurrency | MCA | Náklady za poplatek ve fakturační měně před kredity nebo daněmi
CostInPricingCurrency | MCA | Náklady za poplatek v cenové měně před kredity nebo daněmi
Currency | EA, PAYG | Viz BillingCurrency.
Date | Vše | Datum využití nebo nákupu za poplatek.
EffectivePrice | Vše | Kombinovaná Jednotková cena za období Smíšené ceny jsou průměrem všech výkyvů za jednotkovou cenu, jako je odstupňovaná vrstva, která snižuje cenu při nárůstu množství v průběhu času.
ExchangeRateDate | MCA | Datum, kdy se směnný kurz navázal.
ExchangeRatePricingToBilling | MCA | Směnný kurz, který slouží k převodu nákladů v cenové měně na fakturační měnu.
Frekvence | Vše | Označuje, zda se očekává opakování poplatku. Poplatky se můžou nacházet jednou (**jednorázová**), opakovat na měsíční nebo roční úrovni (**Periodický**) nebo vycházet z využití (**UsageBased**).
InvoiceId | PAYG, MCA | Jedinečné ID dokumentu uvedené ve formátu PDF faktury
InvoiceSection | MCA | Viz InvoiceSectionName.
InvoiceSectionId | EA, MCA | Jedinečný identifikátor oddílu pro fakturaci EA nebo MCA
InvoiceSectionName | EA, MCA | Název oddílu nebo faktury MCA pro oddělení EA
IsAzureCreditEligible | Vše | Určuje, jestli má poplatek nárok na platby za použití kreditů Azure (hodnoty: True, false).
Location | MCA | Umístění datového centra, kde je prostředek spuštěn.
Kategorie měřiče | Vše | Název kategorie klasifikace pro měřič Například *Cloud Services* a *sítě*.
ID měřiče | Vše | Jedinečný identifikátor pro měřič
Název měřiče | Vše | Název měřiče.
Oblast měřiče | Vše | Název umístění datacentra pro ceny služby na základě umístění. Podívejte se na umístění.
Podkategorie měřiče | Vše | Název kategorie podklasifikace měřiče
OfferId | Vše | Název zakoupené nabídky
PartNumber | EA, PAYG | Identifikátor používaný k získání cen konkrétního měřiče.
PlanName | EA, PAYG | Název plánu Marketplace
PreviousInvoiceId | MCA | Odkaz na původní fakturu, pokud je tato položka řádku refundace.
PricingCurrency | MCA | Měna použitá při hodnocení na základě sjednaných cen
Produkt | Vše | Název produktu
ID produktu | MCA | Jedinečný identifikátor produktu
ProductOrderId | Vše | Jedinečný identifikátor pro produktovou objednávku.
ProductOrderName | Vše | Jedinečný název pro produktovou objednávku.
Název vydavatele | Vše | Publisher pro služby Marketplace.
PublisherType | Vše | Typ vydavatele (hodnoty: **Azure**, **AWS**, **Marketplace**).
Množství | Vše | Počet zakoupených nebo spotřebovaných jednotek.
ReservationId | EA, MCA | Jedinečný identifikátor pro zakoupenou instanci rezervace
Rezervační | EA, MCA | Název zakoupené instance rezervace.
Skupina prostředků | Vše | Název [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) , ve které je prostředek.
Prostředku | Vše | Jedinečný identifikátor prostředku [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) .
Umístění prostředku | Vše | Umístění datového centra, kde je prostředek spuštěn. Podívejte se na umístění.
ResourceName | EA, PAYG | Název prostředku.
Typ prostředku | MCA | Typ instance prostředku
ServiceFamily | MCA | Rodina služeb, do které služba patří.
ServiceInfo1 | Vše | Metadata specifická pro službu.
Informace o službách 2 | Vše | Starší verze pole s volitelnými metadaty specifickými pro službu.
ServicePeriodEndDate | MCA | Koncové datum období hodnocení, které definovalo a uzamkl ceny pro spotřebovaný nebo zakoupenou službu.
ServicePeriodStartDate | MCA | Počáteční datum období hodnocení, které definovalo a uzamkl ceny pro spotřebovaný nebo zakoupenou službu.
SubscriptionId | Vše | Jedinečný identifikátor předplatného Azure.
Název odběru | Vše | Název předplatného Azure.
Tags | Vše | Značky přiřazené k prostředku Nezahrnuje značky skupin prostředků. Dá se použít k seskupení nebo distribuci nákladů na interní vrácení peněz. Další informace najdete v tématu [uspořádání prostředků Azure pomocí značek](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Termín | Vše | Zobrazí termín platnosti nabídky. Příklad: V případě rezervovaných instancí se jako termín zobrazí 12 měsíců. Pro jednorázové nákupy nebo opakované nákupy je termín 1 měsíc (SaaS podpora Marketplace). Neplatí to pro využití Azure.
Měrná jednotka | Vše | Měrná jednotka pro fakturaci služby. Například služba COMPUTE se účtuje za hodinu.
JednotkováCena | EA, PAYG | Cena za jednotku za poplatek.

Všimněte si, že některá pole se mohou lišit v malých a velkých rozestupech mezi typy účtů.
Starší verze souborů s průběžnými platbami podle aktuálního využití mají samostatné oddíly pro výpis a denní využití.

### <a name="list-of-terms-from-older-apis"></a>Seznam podmínek ze starších rozhraní API
Následující tabulka mapuje na nové výrazy výrazy používané ve starších rozhraních API. Tyto popisy najdete v tabulce výše.

Starý výraz | Nový termín
--- | ---
Využité množství | Množství
IncludedQuantity | Není k dispozici
ID instance | Prostředku
Tarif | EffectivePrice
Jednotka | Měrná jednotka
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Zajistěte, aby poplatky byly správné.

Pokud chcete získat další informace o podrobném využití a nákladech, přečtěte [](./billing-understand-your-bill.md) si informace o tom, jak porozumět faktuře s průběžnými platbami nebo [zákaznickou smlouvou Microsoftu](billing-mca-understand-your-bill.md) .

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- [Zobrazení a stažení Microsoft Azure faktury](billing-download-azure-invoice.md)
- [Zobrazení a stažení Microsoft Azure používání a poplatků](billing-download-azure-daily-usage.md)
