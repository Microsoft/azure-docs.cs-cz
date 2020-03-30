---
title: Asynchronní aktualizace pro modely služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Popisuje, jak používat rozhraní REST služby Azure Analysis Services ke kódu asynchronní aktualizace dat modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6457f062a40e60a491220fcf977585e8b07445b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273724"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynchronní aktualizace s využitím rozhraní REST API

Pomocí libovolného programovacího jazyka, který podporuje volání REST, můžete provádět operace asynchronní aktualizace dat na tabulkových modelech služby Azure Analysis Services. To zahrnuje synchronizaci replik jen pro čtení pro horizontální navýšení kapacity dotazu. 

Operace aktualizace dat může trvat nějakou dobu v závislosti na řadě faktorů, včetně objemu dat, úrovně optimalizace pomocí oddílů atd. Tyto operace byly tradičně vyvolány s existujícími metodami, jako je například použití [TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (tabulkový objektový model), rutiny [Prostředí PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) nebo [TMSL](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (jazyk skriptování tabulkových modelů). Tyto metody však mohou vyžadovat často nespolehlivé, dlouhotrvající připojení HTTP.

Rozhraní REST API pro Azure Analysis Services umožňuje operace aktualizace dat provádět asynchronně. Při použití rozhraní REST API nejsou dlouho běžící připojení HTTP z klientských aplikací nutné. Existují také další předdefinované funkce pro spolehlivost, jako jsou automatické opakování a dávkové potvrzení.

## <a name="base-url"></a>Základní adresa URL

Základní adresa URL se řídí tímto formátem:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Zvažte například model s názvem AdventureWorks na serveru s názvem `myserver`, který se nachází v oblasti Azure západní USA. Název serveru je:

```
asazure://westus.asazure.windows.net/myserver 
```

Základní adresa URL tohoto názvu serveru je:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Pomocí základní adresy URL lze připojit prostředky a operace na základě následujících parametrů: 

![Asynchronní aktualizace](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Všechno, co končí v **s,** je sbírka.
- Cokoliv, co končí **()** je funkce.
- Cokoli jiného je prostředek/objekt.

Můžete například použít příkaz POST v kolekci Refreshs k provedení operace aktualizace:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Ověřování

Všechna volání musí být ověřena pomocí platného tokenu služby Azure Active Directory (OAuth 2) v hlavičce autorizace a musí splňovat následující požadavky:

- Token musí být token uživatele nebo instanční objekt služby aplikace.
- Token musí mít správnou cílovou skupinu nastavenou na `https://*.asazure.windows.net`.
- Uživatel nebo aplikace musí mít dostatečná oprávnění na serveru nebo modelu, aby požadované volání. Úroveň oprávnění je určena rolemi v rámci modelu nebo skupiny správců na serveru.

    > [!IMPORTANT]
    > V současné době jsou nezbytná oprávnění role **správce serveru.**

## <a name="post-refreshes"></a>POST /aktualizace

Chcete-li provést operaci aktualizace, použijte příkaz POST v kolekci /refreshes k přidání nové položky aktualizace do kolekce. Hlavička Umístění v odpovědi obsahuje ID aktualizace. Klientská aplikace můžete odpojit a zkontrolovat stav později v případě potřeby, protože je asynchronní.

Pro model je přijata pouze jedna operace aktualizace. Pokud existuje aktuální spuštěná operace aktualizace a je odeslána jiná, je vrácen stavový kód 409 Conflict HTTP.

Tělo se může podobat následujícímu:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parametry

Zadání parametrů není vyžadováno. Použije se výchozí nastavení.

| Name (Název)             | Typ  | Popis  |Výchozí  |
|------------------|-------|--------------|---------|
| `Type`           | Výčet  | Typ zpracování, které má být provést. Typy jsou zarovnány s typy [příkazů aktualizace](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl) TMSL: úplné, clearValues, vypočítat, dataOnly, automatické a defragmentace. Typ přidání není podporován.      |   automatická      |
| `CommitMode`     | Výčet  | Určuje, zda budou objekty potvrzeny v dávkách nebo pouze po dokončení. Režimy zahrnují: výchozí, transakční, partialBatch.  |  Transakční       |
| `MaxParallelism` | Int   | Tato hodnota určuje maximální počet podprocesů, na kterých chcete paralelně spouštět příkazy pro zpracování. Tato hodnota je zarovnána s vlastností MaxParallelism, kterou lze nastavit v [příkazu](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl) TMSL Sequence nebo pomocí jiných metod.       | 10        |
| `RetryCount`     | Int   | Označuje, kolikrát se operace bude opakovat před selháním.      |     0    |
| `Objects`        | Pole | Pole objektů, které mají být zpracovány. Každý objekt obsahuje: "tabulka" při zpracování celé tabulky nebo "tabulka" a "oddíl" při zpracování oddílu. Pokud nejsou zadány žádné objekty, aktualizuje se celý model. |   Zpracování celého modelu      |

CommitMode se rovná partialBatch. Používá se při počátečním zatížení velkých datových sad, které mohou trvat hodiny. Pokud se operace aktualizace nezdaří po úspěšném potvrzení jedné nebo více dávek, úspěšně potvrzené dávky zůstanou potvrzeny (nebude vrátit zpět úspěšně potvrzené dávky).

> [!NOTE]
> V době zápisu velikost dávky je maxparallelismus hodnotu, ale tato hodnota může změnit.

### <a name="status-values"></a>Hodnoty stavu

|Hodnota stavu  |Popis  |
|---------|---------|
|`notStarted`    |   Operace ještě nebyla zahájena.      |
|`inProgress`     |   Probíhá operace.      |
|`timedOut`     |    Časový režim operace byl vypován na základě časového parametru zadaného uživatelem.     |
|`cancelled`     |   Operace byla zrušena uživatelem nebo systémem.      |
|`failed`     |   Operace se nezdařila.      |
|`succeeded`      |   Operace byla úspěšná.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Chcete-li zkontrolovat stav operace aktualizace, použijte příkaz GET na ID aktualizace. Zde je příklad těla odezvy. Pokud operace probíhá, `inProgress` je vrácena ve stavu.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /aktualizuje

Chcete-li získat seznam operací historické aktualizace pro model, použijte get sloveso na /refreshes kolekce. Zde je příklad těla odezvy. 

> [!NOTE]
> V době zápisu jsou uloženy a vráceny poslední 30 dny operací aktualizace, ale toto číslo se může změnit.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Chcete-li zrušit probíhající operaci aktualizace, použijte příkaz DELETE na ID aktualizace.

## <a name="post-sync"></a>POST /sync

Po provedení operací aktualizace může být nutné synchronizovat nová data s replikami pro horizontální navýšení kapacity dotazu. Chcete-li provést operaci synchronizace pro model, použijte příkaz POST ve funkci /sync. Hlavička Umístění v odpovědi obsahuje ID operace synchronizace.

## <a name="get-sync-status"></a>GET /sync stav

Chcete-li zkontrolovat stav operace synchronizace, použijte příkaz GET předání ID operace jako parametr. Zde je příklad těla odezvy:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Hodnoty `syncstate`pro :

- 0: Replikace. Soubory databáze jsou replikovány do cílové složky.
- 1: Rehydratace. Databáze je rehydratována na instanci serveru jen pro čtení.
- 2: Dokončeno. Operace synchronizace byla úspěšně dokončena.
- 3: Nepodařilo se. Operace synchronizace se nezdařila.
- 4: Dokončování. Operace synchronizace byla dokončena, ale provádí kroky vyčištění.

## <a name="code-sample"></a>Ukázka kódu

Tady je ukázka kódu C#, která vám pomůže začít, [RestApiSample na GitHubu](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Použití ukázky kódu

1.  Klonujte nebo stáhněte repo. Otevřete řešení RestApiSample.
2.  Najděte **klienta linky. BaseAddress = ...** a zadejte [základní adresu URL](#base-url).

Ukázka kódu používá ověřování [instančního objektu.](#service-principal)

### <a name="service-principal"></a>Instanční objekt

Další informace o nastavení instančního objektu a přiřazení potřebných oprávnění v Azure AS najdete v tématu [Vytvoření instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md) – portál Azure portal a [Přidání instančního objektu do role správce serveru.](analysis-services-addservprinc-admins.md) Po dokončení kroků proveďte následující další kroky:

1.  V ukázce kódu vyhledejte **autoritu řetězce = ...**, nahraďte **společné** ID klienta vaší organizace.
2.  Komentář/uncomment tak ClientCredential třídy se používá k vytvoření instance objektu cred. Ujistěte \<se, že \<id aplikace> a klíč aplikace> hodnoty jsou přístupné bezpečným způsobem nebo použít ověřování na základě certifikátu pro instanční objekty.
3.  Spusťte ukázku.


## <a name="see-also"></a>Viz také

[Vzorky](analysis-services-samples.md)   
[ROZHRANÍ API PRO ODPOČINEK](https://docs.microsoft.com/rest/api/analysisservices/servers)   


