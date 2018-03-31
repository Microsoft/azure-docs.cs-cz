---
title: Výstupní datový proud analýza Data Lake Store | Microsoft Docs
description: Konfigurace ověřování a autorizace Azure Data Lake Store v úloze Stream Analytics
keywords: ''
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: ea5baafa-0054-4c70-973a-6a3a8c6eaffc
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 802193b9f15cbc35bec32191ccc63df3329b96f9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="stream-analytics-data-lake-store-output"></a>Výstupní datový proud analýza Data Lake Store
Úlohy Stream Analytics podporovat několik metod pro výstup, jeden se [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store je celopodnikové, flexibilně škálovatelné úložiště pro analytické úlohy s velkými objemy dat. Data Lake Store umožňuje ukládání dat z jakékoli velikosti, typu a rychlosti příjmu pro provozní a zjišťovací analýzy.

## <a name="authorize-a-data-lake-store-account"></a>Autorizace účtu Data Lake Store
1. Pokud Data Lake Store je vybraná jako výstupu na portálu Azure, zobrazí se výzva k autorizaci použití existující Data Lake Store nebo abychom požádali o přístup k Data Lake Store.
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. Pokud již máte přístup do Data Lake Store, klikněte na tlačítko Autorizovat a po krátkou dobu a stránky objeví se označující "Přesměrováním na autorizace". Stránce se automaticky zavře a zobrazí se stránka, ke které by umožňují konfigurovat výstupní Data Lake Store.

Pokud nemáte registraci pro Data Lake Store, můžete pomocí následujícího odkazu "Přihlásit" zahájit požadavku nebo postupujte podle [získají pokyny k Začínáme](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Konfigurovat vlastnosti výstupní Data Lake Store
Jakmile máte účet Data Lake Store, ověření, můžete konfigurovat vlastnosti pro výstup do Data Lake Store. V následující tabulce je seznam názvů vlastností a jejich popis konfigurace výstup do Data Lake Store.

<table>
<tbody>
<tr>
<td><B>NÁZEV VLASTNOSTI</B></td>
<td><B>POPIS</B></td>
</tr>
<tr>
<td>Alias pro výstup</td>
<td>Toto je popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto Data Lake Store.</td>
</tr>
<tr>
<td>Účet data Lake Store</td>
<td>Název účtu úložiště, kde jsou odesílání výstupu. Zobrazí seznam účtů Data Lake Store, které má přihlášený uživatel přístup k.</td>
</tr>
<tr>
<td>Předpony vzorek cesty [<I>volitelné</I>]</td>
<td>Cesta k souboru používaná k zápisu souborů v rámci zadaného účtu Data Lake Store. <BR>{date}, {time}<BR>Příklad 1: složku1/logs / {date} / {time}<BR>Příklad 2: složku1/logs / {date}</td>
</tr>
<tr>
<td>Datum formátu [<I>volitelné</I>]</td>
<td>Pokud se v cestě předponu používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány soubory. Příklad: Rrrr/MM/DD</td>
</tr>
<tr>
<td>Formát času [<I>volitelné</I>]</td>
<td>Pokud token čas se používá v cestě předponu, zadejte formát času, ve kterém jsou uspořádány soubory. Aktuálně jedinou podporovanou hodnotou je HH.</td>
</tr>
<tr>
<td>Formát serializace události</td>
<td>Formát serializace pro výstupní data. Jsou podporovány JSON, CSV a Avro.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Pokud formátu CSV nebo formátu JSON, kódování musí být zadán. V tuto chvíli je jediným podporovaným formátem kódování UTF-8.</td>
</tr>
<tr>
<td>Oddělovač</td>
<td>Platí jenom pro serializaci sdílených svazků clusteru. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat sdíleného svazku clusteru. Podporované hodnoty jsou čárkami, středník, místo, karta a svislá čára.</td>
</tr>
<tr>
<td>Formát</td>
<td>Platí jenom pro serializaci JSON. Řádcích: Určuje, že výstup se bude formátovat tak, že každý objekt JSON oddělených nový řádek. Pole určuje, že výstup naformátovaný jako pole objektů JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Obnovit autorizační Data Lake Store
V současné době není omezení kde ověřovací token, je potřeba ručně aktualizovat každých 90 dní pro všechny úlohy s výstupem Data Lake Store. Musíte se také znovu provést ověření účtu Data Lake Store, pokud jste změnili heslo, protože úlohu vytvoření nebo poslední ověření. Příznakem tohoto problému je žádný výstup úlohy a chybu v protokolech operaci označující potřebu opětovná autorizace.

Chcete-li vyřešit tento problém, přejděte na výstup do Data Lake Store a zastavit spuštěná úloha. Klikněte na odkaz "Obnovit autorizace" a po krátkou dobu a stránky objeví se označující "Přesměrováním autorizace …". Stránka se automaticky zavře a v případě úspěšného označí "Autorizace byl úspěšně obnoven". Je pak potřeba v dolní části stránky klikněte na tlačítko "Uložit" a restartováním úlohu od poslední zastavena ztráty dat pokračovat.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

