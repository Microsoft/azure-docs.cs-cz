---
title: Vysvětlení podmínek vašeho ceníku zákaznické smlouvy Microsoft – Azure | Dokumentace Microsoftu
description: Zjistěte, jak číst a využití a vyúčtování předplatného Azure
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371355"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>Vysvětlení podmínek vašeho ceníku zákaznické smlouvy Microsoft

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Pokud jste fakturační profil vlastníkem, přispěvatelem, čtečky nebo správce faktur si můžete stáhnout z webu Azure portal ceníku vaší organizace. Zobrazit [zobrazení a stažení cenových organizaci](billing-ea-pricing.md).

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Podrobné podmínky a popisy v ceníku vaší smlouvy zákazníka se společností Microsoft

Následující část popisuje důležité termíny, které jsou zobrazeny v ceníku vaší smlouvy zákazníka se společností Microsoft.

| **Název pole**   | **Popis**   |
| --- | --- |
| billingAccountId  | Jedinečný identifikátor pro fakturační účet.   |
| billingAccountName  | Název fakturačního účtu.  |
| billingProfileId  | Jedinečný identifikátor pro fakturační profil.   |
| billingProfileName  | Název fakturačního profil, který je nastaven na přijímání faktur. Ceny v ceníku jsou spojeny s tímto profilem fakturace. |
| productOrderName  | Název plánu zakoupených produktů. |
| serviceFamily  | Typ služby Azure. Např.: Výpočetní prostředky, analýz, zabezpečení |
| Produkt  | Název produktu, účtují se poplatky za. Např.: Základní SQL DB vs. Standard SQL DB  |
| productId  | Jedinečný identifikátor pro produkt, jehož měření je zpracován. |
| unitOfMeasure  | Určuje jednotky pro účely fakturace pro službu. Například výpočetní služby se účtují po hodinách. |
| meterId  | Jedinečný identifikátor pro měřidla. |
| meterName  | Název měřiče. Reprezentuje nasaditelnému prostředku služby Azure. |
| meterCategory  | Název kategorie klasifikace pro měřidla. Například _cloudových služeb_, _sítě_atd. |
| meterType  |  Název typu měřidla. |
| meterSubCategory  | Název kategorie měřiče dílčí klasifikace.  |
| meterRegion  | Název oblasti, kde je k dispozici měřiče pro službu. Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra.    |
| tierId  | Identifikuje cenové úrovni v případě potřeby. Toto funguje ve spojení s tierMinimumUnits nastavení vrstvené ceny při ceny se liší v závislosti na počet spotřebovaných jednotek.    |
| tierMinimumUnits  | Určuje dolní mez rozsahu úroveň, pro který jsou definovány ceny. Například pokud je rozsah 0 až 100, tierMinimumUnits by být 0.  |
| effectiveStartDate  | Počáteční datum, kdy začne platit cena. |
| effectiveEndDate  | Koncové datum platná cena. |
| UnitPrice  | Cena za jednotku v době fakturace (ne účinné kombinaci cena) jako specifické pro měření a produktu název objednávky.  Poznámka: Je cena ze jednotku není stejný jako v případě služby, které mají rozdílové cen mezi úrovněmi soubory ke stažení platná cena v podrobnosti o použití.  V případě služby s více vrstvé ceny platná cena je kombinaci míra napříč úrovněmi a nezobrazuje konkrétní úroveň Jednotková cena. Kombinaci nebo platné cenu je cena bez daně za spotřebované množství pokrývá více vrstev (kde každá úroveň má konkrétní Jednotková cena). |
| basePrice  | Cena na trhu v době přihlásí zákazníka nebo spustí měřiče služby cenu na trhu v době, pokud je po přihlášení.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- [Zobrazovat a stahovat ceny vaší organizace](billing-ea-pricing.md)
