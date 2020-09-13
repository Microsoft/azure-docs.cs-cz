---
title: Klíč spravovaný zákazníkem v Azure Monitoru
description: Informace a kroky konfigurace klíče spravovaného zákazníkem (CMK) k šifrování dat ve vašich Log Analyticsch pracovních prostorech pomocí Azure Key Vaultho klíče.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 09/09/2020
ms.openlocfilehash: 5d44758ebf94c7487935ef47a17ad810dc5cf9f8
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657298"
---
# <a name="azure-monitor-customer-managed-key"></a>Klíč spravovaný zákazníkem v Azure Monitoru 

Tento článek poskytuje základní informace a kroky ke konfiguraci klíčů spravovaných zákazníkem (CMK) pro vaše pracovní prostory Log Analytics. Po nakonfigurování se všechna data odesílaná do vašich pracovních prostorů šifrují pomocí klíče Azure Key Vault.

Před konfigurací doporučujeme zkontrolovat níže uvedená [omezení a omezení](#limitationsandconstraints) .

## <a name="customer-managed-key-cmk-overview"></a>CMK (Customer-Managed Key) – přehled

[Šifrování v klidovém umístění](../../security/fundamentals/encryption-atrest.md) je běžným požadavkem na ochranu osobních údajů a zabezpečení v organizacích. Azure vám umožní plně spravovat šifrování v klidovém režimu, zatímco máte k dispozici různé možnosti, jak pečlivě spravovat šifrovací a šifrovací klíče.

Azure Monitor zajistí, že všechna data a uložené dotazy budou v klidovém stavu zašifrované pomocí klíčů spravovaných Microsoftem (MMK). Azure Monitor taky nabízí možnost šifrování pomocí vlastního klíče, který je uložený ve vaší [Azure Key Vault](../../key-vault/general/overview.md) a k němuž má přístup úložiště pomocí [spravovaného ověřování identity](../../active-directory/managed-identities-azure-resources/overview.md) přiřazené systémem. Tento klíč (CMK) může být buď [software, nebo hardware-HSM Protected](../../key-vault/general/overview.md). Azure Monitor použití šifrování je stejné jako způsob, jakým [Azure Storage šifrování](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) funguje.

Funkce CMK se doručuje na vyhrazené clustery Log Analytics a dává vám možnost řídit přístup k vašim datům kdykoli a chránit je pomocí ovládacího prvku [bezpečnostní modul](#customer-lockbox-preview) . Abychom ověřili, že pro vyhrazený cluster ve vaší oblasti máme požadovanou kapacitu, vyžadujeme, aby vaše předplatné bylo předem povolené. Než začnete konfigurovat CMK, použijte kontakt Microsoftu, abyste si povolili předplatné.

[Cenový model Log Analytics clusterů](./manage-cost-storage.md#log-analytics-dedicated-clusters) používá rezervace kapacity počínaje úrovní 1000 GB/den.

Data ingestovaná za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tato data zůstávají šifrovaná pomocí klíčů Microsoftu bez ohledu na konfiguraci CMK, ale vaše kontrola nad daty SSD dodržuje [odvolávání klíčů](#cmk-kek-revocation). Pracujeme na tom, aby data SSD zašifrovaná pomocí CMK byla v druhé polovině 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak CMK funguje v Azure Monitor

Azure Monitor využívá spravovanou identitu přiřazenou systémem k udělení přístupu k vašemu Azure Key Vault. Spravovaná identita přiřazená systémem se dá přidružit jenom k jednomu prostředku Azure, zatímco identita Log Analyticsho clusteru je na úrovni clusteru podporovaná. to znamená, že funkce CMK se doručí ve vyhrazeném Log Analytics clusteru. Aby bylo možné podporovat CMK ve více pracovních prostorech, vytvoří nový prostředek *clusteru* Log Analytics jako zprostředkující připojení identity mezi Key Vault a vašimi pracovními prostory Log Analytics. Log Analytics úložiště clusteru používá spravovanou identitu, která je \' přidružená k prostředku *clusteru* k ověření ve vaší Azure Key Vault prostřednictvím Azure Active Directory. 

Po konfiguraci CMK se všechna data ingestovaná do pracovních prostorů přidružených k vašemu prostředku *clusteru* šifrují pomocí vašeho klíče v Key Vault. Kdykoli můžete zrušit přidružení pracovních prostorů ke zdroji *clusteru* . Nová data se ingestují do Log Analytics úložiště a šifrují pomocí klíče Microsoft Key, zatímco můžete bez problémů zadávat dotazy na nová a stará data.


![CMK – přehled](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics prostředek *clusteru* , který má spravovanou identitu s oprávněními Key Vault – identita se rozšíří na Log Analytics vyhrazené úložiště clusteru s Underlay
3. Vyhrazený cluster Log Analytics
4. Pracovní prostory přidružené k prostředku *clusteru* pro šifrování CMK

## <a name="encryption-keys-operation"></a>Operace šifrovacích klíčů

Šifrování dat úložiště má tři typy klíčů:

- Šifrovací klíč **KEK** -Key (CMK)
- **AEK** šifrovací klíč účtu
- **Klíč DEK** – šifrovací klíč dat

Platí následující pravidla:

- Účty úložiště Log Analytics clusteru generují jedinečný šifrovací klíč pro každý účet úložiště, který se označuje jako AEK.

- AEK se používá k odvození DEKs, což jsou klíče, které slouží k zašifrování každého bloku dat zapsaných na disk.

- Když nakonfigurujete klíč v Key Vault a odkazujete na něj v prostředku *clusteru* , Azure Storage posílá žádosti do vašeho Azure Key Vault k zabalení a rozbalení AEK k provádění operací šifrování a dešifrování dat.

- Váš KEK nikdy nezůstane Key Vault a v případě klíče HSM nikdy neopustí hardware.

- Azure Storage používá spravovanou identitu, která je přidružená k prostředku   *clusteru* pro ověřování a přístup k Azure Key Vault prostřednictvím Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Postup zřizování CMK

1. Povolení předplatného – funkce CMK se doručuje na vyhrazené Log Analytics clustery. Abychom ověřili, že ve vaší oblasti máme požadovanou kapacitu, vyžadujeme, aby vaše předplatné bylo předem povolené. Použijte kontakt od Microsoftu k získání povoleného předplatného.
2. Vytváření Azure Key Vault a ukládání klíče
3. Vytvoření prostředku *clusteru*
4. Udělování oprávnění vašemu Key Vault
5. Přidružení Log Analyticsch pracovních prostorů

Procedura není podporovaná v Azure Portal a zřizování se provádí přes PowerShell nebo žádosti REST.

> [!IMPORTANT]
> Každá žádost REST musí v hlavičce požadavku zahrnovat autorizační token držitele.

Například:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer eyJ0eXAiO....
```

Kde *eyJ0eXAiO....* představuje úplný autorizační token. 

Token můžete získat pomocí jedné z těchto metod:

1. Použijte metodu [Registrace aplikací](/graph/auth/auth-concepts#access-tokens) .
2. Na webu Azure Portal
    1. Přejít na Azure Portal v nástroji pro vývojáře (F12)
    1. V části "Batch? rozhraní API-Version" vyhledejte autorizační řetězec v části "hlavičky žádosti". Vypadá to, že: "Authorization: nosiče eyJ0eXAiO....". 
    1. Zkopírujte a přidejte ho do volání rozhraní API podle níže uvedených příkladů.
3. Přejděte na stránku dokumentace k Azure REST. V jakémkoli rozhraní API stiskněte tlačítko vyzkoušet a zkopírujte token nosiče.

### <a name="asynchronous-operations-and-status-check"></a>Asynchronní operace a kontroly stavu

Některé operace v této konfiguraci se spouští asynchronně, protože je nejde rychle dokončit. Když v konfiguraci použijete žádosti REST, odpověď zpočátku po přijetí vrátí stavový kód HTTP 200 (OK) a záhlaví s vlastností *Azure-AsyncOperation* :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Pak můžete zjistit stav asynchronní operace odesláním žádosti o získání do hodnoty v hlavičce *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

Odpověď obsahuje informace o operaci a její *stavu*. Může to být jedna z následujících:

Probíhá operace.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

Probíhá operace aktualizace identifikátoru klíče.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Probíhá odstraňování prostředku *clusteru* – při odstraňování prostředku *clusteru* , který obsahuje pracovní prostory přidružené k pracovním prostorům, je operace zrušení přidružení provedena u každého pracovního prostoru v asynchronních operacích, které mohou chvíli trvat.
Tato funkce není relevantní při odstranění *clusteru* bez přidruženého pracovního prostoru – v tomto případě se prostředek *clusteru* okamžitě odstraní.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Operace je dokončená.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Operace se nezdařila
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>Povolení předplatného pro nasazení CMK

Funkce CMK se doručuje na vyhrazené Log Analytics clustery.Abychom ověřili, že ve vaší oblasti máme požadovanou kapacitu, vyžadujeme, aby vaše předplatné bylo předem povolené. Pomocí kontaktů do Microsoftu poskytněte ID předplatných.

> [!IMPORTANT]
> Možnost CMK je regionální. Vaše Azure Key Vault, prostředek *clusteru* a přidružené pracovní prostory Log Analytics musí být ve stejné oblasti, ale můžou být v různých předplatných.

### <a name="storing-encryption-key-kek"></a>Ukládání šifrovacího klíče (KEK)

Vytvořte nebo použijte Azure Key Vault, který již máte vygenerovat, nebo importujte klíč, který se má použít k šifrování dat. Azure Key Vault musí být nakonfigurovaná tak, aby chránila váš klíč a přístup k vašim datům v Azure Monitor. Tuto konfiguraci můžete ověřit v části vlastnosti Key Vault, měla by být povolená ochrana proti *odstranění* a *vyprázdnění* .

![Obnovitelné odstranění a mazání nastavení ochrany](media/customer-managed-keys/soft-purge-protection.png)

Tato nastavení se dají aktualizovat přes rozhraní příkazového řádku a PowerShellu:

- [Obnovitelné odstranění](../../key-vault/general/soft-delete-overview.md)
- [Vyprázdnit](../../key-vault/general/soft-delete-overview.md#purge-protection) ochranné Guard proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění

### <a name="create-cluster-resource"></a>Vytvořit prostředek *clusteru*

Tento prostředek se používá jako zprostředkující připojení identity mezi Key Vault a vašimi pracovními prostory v Log Analytics. Po obdržení potvrzení, že vaše předplatná byla povolena, vytvořte prostředek *clusteru* Log Analytics v oblasti, ve které jsou umístěny vaše pracovní prostory.

Při vytváření prostředku *clusteru* je nutné zadat úroveň *rezervace kapacity* (SKU). Úroveň *rezervace kapacity* může být v rozsahu 1000 až 3000 GB za den a můžete ji aktualizovat v krocích 100. Pokud potřebujete úroveň rezervace kapacity vyšší než 3000 GB za den, kontaktujte nás na adrese LAIngestionRate@microsoft.com . [Další informace](./manage-cost-storage.md#log-analytics-dedicated-clusters)

Vlastnost *billingType* Určuje přidělení fakturace pro prostředek *clusteru* a jeho data:
- *Cluster* (výchozí) – náklady na rezervaci kapacity pro váš cluster se připočítají ke zdroji *clusteru* .
- *Pracovní prostory* – náklady na rezervaci kapacity pro váš cluster jsou úměrně přičteny k pracovním prostorům v clusteru s prostředkem *clusteru* , který se fakturuje jako část využití, pokud je celkový počet zpracovaných dat v daném dni v rámci rezervace kapacity. Další informace o cenovém modelu clusteru najdete v tématu [Log Analytics vyhrazené clustery](manage-cost-storage.md#log-analytics-dedicated-clusters) . 

> [!NOTE]
> * Po vytvoření prostředku *clusteru* ho můžete aktualizovat pomocí *SKU*, *KEYVAULTPROPERTIES* nebo *billingType* s použitím opravy REST Request.
> * V současné době můžete aktualizovat *billingType* pomocí žádosti REST, to není v PowerShellu podporované.

Tato operace je asynchronní a její dokončení může chvíli trvat.

> [!IMPORTANT]
> Zkopírujte a uložte odpověď, protože budete potřebovat podrobnosti v části Další kroky.
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity daily-ingestion-gigabyte 
```

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

Identita je přiřazena ke zdroji *clusteru* v okamžiku vytvoření.

**Response** (Odpověď)

200 OK a záhlaví.

I když trvá zřízení Log Analytics clusteru a i když se dokončí, můžete stav zřizování ověřit dvěma způsoby:

1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na hodnotu *provisioningState* . Je *ProvisioningAccount* při zřizování a *úspěšném* dokončení.

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

Identifikátor GUID "principalId" vygeneruje Služba Managed identity pro prostředek *clusteru* .

### <a name="grant-key-vault-permissions"></a>Udělení oprávnění Key Vault

Aktualizujte svou Key Vault novými zásadami přístupu, které udělí oprávnění k vašemu prostředku *clusteru* . Tato oprávnění používá Underlay Azure Monitor Storage pro šifrování dat. Otevřete Key Vault v Azure Portal a klikněte na "zásady přístupu", pak "+ Přidat zásadu přístupu" a vytvořte zásadu s těmito nastaveními:

- Klíčová oprávnění: vyberte Get, Wrap Key a Unwrap Key oprávnění.
- Vyberte objekt zabezpečení: zadejte název prostředku *clusteru* nebo hodnotu Principal-ID, která se vrátila v odpovědi v předchozím kroku.

![udělení oprávnění Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Aby bylo možné ověřit, jestli je vaše Key Vault nakonfigurovaná tak, aby chránila váš klíč a přístup k datům Azure Monitor, je potřeba oprávnění *získat* .

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizace prostředku clusteru s podrobnostmi identifikátoru klíče

Tento krok se provádí během úvodní aktualizace a v budoucích aktualizacích klíčových verzí v Key Vault. Informuje Azure Monitor úložiště o verzi klíče, která se má použít k šifrování dat. Po aktualizaci se nový klíč použije k zabalení a rozbalení klíče úložiště (AEK).

Pokud chcete aktualizovat prostředek *clusteru* s podrobnostmi *identifikátoru klíče* Key Vault, vyberte aktuální verzi klíče v Azure Key Vault, abyste získali podrobnosti identifikátoru klíče.

![Udělení oprávnění Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aktualizujte prostředek *clusteru* KeyVaultProperties s podrobnostmi identifikátoru klíče.

Tato operace je asynchronní při aktualizaci podrobností identifikátoru klíče a její dokončení může chvíli trvat. Při aktualizaci hodnoty kapacity je synchronní.

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> Pomocí opravy můžete aktualizovat *SKU*prostředků *clusteru* , *keyVaultProperties* nebo *billingType* .

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
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

**Response** (Odpověď)

200 OK a záhlaví.
Dokončením tohoto identifikátoru klíče bude dokončeno několik minut. Stav aktualizace můžete zjistit dvěma způsoby:
1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na vlastnosti *KeyVaultProperties* . Nedávno aktualizované podrobnosti identifikátoru klíče by se měly vrátit v odpovědi.

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

### <a name="workspace-association-to-cluster-resource"></a>Přidružení pracovního prostoru ke zdroji *clusteru*

K provedení této operace musíte mít oprávnění Write pro váš pracovní prostor i prostředek *clusteru* , což zahrnuje tyto akce:

- V pracovním prostoru: Microsoft. OperationalInsights/pracovní prostory/Write
- V prostředku *clusteru* : Microsoft. OperationalInsights/Clusters/Write

> [!IMPORTANT]
> Tento krok je třeba provést až po dokončení zřizování clusteru Log Analytics. Pokud přidružíte pracovní prostory a ingestování dat před zřizováním, ingestovaná data se ztratí a nepůjde obnovit.

Tato operace je asynchronní a její dokončení může chvíli trvat.

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

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

**Response** (Odpověď)

200 OK a záhlaví.

Ingestovaná data se po operaci přidružení zašifrují pomocí spravovaného klíče, což může trvat až 90 minut. Stav přidružení pracovního prostoru můžete zjistit dvěma způsoby:

1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odeslání [pracovních prostorů – Získejte](/rest/api/loganalytics/workspaces/get) požadavek a sledujte odpověď. přidružený pracovní prostor bude mít clusterResourceId v části funkce.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

**Response** (Odpověď)

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

## <a name="cmk-kek-revocation"></a>Odvolání CMK (KEK)

Přístup k datům můžete odvolat zakázáním klíče nebo odstraněním zásad přístupu k prostředkům *clusteru* v Key Vault. Služba Log Analyticsového úložiště clusteru bude vždy respektovat změny klíčových oprávnění během hodiny nebo dřív a úložiště nebude k dispozici. Všechna nová data, která jsou v pracovních prostorech přidružených k vašemu prostředku *clusteru*přidružená   , se ztratí a nebudou se obnovovat, data jsou nedostupná a dotazy na tyto pracovní prostory selžou. Dříve přijímaná data zůstanou v úložišti, dokud neodstraníte prostředek *clusteru* a vaše pracovní prostory. Nepřístupná data se řídí zásadami uchovávání dat a při dosažení doby uchování se odstraní. 

Ingestovaná data za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tato operace se odstraní při operaci odvolání klíče a stane se taky nedostupným.

Úložiště se pravidelně dotazuje Key Vault k pokusu o rozbalení šifrovacího klíče a po jeho použití, příjmu dat a obnovení dotazů do 30 minut.

## <a name="cmk-kek-rotation"></a>Rotace CMK (KEK)

Rotace CMK vyžaduje explicitní aktualizaci prostředku *clusteru* s novou verzí klíče v Azure Key Vault. Postupujte podle pokynů v kroku aktualizace prostředku *clusteru* s podrobnostmi identifikátoru klíče. Pokud nové podrobnosti identifikátoru klíče v prostředku *clusteru* neaktualizujete, Log Analytics úložiště clusteru bude dál používat k šifrování předchozí klíč. Pokud před aktualizací nového klíče v prostředku *clusteru* zakážete nebo odstraníte starý klíč, dostanete se do stavu [odvolání klíče](#cmk-kek-revocation) .

Všechna vaše data zůstanou po operaci střídání klíčů přístupná, protože data vždycky zašifrovaná pomocí šifrovacího klíče účtu (AEK), zatímco AEK se teď šifruje pomocí nového klíče KEK (Key Encryption Key) v Key Vault.

## <a name="cmk-for-queries"></a>CMK pro dotazy

Dotazovací jazyk používaný v Log Analytics je výrazná a může obsahovat citlivé informace v komentářích přidaných do dotazů nebo v syntaxi dotazu. Některé organizace vyžadují, aby tyto informace byly chráněny jako součást zásad CMK a vy budete potřebovat ukládat dotazy zašifrované s vaším klíčem. Azure Monitor vám umožní ukládat do svého pracovního prostoru dotazy *uložené – prohledávání* a *protokolování výstrah* šifrovaných pomocí vašeho klíče ve vlastním účtu úložiště. 

> [!NOTE]
> Dotazy Log Analytics mohou být uloženy v různých úložištích v závislosti na použitém scénáři. Dotazy zůstávají šifrované pomocí klíče Microsoft Key (MMK) v následujících scénářích bez ohledu na konfiguraci CMK: sešity v Azure Monitor, řídicí panely Azure, aplikace Azure Logic Apps, Azure Notebooks a sady Runbook Automation.

Když zadáte vlastní úložiště (BYOS) a přidružíte ho k pracovnímu prostoru, služba nahraje dotazy na *uložené výsledky hledání* a *protokolování výstrah* do vašeho účtu úložiště. To znamená, že můžete řídit účet úložiště a [zásady šifrování v REST](../../storage/common/encryption-customer-managed-keys.md) buď pomocí stejného klíče, který používáte k šifrování dat v log Analyticsm clusteru, nebo v jiném klíči. Budete ale odpovědní za náklady spojené s tímto účtem úložiště. 

**Otázky před nastavením CMK pro dotazy**
* Musíte mít oprávnění Write k vašemu pracovnímu prostoru i účtu úložiště.
* Ujistěte se, že jste svůj účet úložiště vytvořili ve stejné oblasti, ve které se nachází Log Analytics pracovní prostor.
* *Uložení hledání* v úložišti se považuje za artefakty služby a jejich formát se může změnit.
* Existující *hledání* budou odebrána z pracovního prostoru. Zkopírujte a všechna *hledání* , která budete potřebovat před konfigurací. *Uložená hledání* můžete zobrazit pomocí [prostředí PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) .
* Historie dotazů není podporovaná a nebudete moct zobrazit dotazy, které jste spustili.
* K pracovnímu prostoru můžete přidružit jeden účet úložiště pro účely ukládání dotazů, ale dá se použít k dotazům na *uložená hledání* i *protokolování výstrah* .
* Připnutí na řídicí panel se nepodporuje.

**Konfigurace BYOS pro uložená hledání dotazů**

Přidružit účet úložiště pro *dotaz* k vašemu pracovnímu prostoru – dotazy *uložené při hledání* se ukládají do svého účtu úložiště. 

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

Přidružte účtu úložiště *výstrahy* k vašemu pracovnímu prostoru – dotazy *protokolu výstrahy* se ukládají do svého účtu úložiště. 

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

V Azure Monitor máte tento ovládací prvek pro data v pracovních prostorech přidružených k vašemu Log Analytics vyhrazenému clusteru. Ovládací prvek bezpečnostní modul se vztahuje na data uložená ve Log Analytics vyhrazeném clusteru, kde se udržuje izolovaně v účtech úložiště clusteru v rámci předplatného chráněného bezpečnostním modulem.  

Další informace o [Customer Lockbox pro Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

## <a name="cmk-management"></a>Správa CMK

- **Získá všechny prostředky *clusteru* pro skupinu prostředků.**
  
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

- **Získá všechny prostředky *clusteru* pro předplatné.**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response** (Odpověď)
    
  Stejná odpověď jako u ' prostředků*clusteru* pro skupinu prostředků ', ale v oboru předplatného.

- **Aktualizovat *rezervaci kapacity* v prostředku *clusteru***

  Když se datový svazek, ke kterému se přiřadí vaše přidružené pracovní prostory, mění v čase a chcete patřičně aktualizovat úroveň rezervace kapacity. Postupujte podle kroků [aktualizace prostředku *clusteru* ](#update-cluster-resource-with-key-identifier-details) a zadejte novou hodnotu kapacity. Může být v rozsahu 1000 až 3000 GB za den a v krocích po 100. Pro zajištění vyšší úrovně než 3000 GB za den kontaktujte kontakt Microsoftu, abyste ho povolili. Všimněte si, že nemusíte zadávat úplný text žádosti REST, ale měla by obsahovat SKU:

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Aktualizovat *billingType* v prostředku *clusteru***

  Vlastnost *billingType* Určuje přidělení fakturace pro prostředek *clusteru* a jeho data:
  - *cluster* (výchozí) – fakturace je přidělená předplatnému hostujícímu váš prostředek clusteru.
  - *pracovní prostory* – fakturace je úměrná předplatným hostujícím vaše pracovní prostory.
  
  Postupujte podle [aktualizovaného prostředku *clusteru* ](#update-cluster-resource-with-key-identifier-details) a zadejte novou hodnotu billingType. Všimněte si, že nemusíte zadávat úplný text žádosti REST a měla by obsahovat *billingType*:

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

- **Zrušit přidružení pracovního prostoru**

  K provedení této operace potřebujete oprávnění zapisovat k pracovnímu prostoru a prostředku *clusteru* . V každém okamžiku můžete zrušit přidružení pracovního prostoru od prostředku *clusteru* . Nová ingestovaná data po operaci de-Association je uložená v Log Analyticsovém úložišti a zašifrovaná pomocí klíče Microsoft Key. Můžete zadat dotaz na data, která byla ingestovaná do vašeho pracovního prostoru před a po přidružení deaktivace bez problémů, pokud je prostředek *clusteru* zřízený a nakonfigurovaný pomocí platného Key Vault klíče.

  Tato operace je asynchronní a její dokončení může chvíli trvat.

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response** (Odpověď)

  200 OK a záhlaví.

  Ingestovaná data po uložení operace zrušení přidružení do Log Analyticsho úložiště může trvat 90 minut. Stav zrušení přidružení pracovního prostoru můžete zjistit dvěma způsoby:

  1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
  2. Odeslání [pracovních prostorů – Získejte](/rest/api/loganalytics/workspaces/get) požadavek a sledujte odpověď. nepřidružený pracovní prostor nebude mít *clusterResourceId* v části *funkce*.

- **Zkontroluje stav přidružení pracovního prostoru.**
  
  V pracovním prostoru proveďte operaci get a sledujte, zda je v odpovědi v části *funkce*přítomna vlastnost *clusterResourceId* . Přidružený pracovní prostor bude mít vlastnost *clusterResourceId* .

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Odstranění prostředku *clusteru***

  K provedení této operace potřebujete oprávnění Write pro prostředek *clusteru* . Operace obnovitelného odstranění je provedena, aby bylo možné obnovit *clusterový* prostředek včetně jeho dat do 14 dnů, ať už došlo k nechtěnému nebo úmyslnému odstranění. Název prostředku *clusteru* zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Po uplynutí doby tichého odstranění se název prostředku *clusteru* uvolní, prostředky a data *clusteru* se trvale odstraní a jsou neobnovitelná. Všechny přidružené pracovní prostory se odpojí od prostředku *clusteru* při operaci odstranění. Nová ingestovaná data se ukládají do Log Analyticsho úložiště a šifrují pomocí klíče Microsoft Key. 
  
  Operace, která je odasociována z pracovních prostorů, je asynchronní a dokončení může trvat až 90 minut.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response** (Odpověď)

  200 OK

- **Obnovení prostředku *clusteru* a vašich dat** 
  
  Prostředek *clusteru* , který se odstranil za posledních 14 dní, je ve stavu obnovitelného odstranění a dá se obnovit s jeho daty. Vzhledem k tomu, že všechny pracovní prostory se odřadí z prostředku *clusteru* s odstraněním prostředku *clusteru* , je potřeba po obnovení CMK šifrování znovu přidružit své pracovní prostory. V současné době je operace obnovení prováděna ručně skupinou produktů. Pro požadavky na obnovení použijte Microsoft Channel.

## <a name="limitationsandconstraints"></a>Omezení a omezení

- CMK se podporuje na vyhrazeném clusteru Log Analytics a je vhodný pro zákazníky, kteří odesílají 1 TB za den.

- Maximální počet prostředků *clusteru* na oblast a předplatné je 2

- Pracovní prostor můžete přidružit k prostředku *clusteru* a pak ho zrušit, pokud pracovní prostor CMK není potřeba. Počet přidružení pracovního prostoru na konkrétní pracovní prostor v období 30 dnů je omezený na 2.

- Přidružení pracovního prostoru k prostředku *clusteru* by se mělo přenášet až po ověření, že se zřízení clusteru Log Analytics dokončilo. Data odesílaná do vašeho pracovního prostoru před dokončením budou vyřazena a nebude možné je obnovit.

- Šifrování CMK se vztahuje na nově ingestovaná data po konfiguraci CMK. Data, která byla ingestovaná před konfigurací CMK, zůstávají šifrovaná pomocí klíče Microsoft Key. Můžete zadávat dotazy na data ingestovaná před a po bezproblémové konfiguraci CMK.

- Azure Key Vault musí být nakonfigurované jako obnovitelné. Tyto vlastnosti nejsou ve výchozím nastavení povolené a měly by být nakonfigurované pomocí rozhraní příkazového řádku nebo PowerShellu:<br>
  - [Obnovitelné odstranění](../../key-vault/general/soft-delete-overview.md)
  - Pro ochranu proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění by měla být zapnutá [ochrana vyprázdnění](../../key-vault/general/soft-delete-overview.md#purge-protection) .

- Prostředek *clusteru* přesunout do jiné skupiny prostředků nebo předplatného se momentálně nepodporuje.

- Vaše Azure Key Vault, prostředek *clusteru* a přidružené pracovní prostory musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných.

- Přidružení pracovního prostoru ke zdroji *clusteru* selže, pokud je přidruženo k jinému prostředku *clusteru* .

## <a name="troubleshooting"></a>Řešení potíží

- Chování při Key Vault dostupnosti
  - V normálním provozu – mezipaměť úložiště AEK na krátkou dobu a vrátí se zpět na Key Vault k pravidelnému rozbalení.
    
  - Přechodné chyby připojení – úložiště zpracovává přechodné chyby (vypršení časového limitu, selhání připojení, problémy se službou DNS) tím, že klíče zůstanou v mezipaměti po krátké době delší a to přináší všechny malé výkyvůy v dostupnosti. Funkce dotazování a přijímání i nadále bez přerušení.
    
  - Živý web – nedostupnost přibližně 30 minut způsobí, že účet úložiště nebude k dispozici. Funkce dotazu není k dispozici a ingestovaná data se po několik hodin ukládají do mezipaměti pomocí Microsoft Key, aby se předešlo ztrátě dat. Po obnovení přístupu k Key Vault se dotaz zpřístupní a dočasná data uložená v mezipaměti se ingestují do úložiště dat a zašifrují pomocí CMK.

  - Key Vault míra přístupu – frekvence, kterou Azure Monitor přístup k úložišti Key Vault pro operace zabalení a rozbalení je mezi 6 až 60 sekundami.

- Pokud vytvoříte prostředek *clusteru* a okamžitě zadáte KeyVaultProperties, operace může selhat, protože zásady přístupu nejde definovat, dokud není systémová identita přiřazená k prostředku *clusteru* .

- Pokud aktualizujete existující prostředek *clusteru* s KeyVaultProperties a zásada přístupu pro klíč "Get" v Key Vault chybí, operace se nezdaří.

- Pokud při vytváření prostředku *clusteru* dojde k chybě, může to být tím, že jste v posledních 14 dnech odstranili prostředek *clusteru* a je v období obnovitelného odstranění. Název prostředku *clusteru* zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Název se uvolní po uplynutí doby dočasného odstranění, kdy se prostředek *clusteru* trvale odstraní.

- Pokud provedete aktualizaci prostředku *clusteru* , zatímco probíhá operace, operace se nezdaří.

- Pokud se vám nepodaří nasadit prostředek *clusteru* , ověřte, že Azure Key Vault, prostředek *clusteru*   a přidružené Log Analytics pracovní prostory jsou ve stejné oblasti. Může být v různých předplatných.

- Pokud aktualizujete verzi klíče v Key Vault a neaktualizujete nové podrobnosti identifikátoru klíče v prostředku *clusteru* , cluster Log Analytics bude dál používat předchozí klíč a vaše data nebudou dostupná. Aktualizujte nové podrobnosti identifikátoru klíče v prostředku *clusteru* , aby se obnovil příjem dat a možnost dotazování na data.

- Některé operace jsou dlouhé a jejich dokončení může chvíli trvat – jedná se o vytvoření *clusteru* , aktualizaci klíčů *clusteru* a odstranění *clusteru* . Stav operace můžete zjistit dvěma způsoby:
  1. Při použití REST zkopírujte z odpovědi hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
  2. Odešlete požadavek GET do *clusteru* nebo pracovního prostoru a sledujte odpověď. Například nepřidružený pracovní prostor nebude mít *clusterResourceId* v části *funkce*.

- Pro podporu a nápovědu týkající se spravovaného klíče zákazníka použijte své kontakty do Microsoftu.

- Chybové zprávy
  
  Vytvoření prostředku *clusteru* :
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

  Aktualizace prostředku *clusteru*
  -  400 – cluster je ve stavu odstraňování. Probíhá asynchronní operace. Cluster musí před provedením jakékoli operace aktualizace dokončit jeho operaci.
  -  400 – KeyVaultProperties není prázdný, ale má špatný formát. Viz [aktualizace identifikátoru klíče](#update-cluster-resource-with-key-identifier-details).
  -  400 – nepovedlo se ověřit klíč v Key Vault. Příčinou může být nedostatečná oprávnění nebo pokud klíč neexistuje. Ověřte, že jste [nastavili zásady klíče a přístupu](#grant-key-vault-permissions) v Key Vault.
  -  400-klíč nelze obnovit. Key Vault musí být nastavené na obnovitelné odstranění a ochranu vyprázdnit. Viz [dokumentace Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400 – operaci nelze nyní provést. Počkejte, až se operace Async dokončí, a zkuste to znovu.
  -  400 – cluster je ve stavu odstraňování. Počkejte, až se operace Async dokončí, a zkuste to znovu.

    Prostředek *clusteru* Get:
    -  404--cluster nebyl nalezen, cluster byl pravděpodobně odstraněn. Pokud se pokusíte vytvořit cluster s tímto názvem a dojde ke konfliktu, je cluster v tichém odstranění po dobu 14 dnů. Pokud chcete vytvořit nový cluster, obraťte se na podporu, nebo použijte jiný název. 

  Odstranění prostředku *clusteru*
    -  409 – nelze odstranit cluster ve stavu zřizování. Počkejte, až se operace Async dokončí, a zkuste to znovu.

  Přidružení pracovního prostoru:
  -  404 – pracovní prostor nebyl nalezen. Pracovní prostor, který jste zadali, neexistuje nebo byl odstraněn.
  -  409 – operace přidružení nebo zrušení přidružení pracovního prostoru v procesu.
  -  400 – cluster nebyl nalezen, zadaný cluster neexistuje nebo byl odstraněn. Pokud se pokusíte vytvořit cluster s tímto názvem a dojde ke konfliktu, je cluster v tichém odstranění po dobu 14 dnů. Můžete se obrátit na podporu a obnovit ji.

  Zrušení přidružení pracovního prostoru:
  -  404 – pracovní prostor nebyl nalezen. Pracovní prostor, který jste zadali, neexistuje nebo byl odstraněn.
  -  409 – operace přidružení nebo zrušení přidružení pracovního prostoru v procesu.
