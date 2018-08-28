---
title: Připojení ke zdroji dat v kolekcích pracovních prostorů Power BI | Dokumentace Microsoftu
description: Zjistěte, jak se připojit ke zdroji dat v rámci kolekce pracovních prostorů Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: c626601d9eae7732779020b153c624f80605b56a
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051871"
---
# <a name="connect-to-a-data-source"></a>Připojení ke zdroji dat

S **kolekce pracovních prostorů Power BI**, mohou vkládat sestavy do aplikace. Při vložení sestavy Power BI do vaší aplikace, se sestava připojuje na podkladová data podle **import** kopii dat nebo podle **připojení přímo** do zdroje dat pomocí **DirectQuery** .

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Zde jsou rozdíly mezi **importem** a použitím **DirectQuery**.

| Import | DirectQuery |
| --- | --- |
| Tabulky, sloupce *a data* se naimportují, tedy zkopírují do datové sady sestavy. Chcete-li zobrazit změny, ke kterým došlo na podkladová data, musíte aktualizovat nebo importovat, celou aktuální datovou sadu znovu. |Pouze *tabulky a sloupce* se naimportují, tedy zkopírují do datové sady sestavy. Vždy zobrazit nejnovější data. |

S kolekcemi pracovních prostorů Power BI můžete použít DirectQuery s cloudovými zdroji dat, ale není v místním zdrojům dat v tuto chvíli.

> [!NOTE]
> On-Premises Data Gateway není v tuto chvíli nepodporuje s kolekcemi pracovních prostorů Power BI. To znamená, že DirectQuery nelze použít s místním zdrojům dat.

## <a name="supported-data-sources"></a>Podporované zdroje dat

**DirectQuery**
* Databáze SQL Azure
* Azure SQL Data Warehouse

**Import**

Můžete importovat, používá všechny dostupné zdroje dat v Power BI Desktopu. Budete **není** moct aktualizovat tato data v rámci kolekce pracovních prostorů Power BI. Budete muset uložit změny do souboru PBIX do kolekce pracovních prostorů Power BI. To je způsobeno žádné dostupné brány. 

## <a name="benefits-of-using-directquery"></a>Výhody použití DirectQuery

Existují dvě hlavní výhody při použití **DirectQuery**:

* **DirectQuery** umožňuje vytvářet vizualizace z velkých datových sad, kde ji by jinak nebylo možné nejprve importovat všechna data.
* Změny podkladových dat mohou vyžadovat aktualizaci dat, a u některých sestav může tato potřeba pro zobrazení aktuálních dat může vyžadovat objemné přenosy dat, což data opakovaném importování bylo neproveditelné. Naopak **DirectQuery** sestavy používají aktuální data vždy.

## <a name="limitations-of-directquery"></a>Omezení DirectQuery

Existuje několik omezení, která pomocí **DirectQuery**:

* Všechny tabulky musí pocházet z jedné databáze.
* Pokud je dotaz příliš složitý, dojde k chybě. Chcete-li tuto chybu napravit, musíte Refaktorovat dotaz tak, aby byl méně složitý. Pokud dotaz musí být složité, musíte k importu dat namísto použití **DirectQuery**.
* Filtrování relace je omezené na jeden směr, nikoli obou směrech.
* Nelze změnit datový typ sloupce.
* Ve výchozím omezení umísťují na výrazy jazyka DAX povolené v mírách. Zobrazit [DirectQuery a míry](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery a míry.
K zajištění, že dotazy odeslané do podkladového zdroje dat budou mít přijatelný výkon, jsou na míry zavádí omezení. Při použití **Power BI Desktopu**, pokročilé uživatelé mohou toto omezení obejít výběrem **soubor > Možnosti a nastavení > Možnosti**. V **možnosti** dialogovém okně zvolte **DirectQuery**a vyberte možnost **Povolit neomezené míry v režimu DirectQuery**. Pokud je vybraná tato možnost, je použít jakýkoli výraz jazyka DAX, který je platný pro míru. Uživatelé nesmí zapomenout uvědomit; ale, že některé výrazy, které se zpracovávaly dobře, když se importují data může mít za následek pomalé dotazy na back-end zdroj, když v **DirectQuery** režimu. 

## <a name="see-also"></a>Viz také

* [Začínáme s kolekcemi pracovních prostorů Microsoft Power BI](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)

