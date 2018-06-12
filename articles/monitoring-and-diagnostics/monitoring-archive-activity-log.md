---
title: Archiv protokol činnosti Azure
description: Archivujte váš protokol činnosti Azure pro dlouhodobé uchovávání v účtu úložiště.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 508b2f615819f20a717065d8fff25beff64957d5
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263425"
---
# <a name="archive-the-azure-activity-log"></a>Archiv protokol činnosti Azure
V tomto článku jsme ukazují, jak pomocí portálu Azure, rutiny prostředí PowerShell nebo rozhraní příkazového řádku a platformy pro archivaci vaše [ **protokol činnosti Azure** ](monitoring-overview-activity-logs.md) v účtu úložiště. Tato možnost je užitečná, pokud chcete uchovat déle, než 90 dní (s plnou kontrolu nad zásady uchovávání informací) pro audit, statické analýzy nebo zálohování aktivity protokolu. Pokud potřebujete události uchovávány 90 dnů nebo méně není nutné nastavit archivace na účet úložiště, protože aktivity protokolu události se zachovají v platformy Azure za 90 dnů bez povolení archivace.

## <a name="prerequisites"></a>Požadavky
Než začnete, budete muset [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account) ke kterému může archivaci protokolu aktivit. Důrazně doporučujeme nepoužívejte stávající účet úložiště, který má jiné, -monitoring data v ní uloženy, takže může lépe řídit přístup k datům monitorování. Ale pokud jsou také archivace diagnostické protokoly a metriky na účet úložiště, má smysl pro použití tohoto účtu úložiště pro protokol aktivit také pro monitorování všech dat v centrálním umístění. Účet úložiště, které používáte, musí být účet úložiště obecné účely, nikoli účet úložiště objektů blob. Účet úložiště nemusí být ve stejném předplatném jako předplatné emitování protokoly tak dlouho, dokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC do oba odběry.

> [!NOTE]
>  Nelze archivovat aktuálně dat do úložiště účtu, který za zabezpečené virtuální sítě.

## <a name="log-profile"></a>Profil protokolu
K archivaci protokolu aktivit pomocí kteréhokoli z následujících metod, můžete nastavit **profil protokolu** pro předplatné. Profil protokolu definuje typ události, které jsou uložené nebo prostřednictvím datového proudu a výstupy – úložiště účet nebo události rozbočovače. Definuje také zásady uchovávání informací (počet dní, které chcete zachovat) pro události, které jsou uložené v účtu úložiště. Pokud zásady uchovávání informací je nastaven na hodnotu nula, události jsou uloženy po neomezenou dobu. Jinak to je možné nastavit na jakoukoli hodnotu od 1 do 2147483647. Zásady uchovávání informací jsou použité denní, takže na konci za den (UTC), protokoly dnem, který je teď nad rámec uchovávání se zásada odstraní. Například pokud jste měli zásady uchovávání informací jeden den, od začátku dnešní den protokoly z včerejšek před den by odstraněn. Proces odstraňování začíná na půlnoc UTC, ale Všimněte si, že může trvat až 24 hodin protokolů k odstranění z vašeho účtu úložiště. [Další informace o protokolu profily zde](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Archivovat protokol aktivit pomocí portálu
1. Na portálu, klikněte **protokol aktivit** na levé straně navigační odkaz. Pokud nevidíte odkaz pro protokol aktivit, klikněte **všechny služby** první odkaz.
   
    ![Přejděte do okna Protokol aktivit](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. V horní části okna klikněte na tlačítko **exportovat**.
   
    ![Klikněte na tlačítko Export](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. V okně, který se zobrazí, zaškrtněte políčko pro **exportovat do účtu úložiště** a vyberte účet úložiště.
   
    ![Nastavit účet úložiště](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Pomocí posuvníku nebo textové pole, zadejte počet dní, pro které by měly být udržovány aktivity protokolu události v účtu úložiště. Pokud chcete, aby vaše data uchovávané v účtu úložiště bez omezení, nastavte toto číslo na hodnotu nula.
5. Klikněte na **Uložit**.

## <a name="archive-the-activity-log-via-powershell"></a>Archiv protokol aktivit pomocí prostředí PowerShell

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| StorageAccountId |Ano |ID prostředku účtu úložiště, který má být uložen protokoly aktivity. |
| Umístění |Ano |Seznam oddělený čárkami oblastí, pro které chcete shromažďovat aktivity protokolu události. Můžete zobrazit seznam všech oblastech pro předplatné s použitím `(Get-AzureRmLocation).Location`. |
| retentionInDays |Ne |Počet dní pro události, které by měl být zachován, od 1 do 2147483647. Hodnota nula ukládá protokoly bez omezení (navždy). |
| Kategorie |Ne |Seznam oddělený čárkami kategorií událostí, které by měl být shromážděny. Možné hodnoty jsou zápisu, odstranění a akce.  Pokud není zadaná, pak všechny možné hodnoty jsou považovány za |

## <a name="archive-the-activity-log-via-cli"></a>Archiv protokol aktivit prostřednictvím rozhraní příkazového řádku

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| jméno |Ano |Název vašeho profilu protokolu. |
| id účtu úložiště |Ano |ID prostředku účtu úložiště, který má být uložen protokoly aktivity. |
| Umístění |Ano |Oddělených mezerami seznam oblastí, pro které chcete shromažďovat aktivity protokolu události. Můžete zobrazit seznam všech oblastech pro předplatné s použitím `az account list-locations --query [].name`. |
| dny |Ano |Počet dní pro události, které by měl být zachován, od 1 do 2147483647. Hodnota nula bude po neomezenou dobu ukládání protokolů (navždy).  Pokud nula, pak povoleno parametr by měl být nastaven na hodnotu true. |
|povoleno | Ano |True nebo False.  Umožňuje povolit nebo zakázat zásady uchovávání informací.  V případě hodnoty True, musí být parametr dní hodnotu větší než 0.
| Kategorie |Ano |Oddělených mezerami seznam kategorií událostí, které by měl být shromážděny. Možné hodnoty jsou zápisu, odstranění a akce. |

## <a name="storage-schema-of-the-activity-log"></a>Schéma úložiště protokolu činnosti
Jakmile jste nastavili archivace, kontejner úložiště bude vytvořen v účtu úložiště Jakmile dojde k aktivity protokolu události. Objekty BLOB v kontejneru použijte stejný formát přes protokol aktivit a diagnostické protokoly. Struktura tyto objekty BLOB je:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ID_předplatného}/y={čtyřmístné_číslo_roku}/m={dvoumístné_číslo_měsíce}/d={dvoumístné_číslo_dne}/h={dvoumístné_číslo_hodiny_ve_24hodinovém_formátu}/m=00/PT1H.json
> 
> 

Například může být název objektu blob:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Každý objekt blob PT1H.json obsahuje objekt blob JSON událostí, které nastaly během hodiny zadaného v adrese URL objektu blob (například h = 12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Hodnota minutu (m = 00) je vždy 00, protože aktivity protokolu události jsou rozdělená do jednotlivých objektů blob za hodinu.

V souboru PT1H.json se ukládají všechny události v poli "záznamy" následující tento formát:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Název elementu | Popis |
| --- | --- |
| time |Časové razítko při zpracování požadavku odpovídající události služby Azure vygenerovalo událost. |
| resourceId |ID prostředku ovlivněné prostředku. |
| operationName |Název operace |
| category |Kategorie Akce, např. Zápis, čtení, akce. |
| resultType |Typ výsledku, např. Úspěch, chyba, spuštění |
| resultSignature |Závisí na typu prostředku. |
| durationMs |Doba trvání operace v milisekundách |
| callerIpAddress |IP adresa uživatele, který se má provést operace, deklarace hlavní název uživatele nebo název SPN deklarace identity na základě dostupnosti. |
| correlationId |Obvykle GUID ve formátu řetězce. Události, které sdílejí correlationId patřit do stejné uber akce. |
| identity |Objekt blob JSON s popisem autorizace a deklarace identity. |
| Autorizace |Objekt BLOB RBAC vlastností události. Obvykle zahrnuje vlastnosti "action", "role" a "obor". |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritická", "Chyba", "Upozornění", "Informační" a "Podrobné" |
| location |Oblast, ve které došlo k umístění (nebo globální). |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. |

> [!NOTE]
> Vlastnosti a využití tyto vlastnosti se může lišit v závislosti na prostředek.
> 
> 

## <a name="next-steps"></a>Další postup
* [Stáhnout objekty BLOB pro analýzu](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Datový proud protokolu aktivit do centra událostí](monitoring-stream-activity-logs-event-hubs.md)
* [Další informace o protokolu aktivit](monitoring-overview-activity-logs.md)

