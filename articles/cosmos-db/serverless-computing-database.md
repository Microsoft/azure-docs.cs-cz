---
title: Výpočet databáze bez serveru s Azure Cosmos DB a funkcemi Azure
description: Zjistěte, jak lze azure cosmos DB a funkce Azure společně vytvářet výpočetní aplikace bez serveru založené na událostech.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 079c246f87bb8294f3c7ad6dea3391f5c67ba0ad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985248"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Výpočetní výpočetní služby bez serveru pomocí Azure Cosmos DB a Azure Functions

Serverless computing je o schopnosti zaměřit se na jednotlivé části logiky, které jsou opakovatelné a bezstavové. Tyto části nevyžadují žádnou správu infrastruktury a spotřebovávají prostředky pouze pro sekundy nebo milisekundy, pro které jsou spuštěny. Jádrem výpočetního pohybu bez serveru jsou funkce, které jsou dostupné v ekosystému Azure pomocí [Azure Functions](https://azure.microsoft.com/services/functions). Další informace o dalších prostředích bez serveru v Azure najdete na stránce [Azure bez serveru.](https://azure.microsoft.com/solutions/serverless/) 

Díky nativní integraci mezi [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) a Azure Functions můžete vytvářet aktivační události databáze, vstupní vazby a výstupní vazby přímo z vašeho účtu Azure Cosmos DB. Pomocí Azure Functions a Azure Cosmos DB můžete vytvářet a nasazovat aplikace bez serveru řízené událostmi s přístupem s nízkou latencí k bohatým datům pro globální uživatelskou základnu.

## <a name="overview"></a>Přehled

Azure Cosmos DB a Azure Functions vám umožňují integrovat databáze a aplikace bez serveru následujícími způsoby:

* Vytvořte aktivační událost založené na událostech **Azure Functions pro Cosmos DB**. Tato aktivační událost závisí na datových [proudech kanálu změn](change-feed.md) pro monitorování kontejneru Azure Cosmos pro změny. Při jakékoli změny v kontejneru, datový proud kanálu změn je odeslána na aktivační událost, která vyvolá funkce Azure.
* Případně můžete vytvořit vazbu funkce Azure na kontejner Azure Cosmos pomocí **vstupní vazby**. Vstupní vazby číst data z kontejneru při spuštění funkce.
* Svázat funkci na kontejner Azure Cosmos pomocí **výstupní vazby**. Výstupní vazby zápis dat do kontejneru po dokončení funkce.

> [!NOTE]
> V současné době Azure Functions aktivační události, vstupní vazby a výstupní vazby pro Cosmos DB jsou podporovány pro použití pouze s rozhraním SQL API. Pro všechny ostatní rozhraní API Azure Cosmos DB byste měli přistupovat k databázi z vaší funkce pomocí statického klienta pro vaše rozhraní API.


Následující diagram znázorňuje každou z těchto tří integrací: 

![Jak se integrují funkce Azure Cosmos DB a Azure](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Aktivační událost Azure Functions, vstupní vazba a výstupní vazba pro Azure Cosmos DB lze použít v následujících kombinacích:

* Aktivační událost Azure Functions pro Cosmos DB se dá použít s výstupní vazbou pro jiný kontejner Azure Cosmos. Poté, co funkce provede akci na položku v kanálu změn můžete napsat do jiného kontejneru (zápis do stejného kontejneru pochází by účinně vytvořit rekurzivní smyčky). Nebo můžete použít aktivační událost Azure Functions pro Cosmos DB efektivně migrovat všechny změněné položky z jednoho kontejneru do jiného kontejneru s použitím výstupní vazby.
* Vstupní vazby a výstupní vazby pro Azure Cosmos DB lze použít ve stejné funkci Azure. To funguje dobře v případech, kdy chcete najít určitá data se vstupní vazbou, upravit je ve funkci Azure a potom je po úpravě uložit do stejného kontejneru nebo do jiného kontejneru.
* Vstupní vazba pro kontejner Azure Cosmos lze použít ve stejné funkci jako aktivační událost Azure Functions pro Cosmos DB a lze použít s nebo bez výstupní vazby také. Pomocí této kombinace můžete použít aktuální informace o směně měn (vytahované se vstupní vazbou na kontejner výměny) na kanál změn nových objednávek ve službě nákupního košíku. Celkový aktualizovaný nákupní košík s použitím aktuálního převodu měny lze zapsat do třetího kontejneru pomocí výstupní vazby.

## <a name="use-cases"></a>Případy použití

Následující případy použití ukazují několik způsobů, jak můžete maximálně využít data Azure Cosmos DB – propojením dat s funkcemi Azure založenými na událostech.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Případ použití IoT – aktivační událost azure functions a výstupní vazba pro Cosmos DB

V implementacích IoT můžete vyvolat funkci, když se kontrolka kontrolního motoru zobrazí v připojeném autě.

**Provádění:** Použití aktivační a výstupní vazby Azure Functions pro Cosmos DB

1. **Aktivační událost Funkce Azure pro Cosmos DB** se používá k aktivaci událostí souvisejících s výstrahami vozu, jako je například kontrolka kontrolního modulu, která se rozsvítí v připojeném autě.
2. Když přijde kontrolka kontrolního modulu, data senzoru se odešlou do Azure Cosmos DB.
3. Azure Cosmos DB vytvoří nebo aktualizuje nové dokumenty dat senzoru, pak se tyto změny streamují do aktivační události Azure Functions pro Cosmos DB.
4. Aktivační událost je vyvolána při každé změně dat do sběru dat senzoru, protože všechny změny jsou přenášeny datovým proudem prostřednictvím kanálu změn.
5. Ve funkci se používá prahová podmínka pro odeslání dat ze senzorů do záručního oddělení.
6. Pokud je teplota také nad určitou hodnotou, je majiteli odeslána výstraha.
7. **Výstupní vazba** na funkci aktualizuje záznam vozu v jiném kontejneru Azure Cosmos pro ukládání informací o události kontrolního modulu.

Následující obrázek znázorňuje kód napsaný na portálu Azure pro tuto aktivační událost.

![Vytvoření aktivační události Azure Functions pro Cosmos DB na webu Azure Portal](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Případ finančního použití - aktivační událost časovače a vstupní vazba

Ve finančních implementacích můžete vyvolat funkci, když zůstatek bankovního účtu spadá pod určitou částku.

**Provádění:** Aktivační událost časovače se vstupní vazbou Azure Cosmos DB

1. Pomocí [aktivační události časovače](../azure-functions/functions-bindings-timer.md)můžete načíst informace o zůstatku bankovního účtu uložené v kontejneru Azure Cosmos v časových intervalech pomocí **vstupní vazby**.
2. Pokud je zůstatek pod prahovou hodnotou nízkého zůstatku nastavenou uživatelem, pak zůlej te nakterounou akcí z funkce Azure.
3. Výstupní vazba může být [sendgrid integrace,](../azure-functions/functions-bindings-sendgrid.md) která odešle e-mail z účtu služby na e-mailové adresy určené pro každý z účtů s nízkým zůstatkem.

Následující obrázky zobrazit kód na portálu Azure pro tento scénář.

![Soubor Index.js pro aktivační událost Časovač pro finanční scénář](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Soubor Run.csx pro aktivační událost časovače pro finanční scénář](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Případ použití her – aktivační a výstupní vazba Azure Functions pro Cosmos DB 

Při hraní her můžete při vytvoření nového uživatele vyhledat další uživatele, kteří je mohou znát, pomocí [rozhraní Azure Cosmos DB Gremlin API](graph-introduction.md). Výsledky pak můžete zapsat do [Azure Cosmos DB SQL database] pro snadné načtení.

**Provádění:** Použití aktivační a výstupní vazby Azure Functions pro Cosmos DB

1. Pomocí [databáze grafu](graph-introduction.md) Azure Cosmos DB k ukládání všech uživatelů můžete vytvořit novou funkci s aktivační událostí Azure Functions pro Cosmos DB. 
2. Vždy, když je vložen nový uživatel, funkce je vyvolána a výsledek je uložen pomocí **výstupní vazby**.
3. Funkce dotazuje databázi grafu k hledání všech uživatelů, které přímo souvisejí s novým uživatelem a vrátí tuto datovou sadu funkci.
4. Tato data se pak uloží v Azure Cosmos DB, které pak lze snadno načíst všechny front-end aplikace, která zobrazuje nového uživatele jejich připojení přátelé.

### <a name="retail-use-case---multiple-functions"></a>Případ použití maloobchodu – více funkcí

V maloobchodních implementacích, když uživatel přidá položku do svého košíku, máte nyní možnost vytvářet a vyvolávat funkce pro volitelné součásti obchodního kanálu.

**Provádění:** Více aktivačních událostí Azure pro Cosmos DB naslouchání jednomu kontejneru

1. Můžete vytvořit více funkcí Azure přidáním Azure Functions aktivačních událostí pro Cosmos DB do každého – všechny, které poslouchají stejné změny zdroj dat nákupního košíku. Všimněte si, že při více funkcí naslouchat stejné změny krmiva, je vyžadována nová kolekce zapůjčení pro každou funkci. Další informace o kolekcích zapůjčení naleznete [v tématu Principy knihovny kanálu změn](change-feed-processor.md).
2. Při každém přidání nové položky do nákupního košíku uživatelů je každá funkce nezávisle vyvolána zdrojem změn z kontejneru nákupního košíku.
   * Jedna funkce může použít obsah aktuálního košíku ke změně zobrazení dalších položek, které by mohluživatel zajímat.
   * Jiná funkce může aktualizovat součty zásob.
   * Jiná funkce může zasílat informace o zákaznících pro určité produkty marketingovému oddělení, které jim pošle propagační mailer. 

     Jakékoli oddělení můžete vytvořit Funkce Azure pro Cosmos DB nasloucháním kanálu změn a ujistěte se, že nebude zpozdit události zpracování kritické objednávky v procesu.

Ve všech těchto případech použití, protože funkce oddělila samotnou aplikaci, nemusíte neustále sčítat nové instance aplikace. Místo toho Azure Functions vytáčí jednotlivé funkce k dokončení diskrétní procesy podle potřeby.

## <a name="tooling"></a>Nástroje

Nativní integrace mezi Azure Cosmos DB a Azure Functions je dostupná na webu Azure Portal a ve Visual Studiu 2019.

* Na portálu Azure Functions můžete vytvořit aktivační událost. Pokyny pro rychlý start najdete [v tématu Vytvoření aktivační události Azure Functions pro Cosmos DB na webu Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md).
* Na portálu Azure Cosmos DB můžete přidat aktivační událost Azure Functions pro Cosmos DB do existující aplikace Azure Function ve stejné skupině prostředků.
* Ve Visual Studiu 2019 můžete aktivační událost vytvořit pomocí [nástrojů Azure Functions Tools](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Proč zvolit integraci Azure Functions pro práci bez serveru?

Funkce Azure poskytuje možnost vytvářet škálovatelné jednotky práce nebo stručné části logiky, které lze spustit na vyžádání, bez zřizování nebo správy infrastruktury. Pomocí Funkce Azure, nemusíte vytvářet plnohodnotnou aplikaci reagovat na změny v databázi Azure Cosmos, můžete vytvořit malé opakovaně použitelné funkce pro konkrétní úkoly. Kromě toho můžete také použít data Azure Cosmos DB jako vstup nebo výstup do funkce Azure v reakci na událost, jako jsou požadavky HTTP nebo časovaná aktivační událost.

Azure Cosmos DB je doporučená databáze pro vaši výpočetní architekturu bez serveru z následujících důvodů:

* **Okamžitý přístup ke všem vašim datům**: Máte podrobný přístup ke každé uložené hodnotě, protože Azure Cosmos DB [automaticky indexuje](index-policy.md) všechna data ve výchozím nastavení a tyto indexy okamžitě zpřístupní. To znamená, že můžete neustále dotazovat, aktualizovat a přidávat nové položky do databáze a mít okamžitý přístup prostřednictvím funkce Azure.

* **Bez schémat .** Azure Cosmos DB je bez schématu – takže je jedinečně schopný zpracovat jakýkoli výstup dat z funkce Azure. Tento přístup "zpracovat cokoli" usnadňuje vytvoření různých funkcí, které všechny výstup do Azure Cosmos DB.

* **Škálovatelná propustnost**. Propustnost lze škálovat nahoru a dolů okamžitě v Azure Cosmos DB. Pokud máte stovky nebo tisíce funkce dotazování a zápisu do stejného kontejneru, můžete vertikálně navýšit kapacitu [ru/s](request-units.md) pro zpracování zatížení. Všechny funkce mohou pracovat paralelně pomocí přidělených RU/s a vaše data jsou zaručena [konzistentní](consistency-levels.md).

* **Globální replikace**. Můžete replikovat data Azure Cosmos DB [po celém světě,](distribute-data-globally.md) abyste snížili latenci a geograficky lokalizovali data, která jsou nejblíže místu, kde jsou vaši uživatelé. Stejně jako u všech dotazů Azure Cosmos DB jsou data z aktivačních událostí založená na událostech čtení dat z azure cosmos db, která je uživateli nejblíže.

Pokud chcete integrovat s Funkcemi Azure pro ukládání dat a nepotřebujete hluboké indexování nebo pokud potřebujete ukládat přílohy a mediální soubory, [aktivační událost úložiště objektů blob Azure](../azure-functions/functions-bindings-storage-blob.md) může být lepší volbou.

Výhody funkcí Azure: 

* **Událostmi řízený**. Funkce Azure jsou řízené událostmi a můžou poslouchat kanál změn z Azure Cosmos DB. To znamená, že nemusíte vytvářet logiku naslouchání, stačí dávat pozor na změny, které posloucháte. 

* **Bez omezení**. Funkce se spouštějí paralelně a služba se otáčí tolik, kolik potřebujete. Nastavíte parametry.

* **Dobré pro rychlé úkoly**. Služba spouští nové instance funkcí vždy, když je událost vyvolána, a zavře je, jakmile se funkce dokončí. Platíte pouze za dobu, po kterou jsou vaše funkce spuštěny.

Pokud si nejste jistí, jestli je tok, logické aplikace, funkce Azure nebo webová úloha nejlepší pro vaši implementaci, přečtěte [si část Výběr mezi tokem, logickými aplikacemi, funkcemi a webovými úlohami](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Další kroky

Teď pojďme propojit Azure Cosmos DB a Azure Functions pro skutečné: 

* [Vytvoření aktivační události Azure Functions pro Cosmos DB na webu Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Vytvoření triggeru HTTP ve službě Azure Functions s využitím vstupní vazby Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Vazby a aktivační události Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md)