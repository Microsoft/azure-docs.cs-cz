---
title: Výpočetní databáze bez serveru s Azure Cosmos DB a Azure Functions
description: Přečtěte si, jak Azure Cosmos DB a Azure Functions můžete použít společně k vytváření aplikací založených na událostech řízených serverem bez serveru.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 5264fb44f8088ae8f942abf95bc8c0ef6d917413
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096134"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Výpočetní databáze bez serveru s využitím Azure Cosmos DB a Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Computing bez serveru je vše, co se zaměřuje na jednotlivé kousky logiky, které jsou opakované a bezstavové. Tyto části nevyžadují správu infrastruktury a spotřebovávají prostředky jenom pro sekundy nebo milisekundy, které se spouštějí pro. V jádru přesunu bez serveru jsou funkce, které jsou k dispozici v ekosystému Azure, [Azure Functions](https://azure.microsoft.com/services/functions). Další informace o dalších prostředích pro spouštění bez serveru v Azure najdete v tématu bez [serveru na stránce Azure](https://azure.microsoft.com/solutions/serverless/) . 

Díky nativní integraci mezi [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) a Azure Functions můžete vytvořit triggery databáze, vstupní vazby a výstupní vazby přímo z účtu Azure Cosmos DB. Pomocí Azure Functions a Azure Cosmos DB můžete vytvářet a nasazovat aplikace bez serveru řízené událostmi s přístupem s nízkou latencí k bohatým datům pro globální uživatelskou základnu.

## <a name="overview"></a>Přehled

Azure Cosmos DB a Azure Functions vám umožní integrovat databáze a aplikace bez serveru následujícími způsoby:

* Vytvoří **aktivační událost Azure Functions** řízenou událostmi pro Cosmos DB. Tato aktivační událost spoléhá na změny v datových proudech [kanálu změn](change-feed.md) a monitoruje je. Při provedení jakékoli změny v kontejneru se datový proud kanálu změn pošle triggeru, který vyvolá funkci Azure.
* Případně můžete vytvořit vazbu funkce Azure s kontejnerem Azure Cosmos pomocí **vstupní vazby** . Vstupní vazby čtou data z kontejneru, když se funkce spustí.
* Navázání funkce na kontejner Azure Cosmos pomocí **výstupní vazby** . Výstupní vazby zapisují data do kontejneru při dokončení funkce.

> [!NOTE]
> V současné době se Azure Functions triggeru, vstupní vazby a výstupní vazby pro Cosmos DB podporují jenom pro použití s rozhraním SQL API. Pro všechna ostatní Azure Cosmos DB rozhraní API byste měli získat přístup k databázi z vaší funkce pomocí statického klienta pro vaše rozhraní API.


Následující diagram znázorňuje každou z těchto tří integrací: 

:::image type="content" source="./media/serverless-computing-database/cosmos-db-azure-functions-integration.png" alt-text="Způsob integrace Azure Cosmos DB a Azure Functions" border="false":::

Aktivační událost Azure Functions, vstupní vazba a výstupní vazba pro Azure Cosmos DB lze použít v následujících kombinacích:

* Aktivační událost Azure Functions pro Cosmos DB se dá použít s výstupní vazbou na jiný kontejner Azure Cosmos. Jakmile funkce provede akci s položkou v kanálu změn, můžete ji napsat do jiného kontejneru (zápis do stejného kontejneru, ze kterého pochází, by efektivně vytvořil rekurzivní smyčku). Nebo můžete použít Trigger Azure Functions pro Cosmos DB k efektivní migraci všech změněných položek z jednoho kontejneru do jiného, s použitím výstupní vazby.
* Vstupní vazby a výstupní vazby pro Azure Cosmos DB lze použít ve stejné funkci Azure Functions. Tato funkce dobře funguje v případech, kdy chcete najít určitá data se vstupní vazbou, upravit ji ve funkci Azure a pak ji po úpravě Uložit do stejného kontejneru nebo jiného kontejneru.
* Vstupní vazbu k Cosmos kontejneru Azure je možné použít ve stejné funkci jako Trigger Azure Functions pro Cosmos DB a dá se použít i bez výstupní vazby. Tuto kombinaci můžete použít k použití aktuálnosti informací o směnném kurzu (přidaných se vstupní vazbou na kontejner Exchange) do kanálu změn nových objednávek v rámci služby nákupního košíku. Aktualizovaný součet nákupního košíku s použitým aktuálním převodem měny lze zapsat do třetího kontejneru pomocí výstupní vazby.

## <a name="use-cases"></a>Případy použití

Následující případy použití ukazují několik způsobů, jak můžete využít data Azure Cosmos DB – propojením dat s Azure Functionsem řízeným událostmi.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Případ použití IoT – aktivační událost Azure Functions a výstupní vazba pro Cosmos DB

V implementacích IoT můžete funkci vyvolat, když se na připojeném automobilu zobrazuje světlo kontrolního modulu.

**Implementace:** Použití triggeru Azure Functions a výstupní vazby pro Cosmos DB

1. **Aktivační událost Azure Functions pro Cosmos DB** se používá ke spouštění událostí souvisejících s výstrahami na automobil, jako je například kontroler, který připravujeme v připojeném automobilu.
2. Když se objeví světlo kontrolního modulu, odesílají se do Azure Cosmos DB data snímače.
3. Azure Cosmos DB vytvoří nebo aktualizuje nové dokumenty s daty ze senzorů, pak tyto změny budou streamování do triggeru Azure Functions pro Cosmos DB.
4. Trigger se vyvolá při každé změně dat v kolekci dat senzoru, protože všechny změny se streamují prostřednictvím kanálu změn.
5. Prahová podmínka se používá ve funkci k odeslání dat senzoru do oddělení záruky.
6. Pokud je teplota zároveň větší než určitá hodnota, pošle se vlastníkovi také výstraha.
7. **Výstupní vazba** na funkci aktualizuje záznam auta v jiném kontejneru Azure Cosmos a uloží informace o události kontrolního modulu.

Následující obrázek ukazuje kód napsaný v Azure Portal pro tuto aktivační událost.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-trigger-portal.png" alt-text="Způsob integrace Azure Cosmos DB a Azure Functions":::

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Případ finančního použití – aktivační událost časovače a vstupní vazba

V případě finančních implementací můžete funkci vyvolat, když zůstatek bankovního účtu klesne pod určitou částku.

**Implementace:** Aktivační událost časovače s Azure Cosmos DB vstupní vazbou

1. Pomocí [triggeru časovače](../azure-functions/functions-bindings-timer.md)můžete načíst informace o zůstatku bankovního účtu uložené v kontejneru Azure Cosmos v časových intervalech pomocí **vstupní vazby** .
2. Pokud je zůstatek pod prahovou hodnotou nízkého zůstatku nastaveným uživatelem, postupujte podle akce z funkce Azure Functions.
3. Výstupní vazba může být [SendGrid Integration](../azure-functions/functions-bindings-sendgrid.md) , která odesílá e-maily z účtu služby na e-mailové adresy identifikované pro každý z účtů s nízkým zůstatkem.

Následující obrázky znázorňují kód v Azure Portal pro tento scénář.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png" alt-text="Způsob integrace Azure Cosmos DB a Azure Functions":::

:::image type="content" source="./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png" alt-text="Způsob integrace Azure Cosmos DB a Azure Functions":::

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Případ herního případu použití – aktivační událost Azure Functions a výstupní vazba pro Cosmos DB 

Při vytvoření nového uživatele můžete v herních prostředích vyhledat další uživatele, kteří je můžou znát pomocí [Azure Cosmos DB rozhraní Gremlin API](graph-introduction.md). Pak můžete výsledky zapsat do Azure Cosmos DB nebo databáze SQL pro snadné načtení.

**Implementace:** Použití triggeru Azure Functions a výstupní vazby pro Cosmos DB

1. Pomocí databáze Azure Cosmos DB [Graph](graph-introduction.md) pro ukládání všech uživatelů můžete vytvořit novou funkci s triggerem Azure Functions pro Cosmos DB. 
2. Vždy, když je vložen nový uživatel, je funkce vyvolána a výsledek je uložen pomocí **výstupní vazby** .
3. Funkce dotazuje databázi grafu, aby hledala všechny uživatele, kteří jsou přímo v souvislosti s novým uživatelem, a vrátí tuto datovou sadu do funkce.
4. Tato data se pak uloží do Azure Cosmos DB, kterou pak můžete snadno načíst pomocí libovolné aplikace front-endu, která zobrazuje nového uživatele s připojenými přáteli.

### <a name="retail-use-case---multiple-functions"></a>Případ maloobchodního použití – více funkcí

V maloobchodních implementacích, když uživatel přidá položku na svůj koš, teď máte flexibilitu při vytváření a vyvolávání funkcí pro volitelné komponenty obchodních kanálů.

**Implementace:** Vícenásobné triggery Azure Functions pro Cosmos DB naslouchání jednomu kontejneru

1. Můžete vytvořit více Azure Functions přidáním triggerů Azure Functions pro Cosmos DB každému – všem naslouchat stejnému informačnímu kanálu změny dat nákupního košíku. Všimněte si, že když více funkcí naslouchají stejnému informačnímu kanálu změn, je pro každou funkci nutná nová kolekce zapůjčení. Další informace o kolekcích zapůjčení najdete v tématu [Principy knihovny Change feed Processor](change-feed-processor.md).
2. Pokaždé, když se do nákupního košíku uživatelů přidá nová položka, každá funkce se nezávisle vyvolá pomocí kanálu změn z kontejneru nákupního košíku.
   * Jedna funkce může použít obsah aktuálního košíku ke změně zobrazení dalších položek, které může uživatel zajímat.
   * Souhrn inventáře může aktualizovat jiná funkce.
   * Jiná funkce může odesílat zákazníkům informace o určitých produktech do marketingového oddělení, který jim pošle propagační poštovní schránku. 

     Jakékoli oddělení může vytvořit Azure Functions pro Cosmos DB tím, že naslouchá kanálu změn a zajistěte, aby nezpozdily kritické události zpracování v procesu.

Ve všech těchto případech použití, protože funkce odvolala samotnou aplikaci, nemusíte všechny nové instance aplikace současně aktivovat. Místo toho Azure Functions jednotlivé funkce vytočí, aby bylo možné dokončit samostatné procesy podle potřeby.

## <a name="tooling"></a>Nástroje

Nativní integrace mezi Azure Cosmos DB a Azure Functions je k dispozici v Azure Portal a v aplikaci Visual Studio 2019.

* Na portálu Azure Functions můžete vytvořit Trigger. Pokyny k rychlému startu najdete [v tématu Vytvoření triggeru Azure Functions pro Cosmos DB v Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md).
* Na portálu Azure Cosmos DB můžete přidat Trigger Azure Functions pro Cosmos DB do existující aplikace funkce Azure ve stejné skupině prostředků.
* V aplikaci Visual Studio 2019 můžete vytvořit Trigger pomocí [nástrojů pro Azure Functions](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Proč zvolit Azure Functions Integration pro Computing bez serveru?

Azure Functions poskytuje možnost vytvářet škálovatelné pracovní jednotky nebo stručné části logiky, které je možné spouštět na vyžádání, aniž by bylo potřeba zřizovat nebo spravovat infrastrukturu. Pomocí Azure Functions nemusíte vytvářet plně plnohodnotnou aplikaci, která reaguje na změny ve vaší databázi Azure Cosmos, můžete vytvořit malé opakovaně použitelné funkce pro konkrétní úlohy. Kromě toho můžete použít Azure Cosmos DB data jako vstup nebo výstup do funkce Azure Functions v reakci na událost, jako je například požadavek HTTP nebo aktivační událost s časovým limitem.

Azure Cosmos DB je doporučená databáze pro architekturu bez serveru z následujících důvodů:

* **Okamžitý přístup ke všem datům** : máte podrobný přístup ke všem uloženým hodnotám, protože Azure Cosmos DB [automaticky indexuje](index-policy.md) všechna data ve výchozím nastavení a zpřístupňuje tyto indexy okamžitě. To znamená, že budete moct průběžně dotazovat, aktualizovat a přidávat nové položky do databáze a mít okamžitý přístup prostřednictvím Azure Functions.

* Bez **schématu** . Azure Cosmos DB je bez schématu, takže je jednoznačně možné zpracovat jakýkoliv výstup z funkce Azure Functions. Tento "popisovač" cokoli usnadňuje vytvoření nejrůznějších funkcí, které mají všechny výstupy Azure Cosmos DB.

* **Škálovatelná propustnost** . Propustnost se dá v Azure Cosmos DB okamžitě škálovat nahoru a dolů. Pokud máte stovky nebo tisíce funkcí dotazování a zápis do stejného kontejneru, můžete horizontální navýšení [kapacity pro zpracování](request-units.md) zátěže škálovat. Všechny funkce můžou pracovat paralelně s využitím přidělených RU/s a vaše data jsou zaručená jako [konzistentní](consistency-levels.md).

* **Globální replikace** . Můžete replikovat Azure Cosmos DB data po [celém světě](distribute-data-globally.md) , abyste snížili latenci a geograficky vyhledáváte data, která jsou nejblíže místu, kde jsou vaši uživatelé. Stejně jako u všech dotazů Azure Cosmos DB jsou data z triggerů řízených událostmi čtena data z Azure Cosmos DB nejblíže uživateli.

Pokud chcete provést integraci s Azure Functions pro ukládání dat a nepotřebujete obsáhlé indexování, nebo pokud potřebujete ukládat přílohy a mediální soubory, může být [aktivační událost Azure Blob Storage](../azure-functions/functions-bindings-storage-blob.md) lepší volbou.

Výhody Azure Functions: 

* Založené **na událostech** . Azure Functions jsou založené na událostech a můžou naslouchat kanálu změn z Azure Cosmos DB. To znamená, že nemusíte vytvářet naslouchací logiku, ale jenom si myslíte, že se jedná o změny, se kterými nasloucháte. 

* **Žádná omezení** . Funkce se spouštějí paralelně a služba se uvolní až kolik potřebujete. Nastavíte parametry.

* **Vhodné pro rychlé úlohy** . Služba ukončí nové instance funkcí pokaždé, když se událost aktivuje, jakmile se funkce dokončí. Platíte jenom za čas, kdy jsou vaše funkce spuštěné.

Pokud si nejste jistí, jestli je pro vaši implementaci nejvhodnější tok, Logic Apps, Azure Functions nebo WebJobs, přečtěte si téma [Výběr mezi flow, Logic Apps, functions a WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Další kroky

Nyní se Azure Cosmos DB a Azure Functions připojit k reálnému: 

* [Vytvoření triggeru Azure Functions pro Cosmos DB v Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Vytvoření triggeru HTTP ve službě Azure Functions s využitím vstupní vazby Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Azure Cosmos DB vazby a triggery](../azure-functions/functions-bindings-cosmosdb-v2.md)
