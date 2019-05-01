---
title: Ceny za virtuální počítač nabídky | Dokumentace Microsoftu
description: Popisuje tři metody pro určení ceny nabídky virtuálního počítače.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: a029477dfd8046863ebfe34cd839562a0b1f3d87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094450"
---
<a name="pricing-for-virtual-machine-offers"></a>Ceny nabídek virtuálních počítačů
==================================

Existují tři způsoby, jak určit ceny pro virtuální počítač nabídky: přizpůsobené základní ceny, ceny za jádro a ceny tabulky.


<a name="customized-core-pricing"></a>Vlastní základní ceny
-----------------------

Ceny jsou specifické pro každou kombinaci oblasti a jádro. Všechny oblasti v seznamu zákazník musí být zadán v **virtualMachinePricing**/**regionPrices** část definice.  Použijte správnou měny pro každou [oblasti](#regions) ve vaší žádosti.  Následující příklad znázorňuje tyto požadavky:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Ceny za jádro
----------------

V takovém případě vydavatele zadejte jednu cenu v USD pro jejich SKU a všechny ostatní ceny jsou automaticky generovány. Cena za jádro je zadán v **jeden** parametru v žádosti.

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

Vydavatel může také nahrát jejich cenové tabulky do umístění dočasného úložiště a pak zahrnout identifikátoru URI žádosti podobně jako jiné artefakty souboru. V tabulce pak nahrají, přeložit vyhodnotit určitou cenu plánu a nakonec aktualizuje nabídka s informacemi o cenách. Další požadavky GET nabídky bude vracet tabulky identifikátor URI a vyhodnocené ceny platné pro oblast.

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

<a name="regions"></a>Oblasti
-------

Následující tabulka uvádí různé oblasti, můžete zadat vlastní základní ceny a jejich odpovídající kódy měn.

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
| IS         | Island              | PŘIDĚLENÍ DISKU               |
| IN         | Indie                | INR               |
| ID         | Indonésie            | IDR               |
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
| ME         | Černá Hora           | EUR               |
| MA         | Maroko              | MAD               |
| NL         | Nizozemsko          | EUR               |
| NZ         | Nový Zéland          | NZD               |
| NG         | Nigérie              | NGN               |
| NO         | Norsko               | NOK               |
| OM         | Omán                 | OMR               |
| PK         | Pákistán             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | PEN               |
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
