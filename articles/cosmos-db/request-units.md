---
title: Požadované jednotky a odhadnout propustnost - Azure Cosmos DB | Microsoft Docs
description: Další informace o tom, jak porozumět, zadejte a odhadnout požadavky na jednotky žádosti v Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 9021d0c3f650d64480f2881508d456ce98beab2a
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36961931"
---
# <a name="request-units-in-azure-cosmos-db"></a>Požadované jednotky v Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je globálně distribuované multimodel databáze Microsoft. S Azure DB Cosmos nemáte pronajímat virtuálních počítačů, nasazení softwaru nebo monitorování databází. Azure Cosmos DB je provozována a průběžně monitorovat pomocí Microsoft nejvyšší technici k poskytování špičkových data dostupnosti, výkonu a ochrany. Máte přístup k datům pomocí rozhraní API podle vaší volby, jako je třeba [SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), a [tabulky](table-introduction.md) rozhraní API a graf prostřednictvím [Gremlin API](graph-introduction.md). Všechna rozhraní API jsou všechny nativně podporovány. 

Měna pro Azure Cosmos DB se *jednotka žádosti (RU)*. U jednotek žádosti nemusíte rezervovat kapacity pro čtení a zápis nebo přidělení procesoru, paměti a procesorů. Azure Cosmos DB podporuje různé rozhraní API, které mají různé operace, od jednoduchého čte a zapisuje na dotazy, komplexní grafu. Protože ne všechny požadavky jsou stejné, přiřazené požadavky normalizovaný počtu jednotek žádosti podle množství výpočty potřebné k požadavek vyřídit. Počet jednotek žádosti operace není deterministický. Můžete sledovat počet jednotek žádosti, které se spotřebovávají všechny operace v Azure Cosmos DB prostřednictvím hlavičky odpovědi. 

Zajistit předvídatelný výkon, rezervujte propustnost v jednotkách 100 RU za sekundu. Můžete [odhadnout, musí vaše propustnost](request-units.md#estimating-throughput-needs) pomocí Azure Cosmos DB [kalkulačky jednotek žádosti](https://www.documentdb.com/capacityplanner).

![Propustnost kalkulačky][5]

Po přečtení tohoto článku, budete moct odpovězte si na následující otázky:

* Co jsou jednotek žádosti a poplatky žádosti v Azure Cosmos DB?
* Jak určit kapacitu jednotky žádosti z kontejneru nebo sadu kontejnery v Azure Cosmos DB?
* Jak odhadnout, že je jednotka žádosti Moje aplikace?
* Co se stane, když I překročit kapacitu jednotky žádosti z kontejneru nebo sady kontejnerů v Azure Cosmos DB?

Azure Cosmos DB je multimodel databáze, a proto je důležité si uvědomit, že v tomto článku se vztahují na všechny datové modely a rozhraní API v Azure Cosmos DB. Tento článek používá obecné podmínky, jako je *kontejneru* k obecnému kolekce nebo grafu a *položky* k obecnému tabulky, dokument, uzel nebo entity.

## <a name="request-units-and-request-charges"></a>Jednotek žádosti a poplatky požadavku

Azure Cosmos DB rychlé a poskytuje předvídatelný výkon vyhrazením prostředky pro uspokojení propustnost potřebám vaší aplikace. Aplikace zatížení a přístup vzory časem změnit. Azure Cosmos DB můžete snadno zvýšit nebo snížit množství vyhrazenou propustností, které jsou k dispozici pro aplikaci.

S Azure Cosmos databáze je vyhrazenou propustností specifikované jako jednotky žádosti zpracování za sekundu. Jednotek žádosti si lze představit jako měnu propustnost. Můžete vyhradit počet jednotek žádosti zaručenou být k dispozici pro aplikaci na základě za sekundu. Každé operace v Azure DB Cosmos, včetně zápisu dokumentu, provádění dotazu a aktualizaci dokumentu se odebírá, procesoru, paměti a procesorů. To znamená každou operaci způsobuje požadavek poplatků, vyjádřené v jednotek žádosti. Až porozumíte faktory, které ovlivňují poplatky jednotek žádosti a požadavky na propustnost vaší aplikace, můžete spustit aplikace jako efektivně možné náklady. 

Chcete-li začít pracovat, popisuje Azure manažer programu DB Cosmos Andrew Liu jednotek žádosti v následujícím videu: <br /><br />

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Izolace propustnost v globálně distribuované databáze

Při replikaci databáze k více než jedné oblasti, Azure Cosmos DB poskytuje izolaci propustnost Ujistěte se, že využití jednotek žádosti v jedné oblasti nemá vliv využití jednotek žádosti v jiné oblasti. Například pokud zápisu dat na jednu oblast a čtení dat z jiné oblasti, jednotek žádosti, které jsou použity k provedení operace zápisu v oblasti A nemáte trvat od jednotek žádosti, které se používají pro operace čtení v oblasti B. žádosti nejsou jednotky rozdělení akro ss oblasti, ve kterých jste nasadili vaší databáze. Každou oblast, ve kterém se replikují databáze má úplné počet jednotek žádosti zřízený. Další informace o globální replikace najdete v tématu [distribuci dat globálně pomocí Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Aspekty jednotek žádosti
Když je odhad počtu jednotek žádosti a zajišťují, je potřeba vzít v úvahu následující proměnné:

* **Velikost položky**. Jak roste, také zvyšuje počet jednotek žádosti použití číst nebo zapisovat data.
* **Počet vlastností položky**. Za předpokladu, že výchozí indexování všech vlastností, jednotek použití k zápisu zvýšení dokument, uzel nebo entity jako zvyšuje počet vlastnost.
* **Konzistenci dat**. Při použití modelů konzistence dat jako silná nebo typu s ohraničenou Prošlostí jednotek další žádosti spotřebování ke čtení položek.
* **Indexované vlastnosti**. Zásadu indexu na každý kontejner určuje vlastnosti, které jsou uloženy ve výchozím nastavení. Vaší spotřeby jednotek žádosti pro operace zápisu můžete snížit počet indexované vlastnosti nebo povolením Opožděné indexování.
* **Indexování dokumentů**. Ve výchozím nastavení je automaticky indexovaný jednotlivých položek. Pokud zvolíte možnost Ne indexování některých položek spotřebujete méně jednotek žádosti.
* **Dotaz vzory**. Složitost dotazu ovlivňuje, kolik jednotek žádosti se spotřebovávají pro operace. Počet výsledků dotazu, počet predikáty, povaha predikáty, počet uživatelsky definované funkce, velikost zdroje dat, a všechny projekce ovlivnit náklady na operace dotazů.
* **Použití skriptu**. Stejně jako u dotazů, využívat jednotek žádosti podle složitosti operací během provádění uložené procedury a triggery. Když budete vyvíjet aplikace, zkontrolujte hlavičky požadavku poplatků abyste lépe pochopili, jak každou operaci spotřebovává požadavek jednotky kapacity.

## <a name="estimating-throughput-needs"></a>Odhad potřeb propustnost
Jednotka žádosti je normalizovaný míru náklady na zpracování požadavku. Jednotka jedné žádosti představuje kapacity procesoru, který má požadovaná pro čtení (prostřednictvím ID nebo vlastní odkaz) jednu položku 1 KB, který se skládá z 10 jedinečnou vlastnost hodnot (s výjimkou vlastnosti systému). Požadavek na vytvoření (Vložit), nahraďte nebo odstranit stejné položky spotřebovává další zpracování ze služby a tím vyžaduje další jednotek žádosti. 

> [!NOTE]
> Účaří požadavků 1 jednotka pro položku 1 KB odpovídá ID položky nebo jednoduchý GET ve vlastní odkaz.
> 
> 

Například je zde tabulku, která ukazuje, kolik jednotek žádosti a zajišťují pro položky s tři velikosti (1 KB, 4 KB a 64 KB) a na dvou různých výkonu úrovních (500 čtení za sekundu + 100 zápisů za sekundu a 500 čtení za sekundu + 500 zápisů za sekundu). V tomto příkladu konzistenci dat nastavena na **relace**, a zásady indexování je nastavena na **žádné**.

| Velikost položky | Čtení za sekundu | Zápisů za sekundu | Jednotky žádostí
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1 000 RU/s
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3000 RU/s
| 4 KB | 500 | 100 | (500 * 1,3) + (100 * 7) = 1,350 RU/s
| 4 KB | 500 | 500 | (500 * 1,3) + (500 * 7) = 4,150 RU/s
| 64 kB | 500 | 100 | (500 * 10) + (100 * 48) = 9,800 RU/s
| 64 kB | 500 | 500 | (500 * 10) + (500 * 48) = 29,000 RU/s


### <a name="use-the-request-unit-calculator"></a>Použití kalkulačky jednotek žádosti
Můžete doladit vaše odhady propustnosti, můžete použít webové [kalkulačky jednotek žádosti](https://www.documentdb.com/capacityplanner). Rozhraní kalkulačky může pomoci odhad požadavky jednotky žádosti pro typická operace, včetně:

* Vytvoří položku (zápisy)
* Čtení položky
* Odstranění položky
* Aktualizace položky

Nástroj zahrnuje taky podporu odhadnout požadavky na úložiště dat na základě ukázka položek, které zadáte.

Použití nástroje:

1. Nahrajte jednu nebo více reprezentativní položek (například dokument ukázka JSON).
   
    ![Nahrání položky do kalkulačky jednotek žádosti][2]
2. Chcete-li odhadnout požadavky na úložiště dat, zadejte celkový počet položek (například dokumenty, řádky nebo vrcholy), které se bude ukládat.
3. Zadejte počet vytvoření, čtení, aktualizace a odstranění operace, které budete potřebovat (na základě za sekundu). K zjištění přibližné hodnoty poplatky jednotek žádosti operací aktualizace položky, nahrajte kopii ukázkové položky z kroku 1, zahrnuje typické pole aktualizace. Například pokud položka aktualizace obvykle upravit dvě vlastnosti s názvem *lastLogin* a *userVisits*, zkopírujte ukázkové položky, aktualizujte hodnoty pro tyto dvě vlastnosti a pak nahrajte kopírovaných položek.
   
    ![Zadejte požadavky na propustnost v kalkulačky jednotek žádosti][3]
4. Vyberte **Calculate**a potom si prohlédněte výsledky.
   
    ![Žádosti o výsledky kalkulačky jednotky][4]

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti, odeslání vzorku jednotlivých *typ* z typických položky do nástroje a potom vypočítat výsledky.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Použití hlavičku odpovědi Azure Cosmos DB požadavek zdarma
Každou odpověď ze služby Azure Cosmos DB obsahuje vlastní hlavičky (`x-ms-request-charge`) obsahující jednotek žádosti využité pro daný požadavek. Tuto hlavičku můžete také přistupovat prostřednictvím sady SDK Azure Cosmos DB. V sadě SDK .NET **RequestCharge** je vlastnost **ResourceResponse** objektu. Pro dotazy obsahuje Průzkumníku dat Azure Cosmos DB na portálu Azure požadavek poplatků informace o spuštění dotazů. Další informace o tom, jak získat a propustnost sady pomocí různých více modelu rozhraní API najdete v části [nastavování a získávání propustnost v Azure Cosmos DB](set-throughput.md) článku.

Jednou z možností k odhadování množství vyhrazenou propustností požadované aplikací je záznam zřizování jednotky žádosti přidružené spuštěným typických operací pro reprezentativní položky, které používá vaše aplikace. Potom odhadněte počet operací, které předpokládáte provést každou sekundu. Nezapomeňte také měřit a zahrnují typické dotazy a použití skriptu Azure Cosmos DB.

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti, zaznamenejte poplatků jednotek žádosti příslušné operace spojené s každou *typ* typické položky.
> 
> 

Jedná se například kroky, které může trvat:

1. Zaznamenejte poplatků jednotek žádosti o vytvoření (vkládání) typické položky. 
2. Záznam poplatků jednotek žádosti o čtení typické položky.
3. Záznam poplatků jednotek žádosti aktualizace typické položky.
4. Záznam poplatků jednotek žádosti typické, běžné položky dotazů.
5. Zaznamenejte poplatků jednotek žádosti žádné vlastní skriptů (uložené procedury, triggery, funkce definované uživatelem), které aplikace používá.
6. Vypočítejte jednotky požadované žádosti dané odhadovaný počet operací, které předpokládáte spouštět každou sekundu.

## <a name="a-request-unit-estimate-example"></a>V příkladu odhad jednotek žádosti
Vezměte v úvahu následující dokumentu, což je velikost přibližně 1 KB:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Dokumenty jsou minifikovaný v Azure Cosmos DB, takže velikost Vypočteno systémem dokumentu výše je něco menší než 1 KB.
> 
> 

Následující tabulka uvádí přibližnou požadavek jednotky poplatky za typických operací u této položky. (Zdarma jednotky přibližnou požadavku se předpokládá, že úroveň konzistence účtu je nastavena na **relace** a že jsou všechny položky automaticky indexovány.)

| Operace | Žádost o jednotky zdarma |
| --- | --- |
| Vytvořit položku |~ 15 RU |
| Čtení položky |~ 1 RU |
| Dotaz položky podle ID |~2.5 RU |

Následující tabulka uvádí přibližnou požadavek poplatky jednotky pro typické dotazy použitou v aplikaci:

| Dotaz | Žádost o jednotky zdarma | počet vrácených položek |
| --- | --- | --- |
| Vyberte jídlo podle ID |~2.5 RU |1 |
| Vyberte potravin podle výrobce |~ 7 RU |7 |
| Vyberte skupiny jídlo a pořadí podle váhy |~70 RU |100 |
| Vyberte nejvyšší 10 potravin ve skupině jídlo |~10 RU |10 |

> [!NOTE]
> Požadavek jednotky poplatky lišit v závislosti na počet vrácených položek.
> 
> 

Pomocí těchto informací můžete odhadnout požadavky na jednotky požadavku pro tuto aplikaci zadaný počet operací a dotazy, které předpokládáte, za sekundu:

| Operace nebo dotazu | Očekávaný počet za sekundu | Jednotek požadované žádosti |
| --- | --- | --- |
| Vytvořit položku |10 |150 |
| Čtení položky |100 |100 |
| Vyberte potravin podle výrobce |25 |175 |
| Vyberte jídlo skupinou |10 |700 |
| Vyberte nejvyšší 10 |15 |Celkem 150 |

V takovém případě byste měli průměrnou propustností požadavek 1,275 RU za sekundu. Zaokrouhlení až nejbližší 100 by zřídit 1,300 RU za sekundu pro tuto aplikaci kontejneru (nebo sadu kontejnery).

## <a id="RequestRateTooLarge"></a> Překročení omezení vyhrazenou propustností v Azure Cosmos DB
Spotřeba jednotek žádosti se vyhodnotí na za sekundu. Požadavky jsou pro aplikace, které překračují rychlost jednotky zřízené požadavků, míra limited, dokud rychlost klesne pod úroveň zřízené propustnosti. Když je míra limited požadavek, server ho preventivně skončí požadavek s `RequestRateTooLargeException` (kód stavu HTTP 429) a vrátí `x-ms-retry-after-ms` záhlaví. Záhlaví určuje množství času v milisekundách, která uživatel musí čekat, než požadavek.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Pokud používáte klienta SDK rozhraní .NET a LINQ dotazů, ve většině případů, nikdy máte jak nakládat s výjimku. Aktuální verze rozhraní .NET Client SDK implicitně zachytí této odpovědi, respektuje záhlaví zadaný server opakovat po a automaticky opakovat žádost o. Pokud váš účet je současně přistupuje více klientů, další pokus bude úspěšné.

Pokud máte více než jednoho klienta kumulativně operační vyšší rychlost požadavků, výchozí chování opakování může být nedostatek a vyvolá klienta `DocumentClientException` stavem code 429 k aplikaci. V takových případech můžete chtít zvažte zpracování opakování chování a logiku rutiny zpracování chyb aplikace nebo zvýšit propustnost zřízené pro kontejner (nebo sadu kontejnery).

## <a name="next-steps"></a>Další postup
 
- Zjistěte, jak [nastavování a získávání propustnost v Azure Cosmos DB](set-throughput.md) pomocí portálu Azure a sady SDK.
- Další informace o [výkonu a možností škálování testování pomocí Azure Cosmos DB](performance-testing.md).
- Další informace o vyhrazenou propustností s databázemi Azure Cosmos DB najdete v tématu [ceny Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) a [segmentace dat v Azure Cosmos DB](partition-data.md).
- Další informace o databázi Cosmos Azure, najdete v článku [dokumentace Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

