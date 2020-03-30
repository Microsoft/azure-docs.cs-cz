---
title: Principy konfigurace pravidelného zálohování
description: Použijte funkci pravidelného zálohování a obnovení service fabric pro povolení pravidelného zálohování dat dat aplikace.
author: hrushib
ms.topic: article
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: 34c6495e094a1160f6ac75b9f098934d5cbce967
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610144"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Principy konfigurace pravidelného zálohování ve službě Azure Fabric

Konfigurace pravidelného zálohování spolehlivých stavových služeb nebo spolehlivých aktérů se skládá z následujících kroků:

1. **Vytvoření zásad zálohování**: V tomto kroku je vytvořena jedna nebo více zásad zálohování v závislosti na požadavcích.

2. **Povolení zálohování**: V tomto kroku přidružíte zásady zálohování vytvořené v **kroku 1** k požadovaným entitám, _aplikaci_, _službě_nebo _oddílu_.

## <a name="create-backup-policy"></a>Vytvořit zásady zálohování

Zásady zálohování se skládají z následujících konfigurací:

* **Automatické obnovení při ztrátě dat**: Určuje, zda se má automatické obnovení aktivovat pomocí nejnovější dostupné zálohy v případě, že dojde k události ztráty dat v oddílu.

* **Maximální přírůstkové zálohy**: Definuje maximální počet přírůstkových záloh, které mají být přijaty mezi dvěma úplnými zálohami. Maximální přírůstkové zálohy určují horní limit. Před dokončením zadaného počtu přírůstkových záloh v jedné z následujících podmínek může být provedena úplná záloha.

    1. Replika nikdy nepřijala úplnou zálohu od té doby, co se stala primární.

    2. Některé záznamy protokolu od poslední zálohy byly zkráceny.

    3. Limit MaxAccumulatedBackupLogSizeInMB byla replika předána.

* **Plán zálohování**: Čas nebo četnost, po kterou má být pravidelně zálohováno. Lze naplánovat, aby se zálohy opakovaly v určeném intervalu nebo v pevně stanoveném čase denně / týdně.

    1. **Plán zálohování založený na frekvenci**: Tento typ plánu by měl být použit, pokud je potřeba zálohovat data v pevných intervalech. Požadovaný časový interval mezi dvěma po sobě následujícími zálohami je definován ve formátu ISO8601. Plán zálohování založený na frekvenci podporuje rozlišení intervalu na minutu.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Plán zálohování podle času**: Tento typ plánu by měl být použit, pokud je potřeba provést zálohování dat v určitých časech dne nebo týdne. Typ frekvence plánu může být denní nebo týdenní.
        1. ** _Denní_ plán zálohování založený na čase**: Tento typ plánu by měl být použit, pokud je třeba id pro zálohování dat v určitých časech dne. Chcete-li to `ScheduleFrequencyType` zadat, nastavte na _denní_; a `RunTimes` nastavte na seznam požadovaného času během dne ve formátu ISO8601, datum zadané spolu s časem bude ignorováno. Například `0001-01-01T18:00:00` představuje _6:00 PM_ každý den, ignoruje datum část _0001-01-01_. Níže uvedený příklad ilustruje konfiguraci pro spuštění denního zálohování v _9:00_ a _18:00_ každý den.

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

        2. ** _Týdenní_ plán zálohování založený na čase**: Tento typ plánu by měl být použit, pokud je třeba id pro zálohování dat v určitých časech dne. Chcete-li to `ScheduleFrequencyType` zadat, nastavte na _týdenní_; nastavit `RunDays` na seznam dnů v týdnu, kdy `RunTimes` je třeba spustit zálohování a nastavit na seznam požadovaného času během dne ve formátu ISO8601, datum zadané spolu s časem bude ignorováno. Seznam dnů v týdnu, kdy se má spustit pravidelné zálohování. Níže uvedený příklad ilustruje konfiguraci pro spuštění denního zálohování v _9:00_ a _18:00_ během pondělí až pátek.

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

* **Úložiště záloh**: Určuje umístění pro nahrávání záloh. Úložiště může být úložiště objektů blob Azure nebo sdílené složky.
    1. **Úložiště objektů blob Azure**: Tento typ úložiště by měl být vybrán, když je potřeba ukládat generované zálohy v Azure. Tento typ úložiště můžou používat _samostatné_ clustery i clustery _založené na Azure._ Popis pro tento typ úložiště vyžaduje připojovací řetězec a název kontejneru, kde je třeba odeslat zálohy. Pokud kontejner se zadaným názvem není k dispozici, získá vytvořené během nahrávání zálohy.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Sdílení souborů**: Tento typ úložiště by měl být vybrán pro _samostatné_ clustery, když je potřeba ukládat zálohování dat místně. Popis pro tento typ úložiště vyžaduje cestu ke sdílení souborů, kde je třeba nahrát zálohy. Přístup ke sdílené složce lze nakonfigurovat pomocí jedné z následujících možností
        1. _Integrované ověřování systému Windows_, kde je přístup ke sdílené složce poskytován všem počítačům patřícím do clusteru Service Fabric. V takovém případě nastavte následující pole pro konfiguraci úložiště zálohování založeného na _sdílení souborů._

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Ochrana sdílené složky pomocí uživatelského jména a hesla_, kde je přístup ke sdílené složce poskytován konkrétním uživatelům. Specifikace úložiště sdílené složky také poskytuje možnost zadat sekundární uživatelské jméno a sekundární heslo pro poskytnutí záložních pověření v případě, že se ověření nezdaří s primárním uživatelským jménem a primárním heslem. V takovém případě nastavte následující pole pro konfiguraci úložiště zálohování založeného na _sdílení souborů._

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
> Ujistěte se, že spolehlivost úložiště splňuje nebo překračuje požadavky na spolehlivost zálohovaných dat.
>

* **Zásady uchovávání informací**: Určuje zásady pro zachování záloh v nakonfigurovaném úložišti. Podporovány jsou pouze základní zásady uchovávání informací.
    1. **Základní zásady uchovávání informací**: Tato zásada uchovávání informací umožňuje zajistit optimální využití úložiště odebráním záložních souborů, které již nejsou potřeba. `RetentionDuration`lze zadat pro nastavení časového rozpětí, pro které musí být zálohy v úložišti uchovávány. `MinimumNumberOfBackups`je volitelný parametr, který lze zadat, aby bylo zajištěno, že zadaný `RetentionDuration`počet záloh je vždy zachován bez ohledu na . Níže uvedený příklad ilustruje konfiguraci pro zachování záloh po dobu _10_ dnů a neumožňuje počet záloh jít pod _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Povolit pravidelné zálohování
Po definování zásad zálohování ke splnění požadavků na zálohování dat by měly být zásady zálohování odpovídajícím způsobem přidruženy k _aplikaci_nebo _službě_nebo _k oddílu_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarchické šíření zásad zálohování
V Service Fabric je vztah mezi aplikací, službou a oddíly hierarchický, jak je vysvětleno v [aplikačním modelu](./service-fabric-application-model.md). Zásady zálohování mohou být přidruženy k _aplikaci_, _službě_nebo _oddílu_ v hierarchii. Zásady zálohování se hierarchicky šíří na další úroveň. Za předpokladu, že existuje pouze jedna zásada zálohování vytvořená a přidružená k _aplikaci_, budou všechny stavové oddíly, které patří do všech _spolehlivých stavových služeb_ a _spolehlivé objekty actor_ _aplikace,_ zálohovány pomocí zásad zálohování. Nebo pokud je zásada zálohování přidružena ke _spolehlivé stavové službě_, budou všechny její oddíly zálohovány pomocí zásad zálohování.

### <a name="overriding-backup-policy"></a>Přepsání zásad zálohování
Může napodobuje situace, kdy je pro všechny služby aplikace vyžadováno zálohování dat se stejným plánem zálohování s výjimkou konkrétních služeb, kde je potřeba mít zálohu dat pomocí plánu s vyšší frekvencí nebo zálohování na jiný účet úložiště nebo Fileshare. Chcete-li řešit tyto scénáře, služba obnovení záloh poskytuje zařízení k přepsání šířené zásady v oboru služby a oddílu. Pokud je zásada zálohování přidružena ke _službě_ nebo _oddílu_, přepíše šíření zásad zálohování, pokud existuje.

### <a name="example"></a>Příklad

Tento příklad používá nastavení se dvěma aplikacemi, _MyApp_A_ a _MyApp_B_. MyApp_A _MyApp_A_ aplikace obsahuje dvě spolehlivé stavové služby, _SvcA1_ & _SvcA3_a jednu službu Reliable Actor _ActorA2_. _SvcA1_ obsahuje tři oddíly, zatímco _ActorA2_ a _SvcA3_ obsahují dva oddíly každý.  MyApp_B _MyApp_B_ aplikace obsahuje tři spolehlivé stavové služby, _SvcB1_, _SvcB2_a _SvcB3_. _SvcB1_ a _SvcB2_ obsahuje dva oddíly každý zatímco _SvcB3_ obsahuje tři oddíly.

Předpokládejme, že požadavky těchto aplikací na zálohování dat jsou následující:

1. MyApp_A
    1. Vytvořte denní zálohování dat pro všechny oddíly všech _spolehlivých stavových služeb_ a _spolehlivé objekty actor_ patřící do aplikace. Nahrajte záložní data do umístění _BackupStore1_.

    2. Jedna ze služeb _SvcA3_vyžaduje zálohování dat každou hodinu.

    3. Velikost dat v _oddílu SvcA1_P2_ je více, než se očekávalo a jeho záložní data by měla být uložena do jiného umístění úložiště _BackupStore2_.

2. MyApp_B
    1. Vytvořte zálohování dat každou neděli v 8:00 pro všechny oddíly služby _SvcB1._ Nahrajte záložní data do umístění _BackupStore1_.

    2. Vytvořte zálohování dat každý den v 8:00 pro oddíl _SvcB2_P1_. Nahrajte záložní data do umístění _BackupStore1_.

Chcete-li tyto požadavky na zálohování dat vyřešit, jsou vytvořeny zásady zálohování, které BP_1 BP_5 a zálohování je povoleno následujícím způsobem.
1. MyApp_A
    1. Vytvořte zásady _zálohování, BP_1_, s plánem zálohování na základě frekvence, kde je frekvence nastavena na 24 hodin. a úložiště záloh nakonfigurované pro použití umístění úložiště _BackupStore1_. Povolte tuto zásadu pro _MyApp_A_ aplikací pomocí [funkce Povolit rozhraní API pro zálohování aplikací.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) Tato akce umožňuje zálohování dat pomocí zásad zálohování _BP_1_ pro všechny oddíly _spolehlivé stavové služby_ a _spolehlivé objekty, které_ patří do aplikace _MyApp_A_.

    2. Vytvořte zásady _zálohování, BP_2_, s plánem zálohování na základě frekvence, kde je frekvence nastavena na 1 hrs. a úložiště záloh nakonfigurované pro použití umístění úložiště _BackupStore1_. Povolte tuto zásadu pro službu _SvcA3_ pomocí funkce Povolit rozhraní API [pro zálohování služby.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) Tato akce přepíše šířené zásady _BP_1_ explicitně povolenou zásadou zálohování _BP_2_ pro všechny oddíly služby _SvcA3_ vedoucí k zálohování dat pomocí zásad zálohování _BP_2_ pro tyto oddíly.

    3. Vytvořte zásady _zálohování, BP_3_, s plánem zálohování na základě frekvence, kde je frekvence nastavena na 24 hodin. a úložiště záloh nakonfigurované pro použití umístění úložiště _BackupStore2_. Povolte tuto zásadu pro _SvcA1_P2_ oddílů pomocí [funkce Povolit rozhraní API pro zálohování oddílů.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Tato akce přepíše šířené zásady _BP_1_ explicitně povolenou _BP_3_ zásad zálohování pro _oddíl SvcA1_P2_.

2. MyApp_B
    1. Vytvořte zásady _zálohování, BP_4_, s časovým plánem zálohování, kde je typ frekvence plánu nastaven na týdenní, dny spuštění jsou nastaveny na neděli a časy spuštění jsou nastaveny na 8:00. Úložiště záloh nakonfigurované pro použití umístění úložiště _BackupStore1_. Povolte tuto zásadu pro _službu SvcB1_ pomocí funkce Povolit rozhraní API [pro zálohování služby.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) Tato akce umožňuje zálohování dat pomocí zásad zálohování _BP_4_ pro všechny oddíly služby _SvcB1_.

    2. Vytvořte zásady _zálohování, BP_5_, s časovým plánem zálohování, kde je typ frekvence plánu nastaven na denní a časy spuštění jsou nastaveny na 8:00. Úložiště záloh nakonfigurované pro použití umístění úložiště _BackupStore1_. Povolte tuto zásadu pro _SvcB2_P1_ oddílů pomocí [funkce Povolit rozhraní API pro zálohování oddílů.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Tato akce umožňuje zálohování dat pomocí _BP_5_ zásad zálohování pro _SvcB2_P1_oddílů .

Následující diagram znázorňuje explicitně povolené zásady zálohování a šířené zásady zálohování.

![Hierarchie aplikací prostředků infrastruktury služeb][0]

## <a name="disable-backup"></a>Zakázat zálohování
Zásady zálohování lze zakázat, pokud není nutné zálohovat data. Zásady zálohování povolené v _aplikaci_ lze zakázat pouze ve stejné _aplikaci_ pomocí [funkce Zakázat rozhraní API pro zálohování aplikací,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) zásady zálohování povolené ve _službě_ lze zakázat ve stejné _službě_ pomocí funkce Zakázat rozhraní API pro [zálohování služby](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) a zásady zálohování povolené na _oddílu_ mohou být zakázány na stejném _oddílu_ pomocí [funkce Zakázat rozhraní](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) PARTITION Backup API.

* Zakázání zásad zálohování pro _aplikaci_ zastaví všechny pravidelné zálohování dat děje v důsledku šíření zásad zálohování na spolehlivé oddíly služby stavu nebo spolehlivé actor oddíly.

* Zakázání zásad zálohování pro _službu_ zastaví všechny pravidelné zálohování dat v důsledku šíření této zásady zálohování do oddílů _služby_.

* Zakázání zásad zálohování pro _oddíl_ zastaví všechny pravidelné zálohování dat děje z důvodu zásady zálohování na oddíl.

* Při zakázání zálohování entity (aplikace/služby/oddílu) lze nastavit na `CleanBackup` _hodnotu true,_ aby se odstranily všechny zálohy v nakonfigurovaném úložišti.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Pozastavení & obnovení zálohování
Určitá situace může vyžadovat dočasné pozastavení pravidelného zálohování dat. V takovém případě v závislosti na požadavku pozastavit zálohování rozhraní API lze použít v _aplikaci_, _služby_nebo _oddíl_. Pravidelné zálohování pozastavení je přenosité přes podstrom hierarchie aplikace od bodu, který je použit. 

* Při pozastavení je použita v _aplikaci_ pomocí [pozastavit zálohování aplikace](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API, pak všechny služby a oddíly v rámci této aplikace jsou pozastaveny pro pravidelné zálohování dat.

* Při pozastavení je _použita_ ve službě pomocí [suspend service backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) API, pak všechny oddíly v rámci této služby jsou pozastaveny pro pravidelné zálohování dat.

* Pokud je pozastavení použito v _oddílu_ pomocí [rozhraní SUSPEND Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API, pozastaví oddíly v rámci této služby, které jsou pozastaveny pro pravidelné zálohování dat.

Jakmile je potřeba pozastavení je u konce, pak pravidelné zálohování dat lze obnovit pomocí příslušného obnovení zálohování API. Pravidelné zálohování musí být obnoveno ve stejné _aplikaci_, _službě_nebo _oddílu,_ kde bylo pozastaveno.

* Pokud pozastavení byla použita v _aplikaci_, pak by měla být obnovena pomocí [resume application backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API. 

* Pokud pozastavení byla použita ve _službě_, pak by měla být obnovena pomocí [obnovit service backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) API.

* Pokud pozastavení byla použita na _oddíl_, pak by měla být obnovena pomocí [obnovit partition backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API.

### <a name="difference-between-suspend-and-disable-backups"></a>Rozdíl mezi pozastavením a zakázáním záloh
Zakázat zálohování by mělo být použito v případě, že zálohy již nejsou vyžadovány pro konkrétní aplikaci, službu nebo oddíl. Jeden může vyvolat zakázat požadavek na zálohování spolu s parametrem clean backups, což by znamenalo, že všechny stávající zálohy jsou také odstraněny. Pozastavit však má být použito ve scénářích, kdy člověk chce dočasně vypnout zálohy, jako když se místní disk zaplní nebo nahrávání zálohy selhává kvůli známému problému se sítí atd. 

Zatímco zakázat lze vyvolat pouze na úrovni, která byla dříve povolena pro zálohování explicitně však pozastavení lze použít na libovolné úrovni, která je v současné době povolena pro zálohování buď přímo nebo prostřednictvím dědičnosti / hierarchie. Například pokud je zálohování povoleno na úrovni aplikace, jeden může vyvolat zakázat pouze na úrovni aplikace však pozastavit lze vyvolat v aplikaci, jakékoli služby nebo oddíl u této aplikace. 

## <a name="auto-restore-on-data-loss"></a>Automatické obnovení při ztrátě dat
Oddíl služby může dojít ke ztrátě dat z důvodu neočekávaných chyb. Například disk pro dvě ze tří replik pro oddíl (včetně primární repliky) dojde k poškození nebo vymazání.

Když Service Fabric zjistí, že oddíl je ve `OnDataLossAsync` ztrátě dat, vyvolá metodu rozhraní na oddílu a očekává, že oddíl provést požadovanou akci, aby vyšel ze ztráty dat. V takovém případě pokud účinné zásady `AutoRestoreOnDataLoss` zálohování na `true` oddíl má příznak nastaven na pak obnovení se aktivuje automaticky pomocí nejnovější dostupné zálohy pro tento oddíl.

## <a name="get-backup-configuration"></a>Získání konfigurace zálohy
Samostatná rozhraní API jsou k dispozici pro získání informací o konfiguraci zálohy v _aplikaci_, _službě_a oboru _oddílu._ [Získat informace o konfiguraci zálohování aplikací](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [získat informace o konfiguraci zálohování služby](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)a [získat informace o konfiguraci zálohování oddílu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) jsou tato rozhraní API. Tato rozhraní API hlavně vrátí příslušné zásady zálohování, obor, ve kterém je použita zásada zálohování a podrobnosti pozastavení zálohování. Následuje stručný popis vrácených výsledků těchto api.

- Informace o konfiguraci zálohování aplikací: poskytuje podrobnosti o zásadách zálohování použitých v aplikaci a všechny přepsané zásady ve službách a oddílech, které patří do aplikace. Obsahuje také informace o pozastavení pro aplikaci a služby it a oddíly.

- Informace o konfiguraci zálohování služby: poskytuje podrobnosti o účinné zásady zálohování v provozu a rozsah, ve kterém byla použita tato zásada a všechny přepsány zásady na jeho oddíly. Obsahuje také informace o pozastavení pro službu a její oddíly.

- Informace o konfiguraci zálohování oddílu: poskytuje podrobnosti o účinné zásady zálohování na oddíl a rozsah, ve kterém byla použita tato zásada. Obsahuje také informace o pozastavení pro oddíly.

## <a name="list-available-backups"></a>Seznam dostupných záloh

Dostupné zálohy mohou být uvedeny pomocí rozhraní Get Backup List API. Výsledek volání rozhraní API zahrnuje položky informací o zálohování související se všemi zálohami dostupnými v úložišti záloh, které jsou nakonfigurovány v příslušných zásadách zálohování. Různé varianty tohoto rozhraní API jsou k dispozici seznam dostupných záloh, které patří do aplikace, služby nebo oddílu. Tato rozhraní API podporují získání _nejnovější_ dostupné zálohy všech příslušných oddílů nebo filtrování záloh na základě _počátečního data_ a _koncového data_.

Tato rozhraní API také podporují stránkování výsledků, když _maxResults_ parametr je nastaven na nenulové kladné celé číslo pak rozhraní API vrátí maximální _MaxResults_ položky zálohování info. V případě, že je k dispozici více položek informací o zálohování než hodnota _MaxResults,_ je vrácen token pokračování. Platný parametr tokenu pokračování lze použít k získání další sady výsledků. Při platnosti platné hodnoty tokenu pokračování je předán a další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Žádný token pokračování je zahrnuta v odpovědi při vrácení všech dostupných výsledků.

Následuje stručné informace o podporovaných variantách.

- [Get Application Backup List](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): Vrátí seznam záloh, které jsou k dispozici pro každý oddíl, který patří do dané aplikace Service Fabric.

- [Získat seznam zálohování služby](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): Vrátí seznam záloh, které jsou k dispozici pro každý oddíl patřící dané službě Service Fabric.
 
- [Získat seznam záloh oddílů](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): Vrátí seznam záloh dostupných pro zadaný oddíl.

## <a name="next-steps"></a>Další kroky
- [Odkaz na rozhraní REST API pro obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
