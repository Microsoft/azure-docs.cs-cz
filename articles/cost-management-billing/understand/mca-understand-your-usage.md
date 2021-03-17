---
title: Výrazy v soubor s informacemi o využití Azure a poplatcích pro smlouvu se zákazníkem Microsoftu
description: Zjistěte, jak číst různé oddíly souboru CSV s informacemi o využití Azure a poplatcích pro váš fakturační profil a jak je chápat.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 826217091b3297da0c068336ff67a0a58841716d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690455"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Výrazy v souboru s informacemi o využití Azure a poplatcích pro smlouvu se zákazníkem Microsoftu

Tento článek se týká fakturačního účtu smlouvy se zákazníky Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

Soubor CSV s informacemi o využití Azure a poplatcích obsahuje poplatky za využívání za den a na úrovni jednotlivých měřičů pro aktuální fakturační období.

Informace o tom, jak získat soubor s informacemi o využití Azure a poplatcích, najdete v tématu [Zobrazení a stažení informací o využití Azure a poplatcích pro smlouvu se zákazníkem Microsoftu](download-azure-daily-usage.md). Soubor je dostupný ve formátu CSV a můžete ho otevřít v tabulkové aplikaci.

Poplatky za využívání jsou celkové **měsíční** poplatky za využívání předplatného. Poplatky za využívání nezohledňují kredity ani slevy.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Změny oproti informacím o využití Azure a poplatcích za smlouvu Enterprise

Pokud jste byli zákazníkem se smlouvou Enterprise, zjistíte, že se výrazy v souboru CSV s informacemi o využívání Azure pro fakturační profil liší od výrazů v souboru CSV s informacemi o využívání Azure pro smlouvu Enterprise. V této tabulce najdete výrazy týkající se využívání pro smlouvu Enterprise s odpovídajícími výrazy týkajícími se využívání pro fakturační profil:

| Soubor CSV s informacemi o využití Azure pro smlouvu Enterprise | Soubor CSV s informacemi o využití Azure a poplatcích pro smlouvu se zákazníkem Microsoftu |
| --- | --- |
| Datum | date |
| Month| date |
| Den | date |
| Rok | date |
| Produkt | product |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Značky | tags |
| StoreServiceIdentifier | – |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Podrobnosti o výrazech a jejich popis

Následující výrazy se objevují v souboru s informacemi o využití Azure a poplatcích.

Označení | Popis
--- | ---
invoiceId | Jedinečné ID dokumentu uvedené v souboru PDF faktury
previousInvoiceId | Odkaz na původní fakturu, pokud tato řádková položka představuje refundaci
billingAccountName | Název fakturačního účtu
billingAccountId | Jedinečný identifikátor hlavního fakturačního účtu
billingProfileId | Název fakturačního profilu, kterému se účtují fakturované poplatky
billingProfileName | Jedinečný identifikátor fakturačního profilu, kterému se účtují fakturované poplatky
invoiceSectionId | Jedinečný identifikátor oddílu faktury
invoiceSectionName | Název oddílu faktury
costCenter | Nákladové středisko definované v rámci předplatného pro sledování nákladů (dostupné jen v otevřených fakturačních obdobích)
billingPeriodStartDate | Počáteční datum fakturačního období, pro které se generuje faktura
billingPeriodEndDate | Koncové datum fakturačního období, pro které se generuje faktura
servicePeriodStartDate | Počáteční datum období hodnocení, které má nastavené a uzamčené ceny za využívanou nebo zakoupenou službu
servicePeriodEndDate | Koncové datum období hodnocení, které má nastavené a uzamčené ceny za využívanou nebo zakoupenou službu
date | U poplatků za využívání Azure a Marketplace se jedná o datum uplatnění. U jednorázových nákupů (rezervace, Marketplace) nebo pevných periodických poplatků (nabídky podpory) se jedná o datum nákupu.
serviceFamily | Řada služeb, do které služba patří
productOrderId | Jedinečný identifikátor objednávky produktu
productOrderName | Jedinečný název objednávky produktu
consumedService | Název využívané služby
meterId | Jedinečný identifikátor měřiče
meterName | Název měřiče
meterCategory | Název klasifikační kategorie měřiče. Například *Cloud Services*, *Networking* atd.
meterSubCategory | Název klasifikační podkategorie měřiče
meterRegion | Název oblasti, ve které je daný měřič služby dostupný. Určuje polohu datového centra u služeb, jejichž ceny vycházejí z umístění datového centra
offer | Název zakoupené nabídky
PayGPrice | Maloobchodní cena za prostředek.
PricingModel | Identifikátor, který určuje, jak se měřič účtuje (hodnoty: On Demand, Reservation, Spot)
productId | Jedinečný identifikátor produktu, za který se účtují poplatky
product | Název produktu, za který se účtují poplatky
subscription ID | Jedinečný identifikátor předplatného, kterému se účtují poplatky
subscriptionName | Název předplatného, kterému se účtují poplatky
reservationId | Jedinečný identifikátor zakoupené instance rezervace
reservationName | Název zakoupené instance rezervace
publisherType | Typ vydavatele (hodnoty: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Název vydavatele služeb z Marketplace
resourceGroupId | Jedinečný identifikátor skupiny prostředků přidružené k prostředku
resourceGroupName | Název skupiny prostředků přidružené k prostředku
resourceId | Jedinečný identifikátor instance prostředku
resourceType | Typ instance prostředku
resourceLocation | Určuje umístění datového centra, ve kterém prostředek běží
location | Normalizované umístění prostředku, pokud jsou ve stejných oblastech nakonfigurovaná různá umístění prostředků
quantity | Počet zakoupených nebo spotřebovaných jednotek
unitOfMeasure | Měrná jednotka pro fakturaci služby. Například služby výpočetních prostředků se účtují po hodinách
chargeType | Typ poplatku. Hodnoty: <ul><li>AsCharged-Usage: Poplatky se účtují na základě využívání určité služby Azure. To zahrnuje využívání na virtuálních počítačích, které se neúčtují, protože jsou součástí rezervovaných instancí.</li><li>AsCharged-PurchaseMarketplace: Jednorázové nebo pevné periodické poplatky za nákupy z Marketplace.</li><li>AsCharged-UsageMarketplace: Poplatky za služby z Marketplace, které se účtují na základě počtu jednotek spotřeby.</li></ul>
isAzureCreditEligible | Příznak, který udává, jestli se může poplatek za službu platit z kreditů Azure (hodnoty: True, False)
serviceInfo1 | Metadata konkrétních služeb
serviceInfo2 | Zastaralé pole, které obsahuje volitelná metadata konkrétních služeb
additionalInfo | Další metadata konkrétních služeb
tags | Značky, které přiřadíte k prostředku

### <a name="make-sure-that-charges-are-correct"></a>Kontrola správnosti poplatků

Pokud se chcete ujistit, že jsou v souboru s podrobnými informacemi o využití uvedené správné poplatky, můžete je zkontrolovat. Informace najdete v tématu [Vysvětlení poplatků na faktuře pro váš fakturační profil](review-customer-agreement-bill.md).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Zobrazení a stažení faktury za Microsoft Azure](download-azure-invoice.md)
- [Zobrazení a stažení informací o využití a poplatcích Microsoft Azure](download-azure-daily-usage.md)
