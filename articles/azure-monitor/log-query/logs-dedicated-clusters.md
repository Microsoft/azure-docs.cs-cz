---
title: Azure Monitor protokolovat vyhrazené clustery
description: Zákazníci, kteří ingestují víc než 1 TB dat monitorování, můžou místo sdílených clusterů používat vyhrazené.
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: a5cbbed3881433121f5ab811082969bc3c6c4f7f
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609940"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor protokolovat vyhrazené clustery

Azure Monitor protokolovat vyhrazené clustery: možnost nasazení, která umožňuje pokročilým funkcím pro Azure Monitor protokolovat zákazníky. Zákazníci s vyhrazenými clustery můžou zvolit pracovní prostory, které se mají hostovat na těchto clusterech.

Mezi možnosti, které vyžadují vyhrazené clustery, patří:

- **[Klíče spravované zákazníkem](../platform/customer-managed-keys.md)** – Šifrujte data clusteru pomocí klíčů, které jsou k dispozici a kontrolovány zákazníkem.
- **[Bezpečnostní modul](../platform/customer-managed-keys.md#customer-lockbox-preview)** – zákazníci můžou řídit žádostem o data přístup inženýrům podpory Microsoftu.
- **[Dvojité šifrování](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** chrání proti scénáři, kdy může dojít k ohrožení jednoho z šifrovacích algoritmů nebo klíčů. V takovém případě bude další vrstva šifrování nadále chránit vaše data.
- **[Více pracovních prostorů](../log-query/cross-workspace-query.md)** – Pokud zákazník používá více než jeden pracovní prostor pro produkční prostředí, může být vhodné použít vyhrazený cluster. Dotazy mezi jednotlivými pracovními prostory budou spouštěny rychleji, pokud jsou všechny pracovní prostory ve stejném clusteru. Je také možné, že používání vyhrazeného clusteru je výhodnější, protože přidělené úrovně rezervace kapacity berou v úvahu veškerou kombinaci clusterů a platí pro všechny pracovní prostory, i když jsou některé z nich malé a nemají nárok na slevu za rezervaci kapacity.

Vyhrazené clustery vyžadují, aby se zákazníci zavázali využívat kapacitu alespoň 1 TB příjmu dat za den. Migrace do vyhrazeného clusteru je jednoduchá. Nedochází ke ztrátě dat nebo přerušení služeb. 

> [!IMPORTANT]
> Vyhrazené clustery jsou schválené a plně podporované pro produkční nasazení. Vzhledem k dočasným omezením kapacity ale vyžadujeme, aby se tato funkce používala v předběžném registru. Pomocí kontaktů do Microsoftu poskytněte ID předplatných.

## <a name="management"></a>Správa 

Vyhrazené clustery se spravují pomocí prostředku Azure, který představuje Azure Monitor clusterů protokolů. Všechny operace se provádějí na tomto prostředku pomocí PowerShellu nebo REST API.

Po vytvoření clusteru je možné ho nakonfigurovat a připojit k němu pracovní prostory. Když je pracovní prostor propojený s clusterem, v clusteru se nacházejí nová data odesílaná do pracovního prostoru. Ke clusteru lze propojit pouze pracovní prostory, které jsou ve stejné oblasti jako cluster. Pracovní prostory můžou být na rozdíl od clusteru s některými omezeními. Další podrobnosti o těchto omezeních jsou obsaženy v tomto článku. 

Data ingestovaná do vyhrazených clusterů se dvakrát šifrují – jednou na úrovni služby pomocí klíčů spravovaných Microsoftem nebo [klíče spravovaného zákazníkem](../platform/customer-managed-keys.md)a jednou na úrovni infrastruktury pomocí dvou různých šifrovacích algoritmů a dvou různých klíčů. [Dvojité šifrování](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) chrání proti scénáři, kdy může dojít k ohrožení jednoho z šifrovacích algoritmů nebo klíčů. V takovém případě bude další vrstva šifrování nadále chránit vaše data. Vyhrazený cluster také umožňuje chránit data pomocí ovládacího prvku [bezpečnostní modul](../platform/customer-managed-keys.md#customer-lockbox-preview) .

Všechny operace na úrovni clusteru vyžadují `Microsoft.OperationalInsights/clusters/write` oprávnění akce v clusteru. Toto oprávnění může být uděleno prostřednictvím vlastníka nebo přispěvatele, který obsahuje `*/write` akci nebo prostřednictvím role přispěvatele Log Analytics, která tuto `Microsoft.OperationalInsights/*` akci obsahuje. Další informace o oprávněních Log Analytics najdete [v tématu Správa přístupu k datům protokolů a pracovním prostorům v Azure monitor](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Cenový model clusteru

Log Analytics vyhrazené clustery používají cenový model rezervace kapacity, který je minimálně 1000 GB/měsíc. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb.  Informace o cenách rezervací kapacity najdete na [stránce s cenami Azure monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

Úroveň rezervace kapacity clusteru je konfigurována prostřednictvím programu programově s Azure Resource Manager pomocí `Capacity` parametru v `Sku` . `Capacity`Hodnota je určena v jednotkách GB a může mít hodnoty 1000 GB/den nebo více v přírůstcích po 100 GB za den.

Existují dva režimy fakturace pro použití v clusteru. Tyto parametry mohou být zadány `billingType` parametrem při konfiguraci clusteru. 

1. **Cluster**: v tomto případě (což je výchozí nastavení) se fakturace pro ingestovaná data provádí na úrovni clusteru. Množství zpracovaných dat z každého pracovního prostoru přidruženého ke clusteru se agreguje za účelem výpočtu denního vyúčtování clusteru. 

2. **Pracovní prostory**: náklady na rezervaci kapacity pro váš cluster se úměrně připočítají k pracovním prostorům v clusteru (po zaúčtování pro přidělení podle uzlu z [Azure Security Center](../../security-center/index.yml) pro každý pracovní prostor.)

Pokud váš pracovní prostor používá starší verzi na cenové úrovni jednotlivých uzlů, bude při propojení s clusterem účtován na základě dat přijatých v rámci rezervace kapacity clusteru a již na jeden uzel. Pro přidělení dat jednotlivých uzlů z Azure Security Center bude nadále použito.

Další podrobnosti najdete [tady]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters): Log Analytics vyhrazené clustery k dispozici.

## <a name="asynchronous-operations-and-status-check"></a>Asynchronní operace a kontroly stavu

Některé kroky konfigurace běží asynchronně, protože je nepůjde rychle dokončit. Stav v odpovědi může být jedna z následujících: ' InProgress ', ' aktualizace ', ' odstranění ', ' úspěch nebo neúspěch ' včetně kódu chyby. Při použití REST odpověď zpočátku vrátí stavový kód HTTP 202 (přijato) a záhlaví s Azure-AsyncOperation vlastností:

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Stav asynchronní operace můžete zjistit odesláním žádosti o získání do hodnoty hlavičky Azure-AsyncOperation:

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>Vytvoření clusteru

Nejprve vytvoříte prostředky clusteru, abyste mohli začít vytvářet vyhrazený cluster.

Musí být zadány následující vlastnosti:

- **Název_clusteru**: používá se pro účely správy. Uživatelé nejsou k tomuto názvu vystaveni.
- **ResourceGroupName**: stejně jako u jakéhokoli prostředku Azure patří clustery do skupiny prostředků. Doporučujeme použít skupinu prostředků centrálního IT, protože clustery jsou obvykle sdíleny mnoha týmy v organizaci. Další informace o návrhu najdete v [návrhu nasazení Azure Monitorch protokolů](../platform/design-logs-deployment.md)
- **Umístění**: cluster se nachází v konkrétní oblasti Azure. K tomuto clusteru lze propojit pouze pracovní prostory nacházející se v této oblasti.
- **SkuCapacity**: při vytváření prostředku *clusteru* je nutné zadat úroveň *rezervace kapacity* (SKU). Úroveň *rezervace kapacity* může být v rozsahu 1 000 až 3 000 GB za den. V případě potřeby ji můžete aktualizovat v krocích 100 později. Pokud potřebujete úroveň rezervace kapacity vyšší než 3 000 GB za den, kontaktujte nás na adrese LAIngestionRate@microsoft.com . Další informace o nákladech clusteru najdete v tématu [Správa nákladů pro Log Analytics clustery](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) .

Po vytvoření prostředku *clusteru* můžete upravit další vlastnosti, jako je *SKU*, * keyVaultProperties nebo *billingType*. Další podrobnosti najdete níže.

> [!WARNING]
> Vytvoření clusteru aktivuje přidělení a zřizování prostředků. Dokončení této operace může trvat až hodinu. Doporučuje se spouštět asynchronně.

Uživatelský účet, který vytváří clustery, musí mít standardní oprávnění pro vytváření prostředků Azure: `Microsoft.Resources/deployments/*` a oprávnění k zápisu do clusteru `(Microsoft.OperationalInsights/clusters/write)` .

### <a name="create"></a>Vytvořit 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Call* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Response* (Odpověď)

Měla by být 202 (přijato) a záhlaví.

### <a name="check-cluster-provisioning-status"></a>Zkontroluje stav zřizování clusteru.

Zřizování clusteru Log Analytics trvá delší dobu. Stav zřizování můžete zjistit několika způsoby:

- Spusťte příkaz Get-AzOperationalInsightsCluster PowerShell s názvem skupiny prostředků a ověřte vlastnost ProvisioningState. Hodnota je *ProvisioningAccount* při zřizování a *úspěšném* dokončení.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Zkopírujte hodnotu URL Azure-AsyncOperation z odpovědi a postupujte podle kontroly stavu asynchronních operací.

- Odešlete požadavek GET na prostředek *clusteru* a podívejte se na hodnotu *provisioningState* . Hodnota je *ProvisioningAccount* při zřizování a *úspěšném* dokončení.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **Response** (Odpověď)

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

*PrincipalId* GUID vygeneruje Služba Managed identity pro prostředek *clusteru* .

## <a name="link-a-workspace-to-cluster"></a>Propojení pracovního prostoru s clusterem

Když je pracovní prostor propojený s vyhrazeným clusterem, nová data, která se ingestují do pracovního prostoru, se směrují do nového clusteru, zatímco stávající data zůstanou v existujícím clusteru. Pokud je vyhrazený cluster zašifrovaný pomocí klíčů spravovaných zákazníkem (CMK), zašifrují se pomocí klíče jenom nová data. Systém tento rozdíl odstará od uživatelů a uživatelé se k pracovnímu prostoru dotazují jenom obvyklým způsobem, zatímco systém provádí dotazy mezi clustery na back-endu.

Cluster se dá propojit až 100 pracovních prostorů. Propojené pracovní prostory se nacházejí ve stejné oblasti jako cluster. K ochraně back-endu systému a zabránění fragmentaci dat nelze pracovní prostor propojit s clusterem více než dvakrát měsíčně.

Chcete-li provést operaci propojení, je nutné mít oprávnění Write pro pracovní prostor i pro prostředek *clusteru* :

- V pracovním prostoru: *Microsoft. OperationalInsights/pracovní prostory/Write*
- V prostředku *clusteru* : *Microsoft. OperationalInsights/Clusters/Write*

Kromě aspektů fakturace udržuje propojený pracovní prostor vlastní nastavení, jako je třeba Délka uchovávání dat.
Pracovní prostor a cluster můžou být v různých předplatných. Je možné, že se pracovní prostor a cluster nacházejí v různých klientech, pokud se k mapování obou nich na jednoho klienta používá služba Azure Lighthouse.

Propojení pracovního prostoru se všemi operacemi clusteru se dá provést až po dokončení zřizování clusteru Log Analytics.

> [!WARNING]
> Propojení pracovního prostoru s clusterem vyžaduje synchronizaci více součástí back-end a zajištění vysazování mezipaměti. Dokončení této operace může trvat až dvě hodiny. Doporučujeme spustit ho asynchronně.


**PowerShell**

K připojení ke clusteru použijte následující příkaz prostředí PowerShell:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

K propojení s clusterem použijte následující volání REST:

*Odeslat*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Response* (Odpověď)

202 (přijato) a záhlaví.

### <a name="check-workspace-link-status"></a>Ověřit stav odkazu na pracovní prostor
  
Pokud používáte klíče spravované zákazníkem, budou se po operaci přidružení po operaci přidružení ukládat ingestovaná data se spravovaným klíčem, což může trvat až 90 minut. 

Stav přidružení pracovního prostoru můžete zjistit dvěma způsoby:

- Zkopírujte hodnotu URL Azure-AsyncOperation z odpovědi a postupujte podle kontroly stavu asynchronních operací.

- V pracovním prostoru proveďte operaci get a sledujte, zda je v odpovědi v části *funkce* přítomna vlastnost *clusterResourceId* .

**Rozhraní příkazového řádku**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*Response* (Odpověď)

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>Změnit vlastnosti clusteru

Po vytvoření prostředku *clusteru* a jeho úplné zřízení můžete na úrovni clusteru upravit další vlastnosti pomocí PowerShellu nebo REST API. Kromě vlastností, které jsou k dispozici během vytváření clusteru, lze další vlastnosti nastavit pouze po zřízení clusteru:

- **keyVaultProperties** – aktualizuje klíč v Azure Key Vault. Viz část [aktualizace clusteru s podrobnostmi identifikátoru klíče](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details). Obsahuje následující parametry: *KeyVaultUri*, KeyName, *verze* *klíče*. 
- **billingType** – vlastnost *billingType* Určuje přidělení fakturace pro prostředek *clusteru* a jeho data:
  - **Cluster** (výchozí) – náklady na rezervaci kapacity pro váš cluster se připočítají ke zdroji *clusteru* .
  - **Pracovní prostory** – náklady na rezervaci kapacity pro váš cluster jsou úměrně přičteny k pracovním prostorům v clusteru s prostředkem *clusteru* , který se fakturuje jako část využití, pokud je celkový počet zpracovaných dat v daném dni v rámci rezervace kapacity. Další informace o cenovém modelu clusteru najdete v tématu [Log Analytics vyhrazené clustery](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) . 

> [!NOTE]
> Vlastnost *billingType* není v prostředí PowerShell podporována.

### <a name="get-all-clusters-in-resource-group"></a>Načíst všechny clustery ve skupině prostředků
  
**Rozhraní příkazového řádku**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*Call*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*Response* (Odpověď)
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>Načíst všechny clustery v předplatném

**Rozhraní příkazového řádku**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*Call*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*Response* (Odpověď)
    
Stejné jako u ' clusterů ve skupině prostředků ', ale v oboru předplatného.



### <a name="update-capacity-reservation-in-cluster"></a>Aktualizovat rezervaci kapacity v clusteru

Když se datový svazek do propojených pracovních prostorů změní v čase a chcete patřičně aktualizovat úroveň rezervace kapacity. Kapacita je určena v jednotkách GB a může mít hodnoty 1000 GB/den nebo více v přírůstcích po 100 GB za den. Všimněte si, že nemusíte zadávat úplný text žádosti REST, ale měla by obsahovat SKU.

**Rozhraní příkazového řádku**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*Call*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>Aktualizace billingType v clusteru

Vlastnost *billingType* Určuje přidělení fakturace pro cluster a jeho data:
- *cluster* (výchozí) – fakturace je přidělená předplatnému hostujícímu váš prostředek clusteru.
- *pracovní prostory* – fakturace je úměrná předplatným hostujícím vaše pracovní prostory.

**REST**

*Call*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>Zrušení propojení pracovního prostoru s clusterem

Pracovní prostor můžete odpojit od clusteru. Po odpojení pracovního prostoru od clusteru se do vyhrazeného clusteru neodesílají nová data přidružená k tomuto pracovnímu prostoru. Faktura v pracovním prostoru se už neprovádí přes cluster. Stará data nepropojeného pracovního prostoru můžou zůstat v clusteru. Pokud jsou tato data zašifrovaná pomocí klíčů spravovaných zákazníkem (CMK), zachovají se Key Vault tajné klíče. Systém tuto změnu z Log Analytics uživatelů deabstrakce. Uživatelé můžou pracovní prostor jednoduše dotazovat obvyklým způsobem. Systém provádí dotazy meziclusterů v back-endu podle potřeby bez indikace uživatelům.  

> [!WARNING] 
> Mezi jednotlivými pracovními prostory v měsíci je limit dvou operací propojení. Vezměte v úvahu čas zvážit a naplánovat zrušení propojení akcí.

**Rozhraní příkazového řádku**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

Pomocí následujícího příkazu PowerShellu odpojte pracovní prostor od clusteru:

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>Odstranění clusteru

Vyhrazený prostředek clusteru je možné odstranit. Aby bylo možné odstranit všechny pracovní prostory z clusteru, je nutné zrušit propojení všech pracovních prostorů. K provedení této operace potřebujete oprávnění Write pro prostředek *clusteru* . 

Po odstranění prostředku clusteru vstoupí fyzický cluster do procesu vyprázdnění a odstranění. Odstranění clusteru odstraní všechna data, která byla uložena v clusteru. Data mohou být z pracovních prostorů, které byly v minulosti propojeny s clusterem.

Prostředek *clusteru* , který se odstranil za posledních 14 dní, je ve stavu obnovitelného odstranění a dá se obnovit s jeho daty. Vzhledem k tomu, že se všechny pracovní prostory odřadí z prostředku *clusteru* s odstraněním prostředku *clusteru* , je potřeba po obnovení znovu přidružit své pracovní prostory. Tuto operaci obnovení nemůže uživatel kontaktovat, pokud se na váš kanál Microsoft Channel nebo podporu pro požadavky na obnovení nedají použít.

Během 14 dní po odstranění se název prostředku clusteru rezervuje a nemůže ho používat jiné prostředky.

> [!WARNING] 
> U každého předplatného je limit tří clusterů. Aktivní i tiché odstraněné clustery se počítají jako součást tohoto. Zákazníci by neměli vytvářet opakující se postupy, které vytvářejí a odstraňují clustery. Má významný dopad na Log Analytics systémů back-end.

**PowerShell**

K odstranění clusteru použijte následující příkaz prostředí PowerShell:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Pomocí následujícího volání REST odstraňte cluster:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response** (Odpověď)

  200 OK

## <a name="limits-and-constraints"></a>Omezení a omezení

- Maximální počet clusterů na oblast a předplatné je 2.

- Maximální počet propojených pracovních prostorů ke clusteru je 1000.

- Pracovní prostor můžete propojit s clusterem a pak ho odpojit. Počet operací propojení s pracovním prostorem v konkrétním pracovním prostoru je omezený na 2 v období 30 dnů.

- Přesun clusteru do jiné skupiny prostředků nebo předplatného se momentálně nepodporuje.

- V Číně momentálně není k dispozici bezpečnostní modul. 

- Pro clustery vytvořené z října 2020 se v podporovaných oblastech automaticky nakonfiguruje [dvojité šifrování](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) . Odesláním žádosti o získání do clusteru můžete ověřit, jestli je váš cluster nakonfigurovaný pro šifrování s dvojitým šifrováním a zda `isDoubleEncryptionEnabled` je tato hodnota `true` pro clustery s povoleným dvojitým šifrováním. 
  - Pokud vytvoříte cluster a obdržíte chybu "<název oblasti> nepodporuje dvojité šifrování pro clustery", můžete cluster i nadále vytvořit bez šifrování dvojitým šifrováním přidáním `"properties": {"isDoubleEncryptionEnabled": false}` do textu žádosti REST.
  - Nastavení dvojitého šifrování nelze změnit po vytvoření clusteru.

## <a name="troubleshooting"></a>Řešení potíží

- Pokud při vytváření clusteru dojde k chybě, může to být tím, že jste cluster odstranili za posledních 14 dní a je ve stavu obnovitelného odstranění. Název clusteru zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Název se uvolní po uplynutí doby tichého odstranění, kdy se cluster trvale odstraní.

- Pokud cluster aktualizujete při zřizování nebo aktualizaci stavu, aktualizace se nezdaří.

- Některé operace jsou dlouhé a jejich dokončení může chvíli trvat – jedná se o vytvoření clusteru, aktualizaci klíčů clusteru a odstranění clusteru. Stav operace můžete zjistit dvěma způsoby:
  - Pokud používáte REST, zkopírujte hodnotu adresy URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
  - Odešlete požadavek GET do clusteru nebo pracovního prostoru a sledujte odpověď. Například nepropojený pracovní prostor nebude mít *clusterResourceId* v části *funkce*.

- Odkaz na pracovní prostor na cluster se nezdaří, pokud je propojený s jiným clusterem.

- Chybové zprávy
  
  Vytvoření clusteru:
  -  400 – název clusteru není platný. Název clusteru může obsahovat znaky a – z, A-Z, 0-9 a délku 3-63.
  -  400 – tělo požadavku má hodnotu null nebo je v nesprávném formátu.
  -  400--název SKU je neplatný. Nastavte název SKU na capacityReservation.
  -  400 – byla zadána kapacita, ale SKU není capacityReservation. Nastavte název SKU na capacityReservation.
  -  400 – chybějící kapacita v SKU V krocích po 100 (GB) nastavte hodnotu kapacity na 1000 nebo vyšší.
  -  400 – kapacita v SKU není v rozsahu. Hodnota by měla být minimálně 1000 až do maximální povolené kapacity, která je k dispozici v pracovním prostoru v části využití a odhadované náklady.
  -  400 – kapacita je uzamčena po dobu 30 dnů. Snížení kapacity je povolené 30 dnů od aktualizace.
  -  400 – nebyla nastavena žádná SKU. Nastavte název SKU na capacityReservation a hodnotu kapacity na 1000 nebo vyšší v krocích po 100 (GB).
  -  400--identita je null nebo prázdná. Nastavte identitu pomocí typu systemAssigned.
  -  400 – KeyVaultProperties jsou nastaveny při vytváření. Aktualizujte KeyVaultProperties po vytvoření clusteru.
  -  400 – operaci nelze nyní provést. Asynchronní operace je v jiném než úspěšném stavu. Cluster musí před provedením jakékoli operace aktualizace dokončit jeho operaci.

  Aktualizace clusteru
  -  400 – cluster je ve stavu odstraňování. Probíhá asynchronní operace. Cluster musí před provedením jakékoli operace aktualizace dokončit jeho operaci.
  -  400 – KeyVaultProperties není prázdný, ale má špatný formát. Viz [aktualizace identifikátoru klíče](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400 – nepovedlo se ověřit klíč v Key Vault. Příčinou může být nedostatečná oprávnění nebo pokud klíč neexistuje. Ověřte, že jste [nastavili zásady klíče a přístupu](../platform/customer-managed-keys.md#grant-key-vault-permissions) v Key Vault.
  -  400-klíč nelze obnovit. Key Vault musí být nastavené na obnovitelné odstranění a ochranu vyprázdnit. Viz [dokumentace Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400 – operaci nelze nyní provést. Počkejte, až se operace Async dokončí, a zkuste to znovu.
  -  400 – cluster je ve stavu odstraňování. Počkejte, až se operace Async dokončí, a zkuste to znovu.

  Cluster Get:
    -  404--cluster nebyl nalezen, cluster byl pravděpodobně odstraněn. Pokud se pokusíte vytvořit cluster s tímto názvem a dojde ke konfliktu, je cluster v tichém odstranění po dobu 14 dnů. Pokud chcete vytvořit nový cluster, obraťte se na podporu, nebo použijte jiný název. 

  Odstranění clusteru
    -  409 – nelze odstranit cluster ve stavu zřizování. Počkejte, až se operace Async dokončí, a zkuste to znovu.

  Odkaz na pracovní prostor:
  -  404 – pracovní prostor nebyl nalezen. Pracovní prostor, který jste zadali, neexistuje nebo byl odstraněn.
  -  409--operace propojení pracovního prostoru nebo zrušení propojení v procesu.
  -  400 – cluster nebyl nalezen, zadaný cluster neexistuje nebo byl odstraněn. Pokud se pokusíte vytvořit cluster s tímto názvem a dojde ke konfliktu, je cluster v tichém odstranění po dobu 14 dnů. Můžete se obrátit na podporu a obnovit ji.

  Zrušení propojení pracovního prostoru:
  -  404 – pracovní prostor nebyl nalezen. Pracovní prostor, který jste zadali, neexistuje nebo byl odstraněn.
  -  409--operace propojení pracovního prostoru nebo zrušení propojení v procesu.

## <a name="next-steps"></a>Další kroky

- Další informace o [Log Analytics vyhrazeném účtování clusteru](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Další informace o [správném návrhu pracovních prostorů Log Analytics](../platform/design-logs-deployment.md)
