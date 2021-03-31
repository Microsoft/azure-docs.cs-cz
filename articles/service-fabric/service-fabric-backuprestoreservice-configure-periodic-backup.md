---
title: Principy konfigurace pravidelného zálohování
description: Pomocí funkce periodického zálohování a obnovení Service Fabric můžete nakonfigurovat pravidelné zálohování spolehlivých stavových služeb nebo Reliable Actors.
ms.topic: article
ms.date: 2/01/2019
ms.openlocfilehash: 2607502af44b178131820d78f23bcdf4e32454a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018881"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Princip pravidelné konfigurace zálohování v Azure Service Fabric

Konfigurace pravidelného zálohování vašich spolehlivých stavových služeb nebo Reliable Actors skládá z následujících kroků:

1. **Vytváření zásad zálohování**: v tomto kroku se vytvoří jedna nebo víc zásad zálohování v závislosti na požadavcích.

2. **Povolení zálohování**: v tomto kroku přidružíte zásady zálohování vytvořené v **kroku 1** k požadovaným entitám, _aplikacím_, _službám_ nebo _oddílům_.

## <a name="create-backup-policy"></a>Vytvořit zásady zálohování

Zásady zálohování se skládají z následujících konfigurací:

* **Automatické obnovení při ztrátě dat**: Určuje, jestli se má automaticky aktivovat obnovení pomocí nejnovější dostupné zálohy pro případ, že dojde k události ztráty dat.
> [!NOTE]
> V produkčních clusterech se nedoporučuje nastavení automatického obnovení.
>

* **Max. přírůstkové zálohování**: definuje maximální počet přírůstkových záloh, které se mají mezi dvěma úplnými zálohami považovat. Maximální přírůstkové zálohování určuje horní limit. Před dokončením zadaného počtu přírůstkových záloh můžete provést úplnou zálohu v jedné z následujících podmínek:

    1. Replika nikdy netrvalou úplnou zálohu, protože se stala primární.

    2. Některé záznamy protokolu od posledního zálohování byly zkráceny.

    3. Replika vyhověla MaxAccumulatedBackupLogSizeInMB limitu.

* **Plán zálohování**: čas nebo četnost, s jakou se mají provádět pravidelné zálohy. Jedna může naplánovat opakované zálohování v zadaném intervalu nebo v pevně dané době každý den/týden.

    1. **Plán zálohování podle četnosti**: Tento typ plánu by měl být použit v případě, že je potřeba provést zálohování dat v pevně stanovených intervalech. Požadovaný časový interval mezi dvěma po sobě jdoucími zálohami je definován pomocí formátu ISO8601. Plán zálohování podle frekvencí podporuje rozlišení intervalu až minutu.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Plán zálohování na základě času**: Tento typ plánu by měl být použit v případě, že je potřeba provést zálohování dat v určitých časech dne nebo týdne. Typ frekvence plánu může být buď každý den, nebo každý týden.
        1. **_Denní_ plán zálohování na základě času**: Tento typ plánu by měl být použit v případě, že je potřeba provést zálohování dat v určitém čase daného dne. Chcete-li tuto hodnotu určit, nastavte `ScheduleFrequencyType` na hodnotu _denně_ a `RunTimes` v poli Formát ISO8601 nastavte na seznam požadovaného času a bude se ignorovat datum zadané společně s časem. Například `0001-01-01T18:00:00` reprezentuje _6:00_ . den a ignoruje se datum – část _0001-01-01_. Následující příklad ilustruje konfiguraci pro aktivaci každodenního zálohování v _9:00_ a _6:00 odp_ .

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

        2. **_Týdenní_ plán zálohování založený na čase**: Tento typ plánu by měl být použit v případě, že je potřeba provést zálohu dat v určitém čase daného dne. Pokud to chcete určit, nastavte `ScheduleFrequencyType` na _týdně_; nastavit `RunDays` na seznam dnů v týdnu, kdy se má spustit zálohování a nastavit `RunTimes` na seznam požadovaného času během dne ve formátu ISO8601, datum zadané společně s časem se bude ignorovat. Seznam dnů v týdnu, kdy se má aktivovat pravidelná záloha Následující příklad ilustruje konfiguraci pro aktivaci každodenního zálohování v _9:00_ a _6:00 odpoledne_ během pondělí do pátku.

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

* **Úložiště zálohování**: Určuje umístění pro nahrávání záloh. Úložištěm může být úložiště objektů blob Azure nebo sdílení souborů.
    1. **Úložiště objektů BLOB v Azure**: Tento typ úložiště by měl být vybraný, pokud je potřeba ukládat vygenerované zálohy v Azure. Tento typ úložiště můžou používat _samostatné_ clustery i clustery _založené na Azure_ . Popis tohoto typu úložiště vyžaduje připojovací řetězec a název kontejneru, do kterého se mají nahrát zálohy. Pokud kontejner se zadaným názvem není k dispozici, vytvoří se během nahrávání zálohy.

        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

        > [!NOTE]
        > Služba obnovení záloh nefunguje se službou v1 Azure Storage.
        >

    2. **Sdílená složka**: Tento typ úložiště by měl být vybraný pro _samostatné_ clustery, pokud je potřeba ukládat místně zálohovaná data. Popis pro tento typ úložiště vyžaduje cestu ke sdílené složce, ve které je nutné nahrávat zálohy. Přístup ke sdílené složce se dá nakonfigurovat pomocí jedné z následujících možností:
        1. _Integrované ověřování systému Windows_, kde je přístup ke sdílené složce k dispozici všem počítačům patřícím do clusteru Service Fabric. V takovém případě nastavte následující pole pro konfiguraci úložiště zálohování založeného na _sdílení souborů_ .

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Ochrana sdílené složky pomocí uživatelského jména a hesla_, kde je přístup ke sdílené složce k dispozici konkrétním uživatelům. Specifikace úložiště sdílené složky také nabízí možnost zadat sekundární uživatelské jméno a sekundární heslo pro poskytnutí přihlašovacích údajů zpátky pro případ, že ověřování selhalo s primárním uživatelským jménem a primárním heslem. V takovém případě nastavte následující pole pro konfiguraci úložiště zálohování založeného na _sdílení souborů_ .

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
> Zajistěte, aby spolehlivost úložiště splňovala nebo překročila požadavky na spolehlivost zálohovaných dat.
>

* **Zásady uchovávání informací**: Určuje zásadu pro uchovávání záloh v nakonfigurovaném úložišti. Podporují se jenom základní zásady uchovávání informací.
    1. **Základní zásady uchovávání informací**: tyto zásady uchovávání informací umožňují zajistit optimální využití úložiště odebráním záložních souborů, které nejsou potřeba. `RetentionDuration` dá se zadat, chcete-li nastavit časový rozsah, pro který se musí zálohy uchovávat v úložišti. `MinimumNumberOfBackups` je volitelný parametr, který se dá určit, aby se zajistilo, že zadaný počet záloh vždycky zůstane bez ohledu na `RetentionDuration` . Následující příklad ilustruje konfiguraci pro uchovávání záloh po dobu _10_ dnů a nepovoluje počet záloh, které se budou nacházet pod _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Povolit pravidelná zálohování
Po definování zásad zálohování tak, aby splňovaly požadavky na zálohování dat, by se zásady zálohování měly vhodně přidružit buď k _aplikaci_, nebo ke _službě_, nebo k _oddílu_.

> [!NOTE]
> Před povolením zálohování zajistěte, aby neprobíhaly žádné upgrady aplikací.
>

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarchické šíření zásad zálohování
V Service Fabric vztah mezi aplikací, službou a oddíly je hierarchický, jak je vysvětleno v [modelu aplikace](./service-fabric-application-model.md). Zásady zálohování je možné přidružit buď k _aplikaci_, _službě_, nebo k _oddílu_ v hierarchii. Zásady zálohování se šíří hierarchicky do další úrovně. Za předpokladu, že se pro _aplikaci_ vytvoří jenom jedna zásada zálohování, budou se všechny stavové oddíly patřící do všech _spolehlivých stavových služeb_ a _Reliable Actors_ _aplikace_ zálohovat pomocí zásad zálohování. Nebo pokud jsou zásady zálohování přidružené ke _spolehlivé stavové službě_, všechny její oddíly se zálohují pomocí zásad zálohování.

### <a name="overriding-backup-policy"></a>Přepsání zásad zálohování
Může se jednat o situaci, kdy je pro všechny služby aplikace vyžadováno zálohování dat se stejným plánem zálohování s výjimkou konkrétních služeb, kde je potřeba mít zálohu dat pomocí vyššího plánu četnosti nebo zálohování na jiný účet úložiště nebo sdílení souborů. Pro vyřešení takových scénářů nabízí služba obnovení zálohovací služby možnost přepsat rozšířenou zásadu v oboru služeb a oddílu. Pokud jsou zásady zálohování přidružené k _službě_ nebo _oddílu_, potlačí zásady šíření záloh, pokud nějaké existují.

### <a name="example"></a>Příklad

V tomto příkladu se používá instalační program se dvěma aplikacemi, _MyApp_A_ a _MyApp_B_. Aplikace _MyApp_A_ obsahuje dvě spolehlivé stavové služby, _SvcA1_  &  _SvcA3_ a jednu službu Reliable Actor, _ActorA2_. _SvcA1_ obsahuje tři oddíly, zatímco _ActorA2_ a _SvcA3_ obsahují dva oddíly.  Aplikace _MyApp_B_ obsahuje tři spolehlivé stavové služby, _SvcB1_, _SvcB2_ a _SvcB3_. _SvcB1_ a _SvcB2_ obsahují dva oddíly, zatímco _SvcB3_ obsahuje tři oddíly.

Předpokládejme, že tyto aplikace tyto požadavky na zálohování dat jsou následující.

1. MyApp_A
    1. Vytvořte každodenní zálohu dat pro všechny oddíly všech _spolehlivých stavových služeb_ a _Reliable Actors_ patřících do aplikace. Nahrajte data zálohy do umístění _BackupStore1_.

    2. Jedna ze služeb, _SvcA3_, vyžaduje zálohování dat každou hodinu.

    3. Velikost dat v oddílu _SvcA1_P2_ je větší, než se očekávalo, a data zálohy by se měla ukládat do jiného umístění úložiště _BackupStore2_.

2. MyApp_B
    1. Vytvoří zálohu dat každé neděle v 8:00 pro všechny oddíly služby _SvcB1_ . Nahrajte data zálohy do umístění _BackupStore1_.

    2. Vytvoří zálohu dat každý den v 8:00 _SvcB2_P1_ pro oddíl. Nahrajte data zálohy do umístění _BackupStore1_.

Aby bylo možné tyto požadavky na zálohování dat vyřešit, jsou vytvořeny zásady zálohování BP_1 k BP_5 jsou vytvořeny a zálohování je povoleno následujícím způsobem.
1. MyApp_A
    1. Vytvořte zásady zálohování _BP_1_ s plánem zálohování podle frekvencí, kde frekvence je nastavená na 24 hodin. a úložiště zálohování je nakonfigurované tak, aby používalo umístění úložiště _BackupStore1_. Povolte tuto zásadu pro _MyApp_A_ aplikací pomocí povolení rozhraní API pro [zálohování aplikací](/rest/api/servicefabric/sfclient-api-enableapplicationbackup) . Tato akce umožňuje zálohování dat pomocí zásad zálohování _BP_1_ pro všechny oddíly _spolehlivých stavových služeb_ a _Reliable Actors_ patřících do _MyApp_A_ aplikací.

    2. Vytvořte zásady zálohování _BP_2_ s plánem zálohování podle frekvencí, kde frekvence je nastavená na 1 hod. a úložiště zálohování je nakonfigurované tak, aby používalo umístění úložiště _BackupStore1_. Povolte tuto zásadu pro Service _SvcA3_ pomocí povolení rozhraní API pro [zálohování služby](/rest/api/servicefabric/sfclient-api-enableservicebackup) . Tato akce přepíše rozšířenou zásadu _BP_1_ tím, že explicitně povolí zásady zálohování _BP_2_ pro všechny oddíly služby Service _SvcA3_ , což vede k zálohování dat pomocí zásad zálohování _BP_2_ pro tyto oddíly.

    3. Vytvořte zásady zálohování _BP_3_ s plánem zálohování podle frekvencí, kde frekvence je nastavená na 24 hodin. a úložiště zálohování je nakonfigurované tak, aby používalo umístění úložiště _BackupStore2_. Povolte tuto zásadu pro _SvcA1_P2_ oddílů pomocí povolení rozhraní API pro [zálohování oddílů](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Tato akce přepíše rozšířenou zásadu _BP_1_ tím, že explicitně povolí zásady zálohování _BP_3_ pro _SvcA1_P2_ oddílů.

2. MyApp_B
    1. Vytvořte zásady zálohování _BP_4_ s časovým plánem zálohování na základě času, kde je typ frekvence plánování nastavený na týdně, dny spuštění jsou nastavené na neděli a časy spuštění jsou nastavené na 8:00. Úložiště zálohování je nakonfigurované tak, aby používalo umístění úložiště _BackupStore1_. Povolte tuto zásadu pro Service _SvcB1_ pomocí povolení rozhraní API pro [zálohování služby](/rest/api/servicefabric/sfclient-api-enableservicebackup) . Tato akce povolí zálohování dat pomocí zásad zálohování _BP_4_ pro všechny oddíly služby Service _SvcB1_.

    2. Vytvořte zásady zálohování _BP_5_ s časovým plánem zálohování na základě času, kde je typ frekvence plánování nastavený na denně a časy spuštění jsou nastavené na 8:00. Úložiště zálohování je nakonfigurované tak, aby používalo umístění úložiště _BackupStore1_. Povolte tuto zásadu pro _SvcB2_P1_ oddílů pomocí povolení rozhraní API pro [zálohování oddílů](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Tato akce povolí zálohování dat pomocí zásad zálohování _BP_5_ pro _SvcB2_P1_ oddílů.

Následující diagram znázorňuje explicitně povolené zásady zálohování a zásady šíření záloh.

![Service Fabric hierarchie aplikace][0]

## <a name="disable-backup"></a>Zákaz zálohování
Zásady zálohování je možné zakázat, pokud není nutné data zálohovat. Zásady zálohování povolené v _aplikaci_ je možné zakázat jenom ve stejné _aplikaci_ , která používá zakázané rozhraní API pro [zálohování aplikací](/rest/api/servicefabric/sfclient-api-disableapplicationbackup) . zásady zálohování povolené v rámci _služby_ můžou být ve stejné _službě_ zakázané pomocí rozhraní [disabled Backup](/rest/api/servicefabric/sfclient-api-disableservicebackup) API a zásady zálohování povolené v _oddílu_ můžou být zakázané ve stejném _oddílu_ pomocí rozhraní API pro [zálohování oddílů](/rest/api/servicefabric/sfclient-api-disablepartitionbackup) .

* Zakázáním zásad zálohování pro _aplikaci_ se zastaví všechna pravidelná zálohování dat, která se děje v důsledku šíření zásad zálohování do spolehlivých oddílů stavových služeb nebo spolehlivých oddílů actor.

* Zakázáním zásad zálohování pro _službu_ dojde k zastavení všech pravidelného zálohování dat v důsledku šíření těchto zásad zálohování do oddílů _služby_.

* Zakázání zásad zálohování pro _oddíl_ zastaví všechna pravidelná zálohování dat, která se děje v důsledku zásad zálohování v oddílu.

* Při zakázání zálohování entity (aplikace/služby/oddílu) je `CleanBackup` možné nastavit na _hodnotu true_ , aby se odstranily všechny zálohy v nakonfigurovaném úložišti.
    ```json
    {
        "CleanBackup": true 
    }
    ```
> [!NOTE]
> Zajistěte, aby před vypnutím zálohování neprobíhaly žádné upgrady aplikací.
>

## <a name="suspend--resume-backup"></a>Pozastavit & pokračování v zálohování
Určitá situace může vyžadovat dočasné pozastavení pravidelného zálohování dat. V takové situaci se v závislosti na požadavku dá pozastavit zálohovací rozhraní API použít na _aplikaci_, _službu_ nebo _oddíl_. Přerušení pravidelného zálohování je přenositelný přes podstrom hierarchie aplikace z bodu, který se používá. 

* Pokud je v _aplikaci_ použito pozastavení, které používá rozhraní API pro zablokování [aplikace](/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) , pak se všechny služby a oddíly v této aplikaci pozastaví na pravidelné zálohování dat.

* Když se na _službu_ používá pozastavení pomocí rozhraní API pro [zálohování služby](/rest/api/servicefabric/sfclient-api-suspendservicebackup) , pak se všechny oddíly v této službě pozastaví, aby se pravidelně zálohovaná data používala.

* Když se v _oddílu_ použije pozastavení rozhraní API pro [pozastavení oddílu](/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) , pozastaví se při pravidelném zálohování dat oddíly v této službě.

Až bude potřeba pozastavení převzít, můžete pravidelná zálohování dat obnovit pomocí příslušného obnovení záložního rozhraní API. Pravidelná záloha musí být obnovena ve stejné _aplikaci_, _službě_ nebo _oddílu_ , kde byla pozastavena.

* Pokud bylo na _aplikaci_ použito pozastavení, mělo by být obnoveno pomocí rozhraní [Resume API pro zálohování aplikací](/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) . 

* Pokud se v rámci _služby_ použilo pozastavení, mělo by být obnoveno pomocí rozhraní API pro [zálohování zálohovací služby](/rest/api/servicefabric/sfclient-api-resumeservicebackup) .

* Pokud se v _oddílu_ použilo pozastavení, mělo by se pokračovat pomocí rozhraní API pro [zálohování záložního oddílu](/rest/api/servicefabric/sfclient-api-resumepartitionbackup) .

### <a name="difference-between-suspend-and-disable-backups"></a>Rozdíl mezi pozastavením a zakázáním záloh
Pokud již nejsou pro určitou aplikaci, službu nebo oddíl požadovány zálohy, je vhodné použít příkaz Zakázat zálohování. Jedna z těchto možností může vyvolat zákaz žádosti o zálohování spolu s parametrem vyčistit zálohy, což by znamenalo, že se odstranila i všechna existující zálohování. Pozastavení je ale vhodné použít ve scénářích, kdy si jeden chce dočasně vypnout zálohování, jako když se místní disk změní na plný nebo když se zálohování nedaří kvůli známému problému se sítí atd. 

I když se možnost zakázat dá vyvolávat jenom na úrovni, která byla dříve povolená pro zálohování, ale pozastavení se dá použít na libovolné úrovni, která je aktuálně povolená pro zálohování přímo nebo přes dědičnost nebo hierarchii. Pokud je například na úrovni aplikace zapnuté zálohování, může se jedna z nich vyvolat zakázat jenom na úrovni aplikace. pozastavení je ale možné vyvolat v rámci aplikace, libovolné služby nebo oddílu v této aplikaci. 

## <a name="auto-restore-on-data-loss"></a>Automatické obnovení při ztrátě dat
Oddíl služby může přijít o data z důvodu neočekávané chyby. Například disk pro dvě z následujících replik pro oddíl (včetně primární repliky) je poškozený nebo smazáný.

Pokud Service Fabric zjistí, že se oddíl nachází v případě ztráty dat, vyvolá `OnDataLossAsync` metodu rozhraní v oddílu a očekává, že oddíl provede požadovanou akci, aby mohla přijít o ztrátu dat. V takovém případě platí, že pokud má zásada účinnosti zálohování v oddílu `AutoRestoreOnDataLoss` nastavenou hodnotu, bude `true` obnovení automaticky aktivováno pomocí nejnovější dostupné zálohy pro tento oddíl.

> [!NOTE]
> V produkčních clusterech se nedoporučuje nastavení automatického obnovení.
>

## <a name="get-backup-configuration"></a>Získat konfiguraci zálohování
K dispozici jsou samostatná rozhraní API pro získání informací o konfiguraci zálohování v oboru _aplikací_, _služeb_ a _oddílu_ . [Získat informace o konfiguraci zálohování aplikace](/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [získat informace o konfiguraci zálohování služby](/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)a [získat informace o konfiguraci zálohování oddílu](/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) jsou tato rozhraní API v uvedeném pořadí. Hlavně tato rozhraní API vracejí příslušné zásady zálohování, rozsah, ve kterém jsou zásady zálohování použity, a podrobnosti o pozastavení zálohování. Následuje stručný popis vrácených výsledků těchto rozhraní API.

- Informace o konfiguraci zálohování aplikace: poskytuje podrobné informace o zásadách zálohování použitých v aplikaci a všech riddench zásad na službách a oddílech, které patří k aplikaci. Zahrnuje také informace o pozastavení pro aplikace a IT služby a oddíly.

- Informace o konfiguraci zálohování služby: poskytuje podrobné informace o efektivních zásadách zálohování při provozu a rozsahu, ve kterém se tato zásada použila, a všech riddench zásad ve svých oddílech. Obsahuje také informace o pozastavení pro službu a její oddíly.

- Informace o konfiguraci zálohování oddílů: poskytuje podrobné informace o efektivních zásadách zálohování v oddílu a rozsahu, ve kterém se tato zásada použila. Obsahuje také informace o pozastavení oddílů.

## <a name="list-available-backups"></a>Zobrazit seznam dostupných záloh

K dispozici můžou být zálohy uvedené pomocí získat rozhraní API pro seznam zálohování. Výsledkem volání rozhraní API jsou informace o zálohování, které souvisí se všemi zálohami dostupnými v úložišti zálohování, které je nakonfigurované v příslušných zásadách zálohování. K dispozici jsou různé varianty tohoto rozhraní API, které jsou uvedené v seznamu dostupných záloh, které patří k aplikaci, službě nebo oddílu. Tato rozhraní API podporují získání _nejnovější_ dostupné zálohy všech příslušných oddílů nebo filtrování záloh na základě _počátečního_ a _koncového data_.

Tato rozhraní API také podporují stránkování výsledků, pokud je parametr _MaxResults_ nastaven na nenulové kladné celé číslo, pak rozhraní API vrátí údaje o maximálním _MaxResults_ zálohování. V případě jsou k dispozici více položek informací o zálohování než hodnota _MaxResults_ , pak je vrácen token pokračování. K získání další sady výsledků lze použít platný parametr tokenu pokračování. Když je předána platná hodnota tokenu pokračování dalšímu volání rozhraní API, vrátí rozhraní API další sadu výsledků. V odpovědi není zahrnutý žádný token pro pokračování, pokud jsou vráceny všechny dostupné výsledky.

Níže jsou uvedené stručné informace o podporovaných variantách.

- [Získat seznam zálohování aplikace](/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): vrátí seznam záloh dostupných pro každý oddíl, který patří do dané Service Fabric aplikace.

- [Získat seznam zálohování služby](/rest/api/servicefabric/sfclient-api-getservicebackuplist): vrátí seznam záloh dostupných pro každý oddíl, který patří do dané Service Fabric služby.
 
- [Získat seznam záloh oddílu](/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): vrátí seznam záloh dostupných pro zadaný oddíl.

## <a name="next-steps"></a>Další kroky
- [Odkaz na REST API obnovení zálohy](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
