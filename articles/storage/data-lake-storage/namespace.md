---
title: Azure Data Lake Storage Gen2 Preview hierarchické Namespace
description: Popisuje koncept hierarchického oboru názvů pro Azure Data Lake Storage Gen2 Preview
services: storage
author: jamesbak
manager: twooley
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: f7c3820624a4ef27e2ece4d902f2c033b6a6f48f
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061216"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure hierarchického oboru názvů Data Lake Storage Gen2 Preview

Klíče mechanismus, který umožňuje Azure Data Lake Storage Gen2 Preview zajistit výkon systému souborů v objektu úložiště škálování a ceny, je přidání **hierarchického oboru názvů**. To umožňuje kolekce objektů nebo souborů v rámci účet, který se uspořádají do hierarchie adresářů a vnořené podadresáře stejným způsobem, že je organizovaná systém souborů ve vašem počítači. S hierarchického oboru názvů povoleno Gen2 úložiště Data Lake poskytuje škálovatelnost a nákladová efektivnost objekt úložiště s sémantiku systému souborů, které jsou pro moduly analýzy a rozhraní.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Výhody hierarchického oboru názvů

> [!NOTE]
> Během verzi public preview služby Azure Data Lake Storage Gen2 některé níže uvedené funkce se může lišit v jejich dostupnost. Jako nové funkce a oblastí, jsou vydané v programu preview, tyto informace se budou oznamovat prostřednictvím našich vyhrazené Yammer skupiny.  

Systémy souborů, které implementují hierarchického oboru názvů přes data objektu blob přidruženo následující výhody:

- **Atomic Directory manipulaci:** objekt úložiště Přibližná hierarchie adresářů přijetím konvence vnoření lomítka (/) v názvu objektu k označení segmenty cesty. Při této konvence funguje uspokojivě pro uspořádání objektů, poskytuje konvence bez podpory pro akce jako přesunutí, přejmenování nebo odstranění adresáře. Bez skutečné adresářů musí aplikace potenciálně zpracovat miliony jednotlivých objektů BLOB k dosažení úrovni adresář úlohy. Naopak hierarchického oboru názvů zpracuje tyto úlohy aktualizací jednu položku (nadřazený adresář). 

    Výrazné optimalizace je obzvláště důležité pro mnoho rozhraní analýzy velkých objemů dat. Nástroje, například Hive, Spark, atd. často zapisovat výstup do dočasného umístění a přejmenujte umístění při ukončení úlohy. Bez hierarchického oboru názvů může trvat přejmenujte často déle, než analytics zpracovat sám sebe. Nižší latenci úlohy se rovná snížit náklady na vlastnictví (TCO) pro zatížení analytics.

- **Známé styl rozhraní:** systémy souborů jsou správně pochopeny vývojáři a uživatelé agentem. Není potřeba další nové zlepší úložiště, když přesouváte do cloudu, jak rozhraní systému souboru vystavené Gen2 úložiště Data Lake je stejné zlepší používají počítače, malé i velké.

Jedním z důvodů, že objekt úložiště nebyly v minulosti podporovaná hierarchické obory názvů je, že hierarchické obory názvů omezené škálování. Ale Gen2 úložiště Data Lake hierarchického oboru názvů se škáluje lineárně a sníží kapacitu dat nebo výkonu.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Když chcete-li povolit hierarchického oboru názvů

Zapnutí hierarchického oboru názvů se doporučuje pro úlohy úložiště, které jsou určené pro systémy souborů, které pracují s adresáře. To zahrnuje všechny úlohy, které jsou především pro zpracování analýzy. Datové sady, které vyžadují vysokou míru organizace bude mít prospěch, povolením hierarchického oboru názvů.

Důvody pro povolení hierarchického oboru názvů jsou určeny analýzu celkových nákladů na vlastnictví. Obecně řečeno vylepšení zatížení latence kvůli zrychlení úložiště bude vyžadovat výpočetní prostředky pro méně času. Z důvodu atomic directory manipulaci, povolená ve hierarchického oboru názvů, je možné zvýšit latence pro řadu úloh. V mnoha úloh výpočetních prostředků představuje > 85 % celkových nákladů a proto i mírné snížení zatížení latence rovná významné množství úspory celkových nákladů na vlastnictví. I v případech, kde povolení hierarchického oboru názvů zvyšuje náklady na úložiště je stále snížena celkové náklady na vlastnictví z důvodu výpočetní snížené náklady.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Pokud chcete zakázat hierarchického oboru názvů

Některé úlohy objekt úložiště nemusí získáte všechny výhody povolením hierarchického oboru názvů. Mezi tyto úlohy patří například zálohování, úložiště bitové kopie a jiné aplikace organizace objekt uložení samostatně z samotných objektech (*například*, v samostatné databáze).

> [!NOTE]
> Ve verzi preview Pokud povolíte hierarchického oboru názvů, je žádné interoperabilita dat nebo operace mezi serverem objektů Blob a rozhraní REST API Data Lake Storage Gen2. Tato funkce bude přidána verzi Preview.

## <a name="next-steps"></a>Další postup

- [Vytvoření účtu úložiště](./quickstart-create-account.md)