---
title: Podmínky v zákaznické smlouvy Microsoft Azure využití a poplatků souboru
description: Zjistěte, jak si a porozuměli jim části využití Azure a poplatky za sdíleného svazku clusteru pro váš fakturační profil.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: d11e31366ea5aa15cf7a790eaee800fa2ea6dabe
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490629"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Podmínky v zákaznické smlouvy Microsoft Azure využití a poplatků souboru

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Využití a poplatky za Azure soubor CSV obsahuje poplatky za měřiče úrovni a denní využití pro aktuální fakturační období.

Azure file využití a poplatků, získáte [zobrazit a stáhnout využití Azure a poplatky za vaší smlouvy zákazníka Microsoftu](billing-download-azure-daily-usage.md). Je k dispozici ve formátu souboru hodnot oddělených čárkami (CSV), které můžete otevřít v aplikaci tabulky.

Poplatky za využívání jsou celkové **měsíční** poplatky za využívání předplatného. Poplatky za využívání není vezměte v úvahu všechny kredity a slevy.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Změny z Azure EA využití a poplatků

Pokud jste zákazník EA, můžete si všimnout, že podmínky v Azure fakturační profil využití souboru CSV se liší od podmínek v souboru CSV využití Azure EA. Tady je mapování podmínky smlouvy EA využití do fakturačních podmínek použití profilu:

| Azure EA usage CSV | Využití Azure pro smlouvu Microsoft zákazníka a poplatků sdíleného svazku clusteru |
| --- | --- |
| Datum | date |
| Měsíc| date |
| Den | date |
| Rok | date |
| Product | product |
| ID měřiče | meterID |
| meterCategory | meterCategory |
| meterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| meterName | meterName |
| ConsumedQuantity | množství |
| ResourceRate | effectivePrice |
| ExtendedCost | náklad |
| resourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Tags | značky |
| StoreServiceIdentifier | neuvedeno |
| Název oddělení | invoiceSection |
| Nákladové středisko | Nákladové středisko |
| unitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Podrobné podmínky a popisy

Následující podmínky jsou uvedeny v souboru využití a poplatky za Azure.

Termín | Popis
--- | ---
invoiceId | ID dokumentu jedinečné uvedené na faktuře PDF
previousInvoiceId | Odkaz na původní faktury, pokud je tato položka řádku refundaci
billingAccountName | Název fakturačního účtu
billingAccountId | Jedinečný identifikátor pro kořenovou fakturační účet
billingProfileId | Název fakturačního profil, který vám totiž nabíhají poplatky, které se fakturují
billingProfileName | Jedinečný identifikátor pro profil, který vám totiž nabíhají poplatky, které se fakturují fakturace
invoiceSectionId | Jedinečný identifikátor pro části faktury
invoiceSectionName | Název oddílu faktury
Nákladové středisko | Nákladové středisko definované v rámci předplatného pro sledování nákladů (k dispozici pouze v open fakturačních období)
billingPeriodStartDate | Počáteční datum fakturačního období, pro který je generován faktury
billingPeriodEndDate | Koncové datum fakturačního období, pro který je generován faktury
servicePeriodStartDate | Počáteční datum platnosti hodnocení která je definována a uzamčen, ceny za služby využité nebo co si
servicePeriodEndDate | Datum ukončení hodnocení období, která je definovaný a uzamčen, ceny za služby využité nebo co si
date | Platby za Azure a webu Marketplace na základě využití to je datum, hodnocení. Pro jednorázové nákupy (rezervace, Marketplace) nebo pevné opakované náklady (podpora nabídek) Toto je datum nákupu.
serviceFamily | Skupina služby, která služba patří do
productOrderId | Jedinečný identifikátor pro objednávku produktu
productOrderName | Jedinečný název pro objednávku produktu
consumedService | Název využívaná služba
meterId | Jedinečný identifikátor pro měřidla
meterName | Název měřiče
meterCategory | Název kategorie klasifikace pro měřidla. Například *cloudových služeb*, *sítě*atd.
meterSubCategory | Název dílčí klasifikace kategorie měřiče
meterRegion | Název oblasti, kde je k dispozici měřiče pro službu. Určuje polohu datového centra pro určité služby, které se počítají podle s umístěním datového centra.
nabídka | Název nabídky zakoupili
productId | Jedinečný identifikátor pro produkt, účtují se poplatky za
product | Název produktu, účtují se poplatky za
ID předplatného | Jedinečný identifikátor pro předplatné, účtují se poplatky za
subscriptionName | Název předplatného, účtují se poplatky za
reservationId | Jedinečný identifikátor pro zakoupené rezervované instance
reservationName | Název zakoupené rezervované instance
publisherType | Typ aplikace publisher (hodnoty: firstParty thirdPartyReseller, thirdPartyAgency)
publisherName | Vydavatele služby webu Marketplace
resourceGroupId | Jedinečný identifikátor pro skupinu prostředků, které jsou přidružené k prostředku
resourceGroupName | Název skupiny prostředků, které jsou přidružené k prostředku
resourceId | Jedinečný identifikátor instance prostředku
resourceType | Typ instance prostředku
resourceLocation | Polohu datového centra, ve kterém prostředek běží.
location | Normalizované umístění prostředku, pokud je umístění různých prostředků nakonfigurovaných pro stejné oblasti
množství | Počet jednotek zakoupili nebo spotřebě
unitOfMeasure | Jednotka měření pro fakturaci pro službu. Například výpočetní služby se účtují po hodinách.
chargeType | Typ nákladů. Hodnoty: <ul><li>Použití AsCharged: Poplatky na základě využití služby Azure. To zahrnuje využití pro virtuální počítače, které kvůli rezervované instance se neúčtují.</li><li>AsCharged-PurchaseMarketplace: Jednorázové nebo pevnou opakované účtování poplatků za nákupy na Marketplace</li><li>AsCharged-UsageMarketplace: Poplatky za služby webu Marketplace, které se účtují na základě jednotek spotřeby</li></ul>
isAzureCreditEligible | Příznak označující, pokud platí na službu se nárok na používání kreditů Azure se budou hradit (hodnoty: Hodnota TRUE, False)
serviceInfo1 | Metadata konkrétních služeb
serviceInfo2 | Starší verze pole, které zachycuje volitelná metadata konkrétních služeb
additionalInfo | Další metadata konkrétních služeb.
značky | Značky, které přiřadíte k prostředku

### <a name="make-sure-that-charges-are-correct"></a>Ujistěte se, že jsou správné poplatky

Pokud chcete ověřit, že poplatky v podrobné informace o použití souboru jsou správné, můžete ověřit je. Zobrazit [vysvětlení poplatků za fakturační profil faktury](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- [Zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazovat a stahovat využití Microsoft Azure a poplatky za](billing-download-azure-daily-usage.md)
