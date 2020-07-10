---
title: Konfigurace minimální požadované verze protokolu TLS (Transport Layer Security) pro účet úložiště
titleSuffix: Azure Storage
description: Nakonfigurujte účet úložiště tak, aby vyžadoval minimální verzi protokolu TLS (Transport Layer Security) pro klienty, kteří žádají o Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209385"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Konfigurace minimální požadované verze protokolu TLS (Transport Layer Security) pro účet úložiště

Komunikace mezi klientskou aplikací a účtem Azure Storage je zašifrovaná pomocí protokolu TLS (Transport Layer Security). TLS je standardní kryptografický protokol, který zajišťuje ochranu osobních údajů a integritu dat mezi klienty a službami přes Internet. Další informace o TLS najdete v tématu [zabezpečení transportní vrstvy](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Azure Storage aktuálně podporuje tři verze protokolu TLS: 1,0, 1,1 a 1,2. Protokol TLS 1,2 je nejbezpečnější verzí TLS. Azure Storage používá TLS 1,2 u veřejných koncových bodů HTTPs, ale TLS 1,0 a TLS 1,1 se pořád podporuje kvůli zpětné kompatibilitě.

Ve výchozím nastavení Azure Storage účty umožňují klientům odesílat a přijímat data pomocí nejstarší verze TLS, TLS 1,0 a vyšší. Abyste vynutili přísnější bezpečnostní opatření, můžete nakonfigurovat účet úložiště tak, aby vyžadoval, aby klienti odesílali a přijímali data pomocí novější verze TLS. Pokud účet úložiště vyžaduje minimální verzi TLS, nebudou všechny požadavky vytvořené v předchozí verzi úspěšné.

Tento článek popisuje, jak nakonfigurovat účet úložiště tak, aby vyžadoval, aby klienti odesílali požadavky s minimální verzí TLS. Informace o tom, jak zadat konkrétní verzi TLS při odesílání žádosti z klientské aplikace, najdete v tématu [Konfigurace TLS (Transport Layer Security) pro klientskou aplikaci](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Zjištění verze protokolu TLS používaných klientskými aplikacemi

Když vynutili minimální verzi protokolu TLS pro váš účet úložiště, riskujete zamítnutí žádostí od klientů, kteří odesílají data pomocí dřívější verze TLS. Aby bylo možné pochopit, jak konfigurace minimální verze protokolu TLS může ovlivnit klientské aplikace, společnost Microsoft doporučuje povolit protokolování pro účet Azure Storage a analyzovat protokoly po časovém intervalu, abyste zjistili, jaké verze klientských aplikací TLS používají.

K protokolování požadavků na účet Azure Storage a určení verze TLS, kterou klient používá, můžete použít Azure Storage přihlášení Azure Monitor (Preview). Další informace najdete v tématu [monitorování Azure Storage](monitor-storage.md).

Azure Storage přihlášení Azure Monitor podporuje použití dotazů protokolu k analýze dat protokolu. K dotazování protokolů můžete použít pracovní prostor Azure Log Analytics. Další informace o dotazech protokolu najdete v tématu [kurz: Začínáme s Log Analytics dotazy](../../azure-monitor/log-query/get-started-portal.md).

Pokud chcete protokolovat data Azure Storage pomocí Azure Monitor a analyzovat je pomocí Azure Log Analytics, musíte nejdřív vytvořit nastavení diagnostiky, které indikuje, jaké typy požadavků a pro které služby úložiště chcete data protokolovat. Chcete-li v Azure Portal vytvořit nastavení diagnostiky, postupujte takto:

1. Zaregistrujte se [Azure Storage přihlašování Azure monitor ve verzi Preview](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Vytvořte nový pracovní prostor Log Analytics v předplatném, které obsahuje váš Azure Storage účet. Po nakonfigurování protokolování účtu úložiště budou protokoly k dispozici v pracovním prostoru Log Analytics. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části monitorování vyberte **nastavení diagnostiky (Preview)**.
1. Vyberte službu Azure Storage, pro kterou chcete protokolovat požadavky. Například vyberte **objekt BLOB** , který bude protokolovat požadavky do úložiště objektů BLOB.
1. Vyberte **Přidat nastavení diagnostiky**.
1. Zadejte název pro nastavení diagnostiky.
1. V části **Podrobnosti o kategorii**v části **protokol** vyberte typy požadavků, které se mají protokolovat. Můžete protokolovat požadavky na čtení, zápis a odstranění. Například volba **StorageRead** a **StorageWrite** bude protokolovat požadavky na čtení a zápis do vybrané služby.
1. V části **Podrobnosti o cíli**vyberte **Odeslat do Log Analytics**. Vyberte své předplatné a Log Analytics pracovní prostor, který jste vytvořili dříve, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Snímek obrazovky ukazující, jak vytvořit nastavení diagnostiky pro požadavky protokolování":::

Po vytvoření nastavení diagnostiky se požadavky na účet úložiště následně protokolují podle tohoto nastavení. Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik prostředků v Azure](../../azure-monitor/platform/diagnostic-settings.md).

Odkaz na pole, která jsou k dispozici v Azure Storage protokoly v Azure Monitor, najdete v tématu [protokoly prostředků (Preview)](monitor-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Dotazy protokolovaných požadavků podle verze TLS

Protokoly Azure Storage v Azure Monitor zahrnují verzi TLS používanou k odeslání požadavku účtu úložiště. K ověření verze protokolu TLS protokolovaných požadavků použijte vlastnost **TlsVersion** .

Chcete-li načíst protokoly za posledních 7 dní a určit počet požadavků provedených v úložišti objektů BLOB s každou verzí TLS, otevřete pracovní prostor Log Analytics. Dále vložte následující dotaz do nového dotazu protokolu a spusťte jej. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Ve výsledcích se zobrazuje počet požadavků provedených v každé z verzí TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Snímek obrazovky zobrazující výsledky dotazu Log Analytics pro vrácení verze TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Dotazování protokolovaných požadavků podle IP adresy volajícího a hlavičky uživatelského agenta

Protokoly Azure Storage v Azure Monitor také zahrnují IP adresu volajícího a hlavičku uživatelského agenta, které vám pomůžou vyhodnotit, které klientské aplikace přistupovaly k účtu úložiště. Tyto hodnoty můžete analyzovat a rozhodnout se, jestli se klientské aplikace musí aktualizovat tak, aby používaly novější verzi TLS, nebo jestli je přijatelné, pokud není žádost klienta v případě, že se odešle s minimální verzí protokolu TLS, nezdařila.

Chcete-li načíst protokoly za posledních 7 dní a určit, kteří klienti požádali o verzi TLS před protokolem TLS 1,2, vložte následující dotaz do nového dotazu protokolu a spusťte jej. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>Konfigurace minimální verze protokolu TLS pro účet

Pro konfiguraci minimální verze protokolu TLS pro účet úložiště použijte Azure Portal nebo Azure CLI k nastavení verze **minimumTlsVersion** pro tento účet. Tato vlastnost je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Další informace najdete v tématu [Přehled účtu úložiště](storage-account-overview.md).

# <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete nakonfigurovat minimální verzi TLS pro účet úložiště s Azure Portal, postupujte následovně:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. Vyberte nastavení **Konfigurace** .
1. V části **Minimální verze protokolu TLS**pomocí rozevíracího seznamu vyberte minimální verzi TLS potřebnou pro přístup k datům v tomto účtu úložiště, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat minimální verzi TLS v Azure Portal":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nakonfigurovat minimální verzi TLS pro účet úložiště pomocí Azure CLI, nejdřív Získejte ID prostředku pro svůj účet úložiště voláním příkazu [AZ Resource show](/cli/azure/resource#az-resource-show) . Potom zavolejte příkaz [AZ Resource Update](/cli/azure/resource#az-resource-update) a nastavte vlastnost **minimumTlsVersion** pro účet úložiště. Platné hodnoty pro **minimumTlsVersion** jsou `TLS1_0` , `TLS1_1` a `TLS1_2` .

Následující příklad nastaví minimální verzi protokolu TLS na 1,2. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> Po aktualizaci minimální verze protokolu TLS pro účet úložiště může trvat až 30 sekund, než se změna zcela rozšíří.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>Ověřte minimální požadovanou verzi TLS pro účet.

Pokud chcete zjistit minimální požadovanou verzi TLS konfigurovanou pro účet úložiště, zkontrolujte vlastnost Azure Resource Manager **minimumTlsVersion** . Pokud chcete tuto vlastnost pro velké číslo účtů úložiště ověřit najednou, použijte Průzkumníka Azure Resource graphu.

Vlastnost **minimumTlsVersion** není nastavena ve výchozím nastavení a nevrací hodnotu, dokud ji explicitně nenastavíte. V účtu úložiště se standardně povolují požadavky odeslané pomocí protokolu TLS verze 1,0 nebo vyšší, pokud je hodnota vlastnosti null.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>Ověřte minimální požadovanou verzi TLS pro jeden účet úložiště.

Pokud chcete ověřit minimální požadovanou verzi TLS pro jeden účet úložiště pomocí Azure CLI, zavolejte příkaz [AZ Resource show](/cli/azure/resource#az-resource-show) a dotaz na vlastnost **minimumTlsVersion** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>Ověřte minimální požadovanou verzi TLS pro sadu účtů úložiště.

Pokud chcete ověřit minimální potřebnou verzi TLS v rámci sady účtů úložiště s optimálním výkonem, můžete použít Průzkumníka Azure Resource graphu v Azure Portal. Další informace o používání Průzkumníka grafů prostředků najdete v tématu [rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](/azure/governance/resource-graph/first-query-portal).

Když spustíte následující dotaz, v Průzkumníku grafu prostředků se vrátí seznam účtů úložiště a pro každý účet se zobrazí minimální verze protokolu TLS:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>Otestování minimální verze protokolu TLS z klienta

Pokud chcete otestovat, jestli minimální požadovaná verze TLS pro účet úložiště neumožňuje volání se starší verzí, můžete nakonfigurovat klienta tak, aby používal starší verzi TLS. Další informace o konfiguraci klienta pro použití konkrétní verze TLS najdete v tématu [Konfigurace TLS (Transport Layer Security) pro klientskou aplikaci](transport-layer-security-configure-client-version.md).

Když klient přistupuje k účtu úložiště pomocí verze TLS, která nesplňuje požadavek na minimální verzi TLS nakonfigurovanou pro tento účet, Azure Storage vrátí chybu kód chyby 400 (chybný požadavek) a zprávu oznamující, že použitá verze protokolu TLS není povolená pro vytváření žádostí na tento účet úložiště.

## <a name="next-steps"></a>Další kroky

- [Konfigurace protokolu TLS (Transport Layer Security) pro klientskou aplikaci](transport-layer-security-configure-client-version.md)
- [Doporučení zabezpečení pro úložiště objektů BLOB](../blobs/security-recommendations.md)
