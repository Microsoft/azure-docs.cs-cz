---
title: Exportovat protokol aktivit Azure
description: Exportujte protokol aktivit Azure do úložiště pro archivaci nebo Azure Event Hubs pro export mimo Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248141"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportovat protokol aktivit Azure storage nebo Azure Event Hubs
[Protokolu aktivit Azure](activity-logs-overview.md) poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo ve vašem předplatném Azure. Kromě zobrazení protokolu aktivit na webu Azure Portal nebo zkopírováním do pracovního prostoru Log Analytics, kde můžou být analyzována s dalšími daty shromážděnými službou Azure Monitor, můžete vytvořit profil protokolu k archivaci protokolu aktivit do účtu služby Azure storage nebo Streamovat do  Centrum událostí.

## <a name="archive-activity-log"></a>Archivace protokolu aktivit
Archivace protokolu aktivit do účtu úložiště je užitečné, pokud byste chtěli uchovávat data déle než 90 dnů (s úplnou kontrolou nad zásady uchovávání informací) pro audit, statické analýzy nebo pro zálohování protokolů. Pokud potřebujete události uchovávat po 90 dní nebo méně nepotřebujete nastavit archivaci do účtu úložiště, protože události protokolu aktivit se zachovají na platformě Azure po dobu 90 dnů.

## <a name="stream-activity-log-to-event-hub"></a>Stream protokolů aktivit do centra událostí
[Azure Event Hubs](/azure/event-hubs/) daty streamovanými platformy a události služba pro ingestování, které můžou přijímat a zpracovávat miliony událostí za sekundu. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Jsou dva způsoby, jak můžete použít funkci streamování pro protokol aktivit:
* **Stream protokolování a telemetrie systémům třetích stran**: V průběhu času streamování služby Azure Event Hubs se stanou mechanismus kanálem váš protokol aktivit do sady Siem třetích stran a protokolu analytická řešení.
* **Vytvářejte vlastní telemetrii a protokolování platformy**: Pokud už máte platformu vlastními silami sestavených telemetrická data nebo přemýšlí o vytvoříte, vysoce škálovatelné publikování a odběru povaha služby Event Hubs umožňuje flexibilně ingestování protokolu aktivit. 

## <a name="prerequisites"></a>Požadavky

### <a name="storage-account"></a>Účet úložiště
Pokud jste archivace váš protokol aktivit, budete muset [vytvořit účet úložiště](../../storage/common/storage-quickstart-create-account.md) pokud ho ještě nemáte. Byste neměli používat existující účet úložiště, který obsahuje jiné, než monitorování data uložená v něm může lépe řídit přístup k datům monitorování. Pokud také jsou archivace diagnostických protokolů a metrik na účet úložiště i když můžete používat tento stejný účet úložiště k udržování propojení všech dat monitorování v centrálním umístění.

Účet úložiště nemusí být ve stejném předplatném jako předplatné, které vysílá protokoly za předpokladu, že uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma předplatným.
> [!NOTE]
>  Nelze aktuálně archivace dat do účtu úložiště, který je za zabezpečené virtuální síti.

### <a name="event-hubs"></a>Event Hubs
Pokud odesíláte váš protokol aktivit do centra událostí, pak budete muset [vytvořit Centrum událostí](../../event-hubs/event-hubs-create.md) pokud ho ještě nemáte. Pokud jste předtím streamovali události protokolu aktivit do tohoto oboru názvů služby Event Hubs, pak tohoto centra událostí bude znovu použita.

Zásady sdíleného přístupu definuje oprávnění, která má mechanismu datových proudů. Streamování do Event Hubs vyžaduje oprávnění spravovat, odesílání a naslouchání. Můžete vytvářet nebo upravovat zásady sdíleného přístupu pro službu Event Hubs oboru názvů na webu Azure Portal na kartě Konfigurace pro váš obor názvů služby Event Hubs.

Aktualizovat profil protokolu aktivit protokolu zahrnout streamování, musí mít oprávnění ListKey na tento autorizační pravidlo Event Hubs. Obor názvů Event Hubs, nemusí být ve stejném předplatném jako předplatné, které je vysílá protokoly, jako má uživatel, který konfiguruje nastavení odpovídající roli RBAC přístup k oběma předplatným a obě předplatná jsou ve stejném tenantovi AAD.

Stream protokolů aktivit do centra událostí pomocí [vytváření profilu protokolu](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Vytvoření profilu protokolu
Můžete definovat, jak váš protokol aktivit Azure je exportovat pomocí **profil protokolu**. Každé předplatné Azure může mít jenom jeden profil protokolu. Tato nastavení můžete nakonfigurovat přes **exportovat** možnost v okně protokolu aktivit na portálu. Se taky dají konfigurovat prostřednictvím kódu programu [pomocí REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), rutin prostředí PowerShell nebo rozhraní příkazového řádku.

Definuje následující profilu protokolu.

**Protokol aktivit, které by měla být odeslána.** V současné době jsou k dispozici možnosti účtu úložiště nebo Center událostí.

**Kategorie události, které by měly odeslány.** Význam *kategorie* v profily protokolů a protokolu aktivity se liší události. V profilu protokolu *kategorie* představuje typ operace (akce zápisu, odstranění,). V události protokolu aktivit *kategorie*"* vlastnost představuje zdroj nebo typu události (třeba správu ServiceHealth a upozornění).

**Které oblasti (umístění) by měly být exportovány.** Protože mnoho událostí v protokolu aktivit jsou globální události, měli byste zahrnout všechna umístění.

**Jak dlouho se uchovávají v účtu úložiště protokolu aktivit.** Uchování 0 dnů znamená, že protokoly se uchovávají navždy. V opačném případě hodnota může být libovolný počet dnů mezi 1 a 2147483647.

Pokud nejsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázaný, zásady uchovávání informací nemají žádný vliv. Zásady uchovávání informací jsou použitých za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní. Proces odstraňování začíná o půlnoci UTC, ale Všimněte si, že může trvat až 24 hodin pro protokoly, které mají být odstraněny z vašeho účtu úložiště.



> [!WARNING]
> Formát data protokolu v aktuálním účtu úložiště změnit na řádky JSON na 1. listopadu 2018. [Informace o dopadu a postup pro aktualizaci nástrojů, aby si s novým formátem poradily, najdete v tomto článku](diagnostic-logs-append-blobs.md).
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Vytvoření profilu protokolu pomocí webu Azure portal

Vytvořte nebo upravte profil protokolu s **exportovat do centra událostí** možnost na webu Azure Portal.

1. Z **monitorování** nabídky na webu Azure Portal, vyberte **exportovat do centra událostí**.

    ![Tlačítko pro export na portálu](media/activity-log-export/portal-export.png)

3. V okně, které se zobrazí zadejte následující informace:
   * Oblasti s událostmi pro export. Měli byste vybrat všech oblastech pro zajištění, že jste Nenechte si ujít klíčových událostí protokolu aktivit je globální protokolu (– neregionální), a proto nejvíce událostem nemají k nim má přiřazené oblasti. 
   * Pokud chcete zapisovat do účtu úložiště:
       * Účet úložiště, do kterého chcete uložit události.
       * počet dní, které chcete uchovávat tyto události v úložišti. Nastavení 0 dnů uchová protokoly navždy.
   * Pokud chcete zapisovat do centra událostí:
       * Namespace služby Service Bus, ve kterém chcete vytvořit pro streamování tyto události v Centru událostí.

     ![Okno Export protokolu aktivit](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Klikněte na tlačítko **Uložit** nastavení uložte. Nastavení se okamžitě použijí pro vaše předplatné.


### <a name="configure-log-profile-using-powershell"></a>Konfigurace profilu protokolu pomocí Powershellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud profil protokolu již existuje, musíte nejprve odstranit stávající profilu protokolu a potom vytvořte novou.

1. Použití `Get-AzLogProfile` a zjistěte, jestli existuje profil protokolu.  Poznámka: Pokud profil protokolu neexistuje, *název* vlastnost.

1. Použití `Remove-AzLogProfile` odebrání profilu protokolu z hodnoty *název* vlastnost.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Použití `Add-AzLogProfile` k vytvoření nového profilu protokolu:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Vlastnost | Požaduje se | Popis |
    | --- | --- | --- |
    | Name |Ano |Název profilu protokolu. |
    | StorageAccountId |Ne |ID prostředku účtu úložiště, kterého chcete uložit protokol aktivit. |
    | serviceBusRuleId |Ne |ID pravidla služby Service Bus pro obor názvů služby Service Bus, by měl mít vytvořené v služby event hubs. Toto je řetězec ve formátu: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Ano |Čárkami oddělený seznam oblasti, pro které chcete shromažďovat události protokolu aktivit. |
    | RetentionInDays |Ano |Počet dní, pro které události se uchovávají v účtu úložiště, od 1 do 2147483647. Hodnota nula ukládá protokoly po neomezenou dobu. |
    | Category |Ne |Čárkami oddělený seznam kategorie událostí, které se mají shromažďovat. Možné hodnoty jsou _zápisu_, _odstranit_, a _akce_. |

### <a name="example-script"></a>Ukázkový skript
Tady je ukázkový skript Powershellu k vytvoření profilu protokolu, která zapisuje do obou úložiště účtu a Centrum událostí protokolu aktivit.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurace profilu protokolu pomocí Azure CLI

Pokud profil protokolu již existuje, musíte nejprve odstranit stávající profilu protokolu a potom vytvořit nový profil protokolu.

1. Použití `az monitor log-profiles list` a zjistěte, jestli existuje profil protokolu.
2. Použití `az monitor log-profiles delete --name "<log profile name>` odebrání profilu protokolu z hodnoty *název* vlastnost.
3. Použití `az monitor log-profiles create` k vytvoření nového profilu protokolu:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Vlastnost | Požaduje se | Popis |
    | --- | --- | --- |
    | name |Ano |Název profilu protokolu. |
    | storage-account-id |Ano |ID prostředku účtu úložiště, ke kterému má být uložen protokolů aktivit. |
    | locations |Ano |Místo oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. Můžete zobrazit seznam všech oblastí pro vaše předplatné pomocí `az account list-locations --query [].name`. |
    | days |Ano |Počet dní pro události, které by měla být zachována, od 1 do 365. Hodnota nula bude po neomezenou dobu ukládání protokolů (trvale).  Pokud nula, povolené parametr by měl být nastavte na hodnotu true. |
    |enabled | Ano |True nebo False.  Umožňuje povolit nebo zakázat zásady uchovávání informací.  Pokud je hodnota True, musí být parametr dní hodnotu větší než 0.
    | categories |Ano |Místo oddělený seznam kategorie událostí, které se mají shromažďovat. Možné hodnoty jsou Write, Delete a akce. |



## <a name="activity-log-schema"></a>Schéma protokolu aktivit
Zda jsou odeslána do služby Azure storage nebo centra událostí, data protokolu aktivit se zapíšou do formátu JSON v následujícím formátu.

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
Prvky v tomto JSON jsou popsány v následující tabulce.

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
| authorization |Objekt BLOB RBAC vlastností události. Obvykle obsahuje vlastnosti "action", "role" a "rozsah". |
| level |Úroveň události. Jeden z následujících hodnot: _Kritické_, _chyba_, _upozornění_, _informační_, a _Verbose_ |
| location |Oblast, ve kterém došlo k umístění (nebo globálního). |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. |

> [!NOTE]
> Vlastnosti a využití těchto vlastností se může lišit v závislosti na prostředku.



## <a name="next-steps"></a>Další postup

* [Další informace o protokolu aktivit](../../azure-resource-manager/resource-group-audit.md)
* [Shromažďování protokolů aktivit do protokoly Azure monitoru](activity-log-collect.md)
