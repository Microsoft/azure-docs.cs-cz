---
title: Ceny za nabídky virtuálních strojů | Azure Marketplace
description: Vysvětluje tři metody určení cen nabídek virtuálních strojů.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 487e66b39bc63363497cb3497d32158efd0c6c8a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255885"
---
<a name="pricing-for-virtual-machine-offers"></a>Ceny nabídek virtuálních počítačů
==================================

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Existují tři způsoby, jak určit ceny pro nabídky virtuálních strojů: přizpůsobené základní ceny, ceny za jádro a ceny tabulek.


<a name="customized-core-pricing"></a>Přizpůsobené základní ceny
-----------------------

Ceny jsou specifické pro každou oblast a základní kombinaci. Každá oblast v seznamu prodeje musí být zadána v části **virtualMachinePricing**/**regionPrices** definice.  Použijte správné kódy měn pro každou [oblast](#regions) v požadavku.  Následující příklad ukazuje tyto požadavky:

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


<a name="per-core-pricing"></a>Ceny pro jádro
----------------

V tomto případě vydavatelé zadat jednu cenu v USD pro jejich Skladovou položku a všechny ostatní ceny jsou generovány automaticky. Cena za jádro je uvedena v **jediném** parametru v požadavku.

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


<a name="spreadsheet-pricing"></a>Ceny tabulek
-------------------

Vydavatel může také nahrát svou cenovou tabulku do dočasného umístění úložiště a pak zahrnout identifikátor URI do požadavku stejně jako ostatní artefakty souborů. Tabulka je pak odeslána, přeložena za účelem vyhodnocení zadaného cenového plánu a nakonec aktualizuje nabídku informacemi o cenách. Následné požadavky GET pro nabídku vrátí identifikátor URI tabulky a vyhodnocené ceny pro oblast.

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

<a name="new-core-sizes-added-on-722019"></a>Nové velikosti jader přidané dne 7/2/2019
---------------------------

2. července 2019 byli vydavatelé virtuálních počítače upozorněni na přidání nových cen pro nové velikosti virtuálních automatů Azure (na základě počtu jader).  Nové ceny jsou pro velikosti jádra 10, 44, 48, 60, 120, 208 a 416.  Pro stávající virtuální počítač nabízí nové ceny pro tyto velikosti jader byly automaticky vypočteny na základě aktuálních cen.  Srpen 2019 mají vydavatelé čas do 1.  Po tomto datu, pokud již není vydavatelem znovu publikován, se automaticky vypočtené ceny pro tyto nové velikosti jádra projeví.


<a name="regions"></a>Oblasti
-------

V následující tabulce jsou uvedeny různé oblasti, které můžete zadat pro vlastní základní ceny, a jejich odpovídající kódy měn.

| **Oblasti** | **Název**             | **Kód měny** |
|------------|----------------------|-------------------|
| DZ         | Alžírsko              | Dzd               |
| AR         | Argentina            | ARS               |
| AU         | Austrálie            | AUD               |
| AT         | Rakousko              | EUR               |
| BH         | Bahrajn              | Bhd               |
| BY         | Bělorusko              | RUB               |
| BE         | Belgie              | EUR               |
| BR         | Brazílie               | USD               |
| BG         | Bulharsko             | Bgn               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | Clp               |
| CO         | Kolumbie             | Policajt               |
| CR         | Kostarika           | Crc               |
| HR         | Chorvatsko              | Hrk               |
| CY         | Kypr               | EUR               |
| CZ         | Česká republika       | Kč               |
| DK         | Dánsko              | DKK               |
| DO         | Dominikánská republika   | USD               |
| EC         | Ekvádor              | USD               |
| EG         | Egypt                | Egp               |
| SV         | Salvador          | USD               |
| EE         | Estonsko              | EUR               |
| FI         | Finsko              | EUR               |
| FR         | Francie               | EUR               |
| DE         | Německo              | EUR               |
| GR         | Řecko               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hongkong – zvláštní správní oblast        | HKD               |
| HU         | Maďarsko              | Huf               |
| IS         | Island              | Isk               |
| IN         | Indie                | INR               |
| ID         | Indonésie            | IDR               |
| IE         | Irsko              | EUR               |
| IL         | Izrael               | Ils               |
| IT         | Itálie                | EUR               |
| JP         | Japonsko                | JPY               |
| JO         | Jordánsko               | JOD               |
| KZ         | Kazachstán           | Kzt               |
| KE         | Keňa                | Kes               |
| KR         | Jižní Korea                | KRW               |
| KW         | Kuvajt               | Kwd               |
| LV         | Lotyšsko               | EUR               |
| LI         | Lichtenštejnsko        | CHF               |
| LT         | Litva            | EUR               |
| LU         | Lucembursko           | EUR               |
| MK         | Severní Makedonie      | Mkd               |
| MY         | Malajsie             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Mexiko               | MXN               |
| ME         | Černá Hora           | EUR               |
| MA         | Maroko              | Šílený               |
| NL         | Nizozemsko          | EUR               |
| NZ         | Nový Zéland          | NZD               |
| NG         | Nigérie              | NGN               |
| NO         | Norsko               | NOK               |
| OM         | Omán                 | OMR               |
| PK         | Pákistán             | Pkr               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | Pero               |
| PH         | Filipíny          | PHP               |
| PL         | Polsko               | Pln               |
| PT         | Portugalsko             | EUR               |
| PR         | Portoriko          | USD               |
| QA         | Katar                | Qar               |
| RO         | Rumunsko              | Ron               |
| RU         | Rusko               | RUB               |
| SA         | Saúdská Arábie         | SAR               |
| RS         | Srbsko               | Rsd               |
| SG         | Singapur            | SGD               |
| SK         | Slovensko             | EUR               |
| SI         | Slovinsko             | EUR               |
| ZA         | Jižní Afrika         | ZAR               |
| ES         | Španělsko                | EUR               |
| LK         | Srí Lanka            | USD               |
| SE         | Švédsko               | SEK               |
| CH         | Švýcarsko          | CHF               |
| TW         | Tchaj-wan               | TWD               |
| TH         | Thajsko             | Thb               |
| TT         | Trinidad a Tobago  | Ttd               |
| TN         | Tunisko              | TND               |
| TR         | Turecko               | TRY               |
| UA         | Ukrajina              | Uah               |
| AE         | Spojené arabské emiráty | EUR               |
| GB         | Spojené království       | GBP               |
| USA         | Spojené státy        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
