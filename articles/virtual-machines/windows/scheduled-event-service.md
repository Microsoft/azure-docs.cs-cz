---
title: Monitorování naplánovaných událostí pro virtuální počítače s Windows v Azure
description: Naučte se monitorovat virtuální počítače Azure pro plánované události.
author: mysarn
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 0806c6e0ed89c2c0f4712ec985599810119fcf89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999016"
---
# <a name="monitoring-scheduled-events"></a>Scheduled Events monitorování

Aktualizace se každý den aplikují na různé části Azure, aby byly služby v nich spuštěné v bezpečí a aktuální. Kromě plánovaných aktualizací může dojít také k neplánovaným událostem. Například pokud dojde k selhání nějakého hardwarového snížení nebo chyby, služby Azure můžou potřebovat neplánovanou údržbu. Pomocí migrace za provozu, zachovávání paměti při zachovávání aktualizací a obecně udržuje striktní pruh o dopadu aktualizací. ve většině případů jsou tyto události téměř transparentní pro zákazníky a nemají žádný dopad nebo většinou způsobují několik sekund zablokování virtuálního počítače. U některých aplikací ale může dojít k ovlivnění i několika sekund zablokování virtuálního počítače. Důležité informace o nadcházející údržbě Azure jsou důležité, aby se zajistilo, že budou tyto aplikace co nejlepší. [Služba Scheduled Events](scheduled-events.md) poskytuje programové rozhraní, které bude dostávat oznámení o nadcházející údržbě, a umožňuje provádět řádné zpracování údržby. 

V tomto článku se dozvíte, jak můžete použít naplánované události k oznámení o událostech údržby, které by mohly mít vliv na vaše virtuální počítače, a sestavit základní automatizaci, která může pomáhat s monitorováním a analýzou.


## <a name="routing-scheduled-events-to-log-analytics"></a>Směrování plánovaných událostí do Log Analytics

Scheduled Events je k dispozici jako součást [Azure instance metadata Service](instance-metadata-service.md), která je k dispozici na všech virtuálních počítačích Azure. Zákazníci můžou psát Automation pro dotazování koncového bodu svých virtuálních počítačů, aby našli naplánovaná oznámení o údržbě a aby prováděli zmírnění rizik, jako je uložení stavu a přepnutí virtuálního počítače mimo rotaci. Doporučujeme, abyste sestavili automatizaci pro záznam Scheduled Events, abyste mohli mít protokol auditování událostí údržby Azure. 

V tomto článku Vás provedeme procesem zaznamenání údržby Scheduled Events k Log Analytics. Pak budeme aktivovat některé základní akce oznámení, jako je odeslání e-mailu týmu a získání historických přehledů o všech událostech, které ovlivnily vaše virtuální počítače. Pro agregaci a automatizaci událostí budeme používat [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md), ale můžete použít jakékoli řešení monitorování ke shromáždění těchto protokolů a aktivaci automatizace.

![Diagram znázorňující životní cyklus události](./media/notifications/events.png)

## <a name="prerequisites"></a>Požadavky

V tomto příkladu budete muset vytvořit [virtuální počítač s Windows ve skupině dostupnosti](tutorial-availability-sets.md). Scheduled Events poskytují oznámení o změnách, které můžou ovlivnit některý z virtuálních počítačů ve vaší skupině dostupnosti, cloudové službě, sadě škálování virtuálních počítačů nebo samostatných virtuálních počítačích. Budeme používat [službu](https://github.com/microsoft/AzureScheduledEventsService) , která se dotazuje na naplánované události na jednom z virtuálních počítačů, které se budou chovat jako kolektor, a získat tak události pro všechny ostatní virtuální počítače ve skupině dostupnosti.    

Neodstraňujte skupinu prostředků skupiny na konci tohoto kurzu.

Budete také muset [vytvořit Log Analytics pracovní prostor](../../azure-monitor/learn/quick-create-workspace.md) , který použijeme k agregaci informací z virtuálních počítačů ve skupině dostupnosti.

## <a name="set-up-the-environment"></a>Nastavení prostředí

Teď byste měli mít 2 počáteční virtuální počítače ve skupině dostupnosti. Nyní musíme ve stejné skupině dostupnosti vytvořit třetí virtuální počítač s názvem myCollectorVM. 

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
 

Stáhněte si soubor instalace. zip projektu z [GitHubu](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Připojte se k **myCollectorVM** a zkopírujte soubor. zip do virtuálního počítače a Extrahujte všechny soubory. Na svém VIRTUÁLNÍm počítači otevřete příkazový řádek PowerShellu. Přesuňte výzvu do složky, která obsahuje `SchService.ps1` , například: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>` a nastavte službu.

```powershell
.\SchService.ps1 -Setup
```

Spusťte službu.

```powershell
.\SchService.ps1 -Start
```

Služba se teď začne dotazovat každých 10 sekund na jakékoli naplánované události a schválí události, aby se tato údržba urychlila.  Zablokování, restartování, opětovné nasazení a přerušení jsou události zachycené událostmi plánování.   Všimněte si, že můžete skript roztáhnout, aby před schválením události aktivoval některé zmírnění.

Ověřte stav služby a ujistěte se, že je spuštěný.

```powershell
.\SchService.ps1 -status  
```

Mělo by se vrátit `Running` .

Služba se teď začne dotazovat každých 10 sekund na jakékoli naplánované události a schválí události, aby se tato údržba urychlila.  Zablokování, restartování, opětovné nasazení a přerušení jsou události zachycené událostmi plánování. Před schválením události můžete skript roztáhnout, aby se aktivovaly některé zmírnění.

Pokud je některá z výše uvedených událostí zachycena službou Event Service, bude zaznamenána do stavu události protokolu událostí aplikace, typu události, prostředků (názvů virtuálních počítačů) a NotBefore (minimální doba oznámení). Události s ID 1234 můžete najít v protokolu událostí aplikace.

Jakmile je služba nastavená a spuštěná, bude protokolovat události v protokolech aplikací pro Windows.   Pokud to chcete ověřit, restartujte jeden z virtuálních počítačů ve skupině dostupnosti a v > protokolech systému Windows by se měla zobrazit událost, která se zaznamenala k restartování virtuálního počítače. 

![Snímek obrazovky prohlížeče událostí.](./media/notifications/event-viewer.png)

Když služba Event Scheduler zachytí události, přihlásí se do protokolu události aplikace, a to s využitím stavu události, typu události, prostředků (název virtuálního počítače) a NotBefore (minimální doba oznámení). Události s ID 1234 můžete najít v protokolu událostí aplikace.

> [!NOTE] 
> V tomto příkladu jsou virtuální počítače ve skupině dostupnosti, což nám umožnilo určit jeden virtuální počítač jako kolektor k naslouchání a směrování naplánovaných událostí do služby Log Analytics funguje místo. Pokud máte samostatné virtuální počítače, můžete službu spustit na každém virtuálním počítači a pak je připojit jednotlivě k pracovnímu prostoru Log Analytics.
>
> Pro naše nastavení jsme zvolili Windows, ale můžete navrhnout podobné řešení na platformě Linux.

V jakémkoli okamžiku můžete zastavit nebo odebrat naplánovanou službu Event Service pomocí přepínačů `–stop` a `–remove` .

## <a name="connect-to-the-workspace"></a>Připojení k pracovnímu prostoru


Nyní chceme připojit Log Analytics pracovní prostor k virtuálnímu počítači kolektoru. Pracovní prostor Log Analytics funguje jako úložiště a my nakonfigurujeme shromažďování protokolů událostí pro zachycení protokolů aplikace z virtuálního počítače kolektoru. 

 Chcete-li směrovat Scheduled Events do protokolu událostí, který bude uložen jako protokol aplikace podle naší služby, budete muset připojit virtuální počítač k pracovnímu prostoru Log Analytics.  
 
1. Otevřete stránku pro pracovní prostor, který jste vytvořili.
1. V části **připojit ke zdroji dat** vyberte **virtuální počítače Azure (virtuální počítače)**.

    ![Připojení k virtuálnímu počítači jako zdroji dat](./media/notifications/connect-to-data-source.png)

1. Vyhledejte a vyberte **myCollectorVM**. 
1. Na nové stránce pro **myCollectorVM**vyberte **připojit**.

Tím se na virtuální počítač nainstaluje [Agent Microsoft Monitoring Agent](../extensions/oms-windows.md) . Připojení virtuálního počítače k pracovnímu prostoru a instalace rozšíření bude trvat několik minut. 

## <a name="configure-the-workspace"></a>Konfigurace pracovního prostoru

1. Otevřete stránku pro váš pracovní prostor a vyberte **Upřesnit nastavení**.
1. V nabídce vlevo vyberte **data** a pak vyberte **protokoly událostí systému Windows**.
1. V **části shromáždit z následujících protokolů událostí**začněte psát *aplikaci* a pak v seznamu vyberte **aplikace** .

    ![Vybrat upřesňující nastavení](./media/notifications/advanced.png)

1. Ponechte vybranou možnost **Chyba**, **Upozornění**a **informace** a pak vyberte **Uložit** . tím nastavení uložíte.


> [!NOTE]
> Dojde k prodlevě a může trvat až 10 minut, než bude protokol k dispozici. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Vytvoření pravidla výstrahy pomocí Azure Monitor 


Po vložení událostí do Log Analytics můžete spustit následující [dotaz](../../azure-monitor/log-query/get-started-portal.md) , který vyhledá události plánu.

1. V horní části stránky vyberte **protokoly** a vložte následující text do textového pole:

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

1. Vyberte **Uložit**a pak jako název zadejte *logQuery* , jako typ nechejte **dotaz** , jako **kategorii**zadejte *VMLogs* a pak vyberte **Save (Uložit**). 

    ![Uložit dotaz](./media/notifications/save-query.png)

1. Vyberte **Nové pravidlo upozornění**. 
1. Na stránce **vytvořit pravidlo** nechejte `collectorworkspace` jako **prostředek**.
1. V části **Podmínka**vyberte položku *vždy, když je <login undefined> hledání v protokolu zákazníka *. Otevře se stránka **Konfigurovat logiku signálu** .
1. V části **prahová hodnota**zadejte *0* a potom vyberte **Hotovo**.
1. V části **Akce**vyberte **vytvořit skupinu akcí**. Otevře se stránka **Přidat skupinu akcí** .
1. Do **název skupiny akcí**zadejte *myActionGroup*.
1. Do textu **krátký název**zadejte **myActionGroup**.
1. V **skupiny prostředků**vyberte **myResourceGroupAvailability**.
1. V části Akce zadejte do pole **název akce** **e-mail**a pak vyberte **e-mail/SMS/Push/Voice**. Otevře se stránka **e-mail/SMS/Push/Voice** .
1. Vyberte **e-mail**, zadejte e-mailovou adresu a pak vyberte **OK**.
1. Na stránce **Přidat skupinu akcí** vyberte **OK**. 
1. Na stránce **vytvořit pravidlo** v části **Podrobnosti výstrahy**zadejte *myAlert* pro **název pravidla upozornění**a pak zadejte *pravidlo e-mailové výstrahy* pro **Popis**.
1. Po dokončení vyberte **vytvořit pravidlo výstrahy**.
1. Restartujte jeden z virtuálních počítačů ve skupině dostupnosti. Během několika minut byste měli obdržet e-mail s aktivovaným upozorněním.

Chcete-li spravovat pravidla upozornění, přejděte do skupiny prostředků, v nabídce vlevo vyberte možnost **výstrahy** a v horní části stránky vyberte možnost **Spravovat pravidla výstrah** .

     
## <a name="next-steps"></a>Další kroky

Další informace najdete na stránce [Služba naplánované události](https://github.com/microsoft/AzureScheduledEventsService) na GitHubu.
