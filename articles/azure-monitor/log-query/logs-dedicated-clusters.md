---
title: Azure Monitor protokolovat vyhrazené clustery
description: Zákazníci, kteří ingestují víc než 1 TB dat monitorování, můžou místo sdílených clusterů používat vyhrazené.
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: e5ab3800e2d20bec34f321e0992240be8624404c
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400859"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor protokolovat vyhrazené clustery

Azure Monitor protokoly vyhrazených clusterů je možnost nasazení, která je k dispozici pro lepší obsluhu zákazníků s vysokým objemem. Zákazníci, kteří ingestují více než 4 TB dat za den, budou používat vyhrazené clustery. Zákazníci s vyhrazenými clustery můžou zvolit pracovní prostory, které se mají hostovat na těchto clusterech.

Kromě podpory pro velký objem, existují další výhody používání vyhrazených clusterů:

- **Omezení četnosti** – zákazník může mít vyšší omezení přenosové rychlosti, jenom u vyhrazeného clusteru.
- **Funkce** – některé podnikové funkce jsou dostupné jenom na vyhrazených clusterech – konkrétně na základě zákaznických klíčů (CMK) a podpory bezpečnostního modulu. 
- **Konzistence** – zákazníci mají své vlastní vyhrazené prostředky, takže neovlivňují jiné zákazníky, kteří používají stejnou sdílenou infrastrukturu.
- **Cenová efektivita** – může být výhodnější použít vyhrazený cluster, protože přidělené úrovně rezervace kapacity berou v úvahu veškerou kombinaci clusterů a platí pro všechny své pracovní prostory, i když jsou některé z nich malé a nemají nárok na zlevněnou slevu kapacity.
- Dotazy **mezi pracovními prostory** fungují rychleji, pokud jsou všechny pracovní prostory ve stejném clusteru.

Vyhrazené clustery vyžadují, aby se zákazníci zavázali využívat kapacitu alespoň 1 TB příjmu dat za den. Migrace do vyhrazeného clusteru je jednoduchá. Nedochází ke ztrátě dat nebo přerušení služeb. 

> [!IMPORTANT]
> Vyhrazené clustery jsou schválené a plně podporované pro produkční nasazení. Vzhledem k dočasným omezením kapacity ale vyžadujeme, aby se tato funkce používala v předběžném registru. Pomocí kontaktů do Microsoftu poskytněte ID předplatných.

## <a name="management"></a>Správa 

Vyhrazené clustery se spravují pomocí prostředku Azure, který představuje Azure Monitor clusterů protokolů. Všechny operace se provádějí na tomto prostředku pomocí PowerShellu nebo REST API.

Po vytvoření clusteru je možné ho nakonfigurovat a připojit k němu pracovní prostory. Když je pracovní prostor propojený s clusterem, v clusteru se nacházejí nová data odesílaná do pracovního prostoru. Ke clusteru lze propojit pouze pracovní prostory, které jsou ve stejné oblasti jako cluster. Pracovní prostory můžou být na rozdíl od clusteru s některými omezeními. Další podrobnosti o těchto omezeních jsou obsaženy v tomto článku. 

Všechny operace na úrovni clusteru vyžadují `Microsoft.OperationalInsights/clusters/write` oprávnění akce v clusteru. Toto oprávnění může být uděleno prostřednictvím vlastníka nebo přispěvatele, který obsahuje `*/write` akci nebo prostřednictvím role přispěvatele Log Analytics, která tuto `Microsoft.OperationalInsights/*` akci obsahuje. Další informace o oprávněních Log Analytics najdete [v tématu Správa přístupu k datům protokolů a pracovním prostorům v Azure monitor](../platform/manage-access.md). 

## <a name="billing"></a>Fakturace

Vyhrazené clustery se podporují jenom pro pracovní prostory, které používají plány na GB s úrovněmi rezervace kapacity nebo bez nich. Vyhrazeným clusterům nejsou žádné další poplatky za Zákazníky, kteří se zavazují pro ingestování více než 1 TB pro tento cluster. Možnost potvrdit do ingesta znamená, že jim byla přiřazena úroveň rezervace kapacity alespoň 1 TB/den na úrovni clusteru. I když je rezervace kapacity připojená na úrovni clusteru, existují dvě možnosti pro skutečný zpoplatnění dat:

- *Cluster* (výchozí) – náklady na rezervaci kapacity pro váš cluster se připočítají ke zdroji *clusteru* .
- *Pracovní prostory* – náklady na rezervaci kapacity pro váš cluster jsou úměrně připsány k pracovním prostorům v clusteru. Pokud je celkový počet zpracovaných dat za den v rámci rezervace kapacity, účtuje se v prostředku *clusteru* některé z použití. Další informace o cenovém modelu clusteru najdete v tématu [Log Analytics vyhrazené clustery](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) .

Další informace o vyúčtování vyhrazených clusterů najdete v tématu [Log Analytics pro fakturaci vyhrazeného clusteru](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters).

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
invoke-command -scriptblock { New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} } -asjob

# Check when the job is done
Get-Job

```

**REST**

*Call* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

Mělo by být 200 OK a záhlaví.

### <a name="check-provisioning-status"></a>Kontrola stavu zřizování

Zřizování clusteru Log Analytics trvá delší dobu. Stav zřizování můžete zjistit dvěma způsoby:

1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle kontroly stavu asynchronních operací.

   NEBO

1. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na hodnotu *provisioningState* . Hodnota je *ProvisioningAccount* při zřizování a *úspěšném* dokončení.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

## <a name="change-cluster-properties"></a>Změnit vlastnosti clusteru

Po vytvoření prostředku *clusteru* a jeho úplné zřízení můžete na úrovni clusteru upravit další vlastnosti pomocí PowerShellu nebo REST API. Kromě vlastností, které jsou k dispozici během vytváření clusteru, lze další vlastnosti nastavit pouze po zřízení clusteru:

- **keyVaultProperties**: slouží ke konfiguraci Azure Key Vault používaného ke zřízení [Azure monitor klíče spravovaného zákazníkem](../platform/customer-managed-keys.md#cmk-provisioning-procedure). Obsahuje následující parametry: *KeyVaultUri*, KeyName, *verze* *klíče*. 
- **billingType** – vlastnost *billingType* Určuje přidělení fakturace pro prostředek *clusteru* a jeho data:
- **Cluster** (výchozí) – náklady na rezervaci kapacity pro váš cluster se připočítají ke zdroji *clusteru* .
- **Pracovní prostory** – náklady na rezervaci kapacity pro váš cluster jsou úměrně přičteny k pracovním prostorům v clusteru s prostředkem *clusteru* , který se fakturuje jako část využití, pokud je celkový počet zpracovaných dat v daném dni v rámci rezervace kapacity. Další informace o cenovém modelu clusteru najdete v tématu [Log Analytics vyhrazené clustery](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) . 

> [!NOTE]
> Vlastnost *billingType* není v prostředí PowerShell podporována.
> Aktualizace vlastností clusteru můžou být spouštěny jako asynchronní a dokončení může nějakou dobu trvat.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> Pomocí opravy můžete aktualizovat *SKU*prostředků *clusteru* , *keyVaultProperties* nebo *billingType* .

Příklad: 

*Call*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" obsahuje podrobnosti o identifikátoru Key Vaultho klíče.

*Response* (Odpověď)

200 OK a záhlaví

### <a name="check-cluster-update-status"></a>Zkontroluje stav aktualizace clusteru.

Dokončení šíření identifikátoru klíče trvá několik minut. Stav aktualizace můžete zjistit dvěma způsoby:

- Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle kontroly stavu asynchronních operací. 

   NEBO

- Odešlete požadavek GET na prostředek *clusteru* a podívejte se na vlastnosti *KeyVaultProperties* . Nedávno aktualizované podrobnosti identifikátoru klíče by se měly vrátit v odpovědi.

   Odpověď na požadavek GET prostředku *clusteru* by měla vypadat jako při dokončení aktualizace identifikátoru klíče:

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principle-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "keyVaultProperties": {
         "keyVaultUri": "https://key-vault-name.vault.azure.net",
         "kyName": "key-name",
         "keyVersion": "current-version"
         },
        "provisioningState": "Succeeded",
       "billingType": "cluster",
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
  }
  ```

## <a name="link-a-workspace-to-the-cluster"></a>Propojení pracovního prostoru s clusterem

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


### <a name="link-operations"></a>Operace propojení

**PowerShell**

K připojení ke clusteru použijte následující příkaz prostředí PowerShell:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
invoke-command -scriptblock { Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId } -asjob

# Check when the job is done
Get-Job
```


**REST**

K propojení s clusterem použijte následující volání REST:

*Odeslat*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Response* (Odpověď)

200 OK a záhlaví.

### <a name="using-customer-managed-keys-with-linking"></a>Použití klíčů spravovaných zákazníkem s propojením

Pokud používáte klíče spravované zákazníkem, budou se po operaci přidružení po operaci přidružení ukládat ingestovaná data se spravovaným klíčem, což může trvat až 90 minut. 

Stav přidružení pracovního prostoru můžete zjistit dvěma způsoby:

- Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle kontroly stavu asynchronních operací.

- Odeslání [pracovních prostorů – Získejte](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) požadavek a sledujte odpověď. V přidruženém pracovním prostoru je clusterResourceId v části funkce.

Požadavek Send vypadá následovně:

*Odeslat*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Zrušení propojení pracovního prostoru z vyhrazeného clusteru

Pracovní prostor můžete odpojit od clusteru. Po odpojení pracovního prostoru od clusteru se do vyhrazeného clusteru neodesílají nová data přidružená k tomuto pracovnímu prostoru. Faktura v pracovním prostoru se už neprovádí přes cluster. Stará data nepropojeného pracovního prostoru můžou zůstat v clusteru. Pokud jsou tato data zašifrovaná pomocí klíčů spravovaných zákazníkem (CMK), zachovají se Key Vault tajné klíče. Systém tuto změnu z Log Analytics uživatelů deabstrakce. Uživatelé můžou pracovní prostor jednoduše dotazovat obvyklým způsobem. Systém provádí dotazy meziclusterů v back-endu podle potřeby bez indikace uživatelům.  

> [!WARNING] 
> Mezi jednotlivými pracovními prostory a měsícem je limit dvou operací propojení. Vezměte v úvahu čas zvážit a naplánovat zrušení propojení akcí. 

## <a name="delete-a-dedicated-cluster"></a>Odstranění vyhrazeného clusteru

Vyhrazený prostředek clusteru je možné odstranit. Aby bylo možné odstranit všechny pracovní prostory z clusteru, je nutné zrušit propojení všech pracovních prostorů. Po odstranění prostředku clusteru vstoupí fyzický cluster do procesu vyprázdnění a odstranění. Odstranění clusteru odstraní všechna data, která byla uložena v clusteru. Data mohou být z pracovních prostorů, které byly v minulosti propojeny s clusterem.

## <a name="next-steps"></a>Další kroky

- Další informace o [Log Analytics vyhrazeném účtování clusteru](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Další informace o [správném návrhu pracovních prostorů Log Analytics](../platform/design-logs-deployment.md)
