---
title: Konfigurace klíče spravované ho zákazníkem Azure Monitor
description: Informace a kroky konfigurace klíče spravovaného zákazníkem (CMK) k šifrování dat v pracovních prostorech Analýzy protokolů pomocí klíče Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: dbd217c7135172c52a5ec7459930977960c452aa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260853"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Konfigurace klíče spravované ho zákazníkem Azure Monitor 

Tento článek obsahuje základní informace a kroky ke konfiguraci klíčů spravovaných zákazníkem (CMK) pro pracovní prostory analýzy protokolů a součásti Application Insights. Po nakonfigurování se všechna data odeslaná do pracovních prostorů nebo součástí zašifrují pomocí klíče Azure Key Vault.

Doporučujeme zkontrolovat [omezení a omezení](#limitations-and-constraints) níže před konfigurací.

## <a name="disclaimers"></a>Právní omezení

- Azure Monitor CMK je funkce včasného přístupu a povolená pro registrované předplatná.

- Nasazení CMK popsané v tomto článku je dodáván v kvalitě výroby a podporovány jako takové, i když je to funkce včasného přístupu.

- Funkce CMK se dodává ve vyhrazeném clusteru úložiště dat, což je cluster Azure Data Explorer (ADX) a je vhodný pro zákazníky odesílající 1 TB za den nebo více. 

- Cenový model CMK není momentálně k dispozici a v tomto článku se na něj nevztahuje. Cenový model pro vyhrazený cluster ADX se očekává ve druhém čtvrtletí kalendářního roku (CY) 2020 a bude se vztahovat na všechna existující nasazení CMK.

- Tento článek popisuje konfiguraci CMK pro pracovní prostory Analýzy protokolů. Cmk pro součásti Application Insights je také podporovánpomocí tohoto článku, zatímco rozdíly jsou uvedeny v dodatku.

> [!NOTE]
> Log Analytics a Application Insights používají stejnou platformu úložiště dat a dotazovací stroj.
> Tyto dva obchody spojujeme prostřednictvím integrace přehledů aplikací do analýzy protokolů a vytváříme jedno jednotné úložiště protokolů v rámci Azure Monitoru. Tato změna je plánována na druhé čtvrtletí kalendářního roku 2020. Pokud do té doby není nutné nasazovat CMK pro data Application Insights, doporučujeme počkat na dokončení konsolidace, protože tato nasazení budou narušena konsolidací a budete muset po migraci do pracovního prostoru Log Analytics znovu nakonfigurovat CMK. Minimální hodnota 1 TB za den platí na úrovni clusteru a dokud nebude konsolidace dokončena během druhého čtvrtletí, vyžadují přehledy aplikací a analýza protokolů samostatné clustery.

## <a name="customer-managed-key-cmk-overview"></a>Přehled klíčů spravovaných zákazníkem (CMK)

[Šifrování v klidovém stavu](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) je běžnýpožadavek na ochranu osobních údajů a zabezpečení v organizacích. Azure můžete zcela spravovat šifrování v klidovém stavu, zatímco máte různé možnosti, jak úzce spravovat šifrovací nebo šifrovací klíče.

Úložiště dat Azure Monitor zajišťuje, že všechna data šifrovaná v klidovém stavu pomocí klíčů spravovaných Azure při ukládání ve službě Azure Storage. Azure Monitor také poskytuje možnost šifrování dat pomocí vlastního klíče, který je uložený ve vašem [trezoru klíčů Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview), ke kterému se přistupuje pomocí systémově přiřazeného spravovaného ověřování [identity.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Tento klíč může být [chráněn softwarem nebo hardwarem-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor použití šifrování je shodné se způsobem, jakým [funguje šifrování Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)

Frekvence, která Azure Monitor Storage přistupuje k trezoru klíčů pro operace zalamování a rozbalování je mezi 6 až 60 sekund.Azure Monitor Storage vždy respektuje změny v klíčových oprávnění během jedné hodiny.

Ingestovaná data za posledních 14 dní jsou také uložena v hot-cache (SSD-backed) pro efektivní provoz dotazovacího modulu. Tato data zůstávají šifrována pomocí klíčů společnosti Microsoft bez ohledu na konfiguraci CMK, ale pracujeme na tom, aby byl SSD šifrován s CMK v první polovině roku 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak cmk funguje ve službě Azure Monitor

Azure Monitor využívá spravované identity přiřazené systémem k udělení přístupu k úložišti klíčů Azure.Spravovaná identita přiřazená systémem může být přidružena jenom k jednomu prostředku Azure. Identita úložiště dat Azure Monitor (cluster ADX) je podporována na úrovni clusteru a to vyžaduje, aby funkce CMK byla doručena ve vyhrazeném clusteru ADX. Pro podporu CMK na více pracovních prostorech, nový prostředek Log Analytics *(Cluster)* provádí jako zprostředkující připojení identity mezi trezoru klíčů a pracovníprostory Log Analytics. Tento koncept je v souladu s omezením identity přiřazené systémem a identita je udržována mezi clusterem ADX a prostředkem *clusteru* Log Analytics, zatímco data všech přidružených pracovních prostorů jsou chráněna klíčem trezoru klíčů klíčů. Úložiště clusteru ADX podložení používá\'spravovanou identitu, která je přidružena k prostředku *clusteru* k ověření a přístupu k úložišti klíčů Azure prostřednictvím služby Azure Active Directory.

![CMK - přehled](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Trezor klíčů zákazníka.
2.    Prostředek *clusteru* Log Analytics zákazníka, který má spravovanou identitu s oprávněními k trezoru klíčů – identita je podporována na úrovni úložiště dat (cluster ADX).
3.    Vyhrazený cluster ADX azure monitoru.
4.    Pracovní prostory zákazníka přidružené k prostředku *clusteru* pro šifrování CMK.

## <a name="encryption-keys-management"></a>Správa šifrovacích klíčů

Šifrování dat úložiště zahrnuje 3 typy klíčů:

- **KEK** - Šifrovací klíč (CMK)
- **AEK** - Šifrovací klíč účtu
- **DEK** - šifrovací klíč dat

Platí následující pravidla:

- Účty úložiště ADX generují jedinečný šifrovací klíč pro každý účet úložiště, který se nazývá AEK.

- AEK se používá k odvození DEKs, které jsou klíče, které se používají k šifrování každý blok dat zapsaných na disk.

- Když nakonfigurujete klíč v trezoru klíčů a odkazujete na něj v prostředku *clusteru,* Azure Storage odešle požadavky do trezoru klíčů Azure zabalit a rozbalit AEK k provádění operací šifrování a dešifrování dat.

- Vaše KEK nikdy neopustí trezor klíčů a v případě klíče hardwarového modulu nikdy neopustí hardware.

- Azure Storage používá spravovanou identitu, která je přidružená k prostředku *clusteru,* k ověření a přístupu k Azure Key Vault prostřednictvím Služby Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>CmK zřizování postup

Pro konfiguraci CMK Application Insights postupujte podle obsahu dodatku pro kroky 3 a 6.

1. Odběr whitelisting - to je nutné pro tuto funkci včasného přístupu
2. Vytváření trezoru klíčů Azure a ukládání klíče
3. Vytvoření *prostředku clusteru*
4. Zřizování úložiště dat Azure Monitor (cluster ADX)
5. Udělení oprávnění trezoru klíčů
6. Přisuzování pracovních prostorů Analýzy protokolů

Postup není podporován v ui aktuálně a proces zřizování se provádí prostřednictvím rozhraní REST API.

> [!IMPORTANT]
> Každý požadavek rozhraní API musí obsahovat token autorizace nosiče v hlavičce požadavku.

Příklad:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Kde *eyJ0eXAiO....* představuje úplný token autorizace. 

Token můžete získat pomocí jedné z těchto metod:

1. Použijte [metodu registrace aplikací.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
2. Na webu Azure Portal
    1. Přechod na portál Azure v "vývojářském nástroji" (F12)
    1. Vyhledejte autorizační řetězec v části "Záhlaví požadavků" v jedné z instancí "batch?api-version". Vypadá to takto: "autorizace: Bearer eyJ0eXAiO....". 
    1. Zkopírujte a přidejte do volání rozhraní API podle níže uvedených příkladů.
3. Přejděte na web dokumentace Azure REST. Stiskněte tlačítko "Try it" na libovolnérozhraní API a zkopírujte token Nosiče.

### <a name="asynchronous-operations-and-status-check"></a>Asynchronní operace a kontrola stavu

Některé operace v této konfigurační masce jsou spuštěny asynchronně, protože je nelze rychle dokončit. Odpověď pro asynchronní operace zpočátku vrátí stavový kód HTTP 200 (OK) a záhlaví s *vlastností Azure-AsyncOperation* při přijetí:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Stav asynchronní operace můžete zkontrolovat odesláním požadavku GET do hodnoty hlavičky *Azure-AsyncOperation:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

Odpověď obsahuje informace o operaci a jejím *stavu*. Může to být jedna z následujících možností:

Probíhá operace.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

Operace je dokončena.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Operace se nezdařila.
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

Funkce CMK je funkce včasného přístupu. Předplatná, kde plánujete vytvořit prostředky *clusteru* musí být předem naseznamu povolených podle skupiny produktů Azure. Pomocí kontaktů do Microsoftu zadejte ID předplatných.

> [!IMPORTANT]
> Schopnost CMK je regionální. Trezor klíčů Azure, prostředek *clusteru* a přidružené pracovní prostory Analýzy protokolů musí být ve stejné oblasti, ale mohou být v různých předplatných.

### <a name="storing-encryption-key-kek"></a>Ukládání šifrovacího klíče (KEK)

Vytvořte nebo použijte trezor klíčů Azure, který už musíte vygenerovat, nebo importujte klíč, který se použije pro šifrování dat. Trezor klíčů Azure musí být nakonfigurovaný jako obnovitelný, aby byl váš klíč a přístup k vašim datům ve službě Azure Monitor. Tuto konfiguraci můžete ověřit ve vlastnostech v trezoru klíčů, měla by být povolena *ochrana proti odstranění pomocí programu Soft* a *Purge.*

![Nastavení ochrany proti odstranění a vymazání](media/customer-managed-keys/soft-purge-protection.png)

Tato nastavení jsou k dispozici prostřednictvím rozhraní příkazu příkazové ho příkazu a prostředí PowerShell:
- [Obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Očistit ochranné](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) kryty proti vymazání síly tajemství / trezoru i po měkkém odstranění

### <a name="create-cluster-resource"></a>Vytvořit prostředek *clusteru*

Tento prostředek se používá jako zprostředkující připojení identity mezi trezorem klíčů a pracovními prostory Analýzy protokolů. Po obdržení potvrzení, že vaše předplatná byla zapsána na seznam povolených, vytvořte prostředek *clusteru* Analýzy protokolů v oblasti, kde jsou umístěny vaše pracovní prostory. Přehledy aplikací a analýzy protokolů vyžadují samostatné typy prostředků *clusteru.* Typ prostředku *clusteru* je definován v době vytvoření nastavením *vlastnosti clusterType* na *LogAnalytics*nebo *ApplicationInsights*. Typ prostředku clusteru nelze změnit po.

Pro konfiguraci CMK Application Insights postupujte podle obsahu dodatku.

Při vytváření prostředku *clusteru* je nutné určit úroveň rezervace kapacity (sku). Úroveň rezervace kapacity může být v rozsahu 1 000 až 2 000 GB za den a můžete ji aktualizovat v krocích po 100 později. Pokud potřebujete úroveň rezervace kapacity vyšší než 2 000 GB za den, obraťte se na kontakt společnosti Microsoft a povolte ji. Tato vlastnost nemá vliv na fakturaci v současné době – po zavedení cenového modelu pro vyhrazený cluster, fakturace se bude vztahovat na všechny existující nasazení CMK.

**Vytvořit**

Tento požadavek správce prostředků je asynchronní operace.

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
Identita je přiřazena prostředku *clusteru* v době vytvoření.

**Reakce**

200 OK a záhlaví.
Během období předčasného přístupu funkce clusteru ADX je zřízena ručně. Zatímco trvá zřizování poddx clusteru chvíli dokončit, můžete zkontrolovat stav zřizování dvěma způsoby:
1. Zkopírujte hodnotu adresy URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na hodnotu *provisioningState.* Je *ProvisioningAccount* při zřizování a *úspěšné po* dokončení.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Zřizování úložiště dat Azure Monitor (cluster ADX)

Během období předčasného přístupu funkce je cluster ADX zřízen ručně produktovým týmem po dokončení předchozích kroků. Pro tento krok použijte svůj kanál společnosti Microsoft a zadejte odpověď na prostředky *clusteru.* 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Zkopírujte a uložte odpověď, protože budete potřebovat podrobnosti v dalších krocích.

**Reakce**

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

Identifikátor GUID "principalId" je generován službou spravované identity pro prostředek *clusteru.*

### <a name="grant-key-vault-permissions"></a>Udělení oprávnění trezoru klíčů

Aktualizujte trezor klíčů pomocí nové zásady přístupu, která uděluje oprávnění k prostředku *clusteru.* Tato oprávnění se používají podložení Azure Monitor Storage pro šifrování dat. Otevřete trezor klíčů na webu Azure portal a klikněte na "Zásady přístupu" a potom na "+ Přidat zásady přístupu" a vytvořte zásadu s těmito nastaveními:

- Klíčová oprávnění: vyberte oprávnění "Získat", Zalamovat klíč a Rozbalit klíč.
- Vyberte hlavní: zadejte hodnotu id hlavního povinného, která byla vrácena v odpovědi v předchozím kroku.

![udělení oprávnění trezoru klíčů](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

*Získat* oprávnění je nutné ověřit, že trezor klíčů je nakonfigurovaný jako obnovitelný k ochraně vašeho klíče a přístupu k datům Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizace prostředku clusteru podrobnostmi o identifikátoru klíče

Tento krok se provádí během počátečních a budoucích aktualizací klíčových verzí v trezoru klíčů. Informuje Azure Monitor Storage o verzi klíče, která se má použít pro šifrování dat. Po aktualizaci se nový klíč používá k zabalení a rozbalení klíče úložiště (AEK).

Chcete-li aktualizovat prostředek *clusteru* podrobnostmi *o identifikátoru klíče* trezoru klíčů, vyberte aktuální verzi klíče v trezoru klíčů Azure, abyste získali podrobnosti o identifikátoru klíče.

![Udělení oprávnění trezoru klíčů](media/customer-managed-keys/key-identifier-8bit.png)

Aktualizujte prostředek *clusteru* KeyVaultProperties podrobnostmi o identifikátoru klíče.

**Aktualizace**

Tento požadavek správce prostředků je asynchronní operace.

> [!Warning]
> V aktualizaci prostředků *clusteru* je nutné zadat celé tělo, které zahrnuje *identitu*, *sku*, *KeyVaultProperties* a *umístění*. Chybějící podrobnosti *KeyVaultProperties* odeberou identifikátor klíče z prostředku *clusteru* a způsobí [odvolání klíče](#cmk-kek-revocation).

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
"KeyVaultProperties" obsahuje podrobnosti o identifikátoru klíče trezoru klíčů.

**Reakce**

200 OK a záhlaví.
Trvá šíření identifikátor klíče několik minut k dokončení. Stav zřizování můžete zkontrolovat dvěma způsoby:
1. Zkopírujte hodnotu adresy URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na vlastnosti *KeyVaultProperties.* Vaše nedávno aktualizované údaje o identifikátoru klíče by se měly vrátit v odpovědi.

Odpověď na požadavek GET na prostředek *clusteru* by měla vypadat takto po dokončení aktualizace identifikátoru klíče:

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

### <a name="workspace-association-to-cluster-resource"></a>Přidružení pracovního prostoru k prostředku *clusteru*
Pro konfiguraci CMK Application Insights postupujte podle obsahu dodatku pro tento krok.

K provedení této operace, která zahrnuje tyto akce, musíte mít oprávnění k zápisu do pracovního prostoru i prostředků *clusteru:*

- V pracovním prostoru: Microsoft.OperationalInsights/workspaces/write
- V *prostředku clusteru:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Tento krok by měl být proveden pouze po zřizování clusteru ADX. Pokud přidružíte pracovní prostory a ingestovat data před zřizování, ingestovaná data budou zrušena a nebude obnovitelná.

**Přidružení pracovního prostoru**

Tento požadavek správce prostředků je asynchronní operace.

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

**Reakce**

200 OK a záhlaví.
Po operaci přidružení jsou uložena deštovaná data se spravovaným klíčem, což může trvat až 90 minut. Stav přidružení pracovního prostoru můžete zkontrolovat dvěma způsoby:
1. Zkopírujte hodnotu adresy URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odeslat [pracovní prostory – získat](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) požadavek a sledovat odpověď, přidružený pracovní prostor bude mít clusterResourceId pod "funkce".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
```

**Reakce**

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

## <a name="cmk-kek-revocation"></a>Zrušení CMK (KEK)

Přístup k datům můžete odvolat zakázáním klíče nebo odstraněním zásad přístupu k prostředkům *clusteru* v trezoru klíčů. Azure Monitor Storage bude vždy respektovat změny v klíčových oprávnění během jedné hodiny, obvykle dříve a úložiště bude k dispozici. Všechna data požitá do pracovních prostorů přidružených k prostředku *clusteru* jsou vynechána a dotazy se nezdaří. Dříve ingestovaná data zůstanou ve službě Azure Monitor Storage nepřístupná, pokud jste vaším *prostředkem clusteru* a vaše pracovní prostory se neodstraní. Nedostupná data se řídí zásadami uchovávání dat a budou vymazána, když je dosaženo uchovávání informací.

Úložiště bude pravidelně dotazování trezoru klíčů, aby se pokusil rozbalit šifrovací klíč a po přístupu k obnovení přihlašování dat a dotazu během 30 minut.

## <a name="cmk-kek-rotation"></a>Otáčení CMK (KEK)

Otočení CMK vyžaduje explicitní aktualizaci prostředku *clusteru* s novou verzí klíče v azure key vault. Pokud chcete azure monitor aktualizovat novou verzí klíče, postupujte podle pokynů v kroku Aktualizovat prostředek *clusteru* podrobnosti o identifikátoru klíče. Pokud aktualizujete verzi klíče v trezoru klíčů a neaktualizujete podrobnosti o novém identifikátoru klíče v prostředku *clusteru,* azure monitor storage bude nadále používat váš předchozí klíč.
Všechna data jsou přístupná po operaci otočení klíče, včetně dat požitých před otočením a po něm, protože všechna data zůstávají šifrována šifrovacím klíčem účtu (AEK), zatímco AEK je nyní šifrována novou verzí kek (Key Encryption Key).

## <a name="limitations-and-constraints"></a>Omezení a omezení

- Funkce CMK je podporovaná na úrovni clusteru ADX a vyžaduje vyhrazený cluster Azure Monitor ADX s požadavkem na odesílání 1 TB za den nebo více.

- Maximální počet prostředků *clusteru* na jedno předplatné je omezen na 2

- Přidružení prostředků *clusteru* do pracovního prostoru by mělo být přeneseno pouze po ověření, že zřizování clusteru ADX bylo splněno. Data odeslaná před tímto zřizováním budou zrušena a nebudou obnovitelná.

- Cmk šifrování platí pro nově pozůstalá data po konfiguraci CMK. Data, která byla ingestována před konfigurací CMK, zůstávají zašifrována pomocí klíče společnosti Microsoft. Můžete dotazovat data pomohou před a po konfiguraci CMK bez problémů.

- Jakmile je pracovní prostor přidružen k prostředku *clusteru,* nelze jej odkupit od prostředku *clusteru,* protože data jsou šifrovaná pomocí vašeho klíče a nejsou přístupná bez vašeho KEK v trezoru klíčů Azure.

- Trezor klíčů Azure musí být nakonfigurován jako obnovitelný. Tyto vlastnosti nejsou ve výchozím nastavení povoleny a měly by být nakonfigurovány pomocí rozhraní příkazu příkazu příkazu cli a prostředí PowerShell:

  - [Měkké odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musí být zapnuto.
  - [Ochrana proti vymazání](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) by měla být zapnuta, aby se chránila před vymazáním síly tajného trezoru / trezoru i po měkkém odstranění

- Přehledy aplikací a analýzy protokolů vyžadují samostatné prostředky *clusteru.* Typ prostředku *clusteru* je definován v době vytvoření nastavením vlastnosti "clusterType" na loganalytics nebo "ApplicationInsights". Typ prostředku *clusteru* nelze změnit.

- *Prostředek clusteru* přesunout do jiné skupiny prostředků nebo odběr není aktuálně podporována.

- Trezor klíčů Azure, prostředek *clusteru* a přidružené pracovní prostory musí být ve stejné oblasti a ve stejném tenantovi Azure Active Directory (Azure AD), ale můžou být v různých předplatných.

- Přidružení pracovního prostoru k prostředku *clusteru* se nezdaří, pokud je přidruženo k jinému prostředku *clusteru.*

## <a name="troubleshooting-and-management"></a>Řešení potíží a správa

- Dostupnost trezoru klíčů
    - V normálním provozu – Úložiště ukládá do mezipaměti AEK na krátkou dobu a přejde zpět do trezoru klíčů rozbalit pravidelně.
    
    - Přechodné chyby připojení – úložiště zpracovává přechodné chyby (časové limity, selhání připojení, problémy dns) tím, že klíče zůstat v mezipaměti na krátkou dobu déle, a to překonává všechny malé chyby v dostupnosti. Možnosti dotazu a ingestování pokračovat bez přerušení.
    
    - Živý web – nedostupnost přibližně 30 minut způsobí, že účet úložiště přestane být nedostupný. Funkce dotazu není k dispozici a ingestovaná data jsou uložena do mezipaměti několik hodin pomocí klíče společnosti Microsoft, aby se zabránilo ztrátě dat. Při obnovení přístupu k trezoru klíčů dotaz k dispozici a dočasná data uložená v mezipaměti je požití do úložiště dat a šifrované s CMK.

- Pokud vytvoříte prostředek *clusteru* a okamžitě zadáte vlastnosti KeyVaultProperties, operace může selhat, protože zásadu přístupu nelze definovat, dokud není prostředku *clusteru* přiřazena systémová identita.

- Pokud aktualizujete existující prostředek *clusteru* pomocí vlastností KeyVaultProperties a v trezoru klíčů chybí zásady přístupu klíče Get, operace se nezdaří.

- Pokud se pokusíte odstranit prostředek *clusteru,* který je přidružen k pracovnímu prostoru, operace odstranění se nezdaří.

- Získejte všechny prostředky *clusteru* pro skupinu prostředků:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Reakce**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
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

- Získejte všechny prostředky *clusteru* pro předplatné:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Reakce**
    
  Stejná odpověď jako pro '*prostředky clusteru* pro skupinu prostředků', ale v oboru předplatného.
    
- Odstranit prostředek *clusteru* – operace obnovitelného odstranění se provádí, aby bylo možné obnovit prostředek clusteru, data a přidružené pracovní prostory do 14 dnů, ať už bylo odstranění náhodné nebo úmyslné. Název *prostředku clusteru* zůstane rezervován během období slabého odstranění a nelze vytvořit nový cluster s tímto názvem. Po období obnovitelného odstranění jsou prostředky *clusteru* a data neobnovitelná. Přidružené pracovní prostory jsou de-associated z prostředku *clusteru* a nová data se ingestuje do sdíleného úložiště a šifrované s klíčem Microsoft.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Reakce**

  200 OK

- Obnovte prostředek *clusteru* a data – během období obnovitelného odstranění vytvořte prostředek *clusteru* se stejným názvem a ve stejném předplatném, skupině prostředků a oblasti. Chcete-li obnovit prostředek *clusteru,* postupujte podle kroku Vytvořit prostředek ** *clusteru.* **


## <a name="appendix"></a>Příloha

Application Insights customer managed key (CMK) je také podporována, i když byste měli zvážit následující změny, které vám pomohou naplánovat nasazení CMK pro komponenty Application Insight.

Log Analytics a Application Insights používají stejnou platformu úložiště dat a dotazovací stroj. Spojujeme tyto dva obchody prostřednictvím integrace Application Insights do Log Analytics, abychom poskytli jediné jednotné úložiště protokolů v rámci Azure Monitoru do druhého čtvrtletí
2020. Tato změna přinese vaše data Application Insight do pracovních prostorů Log Analytics a umožní dotazy, přehledy a další vylepšení, zatímco konfigurace CMK na vašem pracovním prostoru, se bude vztahovat také na vaše data Application Insights.

> [!NOTE]
> Pokud není nutné nasadit CMK pro vaše data Application Insight před integrací, doporučujeme počkat s Application Insights CMK, protože tato nasazení bude narušena integrací a budete muset překonfigurovat CMK po migraci do pracovního prostoru Log Analytics. Minimální hodnota 1 TB za den platí na úrovni clusteru a dokud nebude konsolidace dokončena během druhého čtvrtletí, vyžadují přehledy aplikací a analýza protokolů samostatné clustery.

## <a name="application-insights-cmk-configuration"></a>Konfigurace CMK aplikačních přehledů

Konfigurace Application Insights CMK je shodná s procesem znázorněný v tomto článku, včetně omezení a řešení potíží s výjimkou těchto kroků:

- Vytvoření *prostředku clusteru*
- Přidružení komponenty k prostředku *clusteru*

Při konfiguraci CMK pro Application Insights použijte tyto kroky namísto výše uvedených kroků.

### <a name="create-a-cluster-resource"></a>Vytvoření *prostředku clusteru*

Tento prostředek se používá jako zprostředkující připojení identity mezi trezorem klíčů a součástmi. Poté, co jste obdrželi potvrzení, že vaše předplatná byla zapsána na seznam povolených, vytvořte prostředek *clusteru* Log Analytics v oblasti, kde jsou umístěny vaše součásti. Typ prostředku *clusteru* je definován v době vytvoření nastavením *vlastnosti clusterType* na *LogAnalytics*nebo *ApplicationInsights*. Měl by být *ApplicationInsights* for Application Insights CMK. Nastavení *clusterType* nelze po konfiguraci změnit.

**Vytvořit**

Tento požadavek správce prostředků je asynchronní operace.

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

**Reakce**

200 OK a záhlaví.
Během období předčasného přístupu funkce clusteru ADX je zřízena ručně. Zatímco trvá zřizování poddx clusteru chvíli dokončit, můžete zkontrolovat stav zřizování dvěma způsoby:
1. Zkopírujte hodnotu adresy URL Azure-AsyncOperation z odpovědi a postupujte podle [kontroly stavu asynchronních operací](#asynchronous-operations-and-status-check).
2. Odešlete požadavek GET na prostředek *clusteru* a podívejte se na hodnotu *provisioningState.* Je *ProvisioningAccount* při zřizování a *úspěšné po* dokončení.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Přidružení komponenty k prostředku *clusteru* pomocí [rozhraní Components – vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) rozhraní API

K provedení této operace, která zahrnuje tyto akce, musíte mít oprávnění k zápisu pro komponentu i prostředek *clusteru:*

- V komponentě: Microsoft.Insights/component/write
- V *prostředku clusteru:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Tento krok by měl být proveden pouze po zřizování clusteru ADX. Pokud přidružíte komponenty a ingestovat data před zřizování, ingestovaná data budou zrušena a nebude obnovitelná.
> Chcete-li ověřit, zda je zřízen cluster ADX, spusťte prostředek *clusteru* Získat rozhraní REST API a zkontrolujte, zda je hodnota *provisioningState* *Succeeded*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Reakce**
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
> Zkopírujte a uchovávejte odpověď, protože ji budete potřebovat v dalších krocích.

**Přidružení komponenty**

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
"clusterDefinitionId" je hodnota "clusterId" poskytnutá v odpovědi z předchozího kroku.
"kind" příklad je "web".

**Reakce**

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
"clusterDefinitionId" je ID prostředku *clusteru,* které je přidruženo k této součásti.

Po přidružení jsou data odeslaná do komponent uložena zašifrovaná pomocí spravovaného klíče.
