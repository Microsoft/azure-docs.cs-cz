---
title: "Externí řešení monitorování integrovat Azure zásobníku | Microsoft Docs"
description: "Zjistěte, jak integrovat Azure zásobníku externí řešení monitorování ve vašem datovém centru."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3435ada40afb9f1c6e57be64d1b9086d0cdaefd9
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Externí řešení monitorování integrovat Azure zásobníku

Pro externí monitorování infrastruktury Azure zásobníku, budete muset monitorování softwaru zásobník Azure, fyzické počítače a fyzické síťové přepínače. Každá z těchto oblastí nabízí metody za účelem načtení informací o stavu a výstrahy:

- Azure zásobníku softwaru nabízí rozhraní API založené na REST pro načtení stavu a výstrahy. (S použitím technologie softwarově definované jako prostory úložiště – přímé, výstrahy a stav úložiště jsou součástí softwaru monitorování.).
- Fyzické počítače můžete zpřístupnit stavu a informace o výstrahách pomocí řadiče pro správu základní desky (BMC).
- Fyzická síťová zařízení můžete zpřístupnit stavu a informace o výstrahách prostřednictvím protokolu SNMP.

Každé řešení Azure zásobníku se dodává s hostiteli životního cyklu hardwaru. Tento hostitel spouští monitorování software výrobce OEM dodavatele hardwaru pro fyzických serverů a síťových zařízení. V případě potřeby můžete vynechat tato řešení ke sledování a přímo integrovat existující řešení pro monitorování ve vašem datovém centru.

> [!IMPORTANT]
> Externí řešení monitorování, které používáte, musí být bez agenta. Nelze instalovat agenty třetích stran uvnitř součásti zásobníku Azure.

Následující diagram znázorňuje tok přenosů mezi s Azure zásobníku integrované systémem, hostitele životního cyklu hardwaru, do externí řešení pro monitorování a systém kolekce externí lístků nebo data.

![Diagram zobrazující provoz mezi Azure zásobníku, monitorování a lístků pro řešení.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Tento článek vysvětluje, jak integrovat externí řešení monitorování, například System Center Operations Manager a Nagios zásobník Azure. Obsahuje také postupy pro práci s výstrahami programově pomocí prostředí PowerShell nebo prostřednictvím volání rozhraní REST API.

## <a name="integrate-with-operations-manager"></a>Integrace s nástrojem Operations Manager

Nástroj Operations Manager můžete použít pro externí sledování zásobníku Azure. System Center Management Pack pro Microsoft Azure zásobníku umožňuje sledovat více nasazení Azure zásobníku se jedna instance nástroje Operations Manager. Sada management pack používá ke komunikaci s Azure zásobníku poskytovatele prostředků stavu a zprostředkovatele prostředků aktualizace rozhraní REST API. Pokud budete chtít vynechat OEM monitorování software, který běží na hostiteli životního cyklu hardwaru, můžete nainstalovat sady management Pack dodavatele monitorování fyzických serverů. Zjišťování síťových zařízení nástroje Operations Manager můžete použít také k monitorování síťových přepínačů.

Sada management pack pro zásobník Azure poskytuje následující možnosti:

- Můžete spravovat více nasazení Azure zásobníku
- Není poskytována podpora pro Azure Active Directory (Azure AD) a služby Active Directory Federation Services (AD FS)
- Můžete načíst a uzavřít výstrahy
- Je stavu a kapacity řídicí panel
- Zahrnuje režimu údržby Automatická detekce při opravy a aktualizace (P & U) je v průběhu
- Obsahuje vynucené aktualizace úlohy pro nasazení a oblasti
- Můžete přidávat vlastní informace do oblasti
- Podporuje oznámení a vytváření sestav

System Center Management Pack si můžete stáhnout pro Microsoft Azure zásobníku a přidružené [uživatelská příručka](https://www.microsoft.com/en-us/download/details.aspx?id=55184), nebo přímo z nástroje Operations Manager.

Pro tvorbu lístků řešení můžete integrovat nástroje Operations Manager pomocí nástroje System Center Service Manager. Konektor produktů integrované umožňuje obousměrnou komunikaci, která umožňuje zavřít výstrahu v zásobníku Azure a nástrojem Operations Manager po vyřešení žádost o službu na portálu Service Manager.

Následující diagram znázorňuje integrace zásobník Azure s existujícím nasazení produktu System Center. Je možné automatizovat Service Manager další s System Center Orchestrator nebo Service Management Automation (SMA), aby se spustila operace v zásobníku Azure.

![Diagram zobrazující integrace s OM, Service Manager a SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrace s Nagios

Nagios, monitorování modulů plug-in byla vyvinuta společně s Cloudbase partnerských řešení, která je k dispozici v rámci licence projektovou bezplatný software – MIT (Massachusetts Institute of Technology).

Modul plug-in je napsána v Pythonu a využívá zprostředkovatele prostředků stavu rozhraní REST API. Nabízí základní funkce načtení a uzavřít výstrahy v zásobníku Azure. Jako sada management pack System Center můžete přidat více nasazení zásobník Azure a k odeslání oznámení.

Tento modul plug-in pracuje s Nagios Enterprise a Nagios jádra. Můžete ho stáhnout [zde](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Na web pro stahování obsahuje také podrobnosti instalace a konfigurace.

### <a name="plugin-parameters"></a>Parametry modulu plug-in

Konfigurace souboru modulu plug-in "Azurestack_plugin.py" s následujícími parametry:

| Parametr | Popis | Příklad: |
|---------|---------|---------|
| *arm_endpoint* | Koncový bod Azure Resource Manager (správce) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Koncový bod Azure Resource Manager (správce)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID předplatného správce | Načíst prostřednictvím portálu správce nebo prostředí PowerShell |
| *User_name* | Uživatelské jméno předplatné – operátor | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Operátor předplatné heslo | heslo |
| *Client_id* | Klient | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Název oblasti Azure zásobníku | místní |
|  |  |

* Identifikátor GUID prostředí PowerShell, který je k dispozici je univerzální. Můžete ho použít pro každé nasazení.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Použít PowerShell k monitorování stavu a výstrahy

Pokud nepoužíváte Operations Manager, Nagios nebo na základě Nagios řešení, můžete použít PowerShell k povolení širokou škálu sledování řešení pro integraci s Azure zásobníku.
 
1. Pomocí prostředí PowerShell, ujistěte se, že máte [prostředí PowerShell nainstalovaný a nakonfigurovaný](azure-stack-powershell-configure-quickstart.md) pro prostředí Azure zásobníku operátor. Instalace prostředí PowerShell na místní počítač, který můžete dosáhnout koncového bodu Resource Manager (správce) (https://adminmanagement.[Oblast].[External_FQDN]).

2. Spusťte následující příkazy pro připojení k prostředí Azure zásobníku jako operátor zásobník Azure:

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Přejděte do adresáře, kam jste nainstalovali [nástroje Azure zásobníku](https://github.com/Azure/AzureStack-Tools) jako součást instalace prostředí PowerShell, například c:\azurestack-tools-master. Pak přejděte do adresáře infrastruktury a spusťte následující příkaz pro import modulu infrastruktury:

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Použití příkazů jako jsou následující příklady pro práci s výstrahami:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Použít rozhraní API REST k monitorování stavu a výstrahy

Volání rozhraní REST API slouží k získání výstrah, uzavřít výstrahy a získat stav zprostředkovatelé prostředků.

### <a name="get-alert"></a>Získání výstrahy

**Požadavek**

Požadavek získá všechny aktivní a uzavřené výstrahy pro výchozí zprostředkovatel předplatné. Neexistuje žádný text žádosti.


|Metoda  |Identifikátor URI žádosti  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**Argumenty**

|Argument  |Popis  |
|---------|---------|
|armendpoint     |  Koncový bod Azure Resource Manager prostředí Azure zásobníku v https://adminmanagement formátu. {RegionName}. {Externí plně kvalifikovaný název domény}. Například, pokud je externí plně kvalifikovaný název domény *azurestack.external* a název oblasti je *místní*, pak je koncový bod správce prostředků https://adminmanagement.local.azurestack.external.       |
|subid     |   ID předplatného uživatele, který je uskutečněním hovoru. Toto rozhraní API pro dotaz můžete použít pouze s uživatelem, který má oprávnění k předplatnému výchozího zprostředkovatele.      |
|RegionName     |    Název oblasti Azure zásobníku nasazení.     |
|verze rozhraní API.     |  Verze protokolu, který slouží k vytvoření této žádosti. Je nutné použít 2016-05-01.      |
|     |         |

**Odpověď**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Podrobnosti o odpovědi**


|  Argument  |Popis  |
|---------|---------|
|*id*     |      Jedinečné ID výstrahy.   |
|*name*     |     Interní název výstrahy.   |
|*Typ*     |     Definice prostředků.    |
|*location*     |       Název oblasti.     |
|*značek*     |   Značky prostředku.     |
|*closedtimestamp*    |  Čas UTC, kdy se zavřel výstrahu.    |
|*createdtimestamp*     |     Čas UTC vytvoření výstrahy.   |
|*Popis*     |    Popis výstrahy.     |
|*faultid*     | Tato součást.        |
|*alertid*     |  Jedinečné ID výstrahy.       |
|*faulttypeid*     |  Typ jedinečného vadný součásti.       |
|*lastupdatedtimestamp*     |   Čas UTC poslední aktualizace informace o výstrahách.    |
|*Stav healthstate*     | Celkový stav.        |
|*name*     |   Název konkrétní výstrahu.      |
|*fabricname*     |    Název registrované prostředků infrastruktury vadný součásti.   |
|*Popis*     |  Popis součásti registrované prostředků infrastruktury.   |
|*ServiceType*     |   Typ služby registrované prostředků infrastruktury.   |
|*Nápravy*     |   Doporučené nápravy kroky.    |
|*Typ*     |   Typ výstrahy.    |
|*resourceRegistrationid*    |     ID zdroje registrované.    |
|*resourceProviderRegistrationID*   |    ID zaregistrovaný zprostředkovatel prostředku ohrožené součásti.  |
|*serviceregistrationid*     |    ID registrované služby.   |
|*Závažnost*     |     Závažnost výstrahy.  |
|*Stav*     |    Stav výstrahy.   |
|*title*     |    Název výstrahy.   |
|*impactedresourceid*     |     ID prostředku dopad.    |
|*ImpactedresourceDisplayName*     |     Název ovlivněné prostředku.  |
|*closedByUserAlias*     |   Uživatel, který zavřel výstrahu.      |

### <a name="close-alert"></a>Zavřít výstrahu

**Požadavek**

Žádost se zavře výstrahy podle jeho jedinečné ID.

|Metoda    |Identifikátor URI žádosti  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**Argumenty**


|Argument  |Popis  |
|---------|---------|
|*armendpoint*     |   Koncový bod správce prostředků Azure zásobníku prostředí, ve formátu https://adminmanagement. {RegionName}. {Externí plně kvalifikovaný název domény}. Například, pokud je externí plně kvalifikovaný název domény *azurestack.external* a název oblasti je *místní*, pak je koncový bod správce prostředků https://adminmanagement.local.azurestack.external.      |
|*subid*     |    ID předplatného uživatele, který je uskutečněním hovoru. Toto rozhraní API pro dotaz můžete použít pouze s uživatelem, který má oprávnění k předplatnému výchozího zprostředkovatele.     |
|*RegionName*     |   Název oblasti Azure zásobníku nasazení.      |
|*api-version*     |    Verze protokolu, který slouží k vytvoření této žádosti. Je nutné použít 2016-05-01.     |
|*alertid*     |    Jedinečné ID výstrahy.     |

**Text**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Odpověď**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Podrobnosti o odpovědi**


|  Argument  |Popis  |
|---------|---------|
|*id*     |      Jedinečné ID výstrahy.   |
|*name*     |     Interní název výstrahy.   |
|*Typ*     |     Definice prostředků.    |
|*location*     |       Název oblasti.     |
|*značek*     |   Značky prostředku.     |
|*closedtimestamp*    |  Čas UTC, kdy se zavřel výstrahu.    |
|*createdtimestamp*     |     Čas UTC vytvoření výstrahy.   |
|*Popis*     |    Popis výstrahy.     |
|*faultid*     | Tato součást.        |
|*alertid*     |  Jedinečné ID výstrahy.       |
|*faulttypeid*     |  Typ jedinečného vadný součásti.       |
|*lastupdatedtimestamp*     |   Čas UTC poslední aktualizace informace o výstrahách.    |
|*Stav healthstate*     | Celkový stav.        |
|*name*     |   Název konkrétní výstrahu.      |
|*fabricname*     |    Název registrované prostředků infrastruktury vadný součásti.   |
|*Popis*     |  Popis součásti registrované prostředků infrastruktury.   |
|*ServiceType*     |   Typ služby registrované prostředků infrastruktury.   |
|*Nápravy*     |   Doporučené nápravy kroky.    |
|*Typ*     |   Typ výstrahy.    |
|*resourceRegistrationid*    |     ID zdroje registrované.    |
|*resourceProviderRegistrationID*   |    ID zaregistrovaný zprostředkovatel prostředku ohrožené součásti.  |
|*serviceregistrationid*     |    ID registrované služby.   |
|*Závažnost*     |     Závažnost výstrahy.  |
|*Stav*     |    Stav výstrahy.   |
|*title*     |    Název výstrahy.   |
|*impactedresourceid*     |     ID prostředku dopad.    |
|*ImpactedresourceDisplayName*     |     Název ovlivněné prostředku.  |
|*closedByUserAlias*     |   Uživatel, který zavřel výstrahu.      |

### <a name="get-resource-provider-health"></a>Získat stav zprostředkovatele prostředků

**Požadavek**

Požadavek získá stav pro všechny poskytovatele prostředků registrované.


|Metoda  |Identifikátor URI žádosti  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**Argumenty**


|Argumenty  |Popis  |
|---------|---------|
|*armendpoint*     |    Koncový bod správce prostředků prostředí Azure zásobníku v https://adminmanagement formátu. {RegionName}. {Externí plně kvalifikovaný název domény}. Například pokud je azurestack.external externí plně kvalifikovaný název domény a název oblasti je místní, pak koncového bodu správce prostředků je https://adminmanagement.local.azurestack.external.     |
|*subid*     |     ID předplatného uživatele, který je uskutečněním hovoru. Toto rozhraní API pro dotaz můžete použít pouze s uživatelem, který má oprávnění k předplatnému výchozího zprostředkovatele.    |
|*RegionName*     |     Název oblasti Azure zásobníku nasazení.    |
|*api-version*     |   Verze protokolu, který slouží k vytvoření této žádosti. Je nutné použít 2016-05-01.      |


**Odpověď**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Podrobnosti o odpovědi**


|Argument  |Popis  |
|---------|---------|
|*ID*     |   Jedinečné ID výstrahy.      |
|*name*     |  Interní název výstrahy.       |
|*Typ*     |  Definice prostředků.       |
|*location*     |  Název oblasti.       |
|*značek*     |     Značky prostředku.    |
|*RegistrationId*     |   Jedinečné registrace pro poskytovatele prostředků.      |
|*displayName*     |Zobrazovaný název zprostředkovatele prostředků.        |
|*namespace*     |   Implementuje rozhraní API obor názvů zprostředkovatele prostředků.       |
|*routePrefix*     |    Identifikátor URI pro interakci s poskytovatelem prostředků.     |
|*serviceLocation*     |   Oblast tohoto poskytovatele prostředků není zaregistrována.      |
|*infraURI*     |   Identifikátor URI poskytovatele prostředků, které jsou uvedeny jako role infrastruktury.      |
|*alertSummary*     |   Souhrn kritických a výstražných výstrahy, které jsou přidružené ke zprostředkovateli prostředků.      |
|*healthState*     |    Stav poskytovatele prostředků.     |


### <a name="get-resource-health"></a>Získat stav prostředku

Požadavek získá stav pro konkrétní registrované prostředek zprostředkovatele.

**Požadavek**

|Metoda  |Identifikátor URI žádosti  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**Argumenty**

|Argumenty  |Popis  |
|---------|---------|
|*armendpoint*     |    Koncový bod správce prostředků prostředí Azure zásobníku v https://adminmanagement formátu. {RegionName}. {Externí plně kvalifikovaný název domény}. Například pokud je azurestack.external externí plně kvalifikovaný název domény a název oblasti je místní, pak koncového bodu správce prostředků je https://adminmanagement.local.azurestack.external.     |
|*subid*     |ID předplatného uživatele, který je uskutečněním hovoru. Toto rozhraní API pro dotaz můžete použít pouze s uživatelem, který má oprávnění k předplatnému výchozího zprostředkovatele.         |
|*RegionName*     |  Název oblasti Azure zásobníku nasazení.       |
|*api-version*     |  Verze protokolu, který slouží k vytvoření této žádosti. Je nutné použít 2016-05-01.       |
|*RegistrationID* |ID registrace pro konkrétní prostředek zprostředkovatele. |

**Odpověď**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Podrobnosti o odpovědi**

|Argument  |Popis  |
|---------|---------|
|*ID*     |   Jedinečné ID výstrahy.      |
|*name*     |  Interní název výstrahy.       |
|*Typ*     |  Definice prostředků.       |
|*location*     |  Název oblasti.       |
|*značek*     |     Značky prostředku.    |
|*RegistrationId*     |   Jedinečné registrace pro poskytovatele prostředků.      |
|*Typ prostředku*     |Typ prostředku.        |
|*resourceName*     |   Název prostředku.   |
|*usageMetrics*     |    Využití metriky pro prostředek.     |
|*resourceLocation*     |   Název oblasti, kde je nasazen.      |
|*resourceURI*     |   Identifikátor URI pro prostředek.   |
|*alertSummary*     |   Souhrn kritické a upozornění výstrahy, stav.     |

## <a name="learn-more"></a>Další informace

Informace o předdefinovaných stavu monitorování najdete v tématu [monitorovat stav a výstrahy v zásobníku Azure](azure-stack-monitor-health.md).


## <a name="next-steps"></a>Další postup

[Integrace zabezpečení](azure-stack-integrate-security.md)