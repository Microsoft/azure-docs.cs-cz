---
title: Sledování naplánovaných událostí pro virtuální počítače s Windows v Azure
description: Přečtěte si, jak monitorovat virtuální počítače Azure pro naplánované události.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073319"
---
# <a name="monitoring-scheduled-events"></a>Sledování plánovaných událostí

Aktualizace se používají pro různé části Azure každý den, aby služby spuštěné na nich bezpečné a aktuální. Kromě plánovaných aktualizací může dojít také k neplánovaným událostem. Například pokud je zjištěna jakékoli snížení zabezpečení hardwaru nebo poruchy, služby Azure může být nutné provést neplánovanou údržbu. Použití migrace za provozu, zachování paměti aktualizace a obecně zachování přísné pruh na dopad aktualizací, ve většině případů tyto události jsou téměř transparentní pro zákazníky, a nemají žádný vliv nebo nanejvýš způsobit několik sekund virtuálního počítače zmrazit. Však pro některé aplikace i několik sekund zmrazení virtuálního počítače může způsobit dopad. Vědět předem o nadcházející údržbě Azure je důležité, aby bylo zajištěno nejlepší prostředí pro tyto aplikace. [Služba Plánované události](scheduled-events.md) poskytuje programové rozhraní, které má být upozorňováno na nadcházející údržbu, a umožňuje vám řádně zpracovat údržbu. 

V tomto článku ukážeme, jak můžete použít naplánované události, které mají být upozorňovány na události údržby, které by mohly mít vliv na vaše virtuální počítače a vytvořit některé základní automatizace, které vám pomohou s monitorováním a analýzou.


## <a name="routing-scheduled-events-to-log-analytics"></a>Směrování naplánovaných událostí do log analytics

Naplánované události jsou dostupné jako součást [služby metadat instance Azure](instance-metadata-service.md), která je dostupná na každém virtuálním počítači Azure. Zákazníci mohou psát automatizace dotaz u koncového bodu svých virtuálních počítačů najít naplánované oznámení o údržbě a provádět skutečnosti snižující závažnost rizika, jako je uložení stavu a odebrání virtuálního počítače z rotace. Doporučujeme automatizaci vytváření budov pro záznam naplánovaných událostí, abyste mohli mít protokol auditování událostí údržby Azure. 

V tomto článku vás provedeme, jak zachytit naplánované události údržby do analýzy protokolu. Potom spustíme některé základní akce oznámení, jako je odeslání e-mailu vašemu týmu a získání historického zobrazení všech událostí, které ovlivnily vaše virtuální počítače. Pro agregaci událostí a automatizaci budeme používat [Log Analytics](/azure/azure-monitor/learn/quick-create-workspace), ale můžete použít libovolné řešení monitorování shromažďovat tyto protokoly a aktivaci automatizace.

![Diagram znázorňující životní cyklus události](./media/notifications/events.png)

## <a name="prerequisites"></a>Požadavky

V tomto příkladu budete muset vytvořit [virtuální počítač se systémem Windows v sadě dostupnosti](tutorial-availability-sets.md). Naplánované události poskytují oznámení o změnách, které mohou ovlivnit libovolný virtuální počítač ve vaší sadě dostupnosti, cloudové službě, škálovací sadě virtuálních počítačů nebo samostatných virtuálních počítačích. Budeme spouštět [službu,](https://github.com/microsoft/AzureScheduledEventsService) která dotazování pro plánované události na jednom z virtuálních účtů, které budou fungovat jako kolektor, získat události pro všechny ostatní virtuální chody v sadě dostupnosti.    

Neodstraňujte skupinu prostředků skupiny na konci kurzu.

Budete také muset [vytvořit pracovní prostor Log Analytics,](/azure/azure-monitor/learn/quick-create-workspace) který použijeme k agregaci informací z virtuálních aplikací v sadě dostupnosti.

## <a name="set-up-the-environment"></a>Nastavení prostředí

Teď byste měli mít 2 počáteční virtuální chod v sadě dostupnosti. Teď musíme vytvořit třetí virtuální virtuální byl s názvem myCollectorVM ve stejné sadě dostupnosti. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Stáhněte si instalační soubor ZIP projektu z [GitHubu](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Připojte se k **myCollectorVM** a zkopírujte soubor ZIP do virtuálního počítače a extrahujte všechny soubory. Na virtuálním počítači otevřete výzvu powershellu. Přesuňte výzvu do `SchService.ps1`složky obsahující `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`například: a nastavte službu.

```powershell
.\SchService.ps1 -Setup
```

Spusťte službu.

```powershell
.\SchService.ps1 -Start
```

Služba nyní spustí dotazování každých 10 sekund pro všechny naplánované události a schválit události k urychlení údržby.  Zmrazit, restartovat, znovu nasadit a Preempt jsou události zachycené událostmi plánu.   Všimněte si, že můžete rozšířit skript aktivovat některé skutečnosti snižující závažnost rizika před schválením události.

Ověřte stav služby a ujistěte se, že je spuštěna.

```powershell
.\SchService.ps1 -status  
```

To by `Running`se mělo vrátit .

Služba nyní spustí dotazování každých 10 sekund pro všechny naplánované události a schválit události k urychlení údržby.  Zmrazit, restartovat, znovu nasadit a Preempt jsou události zachycené událostmi plánu. Skript můžete rozšířit tak, aby před schválením události aktivoval některé skutečnosti snižující závažnost rizika.

Pokud jsou některé z výše uvedených událostí zachyceny službou Událost plánu, budou zaznamenány ve stavu události protokolu událostí aplikace, typu události, prostředcích (názvy virtuálních počítačů) a NotBefore (minimální výpovědní lhůta). Události s ID 1234 můžete najít v protokolu událostí aplikace.

Jakmile je služba nastavena a spuštěna, bude protokolovat události v protokolech aplikací systému Windows.   Chcete-li ověřit, že to funguje, restartujte jeden z virtuálních počítačů v sadě dostupnosti a měli byste vidět událost protokolovaná v prohlížeči událostí v protokolech Windows > protokol aplikace zobrazující restartování virtuálního počítače. 

![Snímek obrazovky prohlížeče událostí](./media/notifications/event-viewer.png)

Když jsou události zachyceny službou Událost plánu, budou zaznamenány v aplikaci i protokolovat se stavem události, typem události, prostředky (název virtuálního zařízení) a NotBefore (minimální výpovědní doba). Události s ID 1234 můžete najít v protokolu událostí aplikace.

> [!NOTE] 
> V tomto příkladu virtuální počítače byly v sadě dostupnosti, která nám umožnila určit jeden virtuální počítač jako kolektor pro naslouchání a směrování naplánovaných událostí do našeho pracovního místa analýzy protokolů. Pokud máte samostatné virtuální počítače, můžete spustit službu na každém virtuálním počítači a pak je jednotlivě připojit k pracovnímu prostoru analýzy protokolů.
>
> Pro naše nastavení jsme zvolili Windows, ale můžete navrhnout podobné řešení na Linuxu.

Kdykoli můžete zastavit/odebrat službu naplánované události `–stop` pomocí `–remove`přepínačů a .

## <a name="connect-to-the-workspace"></a>Připojení k pracovnímu prostoru


Nyní chceme připojit pracovní prostor Analýzy protokolů k virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu zařízení kolektoru. Pracovní prostor Log Analytics funguje jako úložiště a nakonfigurujeme kolekci protokolu událostí tak, aby zaznamenávala protokoly aplikací z virtuálního počítače kolektoru. 

 Chcete-li směrovat naplánované události do protokolu událostí, který bude uložen jako protokol aplikací naší službou, budete muset připojit virtuální počítač k pracovnímu prostoru Log Analytics.  
 
1. Otevřete stránku pracovního prostoru, který jste vytvořili.
1. V části **Připojení ke zdroji dat** vyberte virtuální počítače Azure **(VM).**

    ![Připojení k virtuálnímu virtuálnímu virtuálnímu zařízení jako zdroji dat](./media/notifications/connect-to-data-source.png)

1. Vyhledejte a vyberte **myCollectorVM**. 
1. Na nové stránce **myCollectorVM**vyberte **připojit**.

Tím nainstalujete [agenta Microsoft Monitoring](/azure/virtual-machines/extensions/oms-windows) ve vašem virtuálním počítači. Připojení virtuálního počítače k pracovnímu prostoru a instalace rozšíření bude trvat několik minut. 

## <a name="configure-the-workspace"></a>Konfigurace pracovního prostoru

1. Otevřete stránku pracovního prostoru a vyberte **Upřesnit nastavení**.
1. V levé nabídce vyberte **Data** a potom vyberte **Protokoly událostí systému Windows**.
1. V **části Shromáždit z následujících protokolů událostí**začněte zapínat *aplikaci* a ze seznamu vyberte **Možnost Aplikace.**

    ![Vybrat upřesnit nastavení](./media/notifications/advanced.png)

1. Ponechte **vybranou možnost Ponechat vybranou chybu,** **UPOZORNĚNÍ**a **INFORMACE** a potom výběrem **možnosti Uložit** nastavení uložte.


> [!NOTE]
> Dojde k určitému zpoždění a může trvat až 10 minut, než bude protokol k dispozici. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Vytvoření pravidla výstrahy pomocí Azure Monitoru 


Po přepnutí událostí do analýzy protokolů můžete spustit následující [dotaz](/azure/azure-monitor/log-query/get-started-portal) a vyhledat události plánu.

1. V horní části stránky vyberte **Protokoly** a do textového pole vložte následující:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Vyberte **Uložit**a zadejte *logQuery* pro název, ponechejte **dotaz** jako typ, zadejte *VMLogs* jako **kategorii**a pak vyberte **Uložit**. 

    ![Uložení dotazu](./media/notifications/save-query.png)

1. Vyberte **Nové pravidlo upozornění**. 
1. Na stránce **Vytvořit** pravidlo `collectorworkspace` ponechte jako **zdroj**.
1. V **části Podmínka**vyberte položku *Vždy, když je <login undefined>hledání v protokolu zákazníka *. Otevře se stránka **Logika signálu Konfigurace.**
1. Pod **prahovou hodnotou**zadejte *hodnotu 0* a pak vyberte **Hotovo**.
1. V části **Akce**vyberte **Vytvořit skupinu akcí**. Otevře se stránka **Přidat skupinu akcí.**
1. Do **názvu skupiny Akce**zadejte příkaz *myActionGroup*.
1. Do **pole Krátký název**zadejte příkaz **myActionGroup**.
1. Ve **skupině Resource**vyberte **položku myResourceGroupAvailability**.
1. V části Akce v **části NÁZEV AKCE** vyberte **e-mail**a pak vyberte **možnost E-mail/SMS/Push/Voice**. Otevře se stránka **E-mail/SMS/Push/Voice.**
1. Vyberte **E-mail**, zadejte svou e-mailovou adresu a pak vyberte **OK**.
1. Na stránce **Přidat skupinu akcí** vyberte **OK**. 
1. Na stránce **Vytvořit pravidlo** zadejte v části **PODROBNOSTI VÝSTRAHY** *příkaz myAlert* pro **název pravidla výstrahy**a potom zadejte *pravidlo upozornění e-mailem* pro **popis**.
1. Po dokončení vyberte **Vytvořit pravidlo výstrahy**.
1. Restartujte jeden z virtuálních počítačů v sadě dostupnosti. Během několika minut byste měli dostat e-mail, že výstraha byla spuštěna.

Pokud chcete spravovat pravidla výstrah, přejděte do skupiny prostředků, v levé nabídce vyberte **Upozornění** a v horní části stránky vyberte **Spravovat pravidla výstrah.**

     
## <a name="next-steps"></a>Další kroky

Další informace najdete na stránce [služby Naplánované události](https://github.com/microsoft/AzureScheduledEventsService) na GitHubu.
