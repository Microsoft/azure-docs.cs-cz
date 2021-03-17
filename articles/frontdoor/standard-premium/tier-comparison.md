---
title: Porovnání skladové položky služby Azure front-end Standard/Premium
description: Tento článek poskytuje přehled služeb Azure front-SKU Standard a Premium a mezi nimi rozdíly mezi funkcemi.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 1753f2bb649e73d7a5fe6c1cc32361a418ea7f63
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181801"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Přehled služby Azure front-SKU Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Přední dvířka Azure jsou nabízená pro 3 různé SKU, [přední dveře Azure](../front-door-overview.md), Standard Azure front-dveří (Preview) a frontu Azure na úrovni Premium (Preview). Skladové jednotky služby Azure front-end Standard/Premium spojují možnosti služby Azure front-dveří, Azure CDN Standard od Microsoftu, Azure WAF do jediné zabezpečené platformy CDN cloudu s využitím inteligentní ochrany před internetovými útoky.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [**doplňujících podmínek použití pro Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

* **Standardní SKU front Azure** je:

    * Optimalizované doručování obsahu
    * Nabídka statického i dynamického zrychlení obsahu
    * Globální vyrovnávání zatížení
    * Přesměrování zpracování SSL
    * Správa domén a certifikátů
    * Vylepšená Analýza provozu 
    * Základní možnosti zabezpečení

* **Skladová jednotka front-SKU pro Azure** je založena na možnostech Standard SKU a přidává:

    * Rozsáhlé možnosti zabezpečení napříč WAF
    * Ochrana BOT
    * Podpora privátních odkazů
    * Integrace s Microsoft Threat Intelligence a analýzou zabezpečení. 

![Diagram znázorňující porovnání mezi SKU front-dveří.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Porovnání funkcí

| Funkce |      Standard      |  Premium |
|----------|:-------------:|------:|
| Vlastní domény | Ano | Ano |
| Přesměrování zpracování SSL | Ano | Ano |
| Ukládání do mezipaměti |  Ano  | Ano |
| Komprese | Ano | Ano   |
| Globální vyrovnávání zatížení | Ano  | Ano |
| Směrování vrstvy 7 | Ano | Ano |
| Přepsání adresy URL | Ano | Ano |
| Stroj pravidel | Ano | Ano |
| Soukromý původ (privátní odkaz) | Ne | Ano |
| WAF | Pouze vlastní pravidla | Ano |
| Ochrana bot | Ne | Ano |
| Vylepšené metriky a diagnostika | Ano | Ano |
| Sestava provozu | Ano | Ano |
| Sestava zabezpečení | Ne | Ano | 

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet přední dveře](create-front-door-portal.md) .
