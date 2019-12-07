---
title: Exportovat protokol aktivit Azure
description: Exportujte protokol aktivit Azure do úložiště k archivaci nebo Azure Event Hubs pro export mimo Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: f2366d60868dd1db52fd8bfc2149756ed4b1b0d1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893616"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Export protokolu aktivit Azure do úložiště nebo do Azure Event Hubs

> [!NOTE]
> Nyní můžete protokol aktivit shromažďovat do Log Analytics pracovního prostoru pomocí nastavení diagnostiky podobného způsobu, jakým shromažďujete protokoly prostředků. Přečtěte si téma [shromáždění a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure monitor](activity-log-collect.md).

[Protokol aktivit Azure](activity-logs-overview.md) poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo ve vašem předplatném Azure. Kromě zobrazení protokolu aktivit v Azure Portal nebo jeho zkopírování do pracovního prostoru Log Analytics, kde se dá analyzovat s ostatními daty shromažďovanými Azure Monitor můžete vytvořit profil protokolu pro archivaci protokolu aktivit do účtu služby Azure Storage nebo jeho streamování do  Centrum událostí.

## <a name="archive-activity-log"></a>Protokol aktivit archivu
Archivace protokolu aktivit do účtu úložiště je užitečná v případě, že byste chtěli uchovávat data protokolu déle než 90 dní (s úplnou kontrolou nad zásadami uchovávání informací) pro audit, statickou analýzu nebo zálohování. Pokud potřebujete události uchovávat jenom 90 dní nebo méně, nemusíte v účtu úložiště nastavovat archivaci, protože události protokolu aktivit se uchovávají na platformě Azure po dobu 90 dnů.

## <a name="stream-activity-log-to-event-hub"></a>Streamování protokolu aktivit do centra událostí
[Azure Event Hubs](/azure/event-hubs/) je platforma pro streamování dat a služba pro příjem událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Možnosti streamování pro protokol aktivit můžete použít dvěma způsoby:
* **Streamování do systémů protokolování a telemetrie třetích stran**: v průběhu času se streamování služby Azure Event Hubs stane mechanismem pro přesměrování vaší aktivity do řešení systémů Siem a Log Analytics třetích stran.
* **Sestavení vlastní telemetrie a**rozhraní pro protokolování: Pokud už máte vlastní platformu telemetrie nebo uvažujete o jejím sestavování, je vysoce škálovatelná verze Event Hubs pro publikování a odběr, která umožňuje flexibilní ingestování protokolu aktivit. 

## <a name="prerequisites"></a>Předpoklady

### <a name="storage-account"></a>Účet úložiště
Pokud budete protokol aktivit archivovat, musíte si [vytvořit účet úložiště](../../storage/common/storage-quickstart-create-account.md) , pokud ho ještě nemáte. Neměli byste používat existující účet úložiště, který obsahuje jiná, nemonitorovaná data, která jsou v něm uložená, abyste mohli lépe řídit přístup k datům monitorování. Pokud i přesto archivujte protokoly a metriky do účtu úložiště, můžete použít stejný účet úložiště, abyste zachovali všechna data monitorování v centrálním umístění.

Účet úložiště nemusí být ve stejném předplatném jako odběr, který vydává protokoly, pokud uživatel, který nakonfiguruje nastavení, má odpovídající přístup RBAC k oběma předplatným.
> [!NOTE]
>  V tuto chvíli nemůžete archivovat data do účtu úložiště, který je za zabezpečenou virtuální sítí.

### <a name="event-hubs"></a>Event Hubs
Pokud odesíláte protokol aktivit do centra událostí, budete muset [vytvořit centrum událostí](../../event-hubs/event-hubs-create.md) , pokud ho ještě nemáte. Pokud jste do tohoto oboru názvů Event Hubs dříve zasílaly události protokolu aktivit, pak se toto centrum událostí znovu použije.

Zásady sdíleného přístupu definují oprávnění, která má mechanismus streamování. Streamování do Event Hubs vyžaduje oprávnění spravovat, odesílat a naslouchat. Zásady sdíleného přístupu pro obor názvů Event Hubs můžete vytvořit nebo upravit v Azure Portal na kartě konfigurovat pro obor názvů Event Hubs.

Pokud chcete profil protokolu aktivit aktualizovat tak, aby zahrnoval streamování, musíte mít pro toto Event Hubs autorizační pravidlo oprávnění ListKey. Obor názvů Event Hubs nemusí být ve stejném předplatném, jako je předplatné, které vysílá protokoly, pokud uživatel, který nastavení nakonfiguruje, má odpovídající přístup RBAC k oběma předplatným a oba odběry jsou ve stejném tenantovi AAD.

Streamujte protokol aktivit do centra událostí [vytvořením profilu protokolu](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Vytvoření profilu protokolu
Můžete definovat způsob exportu protokolu aktivit Azure pomocí **profilu protokolu**. Každé předplatné Azure může mít jenom jeden profil protokolu. Tato nastavení se dají nakonfigurovat pomocí možnosti **exportovat** v okně Protokol aktivit na portálu. Dá se taky nakonfigurovat programově [pomocí Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), rutin PowerShellu nebo rozhraní příkazového řádku.

Profil protokolu definuje následující.

**Kam se má protokol aktivit odeslat.** V současné době jsou dostupné možnosti účet úložiště nebo Event Hubs.

**Které kategorie událostí by se měly odeslat.** Význam *kategorie* v profilech protokolů a událostech protokolu aktivit se liší. V profilu protokolu *kategorie* představuje typ operace (zápis, odstranit, akce). *Kategorie*"* vlastnost" v události protokolu aktivit představuje zdroj nebo typ události (například Správa, ServiceHealth a výstraha).

**Které oblasti (umístění) by měly být exportovány.** Měli byste zahrnout všechna umístění, protože mnoho událostí v protokolu aktivit je globální události.

**Jak dlouho má být protokol aktivit uchováván v účtu úložiště.** Uchování 0 dnů znamená, že protokoly se uchovávají navždy. V opačném případě může být hodnota libovolný počet dní mezi 1 a 365.

Pokud jsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázané, pak zásady uchovávání nemají žádný vliv. Zásady uchovávání informací jsou použitých za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní. Proces odstraňování začíná o půlnoci UTC, ale Všimněte si, že může trvat až 24 hodin pro protokoly, které mají být odstraněny z vašeho účtu úložiště.


> [!IMPORTANT]
> Pokud poskytovatel prostředků Microsoft. Insights není zaregistrovaný, může se vám při vytváření profilu protokolu zobrazovat chyba. Pokud chcete tohoto poskytovatele zaregistrovat, přečtěte si téma [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/resource-manager-supported-services.md) .


### <a name="create-log-profile-using-the-azure-portal"></a>Vytvořit profil protokolu pomocí Azure Portal

Vytvořte nebo upravte profil protokolu s možností **exportovat do centra událostí** v Azure Portal.

1. V nabídce **monitorování** v Azure Portal vyberte **exportovat do centra událostí**.

    ![Tlačítko Exportovat na portálu](media/activity-log-export/portal-export.png)

3. V okně, které se zobrazí, zadejte následující:
   * Oblasti s událostmi k exportu. Měli byste vybrat všechny oblasti, abyste se ujistili, že nedošlo ke klíčovým událostem, protože protokol aktivit je globální (neregionální) protokol, a takže většina událostí nemá přidruženou oblast. 
   * Pokud chcete zapisovat do účtu úložiště:
       * Účet úložiště, na který byste chtěli ukládat události.
       * Počet dní, po které mají být tyto události v úložišti uchovávány. Nastavení 0 dnů uchová protokoly navždy.
   * Pokud chcete zapisovat do centra událostí:
       * Obor názvů Service Bus, ve kterém se má vytvořit centrum událostí pro streamování těchto událostí.

     ![Okno Exportovat protokol aktivit](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Kliknutím na **Uložit** tato nastavení uložte. Nastavení se okamžitě použijí pro vaše předplatné.


### <a name="configure-log-profile-using-powershell"></a>Konfigurace profilu protokolu pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud profil protokolu již existuje, musíte nejprve odebrat existující profil protokolu a pak vytvořit nový.

1. Pokud chcete zjistit, jestli profil protokolu existuje, použijte `Get-AzLogProfile`.  Pokud profil protokolu existuje, poznamenejte si jeho vlastnost *Name* .

1. Pomocí `Remove-AzLogProfile` odeberte profil protokolu pomocí hodnoty z vlastnosti *název* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Vytvořit nový profil protokolu pomocí `Add-AzLogProfile`:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Vlastnost | Požaduje se | Popis |
    | --- | --- | --- |
    | Name (Název) |Ano |Název vašeho profilu protokolu. |
    | StorageAccountId |Ne |ID prostředku účtu úložiště, do kterého se má ukládat protokol aktivit |
    | serviceBusRuleId |Ne |Service Bus ID pravidla pro Service Bus oboru názvů, ve kterém chcete vytvořit centra událostí. Toto je řetězec ve formátu: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Umístění |Ano |Čárkami oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. |
    | RetentionInDays |Ano |Počet dní, po které se mají události uchovávat v účtu úložiště v rozmezí od 1 do 365. Hodnota nula ukládá protokoly po neomezenou dobu. |
    | Kategorie |Ne |Čárkami oddělený seznam kategorií událostí, které se mají shromáždit. Možné hodnoty jsou _Write_, _Delete_a _Action_. |

### <a name="example-script"></a>Ukázkový skript
Následuje ukázkový skript prostředí PowerShell pro vytvoření profilu protokolu aktivit, který zapisuje protokol aktivit do účtu úložiště i centra událostí.

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

Pokud profil protokolu již existuje, musíte nejprve odebrat existující profil protokolu a pak vytvořit nový profil protokolu.

1. Pokud chcete zjistit, jestli profil protokolu existuje, použijte `az monitor log-profiles list`.
2. Pomocí `az monitor log-profiles delete --name "<log profile name>` odeberte profil protokolu pomocí hodnoty z vlastnosti *název* .
3. Vytvořit nový profil protokolu pomocí `az monitor log-profiles create`:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Vlastnost | Požaduje se | Popis |
    | --- | --- | --- |
    | jméno |Ano |Název vašeho profilu protokolu. |
    | storage-account-id |Ano |ID prostředku účtu úložiště, do kterého se mají ukládat protokoly aktivit |
    | locations |Ano |Mezerou oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. Seznam všech oblastí pro vaše předplatné můžete zobrazit pomocí `az account list-locations --query [].name`. |
    | days |Ano |Počet dní, po které se mají uchovávat události v rozmezí od 1 do 365. Hodnota nula bude ukládat protokoly po neomezenou dobu (navždy).  Je-li nastavena hodnota nula, parametr Enabled by měl být nastaven na hodnotu false. |
    |enabled | Ano |True nebo False.  Slouží k povolení nebo zakázání zásad uchovávání informací.  Pokud je hodnota true, parametr Days musí být hodnota větší než 0.
    | categories |Ano |Prostor – seznam kategorií událostí, které mají být shromážděny. Možné hodnoty jsou Write, DELETE a Action. |



## <a name="activity-log-schema"></a>Schéma protokolu aktivit
Ať už se odesílá do služby Azure Storage nebo centra událostí, data protokolu aktivit se zapisují do formátu JSON s následujícím formátem.


> Formát dat protokolu aktivit zapsaný do účtu úložiště se změnil na řádky JSON od 1. listopadu 2018. Podrobnosti o změně tohoto formátu najdete v článku [Příprava změny formátu Azure monitor archivované protokoly prostředků do účtu úložiště](diagnostic-logs-append-blobs.md) .

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
Prvky v tomto formátu JSON jsou popsány v následující tabulce.

| Název elementu | Popis |
| --- | --- |
| time |Časové razítko, kdy se událost vygenerovala službou Azure, zpracování žádosti odpovídající události |
| resourceId |ID prostředku ovlivněného prostředku |
| operationName |Název operace |
| category |Kategorie akce, např. Zápis, čtení, akce. |
| resultType |Typ výsledku, např. Úspěch, chyba, spuštění |
| resultSignature |Závisí na typu prostředku. |
| durationMs |Doba trvání operace v milisekundách |
| callerIpAddress |IP adresa uživatele, který provedl operaci, deklaraci hlavního názvu uživatele (UPN) nebo deklaraci identity hlavního názvu služby (SPN) podle dostupnosti. |
| correlationId |Obvykle identifikátor GUID ve formátu řetězce. Události, které sdílejí ID korelace, patří ke stejné akci Uber. |
| identita |Objekt BLOB JSON popisující autorizaci a deklarace identity. |
| authorization |Objekt BLOB vlastností RBAC události Obvykle zahrnuje vlastnosti "Action", "role" a "Scope". |
| úroveň |Úroveň události Jedna z následujících hodnot: _kritická_, _Chyba_, _Upozornění_, _informativní_a _podrobné_ |
| location |Oblast, ve které došlo k umístění (nebo globálnímu). |
| properties |Sada dvojic `<Key, Value>` (tj. slovník), které popisují podrobnosti události. |

> [!NOTE]
> Vlastnosti a používání těchto vlastností se mohou lišit v závislosti na prostředku.



## <a name="next-steps"></a>Další kroky

* [Další informace o protokolu aktivit](../../azure-resource-manager/resource-group-audit.md)
* [Shromáždění protokolu aktivit do protokolů Azure Monitor](activity-log-collect.md)
