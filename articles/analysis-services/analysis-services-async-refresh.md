---
title: Asynchronní aktualizace pro modely služby Azure Analysis Services | Dokumentace Microsoftu
description: Zjistěte, jak kód asynchronní aktualizace s použitím rozhraní REST API.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 63b64df457af5b7d3d2bd5901f73d89ccd3c913a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65506971"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynchronní aktualizace s využitím rozhraní REST API

S použitím programovací jazyk, který podporuje volání REST, můžete provádět operace asynchronní aktualizace dat na vašich tabulkových modelů služby Azure Analysis Services. To zahrnuje synchronizaci repliky jen pro čtení pro horizontální navýšení kapacity dotazu. 

Operace aktualizace dat může chvíli trvat v závislosti na řadě faktorů včetně objem dat, úroveň optimalizace pomocí oddíly atd. Tyto operace tradičně vyvolání s existující metody, jako je třeba použití [Petr](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (tabulkový objektový Model), [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) rutin, nebo [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (tabulkový Model Skriptovací jazyk). Tyto metody však může vyžadovat často nespolehlivé, dlouhotrvajících připojení prostřednictvím protokolu HTTP.

Rozhraní REST API pro Azure Analysis Services umožňuje operace aktualizace dat provádět asynchronně. Pomocí rozhraní REST API nejsou potřebné dlouhotrvajících připojení HTTP z klientských aplikací. Existují také jiné integrované funkce pro spolehlivost, jako je například automatické opakované pokusy a dávkové potvrzení změn.

## <a name="base-url"></a>Základní adresa URL

Základní adresa URL tento formát:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Představte si třeba modelu s názvem AdventureWorks na serveru s názvem myserver nachází v oblasti Azure USA – západ. Je název serveru:

```
asazure://westus.asazure.windows.net/myserver 
```

Základní adresa URL pro tento název serveru je:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

S použitím základní adresu URL, operace a prostředky lze připojit na základě následujících parametrů: 

![Asynchronní aktualizace](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Cokoli, co končí **s** je kolekce.
- Cokoli, co končí **()** je funkce.
- Cokoli, je prostředku nebo objekt.

Například můžete použít operaci POST na aktualizace kolekce k provedení operace aktualizace:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Všechna volání musí být ověřené na platný token v hlavičce autorizace služby Azure Active Directory (OAuth 2) a musí splňovat následující požadavky:

- Token musí být token uživatele nebo instančního objektu aplikace.
- Token musí být nastavena na správnou cílovou skupinu `https://*.asazure.windows.net`.
- Uživatel nebo aplikace musí mít dostatečná oprávnění na serveru nebo model pro požadovanou volání. Úroveň oprávnění se určuje podle rolí v rámci modelu nebo skupiny správce na serveru.

    > [!IMPORTANT]
    > V současné době **správce serveru** oprávnění role jsou nezbytné.

## <a name="post-refreshes"></a>/Refreshes příspěvku

K provedení operace aktualizace, pomocí operací POST na kolekce /refreshes přidat novou položku aktualizace do kolekce. Hlavičky Location v odpovědi obsahuje ID aktualizace. Klientská aplikace můžete odpojit a zkontrolovat stav později, pokud je to nutné, protože je asynchronní.

Operace pouze jedné aktualizace je přijata po jednom modelu. Pokud je aktuální běžící operaci aktualizace a jiné se odešle, vrátí se stavový kód 409 konflikt HTTP.

Text může vypadat takto:

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

Zadání parametrů se nevyžaduje. Výchozí hodnota je použita.

| Name             | Typ  | Popis  |Výchozí  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Typ zpracování, který má provést. Typy jsou v souladu s TMSL [aktualizovat příkaz](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) typy: full, clearValues, vypočítat, dataOnly, automaticky a defragmentaci. Přidáte typ není podporován.      |   automatic      |
| `CommitMode`     | Enum  | Určuje, pokud objekty budou potvrzeny v dávkách, nebo pouze v případě, že je dokončeno. Režimy: výchozí, transakční, partialBatch.  |  transakční       |
| `MaxParallelism` | Int   | Tato hodnota určuje maximální počet vláken, ve kterém se spustí paralelní zpracování příkazů. Tuto hodnotu v souladu s MaxParallelism vlastnost, která je možné nastavit v TMSL [pořadí příkaz](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) nebo pomocí jiné metody.       | 10        |
| `RetryCount`     | Int   | Označuje počet pokusů, které se předtím, než oznámí opakování operace.      |     0    |
| `Objects`        | Array | Pole objektů, které chcete zpracovat. Každý objekt obsahuje: "tabulky" při zpracování celé tabulky nebo "table" a "oddíl" při zpracování oddílu. Pokud nejsou zadány žádné objekty, se aktualizují celý model. |   Celý model procesu      |

Je rovno partialBatch CommitMode. Používá se při provádění počátečním načtení rozsáhlých datových sad, které může trvat hodiny. Pokud operace aktualizace selže po úspěšně potvrzování jeden nebo více dávky, úspěšně potvrzeny dávky zůstanou potvrzené (nevrátí zpět úspěšně potvrzeny dávky).

> [!NOTE]
> V době psaní velikost dávky je hodnota MaxParallelism, ale tato hodnota může změnit.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId >

Chcete-li zkontrolovat stav operace aktualizace, použijte příkaz GET na ID aktualizace. Následuje příklad těla odpovědi. Pokud je operace probíhá, **inProgress** je vrácen stav.

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

## <a name="get-refreshes"></a>ZÍSKAT /refreshes

Pokud chcete získat seznam operací historických aktualizace pro model, použijte příkaz GET na kolekci /refreshes. Následuje příklad těla odpovědi. 

> [!NOTE]
> V době psaní textu za posledních 30 dní operace aktualizace se ukládají a vrátí, ale toto číslo může změnit.

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

## <a name="delete-refreshesrefreshid"></a>Odstranit /refreshes/\<refreshId >

Zrušit operaci aktualizace probíhá, použijte příkaz DELETE na ID aktualizace.

## <a name="post-sync"></a>/ POST Sync

Máte provést operace aktualizace, může být nutné synchronizovat nová data s replikami pro horizontální navýšení kapacity dotazu. K provedení operace synchronizace pro model, použijte operaci POST na funkci/Sync. Hlavičky Location v odpovědi obsahuje ID operace synchronizace.

## <a name="get-sync-status"></a>ZÍSKÁNÍ stavu/Sync

Pokud chcete zkontrolovat stav operace synchronizace, použijte příkaz GET ID operace předá jako parametr. Tady je příklad těla odpovědi:

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

Hodnoty pro `syncstate`:

- 0: Replikace. Databází se replikují do cílové složky.
- 1: Rehydratace. Databáze se nedosadí data na počet instancí serveru jen pro čtení.
- 2: Dokončit. Operace synchronizace byla úspěšně dokončena.
- 3: Se nezdařilo. Operace synchronizace se nezdařila.
- 4: Dokončování. Operace synchronizace byla dokončena, ale provádí vyčištění.

## <a name="code-sample"></a>Ukázka kódu

Tady je ukázka kódu C# vám pomůžou začít, [RestApiSample na Githubu](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Chcete-li použít vzorový kód

1.  Klonovat nebo stáhnout úložiště. Otevřete řešení RestApiSample.
2.  Vyhledejte řádek **klienta. Vlastnost BaseAddress =...** a poskytnout vaší [základní adresa URL](#base-url).

Vzorový kód používá [instanční objekt služby](#service-principal) ověřování.

### <a name="service-principal"></a>Instanční objekt služby

Zobrazit [vytvoření instančního objektu – Azure portal](../active-directory/develop/howto-create-service-principal-portal.md) a [přidání hlavního názvu služby k roli správce serveru](analysis-services-addservprinc-admins.md) pro další informace o tom, jak nastavení hlavního názvu služby a přidělení potřebných oprávnění v Azure jako . Po dokončení kroků, proveďte následující kroky:

1.  Ve vzorovém kódu najít **řetězec autority =...** , nahraďte **běžné** ve vaší organizaci tenanta ID.
2.  Okomentovat/Odkomentujte tak clientcredential systému třída se používá k vytvoření instance objektu – stáhnout přihlašovací údaje. Zkontrolujte \<ID aplikace > a \<klíče aplikace > hodnoty jsou přístupné z bezpečný nebo použít ověřování pomocí certifikátů pro instanční objekty.
3.  Spusťte ukázku.


## <a name="see-also"></a>Další informace najdete v tématech

[Ukázky](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


