---
title: Azure Monitor klíč spravovaný zákazníkem
description: Informace a kroky konfigurace klíče spravovaného zákazníkem (CMK) k šifrování dat ve vašich Log Analyticsch pracovních prostorech pomocí Azure Key Vaultho klíče.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/07/2020
ms.openlocfilehash: 2838051d8e75ffbe3b7ecc9fbc655f24b57199e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198687"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor klíč spravovaný zákazníkem 

Tento článek poskytuje základní informace a kroky ke konfiguraci klíčů spravovaných zákazníkem (CMK) pro vaše pracovní prostory Log Analytics. Po nakonfigurování se všechna data odesílaná do vašich pracovních prostorů šifrují pomocí klíče Azure Key Vault.

Před konfigurací doporučujeme zkontrolovat níže uvedená [omezení a omezení](#limitations-and-constraints) .

## <a name="disclaimers"></a>Právní omezení

- Funkce CMK se doručuje na vyhrazený cluster Log Analytics, což je fyzický cluster a úložiště dat, které je vhodné pro zákazníky, kteří odesílají 1 TB za den.

- Cenové modely CMK nejsou momentálně dostupné a nejsou uvedené v tomto článku. V druhém čtvrtletí kalendářního roku (CY) 2020 se očekává cenový model pro vyhrazený Log Analytics cluster, který se bude vztahovat na všechna existující nasazení CMK.

## <a name="customer-managed-key-cmk-overview"></a>CMK (Customer-Managed Key) – přehled

[Šifrování v klidovém umístění](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) je běžným požadavkem na ochranu osobních údajů a zabezpečení v organizacích. Azure vám umožní plně spravovat šifrování v klidovém režimu, zatímco máte k dispozici různé možnosti, jak pečlivě spravovat šifrovací a šifrovací klíče.

Služba Azure Monitor Storage zajišťuje, aby všechna zašifrovaná data v klidovém stavu používala klíče spravované službou Azure při uložení v Azure Storage. Azure Monitor taky nabízí možnost šifrování dat pomocí vlastního klíče uloženého v [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), ke kterému se přistupovalo pomocí [spravovaného ověřování identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) přiřazené systémem. Tento klíč může být buď [software, nebo hardware – chráněný](https://docs.microsoft.com/azure/key-vault/key-vault-overview)modulem HSM.
Azure Monitor použití šifrování je stejné jako způsob, jakým [Azure Storage šifrování](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) funguje.

Frekvence, kterou Azure Monitor přístup úložiště Key Vault pro zabalení a rozbalení operací je mezi 6 až 60 sekund.Azure Monitor Storage vždy respektuje změny v klíčových oprávněních během hodiny.

Ingestovaná data za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tato data zůstávají zašifrovaná pomocí klíčů Microsoftu bez ohledu na konfiguraci CMK, ale vaše kontrola nad daty SSD dodržuje [odvolání klíčů](#cmk-kek-revocation) a je nepřístupná. Pracujeme na tom, aby data SSD zašifrovaná pomocí CMK byla v druhé polovině 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak CMK funguje v Azure Monitor

Azure Monitor využívá spravovanou identitu přiřazenou systémem k udělení přístupu k vašemu Azure Key Vault.Spravovaná identita přiřazená systémem se dá přidružit jenom k jednomu prostředku Azure. Identita vyhrazeného Log Analyticsho clusteru je podporovaná na úrovni clusteru a tím se určuje, že funkce CMK se doručí ve vyhrazeném Log Analytics clusteru. Aby bylo možné podporovat CMK ve více pracovních prostorech, vytvoří nový prostředek *clusteru* Log Analytics jako zprostředkující připojení identity mezi Key Vault a vašimi pracovními prostory Log Analytics. Tento koncept udržuje identitu mezi vyhrazeným clusterem Log Analytics a prostředkem Log Analytics *clusteru* , zatímco data přidružených pracovních prostorů jsou chráněná klíčem Key Vault. Vyhrazené Log Analytics úložiště clusteru používá spravovanou identitu, která je \' přidružená k prostředku *clusteru* pro ověřování a přístup k Azure Key Vault prostřednictvím Azure Active Directory.

![CMK – přehled](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Key Vault zákazníka.
2.    Prostředek *clusteru* Log Analytics zákazníka, který má spravovanou identitu s oprávněními Key Vault – identita je podporovaná na vyhrazené úrovni clusteru Log Analytics.
3.    Vyhrazený cluster Log Analytics.
4.    Pracovní prostory zákazníka přidružené k prostředku *clusteru* pro šifrování CMK.

## <a name="encryption-keys-operation"></a>Operace šifrovacích klíčů

Šifrování dat úložiště má tři typy klíčů:

- Šifrovací klíč **KEK** -Key (CMK)
- **AEK** šifrovací klíč účtu
- **Klíč DEK** – šifrovací klíč dat

Platí následující pravidla:

- Vyhrazené Log Analytics účty úložiště clusteru generují jedinečný šifrovací klíč pro každý účet úložiště, který se označuje jako AEK.

- AEK se používá k odvození DEKs, což jsou klíče, které slouží k zašifrování každého bloku dat zapsaných na disk.

- Když nakonfigurujete klíč v Key Vault a odkazujete na něj v prostředku *clusteru* , Azure Storage posílá žádosti do vašeho Azure Key Vault k zabalení a rozbalení AEK k provádění operací šifrování a dešifrování dat.

- Váš KEK nikdy nezůstane Key Vault a v případě klíče HSM nikdy neopustí hardware.

- Azure Storage používá spravovanou identitu, která je přidružená k prostředku *clusteru* pro ověřování a přístup k Azure Key Vault prostřednictvím Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Postup zřizování CMK

1. Seznam povolených odběrů – Chcete-li zajistit, že v oblasti vyhrazeného Log Analyticsho clusteru máte požadovanou kapacitu, musíme předem ověřit a povolit vaše předplatné.
2. Vytváření Azure Key Vault a ukládání klíče
3. Vytvoření prostředku *clusteru*
5. Udělování oprávnění vašemu Key Vault
6. Přidružení Log Analyticsch pracovních prostorů

Procedura není momentálně v uživatelském rozhraní podporovaná a proces zřizování se provádí prostřednictvím REST API.

> [!IMPORTANT]
> Jakýkoli požadavek rozhraní API musí v hlavičce požadavku zahrnovat autorizační token nosiče.

Příklad:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Kde *eyJ0eXAiO....* představuje úplný autorizační token. 

Token můžete získat pomocí jedné z těchto metod:

1. Použijte metodu [Registrace aplikací](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) .
2. Na webu Azure Portal
    1. Přejít na Azure Portal v nástroji pro vývojáře (F12)
    1. V části "Batch? rozhraní API-Version" vyhledejte autorizační řetězec v části "hlavičky žádosti". Vypadá to, že: "Authorization: nosiče eyJ0eXAiO....". 
    1. Zkopírujte a přidejte ho do volání rozhraní API podle níže uvedených příkladů.
3. Přejděte na stránku dokumentace k Azure REST. V jakémkoli rozhraní API stiskněte tlačítko vyzkoušet a zkopírujte token nosiče.

### <a name="asynchronous-operations-and-status-check"></a>Asynchronní operace a kontroly stavu

Některé operace v této konfiguraci se spouští asynchronně, protože je nejde rychle dokončit. Odpověď na asynchronní operaci zpočátku po přijetí vrátí stavový kód HTTP 200 (OK) a záhlaví s vlastností *Azure-AsyncOperation* :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Stav asynchronní operace můžete zjistit odesláním žádosti o získání do hodnoty v hlavičce *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
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

### <a name="subscription-whitelisting"></a>Seznam povolených odběrů

Možnost CMK je funkce předčasného přístupu. K předplatným, ke kterým plánujete vytváření prostředků *clusteru* , musí mít předem povolený produkt skupina produktů Azure. Pomocí kontaktů do Microsoftu poskytněte ID předplatných.

> [!IMPORTANT]
> Možnost CMK je regionální. Vaše Azure Key Vault, prostředek *clusteru* a přidružené pracovní prostory Log Analytics musí být ve stejné oblasti, ale můžou být v různých předplatných.

### <a name="storing-encryption-key-kek"></a>Ukládání šifrovacího klíče (KEK)

Vytvořte nebo použijte Azure Key Vault, který již máte vygenerovat, nebo importujte klíč, který se má použít k šifrování dat. Azure Key Vault musí být nakonfigurovaná tak, aby chránila váš klíč a přístup k vašim datům v Azure Monitor. Tuto konfiguraci můžete ověřit v části vlastnosti Key Vault, měla by být povolená ochrana proti *odstranění* a *vyprázdnění* .

![Obnovitelné odstranění a mazání nastavení ochrany](media/customer-managed-keys/soft-purge-protection.png)

Tato nastavení jsou k dispozici prostřednictvím rozhraní příkazového řádku a prostředí PowerShell:
- [Obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Vyprázdnit](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) ochranné Guard proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění

### <a name="create-cluster-resource"></a>Vytvořit prostředek *clusteru*

Tento prostředek se používá jako zprostředkující připojení identity mezi Key Vault a vašimi pracovními prostory v Log Analytics. Jakmile obdržíte potvrzení, že vaše předplatná byla na seznamu povolených, vytvořte prostředek *clusteru* Log Analytics v oblasti, ve které jsou umístěny vaše pracovní prostory.

Při vytváření prostředku *clusteru* je nutné zadat úroveň *rezervace kapacity* (SKU). Úroveň *rezervace kapacity* může být v rozsahu 1 000 až 2 000 GB za den a můžete ji aktualizovat v krocích 100 později. Pokud potřebujete úroveň rezervace kapacity vyšší než 2 000 GB za den, kontaktujte nás na adrese LAIngestionRate@microsoft.com . [Další informace](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)
    
Vlastnost *billingType* Určuje přidělení fakturace pro prostředek *clusteru* a jeho data:
- *cluster* (výchozí) – fakturace je přidělená předplatnému hostujícímu váš prostředek *clusteru* .
- *pracovní prostory* – fakturace je úměrná předplatným hostujícím vaše pracovní prostory. 

> [!NOTE]
> Po vytvoření prostředku *clusteru* ho můžete aktualizovat pomocí *SKU*, *KEYVAULTPROPERTIES* nebo *billingType* s použitím opravy REST Request.

**Vytvořit**

Tato Správce prostředků požadavek je asynchronní operace.

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
Identita je přiřazena ke zdroji *clusteru* v okamžiku vytvoření.

**Základě**

200 OK a záhlaví.

I když trvá zřizování vyhrazeného Log Analyticsho clusteru a i když se dokončí, můžete stav zřizování ověřit dvěma způsoby:

1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na hodnotu *provisioningState* . Je *ProvisioningAccount* při zřizování a *úspěšném* dokončení.


```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Zkopírujte a uložte odpověď, protože budete potřebovat podrobnosti v části Další kroky.

**Základě**

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
    "clusterType": "LogAnalytics",
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

Aktualizujte svůj Key Vault novou zásadou přístupu, která uděluje oprávnění prostředku *clusteru* . Tato oprávnění používá Underlay Azure Monitor Storage pro šifrování dat. Otevřete Key Vault v Azure Portal a klikněte na "zásady přístupu", pak "+ Přidat zásadu přístupu" a vytvořte zásadu s těmito nastaveními:

- Klíčová oprávnění: vyberte Get, Wrap Key a Unwrap Key oprávnění.
- Vyberte objekt zabezpečení: zadejte hodnotu ID objektu zabezpečení, která se vrátila v odpovědi v předchozím kroku.

![udělení oprávnění Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Aby bylo možné ověřit, jestli je vaše Key Vault nakonfigurovaná tak, aby chránila váš klíč a přístup k datům Azure Monitor, je potřeba oprávnění *získat* .

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizace prostředku clusteru s podrobnostmi identifikátoru klíče

Tento krok se provádí během úvodní aktualizace a v budoucích aktualizacích klíčových verzí v Key Vault. Informuje Azure Monitor úložiště o verzi klíče, která se má použít k šifrování dat. Po aktualizaci se nový klíč použije k zabalení a rozbalení klíče úložiště (AEK).

Pokud chcete aktualizovat prostředek *clusteru* s podrobnostmi *identifikátoru klíče* Key Vault, vyberte aktuální verzi klíče v Azure Key Vault, abyste získali podrobnosti identifikátoru klíče.

![Udělení oprávnění Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aktualizujte prostředek *clusteru* KeyVaultProperties s podrobnostmi identifikátoru klíče.

**Aktualizace**

Tato Správce prostředků požadavek je asynchronní operace při aktualizaci podrobností identifikátoru klíče, zatímco při aktualizaci hodnoty kapacity je synchronní.

> [!Note]
> V prostředku *clusteru* můžete poskytnout částečný text pro aktualizaci *SKU*, *keyVaultProperties* nebo *billingType*.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       }
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" obsahuje podrobnosti o identifikátoru Key Vaultho klíče.

**Základě**

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
      keyVaultUri: "https://key-vault-name.vault.azure.net",
      kyName: "key-name",
      keyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
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
> Tento krok by se měl provádět až po dokončení vyhrazeného zřízení clusteru Log Analytics. Pokud přidružíte pracovní prostory a ingestování dat před zřizováním, ingestovaná data se ztratí a nepůjde obnovit.

**Přidružení pracovního prostoru**

Tato Správce prostředků požadavek je asynchronní operace.

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

**Základě**

200 OK a záhlaví.

Ingestovaná data se po operaci přidružení zašifrují pomocí spravovaného klíče, což může trvat až 90 minut. Stav přidružení pracovního prostoru můžete zjistit dvěma způsoby:

1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odeslání [pracovních prostorů – Získejte](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) požadavek a sledujte odpověď. přidružený pracovní prostor bude mít clusterResourceId v části funkce.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
```

**Základě**

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
    "retentionInDays": days,
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

Přístup k datům můžete odvolat zakázáním klíče nebo odstraněním zásad přístupu k prostředkům *clusteru* v Key Vault. Vyhrazené úložiště Log Analytics clusteru bude vždy respektovat změny v klíčových oprávněních během hodiny nebo dřív a úložiště nebude k dispozici. Všechna data přijímaná do pracovních prostorů přidružených k vašemu prostředku *clusteru* se zahozena a dotazy selžou. Dříve přijímaná data zůstanou v úložišti nepřístupná, jako když váš prostředek *clusteru* a vaše pracovní prostory nejsou smazány. Nepřístupná data se řídí zásadami uchovávání dat a při dosažení doby uchování se odstraní. 

Ingestovaná data za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tato operace se odstraní při operaci odvolání klíče a stane se taky nedostupným.

Úložiště se pravidelně dotazuje Key Vault k pokusu o rozbalení šifrovacího klíče a po jeho použití, příjmu dat a obnovení dotazů do 30 minut.

## <a name="cmk-kek-rotation"></a>Rotace CMK (KEK)

Rotace CMK vyžaduje explicitní aktualizaci prostředku *clusteru* s novou verzí klíče v Azure Key Vault. Postupujte podle pokynů v kroku aktualizace prostředku *clusteru* s podrobnostmi identifikátoru klíče. Pokud nové podrobnosti identifikátoru klíče v prostředku *clusteru* neaktualizujete, vyhrazené úložiště Log Analytics clusteru bude dál používat předchozí klíč.

Všechna vaše data zůstanou po operaci střídání klíčů dostupná, včetně dat, která se ingestují před otočením a po ní, protože se AEK teď šifruje pomocí šifrovacího klíče účtu (AEK), zatímco se teď šifruje pomocí nového klíče KEK) v Key Vault

## <a name="limitations-and-constraints"></a>Omezení a omezení

- CMK se podporuje na vyhrazeném clusteru Log Analytics vhodném pro zákazníky, kteří odesílají 1 TB za den.

- Maximální počet prostředků *clusteru* na oblast a předplatné je 2

- Pracovní prostor můžete přidružit k prostředku *clusteru* a pak ho zrušit, pokud CMK pro jeho data už nepotřebujete nebo jiný důvod. Počet přidružení pracovního prostoru, které můžete v pracovním prostoru provést v období 30 dnů, je omezený na 2.

- Přidružení pracovního prostoru k prostředku *clusteru* by se mělo provádět až po ověření, že se dokončilo vyhrazené zřízení clusteru Log Analytics. Data odesílaná do vašeho pracovního prostoru před dokončením budou vyřazena a nebude možné je obnovit.

- Šifrování CMK se vztahuje na nově ingestovaná data po konfiguraci CMK. Data, která byla ingestovaná před konfigurací CMK, zůstávají šifrovaná pomocí klíče Microsoft Key. Můžete zadávat dotazy na data ingestovaná před a po bezproblémové konfiguraci CMK.

- Azure Key Vault musí být nakonfigurované jako obnovitelné. Tyto vlastnosti nejsou ve výchozím nastavení povolené a měly by být nakonfigurované pomocí rozhraní příkazového řádku nebo PowerShellu:

  - Je nutné zapnout [obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) .
  - Pro ochranu proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění by měla být zapnutá [ochrana vyprázdnění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) .

- Prostředek *clusteru* přesunout do jiné skupiny prostředků nebo předplatného se momentálně nepodporuje.

- Vaše Azure Key Vault, prostředek *clusteru* a přidružené pracovní prostory musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných.

- Přidružení pracovního prostoru ke zdroji *clusteru* selže, pokud je přidruženo k jinému prostředku *clusteru* .


## <a name="management"></a>Správa

- **Získá všechny prostředky *clusteru* pro skupinu prostředků.**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Základě**
  
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
              keyVaultUri: "https://key-vault-name.vault.azure.net",
              keyName: "key-name",
              keyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Základě**
    
  Stejná odpověď jako u ' prostředků*clusteru* pro skupinu prostředků ', ale v oboru předplatného.

- **Aktualizovat *rezervaci kapacity* v prostředku *clusteru***

  Když se datový svazek, ke kterému se přiřadí vaše přidružené pracovní prostory, mění v čase a chcete patřičně aktualizovat úroveň rezervace kapacity. Postupujte podle kroků [aktualizace prostředku *clusteru* ](#update-cluster-resource-with-key-identifier-details) a zadejte novou hodnotu kapacity. Může být v rozsahu 1 000 až 2 000 GB za den a v krocích po 100. Pro zajištění vyšší úrovně než 2 000 GB za den kontaktujte kontakt Microsoftu, abyste ho povolili. Všimněte si, že nemusíte zadávat úplný text žádosti REST a měla by obsahovat SKU:

  ```json
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

  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Zrušit přiřazení pracovního prostoru**

  K provedení této operace potřebujete oprávnění zapisovat k pracovnímu prostoru a prostředku *clusteru* . Pracovní prostor z prostředku *clusteru* můžete kdykoli zrušit. Nová ingestovaná data po operaci de-Association je uložená v Log Analyticsovém úložišti a zašifrovaná pomocí klíče Microsoft Key. Můžete zadat dotaz na data, která byla ingestovaná do vašeho pracovního prostoru před a po přidružení deaktivace bez problémů, pokud je prostředek *clusteru* zřízený a nakonfigurovaný pomocí platného Key Vault klíče.

  Tato Správce prostředků požadavek je asynchronní operace.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  ```

  **Základě**

  200 OK a záhlaví.

  Ingestovaná data po tom, co je operace de-Association uložená v Log Analyticsm úložišti, můžou trvat 90 minut, než se dokončí. Stav zrušení přidružení pracovního prostoru můžete zjistit dvěma způsoby:

  1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
  2. Odeslání [pracovních prostorů – Získejte](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) požadavek a sledujte odpověď. v pracovním prostoru nebude mít *clusterResourceId* v části *funkce*.


- **Odstranění prostředku *clusteru***

  K provedení této operace potřebujete oprávnění Write pro prostředek *clusteru* . Operace obnovitelného odstranění je provedena, aby bylo možné obnovit *clusterový* prostředek včetně jeho dat do 14 dnů, ať už došlo k nechtěnému nebo úmyslnému odstranění. Název prostředku *clusteru* zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Po uplynutí doby tichého odstranění se název prostředku *clusteru* uvolní, prostředky a data *clusteru* se trvale odstraní a jsou neobnovitelná. Při operaci odstranění se z prostředku *clusteru* odpojí všechny přidružené pracovní prostory. Nová ingestovaná data se ukládají do Log Analyticsho úložiště a šifrují pomocí klíče Microsoft Key. Operace de-asociované pracovní prostory je asynchronní a dokončení může trvat až 90 minut.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Základě**

  200 OK

- **Obnovení prostředku *clusteru* a vašich dat** 
  
  Prostředek *clusteru* , který se odstranil za posledních 14 dní, je ve stavu obnovitelného odstranění a dá se obnovit s jeho daty. Vzhledem k tomu, že všechny pracovní prostory byly při odstraňování z prostředku *clusteru* deasociovány, je potřeba po obnovení CMK šifrování znovu přidružit své pracovní prostory. V současné době je operace obnovení prováděna ručně skupinou produktů. Pro požadavky na obnovení použijte Microsoft Channel.

## <a name="troubleshooting"></a>Odstraňování potíží
- Chování při Key Vault dostupnosti
  - V normálním provozu – mezipaměť úložiště AEK na krátkou dobu a vrátí se zpět na Key Vault k pravidelnému rozbalení.
    
  - Přechodné chyby připojení – úložiště zpracovává přechodné chyby (vypršení časového limitu, selhání připojení, problémy se službou DNS) tím, že klíče zůstanou v mezipaměti po krátké době delší a to přináší všechny malé výkyvůy v dostupnosti. Funkce dotazování a přijímání i nadále bez přerušení.
    
  - Živý web – nedostupnost přibližně 30 minut způsobí, že účet úložiště nebude k dispozici. Funkce dotazu není k dispozici a ingestovaná data se po několik hodin ukládají do mezipaměti pomocí Microsoft Key, aby se předešlo ztrátě dat. Po obnovení přístupu k Key Vault se dotaz zpřístupní a dočasná data uložená v mezipaměti se ingestují do úložiště dat a zašifrují pomocí CMK.

- Pokud vytvoříte prostředek *clusteru* a okamžitě zadáte KeyVaultProperties, operace může selhat, protože zásady přístupu nejde definovat, dokud není systémová identita přiřazená k prostředku *clusteru* .

- Pokud aktualizujete existující prostředek *clusteru* s KeyVaultProperties a zásada přístupu pro klíč "Get" v Key Vault chybí, operace se nezdaří.

- Pokud se pokusíte odstranit prostředek *clusteru* , který je přidružen k pracovnímu prostoru, operace odstranění selže.

- Pokud při vytváření prostředku *clusteru* dojde k chybě, může to být tím, že jste v posledních 14 dnech odstranili prostředek *clusteru* a je v období obnovitelného odstranění. Název prostředku *clusteru* zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Název se uvolní po uplynutí doby dočasného odstranění, kdy se prostředek *clusteru* trvale odstraní.

- Pokud provedete aktualizaci prostředku *clusteru* , zatímco probíhá operace, operace se nezdaří.

- Pokud se vám nepodaří nasadit prostředek *clusteru* , ověřte, že Azure Key Vault, prostředek *clusteru*   a přidružené Log Analytics pracovní prostory jsou ve stejné oblasti. Může být v různých předplatných.

- Pokud aktualizujete verzi klíče v Key Vault a neaktualizujete nové podrobnosti identifikátoru klíče v prostředku *clusteru* , cluster Log Analytics bude dál používat předchozí klíč a vaše data nebudou dostupná. Aktualizujte nové podrobnosti identifikátoru klíče v prostředku *clusteru* , aby se obnovil příjem dat a možnost dotazování na data.

- Pro podporu a nápovědu týkající se spravovaného klíče zákazníka použijte své kontakty do Microsoftu.

