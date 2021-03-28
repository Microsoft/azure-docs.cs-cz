---
title: Škálování a Správa vlastních dovedností
titleSuffix: Azure Cognitive Search
description: Naučte se nástroje a techniky pro efektivní škálování vlastní dovednosti pro maximální propustnost. Vlastní dovednosti vyvolávají vlastní modely AI nebo logiku, které můžete přidat do kanálu pro indexování s obohaceným AI do Azure Kognitivní hledání.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 4fdc222fa20aef6639bf6d5d485f7dcf6b6ca535
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641142"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Efektivní horizontální navýšení kapacity vlastní dovednosti

Vlastní dovednosti jsou webová rozhraní API, která implementují konkrétní rozhraní. Vlastní dovednost se dá implementovat na libovolný veřejně adresovatelný prostředek. Mezi nejběžnější implementace vlastních dovedností patří:
* Azure Functions pro vlastní logiky logických znalostí
* Azure webapps pro jednoduché s kontejnerem AI – dovednosti
* Služba Azure Kubernetes pro složitější nebo větší dovednosti.

## <a name="prerequisites"></a>Požadavky

+ Projděte si [vlastní dovednostní rozhraní](cognitive-search-custom-skill-interface.md) , kde se seznámíte se vstupním a výstupním rozhraním, které by měla vlastní dovednost implementovat.

+ Nastavte své prostředí. V [tomto kurzu](/azure/azure-functions/create-first-function-vs-code-python) můžete začít pomocí rozšíření Visual Studio Code a Pythonu, a to od začátku po k nastavení funkce Azure bez serveru.

## <a name="skillset-configuration"></a>Konfigurace dovednosti

Konfigurace vlastní dovednosti pro maximalizaci propustnosti procesu indexování vyžaduje porozumění dovednostím, konfiguracím indexerů a způsobu, jakým se dovednosti týkají jednotlivých dokumentů. Například počet vyvolaných dovedností na dokument a očekávanou dobu trvání na vyvolání.

### <a name="skill-settings"></a>Nastavení dovedností

V části [vlastní dovednost](cognitive-search-custom-skill-web-api.md) nastavte následující parametry.

1. Sada `batchSize` vlastních dovedností ke konfiguraci počtu záznamů odesílaných do dovedností v jednom volání dovednosti.

2. Nastavte na `degreeOfParallelism` kalibraci počtu souběžných požadavků, které indexer provede pro vaši dovednost.

3. Nastavte `timeout` na dostatečnou hodnotu, aby mohla dovednost reagovat s platnou odpovědí.

4. V `indexer` definici nastavte [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) na počet dokumentů, které by měly být načteny ze zdroje dat a obohaceny současně.

### <a name="considerations"></a>Požadavky

Nastavením těchto proměnných pro optimalizaci výkonu indexerů je potřeba určit, jestli vaše dovednost funguje lépe s mnoha souběžnými malými požadavky nebo méně velkými požadavky. Je třeba vzít v úvahu několik otázek:

* Jaká je mohutnost volání dovednosti? Provede se dovednost jednou pro každý dokument, například dovednost klasifikace dokumentu, nebo může být dovednost provedena vícekrát na jeden dokument, jako dovednost klasifikace odstavce?

* Průměrná doba čtení dokumentů ze zdroje dat za účelem vyplnění žádosti o dovednost na základě velikosti dávky dovednosti? V ideálním případě by měl být menší než velikost dávky indexeru. Díky velikosti služby Batch větší než 1 může vaše dovednost přijímat záznamy z více zdrojových dokumentů. Například pokud je počet dávek indexeru 5 a počet dávek dovedností je 50 a každý dokument generuje jenom pět záznamů, indexer bude muset vyplnit vlastní žádost o dovednosti napříč několika dávkami indexeru.

* Průměrný počet požadavků, které může vygenerovat dávka indexeru, by vám měl poskytnout optimální nastavení pro stupně paralelismu. Pokud vaše infrastruktura hostující dovednost nemůže podporovat tuto úroveň souběžnosti, zvažte vytočení stupně paralelismu. Osvědčeným postupem je otestovat konfiguraci s několika dokumenty, abyste ověřili volby v parametrech.

* Testování s menší ukázkou dokumentů vyhodnotí dobu provádění vaší dovednosti na celkovou dobu trvání zpracování podmnožiny dokumentů. Stráví indexer více času sestavování dávky nebo čekání na odpověď od vaší dovednosti? 

* Zvažte, jestli mají vznesené důsledky paralelismu. Pokud je vstup pro vlastní dovednost výstupem z předchozí dovednosti, jsou všechny dovednosti v dovednosti škálované na efektivně, aby se minimalizovala latence?

## <a name="error-handling-in-the-custom-skill"></a>Zpracování chyb v vlastní dovednosti

Vlastní dovednosti by měly vrátit stavový kód úspěchu HTTP 200, když se dovednost úspěšně dokončí. Pokud v jednom nebo více záznamech v dávce dojde k chybám, zvažte vrácení kódu s více stavy 207. Seznam chyb nebo upozornění pro záznam by měl obsahovat příslušnou zprávu.

Všechny položky v dávce, jejichž chyby budou mít za následek selhání odpovídajícího dokumentu. Pokud potřebujete, aby byl dokument úspěšný, vraťte upozornění.

Všechny stavové kódy nad 299 se vyhodnotí jako chyba a všechna rozšíření se nezdařila v důsledku neúspěšného dokumentu. 

### <a name="common-error-messages"></a>Běžné chybové zprávy

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Nastavte parametr timeout pro dovednost tak, aby umožňoval delší dobu trvání spuštění.

* `Could not execute skill because Web Api skill response is invalid.` Indikativní dovednost nevrací zprávu ve vlastním formátu odpovědi na dovednosti. To může být výsledek nezachycené výjimky v dovednosti.

* `Could not execute skill because the Web Api request failed.` Nejpravděpodobnější příčinou chyb autorizace nebo výjimek.

* `Could not execute skill.` Obvykle je výsledkem mapování dovedností, které je mapováno na existující vlastnost v hierarchii dokumentu.

## <a name="testing-custom-skills"></a>Testování vlastních dovedností

Začněte tím, že otestujete vlastní dovednost pomocí REST API klienta k ověření:

* Dovednost implementuje vlastní rozhraní dovedností pro žádosti a odpovědi.

* Dovednost vrátí platný kód JSON s `application/JSON` typem MIME.

* Vrátí platný stavový kód HTTP.

Vytvořte [relaci ladění](cognitive-search-debug-session.md) pro přidání vaší dovednosti do dovednosti a zajistěte, aby byla zajištěna platná obohacení. I když ladicí relace neumožňuje ladit výkon dovednosti, umožňuje zajistit, že je dovednost nakonfigurovaná s platnými hodnotami a vrátí očekávané obohacené objekty.

## <a name="best-practices"></a>Osvědčené postupy

* I když dovednosti můžou přijímat a vracet větší datové části, zvažte, jestli při vracení formátu JSON dojde k omezení odezvy na 150 MB nebo méně.

* Zvažte nastavení velikosti dávky v indexeru a dovednosti, abyste zajistili, že každá dávka zdroje dat vygeneruje úplnou datovou část pro vaši dovednost.

* V případě dlouhotrvajících úloh nastavte časový limit na dostatečně vysokou hodnotu, aby indexovací člen nechybí při zpracování dokumentů souběžně.

* Optimalizujte velikost dávky indexeru, velikost dávky dovedností a dovednosti paralelismu a vygenerujte zátěžový vzor, který očekává vaše dovednosti, méně velký počet požadavků nebo mnoho malých požadavků.

* Sledujte vlastní dovednosti pomocí podrobných protokolů selhání, protože můžete mít scénáře, kdy se konkrétní požadavky konzistentně nedaří v důsledku proměnlivosti dat.


## <a name="next-steps"></a>Další kroky
Gratulujeme! Vaše vlastní dovednost se teď škáluje doprava, aby se maximalizovala propustnost indexeru. 

+ [Dovednosti v Power: úložiště vlastních dovedností](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Přidání vlastní dovednosti do kanálu pro obohacení AI](cognitive-search-custom-skill-interface.md)
+ [Přidat Azure Machine Learning dovednost](./cognitive-search-aml-skill.md)
+ [Použití ladicích relací k testování změn](./cognitive-search-debug-session.md)