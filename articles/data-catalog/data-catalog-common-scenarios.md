---
title: Běžné scénáře služby Azure Data Catalog
description: Přehled běžných scénářů pro Azure Data Catalog, včetně registraci a zjišťování zdrojů dat vysoké hodnoty, povolení samoobslužné služby business intelligence a zaznamenávání stávající znalosti o zdrojích dat a procesů.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 531c5890cb2164b51d7b383d0b1fdd1b92d9e8eb
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405025"
---
# <a name="azure-data-catalog-common-scenarios"></a>Běžné scénáře služby Azure Data Catalog
Tento článek představuje běžné scénáře, kde Azure Data Catalog může pomoci organizaci získat větší hodnotu z její existující zdroje dat.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scénář 1: Registrace zdrojů dat centrální
Organizace často mají mnoho zdrojů dat vysoké hodnoty. Tyto zdroje dat patří-obchodní, online zpracování systémy (OLTP), datových skladů a business intelligence a analýzy databází transakcí. Počet systémů a překrytí mezi nimi, obvykle v čase měnících se potřeb firmy a zvětšuje podnik sám vyvíjí prostřednictvím, například fúze a akvizice.

Může být obtížné pro členy organizace vědět, kam umístit data v rámci těchto zdrojů dat. Jsou všechny moc známé otázky vypadat asi takto:

* Tři HR systémů používaných v rámci společnosti, které mám použít k vytvoření tohoto typu sestavy?
* Kde najdu získat certifikovaných prodejní čísla pro fiskální rok, který právě skončila?
* Kdo by měl požádat, nebo jaký je proces, který mám použít k získání přístupu k datovému skladu?
* Nevím, pokud tyto hodnoty jsou správné. Kdo mi požádat o insight na jak tato data by měla použít předtím, než se dá sdílet tento řídicí panel s mého týmu?

Na tyto a další otázky Azure Data Catalog může poskytnout odpovědi. Zdroje dat centrální vysoké hodnoty, spravuje IT, které se používají v organizacích jsou často logické výchozí bod pro vyplnění katalogu. I když každý uživatel může zaregistrovat zdroj dat, kick-started se zdroji dat, které bývají nejčastějším zadejte hodnotu pro maximální počet uživatelů katalogu pomůže dokázal(a) a použití systému. 

Pokud začínáte se službou Azure Data Catalog, identifikujete a zaregistrujete klíčových zdrojích dat, které jsou používány mnoha různými týmy spotřebitelé dat může být prvním krokem k úspěchu.

Tento scénář také představuje příležitost k přidání poznámek ke zdrojům dat vysoké hodnoty, aby se daly snadněji pochopit a přístup. Klíčový aspekt tomto úsilí je také informace o tom, jak uživatelé si mohou vyžádat přístup ke zdroji dat. S Azure Data Catalog zadáte e-mailovou adresu uživatele nebo týmu, který je zodpovědný za řízení přístupu zdroj dat, odkazy do existujících nástrojů nebo v dokumentaci nebo libovolný text, který popisuje proces žádosti o přístup. Tyto informace pomáhají členy, kteří zjišťovat registrované datové zdroje, ale který ještě nemáte oprávnění pro přístup k datům pomocí procesů, které jsou definovány a řídí vlastníky zdroj dat snadno požádáte o přístup.

## <a name="scenario-2-self-service-business-intelligence"></a>Scénář 2: Samoobslužné služby business intelligence
Přestože tradiční řešení business intelligence i nadále být neocenitelnou součástí řada organizací datových prostředí, změna tempem byznysu provedl samoobslužné funkce BI čím dál tím víc důležité. Pomocí samoobslužné funkce BI informační pracovníci a analytici můžete vytvořit vlastní sestavy, sešity a řídicí panely bez nutnosti spoléhat se na střed IT tým nebo jsou omezené na základě plánu a dostupnosti IT tým.

V samoobslužné BI scénáře uživatelé běžně kombinovat data z víc zdrojů, z nichž mnohá nemusí již byl použit pro BI a analýzy. I když některé z těchto zdrojů dat už může být známé, může být náročné Pokud chcete zjistit, co dělat, vyhledejte a vyhodnotit potenciální zdroje dat pro danou úlohu.

Tradičně, tento proces zjišťování je ruční: analytici použít k identifikaci ostatní, kteří pracují s daty se žádá o jejich síťová připojení partnera. Po zdroj dat je nalezen a použit, proces se opakuje znovu pro každý další samoobslužné BI úsilí, s více uživatelů, kteří provádějí redundantní ruční proces zjišťování.

S Azure Data Catalog vaše organizace můžete přerušit tento cyklus úsilí. Po zjištění zdroje dat prostřednictvím tradičních prostředky, můžete zaregistrovat analytikovi ho tak, aby se snadněji zjistitelné jinými uživateli v budoucnu. I když analytik můžete přidat další hodnoty anotací registrovaných datových assetů, není potřeba probíhat ve stejnou dobu jako registrace tuto poznámku. Uživatelé mohou přispívat v čase, jako jejich plány povolení postupně přínos pro zdroje dat registrované v katalogu.

Tento organickým růstem katalogu obsahu je přirozené doplněk k počáteční registraci centrální datových zdrojů. Předem vyplnění katalogu s daty, která bude nutné mnoho uživatelů může být motivator pro počáteční použití a zjišťování. Povolení uživatelům zaregistrujte a opatřete poznámkami další zdroje může být způsob, jak je a ostatní členové organizace, zapojení.

Je vhodné poznamenat, že i když tento scénář se zaměřuje konkrétně na samoobslužné funkce BI, stejné vzory a problémy platí pro rozsáhlé podnikové BI projekty i. Používání katalogu Data Catalog, můžete zlepšit vaše organizace žádné úsilí, která zahrnuje ruční proces zjišťování zdrojů dat.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scénář 3: Zachytávání znalosti
Jak víte, jaká data je třeba provést úlohy a kde najít data?

Pokud jste v úloze, nějakou dobu, pravděpodobně stačí vědět. Prošli postupné procesem učení a postupně se naučili o zdrojích dat, které jsou klíčem k své každodenní práci.

Poté, co nového zaměstnance připojí vašeho týmu, jak tato osoba vědět, jaká data jsou potřebné pro úlohu a kde ho najít?

Pravděpodobné se nové osobě, vám přinášejí tyto otázky.

Tento probíhající přenos znalosti je součástí procesu zjišťování zdrojů dat v malé i velké organizace. Vedoucí a zkušení členové týmu mají vytvořená znalostní báze v průběhu let a novější členové týmu se naučili, jak požádejte ho, když se na něco zeptat. Nejvíce důležitých informací často existuje pouze v vedoucích několik klíčových osob a pokud se tito uživatelé budou na dovolenou nebo týmu opustí tým, organizace trpí.

Odborníků na data obvykle měli snažit dokumentu své znalosti sdílení e-mailem nebo do dokumentů aplikace Word na týmový web Sharepointu. Přestože tento přístup může být důležité, zavádí nové zjišťování problém: jak uživatelé věděli, jaké dokumentaci existuje a kde ho najít?

S Azure Data Catalog má vaše organizace jednom centrálním umístění pro ukládání a sdílení tyto kmenové znalosti a díky tomu snadno zjistitelné. Ve službě Data Catalog odborníků na data můžete opatřit poznámkami datové assety a poskytují odkazy na stávající dokumentaci. Když členové organizace pomocí katalogu objeví zdroj dat, naleznou nejen zdroj samotný, ale také s vědomím, že dříve existoval pouze ve vaší organizaci odborníků.
