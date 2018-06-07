---
title: Azure SQL databáze Azure Případová studie - GEP | Microsoft Docs
description: Další informace o tom, jak GEP používá SQL Database k dosažení více globálních zákazníků a dosáhnout vyšší efektivity
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: c39f59c4648fb8eafb8db4cb8cd2bc73de02dca4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647108"
---
# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure poskytuje globální reach GEP a vyšší efektivity
![GEP Logo](./media/sql-database-implementation-gep/geplogo.png)

GEP přináší softwaru a služeb, které umožňují nákup žebříčky po celém světě a maximalizovat tak jejich dopad na operace, strategie a finanční přínos své firmy. Kromě služby konzultační a spravované služby nabízí společnost ČIPOVÉ podle GEP®, založené na cloudu, komplexní nákup softwaru platformy. Ale GEP narazili omezení pokusu o podpoře řešení, jako jsou ČIPOVÉ podle GEP vlastní místní datových centrech: investice potřebné byly zvládnutí a zákonné požadavky v dalších zemích by provedli nezbytné investice více stále složitý. Přesunutím do cloudu GEP má uvolněna, IT zdroje, díky kterému jej starosti menší o IT oddělení a zaměřit se další informace o vývoji nových zdrojů hodnoty pro své zákazníky po celém světě.

## <a name="expanding-services-and-growth-by-using-azure"></a>Rozšíření služeb a růstu pomocí služby Azure
ČIPOVÉ GEP zákazníci rádi funkce platformu a snadné použití; Zákazníci můžete spravovat jejich procesy odkudkoli a kdykoli a na jakémkoli zařízení – přenosných počítačů, tabletu nebo telefonu. Přesunutím do služby Microsoft Azure GEP bylo možné přizpůsobit jeho rychlý nárůst a jeho potenciální rozšířit nových trhů. Podle na GEP VP of Technology, Dhananjay Nagalkar "Microsoft Azure hrál klíčovou roli v GEP na úspěch tím, že se nám Rychlé škálování služby s flexibility a tím, že poskytuje regionální datová centra, který Pomozte nám regulačních vyhovovaly našich globální zákazníků."

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>Omezení samoobslužné datového centra
2013 GEP žebříčky rozpoznat, že budou potřeba způsob, jak zajistit škálovatelnost a výkon, jak se vzrostla zákazníkovi základní. Vysvětlení, Nagalkar "ke splnění tohoto vyžádání pomocí našich existující Datacenter, jsme by měli podstatně rozbalte naše infrastruktury a IT zdroje. Investice a časový rámec, který by byl značné." Místní, že fyzické a virtuální počítače vyžadují rozsáhlé konfiguraci, správu, škálování, zálohování a opravy rychlostí, která by měla být cenově pro GEP náklady. Cloudové řešení, na druhé straně nabízí jednoduchost a pohodlí, který povolený GEP umožňuje zaměřit se další na vývoj místo Správa velkých – a rostoucí – IT oddělení. Nagalkar věděl, že GEP může snížit režii nákupu, konfiguraci a správu jeho infrastruktury migrací do cloudu.

GEP potřeby také způsob, jak předcházet regulačních překážky, které zachovány mimo některé globální trhů. Pro řadu potenciální zákazníky Evropského na GEP by vyžadovaly předpisů s daty uloženými v jejich místní zeměpisné oblasti. Ale ho nebude mít praktické pro GEP k sestavení se několik datových center. "Investic do infrastruktury rozšířeným a IT celkové náklady na by přineste významný dopad na okraje," podle Nagalkar. "V důsledku toho jsme ve skutečnosti museli zapněte ji okamžitě potenciální zákazníci, kteří požadované místně uložená data."

Nagalkar věděl, že s cloudovým řešením může být odpověď na tuto dilematem. Pokud GEP může přesunout do poskytovatele cloudových služeb s globální přítomnosti, může lépe splňovat svým zákazníkům regulačních a výkonu musí uložením dat blíže na fyzických umístěních zákazníků.

## <a name="finding-smooth-skies-in-the-cloud"></a>Hledání smooth skies v cloudu
Nagalkar a jeho tým prozkoumali několik cloudových možností, ale většina byly infrastruktury jako služba (IaaS) – na základě řešení, které by vyžadovaly GEP výraznou investice do IT zdroje, konfigurovat a spravovat službu. Přizpůsobit Azure řešení platforma jako služba (PaaS) je mnohem lepší.

"S Azure, GEP nepotřebuje k řešení správy databáze, konfigurace virtuálního počítače, opravy nebo další úlohy správy infrastruktury" uvádí Nagalkar. "Místo toho, abychom se mohli zaměřit naše prostředky na co můžeme udělat nejlépe: využívat naše odborných znalostí v nákup zápis software, který skutečně poskytuje výsledky pro naše zákazníky." 

Přejděte do Azure ve skutečnosti aktivoval GEP pro pracovníky IT operace zmenšení při povolování současně více funkcí pro zákazníky.

Využitím datových centrech Azure po celém světě, GEP snadno rozšířit jeho reach napříč Evropy a Asie. Tyto globální datová centra povolit GEP ke škálování se flexibility a, aby splňoval požadavky zákazníka pro místně uložená data, která snižuje latence a splňuje zákonné požadavky.

## <a name="smart-by-gep-architecture"></a>INTELIGENTNÍ podle GEP architektury
GEP vytvářet ČIPOVÉ podle GEP od základů v Azure. Kritické motivace pro GEP byl větší škálovatelnost, méně výpadky, a náklady méně rozsáhlou údržbu, které GEP se může setkat s Azure SQL Database ve srovnání s co GEP může dosáhnout místně. Ale po přesunout do cloudu, GEP zjištěné nové příležitosti pro vývoj v cloudu, jako je rychlé při vytváření prototypu a štíhlého technici lépe reagovat na požadavky zákazníka. Vývoj v Azure umožní GEP rychle dělat s licenční hlavu, které by mohl spustit jeho vývojáři do místní software. Základní částí INTELIGENTNÍ podle GEP je Azure SQL Database, když GEP používá mnoho jinými službami Azure snadno a rychle pokračovat ke zlepšení ČIPOVÉ podle GEP.

![INTELIGENTNÍ podle GEP architektury](./media/sql-database-implementation-gep/figure1.png) obrázek 1. INTELIGENTNÍ podle GEP architektury

## <a name="structured-data"></a>Strukturovaných dat
Jádrem ČIPOVÉ GEP aplikací jsou instance Azure SQL Database toto řešení power enterprise nákup management. Když GEP analyzovány ČIPOVÉ podle GEP, viděli Azure SQL Database jako vzájemně přizpůsobit pro jeho architekturu, ten, který by povolit společnosti k dosažení na nejvyšší úrovni ochrany dat a k jeho zákonným požadavkům. GEP využívá z několika vrstev ochrany dat, které aplikace nabízí Azure SQL Database, včetně:

* Šifrování dat v klidovém stavu prostřednictvím transparentní šifrování dat.
* Ověřování zabezpečení integrací databáze SQL Azure s Azure Active Directory.
* Omezení přístupu na příslušné podmnožinu dat pomocí zabezpečení na úrovni řádků.
* Maskování dat v reálném čase pomocí zásad.
* Sledování události databáze prostřednictvím auditování databáze SQL Azure.

> "Můžeme použít všechny tyto možnosti bez jakýchkoli změn hlavní kódu a s minimálním dopadem na výkon," uvedená Nagalkar.
> 
> 

Pomocí Azure SQL Database GEP automaticky má větší možnosti zotavení po havárii, než by mohla mít ekonomického analyzovány místně z důvodu funkce odolnost proti chybám integrovaný do Azure SQL Database. GEP používá aktivní geografickou replikací funkce ve službě Azure SQL Database, spolu s více aktivní, čtení a online sekundární repliky (skupiny dostupnosti Always On) v různých zeměpisných oblastech, k vytvoření dvojice vysokou dostupnost. Replikace ČIPOVÉ podle GEP data v oblastech znamená, že v případě havárie celou oblast, GEP můžete snadno obnovit data zákazníků s cíl minimální bodu obnovení (RPO) a cíl doba obnovení (RTO).

Každý ČIPOVÉ zákazníkem GEP má dvě instance Azure SQL Database: jeden pro online zpracování transakcí (OLTP) a jeden pro analýzu (například zákazníka tráví a sestavy analýzy). Azure SQL Database elastické fondy povolit GEP snadno spravovat tisíce databází globálně pro zpracování požadavků na nepředvídatelným databáze prostředky. Elastické fondy prostředkem GEP zajistit, že zákazník databáze můžete škálovat podle potřeby, bez předimenzování nebo v rámci zřizování, a zároveň GEP k řízení nákladů. Navíc vzhledem k tomu, že je služba PaaS, GEP získá všechny nové funkce Azure SQL Database pomocí automatické upgrady.

## <a name="unstructured-and-semi-structured-data"></a>Nestrukturovaných a částečně strukturovaných dat
Některé ČIPOVÉ podle GEP zákaznické údaje však musí méně pevně strukturovaných úložiště. Pro tento typ dat aktivuje GEP úložiště objektů Blob v Azure, Azure Table Storage a Azure Redis Cache. Azure Blob storage je umístěno přílohy, které INTELIGENTNÍ podle GEP uživatelé nahrávání do aplikace. Je také tam, kde INTELIGENTNÍ podle GEP ukládá statický obsah, jako je například kaskádových stylů (CSS) a soubory jazyka JavaScript.

GEP ukládá data zákazníků přístupem, jako jsou ČIPOVÉ GEP data protokolu, v Azure Table Storage, která umožňuje GEP efektivně neomezená cenově výhodné úložiště a časy rychlé načítání bez nutnosti starat o nastavení schéma pro data. GEP používá pro hlavní mezipaměti Azure Redis Cache.

## <a name="authentication-and-routing"></a>Ověřování a směrování
Azure přístup k řízení služby (ACS) poskytuje ČIPOVÉ GEP uživatelé s širokou škálu možností pro přihlášení k softwaru. Azure ACS může provést federaci s všechny zprostředkovatele identity, která výměny ověřovací data pomocí Assertion Markup Language SAML (Security), například Active Directory Domain Services, Ping Identity, OneLogin nebo SiteMinder. To pomáhá GEP implementace jednotné přihlašování (SSO) pro zákazníky bez obav o ukládání uživatelských přihlašovacích údajů a údržbě zásad hesel zákazníků.

Po přihlášení zákazníkům mít přístup k prostředkům správné firmy v ČIPOVÉ podle GEP. GEP používá Azure Traffic Manager pro přesměrování a vyrovnávání zatížení požadavky přicházející z mobilních zařízení zákazníků a relací prohlížeče.

## <a name="other-azure-services"></a>Jinými službami Azure
GEP využívá počet jinými službami Azure, aby ČIPOVÉ podle GEP reaguje na zákazníků potřebuje. GEP používá k hostiteli aplikace – prezentační a obchodní logiky služby Zabezpečené cloudové služby Azure (webové a pracovní role). Cloudové služby umožňují vývojářům ke správě infrastruktury jako kód (IAC) a k nasazení nové ČIPOVÉ GEP aplikace za zlomek času, který je vyžadován s místními datacentry – všechny bez jakékoli zásahu IT. GEP mohou vývojáři služby cloud pracovní prostředí otestovat nové verze bez vlivu na aktuální produkčním nasazení. Po testovány, GEP používá funkce prohození virtuální IP adresy služby Azure cloud services přesunout pracovní kódu na produkční slot do jedné minuty a méně výpadků nasazení.

Pro snížení latencí aplikací, používá GEP sítě doručování obsahu (CDN) Azure uvést statický obsah uložený v úložišti objektů Blob v Azure (například soubory šablon stylů CSS a JavaScript) na servery edge blízko uživatele. GEP používá Azure Service Bus podporovat zpracování Architektura aplikace od publikování a odběru na částečně příkaz dotazu přizpůsobivý oddělení (CQRS) a vrstvu architektura s volné párování a asynchronní komunikaci. GEP Azure Media Services používá ke zvyšování svou službu zákaznické podpory. GEP nalezen, aby mohl snadno odeslat podpora uživatelů videa v Azure Media Services. Tyto videa odpovídají na běžné dotazy uživatele, která pomáhá zlepšit GEP spokojenost uživatelů při pořizování některé zatížení podpory z pracovníci podpory zákazníků společnosti GEP ČIPOVÉ.

K odeslání na tisíce transakčních e-mailů denně generované ČIPOVÉ podle GEP, firma používá rozhraní API .NET sendgrid vám umožňuje integraci s Azure. Pro vývojáře GEP, to je snadné provést – sendgrid vám umožňuje rozšíření pro Azure je k dispozici pravé v Azure Marketplace. Vývojáři GEP může nakonfigurovat ČIPOVÉ podle GEP pomocí právo balíček NuGet sendgrid vám umožňuje v sadě Microsoft Visual Studio; GEP IT může monitorovat provoz e-mailu softwaru sendgrid vám umožňuje přímo z Azure.

Nakonec ČIPOVÉ podle GEP používá Azure Virtual Machines – službu Azure IaaS – k hostování aplikací a služeb, které nedávalo smysl, v době technici nahradit s řešeními PaaS nebo softwaru jako služba (SaaS). Například GEP hostitelem integrace rozhraní API služby virtuálních počítačů pro business-to-business (B2B) integrace systémech zákazníků místní enterprise prostředek plánování (ERP) jako SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP a Lawson a s řešeními SaaS zákazníka efektivně Exchange nákup dokumentů, například faktury.

> "Vytváření INTELIGENTNÍ GEP v cloudu Microsoft Azure úplně odebrala potřebu místní IT, ne jenom pro GEP ale i pro naše zákazníky nákup operace." 
> 
> – Dhananjay Nagalkar, VP řešení technologie
> 
> 

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Rozbalte spokojenost zákazníků bez rozbalení IT
Od migrace z místních datových center na Azure a sestavování ČIPOVÉ podle GEP od začátku na platformě Azure, GEP zvýšilo škálovatelností a flexibilitou, bez nutnosti rozšířit své infrastruktury nebo pracovníci IT. Ve skutečnosti společnost nebyla přidána IT zdroje ve více než 4 roky. Model PaaS pohodlný Azure aktivoval GEP ke snížení jeho výdaje na podporu a operace správy dodavatele. V důsledku toho GEP bylo možné fokus prostředky na vývoj softwaru; a vývoj v cloudu umožňuje vývojářům GEP rychle testování nových nápadů bez nutnosti čas spolupráci s IT nebo starostí o místní licenční požadavky. Databáze SQL Azure pomáhá GEP lépe zajistěte, aby jeho zákazníci vždy výjimečných služby a výkonu.

## <a name="more-information"></a>Další informace
* Domovská stránka GEP: [GEP](http://www.gep.com)
* Inteligentní podle GEP: [inteligentní podle GEP](http://www.smartbygep.com)

## <a name="gep-contributors"></a>Přispěvatelé GEP
* Huzaifa Matawala přidružit ředitel – Architekti, GEP
* GEP Sathyan Narasingh, technici Manager
* Deepa Velukutty, databáze architekti GEP

