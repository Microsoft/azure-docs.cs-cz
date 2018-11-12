---
title: 'Azure Cosmos DB: Úvod do rozhraní SQL API | Dokumentace Microsoftu'
description: Zjistěte, jak můžete používat službu Azure Cosmos DB k ukládání a dotazování velkého množství dokumentů JSON s nízkou latencí pomocí jazyka SQL a JavaScriptu.
keywords: json databáze, databáze dokumentu
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: overview
ms.date: 05/22/2017
ms.author: rafats
ms.openlocfilehash: f71a7ad538e22359a2a0dd9c2e3f6c4448ee5a2d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232379"
---
# <a name="introduction-to-azure-cosmos-db-sql-api"></a>Úvod do služby Azure Cosmos DB: SQL API

[Azure Cosmos DB](introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Azure Cosmos DB poskytuje [globální distribuci na klíč](distribute-data-globally.md), [elastické škálování propustnosti a úložiště](partition-data.md) po celém světě, latence v řádu milisekund na 99. percentilu, [pět jasně definovaných úrovní konzistence](consistency-levels.md) a zaručenou vysokou dostupnost. To vše je podloženo [nejlepšími smlouvami SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat.

![Azure SQL API](./media/sql-api-introduction/cosmosdb-sql-api.png) 

Díky rozhraní SQL API poskytuje služba Azure Cosmos DB bohaté a známé [možnosti příkazů jazyka SQL](sql-api-sql-query.md) nad daty JSON bez schématu s konsistentní nízkou latencí. Tento článek poskytuje přehled rozhraní SQL API služby Azure Cosmos DB a popisuje, jak ho použít k ukládání velkých objemů dat JSON, dotazování těchto dat s latencí v řádu milisekund a snadnému rozšíření schématu. 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Jaké schopnosti a klíčové funkce nabízí databáze Azure Cosmos?
Azure Cosmos DB prostřednictvím rozhraní SQL API nabízí následující klíčové funkce a výhody:

* **Elasticky škálovatelná propustnost a úložiště:** Kapacitu databáze JSON je možné snadno vertikálně navyšovat nebo snižovat, aby splňovala potřeby vaší aplikace. Data se ukládají na discích SSD (solid-state drive), které nabízí nízkou a předvídatelnou latenci. Azure Cosmos DB podporuje kontejnery pro ukládání dat JSON, kterým se říká kolekce a které je možné škálovat na prakticky neomezené velikosti úložiště a zřízenou propustnost. S růstem vaší aplikace je možné službu Azure Cosmos DB bezproblémově elasticky škálovat s předvídatelným výkonem. 


* **Replikace ve více oblastech:** Azure Cosmos DB transparentně replikuje data do všech oblastí, které jste přidružili k vašemu účtu služby Azure Cosmos DB, a umožňuje vyvíjet aplikace, které vyžadují globální přístup k datům a zároveň poskytují kompromisy mezi konzistencí, dostupností a výkonem, a to vše s odpovídajícími zárukami. Azure Cosmos DB poskytuje transparentní regionální převzetí služeb při selhání se souběžnou instalací rozhraní API a schopností elastického škálování propustnosti a úložiště po celém světě. Další informace najdete v tématu [Globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md).

* **Ad hoc dotazy se známou syntaxí SQL:** Můžete ukládat heterogenní dokumenty JSON a dotazovat se na ně pomocí známé syntaxe SQL. Azure Cosmos DB využívá vysoce souběžnou technologii indexování strukturovanou do protokolů, která nevyžaduje zamykání a automaticky indexuje veškerý obsah dokumentů. Díky tomu lze používat bohaté dotazy v reálném čase, aniž by bylo potřeba specifikovat parametry schématu, sekundární indexy nebo zobrazení. Další informace najdete v tématu [Dotazování služby Azure Cosmos DB](sql-api-sql-query.md). 
* **Spouštění JavaScriptu uvnitř databáze:** Logiku aplikace je možné vyjádřit jako uložené procedury, triggery a uživatelem definované funkce (UDF) prostřednictvím standardního JavaScriptu. To logice aplikace umožňuje pracovat s daty, aniž by bylo nutné se obávat neshody mezi aplikací a schématem databáze. Rozhraní SQL API poskytuje možnost plného spouštění logiky aplikace JavaScript z transakcí přímo v databázovém stroji. Tato těsná integrace JavaScriptu umožňuje spouštět operace INSERT, REPLACE, DELETE a SELECT z javascriptového programu jako izolované transakce. Další informace najdete v tématu [Programování na straně serveru SQL](programming.md).

* **Přizpůsobitelné úrovně konzistence:** Pro dosažení optimálního poměru mezi konzistencí a výkonem si můžete vybrat z pěti jasně definovaných úrovní konzistence. Pro dotazy a operace čtení nabízí služba Azure Cosmos DB pět různých úrovní konzistence: silná, omezená neaktuálnost, relace, konzistentní předpona a konečný výsledek. Tyto podrobné, dobře definované úrovně konzistence umožňují zvolit vhodný poměr mezi konzistencí, dostupností a latencí. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

* **Plně spravovaná služba:** Není nutné spravovat prostředky databáze a počítačů. Vzhledem k tomu, že se jedná o plně spravovanou službu Microsoft Azure, není potřeba spravovat virtuální počítače, nasazovat a konfigurovat software, spravovat škálování nebo řešit komplexní upgradování datové vrstvy. Každá databáze je automaticky zálohována a chráněna proti selháním v dané oblasti. Jednoduše si můžete přidat účet služby Azure Cosmos DB a zřídit kapacitu podle potřeby. Díky tomu se můžete soustředit na svou aplikaci, ne na provoz a správu databáze. 

* **Otevřené řešení:** Pomocí existujících dovedností a nástrojů můžete začít rychle. Programování s rozhraním SQL API je jednoduché, srozumitelné a nevyžaduje, abyste se učili pracovat s novými nástroji nebo využívali vlastní rozšíření JSON nebo JavaScriptu. Máte přístup k veškerým funkcím databáze, včetně CRUD, dotazování a zpracování JavaScriptu přes jednoduché rozhraní RESTful HTTP. Rozhraní SQL API pracuje s existujícími formáty, jazyky a standardy a zároveň k nim nabízí hodnotné schopnosti databáze.

* **Automatické indexování:** Azure Cosmos DB ve výchozím nastavení automaticky indexuje všechny dokumenty v databázi a neočekává ani nevyžaduje žádné schéma nebo vytváření sekundárních indexů. Nechcete indexovat všechno? Buďte bez obav, můžete také [výslovně nesouhlasit s používáním cest v souborech JSON](indexing-policies.md).

* **Podpora kanálu změn:** Kanál změn poskytuje seřazený seznam dokumentů v kolekci Azure Cosmos DB v pořadí, ve kterém byly upraveny. Tento informační kanál je možné použít k naslouchání změnám dat, aby bylo možné na základě aktualizací replikovat data, aktivovat volání rozhraní API nebo provádět zpracování datového proudu. Kanál změn je automaticky povolený a snadno se používá: přečtěte si [další informace o kanálu změn](https://docs.microsoft.com/azure/cosmos-db/change-feed). 

## <a name="data-management"></a>Jak se spravují data pomocí rozhraní SQL API?
Rozhraní SQL API pomáhá spravovat data JSON prostřednictvím jasně definovaných databázových prostředků. Tyto prostředky se pro zachování vysoké dostupnosti replikují a je možné je jedinečně adresovat pomocí logického identifikátoru URI. Rozhraní SQL API nabízí pro všechny prostředky jednoduchý programovací model RESTful založený na protokolu HTTP. 


Účet databáze Azure Cosmos DB je jedinečný obor názvů, který vám dává přístup ke službě Azure Cosmos DB. Před vytvořením databázového účtu si musíte pořídit předplatné Azure, které vám umožní přístup k různým službám Azure. 

Všechny prostředky ve službě Azure Cosmos DB jsou modelovány a ukládány jako dokumenty JSON. Prostředky se spravují jako položky, kterými jsou dokumenty JSON obsahující metadata, a jako informační kanály, jimiž jsou kolekce položek. Sady položek se uchovávají ve svých příslušných kanálech.

Obrázek níže znázorňuje vztahy mezi prostředky Azure Cosmos DB:

![Hierarchický vztah mezi prostředky ve službě Azure Cosmos DB][1] 

Databázový účet se skládá ze sady databází, kde každá obsahuje několik kolekcí, z nichž každá pak může obsahovat uložené procedury, triggery, funkce UDF, dokumenty a související přílohy. K databázi jsou také přiřazeni uživatelé. Každý má sadu oprávnění pro přístup k různým kolekcím, uloženým procedurám, triggerům, funkcím UDF, dokumentům a přílohám. Databáze, uživatelé, oprávnění a kolekce jsou systémem definované prostředky s dobře známými schématy – dokumenty, uložené procedury, triggery, funkce UDF a přílohy obsahují libovolný, uživatelem definovaný obsah JSON.  

## <a name="develop"></a>Jak můžu vyvíjet aplikace s rozhraním SQL API?

Azure Cosmos DB zveřejňuje prostředky přes rozhraní REST API, které je možné volat v jakémkoli jazyce schopném zasílat požadavky HTTP/HTTPS. Kromě toho pro rozhraní SQL API nabízíme programovací knihovny pro řadu oblíbených jazyků. Tyto klientské knihovny zjednodušují mnoho aspektů práce s tímto rozhraním API. Řeší detaily, jako je například ukládání adres do mezipaměti, správa výjimek, automatické opakované pokusy apod. Knihovny jsou v tuto chvíli k dispozici pro následující jazyky a platformy:  

| Ke stažení | Dokumentace |
| --- | --- |
| [.NET SDK](https://go.microsoft.com/fwlink/?LinkID=402989) |[Referenční dokumenty pro .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [Java SDK](https://go.microsoft.com/fwlink/?LinkID=402380) |[Referenční dokumenty pro Javu](/java/api/com.microsoft.azure.documentdb) |
| [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) |[Referenční dokumenty pro JavaScript](https://docs.microsoft.com/javascript/api/@azure/cosmos/?view=azure-node-latest) |
| [Python SDK](https://pypi.python.org/pypi/pydocumentdb) |[Referenční dokumenty pro Python](https://github.com/Azure/azure-cosmos-python) |

Pomocí [emulátoru služby Azure Cosmos DB](local-emulator.md) můžete vyvíjet a testovat aplikace místně s použitím rozhraní SQL API bez vytváření předplatného Azure a bez jakýchkoli nákladů. Jakmile budete spokojeni s fungováním aplikace v emulátoru, můžete přejít na účet služby Azure Cosmos DB v cloudu.

Kromě základních operací vytvoření, čtení, aktualizace a odstranění nabízí rozhraní SQL API také bohaté rozhraní příkazů jazyka SQL k načítání dokumentů JSON a podporu spouštění logiky aplikací JavaScript z transakcí na straně serveru. Rozhraní pro spouštění dotazů a skriptů jsou k dispozici prostřednictvím všech knihoven platforem i rozhraní REST API. 

### <a name="sql-query"></a>Dotaz SQL
Azure Cosmos DB podporuje dotazování dokumentů pomocí jazyka SQL, který je integrovaný do systému typů JavaScript, a výrazy s podporou relačních, hierarchických a prostorových dotazů. Dotazovací jazyk služby Azure Cosmos DB je jednoduché, ale výkonné rozhraní pro dotazování dokumentů JSON. Tento jazyk podporuje podmnožinu gramatiky ANSI SQL a přidává těsnou integraci s javascriptovými objekty, poli, vytvářením objektů a voláním funkcí. Rozhraní SQL API poskytuje svůj model dotazování bez explicitního schématu nebo parametrů indexování od vývojáře.

V rozhraní SQL API je možné zaregistrovat uživatelem definované funkce (UDF) a odkazovat na ně jako součást příkazu jazyka SQL. Tím se gramatika rozšiřuje o podporu vlastní logiky aplikace. Tyto funkce se píší jako javascriptové programy a spouští se uvnitř databáze. 

Sada SQL API [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) navíc pro vývojáře v rozhraní .NET nabízí poskytovatele dotazů jazyka LINQ. 

### <a name="transactions-and-javascript-execution"></a>Transakce a spouštění JavaScriptu
Rozhraní SQL API umožňuje psát logiku aplikace jako pojmenované programy vytvořené zcela v JavaScriptu. Tyto programy se registrují ke kolekci a s dokumenty v dané kolekci můžou provádět databázové operace. JavaScript je možné zaregistrovat ke spouštění jako trigger, uloženou proceduru nebo uživatelem definovanou funkci. Triggery a uložené procedury mohou vytvářet, číst, aktualizovat a odstraňovat dokumenty, zatímco uživatelem definované funkce se mohou spouštět jako součást logiky provádění dotazu bez přístupu pro zápis do kolekce.

Spouštění jazyka JavaScript v rámci služby Cosmos DB je modelováno podle konceptů podporovaných relačními databázovými systémy. JavaScript zde slouží jako moderní náhrada jazyka Transact-SQL. Všechna logika JavaScriptu se spouští v ambientní transakci ACID s izolací snímku. Pokud během spouštění JavaScript vyvolá výjimku, je celá transakce zrušena.

## <a name="next-steps"></a>Další kroky
Máte už účet Azure? Pak můžete se službou Azure Cosmos DB začít pomocí našich [Rychlých startů](../cosmos-db/create-sql-api-dotnet.md), které vás provedou vytvořením účtu a začátky práce se službou Cosmos DB.

[1]: ./media/sql-api-introduction/json-database-resources1.png

