---
title: Ceny za nabídky virtuálních počítačů | Azure Marketplace
description: Vysvětluje tři metody určení cen nabídek virtuálních počítačů.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: e398b43e679fb6420c2256e77d34359ae537ac1c
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868745"
---
<a name="pricing-for-virtual-machine-offers"></a>Ceny nabídek virtuálních počítačů
==================================

K dispozici jsou tři způsoby, jak zadat ceny pro virtuální počítače: vlastní ceny za základní ceny, ceny za jádro a ceny v tabulce.


<a name="customized-core-pricing"></a>Vlastní ceny za Core
-----------------------

Ceny jsou specifické pro každou oblast a kombinaci jader. Každá oblast v seznamu prodejů musí být zadána v části **virtualMachinePricing**/**regionPrices** definice.  Pro každou [oblast](#regions) ve vaší žádosti použijte správné kódy měn.  Následující příklad znázorňuje tyto požadavky:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Ceny za jádro
----------------

V tomto případě vydavatelé určí jednu cenu v USD pro svou skladovou jednotku a všechny ostatní ceny se vygenerují automaticky. Cena za jádro je uvedena v **jednom** parametru v žádosti.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Ceny tabulky
-------------------

Vydavatel může také odeslat tabulku s cenami do dočasného umístění úložiště a pak zahrnout identifikátor URI do žádosti jako jiné artefakty souborů. Tabulka se pak nahraje, převede na vyhodnocení zadaného časového plánu a nakonec tuto nabídku aktualizuje o ceny. Následné žádosti o získání nabídky vrátí identifikátor URI tabulky a vyhodnocené ceny pro oblast.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="new-core-sizes-added-on-722019"></a>Nové základní velikosti přidané v 7/2/2019
---------------------------

Vydavatelé virtuálních počítačů byli informováni 2. července 2019 o přidání nových cen pro nové velikosti virtuálních počítačů Azure (na základě počtu jader).  Nové ceny jsou pro základní velikosti 10, 44, 48, 60, 120, 208 a 416.  Pro existující virtuální počítač se automaticky vypočítaly nové ceny pro tyto velikosti jader na základě aktuálních cen.  Vydavatelé mají od 1. srpna 2019 kontrolu nad dalšími cenami a provádět požadované změny.  Po tomto datu se projeví automaticky vypočtené ceny pro tyto nové základní velikosti, pokud už je Vydavatel znovu Nepublikovaná.


<a name="regions"></a>Regions
-------

V následující tabulce jsou uvedeny různé oblasti, které můžete zadat pro vlastní ceny za základní jádro a jejich odpovídající kódy měn.

| **Oblast** | **Název**             | **Kód měny** |
|------------|----------------------|-------------------|
| DZ         | Alžírsko              | DZD               |
| AR         | Argentina            | ARS               |
| AU         | Austrálie            | AUD               |
| AT         | Rakousko              | EUR               |
| BH         | Bahrajn              | BHD               |
| BY         | Bělorusko              | RUB               |
| BE         | Belgie              | EUR               |
| BR         | Brazílie               | USD               |
| BG         | Bulharsko             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Kolumbie             | COP               |
| CR         | Kostarika           | CRC               |
| HR         | Chorvatsko              | HRK               |
| CY         | Kypr               | EUR               |
| CZ         | Česká republika       | CZK               |
| DK         | Dánsko              | DKK               |
| DO         | Dominikánská republika   | USD               |
| EC         | Ekvádor              | USD               |
| EG         | Egypt                | EGP               |
| SV         | Salvador          | USD               |
| EE         | Estonsko              | EUR               |
| FI         | Finsko              | EUR               |
| FR         | Francie               | EUR               |
| DE         | Německo              | EUR               |
| GR         | Řecko               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hongkong – zvláštní správní oblast        | HKD               |
| HU         | Maďarsko              | HUF               |
| IS         | Island              | PŘIDĚLENÍ               |
| IN         | Indie                | INR               |
| id         | Indonésie            | IDR               |
| IE         | Irsko              | EUR               |
| IL         | Izrael               | ILS               |
| IT         | Itálie                | EUR               |
| JP         | Japonsko                | JPY               |
| JO         | Jordánsko               | JOD               |
| KZ         | Kazachstán           | KZT               |
| KE         | Keňa                | KES               |
| KR         | Jižní Korea                | KRW               |
| KW         | Kuvajt               | KWD               |
| LV         | Lotyšsko               | EUR               |
| LI         | Lichtenštejnsko        | CHF               |
| LT         | Litva            | EUR               |
| LU         | Lucembursko           | EUR               |
| MK         | Severní Makedonie      | MKD               |
| MY         | Malajsie             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Mexiko               | MXN               |
| PAMATOVAT         | Černá Hora           | EUR               |
| MA         | Maroko              | MAD –               |
| NL         | Nizozemsko          | EUR               |
| NZ         | Nový Zéland          | NZD               |
| NG         | Nigérie              | NGN               |
| NO         | Norsko               | NOK               |
| OM         | Omán                 | OMR               |
| PK         | Pákistán             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | PSANÉ               |
| PH         | Filipíny          | PHP               |
| PL         | Polsko               | PLN               |
| PT         | Portugalsko             | EUR               |
| PR         | Portoriko          | USD               |
| QA         | Katar                | QAR               |
| RO         | Rumunsko              | RON               |
| RU         | Rusko               | RUB               |
| SA         | Saúdská Arábie         | SAR               |
| RS         | Srbsko               | RSD               |
| SG         | Singapur            | SGD               |
| SK         | Slovensko             | EUR               |
| SI         | Slovinsko             | EUR               |
| ZA         | Jižní Afrika         | ZAR               |
| ES         | Španělsko                | EUR               |
| LK         | Srí Lanka            | USD               |
| SE         | Švédsko               | SEK               |
| CH         | Švýcarsko          | CHF               |
| TW         | Tchaj-wan               | TWD               |
| TH         | Thajsko             | THB               |
| TT         | Trinidad a Tobago  | TTD               |
| TN         | Tunisko              | TND               |
| TR         | Turecko               | TRY               |
| UA         | Ukrajina              | UAH               |
| AE         | Spojené arabské emiráty | EUR               |
| GB         | Spojené království       | GBP               |
| USA         | Spojené státy        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
