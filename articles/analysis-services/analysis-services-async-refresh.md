---
title: Asynchronní aktualizace pro Azure Analysis Services modely | Microsoft Docs
description: Naučte se, jak kódovat asynchronní aktualizace pomocí REST API.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 82e40f756e0d8e0b5627b7c8856bd25fa98adbcb
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932301"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynchronní aktualizace s využitím rozhraní REST API

Pomocí libovolného programovacího jazyka, který podporuje volání REST, můžete provádět asynchronní operace aktualizace dat na vašich Azure Analysis Services tabelárních modelech. To zahrnuje synchronizaci replik jen pro čtení pro horizontální navýšení kapacity dotazů. 

Operace aktualizace dat můžou určitou dobu trvat v závislosti na řadě faktorů, včetně objemu dat, úrovně optimalizace pomocí oddílů atd. Tyto operace byly tradičně vyvolány s existujícími metodami [](https://docs.microsoft.com/bi-reference/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) , jako je například použití modelu (tabelární objektový model), rutin [prostředí PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) nebo [TMSL](https://docs.microsoft.com/bi-reference/tmsl/tabular-model-scripting-language-tmsl-reference) (skriptovací jazyk tabelárního modelu). Tyto metody ale můžou vyžadovat často nespolehlivá, dlouhodobě běžící připojení HTTP.

REST API pro Azure Analysis Services umožňuje asynchronní provádění operací aktualizace dat. Když použijete REST API, dlouhotrvající připojení HTTP z klientských aplikací není nutné. K dispozici jsou také další integrované funkce pro spolehlivost, například automatické opakování a dávková potvrzení.

## <a name="base-url"></a>Základní adresa URL

Základní adresa URL má následující formát:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Představte si třeba model nazvaný AdventureWorks na serveru s názvem MyServer, který se nachází v oblasti Západní USA Azure. Název serveru:

```
asazure://westus.asazure.windows.net/myserver 
```

Základní adresa URL tohoto názvu serveru je:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Pomocí základní adresy URL lze prostředky a operace připojit na základě následujících parametrů: 

![Asynchronní aktualizace](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Cokoli, co končí v **s** , je kolekce.
- Cokoli, co končí **()** je funkce.
- Cokoli jiného je prostředek nebo objekt.

Můžete například použít příkaz POST v kolekci reaktuálnosti k provedení operace aktualizace:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Ověřování

Všechna volání musí být ověřena pomocí platného tokenu Azure Active Directory (OAuth 2) v autorizační hlavičce a musí splňovat následující požadavky:

- Token musí být buď token uživatele, nebo objekt služby aplikace.
- Token musí mít nastavenou `https://*.asazure.windows.net`správnou cílovou skupinu.
- Aby mohl uživatel nebo aplikace provést požadované volání, musí mít na serveru nebo v modelu dostatečná oprávnění. Úroveň oprávnění je určena rolemi v rámci modelu nebo skupiny pro správu na serveru.

    > [!IMPORTANT]
    > V současné době jsou nutná oprávnění role **Správce serveru** .

## <a name="post-refreshes"></a>PŘÍSPĚVEK/refreshes

Chcete-li provést operaci aktualizace, přidejte do kolekce novou položku aktualizace pomocí příkazu POST v kolekci/refreshes. Hlavička umístění v odpovědi zahrnuje ID aktualizace. Klientská aplikace se může v případě potřeby odpojit a později ověřit stav, protože je asynchronní.

V jednom okamžiku se pro model přijme jenom jedna operace aktualizace. Pokud existuje aktuální spuštěná operace aktualizace a je odeslána jiná, vrátí se stavový kód HTTP v konfliktu 409.

Tělo může vypadat takto:

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

Určení parametrů není vyžadováno. Použije se výchozí hodnota.

| Name             | Typ  | Popis  |Výchozí  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Typ zpracování, které má být provedeno. Typy jsou zarovnány s TMSL typy [příkazů pro obnovení](https://docs.microsoft.com/bi-reference/tmsl/refresh-command-tmsl) : Full, clearValues, vypočítat, dataonly, Automatic a defragmentovat. Typ přidání není podporován.      |   Automatické      |
| `CommitMode`     | Enum  | Určuje, zda budou objekty potvrzeny v dávkách nebo pouze v případě, že jsou dokončeny. Mezi režimy patří: Default, Transaction, partialBatch.  |  doručen       |
| `MaxParallelism` | Int   | Tato hodnota určuje maximální počet vláken, ve kterých se paralelně spouští příkazy zpracování. Tato hodnota je zarovnána s vlastností MaxParallelism, kterou lze nastavit v [příkazu](https://docs.microsoft.com/bi-reference/tmsl/sequence-command-tmsl) TMSL Sequence nebo pomocí jiných metod.       | 10        |
| `RetryCount`     | Int   | Určuje počet pokusů, kolikrát operace proběhne znovu, než dojde k selhání.      |     0    |
| `Objects`        | Array | Pole objektů, které mají být zpracovány. Každý objekt obsahuje: "Table" při zpracovávání celé tabulky nebo tabulky "a" partition "při zpracování oddílu. Nejsou-li zadány žádné objekty, je obnoven celý model. |   Zpracování celého modelu      |

CommitMode se rovná partialBatch. Používá se při počátečním zatížení velkých datových sad, které mohou trvat hodiny. Pokud operace aktualizace selže po úspěšném potvrzení jedné nebo více dávek, všechny úspěšně potvrzené dávky zůstanou popsány (nevrátí úspěšně potvrzené dávky).

> [!NOTE]
> V okamžiku psaní je velikost dávky hodnota MaxParallelism, ale tato hodnota se může změnit.

## <a name="get-refreshesrefreshid"></a>Získat/refreshes/\<refreshId >

Chcete-li zjistit stav operace aktualizace, použijte příkaz GET v ID aktualizace. Tady je příklad těla odpovědi. Pokud operace probíhá, vrátí se stav při **zpracování** .

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

## <a name="get-refreshes"></a>ZÍSKAT/refreshes

Chcete-li získat seznam historických operací aktualizace pro model, použijte příkaz GET v kolekci/refreshes. Tady je příklad těla odpovědi. 

> [!NOTE]
> V době psaní se budou ukládat a vracet poslední 30 dní operací aktualizace, ale toto číslo se může změnit.

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

## <a name="delete-refreshesrefreshid"></a>Odstranit/refreshes/\<refreshId >

Chcete-li zrušit probíhající operaci aktualizace, použijte příkaz DELETE pro ID aktualizace.

## <a name="post-sync"></a>PŘÍSPĚVEK/Sync

Po provedení operací aktualizace může být nutné synchronizovat nová data s replikami pro škálování dotazu. K provedení operace synchronizace pro model použijte příkaz POST ve funkci/Sync. Hlavička umístění v odpovědi zahrnuje ID operace synchronizace.

## <a name="get-sync-status"></a>ZÍSKAT stav/Sync

Chcete-li zjistit stav operace synchronizace, použijte příkaz GET s předáním ID operace jako parametru. Tady je příklad těla odpovědi:

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

- 0: Replikaci. Soubory databáze jsou replikovány do cílové složky.
- 1: Dosazování dat. Probíhá rehydratované databáze na instancích serveru jen pro čtení.
- 2: Dokončeno Operace synchronizace se úspěšně dokončila.
- 3: Selhalo Operace synchronizace se nezdařila.
- 4: Dokončuje. Operace synchronizace se dokončila, ale provádí kroky čištění.

## <a name="code-sample"></a>Ukázka kódu

Zde je příklad C# kódu, který vám umožní začít [RestApiSample na GitHubu](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Použití ukázky kódu

1.  Naklonujte nebo Stáhněte úložiště. Otevřete řešení RestApiSample.
2.  Najděte klienta line **. BaseAddress =...** a zadejte svou [základní adresu URL](#base-url).

Ukázka kódu používá ověřování [instančního objektu](#service-principal) .

### <a name="service-principal"></a>Instanční objekt

Další informace o tom, jak nastavit instanční objekt a přiřadit potřebná oprávnění v Azure jako, najdete v tématu [Vytvoření instančního objektu – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md) a [Přidání instančního objektu k roli správce serveru](analysis-services-addservprinc-admins.md) . Po dokončení kroků proveďte následující další kroky:

1.  V ukázce kódu vyhledejte řetězcovou **autoritu =...** , nahraďte **Common** číslem ID tenanta vaší organizace.
2.  Komentář/Odkomentujte, aby se třída ClientCredential použila k vytvoření instance objektu přihlašovacích údajů. Ujistěte se \<, že ID aplikace \<> a klíč aplikace > jsou dostupné zabezpečeným způsobem nebo používají ověřování pomocí certifikátů u instančních objektů.
3.  Spusťte ukázku.


## <a name="see-also"></a>Viz také:

[Ukázky](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   

<!--test-->
