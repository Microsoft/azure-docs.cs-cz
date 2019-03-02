---
title: Vysvětlení podmínek vašeho využití Azure a poplatky za sdíleného svazku clusteru zákaznické smlouvy Microsoft | Dokumentace Microsoftu
description: Zjistěte, jak si a porozuměli jim části využití Azure a poplatky za sdíleného svazku clusteru pro váš fakturační profil
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 774f6bd4beb2c2b8b36588ee595996860a9c7549
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248911"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Vysvětlení podmínek vašeho využití Azure a poplatky za sdíleného svazku clusteru zákaznické smlouvy Microsoft

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Využití a poplatky za Azure soubor CSV obsahuje poplatky za využívání úrovně denně a měřiče pro aktuální fakturační období.

Azure file využití a poplatků, získáte [zobrazit a stáhnout využití Azure a poplatky za vaší smlouvy zákazníka Microsoftu](billing-download-azure-daily-usage.md).
Je k dispozici ve formátu souboru hodnot oddělených čárkami (CSV), které můžete otevřít v aplikaci tabulky.

Poplatky za využívání jsou celkové **měsíční** poplatky za využívání předplatného. Poplatky za využívání není vezměte v úvahu všechny kredity a slevy.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Změny v Azure pro smlouvu Enterprise využití a poplatků sdíleného svazku clusteru

Pokud jste zákazník EA, můžete si všimnout, která podmínkami fakturační profil využití Azure souboru CSV jsou jiné než podmínky v souboru CSV využití EA Azure. Tady je mapování podmínky smlouvy EA využití do fakturačních podmínek použití profilu:

| EA Azure usage CSV | Využití Azure pro smlouvu Microsoft zákazníka a poplatků sdíleného svazku clusteru |
| --- | --- |
| Datum | date |
| Měsíc| date |
| Den | date |
| Rok | date |
| Produkt | produkt |
| ID měřiče | meterID |
| Kategorie měřiče | meterCategory |
| Podkategorie měřiče | meterSubCategory |
| Oblast měřiče | meterRegion |
| Název měřiče | meterName |
| Využité množství | množství |
| Sazba zdroje | effectivePrice | <!-- this was highlighted -->
| Rozšířené náklady | náklad |
| Umístění prostředku | resourceLocation |
| Využívaná služba | consumedService |
| ID instance | instanceId |
| Informace o službách 1 | serviceInfo1 |
| Informace o službách 2 | serviceInfo2 |
| Další informace | additionalInfo |
| Značky | tags |
| Identifikátor služby úložiště | neuvedeno |
| Název oddělení | invoiceSection | <!-- this was highlighted -->
| Nákladové středisko | costCenter |
| Měrná jednotka | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Podrobné podmínky a popisy v souboru využití a poplatky za Azure

Následující část popisuje důležité termíny, které jsou uvedené v souboru využití a poplatky za Azure.

Označení | Popis
--- | ---
invoiceId | ID dokumentu jedinečné uvedené na faktuře PDF
previousInvoiceId | Odkaz na původní faktury, pokud je tato položka řádku refundaci
billingAccountName | Název fakturačního účtu
billingAccountId | Jedinečný identifikátor pro kořenovou fakturační účet
billingProfileId | Název fakturačního profil, který vám totiž nabíhají poplatky, které se fakturují
billingProfileName | Jedinečný identifikátor pro profil, který vám totiž nabíhají poplatky, které se fakturují fakturace
invoiceSectionId | Jedinečný identifikátor pro části faktury
invoiceSectionName | Název oddílu faktury
costCenter | Nákladové středisko definované v rámci předplatného pro sledování nákladů (k dispozici pouze v open fakturačních období)
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
produkt | Název produktu, účtují se poplatky za
ID předplatného | Jedinečný identifikátor pro předplatné, účtují se poplatky za
subscriptionName | Název předplatného, účtují se poplatky za
reservationId | Jedinečný identifikátor pro zakoupené rezervované instance
reservationName | Název zakoupené rezervované instance
publisherType | Typ aplikace publisher (hodnoty: firstParty thirdPartyReseller, thirdPartyAgency)
publisherName | Vydavatele služby webu Marketplace
resourceGroupId | Jedinečný identifikátor pro skupinu prostředků, které jsou přidružené k prostředku
resourceGroupName | Název skupiny prostředků, které jsou přidružené k prostředku
resourceId | Jedinečný identifikátor instance prostředku
Typ prostředku | Typ instance prostředku
resourceLocation | Polohu datového centra, ve kterém prostředek běží.
location | Normalizované umístění prostředku, pokud je umístění různých prostředků nakonfigurovaných pro stejné oblasti
množství | Počet jednotek zakoupili nebo spotřebě
unitOfMeasure | Jednotka měření pro fakturaci pro službu. Například výpočetní služby se účtují po hodinách.
chargeType | Typ nákladů. Hodnoty: <ul><li>Použití AsCharged: Poplatky na základě využití služby Azure. To zahrnuje využití pro virtuální počítače, které kvůli rezervované instance se neúčtují.</li><li>AsCharged-PurchaseMarketplace: Jednorázové nebo pevnou opakované účtování poplatků za nákupy na Marketplace</li><li>AsCharged-UsageMarketplace: Poplatky za služby webu Marketplace, které se účtují na základě jednotek spotřeby</li></ul>
isAzureCreditEligible | Příznak označující, pokud platí na službu se nárok na používání kreditů Azure se budou hradit (hodnoty: Hodnota TRUE, False)
serviceInfo1 | Metadata konkrétních služeb
serviceInfo2 | Starší verze pole, které zachycuje volitelná metadata konkrétních služeb
additionalInfo | Další metadata konkrétních služeb.
tags | Značky, které přiřadíte k prostředku

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Jak můžu se ujistit, že jsou správné poplatky v souboru využití a poplatky za Azure?

Pokud v souboru podrobné informace o využití, která chcete podrobnosti na poplatek, přečtěte si téma [vysvětlení poplatků za fakturační profil faktury](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- [Zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazovat a stahovat využití Microsoft Azure a poplatky za](billing-download-azure-daily-usage.md)
