---
title: Zabránění autorizaci pomocí sdíleného klíče (Preview)
titleSuffix: Azure Storage
description: Pokud chcete, aby klienti k autorizaci požadavků používali službu Azure AD, můžete zakázat žádosti na účet úložiště, který je autorizovaný pomocí sdíleného klíče (Preview).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 49a89228afd3b46f38afafb8ff16bc63a40dd35b
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635207"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Zabránit autorizaci sdíleného klíče pro účet Azure Storage (Preview)

Každý zabezpečený požadavek na účet Azure Storage musí být autorizovaný. Ve výchozím nastavení mohou být požadavky autorizovány buď pomocí pověření Azure Active Directory (Azure AD), nebo pomocí přístupového klíče účtu pro autorizaci pomocí sdíleného klíče. Z těchto dvou typů autorizace poskytuje Azure AD vynikající zabezpečení a snadné použití prostřednictvím sdíleného klíče a doporučuje se od Microsoftu. Pokud chcete, aby klienti k autorizaci požadavků používali službu Azure AD, můžete zakázat žádosti na účet úložiště, který je autorizovaný pomocí sdíleného klíče (Preview).

Pokud zakážete autorizaci sdíleného klíče pro účet úložiště, Azure Storage zamítne všechny následné požadavky na tento účet, které jsou autorizované pomocí přístupových klíčů účtu. Úspěšné budou jenom zabezpečené žádosti, které jsou autorizované se službou Azure AD. Další informace o používání služby Azure AD najdete v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](storage-auth-aad.md).

> [!WARNING]
> Azure Storage podporuje autorizaci Azure AD jenom pro požadavky na úložiště objektů BLOB a front. Pokud zakážete autorizaci se sdíleným klíčem pro účet úložiště, požadavky na soubory Azure nebo úložiště tabulek, které používají autorizaci pomocí sdíleného klíče, se nezdaří.
>
> V rámci verze Preview budou žádosti o soubory Azure nebo úložiště tabulek, které používají tokeny sdíleného přístupového podpisu (SAS) vygenerované pomocí přístupových klíčů k účtu, úspěšné, pokud není povolená autorizace sdíleného klíče. Další informace najdete v tématu [o verzi Preview](#about-the-preview).
>
> Nepovolený přístup ke sdíleným klíčům pro účet úložiště nemá vliv na připojení SMB ke službě soubory Azure.
>
> Společnost Microsoft doporučuje, abyste buď migrujete jakákoli data služby soubory Azure nebo úložiště tabulek do samostatného účtu úložiště, předtím, než zakážete přístup k účtu přes sdílený klíč, nebo že toto nastavení nepoužijete pro účty úložiště, které podporují úlohy Azure Files nebo Table Storage.

Tento článek popisuje, jak zjišťovat požadavky odeslané pomocí autorizace pomocí sdíleného klíče a jak napravit autorizaci sdíleného klíče pro váš účet úložiště. Informace o tom, jak se zaregistrovat ve verzi Preview, najdete v tématu [o verzi Preview](#about-the-preview).

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Zjištění typu autorizace používané klientskými aplikacemi

Pokud zakážete autorizaci sdíleného klíče pro účet úložiště, žádosti od klientů, kteří používají přístupové klíče účtu pro autorizaci pomocí sdíleného klíče, selžou. Aby bylo možné pochopit, jak Nepovolit autorizaci sdíleného klíče může ovlivnit klientské aplikace před provedením této změny, povolte protokolování a metriky pro účet úložiště. Pak můžete analyzovat vzorce požadavků na váš účet za určitou dobu, abyste zjistili, jak se požadavky mají autorizovat.

Pomocí metrik určete počet požadavků, které účet úložiště přijímá a které jsou autorizované pomocí sdíleného klíče nebo sdíleného přístupového podpisu (SAS). Pomocí protokolů určete, kteří klienti tyto požadavky odesílají.

Další informace o interpretaci požadavků provedených se sdíleným přístupovým podpisem ve verzi Preview najdete v tématu [informace o verzi Preview](#about-the-preview).

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Sledovat, kolik požadavků je autorizováno se sdíleným klíčem

Chcete-li sledovat, jak jsou požadavky na účet úložiště autorizovány, použijte Azure Průzkumník metrik v Azure Portal. Další informace o Průzkumník metrik najdete v tématu [Začínáme s Azure Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md).

Pomocí těchto kroků můžete vytvořit metriku, která bude sledovat požadavky vytvořené pomocí sdíleného klíče nebo SAS:

1. Na webu Azure Portal přejděte na svůj účet úložiště. V části **monitorování** vyberte **metriky**.
1. Vyberte **Přidat metriku**. V dialogovém okně **metrika** zadejte následující hodnoty:
    1. Ponechte pole **obor** nastavené na název účtu úložiště.
    1. Nastavte **obor názvů metriky** na hodnotu *account*. Tato metrika bude hlásit všechny požadavky na účet úložiště.
    1. Nastavte pole **metrika** na hodnotu *Transactions*.
    1. Nastavte **agregační** pole na *součet*.

    V nové metrikě se v daném časovém intervalu zobrazí součet počtu transakcí s účtem úložiště. Výsledná metrika se zobrazí, jak je znázorněno na následujícím obrázku:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat metriku pro Shrnutí transakcí provedených se sdíleným klíčem nebo SAS":::

1. V dalším kroku vyberte tlačítko **Přidat filtr** a vytvořte filtr pro danou metriku pro typ autorizace.
1. V dialogovém okně **Filtr** zadejte následující hodnoty:
    1. Nastavte hodnotu **vlastnosti** na *ověřování*.
    1. Nastavte pole **operátor** na znaménko rovná se (=).
    1. V poli **hodnoty** vyberte *klíč účtu* a *SAS*.
1. V pravém horním rohu vyberte časový rozsah, pro který chcete metriku zobrazit. Můžete také určit, jak podrobně má být agregace požadavků, zadáním intervalů kdekoli od 1 minuty po 1 měsíc. Nastavte například **časový rozsah** na 30 dní a **časové rozlišení** na 1 den, aby se v posledních 30 dnech zobrazily požadavky agregované za den.

Po nakonfigurování metriky se v grafu začnou zobrazovat požadavky na váš účet úložiště. Následující obrázek znázorňuje žádosti, které byly autorizovány se sdíleným klíčem nebo vytvořené pomocí tokenu SAS. Žádosti se agregují za den za posledních třicet dnů.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Snímek obrazovky znázorňující agregované požadavky s povoleným sdíleným klíčem":::

Můžete také nakonfigurovat pravidlo výstrahy, které vás upozorní, když se v účtu úložiště vytvoří určitý počet požadavků, které jsou autorizované se sdíleným klíčem. Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Analýza protokolů pro identifikaci klientů, kteří autorizují žádosti se sdíleným klíčem nebo SAS

Protokoly Azure Storage zaznamenávají informace o požadavcích provedených proti účtu úložiště, včetně způsobu autorizace žádosti. Protokoly můžete analyzovat a určit tak, kteří klienti autorizují žádosti se sdíleným klíčem nebo tokenem SAS.

Pokud chcete protokolovat požadavky na účet Azure Storage, abyste mohli vyhodnotit, jak jsou autorizované, můžete použít Azure Storage přihlášení Azure Monitor (Preview). Další informace najdete v tématu [monitorování Azure Storage](../blobs/monitor-blob-storage.md).

Azure Storage přihlášení Azure Monitor podporuje použití dotazů protokolu k analýze dat protokolu. K dotazování protokolů můžete použít pracovní prostor Azure Log Analytics. Další informace o dotazech protokolu najdete v tématu [kurz: Začínáme s Log Analytics dotazy](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Vytvoření nastavení diagnostiky v Azure Portal

Pokud chcete protokolovat data Azure Storage pomocí Azure Monitor a analyzovat je pomocí Azure Log Analytics, musíte nejdřív vytvořit nastavení diagnostiky, které indikuje, jaké typy požadavků a pro které služby úložiště chcete data protokolovat. Chcete-li v Azure Portal vytvořit nastavení diagnostiky, postupujte takto:

1. Zaregistrujte se [Azure Storage přihlašování Azure monitor ve verzi Preview](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Vytvořte nový pracovní prostor Log Analytics v předplatném, které obsahuje účet Azure Storage, nebo použijte existující Log Analytics pracovní prostor. Po nakonfigurování protokolování účtu úložiště budou protokoly k dispozici v pracovním prostoru Log Analytics. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části monitorování vyberte **nastavení diagnostiky (Preview)**.
1. Vyberte službu Azure Storage, pro kterou chcete protokolovat požadavky. Například vyberte **objekt BLOB** , který bude protokolovat požadavky do úložiště objektů BLOB.
1. Vyberte **Přidat nastavení diagnostiky**.
1. Zadejte název pro nastavení diagnostiky.
1. V části **Podrobnosti o kategorii** v **části protokol** vyberte **StorageRead** , **StorageWrite** a **StorageDelete** a Zaprotokolujte všechny požadavky na data vybrané služby.
1. V části **Podrobnosti o cíli** vyberte **Odeslat do Log Analytics**. Vyberte své předplatné a Log Analytics pracovní prostor, který jste vytvořili dříve, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Snímek obrazovky ukazující, jak vytvořit nastavení diagnostiky pro požadavky protokolování":::

Pro každý typ Azure Storage prostředku v účtu úložiště můžete vytvořit nastavení diagnostiky.

Po vytvoření nastavení diagnostiky se požadavky na účet úložiště následně protokolují podle tohoto nastavení. Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik prostředků v Azure](../../azure-monitor/platform/diagnostic-settings.md).

Odkaz na pole, která jsou k dispozici v Azure Storage protokoly v Azure Monitor, najdete v tématu [protokoly prostředků (Preview)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Protokol dotazů pro požadavky vytvořené se sdíleným klíčem nebo SAS

Protokoly Azure Storage v Azure Monitor zahrnují typ ověřování, který se použil k vytvoření požadavku na účet úložiště. Chcete-li načíst protokoly pro požadavky provedené během posledních sedmi dnů, které byly autorizovány se sdíleným klíčem nebo SAS, otevřete pracovní prostor Log Analytics. Dále vložte následující dotaz do nového dotazu protokolu a spusťte jej. Tento dotaz zobrazí deset IP adres, které nejčastěji odesílají požadavky, které byly autorizovány pomocí sdíleného klíče nebo SAS:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

Můžete také nakonfigurovat pravidlo výstrahy na základě tohoto dotazu, které vás upozorní na požadavky autorizované se sdíleným klíčem nebo SAS. Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Opravit autorizaci pomocí sdíleného klíče

Po dokončení analýzy požadavků na účet úložiště můžete provést akci, abyste zabránili přístupu přes sdílený klíč. Nejdřív ale budete muset aktualizovat všechny aplikace, které používají autorizaci pomocí sdíleného klíče, a místo toho použít službu Azure AD. Můžete monitorovat protokoly a metriky, jak je popsáno v tématu [zjištění typu autorizace používaného klientskými aplikacemi](#detect-the-type-of-authorization-used-by-client-applications) ke sledování přechodu. Další informace o používání služby Azure AD s daty objektů BLOB a front najdete v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](storage-auth-aad.md).

Pokud máte jistotu, že můžete bezpečně odmítat žádosti, které jsou autorizované se sdíleným klíčem, můžete nastavit vlastnost **AllowSharedKeyAccess** pro účet úložiště na **false**.

Vlastnost **AllowSharedKeyAccess** není nastavena ve výchozím nastavení a nevrací hodnotu, dokud ji explicitně nenastavíte. Účet úložiště povoluje žádosti, které jsou autorizované se sdíleným klíčem, pokud má vlastnost hodnotu **null** nebo je-li nastavena hodnota **true**.

> [!WARNING]
> Pokud má nějaký klient v současnosti přístup k datům ve vašem účtu úložiště se sdíleným klíčem, společnost Microsoft doporučuje, abyste tyto klienty migrovali do služby Azure AD ještě před tím, než zakážete přístup ke sdíleným klíčům k účtu úložiště.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete zakázat autorizaci sdíleného klíče pro účet úložiště v Azure Portal, postupujte následovně:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části **Nastavení** vyhledejte nastavení **Konfigurace** .
1. Nastavte možnost **Povolení přístupu ke sdíleným klíčům** na **zakázáno**.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Snímek obrazovky ukazující, jak zakázat přístup ke sdíleným klíčům pro účet":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zakázat autorizaci sdíleného klíče pro účet úložiště pomocí Azure CLI, nainstalujte Azure CLI verze 2.9.1 nebo novější. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Dále nakonfigurujte vlastnost **allowSharedKeyAccess** pro nový nebo existující účet úložiště.

Následující příklad ukazuje, jak nastavit vlastnost **allowSharedKeyAccess** pomocí Azure CLI. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

Až zakážete autorizaci pomocí sdíleného klíče, požadavek na účet úložiště s autorizací sdíleného klíče selže s kódem chyby 403 (zakázáno). Azure Storage vrátí chybu, která znamená, že v účtu úložiště není povolená autorizace na základě klíčů.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Ověřte, že přístup ke sdílenému klíči není povolený.

Pokud chcete ověřit, že autorizace sdíleného klíče už není povolená, můžete se pokusit o volání datové operace pomocí přístupového klíče účtu. Následující příklad se pokusí vytvořit kontejner pomocí přístupového klíče. Toto volání se nezdaří, pokud pro účet úložiště není povoleno autorizaci sdíleného klíče. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> Anonymní požadavky nejsou autorizované a budou pokračovat, pokud jste nakonfigurovali účet úložiště a kontejner pro anonymní veřejný přístup pro čtení. Další informace najdete v tématu [Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob](../blobs/anonymous-read-access-configure.md).

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Kontrolovat nastavení přístupu ke sdílenému klíči pro více účtů

Pokud chcete kontrolovat nastavení přístupu ke sdílenému klíči v rámci sady účtů úložiště s optimálním výkonem, můžete v Azure Portal použít Průzkumníka prostředků Azure. Další informace o používání Průzkumníka grafů prostředků najdete v tématu [rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../../governance/resource-graph/first-query-portal.md).

Když spustíte následující dotaz, v Průzkumníku grafu prostředků se vrátí seznam účtů úložiště a pro každý účet se zobrazí nastavení přístupu ke sdílenému klíči:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Informace o tom, jak zakázaný sdílený klíč ovlivňuje tokeny SAS

Pokud je pro účet úložiště zakázaný sdílený klíč, Azure Storage zpracovává tokeny SAS na základě typu SAS a služby, na kterou cílí požadavek. Následující tabulka ukazuje, jak jednotlivé typy SAS mají autorizaci a jak Azure Storage tuto SAS pozastaví, pokud je vlastnost **AllowSharedKeyAccess** pro účet úložiště **nepravdivá**.

| Typ SAS | Typ autorizace | Chování, pokud je AllowSharedKeyAccess false |
|-|-|-|
| SAS delegování uživatelů (jenom BLOB Storage) | Azure AD | Požadavek je povolen. Pokud je to možné, společnost Microsoft doporučuje používat k zajištění nadřazeného zabezpečení přidružení zabezpečení při delegování uživatelů. |
| SAS služby | Sdílený klíč | Požadavek se zamítl pro úložiště objektů BLOB. Požadavek je povolený pro úložiště front a tabulek a pro soubory Azure. Další informace najdete v části [požadavky s tokeny SAS pro fronty, tabulky a soubory, pokud AllowSharedKeyAccess je false](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) v sekci **o verzi Preview** . |
| SAS účtu | Sdílený klíč | Požadavek se zamítl pro úložiště objektů BLOB. Požadavek je povolený pro úložiště front a tabulek a pro soubory Azure. Další informace najdete v části [požadavky s tokeny SAS pro fronty, tabulky a soubory, pokud AllowSharedKeyAccess je false](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) v sekci **o verzi Preview** . |

Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Zvážení kompatibility s dalšími nástroji a službami Azure

Řada služeb Azure používá k komunikaci s Azure Storage autorizaci pomocí sdíleného klíče. Pokud zakážete autorizaci sdíleného klíče pro účet úložiště, tyto služby nebudou mít přístup k datům v tomto účtu a vaše aplikace můžou být nepříznivě ovlivněny.

Některé nástroje Azure nabízejí možnost použít pro přístup k Azure Storage autorizaci Azure AD. V následující tabulce jsou uvedené některé oblíbené nástroje a poznámky Azure, které můžou pomocí Azure AD autorizovat požadavky na Azure Storage.

| Nástroj Azure | Ověřování Azure AD pro Azure Storage |
|-|-|
| portál Azure | Podporuje se. Informace o autorizaci účtu Azure AD z Azure Portal najdete v tématu [Volba způsobu autorizace přístupu k datům objektů BLOB v Azure Portal](../blobs/authorize-data-operations-portal.md). |
| AzCopy | Podporováno pro úložiště objektů BLOB. Informace o autorizaci operací AzCopy najdete v tématu [Volba způsobu poskytování přihlašovacích údajů pro autorizaci](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) v dokumentaci k AzCopy. |
| Průzkumník služby Azure Storage | Podporováno pouze pro úložiště objektů BLOB a Azure Data Lake Storage Gen2. Přístup z Azure AD do úložiště Queue není podporovaný. Ujistěte se, že jste vybrali správného tenanta Azure AD. Další informace najdete v tématu [Začínáme s Průzkumník služby Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) . |
| Azure PowerShell | Podporuje se. Informace o tom, jak autorizovat příkazy PowerShellu pro operace objektů BLOB a front s Azure AD, najdete v tématu [spuštění příkazů PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům objektů BLOB](../blobs/authorize-data-operations-powershell.md) nebo [spuštěním příkazů PowerShellu s přihlašovacími údaji služby Azure AD pro přístup k datům](../queues/authorize-data-operations-powershell.md) |
| Azure CLI | Podporuje se. Informace o tom, jak autorizovat příkazy rozhraní příkazového řádku Azure pomocí Azure AD pro přístup k datům BLOB a Queue, najdete v tématu [spuštění příkazů rozhraní příkazového řádku Azure s přihlašovacími údaji Azure AD pro přístup k datům BLOB nebo Queue](authorize-data-operations-cli.md). |
| Azure IoT Hub | Podporuje se. Další informace najdete v tématu [podpora IoT Hub pro virtuální sítě](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | Azure Cloud Shell je integrované prostředí v Azure Portal. Azure Cloud Shell hostuje soubory pro trvalost ve sdílené složce Azure v účtu úložiště. Tyto soubory budou nepřístupné, pokud pro tento účet úložiště není povolená autorizace sdíleného klíče. Další informace najdete v tématu [připojení úložiště Microsoft Azurech souborů](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage). <br /><br /> Pokud chcete spouštět příkazy v Azure Cloud Shell ke správě účtů úložiště, pro které je přístup ke sdíleným klíčům zakázaný, nejdřív se ujistěte, že jste k těmto účtům udělili potřebná oprávnění prostřednictvím řízení přístupu na základě role Azure (RBAC). Další informace najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md). |

## <a name="about-the-preview"></a>O verzi Preview

Verze Preview pro povolení autorizace sdíleného klíče je dostupná ve veřejném cloudu Azure. Podporuje se jenom pro účty úložiště, které používají model nasazení Azure Resource Manager. Informace o tom, které účty úložiště používají model nasazení Azure Resource Manager, najdete v tématu [typy účtů úložiště](storage-account-overview.md#types-of-storage-accounts).

Pokud se chcete zaregistrovat ve verzi Preview, přečtěte si téma [Azure Storage povolení přístupu ke sdíleným klíčům omezený Public Preview](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u).

> [!IMPORTANT]
> Tato verze Preview je určená jenom pro neprodukční použití.

Verze Preview obsahuje omezení popsaná v následujících částech.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Metriky a protokolování hlásí všechny požadavky vytvořené pomocí SAS bez ohledu na to, jak jsou autorizovány.

Metriky Azure a přihlašování Azure Monitor nerozlišuje mezi různými typy podpisů sdíleného přístupu ve verzi Preview. Filtr **SAS** v Azure Průzkumník metrik a pole **SAS** v Azure Storage přihlašování Azure monitor obě žádosti sestavy, které jsou autorizované s jakýmkoli typem SAS. Různé typy podpisů sdíleného přístupu se ale povolují různě a chovají se jinak, když je přístup ke sdíleným klíčům zakázaný:

- Token SAS služby nebo token SAS účtu mají autorizaci se sdíleným klíčem a v případě, že je vlastnost **AllowSharedKeyAccess** nastavená na **hodnotu false** , nebude u požadavku na úložiště objektů BLOB povolený.
- Přidružení zabezpečení uživatele je autorizováno pomocí Azure AD a bude povoleno na vyžádání úložiště objektů blob, pokud je vlastnost **AllowSharedKeyAccess** nastavena na **hodnotu false**.

Při vyhodnocování provozu do svého účtu úložiště Pamatujte na to, že metriky a protokoly, jak je popsáno v tématu [zjištění typu autorizace používané klientskými aplikacemi](#detect-the-type-of-authorization-used-by-client-applications) , můžou zahrnovat požadavky vytvořené pomocí SAS delegování uživatele. Další informace o tom, jak Azure Storage reaguje na SAS, pokud je vlastnost **AllowSharedKeyAccess** nastavená na **false** , najdete v tématu [Vysvětlení způsobu, jakým nepovoluje sdílený klíč vliv na tokeny SAS](#understand-how-disallowing-shared-key-affects-sas-tokens).

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>Žádosti s tokeny SAS jsou povolené pro fronty, tabulky a soubory, pokud je AllowSharedKeyAccess false.

Pokud je pro účet úložiště během verze Preview povolený přístup ke sdíleným klíčům, budou se dál moct povolit signatury sdíleného přístupu, které cílí na prostředky ve frontě, tabulkách nebo souborech Azure. Toto omezení platí pro tokeny SAS služeb i pro tokeny SAS účtu. Oba typy SAS jsou autorizovány pomocí sdíleného klíče.

## <a name="next-steps"></a>Další kroky

- [Autorizace přístupu k datům v Azure Storage](storage-auth.md)
- [Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](storage-auth-aad.md)
- [Autorizace s využitím sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key)