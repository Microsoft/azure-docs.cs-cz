---
title: Vysazení minimální požadované verze protokolu TLS (Transport Layer Security) pro příchozí požadavky
titleSuffix: Azure Storage
description: Nakonfigurujte účet úložiště tak, aby vyžadoval minimální verzi protokolu TLS (Transport Layer Security) pro klienty, kteří žádají o Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 3a44466f04e598080662599e785eb71698265f87
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592337"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Vynutila minimální požadovanou verzi protokolu TLS (Transport Layer Security) pro požadavky na účet úložiště.

Komunikace mezi klientskou aplikací a účtem Azure Storage je zašifrovaná pomocí protokolu TLS (Transport Layer Security). TLS je standardní kryptografický protokol, který zajišťuje ochranu osobních údajů a integritu dat mezi klienty a službami přes Internet. Další informace o TLS najdete v tématu [zabezpečení transportní vrstvy](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Azure Storage aktuálně podporuje tři verze protokolu TLS: 1,0, 1,1 a 1,2. Azure Storage používá TLS 1,2 u veřejných koncových bodů HTTPS, ale TLS 1,0 a TLS 1,1 se pořád podporuje kvůli zpětné kompatibilitě.

Ve výchozím nastavení Azure Storage účty umožňují klientům odesílat a přijímat data pomocí nejstarší verze TLS, TLS 1,0 a vyšší. Abyste vynutili přísnější bezpečnostní opatření, můžete nakonfigurovat účet úložiště tak, aby vyžadoval, aby klienti odesílali a přijímali data pomocí novější verze TLS. Pokud účet úložiště vyžaduje minimální verzi TLS, nebudou všechny požadavky vytvořené se staršími verzemi úspěšné.

V tomto článku se dozvíte, jak pomocí architektury přetáhnout (detekce a napravit audit-audit) průběžně spravovat zabezpečený protokol TLS pro vaše účty úložiště.

Informace o tom, jak zadat konkrétní verzi TLS při odesílání žádosti z klientské aplikace, najdete v tématu [Konfigurace TLS (Transport Layer Security) pro klientskou aplikaci](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Zjištění verze protokolu TLS používaných klientskými aplikacemi

Když vynutili minimální verzi protokolu TLS pro váš účet úložiště, riskujete zamítnutí žádostí od klientů, kteří odesílají data pomocí starší verze TLS. Aby bylo možné pochopit, jak konfigurace minimální verze protokolu TLS může ovlivnit klientské aplikace, společnost Microsoft doporučuje povolit protokolování pro účet Azure Storage a analyzovat protokoly po časovém intervalu, aby bylo možné zjistit, jaké verze klientských aplikací TLS používají.

K protokolování požadavků na účet Azure Storage a určení verze TLS, kterou klient používá, můžete použít Azure Storage přihlášení Azure Monitor (Preview). Další informace najdete v tématu [monitorování Azure Storage](../blobs/monitor-blob-storage.md).

Azure Storage přihlášení Azure Monitor podporuje použití dotazů protokolu k analýze dat protokolu. K dotazování protokolů můžete použít pracovní prostor Azure Log Analytics. Další informace o dotazech protokolu najdete v tématu [kurz: Začínáme s Log Analytics dotazy](../../azure-monitor/logs/log-analytics-tutorial.md).

Pokud chcete protokolovat data Azure Storage pomocí Azure Monitor a analyzovat je pomocí Azure Log Analytics, musíte nejdřív vytvořit nastavení diagnostiky, které indikuje, jaké typy požadavků a pro které služby úložiště chcete data protokolovat. Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Chcete-li v Azure Portal vytvořit nastavení diagnostiky, postupujte takto:

1. Vytvořte nový pracovní prostor Log Analytics v předplatném, které obsahuje váš Azure Storage účet. Po nakonfigurování protokolování účtu úložiště budou protokoly k dispozici v pracovním prostoru Log Analytics. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../azure-monitor/logs/quick-create-workspace.md).
1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části monitorování vyberte **nastavení diagnostiky (Preview)**.
1. Vyberte službu Azure Storage, pro kterou chcete protokolovat požadavky. Například vyberte **objekt BLOB** , který bude protokolovat požadavky do úložiště objektů BLOB.
1. Vyberte **Přidat nastavení diagnostiky**.
1. Zadejte název pro nastavení diagnostiky.
1. V části **Podrobnosti o kategorii** v části **protokol** vyberte typy požadavků, které se mají protokolovat. Můžete protokolovat požadavky na čtení, zápis a odstranění. Například volba **StorageRead** a **StorageWrite** bude protokolovat požadavky na čtení a zápis do vybrané služby.
1. V části **Podrobnosti o cíli** vyberte **Odeslat do Log Analytics**. Vyberte své předplatné a Log Analytics pracovní prostor, který jste vytvořili dříve, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Snímek obrazovky ukazující, jak vytvořit nastavení diagnostiky pro požadavky protokolování":::

Po vytvoření nastavení diagnostiky se požadavky na účet úložiště následně protokolují podle tohoto nastavení. Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik prostředků v Azure](../../azure-monitor/essentials/diagnostic-settings.md).

Odkaz na pole, která jsou k dispozici v Azure Storage protokoly v Azure Monitor, najdete v tématu [protokoly prostředků (Preview)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Dotazy protokolovaných požadavků podle verze TLS

Protokoly Azure Storage v Azure Monitor zahrnují verzi TLS používanou k odeslání požadavku účtu úložiště. K ověření verze protokolu TLS protokolovaných požadavků použijte vlastnost **TlsVersion** .

Pokud chcete zjistit, kolik požadavků bylo v úložišti objektů BLOB s různými verzemi TLS za posledních sedm dní, otevřete pracovní prostor Log Analytics. Dále vložte následující dotaz do nového dotazu protokolu a spusťte jej. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Ve výsledcích se zobrazuje počet požadavků provedených v každé z verzí TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Snímek obrazovky zobrazující výsledky dotazu Log Analytics pro vrácení verze TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Dotazování protokolovaných požadavků podle IP adresy volajícího a hlavičky uživatelského agenta

Protokoly Azure Storage v Azure Monitor také zahrnují IP adresu volajícího a hlavičku uživatelského agenta, které vám pomůžou vyhodnotit, které klientské aplikace přistupovaly k účtu úložiště. Tyto hodnoty můžete analyzovat a rozhodnout se, jestli se klientské aplikace musí aktualizovat tak, aby používaly novější verzi TLS, nebo jestli je přijatelné, pokud není žádost klienta v případě, že se odešle s minimální verzí protokolu TLS, nezdařila.

Chcete-li určit, kteří klienti požádali o verzi TLS starší než TLS 1,2 za posledních 7 dnů, vložte následující dotaz do nového dotazu protokolu a spusťte jej. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Napravit rizika zabezpečení s minimální verzí TLS

Pokud jste si jisti, že provoz od klientů používajících starší verze protokolu TLS je minimální nebo je přijatelný pro neúspěšné požadavky se starší verzí TLS, můžete začít vynucovat minimální verzi protokolu TLS v účtu úložiště. Vyžadování, aby klienti používali minimální verzi TLS k tomu, aby byly požadavky na účet úložiště součástí strategie pro minimalizaci rizik zabezpečení vašich dat.

> [!IMPORTANT]
> Pokud používáte službu, která se připojuje k Azure Storage, zajistěte, aby tato služba používala příslušnou verzi protokolu TLS, aby odesílala žádosti do Azure Storage předtím, než nastavíte požadovanou minimální verzi pro účet úložiště.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Konfigurace minimální verze protokolu TLS pro účet úložiště

Pokud chcete nakonfigurovat minimální verzi TLS pro účet úložiště, nastavte pro tento účet verzi **MinimumTlsVersion** . Tato vlastnost je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Další informace o modelu nasazení Azure Resource Manager najdete v tématu [Přehled účtu úložiště](storage-account-overview.md).

Vlastnost **MinimumTlsVersion** není nastavena ve výchozím nastavení a nevrací hodnotu, dokud ji explicitně nenastavíte.  Pokud má vlastnost hodnotu **null**, bude účet úložiště umožňovat požadavky odeslané protokolem TLS verze 1,0 nebo vyšší.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Když vytvoříte účet úložiště s Azure Portal, je minimální verze protokolu TLS ve výchozím nastavení nastavená na 1,2.

Pokud chcete nakonfigurovat minimální verzi TLS pro existující účet úložiště pomocí Azure Portal, postupujte takto:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. Vyberte nastavení **Konfigurace** .
1. V části **Minimální verze protokolu TLS** pomocí rozevíracího seznamu vyberte minimální verzi TLS potřebnou pro přístup k datům v tomto účtu úložiště, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat minimální verzi TLS v Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete nakonfigurovat minimální verzi TLS pro účet úložiště pomocí PowerShellu, nainstalujte [Azure PowerShell verze 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) nebo novější. Dále nakonfigurujte vlastnost **MinimumTLSVersion** pro nový nebo existující účet úložiště. Platné hodnoty pro **MinimumTlsVersion** jsou `TLS1_0` , `TLS1_1` a `TLS1_2` .

Následující příklad vytvoří účet úložiště a nastaví **MinimumTLSVersion** na TLS 1,1, potom tento účet aktualizuje a nastaví **MinimumTLSVersion** na TLS 1,2. Příklad také načte hodnotu vlastnosti v každém případě. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nakonfigurovat minimální verzi TLS pro účet úložiště pomocí Azure CLI, nainstalujte Azure CLI verze 2.9.0 nebo novější. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Dále nakonfigurujte vlastnost **minimumTlsVersion** pro nový nebo existující účet úložiště. Platné hodnoty pro **minimumTlsVersion** jsou `TLS1_0` , `TLS1_1` a `TLS1_2` .

Následující příklad vytvoří účet úložiště a nastaví **minimumTLSVersion** na TLS 1,1. Pak aktualizuje účet a nastaví vlastnost **minimumTLSVersion** na TLS 1,2. Příklad také načte hodnotu vlastnosti v každém případě. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Šablona](#tab/template)

Pokud chcete nakonfigurovat minimální verzi TLS pro účet úložiště se šablonou, vytvořte šablonu s vlastností **MinimumTLSVersion** nastavenou na `TLS1_0` , `TLS1_1` nebo `TLS1_2` . Následující postup popisuje, jak vytvořit šablonu v Azure Portal.

1. V Azure Portal klikněte na možnost **vytvořit prostředek**.
1. V **části Hledat na Marketplace** zadejte **šablonu Deployment** a potom stiskněte **ENTER**.
1. Zvolte **template Deployment (nasadit pomocí vlastních šablon) (Preview)**, zvolte **vytvořit** a potom **v editoru zvolte sestavit vlastní šablonu**.
1. V editoru šablon vložte následující JSON pro vytvoření nového účtu a nastavte minimální verzi TLS na TLS 1,2. Nezapomeňte nahradit zástupné symboly v lomených závorkách vlastními hodnotami.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Uložte šablonu.
1. Zadejte parametr skupiny prostředků a potom kliknutím na tlačítko **Revize + vytvořit** nasaďte šablonu a vytvořte účet úložiště s nakonfigurovanou vlastností **MinimumTLSVersion** .

---

> [!NOTE]
> Po aktualizaci minimální verze protokolu TLS pro účet úložiště může trvat až 30 sekund, než se změna zcela rozšíří.

Konfigurace minimální verze protokolu TLS vyžaduje Azure Storage poskytovatele prostředků verze 2019-04-01 nebo novější. Další informace najdete v tématu [REST API poskytovatele prostředků Azure Storage](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Ověřte minimální požadovanou verzi TLS pro víc účtů

Pokud chcete ověřit minimální potřebnou verzi TLS v rámci sady účtů úložiště s optimálním výkonem, můžete použít Průzkumníka Azure Resource graphu v Azure Portal. Další informace o používání Průzkumníka grafů prostředků najdete v tématu [rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../../governance/resource-graph/first-query-portal.md).

Když spustíte následující dotaz, v Průzkumníku grafu prostředků se vrátí seznam účtů úložiště a pro každý účet se zobrazí minimální verze protokolu TLS:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Otestování minimální verze protokolu TLS z klienta

Pokud chcete otestovat, jestli minimální požadovaná verze TLS pro účet úložiště neumožňuje volání se starší verzí, můžete nakonfigurovat klienta tak, aby používal starší verzi TLS. Další informace o konfiguraci klienta pro použití konkrétní verze TLS najdete v tématu [Konfigurace TLS (Transport Layer Security) pro klientskou aplikaci](transport-layer-security-configure-client-version.md).

Když klient přistupuje k účtu úložiště pomocí verze TLS, která nesplňuje požadavek na minimální verzi TLS nakonfigurovanou pro tento účet, Azure Storage vrátí chybu kód chyby 400 (chybný požadavek) a zprávu oznamující, že použitá verze protokolu TLS není povolená pro vytváření žádostí na tento účet úložiště.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Auditovat dodržování předpisů pomocí Azure Policy

Pokud máte velký počet účtů úložiště, možná budete chtít provést audit, abyste se ujistili, že jsou všechny účty nakonfigurované pro minimální verzi TLS, kterou vaše organizace vyžaduje. Pokud chcete auditovat sadu účtů úložiště pro dodržování předpisů, použijte Azure Policy. Azure Policy je služba, kterou můžete použít k vytváření, přiřazování a správě zásad, které používají pravidla pro prostředky Azure. Azure Policy vám pomůže zajistit, aby tyto prostředky vyhovovaly vašim firemním standardům a smlouvám o úrovni služeb. Další informace najdete v tématu [přehled Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Vytvoření zásady pomocí efektu auditu

Azure Policy podporuje efekty, které určují, co se stane, když se pravidlo zásad vyhodnocuje proti prostředku. Při auditu se vytvoří upozornění, pokud prostředek není v souladu s předpisy, ale nezastaví požadavek. Další informace o účincích najdete v tématu [pochopení Azure Policy efektů](../../governance/policy/concepts/effects.md).

Pokud chcete vytvořit zásadu s účinkem auditu pro minimální verzi TLS s Azure Portal, postupujte takto:

1. V Azure Portal přejděte do služby Azure Policy.
1. V části **vytváření obsahu** vyberte **definice**.
1. Pokud chcete vytvořit novou definici zásady, vyberte **Přidat definici zásady** .
1. V poli **umístění definice** vyberte tlačítko **Další** a určete, kde se nachází prostředek zásad auditu.
1. Zadejte název zásady. Volitelně můžete zadat popis a kategorii.
1. V části **pravidlo zásad** přidejte do části **policyRule** následující definici zásady.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Uložte zásady.

### <a name="assign-the-policy"></a>Přiřazení zásady

V dalším kroku přiřaďte zásadu k prostředku. Rozsah zásad odpovídá tomuto prostředku a jakýmkoli prostředkům pod ním. Další informace o přiřazení zásad najdete v tématu [Azure Policy struktura přiřazení](../../governance/policy/concepts/assignment-structure.md).

Chcete-li přiřadit zásadu k Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal přejděte do služby Azure Policy.
1. V části **vytváření obsahu** vyberte **přiřazení**.
1. Vyberte **přiřadit zásadu** a vytvořte nové přiřazení zásady.
1. V poli **obor** vyberte obor přiřazení zásady.
1. V poli **definice zásady** vyberte tlačítko **Další** a pak v seznamu vyberte zásadu, kterou jste definovali v předchozí části.
1. Zadejte název přiřazení zásady. Popis je volitelný.
1. Nechte **vynucení zásad** nastavené na *povoleno*. Toto nastavení nemá žádný vliv na zásady auditu.
1. Vyberte možnost **zkontrolovat + vytvořit** a vytvořte přiřazení.

### <a name="view-compliance-report"></a>Zobrazit sestavu dodržování předpisů

Po přiřazení zásady můžete zobrazit sestavu dodržování předpisů. Sestava dodržování předpisů pro zásady auditu poskytuje informace o tom, které účty úložiště nejsou v souladu se zásadami. Další informace najdete v tématu [získání dat o dodržování zásad](../../governance/policy/how-to/get-compliance-data.md).

Po vytvoření přiřazení zásad může trvat několik minut, než se sestava dodržování předpisů stane dostupnou.

Chcete-li zobrazit sestavu dodržování předpisů v Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal přejděte do služby Azure Policy.
1. Vyberte možnost **dodržování předpisů**.
1. Vyfiltrujte výsledky pro název přiřazení zásady, které jste vytvořili v předchozím kroku. V této sestavě se zobrazuje počet prostředků, které nejsou v souladu se zásadami.
1. Můžete přejít k podrobnostem sestavy, kde najdete další podrobnosti, včetně seznamu účtů úložiště, které nedodržují předpisy.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Snímek obrazovky zobrazující sestavu dodržování předpisů pro zásady auditu pro minimální verzi TLS":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Použití Azure Policy k vymáhání minimální verze protokolu TLS

Azure Policy podporuje zásady správného řízení cloudu tím, že zajistí, že prostředky Azure vyhovují požadavkům a standardům. Abyste vynutili minimální požadavek na verzi TLS pro účty úložiště ve vaší organizaci, můžete vytvořit zásadu, která zabrání vytvoření nového účtu úložiště, který nastaví minimální požadavek TLS na starší verzi TLS, než který je vydaný zásadami. Tato zásada taky zabrání všem změnám v konfiguraci existujícího účtu, pokud minimální nastavení verze TLS pro tento účet není kompatibilní se zásadami.

Zásada vynucení používá efekt odepřít, který brání žádosti, která by vytvořila nebo upravila účet úložiště, aby minimální verze protokolu TLS již neodpovídala standardům vaší organizace. Další informace o účincích najdete v tématu [pochopení Azure Policy efektů](../../governance/policy/concepts/effects.md).

Chcete-li vytvořit zásadu s efektem odepření pro minimální verzi protokolu TLS, která je menší než TLS 1,2, postupujte podle kroků popsaných v tématu [použití Azure Policy k auditování dodržování předpisů](#use-azure-policy-to-audit-for-compliance), ale v části **policyRule** v definici zásady zadejte následující kód JSON:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Když vytvoříte zásadu s použitím efektu odepřít a přiřadíte ji k oboru, nemůže uživatel vytvořit účet úložiště s minimální verzí protokolu TLS, která je starší než 1,2. Ani nemůže uživatel provádět změny konfigurace stávajícího účtu úložiště, který aktuálně vyžaduje minimální verzi protokolu TLS, která je starší než 1,2. Při pokusu o provedení dojde k chybě. Požadovaná minimální verze TLS pro účet úložiště musí být nastavená na 1,2, aby bylo možné pokračovat v vytváření nebo konfiguraci účtu.

Následující obrázek ukazuje chybu, ke které dochází, když se pokusíte vytvořit účet úložiště s minimální verzí TLS nastavenou na TLS 1,0 (výchozí pro nový účet), když zásada s efektem odepření vyžaduje, aby se minimální verze protokolu TLS nastavila na TLS 1,2.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Snímek obrazovky znázorňující chybu při vytváření účtu úložiště při porušení zásad":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>Potřebná oprávnění pro vyžadování minimální verze protokolu TLS

Aby uživatel mohl nastavit vlastnost **MinimumTlsVersion** pro účet úložiště, musí mít oprávnění k vytváření a správě účtů úložiště. Role řízení přístupu na základě role Azure (Azure RBAC), které poskytují tato oprávnění, zahrnují **Microsoft. Storage/storageAccounts/Write** nebo **Microsoft. Storage/storageAccounts \* /** Action. Mezi předdefinované role s touto akcí patří:

- Role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Role [přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Role [Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Tyto role neposkytují přístup k datům v účtu úložiště prostřednictvím služby Azure Active Directory (Azure AD). Zahrnují však **akci Microsoft. Storage/storageAccounts/klíče listkey/Action**, která uděluje přístup k klíčům pro přístup k účtu. S tímto oprávněním může uživatel použít přístupové klíče účtu pro přístup ke všem datům v účtu úložiště.

Přiřazení rolí musí být vymezené na úrovni účtu úložiště nebo vyšší, aby uživatel mohl vyžadovat minimální verzi TLS pro účet úložiště. Další informace o rozsahu role najdete v tématu [vysvětlení oboru pro službu Azure RBAC](../../role-based-access-control/scope-overview.md).

Buďte opatrní, abyste omezili přiřazení těchto rolí jenom na ty, kteří potřebují možnost vytvořit účet úložiště nebo aktualizovat jeho vlastnosti. Použijte princip nejnižších oprávnění, abyste měli jistotu, že uživatelé mají nejnižší oprávnění, která potřebují k tomu, aby mohli plnit své úkoly. Další informace o správě přístupu pomocí služby Azure RBAC najdete v tématu [osvědčené postupy pro službu Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Správci služby pro klasický odběr role správce a Co-Administrator zahrnují ekvivalent role Azure Resource Manager [vlastníka](../../role-based-access-control/built-in-roles.md#owner) . Role **vlastníka** zahrnuje všechny akce, takže uživatel s jednou z těchto rolí pro správu může také vytvářet a spravovat účty úložiště. Další informace najdete v tématech [role správců klasického předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="network-considerations"></a>Důležité informace z hlediska využívání sítě

Když klient odešle požadavek na účet úložiště, napřed zpracováním všech požadavků napředá klient připojení k veřejnému koncovému bodu účtu úložiště. Po navázání připojení se kontroluje nastavení minimální verze protokolu TLS. Pokud žádost používá starší verzi TLS, než je zadaná v nastavení, bude připojení i nadále úspěšné, ale žádost bude nakonec neúspěšná. Další informace o veřejných koncových bodech pro Azure Storage najdete v tématu [syntaxe identifikátoru URI prostředku](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Další kroky

- [Konfigurace protokolu TLS (Transport Layer Security) pro klientskou aplikaci](transport-layer-security-configure-client-version.md)
- [Doporučení zabezpečení pro úložiště objektů BLOB](../blobs/security-recommendations.md)
