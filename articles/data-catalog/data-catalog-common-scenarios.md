---
title: Běžné scénáře služby Azure Data Catalog
description: Přehled běžných scénářů pro Azure Data Catalog, včetně registrace a zjišťování zdrojů dat s vysokou hodnotou, povolení samoobslužné business intelligence a zachycení existujících znalostí o zdrojích dat a procesech.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736462"
---
# <a name="azure-data-catalog-common-scenarios"></a>Běžné scénáře služby Azure Data Catalog
Tento článek představuje běžné scénáře, kde Azure Data Catalog může pomoci vaší organizaci získat větší hodnotu z jeho stávajícízdroje dat.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scénář 1: Registrace centrálních zdrojů dat
Organizace mají často mnoho zdrojů dat s vysokou hodnotou. Tyto zdroje dat zahrnují obchodní systémy, systémy online zpracování transakcí (OLTP), datové sklady a databáze business intelligence/analytics. Počet systémů a jejich překrývání se obvykle časem zvyšuje s tím, jak se vyvíjejí obchodní potřeby a samotné podnikání se vyvíjí například prostřednictvím fúzí a akvizic.

Pro členy organizace může být obtížné zjistit, kde lze data v rámci těchto zdrojů dat najít. Otázky, jako jsou následující, jsou příliš časté:

* Ze tří hr systémů používaných v rámci společnosti, které bych měl použít k vytvoření tohoto typu sestavy?
* Kde mám jít získat certifikovaná prodejní čísla pro fiskální rok, který právě skončil?
* Koho se mám zeptat nebo jaký proces bych měl použít k získání přístupu do datového skladu?
* Nevím, jestli jsou ta čísla správná. Koho mohu požádat o informace o tom, jak mají být tato data používána, než budu sdílet tento řídicí panel se svým týmem?

Na tyto a další otázky může Azure Data Catalog poskytnout odpovědi. Centrální zdroje dat spravované IT s vysokou hodnotou, které se používají napříč organizacemi, jsou často logickým výchozím bodem pro vyplnění katalogu. Přestože každý uživatel může zaregistrovat zdroj dat, s katalogu kick-started se zdroji dat, které jsou s největší pravděpodobností poskytnout hodnotu pro největší počet uživatelů pomáhá řídit přijetí a použití systému. 

Pokud začínáte s Azure Data Catalog, může být vaším prvním krokem k úspěchu identifikace a registrace klíčových zdrojů dat, které používají mnoho různých týmů spotřebitelů dat.

Tento scénář také představuje příležitost k opatří vysoce hodnotné zdroje dat, aby byly srozumitelnější a přístup k nim. Jedním z klíčových aspektů tohoto úsilí je zahrnout informace o tom, jak mohou uživatelé požádat o přístup ke zdroji dat. Pomocí Katalogu dat Azure můžete zadat e-mailovou adresu uživatele nebo týmu, který je zodpovědný za řízení přístupu ke zdroji dat, odkazy na existující nástroje nebo dokumentaci nebo volný text, který popisuje proces žádosti o přístup. Tyto informace pomáhají členům, kteří zjišťují registrované zdroje dat, ale ještě nemají oprávnění k přístupu k datům, aby mohli snadno požádat o přístup pomocí procesů, které jsou definovány a řízeny vlastníky zdrojů dat.

## <a name="scenario-2-self-service-business-intelligence"></a>Scénář 2: Samoobslužné business intelligence
Ačkoli tradiční řešení podnikového podnikového zpravodajství jsou i nadále neocenitelnou součástí datového prostředí mnoha organizací, měnící se tempo podnikání učinilo samoobslužné BI stále důležitější. Pomocí samoobslužné BI mohou informační pracovníci a analytici vytvářet vlastní sestavy, sešity a řídicí panely, aniž by se spoléhali na centrální IT tým nebo byli omezeni plánem a dostupností it týmu.

Ve scénářích samoobslužných BI uživatelé běžně kombinují data z více zdrojů, z nichž mnohé nemusely být dříve použity pro BI a analýzu. Přestože některé z těchto zdrojů dat již mohou být známy, může být náročné zjistit, co dělat, abyste našli a vyhodnotili potenciální zdroje dat pro daný úkol.

Tento proces zjišťování je tradičně ruční: analytici používají svá připojení k síti rovnocenných zařízení k identifikaci ostatních, kteří pracují s požadovanými daty. Po nalezení a použití zdroje dat se proces znovu opakuje pro každé následné samoobslužné úsilí BI, přičemž více uživatelů provádí redundantní ruční proces zjišťování.

S Azure Data Catalog může vaše organizace tento cyklus úsilí přerušit. Po objevení zdroje dat tradičními prostředky může analytik zaregistrovat, aby byl v budoucnu snadněji zjistitelný ostatními uživateli. Přestože analytik může přidat další hodnotu anotací registrovaných datových prostředků, tato anotace nemusí probíhat současně s registrací. Uživatelé mohou přispívat v průběhu času, jak jejich plány umožňují, postupně přidávat hodnotu ke zdrojům dat registrovaným v katalogu.

Tento organický růst obsahu katalogu je přirozeným doplňkem k počáteční registraci centrálních zdrojů dat. Předvyplnění katalogu daty, která bude potřebovat mnoho uživatelů, může být motivátorem pro počáteční použití a zjišťování. Povolení uživatelům zaregistrovat a oznamovat další zdroje může být způsob, jak udržet je a ostatní členy organizace zapojen.

Stojí za zmínku, že i když se tento scénář zaměřuje konkrétně na samoobslužné BI, stejné vzory a výzvy platí i pro rozsáhlé podnikové bi projekty. Pomocí katalogu dat může vaše organizace zlepšit veškeré úsilí, které zahrnuje ruční proces zjišťování zdroje dat.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scénář 3: Zachycení kmenových znalostí
Jak víte, jaká data potřebujete ke své práci a kde je najít?

Pokud jste byl ve své práci na chvíli, pravděpodobně jen víte. Prošli jste postupným procesem učení a postupem času jste se dozvěděli o zdrojích dat, které jsou klíčem k vaší každodenní práci.

Když se k vašemu týmu připojí nový zaměstnanec, jak tato osoba ví, jaká data jsou pro práci potřebná a kde je najít?

Je pravděpodobné, že nová osoba přijde k vám s těmito otázkami.

Tento pokračující přenos kmenových znalostí je součástí procesu zjišťování zdrojů dat v organizacích velkých i malých. Starší a zkušení členové týmu si v průběhu let vybudovali znalosti a novější členové týmu se naučili se jich ptát, když mají otázky. Nejdůležitější informace často existují pouze v hlavách několika klíčových lidí, a když jsou tito lidé na dovolené nebo opustí tým, organizace trpí.

Odborníci na data se obvykle snaží dokumentovat své znalosti a sdílet je prostřednictvím e-mailu nebo v dokumentech aplikace Word na týmovém webu SharePointu. Ačkoli tento přístup může být cenný, zavádí nový problém s objevem: jak lidé vědí, jaká dokumentace existuje a kde ji najít?

S Azure Data Catalog má vaše organizace jediné centrální umístění pro ukládání a sdílení těchto kmenových znalostí a pro snadné jejich zjišťování. V katalogu dat mohou vaši odborníci na data přímo oznamovat datové prostředky a poskytovat odkazy na existující dokumentaci. Když členové organizace používají katalog k nalezení zdroje dat, najdou nejen samotný zdroj, ale také znalosti, které dříve existovaly pouze v myslích odborníků vaší organizace.
