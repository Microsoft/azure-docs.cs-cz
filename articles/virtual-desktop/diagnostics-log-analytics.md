---
title: Windows Virtual Desktop Diagnostics Log Analytics – Azure
description: Jak používat Log Analytics s funkcí diagnostiky virtuálních klientů Windows
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8b696b175c4ef841eef1a51f1d357d1781cba7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018286"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Použití Log Analytics pro diagnostickou funkci

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).

Virtuální plocha Windows používá [Azure monitor](../azure-monitor/overview.md) pro monitorování a výstrahy, jako je mnoho dalších služeb Azure. Správci tak mají jedno rozhraní, ve kterém můžou zjišťovat problémy. Služba vytvoří protokoly aktivit pro uživatele i pro akce správy. Každý protokol aktivit spadá do následujících kategorií:

- Aktivity správy:
    - Můžete sledovat, jestli pokusy o změnu objektů virtuálních klientů Windows pomocí rozhraní API nebo PowerShellu jsou úspěšné. Může například někdo úspěšně vytvořit fond hostitelů pomocí PowerShellu?
- Kanálu
    - Můžou se uživatelé úspěšně přihlásit k odběru pracovních prostorů?
    - Uvidí uživatelé všechny prostředky publikované v klientovi vzdálené plochy?
- Připojení:
    - Když uživatelé zahájí a dokončí připojení ke službě.
- Registrace hostitele:
    - Byl hostitel relace úspěšně zaregistrován u služby při připojení?
- Vyskytl
    - Narazí uživatelé na nějaké problémy s konkrétními aktivitami? Tato funkce může vygenerovat tabulku, která sleduje data aktivity za vás, dokud jsou informace spojeny s aktivitami.
- Kontrolní body
    - Konkrétní kroky při životním cyklu aktivity, která byla dosažena. Během relace se například uživatel vyrovnává jako vyrovnaný k určitému hostiteli, potom byl během připojení přihlášen a tak dále.

Připojení, která nedosáhnou virtuálního klienta Windows, se nezobrazí ve výsledcích diagnostiky, protože samotná služba role diagnostiky je součástí virtuálního klienta Windows. Problémy s připojením k virtuálnímu počítači s Windows se můžou vyskytnout, když uživatel dochází k problémům se síťovým připojením.

Azure Monitor umožňuje analyzovat data virtuálních klientů Windows a sledovat čítače výkonu virtuálních počítačů (VM), a to vše v rámci stejného nástroje. V tomto článku se dozvíte víc o tom, jak povolit diagnostiku pro prostředí virtuálních počítačů s Windows.

>[!NOTE]
>Informace o tom, jak monitorovat virtuální počítače v Azure, najdete v tématu [monitorování virtuálních počítačů Azure pomocí Azure monitor](../azure-monitor/insights/monitor-vm-azure.md). Nezapomeňte také [zkontrolovat prahové hodnoty čítače výkonu](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) , abyste lépe pochopili uživatelské prostředí na hostiteli relace.

## <a name="before-you-get-started"></a>Než začnete

Než budete moct použít Log Analytics, budete muset vytvořit pracovní prostor. Provedete to podle pokynů v jednom z následujících dvou článků:

- Pokud dáváte přednost použití Azure Portal, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Pokud dáváte přednost prostředí PowerShell, přečtěte si téma [vytvoření log Analyticsho pracovního prostoru pomocí prostředí PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

Po vytvoření pracovního prostoru postupujte podle pokynů v tématu [připojení počítačů se systémem Windows k Azure monitor](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) k získání následujících informací:

- ID pracovního prostoru
- Primární klíč vašeho pracovního prostoru

Tyto informace budete potřebovat později v procesu instalace.

Nezapomeňte si projít správu oprávnění pro Azure Monitor, abyste povolili přístup k datům pro uživatele, kteří monitorují a udržují prostředí virtuálních počítačů s Windows. Další informace najdete v tématu [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md).

## <a name="push-diagnostics-data-to-your-workspace"></a>Vložení diagnostických dat do pracovního prostoru

Diagnostická data můžete ze svých objektů virtuálních ploch Windows nahrajte do Log Analytics pro váš pracovní prostor. Tuto funkci můžete nastavit hned při prvním vytvoření svých objektů.

Nastavení Log Analytics nového objektu:

1. Přihlaste se k Azure Portal a přejít na **virtuální plochu Windows**.

2. Přejděte na objekt (například fond hostitelů, skupinu aplikací nebo pracovní prostor), pro který chcete zachytit protokoly a události.

3. V nabídce na levé straně obrazovky vyberte **nastavení diagnostiky** .

4. V nabídce, která se zobrazí na pravé straně obrazovky, vyberte **Přidat nastavení diagnostiky** .

    Možnosti zobrazené na stránce nastavení diagnostiky se budou lišit v závislosti na tom, jaký typ objektu upravujete.

    Pokud například povolíte diagnostiku pro skupinu aplikací, zobrazí se možnosti konfigurace kontrolních bodů, chyb a správy. U pracovních prostorů tyto kategorie konfigurují informační kanál, který se bude sledovat, když se uživatelé přihlásí k odběru seznamu aplikací. Další informace o nastavení diagnostiky najdete [v tématu Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik prostředků v Azure](../azure-monitor/platform/diagnostic-settings.md).

     >[!IMPORTANT]
     >Nezapomeňte povolit diagnostiku pro každý objekt Azure Resource Manager, který chcete monitorovat. Data budou k dispozici pro aktivity po povolení diagnostiky. Po prvním nastavení může trvat několik hodin.

5. Zadejte název konfigurace nastavení a pak vyberte **Odeslat do Log Analytics**. Název, který použijete, by neměl obsahovat mezery a měl by odpovídat [konvencím pojmenování Azure](../azure-resource-manager/management/resource-name-rules.md). V rámci protokolů můžete vybrat všechny možnosti, které chcete přidat do Log Analytics, jako je například kontrolní bod, chyba, Správa a tak dále.

6. Vyberte **Uložit**.

>[!NOTE]
>Log Analytics vám poskytne možnost streamovat data do [Event Hubs](../event-hubs/event-hubs-about.md) nebo archivovat v účtu úložiště. Další informace o této funkci najdete v tématu [streamování dat monitorování Azure do centra událostí](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) a [archivaci protokolů prostředků Azure do účtu úložiště](../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

## <a name="how-to-access-log-analytics"></a>Přístup k Log Analytics

K Log Analytics pracovním prostorům můžete přistupovat Azure Portal nebo Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Přístup k Log Analytics v pracovním prostoru Log Analytics

1. Přihlaste se k webu Azure Portal.

2. Vyhledejte **Log Analytics pracovní prostor**.

3. V části služby vyberte **Log Analytics pracovní prostory**.

4. V seznamu vyberte pracovní prostor, který jste nakonfigurovali pro objekt virtuálního počítače s Windows.

5. V pracovním prostoru vyberte **protokoly**. Seznam nabídek můžete vyfiltrovat pomocí **vyhledávací** funkce.

### <a name="access-log-analytics-on-azure-monitor"></a>Přístup k Log Analytics v Azure Monitor

1. Přihlášení k webu Azure Portal

2. Vyhledejte a vyberte **monitor**.

3. Vyberte **Protokoly**.

4. Podle pokynů na stránce protokolování nastavte obor dotazu.

5. Jste připraveni na dotaz na diagnostiku. Všechny tabulky diagnostiky mají předponu "WVD".

>[!NOTE]
>Podrobnější informace o tabulkách uložených v protokolech Azure Monitor najdete v části [Azure monitor data](/azure/azure-monitor/reference/). Všechny tabulky související s virtuálním počítačem s Windows jsou označené jako "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Tempo pro odesílání diagnostických událostí

Události diagnostiky se odesílají do Log Analytics, když se dokončí.

Pro aktivity připojení Log Analytics jenom sestavy v těchto zprostředkujících stavech:

- Zahájeno: když uživatel vybere a připojí se k aplikaci nebo počítači v klientovi vzdálené plochy.
- Připojeno: když se uživatel úspěšně připojí k virtuálnímu počítači, na kterém je aplikace nebo plocha hostovaná.
- Dokončeno: když uživatel nebo server odpojí relaci, na kterou se aktivita uskutečnila.

## <a name="example-queries"></a>Ukázky dotazů

Přístup k ukázkovým dotazům prostřednictvím uživatelského rozhraní Azure Monitor Log Analytics:
1. Otevřete pracovní prostor Log Analytics a pak vyberte **protokoly**. Ukázkové uživatelské rozhraní dotazu se zobrazí automaticky.
1. Změňte filtr na **kategorie**.
1. Pokud chcete zkontrolovat dostupné dotazy, vyberte **virtuální počítač s Windows** .
1. Zvolením **příkazu Spustit** spusťte vybraný dotaz.

Přečtěte si další informace o rozhraní ukázkových dotazů v [uložených dotazech v Azure Monitor Log Analytics](../azure-monitor/log-query/example-queries.md).

Následující seznam dotazů vám umožní zkontrolovat informace o připojení a problémy pro jednoho uživatele. Tyto dotazy můžete spustit v [Editoru dotazů Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query). Pro každý dotaz nahraďte `userupn` hlavní název uživatele (UPN), kterého chcete vyhledat.


Vyhledání všech připojení pro jednoho uživatele:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


Zjištění počtu připojení uživatele za den:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

Postup zjištění trvání relace podle uživatele:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

Chcete-li najít chyby pro určitého uživatele:

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

Zjistit, zda došlo k určité chybě pro jiné uživatele:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- Když uživatel otevře celou plochu, jejich využití aplikace v relaci není sledováno jako kontrolní body v tabulce WVDCheckpoints.
>- Sloupec ResourcesAlias v tabulce WVDConnections ukazuje, jestli se uživatel připojil k celé ploše nebo publikované aplikaci. V tomto sloupci se zobrazuje jenom první aplikace, kterou otevřel během připojení. Všechny publikované aplikace, které uživatel otevře, jsou sledovány v WVDCheckpoints.
>- V tabulce WVDErrors se zobrazují chyby správy, problémy s registrací hostitele a další problémy, ke kterým dochází, když se uživatel přihlásí k odběru seznamu aplikací nebo stolních počítačů.
>- WVDErrors vám pomůže identifikovat problémy, které mohou být vyřešeny úlohami správce. Hodnota v ServiceError vždy říká "false" pro tyto typy problémů. Pokud ServiceError = "true", budete muset problém vyřešit společnosti Microsoft. Ujistěte se, že jste zadali ID korelace pro chyby, které chcete eskalovat.

## <a name="next-steps"></a>Další kroky

Pokud si chcete projít běžné chybové scénáře, které vám diagnostická funkce může zjistit, přečtěte si téma [identifikace a Diagnostika problémů](diagnostics-role-service.md#common-error-scenarios).