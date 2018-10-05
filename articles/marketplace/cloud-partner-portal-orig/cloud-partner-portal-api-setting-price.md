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
ms.openlocfilehash: c78a54d5002972339994d9590c0a3e23b5c69bd9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809330"
---
<a name="pricing-for-virtual-machine-offers"></a>Ceny za virtuální počítač nabídky
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
| PODLE         | Bělorusko              | RUB               |
| BÝT         | Belgie              | EUR               |
| BRAZÍLIE         | Brazílie               | USD               |
| BG         | Bulharsko             | ZAČÁTEK               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Kolumbie             | KOPÍROVAT               |
| ZNAK CR         | Kostarika           | CRC               |
| HR         | Chorvatsko              | HRK               |
| CY         | Kypr               | EUR               |
| CZ         | Česká republika       | CZK               |
| DK         | Dánsko              | DKK               |
| PROVEĎTE         | Dominikánská republika   | USD               |
| ES         | Ekvádor              | USD               |
| TŘEBA         | Egypt                | EGP               |
| SV         | Salvador          | USD               |
| EE         | Estonsko              | EUR               |
| FI         | Finsko              | EUR               |
| FR         | Francie               | EUR               |
| DE         | Německo              | EUR               |
| GR         | Řecko               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hongkong – zvláštní správní oblast        | HKD               |
| HU         | Maďarsko              | HUF               |
| JE         | Island              | PŘIDĚLENÍ DISKU               |
| INDIE         | Indie                | INR               |
| ID         | Indonésie            | IDR               |
| APLIKACE INTERNET EXPLORER         | Irsko              | EUR               |
| IL         | Izrael               | ILS               |
| IT         | Itálie                | EUR               |
| JP         | Japonsko                | JPY               |
| JO         | Jordánsko               | JOD               |
| KZ         | Kazachstán           | KZT               |
| KE         | Keňa                | KES               |
| KOREA         | Jižní Korea                | KRW               |
| KW         | Kuvajt               | KWD               |
| LV         | Lotyšsko               | EUR               |
| LI         | Lichtenštejnsko        | CHF               |
| LT         | Litva            | EUR               |
| LOGICKÁ JEDNOTKA         | Lucembursko           | EUR               |
| MK         | Makedonie – bývalá republika Jugoslávie       | MKD               |
| MOJE         | Malajsie             | MYR               |
| MT –         | Malta                | EUR               |
| MX         | Mexiko               | MXN               |
| ME         | Černá Hora           | EUR               |
| MA         | Maroko              | MAD –               |
| NL         | Nizozemsko          | EUR               |
| NZ         | Nový Zéland          | NZD               |
| NG         | Nigérie              | NGN               |
| NE         | Norsko               | NOK               |
| OBJEKTOVÝ MODEL         | Omán                 | OMR               |
| PK         | Pákistán             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | PSANÍ PEREM               |
| PH         | Filipíny          | PHP               |
| PL         | Polsko               | PLN               |
| PT         | Portugalsko             | EUR               |
| ŽÁDOST O PŘIJETÍ ZMĚN         | Portoriko          | USD               |
| KONTROLA KVALITY         | Katar                | QAR               |
| RO         | Rumunsko              | RON               |
| RU         | Rusko               | RUB               |
| SA         | Saúdská Arábie         | SAR               |
| RS         | Srbsko               | RSD               |
| ZABEZPEČENÍ         | Singapur            | SGD               |
| SK         | Slovensko             | EUR               |
| SI         | Slovinsko             | EUR               |
| ZA         | Jihoafrická republika         | ZAR               |
| ES         | Španělsko                | EUR               |
| LK         | Srí Lanka            | USD               |
| SE         | Švédsko               | SEK               |
| CH         | Švýcarsko          | CHF               |
| TRADIČNÍ ČÍNŠTINA         | Tchaj-wan               | TWD               |
| TH         | Thajsko             | THB               |
| TT         | Trinidad a Tobago  | TTD               |
| TN         | Tunisko              | TND               |
| TR         | Turecko               | TRY               |
| UŽIVATELSKÝ AGENT         | Ukrajina              | UAH               |
| AE         | Spojené arabské emiráty | EUR               |
| GB         | Spojené království       | GBP               |
| USA         | Spojené státy        | USD               |
| UY         | Uruguay              | UYU               |
| ODEBRAT         | Venezuela            | USD               |
|  |  |  |
