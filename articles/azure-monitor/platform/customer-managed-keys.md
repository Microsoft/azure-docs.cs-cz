---
title: Azure Monitor konfiguraci klíče spravovaného zákazníkem
description: Informace a kroky konfigurace klíče spravovaného zákazníkem (CMK) k šifrování dat ve vašich Log Analyticsch pracovních prostorech pomocí Azure Key Vaultho klíče.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758808"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor konfiguraci klíče spravovaného zákazníkem 

Tento článek poskytuje základní informace a kroky ke konfiguraci klíčů spravovaných zákazníkem (CMK) pro vaše pracovní prostory Log Analytics a Application Insights součásti. Po nakonfigurování budou všechna data odesílaná do vašich pracovních prostorů nebo součástí šifrována pomocí Azure Key Vaultho klíče.

Před konfigurací doporučujeme zkontrolovat níže uvedená [omezení a omezení](#limitations-and-constraints) .

## <a name="disclaimers"></a>Právní omezení

- Azure Monitor CMK je funkce předčasného přístupu a povoluje se u registrovaných předplatných.

- Nasazení CMK popsané v tomto článku se dodává v produkční kvalitě a podporuje se, protože se jedná o funkci předčasného přístupu.

- Funkce CMK se doručuje na vyhrazený cluster úložiště dat, což je cluster Azure Průzkumník dat (ADX), který je vhodný pro zákazníky, kteří odesílají 1 TB za den. 

- Cenové modely CMK nejsou momentálně dostupné a nejsou uvedené v tomto článku. V druhém čtvrtletí kalendářního roku (CY) 2020 se očekává cenový model pro vyhrazený cluster ADX, který se bude vztahovat na všechna existující nasazení CMK.

- Tento článek popisuje CMK konfiguraci pro pracovní prostory Log Analytics. CMK pro součásti Application Insights jsou také podporovány pomocí tohoto článku, zatímco rozdíly jsou uvedeny v příloze.

> [!NOTE]
> Log Analytics a Application Insights používají stejnou platformu pro úložiště dat a dotazovací stroj.
> Tyto dvě úložiště přinášíme společně prostřednictvím integrace Application Insights do Log Analytics k vytvoření jednoho sjednoceného úložiště protokolů v rámci Azure Monitor. Tato změna se plánuje pro druhé čtvrtletí kalendářního roku 2020. Pokud nemusíte nasazovat CMK pro data Application Insights, doporučujeme počkat na dokončení konsolidace, protože tato nasazení budou přerušena konsolidací a po migraci na Log Analytics pracovní prostor budete muset znovu nakonfigurovat CMK. Minimum 1 TB za den se vztahuje na úrovni clusteru a až do dokončení konsolidace během druhého čtvrtletí Application Insights a Log Analytics vyžadují samostatné clustery.

## <a name="customer-managed-key-cmk-overview"></a>CMK (Customer-Managed Key) – přehled

[Šifrování v klidovém umístění](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) je běžným požadavkem na ochranu osobních údajů a zabezpečení v organizacích. Azure vám umožní plně spravovat šifrování v klidovém režimu, zatímco máte k dispozici různé možnosti, jak pečlivě spravovat šifrovací a šifrovací klíče.

Azure Monitor data-Store zajišťuje, aby všechna zašifrovaná data byla v klidovém stavu pomocí klíčů spravovaných Azure při uložení v Azure Storage. Azure Monitor taky nabízí možnost šifrování dat pomocí vlastního klíče uloženého v [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), ke kterému se přistupovalo pomocí [spravovaného ověřování identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) přiřazené systémem. Tento klíč může být buď [software, nebo hardware – chráněný](https://docs.microsoft.com/azure/key-vault/key-vault-overview)modulem HSM.
Azure Monitor použití šifrování je stejné jako způsob, jakým [Azure Storage šifrování](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) funguje.

Frekvence, kterou Azure Monitor přístup úložiště Key Vault pro zabalení a rozbalení operací je mezi 6 až 60 sekund.Azure Monitor Storage vždy respektuje změny v klíčových oprávněních během hodiny.

Ingestovaná data za posledních 14 dní jsou také uchovávána v Hot cache (zazálohovaně SSD) pro efektivní operaci dotazovacího stroje. Tato data zůstávají šifrovaná pomocí klíčů Microsoftu bez ohledu na konfiguraci CMK, ale pracujeme na tom, aby se disk SSD zašifroval s CMK v první polovině 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak CMK funguje v Azure Monitor

Azure Monitor využívá spravovanou identitu přiřazenou systémem k udělení přístupu k vašemu Azure Key Vault.Spravovaná identita přiřazená systémem se dá přidružit jenom k jednomu prostředku Azure. Identita Azure Monitorho úložiště dat (ADX) je podporovaná na úrovni clusteru a tím se určí, že se funkce CMK doručí ve vyhrazeném clusteru ADX. Aby bylo možné podporovat CMK ve více pracovních prostorech, nový prostředek Log Analytics (*cluster*) provádí jako zprostředkující připojení identity mezi Key Vault a vašimi pracovními prostory Log Analytics. Tento pojem odpovídá omezení identity přiřazené systémem a identita se udržuje mezi clusterem ADX a prostředkem *clusteru* Log Analytics, zatímco data všech přidružených pracovních prostorů jsou chráněná pomocí vašeho Key Vault klíče. Úložiště clusteru Underlay ADX používá spravovanou identitu, která\'je přidružená k prostředku *clusteru* pro ověřování a přístup k vašemu Azure Key Vault prostřednictvím Azure Active Directory.

![CMK – přehled](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Key Vault zákazníka.
2.    Prostředek *clusteru* Log Analytics zákazníka, který má spravovanou identitu s oprávněním Key Vault – identita je podporovaná na úrovni clusteru úložiště dat (ADX).
3.    Azure Monitor vyhrazený cluster ADX.
4.    Pracovní prostory zákazníka přidružené k prostředku *clusteru* pro šifrování CMK.

## <a name="encryption-keys-management"></a>Správa šifrovacích klíčů

Šifrování dat úložiště má tři typy klíčů:

- Šifrovací klíč **KEK** -Key (CMK)
- **AEK** šifrovací klíč účtu
- **Klíč DEK** – šifrovací klíč dat

Platí následující pravidla:

- Účty úložiště ADX generují jedinečný šifrovací klíč pro každý účet úložiště, který se označuje jako AEK.

- AEK se používá k odvození DEKs, což jsou klíče, které slouží k zašifrování každého bloku dat zapsaných na disk.

- Když nakonfigurujete klíč v Key Vault a odkazujete na něj v prostředku *clusteru* , Azure Storage posílá žádosti do vašeho Azure Key Vault k zabalení a rozbalení AEK k provádění operací šifrování a dešifrování dat.

- Váš KEK nikdy nezůstane Key Vault a v případě klíče HSM nikdy neopustí hardware.

- Azure Storage používá spravovanou identitu, která je přidružená k prostředku *clusteru* pro ověřování a přístup k Azure Key Vault prostřednictvím Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Postup zřizování CMK

V případě konfigurace Application Insights CMK postupujte podle obsahu přílohy pro kroky 3 a 6.

1. Seznam povolených odběrů – to je vyžadováno pro tuto funkci předčasného přístupu
2. Vytváření Azure Key Vault a ukládání klíče
3. Vytvoření prostředku *clusteru*
4. Zřizování úložiště dat Azure Monitor (cluster ADX)
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

Tento prostředek se používá jako zprostředkující připojení identity mezi Key Vault a vašimi pracovními prostory v Log Analytics. Jakmile obdržíte potvrzení, že vaše předplatná byla na seznamu povolených, vytvořte prostředek *clusteru* Log Analytics v oblasti, ve které jsou umístěny vaše pracovní prostory. Application Insights a Log Analytics vyžadují samostatné typy prostředků *clusteru* . Typ prostředku *clusteru* je definován při vytvoření nastavením vlastnosti *ClusterType* na hodnotu *LogAnalytics*nebo *ApplicationInsights*. Typ prostředku clusteru se nedá změnit po.

V případě konfigurace Application Insights CMK postupujte podle obsahu přílohy.

Při vytváření prostředku *clusteru* je nutné zadat úroveň rezervace kapacity (SKU). Úroveň rezervace kapacity může být v rozsahu 1 000 až 2 000 GB za den a můžete ji aktualizovat v krocích 100 později. Pokud potřebujete úroveň rezervace kapacity vyšší než 2 000 GB za den, obraťte se na kontakt Microsoftu, abyste ho povolili. Tato vlastnost nemá v současnosti vliv fakturace. v tomto případě se zavede cenový model vyhrazeného clusteru. bude se účtovat na všechna existující nasazení CMK.

**Vytvořit**

Tato Správce prostředků požadavek je asynchronní operace.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
Identita je přiřazena ke zdroji *clusteru* v okamžiku vytvoření.

**Základě**

200 OK a záhlaví.
Během období předčasného přístupu k této funkci se cluster ADX zřídí ručně. I když trvá zřízení v clusteru v rámci ADX a i po dokončení, můžete stav zřizování zjistit dvěma způsoby:
1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na hodnotu *provisioningState* . Je *ProvisioningAccount* při zřizování a *úspěšném* dokončení.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Zřizování úložiště dat Azure Monitor (cluster ADX)

Během období předčasného přístupu k této funkci cluster ADX zřídí produktový tým ručně po dokončení předchozích kroků. Pro tento krok použijte Microsoft Channel a poskytněte odpověď prostředku *clusteru* . 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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

> [!Warning]
> V aktualizaci prostředků *clusteru* musíte poskytnout úplný text, který zahrnuje *identity*, *SKU*, *KeyVaultProperties* a *Location*. Chybějící podrobnosti *KeyVaultProperties* odstraní identifikátor klíče z prostředku *clusteru* a způsobí [odvolání klíče](#cmk-kek-revocation).

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
     "capacity": 1000
     },
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Přidružení pracovního prostoru ke zdroji *clusteru*
V případě konfigurace Application Insights CMK postupujte podle obsahu přílohy pro tento krok.

K provedení této operace musíte mít oprávnění Write pro váš pracovní prostor i prostředek *clusteru* , což zahrnuje tyto akce:

- V pracovním prostoru: Microsoft. OperationalInsights/pracovní prostory/Write
- V prostředku *clusteru* : Microsoft. OperationalInsights/Clusters/Write

> [!IMPORTANT]
> Tento krok je třeba provést až po zřízení clusteru ADX. Pokud přidružíte pracovní prostory a ingestování dat před zřizováním, ingestovaná data se ztratí a nepůjde obnovit.

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

Přístup k datům můžete odvolat zakázáním klíče nebo odstraněním zásad přístupu k prostředkům *clusteru* ve vašem Key Vault. Služba Azure Monitor Storage vždy v průběhu jedné hodiny bude brát v platnost změny klíčových oprávnění a úložiště se stane nedostupným. Všechna data, která jsou v pracovních prostorech přidružených k vašemu prostředku *clusteru* přidružená, se ztratí a dotazy selžou. Dříve přijímaná data zůstávají v Azure Monitor úložišti nepřístupná, pokud jste prostředek *clusteru* a vaše pracovní prostory nebudou smazány. Nepřístupná data se řídí zásadami uchovávání dat a při dosažení doby uchování se odstraní.

Úložiště se bude pravidelně dotazovat na Key Vault a pokusí se o rozbalení šifrovacího klíče a po jeho použití, příjmu dat a obnovení dotazů do 30 minut.

## <a name="cmk-kek-rotation"></a>Rotace CMK (KEK)

Rotace CMK vyžaduje explicitní aktualizaci prostředku *clusteru* s novou verzí klíče v Azure Key Vault. Pokud chcete Azure Monitor aktualizovat s novou verzí klíče, postupujte podle pokynů v kroku aktualizace prostředku *clusteru* s podrobnostmi identifikátoru klíče. Pokud aktualizujete verzi klíče v Key Vault a neaktualizujete nové podrobnosti identifikátoru klíče v prostředku *clusteru* , Azure monitor úložiště bude dál používat předchozí klíč.
Všechna vaše data jsou přístupná po operaci střídání klíčů, včetně dat přijatých před otočením a po ní, protože všechna data zůstanou zašifrovaná šifrovacím klíčem účtu (AEK), zatímco AEK je teď zašifrovaná pomocí vaší nové verze KEK (Key Encryption Key).

## <a name="limitations-and-constraints"></a>Omezení a omezení

- Funkce CMK je podporovaná na úrovni clusteru ADX a vyžaduje vyhrazený cluster Azure Monitor ADX s požadavkem na odeslání 1 TB za den.

- Maximální počet prostředků *clusteru* na předplatné je omezený na 2.

- Přidružení prostředků *clusteru* k pracovnímu prostoru by se mělo provádět až po ověření, že se Provisioning cluster ADX dokončil. Data odesílaná do vašeho pracovního prostoru před dokončením zřizování se ztratí a nepůjde obnovit.

- Šifrování CMK se vztahuje na nově ingestovaná data po konfiguraci CMK. Data, která byla ingestovaná před konfigurací CMK, zůstávají šifrovaná pomocí klíče Microsoft Key. Můžete zadávat dotazy na data ingestovaná před a po bezproblémové konfiguraci CMK.

- Pokud se rozhodnete, že CMK není pro konkrétní pracovní prostor nutný, můžete zrušit přidružení pracovního prostoru od prostředku *clusteru* . Nová ingestovaná data po operaci de-Association se uloží do sdíleného Log Analytics úložiště jako předtím, než byla přidružena k prostředku *clusteru* . Pokud je prostředek *clusteru* zřízený a nakonfigurovaný pomocí platného Key Vaultho klíče, můžete dotazovat se na data ingestovaná před a po deasociaci.

- Azure Key Vault musí být nakonfigurované jako obnovitelné. Tyto vlastnosti nejsou ve výchozím nastavení povolené a měly by být nakonfigurované pomocí rozhraní příkazového řádku nebo PowerShellu:

  - Je nutné zapnout [obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) .
  - Pro ochranu proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění by měla být zapnutá [ochrana vyprázdnění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) .

- Application Insights a Log Analytics vyžadují samostatné prostředky *clusteru* . Typ prostředku *clusteru* je definován při vytvoření nastavením vlastnosti "clusterType" na hodnotu "LogAnalytics" nebo "ApplicationInsights". Typ prostředku *clusteru* se nedá změnit.

- Prostředek *clusteru* přesunout do jiné skupiny prostředků nebo předplatného se momentálně nepodporuje.

- Vaše Azure Key Vault, prostředek *clusteru* a přidružené pracovní prostory musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných.

- Přidružení pracovního prostoru ke zdroji *clusteru* selže, pokud je přidruženo k jinému prostředku *clusteru* .

## <a name="troubleshooting-and-management"></a>Řešení potíží a Správa

- Dostupnost Key Vault
    - V normálním provozu – mezipaměť úložiště AEK na krátkou dobu a vrátí se zpět na Key Vault k pravidelnému rozbalení.
    
    - Přechodné chyby připojení – úložiště zpracovává přechodné chyby (vypršení časového limitu, selhání připojení, problémy se službou DNS) tím, že klíče zůstanou v mezipaměti po krátké době delší a to přináší všechny malé výkyvůy v dostupnosti. Funkce dotazování a přijímání i nadále bez přerušení.
    
    - Živý web – nedostupnost přibližně 30 minut způsobí, že účet úložiště nebude k dispozici. Funkce dotazu není k dispozici a ingestovaná data se po několik hodin ukládají do mezipaměti pomocí Microsoft Key, aby se předešlo ztrátě dat. Po obnovení přístupu k Key Vault se dotaz zpřístupní a dočasná data uložená v mezipaměti se ingestují do úložiště dat a zašifrují pomocí CMK.

- Pokud vytvoříte prostředek *clusteru* a okamžitě zadáte KeyVaultProperties, operace může selhat, protože zásady přístupu nejde definovat, dokud není systémová identita přiřazená k prostředku *clusteru* .

- Pokud aktualizujete existující prostředek *clusteru* s KeyVaultProperties a zásada přístupu pro klíč "Get" v Key Vault chybí, operace se nezdaří.

- Pokud se pokusíte odstranit prostředek *clusteru* , který je přidružen k pracovnímu prostoru, operace odstranění selže.

- Pokud při vytváření prostředku *clusteru* dojde k chybě, může to být tím, že jste v posledních 14 dnech odstranili prostředek *clusteru* a je v období obnovitelného odstranění. Název prostředku *clusteru* zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Název se uvolní po uplynutí doby dočasného odstranění, kdy se prostředek *clusteru* trvale odstraní.

- Načíst všechny prostředky *clusteru* pro skupinu prostředků:

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
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Získat všechny prostředky *clusteru* pro předplatné:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Základě**
    
  Stejná odpověď jako u ' prostředků*clusteru* pro skupinu prostředků ', ale v oboru předplatného.

- Aktualizovat *rezervaci kapacity* v prostředku *clusteru* – když se změní datový svazek na přidružené pracovní prostory a chcete aktualizovat úroveň rezervace kapacity pro účely fakturace, postupujte podle [prostředku aktualizovat *cluster* ](#update-cluster-resource-with-key-identifier-details) a zadejte novou hodnotu kapacity. Úroveň rezervace kapacity může být v rozsahu 1 000 až 2 000 GB za den a v krocích po 100. Pro zajištění vyšší úrovně než 2 000 GB za den kontaktujte kontakt Microsoftu, abyste ho povolili.

- Odstranění prostředku *clusteru* – provede se operace obnovitelného odstranění, která umožňuje obnovení prostředku *clusteru* včetně jeho dat do 14 dnů, ať už došlo k nechtěnému nebo úmyslnému odstranění. Název prostředku *clusteru* zůstane rezervovaný během období obnovitelného odstranění a nemůžete vytvořit nový cluster s tímto názvem. Po uplynutí doby tichého odstranění se název prostředku *clusteru* uvolní, prostředky a data *clusteru* se trvale odstraní a jsou neobnovitelná. Při operaci odstranění se z prostředku *clusteru* odpojí všechny přidružené pracovní prostory. Nová ingestovaná data se ukládají do sdíleného Log Analyticsho úložiště a šifrují s klíčem Microsoft Key. Operace de-asociované pracovní prostory je asynchronní.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Základě**

  200 OK

- Obnovení prostředku *clusteru* a vašich dat – prostředek *clusteru* , který se odstranil za posledních 14 dní, je ve stavu obnovitelného odstranění a je možné ho obnovit. Tato služba je v současnosti prováděna ručně skupinou produktů. Pro požadavky na obnovení použijte Microsoft Channel.

## <a name="appendix"></a>Příloha

Také se podporuje CMK (Customer Managed Key), i když byste měli vzít v úvahu následující změny, které vám pomůžou naplánovat nasazení CMK pro komponenty Insight Application Insights. Application Insights

Log Analytics a Application Insights používají stejnou platformu pro úložiště dat a dotazovací stroj. Tato dvě úložiště přinášíme společně prostřednictvím integrace Application Insights do Log Analytics a v rámci Azure Monitor druhé čtvrtletí poskytneme jediné sjednocené úložiště protokolů.
2020. Tato změna přinese data do vašich aplikací do Log Analytics pracovních prostorů a provede dotazy, přehledy a další vylepšení, které je možné použít i v případě, že se konfigurace CMK v pracovním prostoru vztahuje i na vaše Application Insights data.

> [!NOTE]
> Pokud před integrací nemusíte nasazovat CMK pro data vaší aplikace, doporučujeme počkat s Application Insights CMK, protože taková nasazení budou přerušena integrací a po migraci na Log Analytics pracovní prostor budete muset znovu nakonfigurovat CMK. Minimum 1 TB za den se vztahuje na úrovni clusteru a až do dokončení konsolidace během druhého čtvrtletí Application Insights a Log Analytics vyžadují samostatné clustery.

## <a name="application-insights-cmk-configuration"></a>Konfigurace Application Insights CMK

Konfigurace Application Insights CMK je stejná jako proces, který je znázorněný v tomto článku, včetně omezení a řešení potíží s výjimkou těchto kroků:

- Vytvoření prostředku *clusteru*
- Přidružení součásti k prostředku *clusteru*

Při konfiguraci CMK pro Application Insights použijte tento postup namísto těch, které jsou uvedeny výše.

### <a name="create-a-cluster-resource"></a>Vytvoření prostředku *clusteru*

Tento prostředek se používá jako zprostředkující připojení identity mezi vaším Key Vault a vašimi komponentami. AŽ obdržíte potvrzení, že vaše předplatná byla povolená, vytvořte prostředek *clusteru* Log Analytics v oblasti, ve které jsou umístěné vaše komponenty. Typ prostředku *clusteru* je definován při vytvoření nastavením vlastnosti *ClusterType* na hodnotu *LogAnalytics*nebo *ApplicationInsights*. Měl by být *ApplicationInsights* pro Application Insights CMK. Nastavení *clusterType* nelze po konfiguraci změnit.

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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Základě**

200 OK a záhlaví.
Během období předčasného přístupu k této funkci se cluster ADX zřídí ručně. I když trvá zřízení v clusteru v rámci ADX a i po dokončení, můžete stav zřizování zjistit dvěma způsoby:
1. Z odpovědi Zkopírujte hodnotu adresy URL Azure-AsyncOperation a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na hodnotu *provisioningState* . Je *ProvisioningAccount* při zřizování a *úspěšném* dokončení.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Přidružení součásti k prostředku *clusteru* pomocí [komponent – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) rozhraní API

K provedení této operace je potřeba mít oprávnění Write pro i prostředek *clusteru* , což zahrnuje tyto akce:

- V součásti: Microsoft. Insights/Component/Write
- V prostředku *clusteru* : Microsoft. OperationalInsights/Clusters/Write

> [!IMPORTANT]
> Tento krok je třeba provést až po zřízení clusteru ADX. Pokud přiřadíte součásti a ingestovat data před zřizováním, ingestovaná data se ztratí a nepůjde obnovit.
> Pokud chcete ověřit, jestli je cluster ADX zřízený, spusťte *clusterový* prostředek získat REST API a zkontrolujte, jestli je hodnota *provisioningState* *úspěšná*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> Zkopírujte a zachovejte odpověď, protože ji budete potřebovat v dalších krocích.

**Přidružit komponentu**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" je hodnota "clusterId" zadaná v odpovědi z předchozího kroku.
Příklad "druh" je "Web".

**Základě**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId" je ID prostředku *clusteru* , které je přidruženo k této součásti.

Po přidružení se data odesílaná do vašich komponent ukládají zašifrovaný pomocí spravovaného klíče.
