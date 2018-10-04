---
title: Jednotky žádostí a k odhadování propustnost – Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o tom, jak porozumět, zadejte a odhadnout požadavky jednotek žádosti ve službě Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: rimman
ms.openlocfilehash: 23a3e629e12e2a4d417757c9fef5db804bb72c9e
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248750"
---
# <a name="throughput-and-request-units-in-azure-cosmos-db"></a>Propustnost a jednotkách požadavků ve službě Azure Cosmos DB

Prostředky Azure Cosmos DB se účtují na základě zřízenou propustnost a úložiště. Azure Cosmos DB propustnost je vyjádřena z hlediska **jednotek žádostí za sekundu (RU/s)**. Azure Cosmos DB podporuje různá rozhraní API, které mají různé operace, od jednoduchého čte a zapisuje do grafu komplexní dotazy. Každý požadavek využívá jednotek žádosti na základě objemu výpočtů, které jsou zapotřebí pro zpracování požadavku. Počet jednotek žádostí pro operaci je deterministický. Můžete sledovat počet jednotky žádostí, které jsou všechny operace ve službě Azure Cosmos DB s použitím hlavičky odpovědi. Pokud chcete zajistit předvídatelný výkon, měli byste si rezervovat propustnost v jednotkách 100 RU/s. Vašim potřebám propustnosti můžete odhadnout s využitím služby Azure Cosmos DB [Kalkulačka jednotek žádosti](https://www.documentdb.com/capacityplanner).

Ve službě Azure Cosmos DB může zřídit propustnost v přírůstcích po dvou: 

1. **Kontejner Azure Cosmos DB:** propustnosti zřízené pro kontejner je vyhrazený pro tento konkrétní kontejner. Při přiřazování throughput(RU/s) na úrovni kontejneru, kontejnery lze vytvořit jako **Fixed** nebo **Unlimited**. 

  Kontejnery s pevnou velikostí mají omezení maximální propustnost 10 000 RU/s a 10 GB limit úložiště. Pokud chcete vytvořit neomezený kontejner, je nutné zadat minimální propustnost 1 000 RU/s a [klíč oddílu](partition-data.md). Vzhledem k tomu, že vaše data mohou rozdělit mezi několik oddílů, budete muset vybrat klíč oddílu, který má vysokou kardinalitou (100 milionům jedinečné hodnoty). Tak, že vyberete klíče oddílu s mnoha různých hodnot, služby Azure Cosmos DB zajišťuje, že požadavky na kolekce, tabulky a grafu se upraví rovnoměrně. 

2. **Databáze Azure Cosmos DB:** propustnosti zřízené pro databázi je sdílen mezi všechny kontejnery v rámci této databáze. Při zřizování propustnosti na úrovni databáze, můžete explicitně vyloučit určité kontejnery a místo toho zajištění propustnosti u těchto kontejnerů na úrovni kontejneru. Úroveň propustnosti databáze vyžaduje všechny kolekce se vytvoří s klíčem oddílu. Při přiřazování propustnosti na úrovni databáze, kontejnery, které patří do této databáze by měl vytvořit s klíčem oddílu, protože je každá kolekce **Unlimited** kontejneru.  

Na základě zřízené propustnosti, Azure Cosmos DB se přidělí fyzické oddíly k hostování kontejnerů a rozdělení dat napříč oddíly, jak rostou. Následující obrázek ukazuje zřizování propustnosti na různých úrovních:

  ![Zřizování jednotek žádostí pro jednotlivé kontejnery a sadu kontejnerů](./media/request-units/provisioning_set_containers.png)

> [!NOTE] 
> Zřizování propustnosti na úrovni databáze a úrovni kontejneru jsou samostatné nabídky a přepínání mezi některý z těchto vyžadují migraci dat ze zdroje do cíle. Což znamená, že budete muset vytvořit novou databázi nebo nové kolekce a potom migrovat data s využitím [hromadné prováděcí modul knihovny](bulk-executor-overview.md) nebo [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

## <a name="request-units-and-request-charges"></a>Jednotky žádostí a poplatky za žádost

Azure Cosmos DB poskytuje vysoký a předvídatelný výkon tím, že rezervuje prostředky, aby splnila požadavky vaší aplikace na propustnost. Vzory zatížení aplikací a přístupu k nim se v průběhu času mění. Azure Cosmos DB vám může pomoct snadno zvýšit nebo snížit rezervovanou propustnost, kterou má vaše aplikace k dispozici.

Pomocí služby Azure Cosmos DB rezervované propustnosti specifikované jako jednotky žádosti, které zpracovává za sekundu. Jednotky žádostí můžete představit jako měnu propustnost. Které si vyhradíte počet jednotek zaručené žádosti k dispozici pro vaši aplikaci na základě za sekundu. Každá operace ve službě Azure Cosmos DB, včetně zápis dokumentu, provádění dotazu a aktualizuje se dokument, zpracuje procesoru, paměti a vstupně-výstupních operací. To znamená každé operace se účtují poplatky žádosti, která je vyjádřena v jednotkách požadavků. Až porozumíte faktorů ovlivňujících za jednotky žádosti a požadavkům na propustnost vaší aplikace, můžete spustit aplikaci jako nákladů efektivní co nejlépe. 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Izolace propustnost v globálně distribuované databáze

Pokud replikovat vaši databázi do více než jedné oblasti Azure Cosmos DB poskytuje izolaci propustnost pro zajištění, že využití jednotek žádosti v jedné oblasti nemá vliv na využití jednotek žádosti v jiné oblasti. Například pokud zápis dat do jedné oblasti a čtení dat z jiné oblasti, jednotkách požadavků, které se používají k provedení operace zápisu v oblasti A nevyřídí od jednotkách požadavků, které se používají pro operace čtení v oblasti žádosti o služby serveru B. jednotek nejsou rozdělené acro ss oblasti, ve kterých jste nasadili vaší databáze. Každá oblast, ve které se replikují do databáze má úplné číslo zřízené jednotky žádostí. Další informace o globální replikaci najdete v tématu [globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Aspekty jednotek žádosti
Při stanovení počtu jednotek požadavku na zřízení funkce, je důležité vzít v úvahu následující proměnné:

* **Velikost položky**. Jak se zvyšuje velikost, počet jednotek žádostí využité ke čtení nebo zápis dat také zvyšuje.
* **Počet vlastností položky**. Za předpokladu, že výchozí indexování všech vlastností, jednotky využité pro zápis dokumentu, uzel nebo entity zvýšení jako zvýší počet vlastností.
* **Konzistence dat**. Při použití modelů konzistence dat jako jsou silná nebo omezené Neaktuálnosti další jednotky žádostí se spotřebuje ke čtení položky.
* **Indexované vlastnosti**. Zásady služby index na každý kontejner určuje vlastnosti, které jsou indexovány ve výchozím nastavení. Tím, že omezíte počet indexované vlastnosti nebo tím, že opožděná indexování můžete snížit spotřebu jednotek žádosti pro operace zápisu.
* **Indexování dokumentů**. Ve výchozím nastavení je automaticky indexováno každé položky. Pokud budete chtít neindexujte některé z položek aplikace využíváte menšího počtu jednotek žádostí.
* **Dotazování vzory**. Složitost dotazu má vliv na tom, kolik jednotek žádosti se spotřebovávají pro operaci. Počet výsledků dotazu, počet predikátů, povaze predikáty, číslo uživatelem definované funkce, velikost zdroje dat, a všechny projekce ovlivnit náklady na operace dotazů.
* **Skript použití**. Stejně jako u dotazů, využívat jednotky žádostí podle složitosti prováděných operací uložených procedur a aktivačních událostí. Při vývoji vaší aplikace, kontrolovat poplatek hlavičky požadavku pro lepší pochopení způsobu, jakým každou operaci využívá kapacitu jednotek žádosti.

## <a name="estimating-throughput-needs"></a>Odhad potřebám propustnosti
Jednotky žádosti je normalizované míra náklady na zpracování žádosti. Jeden požadavek jednotka představuje kapacitu zpracování, který má požadovaná pro čtení (prostřednictvím odkazu na sebe sama nebo ID) jednu položku 1KB, který se skládá z 10 jedinečných vlastnost hodnot (s výjimkou vlastnosti systému). Požadavek na vytvoření (Vložit), nahrazení nebo odstranění stejná položka využívá další zpracování ze služby a tím vyžaduje další jednotky žádostí. 

> [!NOTE]
> Základní jednotka 1 žádost pro 1 KB položku odpovídá jednoduché GET ve vlastní odkaz nebo ID položky.
> 
> 

Například tady je tabulka, která ukazuje, kolik jednotek požadavku na zřízení funkce pro položky s tři různé velikosti (1 KB, 4 KB a 64 KB) a na dvě různé úrovně výkonu (500 čtení za sekundu 100 zápisů za sekundu a 500 čtení za sekundu + 500 zápisů za sekundu). V tomto příkladu konzistence dat je nastavená na **relace**, a zásady indexování je nastavená na **žádný**.

| Velikost položky | Operace čtení za sekundu | Zápisů za sekundu | Jednotky žádostí
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1 000 RU/s
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3 000 RU/s
| 4 KB | 500 | 100 | (500 * 1.3) + (100 * 7) = rovnaly 1 350 RU/s
| 4 KB | 500 | 500 | (500 * 1.3) + (500 * 7) = 4,150 RU/s
| 64 kB | 500 | 100 | (500 * 10) + (100 * 48) = 9,800 RU/s
| 64 kB | 500 | 500 | (500 * 10) + (500 * 48) = 29,000 RU/s

### <a name="use-the-request-unit-calculator"></a>Použití kalkulačky jednotek žádosti
Vám usnadní vyladění vašich odhady propustnost, můžete použít webovou [Kalkulačka jednotek žádosti](https://www.documentdb.com/capacityplanner). Kalkulačce vám můžou pomoct odhadu požadavky jednotek žádosti o typických operací, včetně:

* Vytvoří položku (zápis)
* Čtení položky
* Odstraní položku
* Aktualizace položky

Nástroj také zahrnuje podporu pro odhad, požadavky na úložiště dat na základě vzorku položek, které zadáte.

Použití nástroje:

1. Nahrajte jednu nebo více reprezentativní položek (například dokument JSON ukázka).
   
    ![Nahrát položky ke kalkulačce jednotek žádosti][2]
2. Pokud chcete odhadnout požadavky na úložiště dat, zadejte celkový počet položek (například dokumenty, řádky nebo vrcholy), které očekáváte, že k uložení.
3. Zadejte počet vytvoření, čtení, aktualizace a operace odstranění, které potřebujete (na základě za sekundu). Pokud chcete odhadnout za jednotky žádosti položky aktualizačních operací, odešle kopii ukázkové položky z kroku 1, která obsahuje aktualizace typické pole. Například, pokud položka aktualizace obvykle upravit dvě vlastnosti s názvem *lastLogin* a *userVisits*, zkopírujte ukázkové položky, aktualizujte hodnoty pro tyto dvě vlastnosti a pak nahrajte zkopírovaný položky.
   
    ![Zadejte požadavky na propustnost v kalkulačce jednotek žádosti][3]
4. Vyberte **Calculate**a potom si prohlédněte výsledky.
   
    ![Výsledky Kalkulačka jednotek žádosti][4]

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti Nahrát ukázku pro každou z *typ* z typických položky do nástroje a potom vypočítat výsledky.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Použití hlavičky odpovědi poplatek za žádost o služby Azure Cosmos DB
Každou odpověď ze služby Azure Cosmos DB obsahuje vlastní hlavičky (`x-ms-request-charge`), který obsahuje jednotky žádostí využité pro daný požadavek. Této hlavičky také přístupné prostřednictvím se sadami SDK služby Azure Cosmos DB. V sadě .NET SDK **RequestCharge** je vlastnost **ResourceResponse** objektu. Pro dotazy Průzkumník dat Azure Cosmos DB na webu Azure Portal poskytuje informace poplatek za žádost pro spuštěné dotazy. Další informace o tom, jak získat a nastavení propustnosti s použitím různých rozhraní API pro více modelů najdete v článku [nastavení a zjištění propustnosti ve službě Azure Cosmos DB](set-throughput.md) článku.

Jedním ze způsobů pro odhad množství vyhrazenou propustností, které jsou požadovány příslušnou aplikací je k zaznamenání poplatek za jednotky žádosti o souvisejícím se spouštěním typických operací proti reprezentativní položky, který používá vaše aplikace. Potom odhadněte počet operací, které očekáváte, že pokud chcete provést každou sekundu. Nezapomeňte si také měření a zahrnují typické dotazy a skript využití služby Azure Cosmos DB.

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti záznamu zátěž jednotky žádostí příslušné operace spojené s jednotlivými *typ* typické položky.
> 
> 

Jedná se například kroky, které můžete chtít provést:

1. Záznam se poplatky za jednotky žádosti o vytvoření (vkládání) typickou položku. 
2. Záznam se poplatky za jednotky žádosti o čtení typické položky.
3. Záznam se poplatky za jednotky žádosti o aktualizaci typické položky.
4. Záznam zátěž jednotky žádostí dotazů na typické, běžné položky.
5. Zaznamenejte poplatek za jednotky žádosti vlastních skriptů (uložené procedury, aktivační události, uživatelem definované funkce), které aplikace používá.
6. Výpočet jednotek požadované žádosti o odhadovaný počet operací, které očekáváte, že ke spuštění každou sekundu.

## <a name="a-request-unit-estimate-example"></a>Jako příklad odhad jednotek žádosti
Vezměte v úvahu následující dokument, což je velikost přibližně 1 KB:

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
> Dokumenty jsou minifikovaný ve službě Azure Cosmos DB, takže velikost Vypočteno systémem dokumentu výše je o něco menší než 1 KB.
> 
> 

Následující tabulka uvádí přibližnou žádosti za jednotky pro běžné operace u této položky. (Zátěž jednotky přibližné žádostí se předpokládá, že úroveň konzistence účtu je nastavena na **relace** a že všechny položky jsou automaticky indexován.)

| Operace | Poplatek za jednotky žádosti |
| --- | --- |
| Vytvořit položku |~ 15 RU |
| Čtení položky |~ 1 RU |
| Dotaz položky podle ID |~2.5 RU |

Následující tabulka uvádí přibližnou žádosti za jednotky pro typické dotazy používaných v aplikaci:

| Dotaz | Poplatek za jednotky žádosti | počet vrácených položek |
| --- | --- | --- |
| Vyberte potravin podle ID |~2.5 RU |1 |
| Vyberte foods podle výrobce |~ 7 RU |7 |
| Vyberte potravin skupinou a pořadí podle váhy |~70 RU |100 |
| Vyberte hlavní 10 foods ve skupině potravin |~10 RU |10 |

> [!NOTE]
> Poplatky za jednotky žádosti lišit v závislosti na počtu vrácených položek.
> 
> 

Pomocí těchto informací můžete odhadnout požadavky jednotek žádosti pro tuto aplikaci počet operace a dotazy, které předpokládáte, za sekundu:

| Operace dotazů | Odhadovaný počet za sekundu | Požadované žádosti jednotky |
| --- | --- | --- |
| Vytvořit položku |10 |150 |
| Čtení položky |100 |100 |
| Vyberte foods podle výrobce |25 |175 |
| Vyberte skupiny potravin |10 |700 |
| Vyberte prvních 10 |15 |Celkem 150 |

V takovém případě můžete očekávat, že požadavek průměrnou propustností 1,275 RU/s. Zaokrouhlení až nejbližší 100 zřizují 1,300 RU/s pro tuto aplikaci kontejneru (nebo sadu kontejnerů).

## <a id="RequestRateTooLarge"></a> Překročení limitů vyhrazenou propustností, které ve službě Azure Cosmos DB
Spotřebu jednotek žádosti je vyhodnocován sazbou za sekundu. Pro aplikace, které překračují jednotka frekvence zřízené požadavků požadavky se míra časově omezený, dokud rychlost klesne pod úroveň zřízené propustnosti. Míra časově omezený při požadavku na serveru preventivně končí požadavek s `RequestRateTooLargeException` (kód stavu HTTP 429) a vrátí `x-ms-retry-after-ms` záhlaví. Záhlaví označuje množství času v milisekundách, které musí uživatel čekat před opakováním žádosti.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Pokud používáte .NET klientské sady SDK a LINQ dotazů, ve většině případů, nikdy máte vypořádat s touto výjimkou. Aktuální verze sady .NET SDK klienta implicitně zachycuje tuto odpověď, respektuje zadaný server hlavičkou retry-after a automaticky opakovat žádost o. Pokud váš účet je současně přistupuje více klientů, bude při dalším pokusu úspěšné.

Pokud máte více než jednoho klienta kumulativně provozní výše je frekvence požadavků, výchozí chování opakování může být nedostatečné a vyvolá klienta `DocumentClientException` se stavem kódu 429 do aplikace. V takových případech můžete chtít zvážit chování opakování a logiku zpracování chyb rutiny vaší aplikace nebo zvýšení propustnosti zřízené pro kontejneru (nebo sadu kontejnerů).

## <a name="next-steps"></a>Další postup
 
- Zjistěte, jak [nastavení a zjištění propustnosti ve službě Azure Cosmos DB](set-throughput.md) pomocí webu Azure portal a sady SDK.
- Další informace o [testování pomocí služby Azure Cosmos DB výkonu a škálování](performance-testing.md).
- Další informace o vyhrazenou propustností, které s databází Azure Cosmos DB najdete v tématu [ceny služby Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) a [dělení dat ve službě Azure Cosmos DB](partition-data.md).
- Další informace o službě Azure Cosmos DB najdete v tématu [dokumentace ke službě Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png


