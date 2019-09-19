---
title: Vysvětlení výrazů v ceníku pro smlouvu se zákazníkem Microsoftu – Azure
description: Zjistěte, jak číst informace o využití a fakturu za smlouvu se zákazníkem Microsoftu a jak je chápat.
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
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490651"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Výrazy v ceníku pro smlouvu se zákazníkem Microsoftu

Tento článek se týká fakturačního účtu Azure za smlouvu se zákazníkem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

Pokud jste vlastník, přispěvatel, čtenář nebo správce faktur fakturačního profilu, můžete si stáhnout ceník pro vaši organizaci z webu Azure Portal. Viz téma [Zobrazení a stažení cen pro vaši organizaci](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Výrazy uvedené v ceníku a jejich popis

Následující část popisuje důležité výrazy uvedené v ceníku pro smlouvu se zákazníkem Microsoftu.

| **Název pole**   | **Popis**   |
| --- | --- |
| billingAccountId  | Jedinečný identifikátor fakturačního účtu   |
| billingAccountName  | Název fakturačního účtu  |
| billingProfileId  | Jedinečný identifikátor fakturačního profilu   |
| billingProfileName  | Název fakturačního profilu nastaveného jako příjemce faktur. Ceny uvedené v ceníku jsou přidružené k tomuto fakturačnímu profilu. |
| productOrderName  | Název zakoupeného plánu produktu |
| serviceFamily  | Typ služby Azure, např.: výpočetní, analytická nebo bezpečnostní |
| Produkt  | Název produktu, za který se účtují poplatky, např.: SQL Database úrovně Basic nebo SQL Database úrovně Standard  |
| productId  | Jedinečný identifikátor produktu, jehož měřič se spotřebovává |
| unitOfMeasure  | Určuje měrné jednotky pro fakturaci služby. Například výpočetní služby se účtují po hodinách. |
| meterId  | Jedinečný identifikátor měřiče |
| meterName  | Název měřiče. Měřič představuje nasaditelný prostředek služby Azure. |
| meterCategory  | Název klasifikační kategorie měřiče. Například _Cloudové služby_, _Sítě_ atd. |
| meterType  |  Název typu měřiče |
| meterSubCategory  | Název klasifikační podkategorie měřiče  |
| meterRegion  | Název oblasti, ve které je daný měřič služby dostupný. Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra.    |
| tierId  | Určuje cenovou úroveň (pokud je k dispozici). Pomocí kombinace tohoto měřiče s měřičem tierMinimumUnits se nastavují odstupňované ceny v případě, že se ceny liší podle počtu spotřebovaných jednotek.    |
| tierMinimumUnits  | Definuje dolní mez rozsahu úrovně pro který se definují ceny. Pokud je například rozsah od 0 do 100, hodnota tierMinimumUnits je 0.  |
| effectiveStartDate  | Počáteční datum, od kterého cena začíná platit. |
| effectiveEndDate  | Koncové datum platné ceny. |
| unitPrice  | Cena za jednotku v době fakturace (nikoli platná smíšená cena) pro konkrétní měřič a název objednávky produktu.  Poznámka: V případě služeb, které mají pro různé úrovně různé ceny, se jednotková cena liší od platné ceny ve stažených podrobnostech o využití.  V případě služeb s různými cenami pro různé úrovně platná cena představuje smíšenou sazbu za všechny úrovně, a nepředstavuje jednotkovou cenu pro konkrétní úroveň. Smíšená neboli platná cena je čistá cena za spotřebované množství napříč různými úrovněmi (kde pro každou úroveň platí jiná jednotková cena). |
| basePrice  | Tržní cena v době přihlášení zákazníka nebo v době spuštění měřiče služby, pokud k němu dojde až po přihlášení   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Zobrazení a stažení cen pro vaši organizaci](billing-ea-pricing.md)
