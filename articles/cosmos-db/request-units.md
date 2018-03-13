---
title: "Požadované jednotky & odhadnout propustnost - Azure Cosmos DB | Microsoft Docs"
description: "Další informace o tom, jak porozumět, zadejte a odhadnout požadavky na jednotky žádosti v Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mimig
ms.openlocfilehash: 3679aa76d4a6b9fd6335371e1639f1f246867fa5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Požadované jednotky v Azure Cosmos DB
Nyní k dispozici: Azure Cosmos DB [kalkulačky jednotek žádosti](https://www.documentdb.com/capacityplanner). Další informace v [odhadnout, musí vaše propustnost](request-units.md#estimating-throughput-needs).

![Propustnost kalkulačky][5]

## <a name="introduction"></a>Úvod
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je globálně distribuované databáze více modelu společnosti Microsoft. S Azure DB Cosmos nemáte pronajímat virtuálních počítačů, nasazení softwaru nebo monitorování databází. Azure Cosmos DB je provozována a průběžně monitorovat pomocí Microsoft nejvyšší technici k poskytování world třída data dostupnosti, výkonu a ochrany. Přistupujete k datům pomocí rozhraní API podle vaší volby, například [rozhraní SQL API](documentdb-introduction.md), [MongoDB rozhraní API](mongodb-introduction.md), [tabulky API](table-introduction.md)a Gremlin prostřednictvím [rozhraní Graph API](graph-introduction.md) - všechny nativně podporuje. Měna Azure Cosmos DB je jednotka žádosti (RU). S RUs není potřeba rezervovat kapacity pro čtení a zápis nebo přidělení procesoru, paměti a procesorů.

Azure Cosmos DB podporuje několik rozhraní API s různé operace, od jednoduchého čte a zapisuje do grafu komplexní dotazy. Vzhledem k tomu, že ne všechny požadavky jsou stejné, jsou přiřazeny normalizovaný objemu **požadované jednotky** založenou na velikosti výpočty potřebné k požadavek vyřídit. Počet jednotek žádosti operace je deterministická, a můžete sledovat počet jednotek žádosti spotřebovávají všechny operace v Azure Cosmos DB prostřednictvím hlavičky odpovědi. 

Zajistit předvídatelný výkon, budete muset rezervovat propustnost v jednotkách 100 RU za sekundu. 

Po přečtení tohoto článku, budete moct odpovězte si na následující otázky:  

* Jaké jsou požadované jednotky a požádat o poplatky?
* Jak určit kapacitu jednotky žádosti pro kontejner?
* Jak odhadnout, že je jednotka žádosti Moje aplikace?
* Co se stane, když I překročit kapacitu jednotky žádosti pro kontejner?

Jak Azure Cosmos DB je více modelu databáze, je důležité si uvědomit, že v tomto článku odkazuje na kolekci či dokumentu pro dokument rozhraní API, grafu nebo uzel pro graf rozhraní API a tabulka/entity pro rozhraní API tabulky. Tento článek se týká konceptu kolekce, grafu nebo tabulky jako kontejner a dokument, uzel nebo entity jako položku.

## <a name="request-units-and-request-charges"></a>Jednotek žádosti a poplatky požadavku
Azure Cosmos DB poskytuje rychlé, předvídatelný výkon pomocí *rezervování* prostředky pro uspokojení musí propustnost vaší aplikace.  Vzhledem k aplikaci načíst a přístup k vzory změny v čase, Azure Cosmos DB umožňuje snadno zvýšit nebo snížit množství vyhrazenou propustností, které jsou k dispozici pro aplikaci.

S Azure Cosmos databáze je zadána vyhrazenou propustností z hlediska jednotek žádosti zpracování za sekundu. Si můžete představit jednotek žádosti jako měnu propustnost, které jste *rezervovat* množství jednotek zaručenou žádosti, které jsou k dispozici pro aplikaci na základě za sekundu.  Každé operace v Azure DB Cosmos - zápis dokumentu, provádění dotazu, aktualizace dokumentu - spotřebuje procesoru, paměti a procesorů.  To znamená, každou operaci způsobuje *požadavku poplatků*, vyjádřeného v *požadované jednotky*.  Principy faktory, které mají vliv poplatky jednotek žádosti, společně s požadavky na propustnost vaší aplikace, umožňuje aplikaci spustit jako efektivně možné náklady. Průzkumníku dat na portálu Azure je také skvělý nástroj pro testování základní dotazu.

Doporučujeme začít následujícím videem, kde vysvětluje Aravind Ramachandran jednotek žádosti a předvídatelného výkonu s Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>Určení požadavku jednotka kapacity v Azure Cosmos DB
Při spouštění nový kontejner je zadat počet jednotek žádosti za sekundu (RU za sekundu), kterou chcete vyhrazené. Na základě zřízené propustnosti, Azure Cosmos DB přiděluje fyzické oddíly pro hostování vašeho kontejneru a rozdělení/rebalances dat napříč oddíly ho s růstem.

Kontejnery Azure Cosmos DB lze vytvořit jako pevný nebo neomezená. Kontejnery s pevnou velikostí mají omezení maximální velikosti 10 GB a propustnosti 10 000 RU/s. Chcete-li vytvořit kontejner neomezená musíte zadat minimální propustnost 1000 RU/s a [klíč oddílu](partition-data.md). Vzhledem k tomu, aby se daly rozdělit mezi více oddílů mohou mít vaše data, je nutné vybrat klíč oddílu, který má vysokou kardinalitou (100 na miliony odlišné hodnoty). Výběrem klíč oddílu s mnoha jedinečných hodnot je zajistit, že kontejner a tabulka/grafika a žádosti o je možné rozšířit jednotně pomocí Azure Cosmos DB. 

> [!NOTE]
> Klíč oddílu je logické hranice a není fyzický jeden. Proto není potřeba omezit počet hodnoty klíče jedinečné oddílu. Ve skutečnosti je lepší má více jedinečných hodnot klíče oddílu menší, než databázi Cosmos Azure má další možnosti vyrovnávání zatížení.

Zde je fragment kódu pro vytvoření kontejneru s 3 000 jednotek žádosti za druhé pomocí sady .NET SDK:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Azure Cosmos DB funguje na rezervace modelu na propustnost. To znamená, že se účtují pro množství propustnost *vyhrazené*, bez ohledu na to, kolik z této propustnost je aktivně *používá*. Jako vaše aplikace je zatížení, data a využití vzory změnu, je možné snadno škálovat nahoru a dolů množství vyhrazené RUs prostřednictvím sady SDK nebo pomocí [portálu Azure](https://portal.azure.com).

Každý kontejner je namapována na `Offer` prostředků v Azure DB Cosmos, který má metadata o zřízené propustnosti. Vyhledávání odpovídající prostředek nabídka pro kontejner a poté aktualizace pomocí novou hodnotu propustnosti, můžete změnit přidělené propustnost. Zde je fragment kódu pro změnu propustnost kontejner do 5 000 jednotek žádosti za druhé pomocí sady .NET SDK:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Neexistuje žádný vliv na dostupnost vaší kontejneru při změně propustnost. Nové vyhrazenou propustností je obvykle efektivní během několika sekund na použití nové propustnost.

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Izolace propustnost v globálně distribuované databáze

Při replikaci databáze k více než jedné oblasti Azure Cosmos DB poskytuje izolaci propustnost zajistit, aby využití RU v jedné oblasti neměla vliv RU využití v jiné oblasti. Například pokud zapsat data do jedné oblasti a čtení dat z jiné oblasti, RUs použít k provedení operace zápisu v oblasti A nepřebírají od RUs používat pro operace čtení v oblasti, které nejsou B. RUs rozdělit do oblasti, ve kterých jste nasadili. Každou oblast, ve kterém se replikují databáze má v plné výši RUs zřízený. Další informace o globální replikace najdete v tématu [distribuci dat globálně pomocí Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Aspekty jednotek žádosti
Při odhadování počet jednotek žádosti můžete vyhradit pro váš kontejner Azure Cosmos DB, je důležité vzít v úvahu následující proměnné:

* **Velikost položky**. Jak roste množství jednotek použití pro čtení nebo zápis, že data se taky zvýší.
* **Počet vlastností položky**. Za předpokladu, že výchozí indexování všech vlastností, jednotek použití k zápisu zvýšení dokumentu nebo uzel nebo entity jako zvyšuje počet vlastnost.
* **Konzistenci dat**. Při použití úrovně konzistence dat silného nebo typu s ohraničenou Prošlostí, používají se další jednotky pro čtení položek.
* **Indexované vlastnosti**. Zásadu indexu na každý kontejner určuje vlastnosti, které jsou uloženy ve výchozím nastavení. Omezení počtu indexované vlastnosti nebo povolením Opožděné indexování můžete snížit spotřebu jednotky vaší žádosti.
* **Indexování dokumentů**. Ve výchozím nastavení je každá položka automaticky indexovaný. Pokud se rozhodnete indexování některých položek spotřebujete méně jednotek žádosti.
* **Dotaz vzory**. Složitost dotazu má dopad na tom, kolik jednotek žádosti se spotřebovávají pro operace. Počet predikáty, povaha predikáty, projekce, počet UDF a velikost datové sady zdroje, které jsou všechny ovlivnit náklady na operace dotazů.
* **Použití skriptu**.  Stejně jako u dotazů, využívat jednotek žádosti podle složitosti operací během provádění uložené procedury a triggery. Když budete vyvíjet aplikace, zkontrolujte hlavičky požadavku poplatků abyste lépe pochopili, jak každou operaci spotřebovává požadavek jednotky kapacity.

## <a name="estimating-throughput-needs"></a>Odhad potřeb propustnost
Jednotka žádosti je normalizovaný míru náklady na zpracování požadavku. Jednotka jedné žádosti představuje kapacity zpracování požadovaná pro čtení (prostřednictvím id nebo vlastní odkaz) jeden 1 KB položky skládající se z 10 jedinečnou vlastnost hodnot (s výjimkou vlastnosti systému). Požadavek na vytvoření (Vložit), nahraďte nebo odstranění stejnou položku spotřebuje další zpracování ze služby a tím více jednotek žádosti.   

> [!NOTE]
> Vlastní odkaz nebo id položky odpovídá jednoduché GET účaří požadavků 1 jednotka pro položku 1 KB.
> 
> 

Například je zde tabulku, která zobrazí počet jednotek žádosti zřídit na tři různé položky velikosti (1 KB, 4 KB a 64 KB) a na dvou různých výkonu úrovních (500 čtení za sekundu + 100 zápisů za sekundu a 500 čtení za sekundu + 500 zápisů za sekundu). Konzistenci dat byl nakonfigurován v relaci a zásady indexování byla nastavena na hodnotu None.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Velikost položky</strong></p></td>
            <td valign="top"><p><strong>Čtení za sekundu</strong></p></td>
            <td valign="top"><p><strong>Zápisů za sekundu</strong></p></td>
            <td valign="top"><p><strong>Jednotky žádostí</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1 000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 kB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 kB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Použití kalkulačky jednotek žádosti
Na pomoc zákazníkům upřesnit jejich odhady propustnost, není webové [kalkulačky jednotek žádosti](https://www.documentdb.com/capacityplanner) ke zjištění přibližné hodnoty požadované žádosti jednotky pro typická operace, včetně:

* Vytvoří položku (zápisy)
* Čtení položky
* Odstranění položky
* Aktualizace položky

Nástroj zahrnuje taky podporu odhadnout požadavky na úložiště dat podle ukázkové položky, které zadáte.

Pomocí nástroje je jednoduchý:

1. Nahrajte jednu nebo více reprezentativní položek.
   
    ![Nahrání položky do kalkulačky jednotek žádosti][2]
2. Chcete-li odhadnout požadavky na úložiště dat, zadejte celkový počet položek, které chcete uložit.
3. Zadejte počet položek, které vytvářet, číst, aktualizovat a odstranit operations vyžadují (na základě za sekundu). K zjištění přibližné hodnoty poplatky jednotek žádosti operací aktualizace položky, nahrajte kopii ukázkové položky z kroku 1 výše, zahrnuje typické pole aktualizace.  Například pokud položku aktualizace obvykle upravit dvě vlastnosti s názvem lastLogin a userVisits, pak jednoduše zkopírovat ukázkové položky, aktualizujte hodnoty pro tyto dvě vlastnosti a nahrát kopírovaných položek.
   
    ![Zadejte požadavky na propustnost v kalkulačky jednotek žádosti][3]
4. Klikněte na tlačítko Vypočítat a podívejte se na výsledky.
   
    ![Žádosti o výsledky kalkulačky jednotky][4]

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti, nahrajte vzorek každého *typ* z typických položky do nástroje a potom vypočítat výsledky.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Použití hlavičku odpovědi Azure Cosmos DB požadavek zdarma
Každou odpověď ze služby Azure Cosmos DB obsahuje vlastní hlavičky (`x-ms-request-charge`) obsahující jednotek žádosti využité pro požadavek. Tuto hlavičku je také přístupné prostřednictvím sady SDK Azure Cosmos DB. V sadě SDK .NET je RequestCharge vlastnost ResourceResponse objektu.  Pro dotazy obsahuje Průzkumníku dat Azure Cosmos DB na portálu Azure požadavek poplatků informace o spuštění dotazů.

Myslete na to je záznam zřizování jednotky žádosti přidružené spuštěná typických operací proti položku reprezentativní používá vaše aplikace a pak odhadnout jedné metody odhadnout velikost vyhrazenou propustností požadované aplikací počet operací, které předpokládáte provádění každou sekundu.  Nezapomeňte měřit a zahrnují typické dotazy a také při použití skriptu Azure Cosmos DB.

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti, potom si poznamenejte poplatků jednotek žádosti příslušné operace spojené s každou *typ* typické položky.
> 
> 

Příklad:

1. Zaznamenejte poplatků jednotek žádosti o vytvoření (vkládání) typické položky. 
2. Záznam poplatků jednotek žádosti o čtení typické položky.
3. Záznam poplatků jednotek žádosti aktualizace typické položky.
4. Záznam poplatků jednotek žádosti typické, běžné položky dotazů.
5. Zaznamenejte poplatků jednotek žádosti vlastních skriptů (uložené procedury, triggery, funkce definované uživatelem), využít aplikací
6. Vypočítejte jednotky požadované žádosti dané odhadovaný počet operací, které předpokládáte spouštět každou sekundu.

## <a id="GetLastRequestStatistics"></a>Použití rozhraní API pro příkaz GetLastRequestStatistics pro MongoDB
Rozhraní API MongoDB podporuje vlastního příkazu *getLastRequestStatistics*, pro načítání poplatků požadavku pro zadané operace.

Například v prostředí Mongo provést operaci chcete ověřit žádost zdarma pro.
```
> db.sample.find()
```

Potom spusťte příkaz *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Myslete na to je záznam zřizování jednotky žádosti přidružené spuštěná typických operací proti položku reprezentativní používá vaše aplikace a pak odhadnout jedné metody odhadnout velikost vyhrazenou propustností požadované aplikací počet operací, které předpokládáte provádění každou sekundu.

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti, potom si poznamenejte poplatků jednotek žádosti příslušné operace spojené s každou *typ* typické položky.
> 
> 

## <a name="use-mongodb-api-portal-metrics"></a>Použití portálu metriky rozhraní API MongoDB
Nejjednodušší způsob, jak získat dobrý odhad požadavku poplatky jednotky pro vaši databázi MongoDB rozhraní API je použití [portál Azure](https://portal.azure.com) metriky. S *počet požadavků, které* a *požadavek poplatků* grafy, můžete získat odhad, kolik jednotek žádosti ze každý je náročné operace a kolik jednotek žádosti, které budou využívat relativně k jinému.

![Rozhraní API MongoDB portálu metriky][6]

## <a name="a-request-unit-estimation-example"></a>V příkladu odhad jednotek žádosti
Vezměte v úvahu následující ~ 1 KB dokumentu:

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
> Dokumenty jsou minifikovaný v Azure Cosmos DB, takže systém vypočítat velikost dokumentu výše je něco menší než 1 KB.
> 
> 

Následující tabulka uvádí přibližnou požadavek jednotky poplatky za typických operací u této položky (zdarma jednotky přibližnou požadavek předpokládá, že je úroveň konzistence účtu nastavená na "Relace" a automaticky indexování všech položek):

| Operace | Žádost o jednotky zdarma |
| --- | --- |
| Vytvoření položky |~15 RU |
| Čtení položky |~1 RU |
| Dotaz položky podle id |~2.5 RU |

Kromě toho tato tabulka ukazuje přibližnou požadavek poplatky jednotky pro typické dotazy použitou v aplikaci:

| Dotaz | Žádost o jednotky zdarma | počet vrácených položek |
| --- | --- | --- |
| Vyberte jídlo podle id |~2.5 RU |1 |
| Vyberte potravin podle výrobce |~7 RU |7 |
| Vyberte skupiny jídlo a pořadí podle váhy |~70 RU |100 |
| Vyberte nejvyšší 10 potravin ve skupině jídlo |~10 RU |10 |

> [!NOTE]
> Poplatky za RU lišit v závislosti na počet vrácených položek.
> 
> 

Pomocí těchto informací můžete odhadnout požadavky pro tuto aplikaci zadaný počet operací a dotazy očekávat za sekundu na RU:

| Operace nebo dotazu | Očekávaný počet za sekundu | Požadované RUs |
| --- | --- | --- |
| Vytvoření položky |10 |150 |
| Čtení položky |100 |100 |
| Vyberte potravin podle výrobce |25 |175 |
| Vyberte jídlo skupinou |10 |700 |
| Vyberte nejvyšší 10 |15 |Celkem 150 |

V takovém případě byste měli průměrnou propustností požadavek 1,275 RU/s.  Zaokrouhlení až nejbližší 100 by zřídit 1 300 RU/s pro tuto aplikaci kontejneru.

## <a id="RequestRateTooLarge"></a> Překročení omezení vyhrazenou propustností v Azure Cosmos DB
Odvolat, že spotřeba jednotek žádosti budou vyhodnocené jako za sekundu Pokud rozpočtu je prázdný. Pro aplikace, které překračují rychlost jednotky zřízené požadavků pro kontejner jsou omezené požadavky do tohoto kontejneru, dokud rychlost klesne pod úroveň vyhrazené. Když dojde omezení, server ho preventivně končí požadavek s RequestRateTooLargeException (kód stavu HTTP 429) a vrátí hlavičku x-ms opakování za ms určující dobu v milisekundách, která uživatel musí počkat před provedením nového pokusu požadavek.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Pokud používáte klienta SDK rozhraní .NET a LINQ dotazů a potom ve většině případů není nutné řešit výjimku, jako aktuální verze rozhraní .NET Client SDK implicitně zachytí této odpovědi, respektuje záhlaví zadaný server opakovat po a opakuje požadavek. Pokud váš účet je současně přistupuje více klientů, další pokus bude úspěšné.

Pokud máte více než jednoho klienta kumulativně operační vyšší rychlost požadavků nemusí stačit výchozí chování opakování a klient vyvolá výjimku DocumentClientException se stavovým kódem 429 k aplikaci. V případech, jako je tato zvažte zpracování logiky aplikace chyba zpracování rutiny nebo zvýšení vyhrazenou propustností kontejneru a postup pro opakované.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Překročení omezení vyhrazenou propustností v rozhraní API MongoDB
Aplikace, které překračují jednotek zřízené žádosti pro kontejner budou omezeny, dokud rychlost klesne pod úroveň vyhrazené. Když dojde omezení, back-end se ukončí ho preventivně požadavek s *16500* kód chyby - *příliš mnoho požadavků*. Ve výchozím nastavení, rozhraní API MongoDB automaticky opakovat až 10krát před vrácením *příliš mnoho požadavků* kód chyby. Pokud se zobrazuje řada *příliš mnoho požadavků* kódy chyb, můžete zvážit buď přidání opakování chování vaší aplikace chyba zpracování rutiny nebo [zvýšení vyhrazenou propustností kontejneru](set-throughput.md).

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou propustností s databázemi Azure Cosmos DB najdete v těchto zdrojích:

* [Azure Cosmos DB ceny](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Segmentace dat v Azure Cosmos DB](partition-data.md)

Další informace o databázi Cosmos Azure najdete v tématu Azure Cosmos DB [dokumentaci](https://azure.microsoft.com/documentation/services/cosmos-db/). 

Začínáme s škálování a výkon testování pomocí Azure Cosmos DB, najdete v tématu [testování výkonu a škálování s Azure Cosmos DB](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
[6]: ./media/request-units/api-for-mongodb-metrics.png
