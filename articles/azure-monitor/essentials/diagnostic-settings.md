---
title: Vytvoření nastavení diagnostiky pro odesílání metrik a protokolů platformy do různých cílů
description: Pomocí nastavení diagnostiky můžete posílat metriky a protokoly platformy Azure Monitor do protokolů Azure Monitor, úložiště Azure nebo Azure Event Hubs.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 254d403adc687074eae772bcdcc55793bb25b336
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048909"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Vytvoření nastavení diagnostiky pro odesílání metrik a protokolů platformy do různých cílů
[Protokoly platforem](./platform-logs-overview.md) v Azure, včetně protokolů aktivit Azure a protokolů prostředků, poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. [Metriky platformy](./data-platform-metrics.md) se ve výchozím nastavení shromažďují a obvykle se ukládají do databáze Azure monitor metrik. Tento článek poskytuje podrobné informace o vytváření a konfiguraci nastavení diagnostiky pro odesílání metrik platforem a protokolů platforem do různých umístění.

> [!IMPORTANT]
> Před vytvořením nastavení diagnostiky pro protokol aktivit byste nejdřív měli zakázat všechny starší konfigurace. Podrobnosti naleznete v tématu [starší metody kolekce](../essentials/activity-log.md#legacy-collection-methods) .

Každý prostředek Azure vyžaduje vlastní nastavení diagnostiky, které definuje následující kritéria:

- Kategorie protokolů a data metriky, které jsou odesílány do cílů definovaných v nastavení. Dostupné kategorie se budou lišit pro různé typy prostředků.
- Jeden nebo více cílů pro odeslání protokolů. Aktuální cíle zahrnují Log Analytics pracovní prostor, Event Hubs a Azure Storage.

Jedno diagnostické nastavení může definovat více než jedno z cílů. Pokud chcete odesílat data do více než jednoho konkrétního cílového typu (například dvou různých Log Analytics pracovních prostorů), vytvořte více nastavení. Každý prostředek může mít až 5 nastavení diagnostiky.

Následující video vás provede protokoly platformy směrování s nastavením diagnostiky.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [Metriky platformy](./metrics-supported.md) se odesílají automaticky, aby se [Azure monitor metriky](./data-platform-metrics.md). Nastavení diagnostiky se dá použít k posílání metrik pro určité služby Azure do protokolů Azure Monitor pro analýzu s dalšími daty monitorování pomocí [dotazů protokolu](../logs/log-query-overview.md) s určitými omezeními. 
>  
>  
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí. *Například*: metrika ' IOReadBytes ' na blockchain lze prozkoumat a vytvořit graf na úrovni jednotlivých uzlů. Při exportu pomocí nastavení diagnostiky ale vyexportovaná metrika představuje všechny bajty čtení pro všechny uzly. Kromě interních omezení nejsou všechny metriky exportovatelné do Azure Monitor protokolů/Log Analytics. Další informace najdete v [seznamu exportovatelné metriky](./metrics-supported-export-diagnostic-settings.md). 
>  
>  
> Pokud chcete získat tato omezení pro konkrétní metriky, doporučujeme je ručně extrahovat pomocí [metrik REST API](/rest/api/monitor/metrics/list) a importovat je do protokolů Azure monitor pomocí [rozhraní API pro Azure monitor kolektor dat](../logs/data-collector-api.md).  


## <a name="destinations"></a>Cíle
Protokoly a metriky platformy je možné odeslat do cílových umístění v následující tabulce. 

| Cíl | Popis |
|:---|:---|
| [Pracovní prostor Log Analytics](../logs/design-logs-deployment.md) | Odesílání protokolů a metrik do Log Analyticsového pracovního prostoru vám umožní je analyzovat s dalšími daty monitorování shromážděnými pomocí Azure Monitor pomocí výkonných dotazů protokolu a také využívat jiné Azure Monitor funkce, jako jsou výstrahy a vizualizace. |
| [Centrum událostí](../../event-hubs/index.yml) | Odesílání protokolů a metrik do Event Hubs umožňuje streamování dat do externích systémů, jako jsou systémů Siem třetích stran a další řešení Log Analytics.  |
| [Účet úložiště Azure](../../storage/blobs/index.yml) | Archivace protokolů a metrik do účtu služby Azure Storage je užitečná pro audit, statickou analýzu nebo zálohování. V porovnání s protokoly Azure Monitor a pracovním prostorem Log Analytics je úložiště Azure levnější a protokoly se můžou uchovávat po neomezenou dobu.  |


### <a name="destination-requirements"></a>Požadavky na cíl

Před vytvořením nastavení diagnostiky je nutné vytvořit všechna cílová umístění pro nastavení diagnostiky. Cíl nemusí být ve stejném předplatném jako prostředek odesílající protokoly, pokud uživatel, který nastavení nakonfiguruje, má odpovídající přístup k oběma předplatným Azure RBAC. Následující tabulka obsahuje jedinečné požadavky na jednotlivé cíle včetně případných regionálních omezení.

| Cíl | Požadavky |
|:---|:---|
| Pracovní prostor služby Log Analytics | Pracovní prostor nemusí být ve stejné oblasti jako monitorovaný prostředek.|
| Event Hubs | Zásady sdíleného přístupu pro obor názvů definují oprávnění, která má mechanismus streamování. Streamování do Event Hubs vyžaduje oprávnění spravovat, odesílat a naslouchat. Chcete-li aktualizovat nastavení diagnostiky tak, aby zahrnovalo streamování, musíte mít oprávnění ListKey k tomuto autorizačnímu pravidlu Event Hubs.<br><br>Obor názvů centra událostí musí být ve stejné oblasti jako monitorovaný prostředek, pokud je prostředek místní. |
| Účet služby Azure Storage | Neměli byste používat existující účet úložiště, který obsahuje jiná, nemonitorovaná data, která jsou v něm uložená, abyste mohli lépe řídit přístup k datům. Pokud společně archivujte protokoly aktivit a protokoly prostředků, můžete použít stejný účet úložiště, abyste zachovali všechna data monitorování v centrálním umístění.<br><br>Pokud chcete odesílat data do neměnných úložišť, nastavte pro účet úložiště neměnné zásady, jak je popsáno v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](../../storage/blobs/storage-blob-immutability-policies-manage.md). Musíte postupovat podle všech kroků v tomto článku, včetně povolení chráněných připojených objektů BLOB pro zápis.<br><br>Účet úložiště musí být ve stejné oblasti jako monitorovaný prostředek, pokud je prostředek regionální. |

> [!NOTE]
> Účty Azure Data Lake Storage Gen2 se v současné době nepodporují jaké cíl pro nastavení diagnostiky, přestože na webu Azure Portal můžou být uvedené jako platná možnost.

> [!NOTE]
> Azure Monitor (nastavení diagnostiky) nemá přístup k prostředkům Event Hubs, pokud jsou povolené virtuální sítě. Je nutné povolit, aby důvěryhodné služby Microsoftu vypnuly toto nastavení brány firewall v centru událostí, aby se službě Azure Monitor (nastavení diagnostiky) udělil přístup k prostředkům Event Hubs. 


## <a name="create-in-azure-portal"></a>Vytvoření na webu Azure Portal

Nastavení diagnostiky můžete nakonfigurovat v Azure Portal, a to buď z nabídky Azure Monitor, nebo z nabídky pro daný prostředek.

1. Pokud konfigurujete nastavení diagnostiky v Azure Portal závisí na prostředku.

   - U jednoho prostředku klikněte v nabídce prostředku na položku **nastavení diagnostiky** v části **monitorování** .

        ![Snímek obrazovky s oddílem monitorování v nabídce prostředků v Azure Portal s zvýrazněným diagnostickým nastavením](media/diagnostic-settings/menu-resource.png)

   - Pro jeden nebo více prostředků klikněte na **nastavení diagnostiky** v části **nastavení** v nabídce Azure monitor a potom klikněte na prostředek.

        ![Snímek obrazovky s oddílem nastavení v nabídce Azure Monitor se zvýrazněným diagnostickým nastavením](media/diagnostic-settings/menu-monitor.png)

   - V části Protokol aktivit klikněte na **Protokol aktivit** v nabídce **Azure monitor** a pak na **nastavení diagnostiky**. Ujistěte se, že jste zakázali všechny starší konfigurace protokolu aktivit. Podrobnosti najdete v tématu [zakázání existujících nastavení](./activity-log.md#legacy-collection-methods) .

        ![Snímek obrazovky nabídky Azure Monitor s vybraným protokolem aktivit a nastavením diagnostiky, které se zvýrazní v řádku nabídek protokolu Monitor-Activity](media/diagnostic-settings/menu-activity-log.png)

2. Pokud u vybraného prostředku neexistuje žádné nastavení, zobrazí se výzva k vytvoření nastavení. Klikněte na **Přidat nastavení diagnostiky**.

   ![Přidat nastavení diagnostiky – žádná existující nastavení](media/diagnostic-settings/add-setting.png)

   Pokud je u prostředku k dispozici existující nastavení, zobrazí se seznam nastavení, která jsou už nakonfigurovaná. Kliknutím na **Přidat nastavení diagnostiky** můžete přidat nové nastavení nebo **Upravit nastavení** a upravit existující. Každé nastavení nemůže mít více než jeden z cílových typů.

   ![Přidat nastavení diagnostiky – existující nastavení](media/diagnostic-settings/edit-setting.png)

3. Dejte nastavení název, pokud ho ještě nikdo nemá.

    ![Přidat nastavení diagnostiky](media/diagnostic-settings/setting-new-blank.png)

4. **Podrobnosti kategorie (co směrovat)** – zaškrtněte políčko u každé kategorie dat, která chcete odeslat do cílových umístění určených později. Seznam kategorií se u jednotlivých služeb Azure liší.

     - **AllMetrics** směruje metriky platforem prostředku do úložiště protokolů Azure, ale ve formuláři protokolu. Tyto metriky jsou obvykle odesílány pouze do databáze časových řad Azure Monitor metriky. Odeslání do úložiště protokolů Azure Monitor (které lze prohledávat prostřednictvím Log Analytics) vám pomůže je integrovat do dotazů, které hledají v jiných protokolech. Tato možnost nemusí být k dispozici pro všechny typy prostředků. Pokud je tato podpora podporovaná, [Azure monitor podporované metriky](./metrics-supported.md) uvádí, jaké metriky se shromažďují pro typy prostředků.

       > [!NOTE]
       > V části omezení pro metriky směrování můžete Azure Monitor protokoly dříve v tomto článku.  


     - V **protokolech** jsou uvedeny různé kategorie, které jsou k dispozici v závislosti na typu prostředku. Podívejte se na všechny kategorie, které byste chtěli směrovat do cílového umístění.

5. **Podrobnosti o cíli** – zaškrtněte políčko pro všechny cíle. Když zaškrtnete každé políčko, zobrazí se možnosti, které vám umožní přidat další informace.

      ![Odeslání do Log Analytics nebo Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** – zadejte předplatné a pracovní prostor.  Pokud nemáte pracovní prostor, budete [ho muset před pokračováním vytvořit](../logs/quick-create-workspace.md).

    1. **Centra událostí** – zadejte následující kritéria:
       - Předplatné, jehož součástí je centrum událostí
       - Obor názvů centra událostí – Pokud ho ještě nemáte, budete ho muset [vytvořit](../../event-hubs/event-hubs-create.md) .
       - Název centra událostí (volitelné), do kterého se mají posílat všechna data Pokud název nezadáte, vytvoří se v každé kategorii protokolu centrum událostí. Pokud posíláte více kategorií, možná budete chtít zadat název pro omezení počtu vytvořených Center událostí. Podrobnosti najdete v tématu [kvóty a omezení pro Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) .
       - Zásada centra událostí (volitelné) zásada definuje oprávnění, která má mechanismus streamování. Další informace najdete v části [Event-Center-Features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Úložiště** – vyberte předplatné, účet úložiště a zásady uchovávání informací.

        ![Odeslat do úložiště](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Zvažte nastavení zásad uchovávání na 0 a ruční mazání dat z úložiště pomocí naplánované úlohy, aby se předešlo případnému nejasnostem v budoucnu.
        >
        > Pokud používáte úložiště k archivaci, obecně budete chtít, aby vaše data byla delší než 365 dní. Pokud zvolíte zásadu uchovávání informací, která je větší než 0, datum vypršení platnosti se připojí k protokolům v době úložiště. Po uložení se data pro tyto protokoly nedají změnit.
        >
        > Pokud jste například nastavili zásady uchovávání informací pro *aktivity WorkflowRuntime* na 180 dní a pak 24 hodin později nastavíte na 365 dní, budou se protokoly uložené během těchto prvních 24 hodin automaticky odstraňovat po 180 dnů, zatímco všechny další protokoly tohoto typu se po 365 dnech automaticky odstraní. Pokud později změníte zásady uchovávání informací, nevytvoří se prvních 24 hodin protokolů po dobu 365 dnů.

6. Klikněte na **Uložit**.

Po chvíli se nové nastavení objeví v seznamu nastavení tohoto prostředku a protokoly se streamují do zadaných cílů, protože se generují nová data události. Může trvat až 15 minut, než se událost vygeneruje, a když [se zobrazí v pracovním prostoru Log Analytics](../logs/data-ingestion-time.md).

## <a name="create-using-powershell"></a>Vytvoření s použitím PowerShellu

Pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) vytvořte nastavení diagnostiky s [Azure PowerShell](../powershell-samples.md). Popis jeho parametrů naleznete v dokumentaci k této rutině.

> [!IMPORTANT]
> Tuto metodu nelze použít pro protokol aktivit Azure. Místo toho použijte [Vytvoření nastavení diagnostiky v Azure monitor pomocí šablony Správce prostředků](./resource-manager-diagnostic-settings.md) k vytvoření šablony Správce prostředků a jejím nasazením pomocí prostředí PowerShell.

Následuje příklad rutiny prostředí PowerShell pro vytvoření nastavení diagnostiky pomocí všech tří cílů.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Vytvoření pomocí Azure CLI

Pomocí příkazu [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) vytvořte nastavení diagnostiky pomocí [Azure CLI](/cli/azure/monitor). Popis jeho parametrů naleznete v dokumentaci k tomuto příkazu.

> [!IMPORTANT]
> Tuto metodu nelze použít pro protokol aktivit Azure. Místo toho použijte možnost [vytvořit nastavení diagnostiky v Azure monitor pomocí šablony Správce prostředků](./resource-manager-diagnostic-settings.md) k vytvoření šablony Správce prostředků a nasaďte ji pomocí rozhraní příkazového řádku.

Následuje příklad příkazu CLI pro vytvoření nastavení diagnostiky pomocí všech tří cílů.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-resource-manager-template"></a>Vytvoření pomocí šablony Správce prostředků
V tématu [Správce prostředků ukázek šablon pro nastavení diagnostiky v Azure monitor](./resource-manager-diagnostic-settings.md) můžete vytvořit nebo aktualizovat nastavení diagnostiky se šablonou správce prostředků.

## <a name="create-using-rest-api"></a>Vytvoření s použitím REST API
Pokud chcete vytvořit nebo aktualizovat nastavení diagnostiky pomocí [REST API Azure monitor](/rest/api/monitor/), přečtěte si téma [nastavení diagnostiky](/rest/api/monitor/diagnosticsettings) .

## <a name="create-using-azure-policy"></a>Vytvořit pomocí Azure Policy
Vzhledem k tomu, že pro každý prostředek Azure je třeba vytvořit diagnostické nastavení, Azure Policy lze použít k automatickému vytvoření nastavení diagnostiky při vytvoření každého prostředku. Podrobnosti najdete v tématu [nasazení Azure monitor ve velkém rozsahu pomocí Azure Policy](../deploy-scale.md) .

## <a name="metric-category-is-not-supported-error"></a>Kategorie metriky není podporována. Chyba
Při nasazování nastavení diagnostiky se zobrazí následující chybová zpráva:

   Kategorie metriky *xxxx* není podporovaná.

Například: 

   Kategorie metriky ActionsFailed není podporovaná.

kam bylo předchozí nasazení úspěšné. 

K tomuto problému dochází při použití šablony Správce prostředků, nastavení diagnostiky REST API, rozhraní příkazového řádku Azure CLI nebo Azure PowerShell. Nastavení diagnostiky vytvořená prostřednictvím Azure Portal nejsou ovlivněna, protože jsou uvedeny pouze podporované názvy kategorií.

Problém je způsoben poslední změnou v základním rozhraní API. Jiné kategorie metrik než ' AllMetrics ' nejsou podporovány a nikdy nebyly s výjimkou několika velmi specifických služeb Azure. V minulosti byly při nasazení nastavení diagnostiky ignorovány další názvy kategorií. Azure Monitor back-end jednoduše přesměrují tyto kategorie na "AllMetrics".  Od února 2021 byl back-end aktualizován, aby konkrétně ověřil, že poskytnutá kategorie metrik je přesná. Tato změna způsobila selhání některých nasazení.

Pokud se zobrazí tato chyba, aktualizujte nasazení tak, aby nahradila všechny názvy kategorií metriky pomocí ' AllMetrics ', aby se problém vyřešil. Pokud nasazení dříve přidalo více kategorií, měla by být zachována pouze jedna z nich s odkazem ' AllMetrics '. Pokud budete tento problém mít i nadále, kontaktujte prosím podporu Azure prostřednictvím Azure Portal. 



## <a name="next-steps"></a>Další kroky

- [Další informace o protokolech platforem Azure](./platform-logs-overview.md)