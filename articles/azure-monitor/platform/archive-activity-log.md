---
title: Archivace protokolu aktivit Azure
description: Protokol aktivit Azure pro dlouhodobé uchovávání v účtu úložiště můžete archivovat.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 9f7364f65d008753bbcfc105de898d0d39226f27
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53388771"
---
# <a name="archive-the-azure-activity-log"></a>Archivace protokolu aktivit Azure
V tomto článku vám ukážeme, jak pomocí webu Azure portal, rutin Powershellu nebo CLI pro různé platformy pro archivaci vaše [ **protokolu aktivit Azure** ](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) v účtu úložiště. Tato možnost je užitečná, pokud byste chtěli zachovat váš protokol aktivit déle než 90 dnů (s úplnou kontrolou nad zásady uchovávání informací) pro audit, statické analýzy nebo pro zálohování. Pokud potřebujete události uchovávat po 90 dní nebo méně nepotřebujete nastavit archivaci do účtu úložiště, protože události protokolu aktivit se zachovají na platformě Azure po dobu 90 dnů bez povolení archivace.

> [!WARNING]
> 1. listopadu 2018 se formát dat protokolů v účtu úložiště změní na řádky JSON. [Informace o dopadu a postup pro aktualizaci nástrojů, aby si s novým formátem poradily, najdete v tomto článku](./../../monitoring-and-diagnostics/monitor-diagnostic-logs-append-blobs.md). 
>
> 

## <a name="prerequisites"></a>Požadavky
Než začnete, budete muset [vytvořit účet úložiště](../../storage/common/storage-quickstart-create-account.md) ke kterému je možné protokol aktivit můžete archivovat. Důrazně doporučujeme, že nepoužíváte existující účet úložiště, který obsahuje jiné, než monitorování data uložená v něm může lépe řídit přístup k datům monitorování. Ale pokud jsou také archivace diagnostických protokolů a metrik na účet úložiště, může mít smysl použít tento účet úložiště pro váš protokol aktivit také zajistit všechna data monitorování v centrálním umístění. Účet úložiště nemusí být ve stejném předplatném jako předplatné, které vysílá protokoly za předpokladu, že uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma předplatným.

> [!NOTE]
>  Nelze aktuálně archivace dat do účtu služby storage vytvořené za zabezpečené virtuální síti.

## <a name="log-profile"></a>Profilu protokolu
Archivace protokolu aktivit některou z níže uvedených metod, že nastavíte **profilu protokolu** pro odběr. Profil protokolu definuje typ události, které jsou uložená nebo prostřednictvím datového proudu a výstupy – úložiště účtu a/nebo event hub. Definuje také zásady uchovávání informací (počet dní uchování) pro události, které jsou uložené v účtu úložiště. Pokud zásady uchovávání informací je nastavena na hodnotu nula, události jsou uloženy po neomezenou dobu. V opačném případě to lze nastavit na libovolnou hodnotu mezi 1 a 2147483647. Zásady uchovávání informací jsou použité za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní. Proces odstraňování začíná o půlnoci UTC, ale Všimněte si, že může trvat až 24 hodin pro protokoly, které mají být odstraněny z vašeho účtu úložiště. [Další informace o protokolu profily zde](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Archivace protokolu aktivit na portálu
1. Na portálu klikněte na tlačítko **protokolu aktivit** odkaz v levé navigaci. Pokud nevidíte odkaz pro protokol aktivit, klikněte na tlačítko **všechny služby** nejdřív propojit.
   
    ![Přejděte do okna protokolu aktivit](media/archive-activity-log/activity-logs-portal-navigate-v2.png)
2. V horní části okna klikněte na tlačítko **exportovat do centra událostí**.
   
    ![Klikněte na tlačítko pro Export](media/archive-activity-log/activity-logs-portal-export-v2.png)
3. V okně, které se zobrazí, zaškrtněte políčko u **exportovat do účtu úložiště** a vyberte účet úložiště.
   
    ![Nastavit účet úložiště](media/archive-activity-log/act-log-portal-export-blade.png)
4. Pomocí posuvník nebo textové pole, definujte počet dní (0 až 365), pro které události protokolu aktivit se uchovávají v účtu úložiště. Pokud chcete, aby vaše data ukládají v účtu úložiště po neomezenou dobu, nastavte toto číslo na hodnotu nula. Pokud je potřeba zadat číslo větší než 365 dnů, použijte PowerShell nebo rozhraní příkazového řádku metody popsané níže.
5. Klikněte na **Uložit**.

## <a name="archive-the-activity-log-via-powershell"></a>Archivace protokolu aktivit přes PowerShell

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
| StorageAccountId |Ano |ID prostředku účtu úložiště, ke kterému má být uložen protokolů aktivit. |
| Umístění |Ano |Čárkami oddělený seznam oblasti, pro které chcete shromažďovat události protokolu aktivit. Můžete zobrazit seznam všech oblastí pro vaše předplatné pomocí `(Get-AzureRmLocation).Location`. |
| RetentionInDays |Ne |Počet dní pro události, které by měla být zachována, od 1 do 2147483647. Hodnota nula ukládá protokoly po neomezenou dobu (trvale). |
| Kategorie |Ne |Čárkami oddělený seznam kategorie událostí, které se mají shromažďovat. Možné hodnoty jsou Write, Delete a akce.  Pokud se nezadá, pak všechny možné hodnoty jsou považovány za |

## <a name="archive-the-activity-log-via-cli"></a>Archivace protokolu aktivit prostřednictvím rozhraní příkazového řádku

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| jméno |Ano |Název profilu protokolu. |
| id účtu úložiště |Ano |ID prostředku účtu úložiště, ke kterému má být uložen protokolů aktivit. |
| umístění |Ano |Místo oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. Můžete zobrazit seznam všech oblastí pro vaše předplatné pomocí `az account list-locations --query [].name`. |
| dny |Ano |Počet dní pro události, které by měla být zachována, od 1 do 2147483647. Hodnota nula bude po neomezenou dobu ukládání protokolů (trvale).  Pokud nula, povolené parametr by měl být nastavte na hodnotu true. |
|povoleno | Ano |True nebo False.  Umožňuje povolit nebo zakázat zásady uchovávání informací.  Pokud je hodnota True, musí být parametr dní hodnotu větší než 0.
| categories |Ano |Místo oddělený seznam kategorie událostí, které se mají shromažďovat. Možné hodnoty jsou Write, Delete a akce. |

## <a name="storage-schema-of-the-activity-log"></a>Schéma úložiště protokolu aktivit
Po jste nastavili archivaci, kontejner úložiště se vytvoří v účtu úložiště, jakmile dojde k události protokolu aktivit. Objekty BLOB v kontejneru podle stejné zásady vytváření názvů v protokolech aktivit a diagnostické protokoly, jak je znázorněno zde:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například může být název objektu blob:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Každý objekt blob PT1H.json obsahuje objekt blob JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h = 12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Minut (m = 00) je vždy 00, protože události protokolu aktivit se rozdělují do jednotlivých objektů BLOB po hodinách.

V rámci souboru pt1h.JSON, když se ukládají všechny události v poli "záznamy", za tento formát:

``` JSON
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
| time |Časové razítko události vygenerované službou Azure zpracování požadavku odpovídající události. |
| resourceId |ID prostředku ovlivněných prostředků. |
| operationName |Název operace |
| category |Kategorie Akce, např. Zápisu, čtení, akce. |
| resultType |Typ výsledku, např. Úspěch, selhání spuštění |
| resultSignature |Závisí na typu prostředku. |
| durationMs |Doba trvání operace v milisekundách |
| callerIpAddress |IP adresa uživatele, který provedl operaci, deklarace nebo hlavní název služby deklarace identity na základě dostupnosti. |
| correlationId |Obvykle GUID ve formátu řetězce. Události, které sdílejí ID korelace patřit do stejné akce uber. |
| identity |Objekt blob JSON popisující autorizace a deklarace identity. |
| Autorizace |Objekt BLOB RBAC vlastností události. Obvykle obsahuje vlastnosti "action", "role" a "rozsah". |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritický", "Chyba", "Varování", "Informační" a "Verbose" |
| location |Oblast, ve kterém došlo k umístění (nebo globálního). |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. |

> [!NOTE]
> Vlastnosti a využití těchto vlastností se může lišit v závislosti na prostředku.
> 
> 

## <a name="next-steps"></a>Další postup
* [Stažení objektů blob pro analýzu](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Stream protokolů aktivit do služby Event Hubs](../../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
* [Další informace o protokolu aktivit](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

