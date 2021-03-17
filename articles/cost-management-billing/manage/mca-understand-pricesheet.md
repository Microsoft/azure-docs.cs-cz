---
title: Výrazy v ceníku pro smlouvu se zákazníkem Microsoftu – Azure
description: Zjistěte, jak číst informace o využití a fakturu za smlouvu se zákazníkem Microsoftu a jak je chápat.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 6205867cbcb65a3b5720fefd1356b36bcd8c6838
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682718"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Výrazy v ceníku pro smlouvu se zákazníkem Microsoftu

Tento článek se týká fakturačního účtu Azure za smlouvu se zákazníkem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

Pokud jste vlastník, přispěvatel, čtenář nebo správce faktur fakturačního profilu, můžete si stáhnout ceník pro vaši organizaci z webu Azure Portal. Viz téma [Zobrazení a stažení cen pro vaši organizaci](ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Výrazy uvedené v ceníku a jejich popis

Následující část popisuje důležité výrazy uvedené v ceníku pro smlouvu se zákazníkem Microsoftu.

| **Název pole**   | **Popis**   |
| --- | --- |
| basePrice  | Tržní cena v době přihlášení zákazníka nebo v době spuštění měřiče služby, pokud k němu dojde až po přihlášení   |
| billingAccountId  | Jedinečný identifikátor fakturačního účtu   |
| billingAccountName  | Název fakturačního účtu.  |
| billingCurrency | Měna, ve které jsou uvedené poplatky |
| billingProfileId  | Jedinečný identifikátor fakturačního profilu   |
| billingProfileName  | Název fakturačního profilu nastaveného jako příjemce faktur. Ceny uvedené v ceníku jsou přidružené k tomuto fakturačnímu profilu. |
| currency | Měna, ve které jsou uvedené všechny ceny |
| discount | Sleva z ceny nabízená pro úroveň Graduation, úroveň Free, Zahrnuté množství nebo vyjednané slevy, pokud existují. Je vyjádřená v procentech. |
| effectiveEndDate  | Koncové datum platné ceny. |
| effectiveStartDate  | Počáteční datum, od kterého cena začíná platit. |
| includedQuantity | Objem konkrétní služby, na který má zákazník nárok bez zvýšení poplatků |
| marketPrice | Aktuální převažující cena na trhu pro konkrétní službu |
| meterId  | Jedinečný identifikátor měřiče |
| meterCategory  | Název klasifikační kategorie měřiče. Například _Cloudové služby_, _Sítě_ atd. |
| meterName  | Název měřiče. Měřič představuje nasaditelný prostředek služby Azure. |
| meterSubCategory  | Název klasifikační podkategorie měřiče  |
| meterType  |  Název typu měřiče |
| meterRegion  | Název oblasti, ve které je daný měřič služby dostupný. Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra.    |
| Produkt  | Název produktu, za který se účtují poplatky, např.: SQL Database úrovně Basic nebo SQL Database úrovně Standard  |
| productId  | Jedinečný identifikátor produktu, jehož měřič se spotřebovává |
| productOrderName  | Název zakoupeného plánu produktu |
| serviceFamily  | Typ služby Azure, např.: výpočetní, analytická nebo bezpečnostní |
| tierMinimumUnits  | Definuje dolní mez rozsahu úrovně pro který se definují ceny. Pokud je například rozsah od 0 do 100, hodnota tierMinimumUnits je 0.  |
| unitOfMeasure  | Určuje měrné jednotky pro fakturaci služby. Například výpočetní služby se účtují po hodinách. |
| unitPrice  | Cena za jednotku v době fakturace (nikoli platná smíšená cena) pro konkrétní měřič a název objednávky produktu.  Poznámka: V případě služeb, které mají pro různé úrovně různé ceny, se jednotková cena liší od platné ceny ve stažených podrobnostech o využití.  V případě služeb s různými cenami pro různé úrovně platná cena představuje smíšenou sazbu za všechny úrovně, a nepředstavuje jednotkovou cenu pro konkrétní úroveň. Smíšená neboli platná cena je čistá cena za spotřebované množství napříč různými úrovněmi (kde pro každou úroveň platí jiná jednotková cena). |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Zobrazení a stažení cen pro vaši organizaci](ea-pricing.md)
