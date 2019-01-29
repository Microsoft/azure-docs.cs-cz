---
title: Principy pravidelné zálohování konfigurace v Azure Service Fabric | Dokumentace Microsoftu
description: Použít Service Fabric pravidelné zálohování a obnovení funkce umožňující použití periodických dat. zálohování dat ve vašich aplikacích.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: hrushib
ms.openlocfilehash: 14d7ae7cc347b771dfdb1209bc8d55c484d00db0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193722"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Principy pravidelné zálohování konfigurace v Azure Service Fabric

Konfigurování pravidelné zálohování spolehlivé stavové služby Reliable Actors nebo se skládá z následujících kroků:

1. **Vytvoření zásady zálohování**: V tomto kroku se vytvoří jeden nebo více zásad zálohování v závislosti na požadavcích.

2. **Povolení zálohování**: V tomto kroku přidružte zásady zálohování, které jsou vytvořené v **kroku 1** do požadované entity _aplikace_, _služby_, nebo _oddílu_.

## <a name="create-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování se skládá z následujících konfigurací:

* **Automatické obnovení ztráty dat**: Určuje, zda se aktivovat obnovení automaticky pomocí nejnovější dostupné zálohy v případě, že oddíl dojde před událostí ztráty.

* **Maximální počet přírůstkové zálohování**: Definuje maximální počet přírůstkové zálohy, jež mají být provedeny mezi dvěma úplné zálohy. Maximální počet přírůstkové zálohy zadejte horní limit. Úplné zálohy mohou být přijata před zadaným číslem přírůstkových záloh jsou dokončeny v jednom z následujících podmínek

    1. Replika se nikdy neobsadila úplnou zálohu. vzhledem k tomu se stal primárním.

    2. Některé záznamy protokolu od poslední zálohy byl zkrácen.

    3. Repliky předán MaxAccumulatedBackupLogSizeInMB limit.

* **Plán zálohování**: Chvíli a frekvenci, ve kterém se má provést pravidelného zálohování. Jeden můžete naplánovat zálohování se opakovat v zadaném intervalu nebo v pevném časovém denně nebo týdně.

    1. **Plán zálohování na základě frekvence**: Tento typ plán by měl použít, pokud je nutné provést zálohování dat v pravidelných intervalech. Požadovaný časový interval mezi dvěma po sobě jdoucích zálohy je definována použít formát ISO8601. Plán frekvence zálohování podporuje rozlišení intervalu na minutu.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Plán zálohování podle času**: Tento typ plán by měl použít, pokud je nutné provést zálohování dat v určitých časech ze dne nebo týdne. Typ frekvence plán může být buď denně nebo týdně.
        1. **_Denní_ časovou synchronizací plán zálohování**: Tento typ plán by měl použít, pokud identifikátor potřeba provést zálohování dat v určitých časech dne. Chcete-li tuto verzi uveďte, nastavte `ScheduleFrequencyType` k _denní_; a nastavte `RunTimes` do seznamu požadovanou dobu během dne, ve formátu ISO8601 a datem zadat společně s času bude ignorován. Například `0001-01-01T18:00:00` představuje _18:00:00_ každý den, ignoruje se část data _0001-01-01_. Následující příklad znázorňuje konfiguraci tak, aby aktivační událost denní zálohování na _9:00:00_ a _18:00:00_ každý den.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Týdenní_ časovou synchronizací plán zálohování**: Tento typ plán by měl použít, pokud identifikátor potřeba provést zálohování dat v určitých časech dne. Chcete-li tuto verzi uveďte, nastavte `ScheduleFrequencyType` k _týdenní_set `RunDays` seznam dní v týdnu, kdy zálohování musí spustit a nastavte `RunTimes` do seznamu požadovanou dobu během dne, ve formátu ISO8601 a datum zadat společně s čas budou ignorovány. Seznam dní v týdnu kdy aktivovat pravidelné zálohování. Následující příklad znázorňuje konfiguraci tak, aby aktivační událost denní zálohování na _9:00:00_ a _18:00:00_ během od pondělí do pátku.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Úložiště zálohování**: Určuje umístění pro nahrání zálohy. Úložiště může být buď úložiště objektů blob v Azure nebo pro sdílení souborů.
    1. **Úložiště objektů blob v Azure**: Tento typ úložiště musí být vybrána, když potřeba, je vygenerována úložiště záloh v Azure. Obě _samostatné_ a _založené na Azure_ clustery můžete použít tento typ úložiště. Popis pro tento typ úložiště vyžaduje připojovací řetězec a název kontejneru, ve kterém budete muset nahrát zálohy. Pokud kontejneru se zadaným názvem není k dispozici, se vytvoří při nahrávání zálohy.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Sdílené složky**: Tento typ úložiště by měl vybrat pro _samostatné_ clusterů, když je potřeba k ukládání dat zálohovat místní. Popis pro tento typ úložiště vyžaduje cesta sdílení souborů, ve kterém budete muset nahrát zálohy. Přístup ke sdílené složce je nakonfigurovat pomocí jednoho z následujících možností
        1. _Integrované ověřování Windows_, kterých je přístup ke sdílené složce k dispozici pro všechny počítače patřící do clusteru Service Fabric. V takovém případě nastavte následující pole konfigurace _sdílenou složku_ záložní úložiště využívající SSD disky.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Ochranu sdílené složky pomocí uživatelského jména a hesla_, kterých je přístup ke sdílené složce k dispozici pro určité uživatele. Specifikace úložiště sdílené složky souboru také poskytuje možnost zadání sekundární uživatelské jméno a sekundární heslo pro pověření fall zpět v případě selhání ověřování s primární uživatelské jméno a heslo primární. V takovém případě nastavte následující pole konfigurace _sdílenou složku_ záložní úložiště využívající SSD disky.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Ujistěte se, že spolehlivost úložiště splňuje nebo překračuje požadavky spolehlivost dat záloh.
>

* **Zásady uchovávání informací**: Určuje zásady pro uchovávání záloh v nakonfigurovaného úložiště. Je podporován pouze základní zásady uchovávání informací.
    1. **Zásady uchovávání informací základní**: Tyto zásady uchování umožňuje zajistit optimální úložiště využití Odebírání záložních souborů, které jsou požadovány žádné další. `RetentionDuration` lze nastavit časové období, pro kterou jsou požadovány pro zachování v úložišti záloh. `MinimumNumberOfBackups` je volitelný parametr, který je možné zadat pro Ujistěte se, že zadaný počet zálohy se vždy zachovají bez ohledu na `RetentionDuration`. Následující příklad znázorňuje konfiguraci pro uchovávání záloh pro _10_ dnů a neumožňuje počet zálohy přejít níže _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

> [!IMPORTANT]
> Kvůli problému v modulu runtime Ujistěte se, že doba uchování v zásadách uchovávání dat je nakonfigurovaný, aby byl méně než 24 dní, jinak by vést ve službě obnovení zálohy přejít do převzetí služeb při selhání kvora ztráty příspěvek repliky.

## <a name="enable-periodic-backup"></a>Povolit pravidelné zálohování
Po definování zásad zálohování pro splnění požadavků pro zálohování dat, zásady zálohování by měly být správně přidružené buď _aplikace_, nebo _služby_, nebo _oddílu_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarchické šíření zásady zálohování
V Service Fabric, je vztah mezi aplikace, služby a oddíly hierarchické, jak je vysvětleno v [aplikační model](./service-fabric-application-model.md). Zásady zálohování může být přidružené buď _aplikace_, _služby_, nebo _oddílu_ v hierarchii. Zásady zálohování hierarchicky rozšíří na další úroveň. Za předpokladu, že existuje pouze jeden zásady zálohování vytvořena a přidružena _aplikace_, všechny stavové oddíly, které patří ke všem _spolehlivé stavové služby_ a _Reliable Actors_ z _aplikace_ bude možné zálohovaných pomocí zásady zálohování. Nebo pokud je přidružené zásady zálohování _spolehlivé stavové služby_, všechny její oddíly budou mít zálohovaných pomocí zásady zálohování.

### <a name="overriding-backup-policy"></a>Přepsání zásady zálohování
Můžou existovat scénáře, kde je zálohování dat pomocí stejného plánu zálohování vyžaduje pro všechny služby aplikace s výjimkou určité služby ve kterém je potřeba, aby zálohování dat používají vyšší frekvence plán nebo pořízení zálohy na jiný účet úložiště nebo sdílení souborů. K řešení scénářů, obnovení zálohy služba poskytuje zařízení pro zásady přepsání rozšíří na služby a oddílu oboru. Když zásada zálohování je přidružené na _služby_ nebo _oddílu_, přepíše rozšíří zásady zálohování, pokud existuje.

### <a name="example"></a>Příklad:

Tento příklad používá instalace s dvěma aplikacemi _MyApp_A_ a _MyApp_B_. Aplikace _MyApp_A_ obsahuje dvě Reliable Stateful services _SvcA1_ & _SvcA3_a jedna služba Reliable Actors _ActorA2_. _SvcA1_ obsahuje tři oddíly při _ActorA2_ a _SvcA3_ obsahují dva oddíly.  Aplikace _MyApp_B_ obsahuje tři Reliable Stateful services _SvcB1_, _SvcB2_, a _SvcB3_. _SvcB1_ a _SvcB2_ obsahuje dva oddíly, které každý při _SvcB3_ obsahuje tři oddíly.

Předpokládají, že jsou požadavky na tyto aplikace dat záloh následujícím způsobem

1. MyApp_A
    1. Vytvoření denní zálohování dat pro všechny oddíly všech _Reliable Stateful services_ a _Reliable Actors_ patřící do aplikace. Nahrát data záloh do umístění _BackupStore1_.

    2. Jedna ze služeb, _SvcA3_, vyžaduje data zálohování každou hodinu.

    3. Velikost dat v oddílu _SvcA1_P2_ je větší než bylo očekáváno a jeho zálohovaná data by měla být uložena do umístění jiného úložiště _BackupStore2_.

2. MyApp_B
    1. Vytvořit zálohu dat každou neděli v 8:00:00 pro všechny oddíly _SvcB1_ služby. Nahrát data záloh do umístění _BackupStore1_.

    2. Vytvořit záložní kopii dat, každý den v 8:00:00 pro oddíl _SvcB2_P1_. Nahrát data záloh do umístění _BackupStore1_.

Pokud chcete vyřešit tyto požadavky pro zálohování dat, se vytvoří zásady zálohování k BP_5 BP_1 a zásadu povolíte následujícím způsobem.
1. MyApp_A
    1. Vytvořit zásadu zálohování, _BP_1_, s frekvencí podle plánu zálohování kde frequency je nastavená na 24 hodin. a úložiště, které umožňují použít umístění úložiště zálohování _BackupStore1_. Tuto zásadu Povolit pro aplikaci _MyApp_A_ pomocí [povolit zálohování aplikace](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) rozhraní API. Tato akce povolí zálohování dat pomocí zásad zálohování _BP_1_ pro všechny oddíly _Reliable Stateful services_ a _Reliable Actors_ které patří k aplikaci  _MyApp_A_.

    2. Vytvořit zásadu zálohování, _BP_2_, kde frequency je nastavená na 1 na základě četnosti zálohování plánu hodin. a úložiště, které umožňují použít umístění úložiště zálohování _BackupStore1_. Povolit tyto zásady pro službu _SvcA3_ pomocí [povolit službu Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) rozhraní API. Tato akce přepíše rozšíří zásady _BP_1_ ve explicitně povolené zásady zálohování _BP_2_ pro všechny oddíly služby _SvcA3_ což vede k zálohování dat pomocí služby backup zásady _BP_2_ pro tyto oddíly.

    3. Vytvořit zásadu zálohování, _BP_3_, s frekvencí podle plánu zálohování kde frequency je nastavená na 24 hodin. a úložiště, které umožňují použít umístění úložiště zálohování _BackupStore2_. Povolit tyto zásady pro oddíl _SvcA1_P2_ pomocí [povolit zálohování oddílu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) rozhraní API. Tato akce přepíše rozšíří zásady _BP_1_ ve explicitně povolené zásady zálohování _BP_3_ pro oddíl _SvcA1_P2_.

2. MyApp_B
    1. Vytvořit zásadu zálohování, _BP_4_, s časovou synchronizací plán zálohování kde je plán frekvence typu nastaven na každý týden, spuštění dnů je nastavena na neděli, a doba spuštění nastavený na 8:00:00. Úložiště nakonfigurovaná tak, aby používal umístění úložiště zálohování _BackupStore1_. Povolit tyto zásady pro službu _SvcB1_ pomocí [povolit službu Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) rozhraní API. Tato akce povolí zálohování dat pomocí zásad zálohování _BP_4_ pro všechny oddíly služby _SvcB1_.

    2. Vytvořit zásadu zálohování, _BP_5_, s časovou synchronizací plán zálohování kde typ frekvenci plánu je nastavena na každý den a čas spuštění je nastavená na 8:00:00. Úložiště nakonfigurovaná tak, aby používal umístění úložiště zálohování _BackupStore1_. Povolit tyto zásady pro oddíl _SvcB2_P1_ pomocí [povolit zálohování oddílu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) rozhraní API. Tato akce povolí zálohování dat pomocí zásad zálohování _BP_5_ pro oddíl _SvcB2_P1_.

Následující diagram znázorňuje explicitně povolené zásady zálohování a rozšíří zásady zálohování.

![Hierarchie aplikace Service Fabric][0]

## <a name="disable-backup"></a>Zakázat zálohování
Pokud není nutné k zálohování dat, můžete zásady zálohování zakázáno. Zásady povolené na zálohování _aplikace_ se dá deaktivovat jenom na stejné _aplikace_ pomocí [zakázat zálohu aplikace](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) rozhraní API, zásady zálohování povoleno _služby_ současně se dají zakázat _služby_ pomocí [zakázat službu Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) rozhraní API a aktivovat na zásady zálohování _oddílu_ současně se dají zakázat _oddílu_ pomocí [zakázat zálohování oddílu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) rozhraní API.

* Zakázání zásady zálohování pro _aplikace_ zastaví všechny zálohy periodických dat. děje oddílů spolehlivé stavové služby nebo oddíly Reliable Actors v důsledku šíření zásady zálohování.

* Zakázání zásady zálohování pro _služby_ zastaví všechny zálohy periodických dat. děje v důsledku šíření tyto zásady zálohování na oddíly _služby_.

* Zakázání zásady zálohování pro _oddílu_ zastaví všechny zálohy periodických dat. děje z důvodu zásad zálohování v oddílu.

* Při zakazování zálohy pro entity(application/service/partition) `CleanBackup` může být nastaven na _true_ odstranit všechny zálohy v nakonfigurovaného úložiště.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Pozastavit a obnovit zálohování
Dočasné pozastavení pravidelné zálohování dat může vyžadují určité situace. V takové situaci, v závislosti na požadavek na pozastavení zálohování rozhraní API může použít na _aplikace_, _služby_, nebo _oddílu_. Pravidelné zálohování pozastavení je přenositelný podstrom hierarchie aplikace od okamžiku, kdy se použije. 

* Při pozastavení se má použít na _aplikace_ pomocí [pozastavení aplikace Zálohování](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) rozhraní API, všechny služby a oddíly v rámci této aplikace se pozastavuje pravidelné zálohování dat.

* Při pozastavení se má použít na _služby_ pomocí [pozastavení služby zálohování](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) rozhraní API, všechny oddíly v rámci této služby se pozastavuje pravidelné zálohování dat.

* Při pozastavení se má použít na _oddílu_ pomocí [pozastavit oddílu zálohování](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) rozhraní API a pak se pozastaví oddíly v rámci této služby se pozastavuje pravidelné zálohování dat.

Po pozastavení se vaše potřeby zálohování periodických dat. můžete obnovit pomocí odpovídajících obnovit zálohování rozhraní API. Pravidelné zálohování musí pokračovat znovu současně _aplikace_, _služby_, nebo _oddílu_ kde bylo pozastaveno.

* Pokud bylo použito pro pozastavení _aplikace_, pak by měl obnovit, pomocí [obnovit zálohu aplikace](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) rozhraní API. 

* Pokud bylo použito pro pozastavení _služby_, pak by měl obnovit, pomocí [obnovit službu zálohování](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) rozhraní API.

* Pokud bylo použito pro pozastavení _oddílu_, pak by měl obnovit, pomocí [Obnovit oddíl zálohování](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) rozhraní API.

### <a name="difference-between-suspend-and-disable-backups"></a>Rozdíl mezi pozastavit a zakázat zálohování
Zakázat zálohování by měl při zálohování už nejsou vyžadované pro konkrétní aplikace, služby nebo oddíl. Jeden můžete vyvolat zakažte požadavek na zálohování společně s parametrem čisté zálohy na mít hodnotu true, což znamená, že všechny existující zálohy budou také odstraněny. Ale pozastavení se má použít ve scénářích, kde jeden chce vypnout záloh dočasně jako při zaplnění místního disku nebo nahrávání zálohování selhává kvůli problému známé síti atd. 

Při zakázání lze vyvolat pouze na úrovni, která byla starší pro zálohování explicitně povolená ale pozastavení můžete použít na libovolné úrovni, která je aktuálně povolena pro zálohování buď přímo nebo prostřednictvím dědičnosti nebo hierarchii. Například pokud je zálohování povolené na úrovni aplikace, jeden vyvolat zakázat pouze na úrovni aplikace ale pozastavit může být vyvolána v aplikaci, jakékoli služby nebo oddíl pro tuto aplikaci. 

## <a name="auto-restore-on-data-loss"></a>Automatické obnovení ztráty dat
Oddíl služby může dojít ke ztrátě dat z důvodu neočekávané chyby. Například disku pro dvě ze tří replik pro oddíl (včetně primární replice) získá poškozený nebo dojde k vymazání.

Když Service Fabric zjistí, že oddíl je ztrátu dat, vyvolá `OnDataLossAsync` rozhraní metoda v oddílu a očekává, že oddíl má provést požadované akce, která pocházejí z ztrátě dat. V této situaci, pokud má platné zásady zálohování v oddílu `AutoRestoreOnDataLoss` příznak nastaven na `true` pak získá obnovení aktivuje automaticky pomocí nejnovější dostupné zálohy pro tento oddíl.

## <a name="get-backup-configuration"></a>Získat konfiguraci zálohování
Jsou k dispozici získat informace o konfiguraci zálohování na samostatné rozhraní API _aplikace_, _služby_, a _oddílu_ oboru. [Získejte informace o konfiguraci zálohování aplikace](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [získat informace o konfiguraci zálohování služby](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo), a [získat informace o konfiguraci zálohování oddílu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) jsou rozhraní API v uvedeném pořadí. Tato rozhraní API, hlavně vrátí příslušné zásady zálohování, rozsah, jakou zásadu zálohování, která jsou použité a zálohování pozastavení podrobnosti. Tady je stručný popis vrácené výsledky těchto rozhraní API.

- Informace o konfiguraci zálohování aplikace: poskytuje podrobné informace o zásady zálohování použít v aplikaci a všechny zásady údajů na služby a oddíly, které patří k aplikaci. Zahrnuje také informace o pozastavení pro aplikace a služby a oddíly.

- Informace o konfiguraci zálohování služby: poskytuje podrobné informace o platné zásady zálohování na službu a obor, na které byla tato zásada použitá a všechny zásady údajů na její oddíly. Obsahuje také informace o pozastavení služby a jejích oddílů.

- Rozdělit informace o konfiguraci zálohování: poskytuje podrobné informace o platné zásady zálohování v oddílu a rozsah, ve kterém byla tato zásada použitá. Obsahuje také informace o pozastavení pro oddíly.

## <a name="list-available-backups"></a>Seznam dostupných záloh

Dostupné zálohy je možný, pomocí rozhraní API získat seznam zálohování. Výsledek volání rozhraní API obsahuje informace o zálohování položky související s všechny zálohy, které jsou k dispozici na na úložiště zálohování, který je nakonfigurován v příslušné zásady zálohování. Seznam dostupných záloh patřící do aplikace, služby nebo oddílu jsou k dispozici různé varianty tohoto rozhraní API. Tato rozhraní API podporovat získávání _nejnovější_ dostupnou zálohou všemi příslušnými oddílů nebo filtrování záloh na základě _počáteční datum_ a _koncové datum_.

Tato rozhraní API také podporujte stránkování výsledků, když _MaxResults_ parametr je nastaven na nenulové kladné celé číslo a rozhraní API vrátí maximální _MaxResults_ zálohovat informace o položky. V případě více informací o záloze položek jsou k dispozici než _MaxResults_ hodnotu, je vrácena token pro pokračování. Platné pokračování token parametru se dá použít k získání další sadu výsledků. Pokud hodnota tokenu platné pokračování je předán do příštího volání rozhraní API, rozhraní API vrátí další sadu výsledků. Žádný token pro pokračování je součástí odpovědi budou při všechny dostupné výsledky jsou vráceny.

Tady je stručné informace o podporovaných variant.

- [Získání seznamu zálohování aplikace](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): Vrátí seznam záloh, které jsou k dispozici pro každý oddíl, do které patří k dané aplikaci Service Fabric.

- [Získání služby zálohování seznamu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): Vrátí seznam záloh, které jsou k dispozici pro každý oddíl, do které patří k dané služby Service Fabric.
 
- [Získání seznamu zálohování oddílu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): Vrátí seznam záloh, které jsou k dispozici pro zadaný oddíl.

## <a name="next-steps"></a>Další postup
- [Reference k rozhraní API REST obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/BackupPolicyAssociationExample.png
