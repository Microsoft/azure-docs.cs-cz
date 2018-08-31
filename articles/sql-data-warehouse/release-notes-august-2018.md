---
title: Azure SQL Data Warehouse poznámky k verzi ze srpna 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/13/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: c52fcd544e22c274d0e9053da93d2b9dee0df695
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286628"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Srpen 2018
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny v srpna 2018.

## <a name="automatic-intelligent-insights"></a>Automatické Intelligent Insights
Microsoft zavedl [automatické intelligent insights](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) k poskytování automatizace pro datový sklad v oblasti cloudu. Musíte již monitorování datového skladu pro data tabulky nerovnoměrné rozdělení a neoptimální statistiku. Bez dalších poplatků SQL Data Warehouse poskytuje informace o inteligentní přehledy pro všemi instancemi Gen2. Díky integraci s [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), se automaticky zobrazí doporučení osvědčených postupů pro zlepšení výkonu aktivní úlohy. SQL Data Warehouse analyzuje vaše úlohy a zpřístupňují doporučení na základě využití. Tato analýza se stane, denní umožňuje sledovat využití sestav a doporučení pro vylepšení vaší úloze.

Na portálu Azure Advisor můžete zobrazit doporučení: ![Azure Advisor portál doporučení pro Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

Můžete zobrazit podrobnosti jednotlivých kategorií můžete zobrazit doporučení pro konkrétní výstrahu: ![Azure Advisor portál podrobnosti o doporučení pro Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>Opravy chyb

| Titul | Popis |
|:---|:---|
| **Při překročení maximálního limitu počtu rozdělení potenciální chyby dotazu** |Při překročení limitu rozdělení souboru horní mez 1 milion stroj SQL pro výpis způsobila neošetřenou výjimku a všechny dotazy se nezdařilo. Tato oprava adresou problém tak, že správně zpracování výjimek a vrátit chybu, aniž by to způsobilo dotazů. |
| **Větší ExternalMoveReadersPerNode výchozí hodnotu pro zlepšení výkonu zatížení** |Tento problém byl způsobený ExternalMoveReadersPerNode nastavení vlastnosti nejsou synchronizovány s využitím service fabric nastavení. Tento regresní způsobit snížení výkonu zatížení Gen2. Oprava přináší výkon načítání Gen2 za parametry optimalizované návrhu.|


## <a name="next-steps"></a>Další postup
Teď, když už víte o něco o SQL Data Warehouse, zjistěte, jak rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]. Pokud s Azure začínáte, může vám být užitečný [Glosář Azure][Azure glossary], kde najdete potřebnou terminologii. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

* [Úspěšné zákaznické implementace]
* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy zákaznického poradního týmu]
* [Fórum Stack Overflow]
* [Twitter]


[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Úspěšné zákaznické implementace]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md