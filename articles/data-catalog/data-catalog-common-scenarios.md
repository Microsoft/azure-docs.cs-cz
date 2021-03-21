---
title: Běžné scénáře služby Azure Data Catalog
description: Přehled běžných scénářů pro Azure Data Catalog, včetně registrace a zjišťování zdrojů dat s vysokými hodnotami, povolení samoobslužného business intelligence a zachytávání stávajících znalostí o zdrojích a procesech dat.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 4f76a9696d613a4a974c9ae7c7b2578e56f2fcfa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674610"
---
# <a name="azure-data-catalog-common-scenarios"></a>Běžné scénáře služby Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Tento článek představuje běžné scénáře, kdy Azure Data Catalog může vaší organizaci pomáhat získat větší hodnotu z existujících zdrojů dat.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scénář 1: registrace centrálních zdrojů dat
Organizace často mají mnoho zdrojů dat s vysokými hodnotami. Tyto zdroje dat zahrnují systémy, OLTP (online Transaction Processing), datové sklady a databáze business intelligence/Analytics. Počet systémů a překryv mezi nimi obvykle roste v čase podle toho, jak se podniková potřeby vyvíjí, a samotný podnik se vyvíjí, například fúze a akvizice.

U členů organizace může být obtížné zjistit, kde najít data v těchto zdrojích dat. Otázky, jako jsou následující, jsou příliš běžné:

* Ze tří systémů HR používaných v rámci společnosti, které mám použít k vytvoření tohoto typu sestavy?
* Kde mám přejít k získání certifikovaných prodejních čísel pro fiskální rok, který jste právě ukončili?
* Kdo má požádat o přístup k datovému skladu, nebo jaký je proces, který mám použít k získání přístupu?
* Nevím, jestli jsou tato čísla pravá. Kdo můžu požádat o přehled o tom, jak se mají tato data použít, než budu sdílet tento řídicí panel s týmem?

Na tyto a další otázky vám Azure Data Catalog můžou poskytnout odpovědi. Střední, vysoká hodnota, spravované zdroje dat, které jsou používány napříč organizacemi, jsou často logickým výchozím bodem pro naplnění katalogu. I když kterýkoli uživatel může zaregistrovat zdroj dat, který zahájí katalog s daty, který je nejpravděpodobnější pro největší počet uživatelů, pomáhá při přijímání a používání systému. 

Pokud začínáte s Azure Data Catalog, identifikujte a zaregistrujete klíčové zdroje dat, které používá mnoho různých týmů uživatelů dat, může být prvním krokem úspěšné.

Tento scénář také představuje příležitost k tomu, abyste porozuměli zdrojům dat s vysokými hodnotami, aby bylo snazší pochopit a přistupovat k nim. Jedním z klíčových aspektů tohoto úsilí je zahrnout informace o tom, jak můžou uživatelé požádat o přístup ke zdroji dat. Pomocí Azure Data Catalog můžete zadat e-mailovou adresu uživatele nebo týmu, který je zodpovědný za řízení přístupu ke zdroji dat, odkazů na existující nástroje nebo dokumentaci nebo na bezplatný text, který popisuje proces přístupu-požadavek. Tyto informace pomáhají členům, kteří zjišťují registrované zdroje dat, ale kteří ještě nemají oprávnění k přístupu k datům, aby mohli snadno požádat o přístup pomocí procesů, které jsou definovány a řízeny vlastníky zdrojů dat.

## <a name="scenario-2-self-service-business-intelligence"></a>Scénář 2: business intelligence samoobslužných služeb
I když tradiční podniková podniková řešení i nadále jsou nepotřebná součást mnoha organizací data na úrovni cloudu, měnící se tempo firmy tak, že je samoobslužné a důležitější. Pomocí samoobslužného BI můžou informační pracovníci a analytiké vytvářet vlastní sestavy, sešity a řídicí panely, aniž by se museli spoléhat na centrálního IT oddělení nebo omezit plán a dostupnost tohoto týmu.

Ve scénářích samoobslužného BI uživatelé často slučují data z více zdrojů, mnohé z nich se ještě nemusely použít pro BI a analýzy. I když některé z těchto zdrojů dat již mohou být známy, může být obtížné zjistit, co se má udělat pro vyhledání a vyhodnocení potenciálních zdrojů dat pro danou úlohu.

Tradičně je tento proces zjišťování ručním: analytici používají připojení k síti rovnocenných sítí k identifikaci dalších uživatelů, kteří pracují s daty, která jsou požadována. Po nalezení a použití zdroje dat se tento proces zopakuje znovu pro každou následnou samoobslužnou práci BI s více uživateli, kteří provádějí redundantní ruční proces zjišťování.

S Azure Data Catalog může vaše organizace tento cyklus úsilí porušit. Po zjištění zdroje dat prostřednictvím tradičních prostředků ho analytik může zaregistrovat, aby ho snadněji vypracovali jiní uživatelé v budoucnu. I když analytik může přidat další hodnotu tím, že se zapisují do registrovaných datových assetů, tato poznámka se nemusí nakonat ve stejnou dobu jako registrace. Uživatelé mohou přispívat v průběhu času, jak plány umožňují, postupně přidávají hodnoty do zdrojů dat zaregistrovaných v katalogu.

Tento ekologický růst obsahu katalogu je přirozeným doplňkem k registraci centrálních zdrojů dat předem. Předem naplnění katalogu daty, které bude potřebovat mnoho uživatelů, může být Motivator pro počáteční použití a zjišťování. Umožnění registrace dalších zdrojů a přidávání poznámek do nich může být způsob, jak zajistit, aby se mohli i ostatní členové organizace zabývají.

Je potřeba poznamenat, že i když se tento scénář zaměřuje konkrétně na samoobslužné služby BI, stejné vzory a problémy se taky vztahují i na rozsáhlé podnikové projekty BI. Pomocí Data Catalog může vaše organizace zlepšit jakékoli úsilí, které zahrnuje ruční zpracování zjišťování zdrojů dat.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scénář 3: zachytávání znalostí samosprávné
Jak zjistíte, jaká data potřebujete k provedení úlohy a kde tato data najít?

Pokud jste v průběhu chvilky pracovali s vaší úlohou, pravděpodobně jste právě znali. Prošli jste postup postupného učení a v průběhu času jste se dozvěděli o zdrojích dat, které jsou klíčem ke každodenní práci.

Když se tým připojí k vašemu týmu, jak tato osoba ví, jaká data jsou potřebná pro úlohu a kde ji najít?

Lichá se vám nová osoba dostane s těmito otázkami.

Tento nepřetržitý přenos samosprávné znalostí je součástí procesu zjišťování zdrojů dat v organizacích, které jsou velké a malé. Další zkušení a zkušení členové týmu sestavili znalostní bázi během let a dozvěděli se o tom, že se k nim členové týmu dozvěděli. Nejdůležitější informace často existují jenom v hlavice několika klíčových osob, a když jsou lidé volní nebo odejdou z týmu, organizace utrpí.

Odborníky na data obvykle vyvinou úsilí k dokumentaci svých znalostí a jejich sdílení prostřednictvím e-mailu nebo dokumentů aplikace Word na týmovém webu služby SharePoint. I když tento přístup může být užitečný, zavádí nový problém zjišťování: jak uživatelé vědí, jaká dokumentace existuje a kde ji najít?

V Azure Data Catalog má vaše organizace jedno centrální umístění pro ukládání a sdílení těchto samosprávnéch znalostí a usnadňuje zjistitelnost. V Data Catalog můžou odborníci na data dokomentovat datové assety přímo a poskytnout odkazy na stávající dokumentaci. Když členové organizace použijí Katalog ke zjištění zdroje dat, zjistí nejen samotný zdroj, ale také znalosti, které dříve existovaly pouze v mozkyi expertů vaší organizace.
