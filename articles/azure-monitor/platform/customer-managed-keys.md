---
title: Klíč spravovaný zákazníkem v Azure Monitoru
description: Informace a kroky pro konfiguraci klíče spravovaného zákazníkem k šifrování dat ve vašich Log Analyticsch pracovních prostorech pomocí Azure Key Vaultho klíče.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 9d8d37e1b161dfc8344d7ff03bc0093d23f86101
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917828"
---
# <a name="azure-monitor-customer-managed-key"></a>Klíč spravovaný zákazníkem v Azure Monitoru 

Data v Azure Monitor jsou šifrovaná pomocí klíčů spravovaných Microsoftem. K ochraně dat a uložených dotazů v pracovních prostorech můžete použít vlastní šifrovací klíč. Když zadáte klíč spravovaný zákazníkem, tento klíč se použije k ochraně a řízení přístupu k datům a po jejich nakonfigurování se všechna data odesílaná do vašich pracovních prostorů šifrují pomocí klíče Azure Key Vault. Klíče spravované zákazníkem nabízejí větší flexibilitu při správě řízení přístupu.

Před konfigurací doporučujeme zkontrolovat níže uvedená [omezení a omezení](#limitationsandconstraints) .

## <a name="customer-managed-key-overview"></a>Přehled klíčů spravovaných zákazníkem

[Šifrování v klidovém umístění](../../security/fundamentals/encryption-atrest.md) je běžným požadavkem na ochranu osobních údajů a zabezpečení v organizacích. Azure vám umožní plně spravovat šifrování v klidovém režimu, zatímco máte k dispozici různé možnosti, jak pečlivě spravovat šifrovací a šifrovací klíče.

Azure Monitor zajistí, že všechna data a uložené dotazy budou v klidovém stavu zašifrované pomocí klíčů spravovaných Microsoftem (MMK). Azure Monitor taky nabízí možnost šifrování pomocí vlastního klíče, který je uložený v [Azure Key Vault](../../key-vault/general/overview.md), což vám umožní řídit přístup k vašim datům kdykoli. Azure Monitor použití šifrování je stejné jako způsob, jakým [Azure Storage šifrování](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) funguje.

Klíč spravovaný zákazníkem se doručuje na [vyhrazené clustery](../log-query/logs-dedicated-clusters.md) , které poskytují vyšší úroveň ochrany a řízení. Data ingestovaná do vyhrazených clusterů se dvakrát šifrují – jednou na úrovni služby pomocí klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem a jednou na úrovni infrastruktury pomocí dvou různých šifrovacích algoritmů a dvou různých klíčů. [Dvojité šifrování](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) chrání proti scénáři, kdy může dojít k ohrožení jednoho z šifrovacích algoritmů nebo klíčů. V takovém případě bude další vrstva šifrování nadále chránit vaše data. Vyhrazený cluster také umožňuje chránit data pomocí ovládacího prvku [bezpečnostní modul](#customer-lockbox-preview) .

Data ingestovaná za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tato data zůstávají zašifrovaná pomocí klíčů Microsoftu bez ohledu na konfiguraci klíče spravované zákazníkem, ale vaše kontrola nad daty SSD dodržuje [odvolání klíčů](#key-revocation). Pracujeme na tom, aby data SSD zašifrovaná pomocí klíče spravovaného zákazníkem v první polovině roku 2021.

Log Analytics vyhrazené clustery používají [cenový model](../log-query/logs-dedicated-clusters.md#cluster-pricing-model) rezervace kapacity od 1000 GB za den.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Jak klíč spravovaný zákazníkem funguje v Azure Monitor

Azure Monitor používá spravovanou identitu pro udělení přístupu k vašemu Azure Key Vault. Identita clusteru Log Analytics je podporovaná na úrovni clusteru. K povolení ochrany klíčů spravované zákazníkem ve více pracovních prostorech provede nový prostředek Log Analytics *clusteru* jako zprostředkující připojení identity mezi Key Vault a vašimi pracovními prostory Log Analytics. Úložiště clusteru používá spravovanou identitu, která je \' přidružená k prostředku *clusteru* k ověření pro vaši Azure Key Vault prostřednictvím Azure Active Directory. 

Po konfiguraci klíče spravovaného zákazníkem se s vaším klíčem šifrují nová ingestovaná data do pracovních prostorů propojených s vaším vyhrazeným clusterem. Pracovní prostory z clusteru můžete kdykoli zrušit. Nová data se pak ingestují Log Analytics úložiště a šifrují pomocí klíče Microsoft Key, zatímco můžete bez problémů zadávat dotazy na nová a stará data.

> [!IMPORTANT]
> Klíčové funkce spravované zákazníkem jsou regionální. Pracovní prostory Azure Key Vault, cluster a propojené Log Analytics musí být ve stejné oblasti, ale mohou být v různých předplatných.

![Přehled klíčů spravovaných zákazníkem](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics prostředek *clusteru* , který má spravovanou identitu s oprávněními Key Vault – identita se rozšíří na Log Analytics vyhrazené úložiště clusteru s Underlay
3. Vyhrazený cluster Log Analytics
4. Pracovní prostory připojené ke zdroji *clusteru* 

### <a name="encryption-keys-operation"></a>Operace šifrovacích klíčů

Šifrování dat úložiště má tři typy klíčů:

- **KEK** klíč šifrovacího klíče (váš zákazníkem spravovaný klíč)
- **AEK** šifrovací klíč účtu
- **Klíč DEK** – šifrovací klíč dat

Platí následující pravidla:

- Účty úložiště Log Analytics clusteru generují jedinečný šifrovací klíč pro každý účet úložiště, který se označuje jako AEK.
- AEK se používá k odvození DEKs, což jsou klíče, které slouží k zašifrování každého bloku dat zapsaných na disk.
- Když nakonfigurujete klíč v Key Vault a odkazujete na něj v clusteru, Azure Storage posílá žádosti na vaše Azure Key Vault k zabalení a rozbalení AEK k provádění operací šifrování a dešifrování dat.
- Váš KEK nikdy nezůstane Key Vault a v případě klíče HSM nikdy neopustí hardware.
- Azure Storage používá spravovanou identitu, která je přidružená k prostředku *clusteru* pro ověřování a přístup k Azure Key Vault prostřednictvím Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed kroky zřizování klíčů

1. Vytváření Azure Key Vault a ukládání klíče
1. Vytváření clusteru
1. Udělování oprávnění vašemu Key Vault
1. Aktualizuje se cluster s podrobnostmi identifikátoru klíče.
1. Propojení Log Analyticsch pracovních prostorů

Konfigurace klíče spravovaného zákazníkem se Azure Portal v současné době nepodporuje a zřizování se dá provádět přes [PowerShell](/powershell/module/az.operationalinsights/), [CLI](/cli/azure/monitor/log-analytics) nebo požadavky [REST](/rest/api/loganalytics/) .

### <a name="asynchronous-operations-and-status-check"></a>Asynchronní operace a kontroly stavu

Některé kroky konfigurace běží asynchronně, protože je nepůjde rychle dokončit. `status`V reakci může být jedna z následujících: ' InProgress ', ' aktualizace ', ' odstranění ', ' uspěla nebo ' neúspěšné ' s kódem chyby.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="rest"></a>[REST](#tab/rest)

Při použití REST odpověď zpočátku vrátí stavový kód HTTP 202 (přijato) a hlavičku s vlastností *Azure-AsyncOperation* :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Stav asynchronní operace můžete zjistit odesláním požadavku GET do koncového bodu v hlavičce *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Ukládání šifrovacího klíče (KEK)

Vytvořte nebo použijte Azure Key Vault, který již máte vygenerovat, nebo importujte klíč, který se má použít k šifrování dat. Azure Key Vault musí být nakonfigurovaná tak, aby chránila váš klíč a přístup k vašim datům v Azure Monitor. Tuto konfiguraci můžete ověřit v části vlastnosti Key Vault, měla by být povolená ochrana proti *odstranění* a *vyprázdnění* .

![Obnovitelné odstranění a mazání nastavení ochrany](media/customer-managed-keys/soft-purge-protection.png)

Tato nastavení je možné aktualizovat v Key Vault prostřednictvím rozhraní příkazového řádku a PowerShellu:

- [Obnovitelné odstranění](../../key-vault/general/soft-delete-overview.md)
- [Vyprázdnit](../../key-vault/general/soft-delete-overview.md#purge-protection) ochranné Guard proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění

## <a name="create-cluster"></a>Vytvoření clusteru

Clustery podporují dva [typy spravovaných identit](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types): přiřazeno systémem a přiřazeno uživateli, zatímco jedna identita může být definována v clusteru v závislosti na vašem scénáři. 
- Spravovaná identita přiřazená systémem se zjednodušuje a generuje se automaticky s vytvořením clusteru, když `type` je identita nastavená na "*SystemAssigned*". Tuto identitu můžete později použít k udělení přístupu úložiště k vašemu Key Vault pro zabalení a rozbalení operací. 
  
  Nastavení identit v clusteru pro spravovanou identitu přiřazenou systémem
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Pokud chcete nakonfigurovat klíč spravovaný zákazníkem při vytváření clusteru, měli byste mít předem přidělenou klíčovou a uživatelem přiřazenou identitu Key Vault a pak vytvořit cluster s tímto nastavením: identita `type` jako "*UserAssigned*", `UserAssignedIdentities` s *ID prostředku* vaší identity.

  Nastavení identit v clusteru pro spravovanou identitu přiřazenou uživatelem
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft. ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Spravovanou identitu přiřazenou uživatelem nemůžete použít, pokud je vaše Key Vault v Private-Link (vNet). V tomto scénáři můžete použít spravovanou identitu přiřazenou systémem.

Postupujte podle postupu popsaného v [článku věnovaném vyhrazeným clusterům](../log-query/logs-dedicated-clusters.md#creating-a-cluster). 

## <a name="grant-key-vault-permissions"></a>Udělení oprávnění Key Vault

Vytvoření zásad přístupu v Key Vault pro udělení oprávnění vašemu clusteru. Tato oprávnění používá Underlay Azure Monitor Storage. Otevřete Key Vault v Azure Portal a klikněte na *"zásady přístupu"* , pak *"+ Přidat zásadu přístupu"* a vytvořte zásadu s těmito nastaveními:

- Klíčová oprávnění: vyberte *Get*, *Wrap Key* a *Unwrap Key*.
- Vyberte objekt zabezpečení: v závislosti na typu identity používaném v clusteru (spravované identity systémem nebo uživatelem) zadejte název clusteru nebo ID objektu zabezpečení clusteru pro spravovanou identitu přiřazenou systémem nebo název spravované identity přiřazený uživateli.

![udělení oprávnění Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Aby bylo možné ověřit, jestli je vaše Key Vault nakonfigurovaná tak, aby chránila váš klíč a přístup k datům Azure Monitor, je potřeba oprávnění *získat* .

## <a name="update-cluster-with-key-identifier-details"></a>Aktualizovat cluster s podrobnostmi identifikátoru klíče

Všechny operace v clusteru vyžadují `Microsoft.OperationalInsights/clusters/write` oprávnění akce. Toto oprávnění může být uděleno prostřednictvím vlastníka nebo přispěvatele, který obsahuje `*/write` akci nebo prostřednictvím role přispěvatele Log Analytics, která tuto `Microsoft.OperationalInsights/*` akci obsahuje.

Tento krok aktualizuje Azure Monitor úložiště pomocí klíče a verze, která se má použít pro šifrování dat. Po aktualizaci se nový klíč použije k zabalení a rozbalení klíče úložiště (AEK).

Vyberte aktuální verzi klíče v Azure Key Vault, abyste získali podrobnosti o identifikátoru klíče.

![Udělení oprávnění Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aktualizuje KeyVaultProperties v clusteru s podrobnostmi identifikátoru klíče.

Operace je asynchronní a její dokončení může chvíli trvat.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Response** (Odpověď)

Dokončení tohoto klíče trvá několik minut. Stav aktualizace můžete zjistit dvěma způsoby:
1. Zkopírujte hodnotu URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na cluster a podívejte se na vlastnosti *KeyVaultProperties* . Nedávno aktualizovaný klíč by měl vrátit odpověď.

Odpověď na požadavek GET by měla vypadat jako při dokončení aktualizace klíče: 202 (přijato) a záhlaví
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

---

## <a name="link-workspace-to-cluster"></a>Propojit pracovní prostor s clusterem

> [!IMPORTANT]
> Tento krok je třeba provést až po dokončení zřizování clusteru Log Analytics. Pokud propojíte pracovní prostory a ingestovat data před zřizováním, ingestovaná data se ztratí a nepůjde obnovit.

K provedení této operace, která zahrnuje a, musíte mít oprávnění Write pro váš pracovní prostor i cluster `Microsoft.OperationalInsights/workspaces/write` `Microsoft.OperationalInsights/clusters/write` .

Postupujte podle postupu popsaného v [článku věnovaném vyhrazeným clusterům](../log-query/logs-dedicated-clusters.md#link-a-workspace-to-cluster).

## <a name="key-revocation"></a>Odvolání klíče

> [!IMPORTANT]
> - Doporučeným způsobem, jak odvolat přístup k datům, je zakázat klíč nebo odstranit zásady přístupu v Key Vault.
> - Když nastavíte cluster `identity` `type` na žádné, odvoláte přístup k datům, ale tento postup se nedoporučuje, protože nemůžete vrátit zpět změny `identity` v clusteru, aniž byste museli otevřít žádost o podporu.

Úložiště clusteru bude vždy respektovat změny klíčových oprávnění během hodiny nebo dřív a úložiště nebude k dispozici. Všechna nová data, která se ingestují do pracovních prostorů propojených s vaším clusterem, se zahodila a nebudou se moct obnovit, data budou nepřístupná a dotazy na tyto pracovní prostory selžou. Dříve přijímaná data zůstanou v úložišti, dokud neodstraníte svůj cluster a vaše pracovní prostory. Nepřístupná data se řídí zásadami uchovávání dat a při dosažení doby uchování se odstraní. Ingestovaná data za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tím se odstraní operace odvolání klíče a stane se nedostupnými.

Úložiště clusteru pravidelně kontroluje Key Vault k pokusu o rozbalení šifrovacího klíče a po jeho použití se v průběhu 30 minut obnoví zpracování a dotazování dat.

## <a name="key-rotation"></a>Obměna klíčů

Střídání klíčů spravované zákazníkem vyžaduje explicitní aktualizaci clusteru s novou verzí klíče v Azure Key Vault. [Aktualizujte cluster s podrobnostmi identifikátoru klíče](#update-cluster-with-key-identifier-details). Pokud novou verzi klíče v clusteru neaktualizujete, Log Analytics úložiště clusteru bude dál používat k šifrování předchozí klíč. Pokud před aktualizací nového klíče v clusteru zakážete nebo odstraníte starý klíč, dostanete se do stavu [odvolání klíče](#key-revocation) .

Všechna vaše data zůstanou po operaci střídání klíčů přístupná, protože data vždycky zašifrovaná pomocí šifrovacího klíče účtu (AEK), zatímco AEK se teď šifruje pomocí nového klíče KEK (Key Encryption Key) v Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Klíč spravovaný zákazníkem pro uložené dotazy

Dotazovací jazyk používaný v Log Analytics je výrazná a může obsahovat citlivé informace v komentářích přidaných do dotazů nebo v syntaxi dotazu. Některé organizace vyžadují, aby tyto informace byly chráněné v souladu se zásadami klíčů spravovanými zákazníkem a vy budete potřebovat uložit dotazy zašifrované s vaším klíčem. Azure Monitor vám umožní ukládat do svého pracovního prostoru dotazy *uložené – prohledávání* a *protokolování výstrah* šifrovaných pomocí vašeho klíče ve vlastním účtu úložiště. 

> [!NOTE]
> Dotazy Log Analytics mohou být uloženy v různých úložištích v závislosti na použitém scénáři. Dotazy zůstávají šifrované pomocí klíče Microsoft Key (MMK) v následujících scénářích bez ohledu na konfiguraci klíče spravovaného zákazníkem: sešity v Azure Monitor, řídicí panely Azure, aplikace Azure Logic Apps, Azure Notebooks a sady Runbook služby Automation.

Když přenesete vlastní úložiště (BYOS) a propojíte ho s vaším pracovním prostorem, služba nahraje dotazy na *uložené výsledky hledání* a *protokolování výstrah* do vašeho účtu úložiště. To znamená, že můžete řídit účet úložiště a [zásady šifrování v REST](../../storage/common/customer-managed-keys-overview.md) buď pomocí stejného klíče, který používáte k šifrování dat v log Analyticsm clusteru, nebo v jiném klíči. Budete ale odpovědní za náklady spojené s tímto účtem úložiště. 

**Důležité upozornění před nastavením klíče spravovaného zákazníkem na dotazy**
* Musíte mít oprávnění Write k vašemu pracovnímu prostoru i účtu úložiště.
* Ujistěte se, že jste svůj účet úložiště vytvořili ve stejné oblasti, ve které se nachází Log Analytics pracovní prostor.
* *Uložení hledání* v úložišti se považuje za artefakty služby a jejich formát se může změnit.
* Existující *hledání* budou odebrána z pracovního prostoru. Zkopírujte a všechna *hledání* , která budete potřebovat před konfigurací. *Uložená hledání* můžete zobrazit pomocí [prostředí PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) .
* Historie dotazů není podporovaná a nebudete moct zobrazit dotazy, které jste spustili.
* Pro účely ukládání dotazů můžete propojit jeden účet úložiště k pracovnímu prostoru, ale je možné ho použít k dotazům na *uložené hledání* i *proti protokolování výstrah* .
* Připnutí na řídicí panel se nepodporuje.

**Konfigurace BYOS pro uložená hledání dotazů**

Propojení účtu úložiště pro *dotaz* k vašemu pracovnímu prostoru – dotazy *uložené při hledání* se ukládají do svého účtu úložiště. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Po dokončení konfigurace budou všechny nové *uložené vyhledávací* dotazy uloženy v úložišti.

**Konfigurace BYOS pro dotazy log-Alerts**

Propojení účtu úložiště s *upozorněními* k vašemu pracovnímu prostoru – dotazy *protokolu výstrahy* se ukládají do svého účtu úložiště. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Po dokončení konfigurace se všechny nové dotazy na upozornění uloží do úložiště.

## <a name="customer-lockbox-preview"></a>Customer Lockbox (Preview)

Bezpečnostní modul poskytuje kontrolu na schválení nebo odmítnutí žádosti Microsoft inženýra o přístup k vašim datům během žádosti o podporu.

V Azure Monitor máte tento ovládací prvek pro data v pracovních prostorech propojených s vaším Log Analytics vyhrazeným clusterem. Ovládací prvek bezpečnostní modul se vztahuje na data uložená ve Log Analytics vyhrazeném clusteru, kde se udržuje izolovaně v účtech úložiště clusteru v rámci předplatného chráněného bezpečnostním modulem.  

Další informace o [Customer Lockbox pro Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed operací klíčů

Customer-Managed klíč je k dispozici na vyhrazeném clusteru a tyto operace jsou označovány jako [vyhrazené článek o clusteru](../log-query/logs-dedicated-clusters.md#change-cluster-properties) .

- Načíst všechny clustery ve skupině prostředků  
- Načíst všechny clustery v předplatném
- Aktualizovat *rezervaci kapacity* v clusteru
- Aktualizace *billingType* v clusteru
- Zrušení propojení pracovního prostoru s clusterem
- Odstranění clusteru

## <a name="limitations-and-constraints"></a>Omezení a omezení

- Maximální počet clusterů na oblast a předplatné je 2.

- Maximální počet pracovních prostorů, které lze propojit s clusterem, je 1000

- Pracovní prostor můžete propojit s clusterem a pak ho odpojit. Počet operací propojení s pracovním prostorem v konkrétním pracovním prostoru je omezený na 2 v období 30 dnů.

- Šifrování klíče spravovaného zákazníkem se vztahuje na nově ingestovaná data po čase konfigurace. Data, která byla ingestovaná před konfigurací, zůstávají šifrovaná pomocí klíče Microsoft Key. Můžete zadávat dotazy na data ingestovaná před a po bezproblémové konfiguraci klíče spravovaného zákazníkem.

- Azure Key Vault musí být nakonfigurované jako obnovitelné. Tyto vlastnosti nejsou ve výchozím nastavení povolené a měly by být nakonfigurované pomocí rozhraní příkazového řádku nebo PowerShellu:<br>
  - [Obnovitelné odstranění](../../key-vault/general/soft-delete-overview.md)
  - Pro ochranu proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění by měla být zapnutá [ochrana vyprázdnění](../../key-vault/general/soft-delete-overview.md#purge-protection) .

- Přesun clusteru do jiné skupiny prostředků nebo předplatného se momentálně nepodporuje.

- Vaše Azure Key Vault, cluster a pracovní prostory musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných.

- V Číně momentálně není k dispozici bezpečnostní modul. 

- Pro clustery vytvořené z října 2020 se v podporovaných oblastech automaticky nakonfiguruje [dvojité šifrování](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) . Odesláním žádosti o získání do clusteru můžete ověřit, jestli je váš cluster nakonfigurovaný pro šifrování s dvojitým šifrováním a zda `isDoubleEncryptionEnabled` je tato hodnota `true` pro clustery s povoleným dvojitým šifrováním. 
  - Pokud vytvoříte cluster a obdržíte chybu "<název oblasti> nepodporuje dvojité šifrování pro clustery", můžete cluster i nadále vytvořit bez šifrování dvojitým šifrováním přidáním `"properties": {"isDoubleEncryptionEnabled": false}` do textu žádosti REST.
  - Nastavení dvojitého šifrování nelze změnit po vytvoření clusteru.

  - Pokud je váš cluster nastavený pomocí spravované identity přiřazené uživatelem, nastavení `UserAssignedIdentities` `None` pozastaví cluster a znemožní přístup k vašim datům, ale nemůžete vrátit zpět odvolání a aktivovat cluster bez nutnosti otevřít žádost o podporu. Toto omezení se vztahuje na spravovanou identitu přiřazenou systémem.

  - Pokud je vaše Key Vault v Private-Link (vNet), nemůžete použít klíč spravovaný zákazníkem s uživatelem přiřazenou spravovanou identitou. V tomto scénáři můžete použít spravovanou identitu přiřazenou systémem.

## <a name="troubleshooting"></a>Řešení potíží

- Chování při Key Vault dostupnosti
  - V normálním provozu – mezipaměť úložiště AEK na krátkou dobu a vrátí se zpět na Key Vault k pravidelnému rozbalení.
    
  - Přechodné chyby připojení – úložiště zpracovává přechodné chyby (vypršení časového limitu, selhání připojení, problémy se službou DNS) tím, že klíče zůstanou v mezipaměti po krátké době delší a to přináší všechny malé výkyvůy v dostupnosti. Funkce dotazování a přijímání i nadále bez přerušení.
    
  - Živý web – nedostupnost přibližně 30 minut způsobí, že účet úložiště nebude k dispozici. Funkce dotazu není k dispozici a ingestovaná data se po několik hodin ukládají do mezipaměti pomocí Microsoft Key, aby se předešlo ztrátě dat. Po obnovení přístupu k Key Vault se dotaz zpřístupní a dočasná data uložená v mezipaměti se ingestují do úložiště dat a zašifrují pomocí klíče spravovaného zákazníkem.

  - Key Vault míra přístupu – frekvence, kterou Azure Monitor přístup k úložišti Key Vault pro operace zabalení a rozbalení je mezi 6 až 60 sekundami.

- Pokud cluster aktualizujete při zřizování nebo aktualizaci stavu, aktualizace se nezdaří.

- Pokud při vytváření clusteru dojde k chybě, může to být tím, že jste cluster odstranili za posledních 14 dní a je v období obnovitelného odstranění. Název clusteru zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Název se uvolní po uplynutí doby tichého odstranění, kdy se cluster trvale odstraní.

- Odkaz na pracovní prostor na cluster se nezdaří, pokud je propojený s jiným clusterem.

- Pokud vytvoříte cluster a okamžitě zadáte KeyVaultProperties, operace může selhat, protože zásady přístupu nejde definovat, dokud není systémová identita přiřazená ke clusteru.

- Pokud aktualizujete existující cluster pomocí KeyVaultProperties a zásada přístupu pro klíč "Get" v Key Vault chybí, operace se nezdaří.

- Pokud se nedaří nasadit cluster, ověřte, že pracovní prostory Azure Key Vault, cluster a propojené Log Analytics jsou ve stejné oblasti. Může být v různých předplatných.

- Pokud aktualizujete verzi klíče v Key Vault a neaktualizujete nové podrobnosti identifikátoru klíče v clusteru, cluster Log Analytics bude dál používat předchozí klíč a vaše data budou nepřístupná. Aktualizuje nové podrobnosti identifikátoru klíče v clusteru, aby se obnovil příjem dat a možnost dotazování na data.

- Některé operace jsou dlouhé a jejich dokončení může chvíli trvat – jedná se o vytvoření clusteru, aktualizaci klíčů clusteru a odstranění clusteru. Stav operace můžete zjistit dvěma způsoby:
  1. Pokud používáte REST, zkopírujte hodnotu adresy URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
  2. Odešlete požadavek GET do clusteru nebo pracovního prostoru a sledujte odpověď. Například nepropojený pracovní prostor nebude mít *clusterResourceId* v části *funkce*.

- Chybové zprávy
  
  **Vytvoření clusteru**
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

  **Aktualizace clusteru**
  -  400 – cluster je ve stavu odstraňování. Probíhá asynchronní operace. Cluster musí před provedením jakékoli operace aktualizace dokončit jeho operaci.
  -  400 – KeyVaultProperties není prázdný, ale má špatný formát. Viz [aktualizace identifikátoru klíče](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400 – nepovedlo se ověřit klíč v Key Vault. Příčinou může být nedostatečná oprávnění nebo pokud klíč neexistuje. Ověřte, že jste [nastavili zásady klíče a přístupu](../platform/customer-managed-keys.md#grant-key-vault-permissions) v Key Vault.
  -  400-klíč nelze obnovit. Key Vault musí být nastavené na obnovitelné odstranění a ochranu vyprázdnit. Viz [dokumentace Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400 – operaci nelze nyní provést. Počkejte, až se operace Async dokončí, a zkuste to znovu.
  -  400 – cluster je ve stavu odstraňování. Počkejte, až se operace Async dokončí, a zkuste to znovu.

  **Získání clusteru**
    -  404--cluster nebyl nalezen, cluster byl pravděpodobně odstraněn. Pokud se pokusíte vytvořit cluster s tímto názvem a dojde ke konfliktu, je cluster v tichém odstranění po dobu 14 dnů. Pokud chcete vytvořit nový cluster, obraťte se na podporu, nebo použijte jiný název. 

  **Odstranění clusteru**
    -  409 – nelze odstranit cluster ve stavu zřizování. Počkejte, až se operace Async dokončí, a zkuste to znovu.

  **Odkaz na pracovní prostor**
  -  404 – pracovní prostor nebyl nalezen. Pracovní prostor, který jste zadali, neexistuje nebo byl odstraněn.
  -  409--operace propojení pracovního prostoru nebo zrušení propojení v procesu.
  -  400 – cluster nebyl nalezen, zadaný cluster neexistuje nebo byl odstraněn. Pokud se pokusíte vytvořit cluster s tímto názvem a dojde ke konfliktu, je cluster v tichém odstranění po dobu 14 dnů. Můžete se obrátit na podporu a obnovit ji.

  **Zrušit propojení pracovního prostoru**
  -  404 – pracovní prostor nebyl nalezen. Pracovní prostor, který jste zadali, neexistuje nebo byl odstraněn.
  -  409--operace propojení pracovního prostoru nebo zrušení propojení v procesu.
## <a name="next-steps"></a>Další kroky

- Další informace o [Log Analytics vyhrazeném účtování clusteru](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Další informace o [správném návrhu pracovních prostorů Log Analytics](../platform/design-logs-deployment.md)