---
title: Klíč spravovaný zákazníkem v Azure Monitoru
description: Informace a kroky pro konfiguraci Customer-Managed Key k šifrování dat v pracovních prostorech Log Analytics pomocí Azure Key Vaultho klíče.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/18/2020
ms.openlocfilehash: 7bfd951d7cec27e0b8264aaabf9bc3a17875256a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000721"
---
# <a name="azure-monitor-customer-managed-key"></a>Klíč spravovaný zákazníkem v Azure Monitoru 

Tento článek poskytuje základní informace a kroky ke konfiguraci klíčů spravovaných zákazníkem pro vaše Log Analytics pracovní prostory. Po nakonfigurování se všechna data odesílaná do vašich pracovních prostorů šifrují pomocí klíče Azure Key Vault.

Před konfigurací doporučujeme zkontrolovat níže uvedená [omezení a omezení](#limitationsandconstraints) .

## <a name="customer-managed-key-overview"></a>Přehled klíčů spravovaných zákazníkem

[Šifrování v klidovém umístění](../../security/fundamentals/encryption-atrest.md) je běžným požadavkem na ochranu osobních údajů a zabezpečení v organizacích. Azure vám umožní plně spravovat šifrování v klidovém režimu, zatímco máte k dispozici různé možnosti, jak pečlivě spravovat šifrovací a šifrovací klíče.

Azure Monitor zajistí, že všechna data a uložené dotazy budou v klidovém stavu zašifrované pomocí klíčů spravovaných Microsoftem (MMK). Azure Monitor taky nabízí možnost šifrování pomocí vlastního klíče, který je uložený v [Azure Key Vault](../../key-vault/general/overview.md) a poskytuje vám kontrolu nad tím, že přístup k datům odvolá. Azure Monitor použití šifrování je stejné jako způsob, jakým [Azure Storage šifrování](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) funguje.

Customer-Managed klíč se doručuje na vyhrazené Log Analytics clustery, které poskytují vyšší úroveň ochrany a řízení. Data ingestovaná do vyhrazených clusterů se dvakrát šifrují – jednou na úrovni služby pomocí klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem a jednou na úrovni infrastruktury pomocí dvou různých šifrovacích algoritmů a dvou různých klíčů. [Dvojité šifrování](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) chrání proti scénáři, kdy může dojít k ohrožení jednoho z šifrovacích algoritmů nebo klíčů. V takovém případě bude další vrstva šifrování nadále chránit vaše data. Vyhrazený cluster také umožňuje chránit data pomocí ovládacího prvku [bezpečnostní modul](#customer-lockbox-preview) .

Data ingestovaná za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tato data zůstávají zašifrovaná pomocí klíčů Microsoftu bez ohledu na konfiguraci klíče spravované zákazníkem, ale vaše kontrola nad daty SSD dodržuje [odvolání klíčů](#key-revocation). Pracujeme na tom, aby data SSD zašifrovaná pomocí Customer-Managed klíč v první polovině 2021.

[Cenový model Log Analytics clusterů](./manage-cost-storage.md#log-analytics-dedicated-clusters) používá rezervace kapacity počínaje úrovní 1000 GB/den.

> [!IMPORTANT]
> Kvůli dočasnám omezením kapacity vyžadujeme před vytvořením clusteru předem jejich registraci. Použijte své kontakty do Microsoftu nebo otevřete žádost o podporu pro registraci ID předplatných.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Jak Customer-Managed klíč funguje v Azure Monitor

Azure Monitor využívá spravovanou identitu přiřazenou systémem k udělení přístupu k vašemu Azure Key Vault. Spravovaná identita přiřazená systémem se dá přidružit jenom k jednomu prostředku Azure, zatímco identita Log Analyticsho clusteru je na úrovni clusteru podporovaná. to znamená, že se tato funkce doručí na vyhrazený Log Analytics cluster. Aby bylo možné podporovat Customer-Managed klíč ve více pracovních prostorech, provede nový prostředek Log Analytics *clusteru* jako připojení zprostředkující identity mezi Key Vault a vašimi pracovními prostory Log Analytics. Log Analytics úložiště clusteru používá spravovanou identitu, která je \' přidružená k prostředku *clusteru* k ověření ve vaší Azure Key Vault prostřednictvím Azure Active Directory. 

Po dokončení konfigurace se všechna data, která se ingestují do pracovních prostorů propojených s vaším vyhrazeným clusterem, zašifrují pomocí klíče v Key Vault. Pracovní prostory z clusteru můžete kdykoli zrušit. Nová data se pak ingestují Log Analytics úložiště a šifrují pomocí klíče Microsoft Key, zatímco můžete bez problémů zadávat dotazy na nová a stará data.


![Přehled Customer-Managed Key](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics prostředek *clusteru* , který má spravovanou identitu s oprávněními Key Vault – identita se rozšíří na Log Analytics vyhrazené úložiště clusteru s Underlay
3. Vyhrazený cluster Log Analytics
4. Pracovní prostory připojené ke zdroji *clusteru* 

## <a name="encryption-keys-operation"></a>Operace šifrovacích klíčů

Šifrování dat úložiště má tři typy klíčů:

- Šifrovací klíč **KEK** (váš Customer-Managed klíč)
- **AEK** šifrovací klíč účtu
- **Klíč DEK** – šifrovací klíč dat

Platí následující pravidla:

- Účty úložiště Log Analytics clusteru generují jedinečný šifrovací klíč pro každý účet úložiště, který se označuje jako AEK.
- AEK se používá k odvození DEKs, což jsou klíče, které slouží k zašifrování každého bloku dat zapsaných na disk.
- Když nakonfigurujete klíč v Key Vault a odkazujete na něj v clusteru, Azure Storage posílá žádosti na vaše Azure Key Vault k zabalení a rozbalení AEK k provádění operací šifrování a dešifrování dat.
- Váš KEK nikdy nezůstane Key Vault a v případě klíče HSM nikdy neopustí hardware.
- Azure Storage používá spravovanou identitu, která je přidružená k prostředku *clusteru* pro ověřování a přístup k Azure Key Vault prostřednictvím Azure Active Directory.

## <a name="customer-managed-key-provisioning-procedure"></a>Customer-Managed postup zřizování klíčů

1. Zaregistrovat předplatné, aby bylo možné vytvořit cluster
1. Vytváření Azure Key Vault a ukládání klíče
1. Vytváření clusteru
1. Udělování oprávnění vašemu Key Vault
1. Propojení Log Analyticsch pracovních prostorů

Konfigurace Customer-Managed klíčů není podporovaná v Azure Portal a zřizování se provádí prostřednictvím [PowerShellu](https://docs.microsoft.com/powershell/module/az.operationalinsights/), [CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics) nebo požadavků [REST](https://docs.microsoft.com/rest/api/loganalytics/) .

### <a name="asynchronous-operations-and-status-check"></a>Asynchronní operace a kontroly stavu

Některé kroky konfigurace běží asynchronně, protože je nepůjde rychle dokončit. Při použití REST odpověď zpočátku po přijetí vrátí stavový kód HTTP 200 (OK) a záhlaví s vlastností *Azure-AsyncOperation* :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Stav asynchronní operace můžete zjistit odesláním žádosti o získání do hodnoty v hlavičce *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

`status`V odpovědi může být jedna z následujících: ' InProgress ', ' aktualizace ', ' odstranění ', ' úspěch nebo ' neúspěch ' včetně kódu chyby.

### <a name="allowing-subscription"></a>Povoluje se předplatné

> [!IMPORTANT]
> Customer-Managed klíčovou funkcí je oblast. Pracovní prostory Azure Key Vault, cluster a propojené Log Analytics musí být ve stejné oblasti, ale mohou být v různých předplatných.

### <a name="storing-encryption-key-kek"></a>Ukládání šifrovacího klíče (KEK)

Vytvořte nebo použijte Azure Key Vault, který již máte vygenerovat, nebo importujte klíč, který se má použít k šifrování dat. Azure Key Vault musí být nakonfigurovaná tak, aby chránila váš klíč a přístup k vašim datům v Azure Monitor. Tuto konfiguraci můžete ověřit v části vlastnosti Key Vault, měla by být povolená ochrana proti *odstranění* a *vyprázdnění* .

![Obnovitelné odstranění a mazání nastavení ochrany](media/customer-managed-keys/soft-purge-protection.png)

Tato nastavení je možné aktualizovat v Key Vault prostřednictvím rozhraní příkazového řádku a PowerShellu:

- [Obnovitelné odstranění](../../key-vault/general/soft-delete-overview.md)
- [Vyprázdnit](../../key-vault/general/soft-delete-overview.md#purge-protection) ochranné Guard proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění

### <a name="create-cluster"></a>Vytvoření clusteru

Postupujte podle postupu popsaného v [článku věnovaném vyhrazeným clusterům](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster). 

> [!IMPORTANT]
> Zkopírujte a uložte odpověď, protože budete potřebovat podrobnosti v části Další kroky.

### <a name="grant-key-vault-permissions"></a>Udělení oprávnění Key Vault

Vytvoření zásad přístupu v Key Vault pro udělení oprávnění vašemu clusteru. Tato oprávnění používá Underlay Azure Monitor Storage pro šifrování dat. Otevřete Key Vault v Azure Portal a klikněte na "zásady přístupu", pak "+ Přidat zásadu přístupu" a vytvořte zásadu s těmito nastaveními:

- Klíčová oprávnění: vyberte Get, Wrap Key a Unwrap Key oprávnění.
- Vyberte objekt zabezpečení: zadejte název clusteru nebo hodnotu Principal-ID, která se vrátila v odpovědi v předchozím kroku.

![udělení oprávnění Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Aby bylo možné ověřit, jestli je vaše Key Vault nakonfigurovaná tak, aby chránila váš klíč a přístup k datům Azure Monitor, je potřeba oprávnění *získat* .

### <a name="update-cluster-with-key-identifier-details"></a>Aktualizovat cluster s podrobnostmi identifikátoru klíče

Všechny operace v clusteru vyžadují oprávnění Microsoft. OperationalInsights/Clusters/Write Action. Toto oprávnění může být uděleno prostřednictvím vlastníka nebo přispěvatele, který obsahuje *akci/Write nebo prostřednictvím role přispěvatel Log Analytics, která obsahuje akci Microsoft. OperationalInsights/* Action.

Tento krok aktualizuje Azure Monitor úložiště pomocí klíče a verze, která se má použít pro šifrování dat. Po aktualizaci se nový klíč použije k zabalení a rozbalení klíče úložiště (AEK).

Vyberte aktuální verzi klíče v Azure Key Vault, abyste získali podrobnosti o identifikátoru klíče.

![Udělení oprávnění Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aktualizuje KeyVaultProperties v clusteru s podrobnostmi identifikátoru klíče.

Operace je asynchronní a její dokončení může chvíli trvat.

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name"?api-version=2020-08-01
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

Dokončením tohoto identifikátoru klíče bude dokončeno několik minut. Stav aktualizace můžete zjistit dvěma způsoby:
1. Zkopírujte hodnotu URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na cluster a podívejte se na vlastnosti *KeyVaultProperties* . Nedávno aktualizované podrobnosti identifikátoru klíče by se měly vrátit v odpovědi.

Odpověď na požadavek GET by měla vypadat takto jako při dokončení aktualizace identifikátoru klíče: 200 OK a záhlaví
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

### <a name="link-workspace-to-cluster"></a>Propojit pracovní prostor s clusterem

K provedení této operace musíte mít oprávnění Write pro váš pracovní prostor i cluster, což zahrnuje tyto akce:

- V pracovním prostoru: Microsoft. OperationalInsights/pracovní prostory/Write
- V clusteru: Microsoft. OperationalInsights/Clusters/Write

> [!IMPORTANT]
> Tento krok je třeba provést až po dokončení zřizování clusteru Log Analytics. Pokud propojíte pracovní prostory a ingestovat data před zřizováním, ingestovaná data se ztratí a nepůjde obnovit.

Tato operace je asynchronní a její dokončení může chvíli trvat.

Postupujte podle postupu popsaného v [článku věnovaném vyhrazeným clusterům](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#link-a-workspace-to-the-cluster).

## <a name="key-revocation"></a>Odvolání klíče

Přístup k datům můžete odvolat tím, že klíč zakážete nebo odstraníte zásady přístupu clusteru v Key Vault. Služba Log Analyticsového úložiště clusteru bude vždy respektovat změny klíčových oprávnění během hodiny nebo dřív a úložiště nebude k dispozici. Všechna nová data, která se ingestují do pracovních prostorů propojených s vaším clusterem, se zahozena a nebudou se obnovovat, data jsou nedostupná a dotazy do těchto pracovních prostorů selžou. Dříve přijímaná data zůstanou v úložišti, dokud neodstraníte svůj cluster a vaše pracovní prostory. Nepřístupná data se řídí zásadami uchovávání dat a při dosažení doby uchování se odstraní. 

Ingestovaná data za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tato operace se odstraní při operaci odvolání klíče a stane se taky nedostupným.

Úložiště se pravidelně dotazuje Key Vault k pokusu o rozbalení šifrovacího klíče a po jeho použití, příjmu dat a obnovení dotazů do 30 minut.

## <a name="key-rotation"></a>Obměna klíčů

Customer-Managed rotace klíčů vyžaduje explicitní aktualizaci clusteru s novou verzí klíče v Azure Key Vault. Postupujte podle pokynů v kroku aktualizace clusteru s podrobnostmi identifikátoru klíče. Pokud nové podrobnosti identifikátoru klíče v clusteru neaktualizujete, Log Analytics úložiště clusteru bude dál používat k šifrování předchozí klíč. Pokud před aktualizací nového klíče v clusteru zakážete nebo odstraníte starý klíč, dostanete se do stavu [odvolání klíče](#key-revocation) .

Všechna vaše data zůstanou po operaci střídání klíčů přístupná, protože data vždycky zašifrovaná pomocí šifrovacího klíče účtu (AEK), zatímco AEK se teď šifruje pomocí nového klíče KEK (Key Encryption Key) v Key Vault.

## <a name="customer-managed-key-for-queries"></a>Customer-Managed klíč pro dotazy

Dotazovací jazyk používaný v Log Analytics je výrazná a může obsahovat citlivé informace v komentářích přidaných do dotazů nebo v syntaxi dotazu. Některé organizace vyžadují, aby se tyto informace udržovaly v souladu s Customer-Managed zásadami klíčů a vy budete potřebovat ukládat dotazy zašifrované s vaším klíčem. Azure Monitor vám umožní ukládat do svého pracovního prostoru dotazy *uložené – prohledávání* a *protokolování výstrah* šifrovaných pomocí vašeho klíče ve vlastním účtu úložiště. 

> [!NOTE]
> Dotazy Log Analytics mohou být uloženy v různých úložištích v závislosti na použitém scénáři. Dotazy zůstávají šifrované pomocí klíče Microsoft Key (MMK) v následujících scénářích, a to bez ohledu na konfiguraci Customer-Managed klíčů: sešity v Azure Monitor, řídicí panely Azure, aplikace Azure Logic Apps, Azure Notebooks a Runbooky služby Automation.

Když přenesete vlastní úložiště (BYOS) a propojíte ho s vaším pracovním prostorem, služba nahraje dotazy na *uložené výsledky hledání* a *protokolování výstrah* do vašeho účtu úložiště. To znamená, že můžete řídit účet úložiště a [zásady šifrování v REST](../../storage/common/customer-managed-keys-overview.md) buď pomocí stejného klíče, který používáte k šifrování dat v log Analyticsm clusteru, nebo v jiném klíči. Budete ale odpovědní za náklady spojené s tímto účtem úložiště. 

**Důležité důvody před nastavením Customer-Managed klíč pro dotazy**
* Musíte mít oprávnění Write k vašemu pracovnímu prostoru i účtu úložiště.
* Ujistěte se, že jste svůj účet úložiště vytvořili ve stejné oblasti, ve které se nachází Log Analytics pracovní prostor.
* *Uložení hledání* v úložišti se považuje za artefakty služby a jejich formát se může změnit.
* Existující *hledání* budou odebrána z pracovního prostoru. Zkopírujte a všechna *hledání* , která budete potřebovat před konfigurací. *Uložená hledání* můžete zobrazit pomocí [prostředí PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) .
* Historie dotazů není podporovaná a nebudete moct zobrazit dotazy, které jste spustili.
* Pro účely ukládání dotazů můžete propojit jeden účet úložiště k pracovnímu prostoru, ale je možné ho použít k dotazům na *uložené hledání* i *proti protokolování výstrah* .
* Připnutí na řídicí panel se nepodporuje.

**Konfigurace BYOS pro uložená hledání dotazů**

Propojení účtu úložiště pro *dotaz* k vašemu pracovnímu prostoru – dotazy *uložené při hledání* se ukládají do svého účtu úložiště. 

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

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

Po dokončení konfigurace budou všechny nové *uložené vyhledávací* dotazy uloženy v úložišti.

**Konfigurace BYOS pro dotazy log-Alerts**

Propojení účtu úložiště s *upozorněními* k vašemu pracovnímu prostoru – dotazy *protokolu výstrahy* se ukládají do svého účtu úložiště. 

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

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

Po dokončení konfigurace se všechny nové dotazy na upozornění uloží do úložiště.

## <a name="customer-lockbox-preview"></a>Customer Lockbox (Preview)
Bezpečnostní modul poskytuje kontrolu na schválení nebo odmítnutí žádosti Microsoft inženýra o přístup k vašim datům během žádosti o podporu.

V Azure Monitor máte tento ovládací prvek pro data v pracovních prostorech propojených s vaším Log Analytics vyhrazeným clusterem. Ovládací prvek bezpečnostní modul se vztahuje na data uložená ve Log Analytics vyhrazeném clusteru, kde se udržuje izolovaně v účtech úložiště clusteru v rámci předplatného chráněného bezpečnostním modulem.  

Další informace o [Customer Lockbox pro Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed operací klíčů

- **Získání všech clusterů ve skupině prostředků**
  
  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response** (Odpověď)
  
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

- **Získání všech clusterů v předplatném**

  ```azurecli
  az monitor log-analytics cluster list
  ```

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response** (Odpověď)
    
  Stejná odpověď jako u ' cluster ve skupině prostředků ', ale v oboru předplatného.

- **Aktualizovat *rezervaci kapacity* v clusteru**

  Když se datový svazek do propojených pracovních prostorů změní v čase a chcete patřičně aktualizovat úroveň rezervace kapacity. Postupujte podle [aktualizovaného clusteru](#update-cluster-with-key-identifier-details) a zadejte novou hodnotu kapacity. Může být v rozsahu 1000 až 3000 GB za den a v krocích po 100. Pro zajištění vyšší úrovně než 3000 GB za den kontaktujte kontakt Microsoftu, abyste ho povolili. Všimněte si, že nemusíte zadávat úplný text žádosti REST, ale měla by obsahovat SKU:

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

- **Aktualizace *billingType* v clusteru**

  Vlastnost *billingType* Určuje přidělení fakturace pro cluster a jeho data:
  - *cluster* (výchozí) – fakturace je přidělená předplatnému hostujícímu váš prostředek clusteru.
  - *pracovní prostory* – fakturace je úměrná předplatným hostujícím vaše pracovní prostory.
  
  Postupujte podle [aktualizovaného clusteru](#update-cluster-with-key-identifier-details) a zadejte novou hodnotu billingType. Všimněte si, že nemusíte zadávat úplný text žádosti REST a měla by obsahovat *billingType*:

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

- **Zrušení propojení s pracovním prostorem**

  K provedení této operace potřebujete oprávnění Write pro tento pracovní prostor a cluster. Pracovní prostor z clusteru můžete kdykoli zrušit. Nová ingestovaná data po operaci zrušení propojení se uloží do Log Analytics úložiště a šifrují pomocí klíče Microsoft Key. Můžete zadat dotaz na data, která byla ingestovaná do vašeho pracovního prostoru před a po bezproblémovém propojení, pokud je cluster zřízený a nakonfigurovaný pomocí platného Key Vaultho klíče.

  Tato operace je asynchronní a její dokončení může chvíli trvat.

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  - **Ověřit stav odkazu na pracovní prostor**
  
  V pracovním prostoru proveďte operaci get a sledujte, zda je v odpovědi v části *funkce* přítomna vlastnost *clusterResourceId* . Propojený pracovní prostor bude mít vlastnost *clusterResourceId* .

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Odstranění clusteru**

  K provedení této operace potřebujete oprávnění k zápisu do clusteru. Operace obnovitelného odstranění je provedena za účelem umožnění obnovení clusteru včetně jeho dat do 14 dnů, bez ohledu na to, zda došlo k náhodnému nebo úmyslnému odstranění. Název clusteru zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Po období obnovitelného odstranění se název clusteru uvolní a váš cluster a jeho data se trvale odstraní a jsou neobnovitelná. Libovolný propojený pracovní prostor se z clusteru odpojí při operaci odstranění. Nová ingestovaná data se ukládají do Log Analyticsho úložiště a šifrují pomocí klíče Microsoft Key. 
  
  Operace zrušení propojení je asynchronní a dokončení může trvat až 90 minut.

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```
 
  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
  
- **Obnovení clusteru a vašich dat** 
  
  Cluster, který se odstranil za posledních 14 dní, je ve stavu obnovitelného odstranění a dá se obnovit s jeho daty. Vzhledem k tomu, že všechny pracovní prostory byly odpojování od odstranění clusteru, je potřeba po obnovení clusteru znovu propojit své pracovní prostory. Operace obnovení je aktuálně prováděna ručně skupinou produktů. K obnovení odstraněného clusteru použijte svůj kanál Microsoft nebo otevřete žádost o podporu.

## <a name="limitations-and-constraints"></a>Omezení a omezení

- Customer-Managed klíč se podporuje ve vyhrazeném Log Analyticsm clusteru a je vhodný pro zákazníky, kteří odesílají 1 TB za den.

- Maximální počet clusterů na oblast a předplatné je 2.

- Maximální počet propojených pracovních prostorů ke clusteru je 1000.

- Pracovní prostor můžete propojit s clusterem a pak ho odpojit. Počet operací propojení s pracovním prostorem v konkrétním pracovním prostoru je omezený na 2 v období 30 dnů.

- Odkaz na pracovní prostor na cluster by se měl přenést až po ověření, že se zřízení clusteru Log Analytics dokončilo. Data odesílaná do vašeho pracovního prostoru před dokončením budou vyřazena a nebude možné je obnovit.

- Šifrování Customer-Managed Key se vztahuje na nově ingestovaná data po čase konfigurace. Data, která byla ingestovaná před konfigurací, zůstávají šifrovaná pomocí klíče Microsoft Key. Můžete zadávat dotazy na data ingestovaná před a po bezproblémové konfiguraci Customer-Managedho klíče.

- Azure Key Vault musí být nakonfigurované jako obnovitelné. Tyto vlastnosti nejsou ve výchozím nastavení povolené a měly by být nakonfigurované pomocí rozhraní příkazového řádku nebo PowerShellu:<br>
  - [Obnovitelné odstranění](../../key-vault/general/soft-delete-overview.md)
  - Pro ochranu proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění by měla být zapnutá [ochrana vyprázdnění](../../key-vault/general/soft-delete-overview.md#purge-protection) .

- Přesun clusteru do jiné skupiny prostředků nebo předplatného se momentálně nepodporuje.

- Vaše Azure Key Vault, cluster a propojené pracovní prostory musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných.

- Odkaz na pracovní prostor na cluster se nezdaří, pokud je propojený s jiným clusterem.

## <a name="troubleshooting"></a>Řešení potíží

- Chování při Key Vault dostupnosti
  - V normálním provozu – mezipaměť úložiště AEK na krátkou dobu a vrátí se zpět na Key Vault k pravidelnému rozbalení.
    
  - Přechodné chyby připojení – úložiště zpracovává přechodné chyby (vypršení časového limitu, selhání připojení, problémy se službou DNS) tím, že klíče zůstanou v mezipaměti po krátké době delší a to přináší všechny malé výkyvůy v dostupnosti. Funkce dotazování a přijímání i nadále bez přerušení.
    
  - Živý web – nedostupnost přibližně 30 minut způsobí, že účet úložiště nebude k dispozici. Funkce dotazu není k dispozici a ingestovaná data se po několik hodin ukládají do mezipaměti pomocí Microsoft Key, aby se předešlo ztrátě dat. Po obnovení přístupu k Key Vault se dotaz zpřístupní a dočasná data uložená v mezipaměti se ingestují do úložiště dat a zašifrují pomocí klíče Customer-Managed.

  - Key Vault míra přístupu – frekvence, kterou Azure Monitor přístup k úložišti Key Vault pro operace zabalení a rozbalení je mezi 6 až 60 sekundami.

- Pokud vytvoříte cluster a okamžitě zadáte KeyVaultProperties, operace může selhat, protože zásady přístupu nejde definovat, dokud není systémová identita přiřazená ke clusteru.

- Pokud aktualizujete existující cluster pomocí KeyVaultProperties a zásada přístupu pro klíč "Get" v Key Vault chybí, operace se nezdaří.

- Pokud při vytváření clusteru dojde k chybě, může to být tím, že jste cluster odstranili za posledních 14 dní a je v období obnovitelného odstranění. Název clusteru zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Název se uvolní po uplynutí doby tichého odstranění, kdy se cluster trvale odstraní.

- Pokud cluster aktualizujete, zatímco probíhá operace, operace se nezdaří.

- Pokud se nedaří nasadit cluster, ověřte, že pracovní prostory Azure Key Vault, cluster a propojené Log Analytics jsou ve stejné oblasti. Může být v různých předplatných.

- Pokud aktualizujete verzi klíče v Key Vault a neaktualizujete nové podrobnosti identifikátoru klíče v clusteru, cluster Log Analytics bude dál používat předchozí klíč a vaše data budou nepřístupná. Aktualizuje nové podrobnosti identifikátoru klíče v clusteru, aby se obnovil příjem dat a možnost dotazování na data.

- Některé operace jsou dlouhé a jejich dokončení může chvíli trvat – jedná se o vytvoření clusteru, aktualizaci klíčů clusteru a odstranění clusteru. Stav operace můžete zjistit dvěma způsoby:
  1. Pokud používáte REST, zkopírujte hodnotu adresy URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
  2. Odešlete požadavek GET do clusteru nebo pracovního prostoru a sledujte odpověď. Například nepropojený pracovní prostor nebude mít *clusterResourceId* v části *funkce*.

- Pro clustery vytvořené z října 2020 se pro clustery vytvořené v říjnu nakonfigurovalo [dvojité šifrování](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) , pokud se v oblasti použilo dvojité šifrování Pokud vytvoříte cluster a obdržíte chybu "<název oblasti> nepodporuje dvojité šifrování pro clustery.", cluster stále můžete vytvořit, ale je zakázané dvojité šifrování. Po vytvoření clusteru se nedá zapnout nebo vypnout. Pokud chcete vytvořit cluster, pokud se v oblasti nepodporují šifrování, přidejte `"properties": {"isDoubleEncryptionEnabled": false}` do textu žádosti REST.

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
  -  400 – KeyVaultProperties není prázdný, ale má špatný formát. Viz [aktualizace identifikátoru klíče](#update-cluster-with-key-identifier-details).
  -  400 – nepovedlo se ověřit klíč v Key Vault. Příčinou může být nedostatečná oprávnění nebo pokud klíč neexistuje. Ověřte, že jste [nastavili zásady klíče a přístupu](#grant-key-vault-permissions) v Key Vault.
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
