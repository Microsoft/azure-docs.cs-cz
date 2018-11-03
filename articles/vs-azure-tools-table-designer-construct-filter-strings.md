---
title: Vytváření řetězce filtru pro návrháře tabulky | Dokumentace Microsoftu
description: Vytváření řetězce filtru pro Návrhář tabulky
services: visual-studio-online
author: ghogen
manager: douge
assetId: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: 1e2fd16d25d9552783d12000820cac56803fca63
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969381"
---
# <a name="constructing-filter-strings-for-the-table-designer"></a>Vytváření řetězce filtru pro Návrhář tabulky
## <a name="overview"></a>Přehled
Jak filtrovat data v tabulce Azure, který se zobrazí v sadě Visual Studio **návrháře tabulky**, sestavit řetězec filtru a zadejte do pole filtru. Přesná syntaxe filtru je definována služby WCF Data Services je podobná klauzuli WHERE příkazu SQL, ale je odeslána do služby Table service prostřednictvím požadavku HTTP. **Návrháře tabulky** zpracovává správné kódování, tak k filtrování hodnotu požadované vlastnosti, byste třeba zadat název vlastnosti, operátor porovnání, hodnotu pro kritéria a volitelně, logická hodnota operátoru do pole filtru. Není potřeba zahrnují možnost dotazu $filter, jako kdybyste se generuje adresu URL a dotaz tabulku prostřednictvím [Reference k REST API služby Storage](http://go.microsoft.com/fwlink/p/?LinkId=400447).

Služby WCF Data Services jsou založené na [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Podrobnosti o filtru možností dotazu systému (**$filter**), najdete v článku [specifikace konvence prostředí OData pro identifikátor URI](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Operátory porovnání
Pro všechny typy vlastností jsou podporovány následující logické operátory:

| Logický operátor | Popis | Příklad filtru řetězce |
| --- | --- | --- |
| EQ |rovno |Město eq "Redmond" |
| gt |Větší než |Cena gt 20 |
| ge |Je větší nebo rovno |Cena ge 10 |
| lt |Méně než |Cena lt 20 |
| Le |Menší než nebo rovno |Cena le 100 |
| Ne |Není rovno |Ne City "Londýn" |
| a |A |Cena le 200 a cena gt 3.5 |
| nebo |Nebo |Cena le 3.5 nebo cena gt 200 |
| ne |Not |není isAvailable |

Při vytváření řetězce filtru, jsou důležité následující pravidla:

* Použijte logické operátory k porovnání vlastnosti na hodnotu. Všimněte si, že není možné porovnat vlastnost s dynamickou hodnotou; jedna strana výrazu musí být konstanta.
* Ve všech částech řetězce filtru se rozlišují malá a velká písmena.
* Hodnota konstanty musí být stejného datového typu jako vlastnost, aby filtr vrátil platné výsledky. Další informace o podporovaných typech vlastností najdete v tématu [Vysvětlení datového modelu služby Table Service](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtrování podle vlastností řetězce
Při filtrování na vlastnosti řetězce, uzavřete do jednoduchých uvozovek řetězcová konstanta.

Následující příklad filtry **PartitionKey** a **RowKey** vlastnosti; další neklíčovým vlastnosti může také být přidán do řetězce filtru:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Každý výraz filtru lze uvést v závorkách, i když není potřeba:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Všimněte si, že služba Table service nepodporuje dotazy zástupný znak a nejsou podporovány v Návrháři tabulek buď. Však můžete provést pomocí operátorů porovnání na požadovanou předponu porovnávání předpon. Následující příklad vrátí entity, které LastName vlastnosti, začínající písmenem "A":

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrování na číselné vlastnosti
Chcete-li filtrovat na celé číslo nebo číslo s plovoucí desetinnou čárkou, zadejte číslo bez uvozovek.

V tomto příkladu vrátí všechny entity s vlastností stáří jehož hodnota je větší než 30:

    Age gt 30

V tomto příkladu vrátí všechny entity, s jehož hodnota je menší nebo rovna 100.25 AmountDue vlastností:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrování na logické vlastnosti
Chcete-li filtrovat na logickou hodnotu, zadejte **true** nebo **false** bez uvozovek.

Následující příklad vrátí všechny entity, kde je vlastnost IsActive nastaven na **true**:

    IsActive eq true

Výraz filtru bez logický operátor, který lze také zapsat. V následujícím příkladu služba Table service také vrátí všechny entity se IsActive **true**:

    IsActive

Chcete-li vrátit všechny entity, pokud má hodnotu false IsActive, můžete použít nikoli operátor:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrování podle vlastnosti datum a čas
Chcete-li filtrovat na základě hodnot data a času, zadejte **data a času** – klíčové slovo, za nímž následuje datum a čas – konstanta v jednoduchých uvozovkách. Konstanta data a času musí být ve formátu UTC kombinované, jak je popsáno v [formátování hodnot data a času vlastností](http://go.microsoft.com/fwlink/p/?LinkId=400449).

Následující příklad vrací entity, kde je vlastnost CustomerSince rovno 10. července 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
