---
title: Připojení ke zdroji dat v kolekcích prostoru Power BI | Microsoft Docs
description: Zjistěte, jak se připojit ke zdroji dat v kolekcích prostoru Power BI.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 5a154c4899db974645bb7ade028d8bd8f267aad7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409852"
---
# <a name="connect-to-a-data-source"></a>Připojení ke zdroji dat

S **kolekce pracovních prostorů Power BI**, můžete vložení sestavy do vlastní aplikace. Při vložení sestavy Power BI do vaší aplikace pro sestavu připojí k základní data podle **import** kopii dat nebo pomocí **připojení přímo** do zdroje dat pomocí **DirectQuery**.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Zde jsou rozdíly mezi **importem** a použitím **DirectQuery**.

| Import | DirectQuery |
| --- | --- |
| Tabulky, sloupce *a data* se naimportují, tedy zkopírují do sestavy datovou sadu. Chcete-li zobrazit změny, ke kterým došlo v základních datech, musíte aktualizovat nebo importovat, celou aktuální datovou sadu znovu. |Pouze *tabulky a sloupce* se naimportují, tedy zkopírují do sestavy datovou sadu. Vždy zobrazíte nejnovější data. |

S Power BI prostoru kolekcí jste můžete použít DirectQuery s cloudovými zdroji dat, ale není místní zdroje dat v tuto chvíli.

> [!NOTE]
> Bránu dat On-Premises nepodporuje kolekce pracovních prostorů Power BI v tuto chvíli. To znamená, že DirectQuery nelze použít s místní datové zdroje.

## <a name="supported-data-sources"></a>Podporované zdroje dat

**DirectQuery**
* Databáze SQL Azure
* Azure SQL Data Warehouse

**Import**

Můžete importovat používá všechny dostupné zdroje dat v rámci Power BI Desktop. Budete **není** moct aktualizovat data v rámci kolekce pracovních prostorů Power BI. Budete muset odešlete změny do souboru soubor PBIX do Power BI prostoru kolekcí. Toto je z důvodu žádná brána k dispozici. 

## <a name="benefits-of-using-directquery"></a>Výhody použití DirectQuery

Existují dvě hlavní výhody při použití **DirectQuery**:

* **DirectQuery** umožňují vytvářet vizualizace přes rozsáhlých datových sad, pokud v opačném případě by bylo nebude proveditelné první importovat všechna data.
* Základní změny dat může vyžadovat aktualizaci dat a pro některé sestavy, potřeba zobrazují aktuální data, může vyžadovat rozsáhlé datové přenosy, vytvoření opakovaném importování dat nebude proveditelné. Naopak **DirectQuery** sestavy vždy používat aktuální data.

## <a name="limitations-of-directquery"></a>Omezení DirectQuery

Existuje několik omezení použití jsou **DirectQuery**:

* Všechny tabulky musí pocházet z jedné databáze.
* Pokud dotaz je příliš složité, dojde k chybě. Chcete-li opravit chyby musí Refaktorovat dotaz tak, aby byl menší komplexní. Pokud dotaz musí být složité, je třeba importovat data místo použití **DirectQuery**.
* Filtrování relace je omezený na jeden směr, nikoli obou směrech.
* Nelze změnit datový typ sloupce.
* Ve výchozím omezení jsou umístěny na výrazy jazyka DAX v míry povoleny. V tématu [DirectQuery a míry](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery a míry.
K zajištění, že máte přijatelný výkon dotazů odesílaných do základní zdroj dat, jsou omezení vynucená pro míry. Při použití **Power BI Desktop**rozšířené uživatelé mohou toto omezení obejít tak, že zvolíte **soubor > Možnosti a nastavení > Možnosti**. V **možnosti** dialogovém okně, vyberte **DirectQuery**a vyberte možnost **Povolit neomezené míry v režimu DirectQuery**. Pokud je vybraná tato možnost, lze použít jakýkoli DAX výraz, který je platný pro míru. Uživatelé musí být vědomi; však, že některé výrazů, které dobře provést při importu může zapříčinit pomalu dotazuje na back-end zdroj při v **DirectQuery** režimu. 

## <a name="see-also"></a>Viz také

* [Začínáme s Microsoft Power BI prostoru kolekce](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)

