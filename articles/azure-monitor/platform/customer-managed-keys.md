---
title: Azure Monitor konfiguraci klíče spravovaného zákazníkem
description: Informace a kroky konfigurace klíče spravovaného zákazníkem (CMK) k šifrování dat ve vašich Log Analyticsch pracovních prostorech pomocí Azure Key Vaultho klíče.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/07/2020
ms.openlocfilehash: d6419e86e1a541638a7053654bfcd7945aa41ae7
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891062"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor konfiguraci klíče spravovaného zákazníkem 

Tento článek poskytuje základní informace a kroky ke konfiguraci klíčů spravovaných zákazníkem (CMK) pro vaše pracovní prostory Log Analytics a Application Insights součásti. Po nakonfigurování se všechna data odesílaná do vašich pracovních prostorů šifrují pomocí klíče Azure Key Vault.

Před konfigurací doporučujeme zkontrolovat níže uvedená [omezení a omezení](#Limitations and constraints) .

## <a name="disclaimers"></a>Právní omezení

- Azure Monitor CMK je funkce předčasného přístupu a povolená pro registrovaná předplatná.

- Nasazení CMK popsané v tomto článku se dodává v produkční kvalitě a podporuje se, protože se jedná o funkci předčasného přístupu.

- Funkce CMK se doručuje na vyhrazený cluster úložiště dat, který je clusterem Azure Průzkumník dat (ADX), který je vhodný pro zákazníky, kteří odesílají 1 TB za den. 

- Cenové modely CMK nejsou momentálně dostupné a nejsou uvedené v tomto článku. V druhém čtvrtletí kalendářního roku (CY) 2020 se očekává cenový model vyhrazeného úložiště dat – cluster by se měl uplatňovat pro všechna existující nasazení CMK.

- Tento článek popisuje CMK konfiguraci pro pracovní prostory Log Analytics. CMK pro součásti Application Insights jsou také podporovány pomocí tohoto článku, zatímco rozdíly jsou uvedeny v příloze.

> [!NOTE]
> Log Analytics a Application Insights používají stejnou platformu pro úložiště dat a dotazovací stroj.
> Tyto dvě úložiště přinášíme společně prostřednictvím integrace Application Insights do Log Analytics k vytvoření jednoho sjednoceného úložiště protokolů v rámci Azure Monitor. Tato změna se plánuje pro druhé čtvrtletí kalendářního roku 2020. Pokud nemusíte nasazovat CMK pro vaše Application Insight data, doporučujeme počkat na dokončení konsolidace, protože tato nasazení budou přerušena konsolidací a po migraci na protokol bude nutné znovu nakonfigurovat CMK. Pracovní prostor analýzy. Minimálně 1 TB za den se vztahuje na úrovni clusteru a až do dokončení konsolidace během druhého čtvrtletí Application Insights a Log Analytics vyžadují samostatné clustery.

## <a name="customer-managed-key-cmk-overview"></a>CMK (Customer-Managed Key) – přehled

[Šifrování v klidovém umístění](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) je běžným požadavkem na ochranu osobních údajů a zabezpečení v organizacích. Azure vám umožní plně spravovat šifrování v klidovém režimu, zatímco máte k dispozici různé možnosti, jak pečlivě spravovat šifrovací a šifrovací klíče.

Azure Monitor data-Store zajišťuje, aby všechna zašifrovaná data byla v klidovém stavu pomocí klíčů spravovaných Azure při uložení v Azure Storage. Azure Monitor taky nabízí možnost šifrování dat pomocí vlastního klíče, který je uložený v [trezorech klíčů Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview), ke kterým se přistupovalo pomocí [spravovaného ověřování identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) přiřazené systémem. Tento klíč může být buď [software, nebo hardware – chráněný](https://docs.microsoft.com/azure/key-vault/key-vault-overview)modulem HSM.
Azure Monitor použití šifrování je stejné jako způsob, jakým [Azure Storage šifrování](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) funguje.

Frekvence, kterou Azure Monitor přístup úložiště Key Vault pro zabalení a rozbalení operací je mezi 6 až 60 sekund. Azure Monitor úložiště  
vždy respektuje změny v klíčových oprávněních během hodiny.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak CMK funguje v Azure Monitor

Azure Monitor využívá spravovanou identitu přiřazenou systémem k udělení přístupu k vašemu Azure Key Vault. Spravovaná identita přiřazená systémem se dá přidružit jenom k jednomu prostředku Azure. Identita Azure Monitorho úložiště dat (ADX) je podporovaná na úrovni clusteru a tím se určuje, že se funkce CMK doručí ve vyhrazeném clusteru ADX. Aby bylo možné podporovat CMK ve více pracovních prostorech, nový prostředek Log Analytics (*cluster*) provádí jako zprostředkující připojení identity mezi Key Vault a vašimi pracovními prostory Log Analytics. Tento pojem odpovídá omezení identity přiřazené systémem a identita se udržuje mezi clusterem ADX a prostředkem *clusteru* Log Analytics *,* zatímco data všech přidružených pracovních prostorů jsou chráněná pomocí vašeho Key Vault klíče. Úložiště clusteru Underlay ADX používá spravovanou identitu, kterou\'s přidruženou ke *clusteru* prostředků k ověřování a přístup k Azure Key Vault prostřednictvím Azure Active Directory.

![CMK – přehled](media/customer-managed-keys/cmk-overview-8bit.png)

## <a name="encryption-keys-management"></a>Správa šifrovacích klíčů

Šifrování dat úložiště má tři typy klíčů:

- Šifrovací klíč **KEK** -key v Key Vault (CMK)
- **AEK** šifrovací klíč účtu
- **Klíč DEK** – šifrovací klíč dat

Platí následující pravidla:

- Účet úložiště ADX vygeneruje jedinečný šifrovací klíč pro každý účet úložiště, který se označuje jako AEK.

- AEK se používá k odvození DEKs, což jsou klíče, které slouží k zašifrování každého bloku dat zapsaných na disk.

- Když nakonfigurujete klíč v Key Vault a odkazujete na něj v prostředku *clusteru* , Azure Storage ZAbalí AEK do KEK v Azure Key Vault.

- Vaše KEK nikdy neopustí vaše Key Vault a v případě klíče HSM nikdy neopustí hardware.

- Azure Storage používá spravovanou identitu, která je přidružená k prostředku *clusteru* pro ověřování a přístup k Azure Key Vault prostřednictvím Azure Active Directory.

- Pro operace čtení a zápisu Azure Storage posílá žádosti, aby Azure Key Vault zabalit a rozbalí AEK a prováděla tak operace šifrování a dešifrování.

## <a name="cmk-provisioning-procedure"></a>Postup zřizování CMK

Proces zřizování zahrnuje tyto kroky:

1. Seznam povolených odběrů – to je vyžadováno pro tuto funkci předčasného přístupu
2. Vytváření Azure Key Vault a ukládání klíče
3. Vytvoření prostředku *clusteru*
4. Udělení oprávnění vašemu Key Vault
5. Zřizování úložiště dat Azure Monitor (ADX)
6. Log Analytics přidružení pracovních prostorů

Procedura není momentálně v uživatelském rozhraní podporovaná a proces zřizování se provádí prostřednictvím REST API.

> [!IMPORTANT]
> Jakýkoli požadavek rozhraní API musí v hlavičce požadavku zahrnovat autorizační token nosiče.

Příklad:

```rst
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2015-11-01-preview]
  authorization: Bearer eyJ0eXAiO....
```

kde *eyJ0eXAiO....* představuje úplný autorizační token. 

Token můžete získat pomocí jedné z těchto metod:

1. Použijte metodu [Registrace aplikací](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) .

2. Na webu Azure Portal
    1. Přejít na Azure Portal v nástroji pro vývojáře (F12)
    1. V části "Batch? rozhraní API-Version" vyhledejte autorizační řetězec v části "hlavičky žádosti". Vypadá to, že: "Authorization: nosič \<token\>". 
    1. Zkopírujte a přidejte ho do volání rozhraní API podle níže uvedených příkladů.

3. Přejděte na stránku dokumentace k Azure REST. V jakémkoli rozhraní API stiskněte tlačítko vyzkoušet a zkopírujte token nosiče.

### <a name="subscription-whitelisting"></a>Seznam povolených odběrů

Možnost CMK je funkce předčasného přístupu. K předplatným, ke kterým plánujete vytváření prostředků *clusteru* , musí mít předem povolený produkt skupina produktů Azure. Pomocí kontaktů do Microsoftu poskytněte ID předplatných.

> [!WARNING]
> Možnost CMK je regionální. Vaše Azure Key Vault, účet úložiště, prostředek *clusteru* a přidružené Log Analytics pracovní prostory musí být ve stejné oblasti, ale mohou být v různých předplatných.

### <a name="storing-encryption-key-kek"></a>Ukládání šifrovacího klíče (KEK)

Vytvořte prostředek Azure Key Vault a pak vygenerujte nebo importujte klíč, který se použije k šifrování dat.

Aby bylo možné klíč a přístup k datům Azure Monitor chránit, musí být Azure Key Vault nakonfigurovaný jako obnovitelný.

[Zapnutí možností obnovení](https://docs.microsoft.com/azure/key-vault/key-vault-best-practices#turn-on-recovery-options):
- Je nutné zapnout [obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) .
- Pro ochranu proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění by měla být zapnutá ochrana vyprázdnění.

### <a name="create-cluster-resource"></a>Vytvořit prostředek *clusteru*

Tento prostředek se používá jako zprostředkující připojení identity mezi vaším Key Vault a vašimi pracovními prostory. Až se vám zobrazí potvrzení, že vaše předplatná byla povolená, vytvořte prostředek *clusteru* Log Analytics v oblasti, ve které se vaše pracovní prostory nacházejí.

**Vytvoření**

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "region-name",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```

**Odpověď**

Identita je přiřazena ke *clusteru* v okamžiku vytvoření.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id" //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```

Pokud chcete prostředek *clusteru* odstranit z jakéhokoli důvodu (například ho vytvořit s jiným názvem), použijte toto volání rozhraní API:

```
DELETE
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Udělení oprávnění Key Vault

Aktualizujte Key Vault a přidejte zásady přístupu pomocí oprávnění Get, Wrap Key a Unwrap Key k ID prostředku *clusteru* nebo názvu prostředku *clusteru* . Tato oprávnění se rozšíří do Azure Monitor úložiště.

![Udělení oprávnění Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Aby bylo možné ověřit, jestli je vaše Key Vault nakonfigurovaná tak, aby chránila váš klíč a přístup k datům Azure Monitor, je potřeba oprávnění *získat* .

Bude trvat několik minut, než se prostředek *clusteru* rozšíří do Azure Resource Manager. Když nakonfigurujete tuto zásadu přístupu hned po vytvoření prostředku *clusteru* , může dojít k přechodné chybě. V takovém případě zkuste to znovu za několik minut.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizace prostředku clusteru s podrobnostmi identifikátoru klíče

Když vytváříte novou verzi klíče, musíte aktualizovat prostředek clusteru s Azure Key Vault podrobností o identifikátoru klíče, aby úložiště Azure Monitor mohlo používat novou verzi. Pokud chcete získat identifikátor klíče, vyberte aktuální verzi klíče v Azure Key Vault:

![Udělení oprávnění Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aktualizujte prostředek *clusteru* KeyVaultProperties s podrobnostmi identifikátoru klíče.

**Aktualizace**

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": { //Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
            },
   },
   "location":"region-name",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```

**Odpověď**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
       "KeyVaultProperties": {     // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name" //Example: Switzerland North
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Zřizování úložiště dat Azure Monitor (cluster ADX)

Během období předčasného přístupu k této funkci cluster ADX zřídí produktový tým ručně po dokončení předchozích kroků. Pomocí kanálu, který máte s Microsoftem, zadejte následující podrobnosti:

1. Potvrzení, že výše uvedené kroky jsou dokončeny

2. ID prostředku *clusteru* , které jste dostali v odpovědi, vypadá takto:

```
"id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
```

ID prostředku *clusteru* lze kdykoli získat pomocí volání metody Get rozhraní API.

**Přečtěte si ID prostředku *clusteru* .**

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**Odpověď**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-Id"
  },
  "properties": {
       "KeyVaultProperties": { // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
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

> [!NOTE]
> Tento krok je **potřeba provést až** po přijetí potvrzení ze skupiny produktů prostřednictvím kanálu Microsoft, že **zřízení Azure Monitorho úložiště dat (ADX cluster)** bylo splněné. Pokud přidružíte pracovní prostory a ingestování dat před tímto **zřizováním**, budou data vyřazena a nebude obnovitelné.

**Přidružení pracovního prostoru k prostředku *clusteru***

```json
PUT https://management.azure.com.resources.windows-int.net/Customer.svc/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name} 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "source": "Azure",
    "customerId": {workspace-id}, //Available in Azure portal under Log Analytics workspace Overview section
    "features": {
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    }
  },
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

**Odpověď**

```
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-id",
    "retentionInDays": value,
    "features": {
      "legacy": value,
      "searchVersion": value,
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    },
    "workspaceCapping": {
      "dailyQuotaGb": value,
      "quotaNextResetTime": "timeStamp",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}

```

Po přidružení se data odesílaná do vašich pracovních prostorů ukládají zašifrovaný pomocí spravovaného klíče.

## <a name="cmk-kek-revocation"></a>Odvolání CMK (KEK)

Služba Azure Monitor Storage bude vždy respektovat změny klíčových oprávnění během celé hodiny, normálně dřív a úložiště nebude k dispozici – veškerá data přijímaná do pracovních prostorů přidružených k vašemu prostředku *clusteru* budou zahozena a dotazy selžou. Dříve přijímaná data zůstávají v Azure Monitor úložišti nepřístupná, pokud je váš klíč odvolaný a vaše pracovní prostory nebudou smazány. Nepřístupná data se řídí zásadami uchovávání dat a při dosažení doby uchování se odstraní.

Úložiště se bude pravidelně dotazovat na Key Vault a pokusí se o rozbalení šifrovacího klíče a po jeho použití, příjmu dat a obnovení dotazů do 30 minut.

## <a name="cmk-kek-rotation"></a>Rotace CMK (KEK)

Rotace CMK vyžaduje explicitní aktualizaci prostředku clusteru s novou verzí Azure Key Vault klíče. Pokud chcete Azure Monitor aktualizovat s novou verzí klíče, postupujte podle pokynů v kroku aktualizace prostředku *clusteru* s podrobnostmi identifikátoru klíče.

-   Pokud svůj klíč otočíte v Key Vault a neaktualizujete novou verzi v Azure Monitor krátce po, klíč nebude přístupný Azure Monitor Storage.

## <a name="limitations-and-constraints"></a>Omezení a omezení

- Funkce CMK je podporovaná na úrovni clusteru ADX a vyžaduje vyhrazený cluster Azure Monitor ADX.

- Maximální počet prostředků *clusteru* na předplatné je omezený na 5.

- Přidružení prostředků *clusteru* k pracovnímu prostoru by se mělo provádět až po obdržení potvrzení ze skupiny produktů, kterou bylo zřizování clusteru ADX. Data, která se odešlou před tímto zřizováním, se vynechá a nebudou se moct obnovit.

- Šifrování CMK se vztahuje na nově ingestovaná data po konfiguraci CMK. Data, která byla ingestovaná před konfigurací CMK, zůstala zašifrovaná pomocí klíče Microsoft Key. Můžete zadat dotaz na data před a po bezproblémové konfiguraci.

- Funkce CMK je oblastní – vaše Azure Key Vault, prostředek *clusteru* a přidružené pracovní prostory musí být ve stejné oblasti, ale můžou být v různých předplatných.

- Když je pracovní prostor přidružený k prostředku *clusteru* , nedá se z prostředku *clusteru* zrušit jeho přidružení, protože data se šifrují pomocí klíče a nejsou dostupná bez KEK v Azure Key Vault.

- Azure Key Vault musí být nakonfigurované jako obnovitelné. Tyto vlastnosti nejsou ve výchozím nastavení povolené:

  - [Obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) je zapnuté.
  - Možnost Nemazat je zapnutá pro ochranu proti vynucenému odstranění tajného klíče nebo trezoru i po obnovitelném odstranění.

- Prostředek *clusteru* přesunout do jiné skupiny prostředků nebo předplatného se momentálně nepodporuje.

- Přidružení pracovního prostoru k prostředku *clusteru* selže, pokud je prostředek *clusteru* v jiném tenantovi.

-   Přidružení pracovního prostoru ke zdroji *clusteru* selže, pokud je přidruženo k jinému prostředku *clusteru* .

## <a name="troubleshooting-and-management"></a>Řešení potíží a Správa

- Dostupnost Key Vault
    - V běžném provozu mezipamětí úložiště AEK pro krátkou dobu se pravidelně vrací na Key Vault k rozbalení.
    
    - Přechodné chyby připojení Úložiště zpracovává přechodné chyby (vypršení časového limitu, selhání připojení, problémy se službou DNS) tím, že klíče zůstanou v mezipaměti po krátké době delší a to přináší všechny malé výkyvůy v rámci dostupnosti dotazů, které jsou k dispozici bez přerušení.
            -Ingestování pokračuje bez přerušení.
    
    - Při nedostupnosti živého webu asi 30 minut dojde k nedostupnosti účtu úložiště.
            funkce **dotazu** -   není k dispozici **– ingestování – data** se ukládají do mezipaměti několik hodin pomocí klávesy Microsoft, aby se předešlo ztrátě dat. Po obnovení přístupu k Key Vault se dotaz zpřístupní a dočasná data uložená v mezipaměti se ingestují do úložiště dat a zašifrují pomocí CMK.

- Pokud vytvoříte prostředek *clusteru* a okamžitě zadáte KeyVaultProperties, operace může selhat, protože zásady přístupu nejde definovat, dokud není systémová identita přiřazená k prostředku *clusteru* .

- Pokud aktualizujete existující prostředek *clusteru* s KeyVaultProperties a zásada přístupu pro klíč "Get" v Key Vault chybí, operace se nezdaří.

- Pokud se pokusíte odstranit prostředek *clusteru* , který je přidružen k pracovnímu prostoru, operace odstranění selže.

- Získat všechny clustery pro skupinu prostředků:

    ```
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
    ```
    
    *Odpověď*

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
             "KeyVaultProperties": { // Key Vault key identifier
                KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
                KeyName: {key-name},
                KeyVersion: {current-version}
                },
            "provisioningState": "Succeeded",
            "clusterType": "LogAnalytics", 
            "clusterId": "cluster-id"
          },
          "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
          "name": "cluster-name",
          "type": "Microsoft.OperationalInsights/clusters",
          "location": "region-name"
        }
      ]
    }
    ```

- Získání všech clusterů pro předplatné

    ```
    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
    ```
    
    *Odpověď*
    
    Stejné jako odpověď z pro všechny clustery pro skupinu prostředků, ale v oboru předplatného.
    
- Odstranění prostředku *clusteru* :

> Aby bylo možné odstranit *clusterový* prostředek, musíte odstranit všechny přidružené pracovní prostory:
>
> DELETE https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
>

Odpověď

200 OK

## <a name="appendix"></a>Příloha

Tento článek se vztahuje také na Application Insights spravovaný klíč zákazníka (CMK), i když byste měli vzít v úvahu nadcházející změnu, která vám pomůžete naplánovat nasazení CMK pro komponenty Insight Application Insights.

Log Analytics a Application Insights používají stejnou platformu pro úložiště dat a dotazovací stroj – tyto dvě úložiště přinášíme společně prostřednictvím integrace Application Insights do Log Analytics k poskytnutí jediného úložiště Unified Logs v oblasti Azure Monitor druhé čtvrtletí z
2020. Tato změna přinese data do vašich aplikací do Log Analytics pracovních prostorů a provede dotazy, přehledy a další vylepšení, které je možné použít i v případě, že se konfigurace CMK v pracovním prostoru vztahuje i na vaše Application Insights data.

> [!NOTE]
> Pokud nemusíte nasazovat CMK na data přehledu vaší aplikace před druhou čtvrti kr 2020, doporučujeme počkat na dokončení konsolidace, protože taková nasazení budou přerušena konsolidací a budete muset CMK znovu nakonfigurovat na pracovní prostor za ním.

## <a name="application-insights-cmk-configuration"></a>Konfigurace Application Insights CMK

Konfigurace Application Insights CMK je stejná jako proces, který je znázorněný v tomto článku, včetně omezení a řešení potíží s výjimkou těchto kroků:

- Vytvořit prostředek *clusteru*

- Přidružení součásti k prostředku *clusteru*

Při konfiguraci CMK pro Application Insights použijte tento postup namísto těch, které jsou uvedeny výše.

### <a name="create-a-cluster-resource"></a>Vytvoření prostředku *clusteru*

Tento prostředek se používá jako zprostředkující připojení identity mezi vaším Key Vault a vašimi komponentami. AŽ obdržíte potvrzení, že vaše předplatná byla povolená, vytvořte prostředek clusteru Log Analytics v oblasti, ve které jsou umístěné vaše komponenty. Typ prostředku clusteru je definován při vytvoření nastavením vlastnosti *clusterType* na hodnotu *LogAnalytics*nebo *ApplicationInsights*. Měl by být *ApplicationInsights* pro Application Insights CMK. Nastavení *clusterType* nelze po konfiguraci změnit.

Vytváření:

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "region-name",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

Odpověď:

Identita je přiřazena ke clusteru v okamžiku vytvoření.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id" //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="associate-a-component-to-a-cluster-resource"></a>Přidružení součásti k prostředku clusteru

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Insights/components/{component-name}?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id" //The id of the cluster resource
  },
  "location": "region-name",
  "kind": "component-type",
}
```

Odpověď

```json
{
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.insights/components/{component-name}",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id" //The id of the cluster resource
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

Po přidružení se data odesílaná do vašich komponent ukládají zašifrovaný pomocí spravovaného klíče.
