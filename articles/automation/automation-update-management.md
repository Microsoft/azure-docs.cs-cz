---
title: Řešení Update Management v Azure
description: Tento článek vám pomůže pochopit, jak používat řešení Azure Update Management ke správě aktualizací pro počítače se systémem Windows a Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 20d8106f06b708527fc60e025a19c6b07656acb3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102652"
---
# <a name="update-management-solution-in-azure"></a>Řešení Update Management v Azure

Řešení Update Management v Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače se systémem Windows a Linux v Azure, v místních prostředích nebo v jiných poskytovatelích cloudu. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Můžete povolit Update Management pro virtuální počítače přímo z účtu Azure Automation. Informace o tom, jak povolit Update Management pro virtuální počítače z vašeho účtu Automation, najdete v tématu [Správa aktualizací pro několik virtuálních počítačů](manage-update-multi.md). Můžete také povolit Update Management pro virtuální počítač ze stránky virtuálního počítače v Azure Portal. Tento scénář je k dispozici pro virtuální počítače se systémy [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) a [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) .

> [!NOTE]
> Řešení Update Management vyžaduje propojení pracovního prostoru Log Analytics s vaším účtem Automation. Konečný seznam podporovaných oblastí najdete v tématu [mapování pracovních prostorů Azure](./how-to/region-mappings.md). Mapování oblastí neovlivňují možnost spravovat virtuální počítače v samostatné oblasti, než je váš účet Automation.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Přehled řešení

Počítače spravované pomocí Update Management pro vyhodnocení a nasazení aktualizací používají následující konfigurace:

* Microsoft Monitoring Agent (MMA) pro Windows nebo Linux
* Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
* Funkci Hybrid Runbook Worker služby Automation
* Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače se systémem Windows

Následující diagram znázorňuje koncepční zobrazení chování a toku dat s tím, jak řešení vyhodnocuje a aplikuje aktualizace zabezpečení na všechny připojené počítače s Windows serverem a Linux v pracovním prostoru:

![Tok procesu Update Management](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management lze použít k nativně zprovoznění počítačů ve více předplatných ve stejném tenantovi.

Po uvolnění balíčku trvá 2-3 hodin, než se oprava zobrazí pro počítače se systémem Linux pro posouzení. U počítačů s Windows trvá 12-15 hodin, než se oprava zobrazí až po jejím vydání.

Když počítač dokončí kontrolu shody aktualizací, agent přepošle informace hromadně do Azure Monitor protokolů. V počítači se systémem Windows se kontrola kompatibility ve výchozím nastavení spouští každých 12 hodin.

Kromě plánu vyhledávání se kontrola kompatibility aktualizací zahájila do 15 minut od restartování MMA, před instalací aktualizace a po instalaci aktualizace.

U počítače se systémem Linux se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud se agent MMA restartuje, zahájí se kontrola kompatibility do 15 minut.

Řešení informuje o tom, jak aktuální počítač vychází ze zdroje, se kterým jste nakonfigurovali synchronizaci. Pokud je počítač s Windows nakonfigurovaný tak, aby hlásil službu WSUS, v závislosti na tom, kdy se služba WSUS naposledy synchronizoval s Microsoft Update, se výsledky můžou lišit od toho, co Microsoft Update zobrazuje. Toto chování je stejné pro počítače se systémem Linux, které jsou nakonfigurovány k hlášení do místního úložiště místo do veřejného úložiště.

> [!NOTE]
> Aby bylo možné řádně ohlásit službu, Update Management vyžaduje, aby byly povoleny určité adresy URL a porty. Další informace o těchto požadavcích najdete v tématu [Plánování sítě pro hybridní pracovní procesy](automation-hybrid-runbook-worker.md#network-planning).

Na počítače, které vyžadují aktualizace softwaru, můžete tyto aktualizace nasadit a nainstalovat tak, že vytvoříte plánované nasazení. Aktualizace klasifikované jako *volitelné* nejsou zahrnuty v oboru nasazení pro počítače se systémem Windows. V rozsahu nasazení jsou zahrnuté jenom požadované aktualizace.

Plánované nasazení definuje, které cílové počítače obdrží příslušné aktualizace, buď explicitním zadáním počítačů, nebo výběrem [skupiny počítačů](../azure-monitor/platform/computer-groups.md) , která je založená na hledání protokolu konkrétní sady počítačů, nebo pomocí [dotazu Azure](#azure-machines) . Tím se dynamicky vybraly virtuální počítače Azure na základě zadaných kritérií. Tyto skupiny se liší od [Konfigurace oboru](../azure-monitor/insights/solution-targeting.md), která se používá pouze k určení, které počítače získávají sady Management Pack umožňující řešení.

Také zadáte plán, který schválíte a nastavíte časový úsek, během kterého lze aktualizace nainstalovat. Tato doba se nazývá časový interval pro správu a údržbu. Pokud je restartování nutné a vybrali jste vhodnou možnost restartování, je v časovém intervalu pro správu a údržbu vyrezervováno deset minut. Pokud provádění oprav trvá déle, než bylo očekáváno, a v časovém intervalu údržby je méně než deset minut, nebude k dispozici restart.

Aktualizace se instalují podle runbooků ve službě Azure Automation. Tyto Runbooky nemůžete zobrazit a runbooky nevyžadují žádnou konfiguraci. Když se vytvoří nasazení aktualizace, vytvoří nasazení aktualizace plán, který spustí v zadaném čase pro zahrnuté počítače sadu Master Update Runbook. Hlavní runbook spouští podřízený Runbook na každém agentovi pro instalaci požadovaných aktualizací.

V datu a čase zadaném v nasazení aktualizace spustí cílové počítače paralelně nasazení. Před instalací se spustí Kontrola, aby se ověřilo, že se aktualizace pořád vyžadují. V případě klientských počítačů služby WSUS, pokud aktualizace nejsou schváleny ve službě WSUS, nasazení aktualizace se nezdařilo.

Počítač zaregistrovaný pro Update Management ve více než jednom pracovním prostoru Log Analytics (více domovských) se nepodporuje.

## <a name="clients"></a>Klienti

### <a name="supported-client-types"></a>Podporované klientské typy

V následující tabulce je uveden seznam podporovaných operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM a SP1 Standard)|**Posouzení aktualizací**: Podporováno<br><br>Probíhá **Oprava**: Vyžaduje Hybrid Runbook Worker. Viz [požadavky na Hybrid Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker)|
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Oprava založená na klasifikaci vyžaduje, aby příkaz "Yumu" vracel data zabezpečení, která CentOS nejsou v poli. Další informace o opravách na základě klasifikace v CentOS najdete v tématu [klasifikace aktualizací v systému Linux](#linux-2) .          |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14,04 LTS, 16,04 LTS a 18,04 (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

> [!NOTE]
> Sady škálování virtuálních počítačů Azure je možné spravovat pomocí Update Management. Update Management pracuje na samotných instancích a ne na základní imagi. Aktualizace budete muset naplánovat přírůstkově, protože neaktualizujete všechny instance virtuálních počítačů najednou.
> Uzly VMSS se dají přidat pomocí následujících kroků v části [onbaord a non-Azure Machine](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Nepodporované klientské typy

Následující tabulka uvádí operační systémy, které nejsou podporovány:

|Operační systém  |Poznámky  |
|---------|---------|
|Klient Windows     | Klientské operační systémy (například Windows 7 a Windows 10) nejsou podporovány.        |
|Windows Server 2016 Nano Server     | Nepodporuje se.       |
|Uzly služby Azure Kubernetes | Nepodporuje se. Použijte postup opravy podrobně popsaný v části [použití aktualizací zabezpečení a jádra pro uzly Linux ve službě Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md) .|

### <a name="client-requirements"></a>Požadavky na klienta

#### <a name="windows"></a>Windows

Agenti systému Windows musí být nakonfigurováni pro komunikaci se serverem WSUS nebo musí mít přístup k Microsoft Update. Update Management můžete použít s System Center Configuration Manager. Další informace o integračních scénářích najdete v tématu věnovaném [integraci System Center Configuration Manager s Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). [Agent pro Windows](../azure-monitor/platform/agent-windows.md) je povinný. Pokud se připojujete k virtuálnímu počítači Azure, Agent se nainstaluje automaticky.

> [!NOTE]
> Uživatel může upravit Zásady skupiny, aby mohl restartovat počítač jenom uživatel, a ne systém. Spravované počítače se můžou zablokovat, pokud Update Management nemá oprávnění k restartování počítače bez ruční interakce uživatele.
>
> Další informace najdete v tématu [Konfigurace nastavení zásady skupiny pro automatické aktualizace](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Pro Linux musí mít počítač přístup k úložišti aktualizací. Úložiště aktualizací může být privátní nebo veřejné. K interakci s Update Management je potřeba protokol TLS 1,1 nebo TLS 1,2. V tomto řešení se nepodporuje agent Log Analytics pro Linux, který je nakonfigurovaný tak, aby hlásil do více než jednoho pracovního prostoru Log Analytics.  Počítač musí mít také nainstalovaný Python 2. x.

Informace o tom, jak nainstalovat agenta Log Analytics pro Linux a stáhnout nejnovější verzi, najdete v tématu [Log Analytics Agent pro Linux](https://github.com/microsoft/oms-agent-for-linux). Informace o tom, jak nainstalovat agenta Log Analytics pro Windows, najdete v tématu [Microsoft Monitoring Agent pro Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit a spravovat nasazení aktualizací, potřebujete specifická oprávnění. Další informace o těchto oprávněních najdete v tématu [přístup na základě rolí – Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Součásti řešení

Řešení se skládá z následujících zdrojů. Prostředky se přidají do vašeho účtu Automation. Jsou buď přímo připojení agenti, nebo ve skupině pro správu připojené Operations Manager.

### <a name="hybrid-worker-groups"></a>Skupiny Hybrid Worker

Po povolení tohoto řešení se všechny počítače se systémem Windows, které jsou přímo připojené k vašemu pracovnímu prostoru Log Analytics, automaticky nakonfigurují jako Hybrid Runbook Worker pro podporu runbooků, které jsou součástí tohoto řešení.

Každý počítač se systémem Windows, který je spravován řešením, je uveden v podokně **Hybrid Worker** Groups jako **systémová skupina hybridních pracovních procesů** pro účet Automation. Řešení používají konvenci pojmenování *názvu hostitele FQDN_GUID*. Tyto skupiny nemůžete cílit na tyto sady Runbook ve vašem účtu. Při pokusu dojde k chybě. Tyto skupiny jsou určené pro podporu pouze řešení pro správu.

Počítače s Windows můžete přidat do skupiny Hybrid Runbook Worker v účtu Automation, abyste mohli podporovat Runbooky Automation, pokud použijete stejný účet pro řešení i pro členství v Hybrid Runbook Worker skupině. Tato funkce se přidala do 7.2.12024.0 verze Hybrid Runbook Worker.

### <a name="management-packs"></a>Sady Management Pack

Pokud je vaše skupina pro správu System Center Operations Manager připojená k pracovnímu prostoru Log Analytics, v Operations Manager se nainstalují následující sady Management Pack. Tyto sady Management Pack jsou také nainstalovány na přímo připojené počítače se systémem Windows po přidání řešení. Tyto sady Management Pack nemusíte konfigurovat ani spravovat.

* Aktualizace Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Aktualizace sady pro správu nasazení

> [!NOTE]
> Pokud máte skupinu pro správu Operations Manager 1807 nebo 2019 s agenty nakonfigurovanými na úrovni skupiny pro správu, která má být přidružena k pracovnímu prostoru, aktuální alternativní řešení, které se zobrazí, je přepsat **IsAutoRegistrationEnabled** na **hodnotu true** . pravidlo **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Další informace o tom, jak se aktualizují sady Management Pack řešení, najdete v tématu [připojení Operations Manager k Azure monitor protokolů](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Pro systémy s agentem Operations Manageru, které je možné plně spravovat pomocí Update Management, musí být agent aktualizovaný na Microsoft Monitoring Agent. Informace o tom, jak aktualizovat agenta, najdete v tématu [Postup upgradu agenta Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). V prostředích, která používají Operations Manager, je potřeba, abyste spustili System Center Operations Manager 2012 R2 UR 14 nebo novějším.

## <a name="onboard"></a>Povolit Update Management

Aby bylo možné začít opravovat systémy, je nutné povolit řešení Update Management. Existuje mnoho způsobů, jak počítače připojit k Update Management. Níže jsou doporučené a podporované způsoby připojení řešení:

* [Z virtuálního počítače](automation-onboard-solutions-from-vm.md)
* [Z prohlížení více počítačů](automation-onboard-solutions-from-browse.md)
* [Z účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [S Azure Automation sadou Runbook](automation-onboard-solutions.md)

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Potvrďte, že jsou připojené počítače mimo Azure.

Pokud chcete potvrdit, že přímo připojené počítače komunikují s protokoly Azure Monitor, můžete po několika minutách spustit jednu z následujících možností prohledávání protokolu.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Na počítači s Windows můžete zkontrolovat následující informace a ověřit připojení agenta s protokoly Azure Monitor:

1. V Ovládacích panelech otevřete **Microsoft Monitoring Agent**. Na kartě **Azure Log Analytics** se v agentovi zobrazí následující zpráva: **Microsoft Monitoring Agent se úspěšně připojil k Log Analytics**.
2. Otevřete protokol událostí systému Windows. V části **Application and Services Logs\Operations Manager** vyhledejte události ID 3000 a id události 5002 ze zdrojového **konektoru služby**. Tyto události signalizují, že je počítač zaregistrován v pracovním prostoru Log Analytics a přijímá konfiguraci.

Pokud Agent nemůže komunikovat s protokoly Azure Monitor a Agent je nakonfigurován pro komunikaci s internetem prostřednictvím brány firewall nebo proxy server, ověřte, zda je správně nakonfigurována brána firewall nebo proxy server. Informace o tom, jak ověřit, jestli je brána firewall nebo proxy server správně nakonfigurovaná, najdete v tématu [Konfigurace sítě pro agenta Windows](../azure-monitor/platform/agent-windows.md) nebo [konfiguraci sítě pro agenta Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Pokud jsou vaše systémy Linux nakonfigurované pro komunikaci s proxy serverem nebo Log Analytics bránou a připojujete se k tomuto řešení, aktualizujte *proxy. conf* oprávnění, které skupině omiuser udělí oprávnění ke čtení souboru pomocí následujících příkazů:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nově přidaní agenti Linux zobrazují po provedení posouzení stav **aktualizovaný** . Tento proces může trvat až 6 hodin.

Pokud chcete ověřit, že skupina pro správu Operations Manager komunikuje s protokoly Azure Monitor, přečtěte si téma [ověření Operations Manager integrace s protokoly Azure monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které podporují toto řešení:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů systému Windows a poté inicializuje instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Řešení shromažďuje informace o aktualizacích systému od agentů systému Linux a následně inicializuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu.<br/>Přímé připojení od agenta Operations Manager do Azure Monitor protokolů není vyžadováno. Data se přesměrovávají ze skupiny pro správu do pracovního prostoru Log Analytics. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

U každého spravovaného počítače se systémem Windows se kontrola provádí dvakrát denně. Každých 15 minut se volá rozhraní Windows API, které se dotazuje na čas poslední aktualizace, aby se zjistilo, jestli se změnil stav. Pokud se stav změnil, je zahájeno prohledávání dodržování předpisů.

Kontrola se provádí každou hodinu každého spravovaného počítače se systémem Linux.

Může trvat 30 minut a 6 hodin, než se na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

Průměrná Azure Monitor využití dat v protokolu pro počítač používající Update Management je přibližně 25 MB JAVASCRIPTOVÉHO měsíčně. Tato hodnota je pouze aproximace a podléhá změnám v závislosti na vašem prostředí. Doporučujeme vám monitorovat prostředí, abyste viděli přesné využití, které máte.

## <a name="viewing-update-assessments"></a>Zobrazit posouzení aktualizací

V účtu Automation vyberte **Update Management** a zobrazte stav vašich počítačů.

Toto zobrazení poskytuje informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací. Ve **sloupci dodržování předpisů**si můžete prohlédnout čas, kdy byl počítač naposledy vyhodnocen. Ve sloupci **připravenosti agenta aktualizace** můžete zjistit, zda stav agenta aktualizace. Pokud dojde k nějakému problému, vyberte odkaz pro řešení potíží, které vám pomůžou zjistit, jaké kroky je potřeba k vyřešení tohoto problému.

Pokud chcete spustit prohledávání protokolů, které vrátí informace o počítači, aktualizaci nebo nasazení, vyberte položku v seznamu. Otevře se podokno **prohledávání protokolu** s dotazem na vybranou položku:

![Update Management výchozí zobrazení](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Instalovat aktualizace

Po posouzení aktualizací pro všechny počítače se systémem Linux a Windows ve vašem pracovním prostoru můžete požadované aktualizace nainstalovat vytvořením *nasazení aktualizace*. Pokud chcete vytvořit nasazení aktualizace, musíte mít oprávnění k zápisu do účtu Automation a přístup pro zápis do všech virtuálních počítačů Azure, které jsou cílené na nasazení. Nasazení aktualizace je naplánovaná instalace požadovaných aktualizací pro jeden nebo více počítačů. Zadejte datum a čas nasazení a počítače nebo skupiny počítačů, které chcete zahrnout do rozsahu nasazení. Další informace o skupinách počítačů najdete v tématu [skupiny počítačů v protokolu Azure monitor](../azure-monitor/platform/computer-groups.md).

Při zahrnutí skupin počítačů do nasazení aktualizace se členství ve skupině vyhodnotí pouze jednou, a to v době vytváření plánu. Následné změny skupiny se neprojeví. Pokud se chcete vyhnout tomuto používání [dynamických skupin](#using-dynamic-groups), jsou tyto skupiny vyřešeny v době nasazení a jsou definovány dotazem pro virtuální počítače Azure nebo uloženým hledáním pro virtuální počítače mimo Azure.

> [!NOTE]
> Virtuální počítače s Windows, které jsou nasazené z Azure Marketplace ve výchozím nastavení, jsou nastavené na příjem automatických aktualizací ze služby web Windows Update. Toto chování se nemění, když přidáte toto řešení nebo do svého pracovního prostoru přidáte virtuální počítače s Windows. Pokud nespravujete aktivně aktualizace pomocí tohoto řešení, platí výchozí chování (pro automatické použití aktualizací).

Pokud nechcete, aby se aktualizace používaly mimo časové období údržby v Ubuntu, překonfigurujte balíček bezobslužné aktualizace tak, aby nedocházelo k automatickým aktualizacím. Informace o tom, jak nakonfigurovat balíček, najdete [v tématu věnovaném automatickým aktualizacím v příručce k serveru Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Virtuální počítače vytvořené z imagí RHEL (na Red Hat Enterprise Linux vyžádání), které jsou k dispozici v Azure Marketplace, se registrují pro přístup k infrastruktuře RHUI (Red Hat Update Infrastructure), která je nasazená v Azure. Jakákoli jiná distribuce systému Linux musí být aktualizována z online úložiště souborů distribuce pomocí podporovaných metod distribuce.

Pokud chcete vytvořit nové nasazení aktualizace, vyberte **naplánovat nasazení aktualizací**. Otevře se stránka **nové nasazení aktualizace** . Zadejte hodnoty vlastností popsaných v následující tabulce a pak klikněte na **vytvořit**:

| Vlastnost | Description |
| --- | --- |
| Name |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Linux nebo Windows|
| Skupiny, které se mají aktualizovat |V případě počítačů Azure definujte dotaz založený na kombinaci předplatného, skupin prostředků, umístění a značek, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. </br></br>V případě počítačů mimo Azure vyberte existující uložené hledání a vyberte skupinu počítačů mimo Azure, které chcete zahrnout do nasazení. </br></br>Další informace najdete v tématu [dynamické skupiny](automation-update-management.md#using-dynamic-groups) .|
| Počítače, které se mají aktualizovat |V rozevíracím seznamu vyberte uložené hledání, importovanou skupinu nebo vyberte možnost počítač a vyberte jednotlivé počítače. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů.</br> Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../azure-monitor/platform/computer-groups.md) |
|Klasifikace aktualizací|Vyberte všechny klasifikace aktualizací, které potřebujete.|
|Zahrnout nebo vyloučit aktualizace|Tím se otevře stránka **zahrnutí/vyloučení** . Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o způsobu zpracování zahrnutí najdete v tématu věnovaném [chování při zahrnutí](automation-update-management.md#inclusion-behavior) . |
|Nastavení plánu|Vyberte čas, kdy se má spustit, a pro opakování vyberte buď jednou, nebo opakovanou.|
| Pre-Scripts + post-Scripts|Vyberte skripty, které se spustí před nasazením a po něm.|
| Časové období údržby |Počet minut, po které se nastaví aktualizace. Hodnota nesmí být kratší než 30 minut a maximálně 6 hodin. |
| Restartovat ovládací prvek| Určuje, jak by se mělo zpracovat restartování. Dostupné možnosti jsou:</br>Restartovat v případě potřeby (výchozí)</br>Vždy restartovat</br>Nikdy nerestartovat</br>Pouze restartovat – nenainstalují se aktualizace|

Nasazení aktualizací lze také vytvořit programově. Informace o tom, jak vytvořit nasazení aktualizace pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také Ukázková sada Runbook, kterou lze použít k vytvoření týdenního nasazení aktualizací. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="maintenance-windows"></a>Okna údržby

Časový interval pro správu a údržbu určuje dobu, po kterou je možné instalovat aktualizace. Při určování časového období údržby Vezměte v úvahu následující podrobnosti.

* Časové intervaly pro správu a údržbu určují, kolik aktualizací se zkouší nainstalovat.
* Update Management neukončí instalaci nových aktualizací, pokud se blíží konec okna údržby.
* Update Management neukončí probíhající aktualizace, pokud dojde k překročení časového intervalu pro správu a údržbu.
* Pokud dojde k překročení časového intervalu pro správu a údržbu v systému Windows, často je instalace aktualizace Service Pack delší dobu trvat.

### <a name="multi-tenant"></a>Nasazení aktualizací mezi klienty

Pokud máte počítače v jiném tenantovi Azure, které vám Update Management, že je potřeba opravit, musíte k jejich naplánování použít následující alternativní řešení. K vytvoření plánu můžete použít rutinu [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) s `-ForUpdate` přepínačem a použít [rutinu New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) a předat počítače do druhé. do `-NonAzureComputer` parametru tenant. Následující příklad ukazuje příklad toho, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Vyberte **chybějící aktualizace** a zobrazte tak seznam aktualizací, které na vašich počítačích chybí. Každá aktualizace je uvedena a je možné ji vybrat. Zobrazí se informace o počtu počítačů, které vyžadují aktualizaci, o operačním systému a o odkazech. V podokně **prohledávání protokolu** se zobrazí další podrobnosti o aktualizacích.

## <a name="view-update-deployments"></a>Zobrazit nasazení aktualizací

Vyberte kartu **nasazení aktualizací** , abyste zobrazili seznam existujících nasazení aktualizací. Vyberte libovolné nasazení aktualizací v tabulce a otevřete tak podokno **aktualizace nasazení aktualizací** pro toto nasazení aktualizace. Protokoly úloh se ukládají na maximum po dobu 30 dnů.

![Přehled výsledků nasazení aktualizací](./media/automation-update-management/update-deployment-run.png)

Pokud chcete zobrazit nasazení aktualizace z REST API, přečtěte si téma [spuštění konfigurace aktualizací softwaru](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Klasifikace aktualizací

V následujících tabulkách jsou uvedeny klasifikace aktualizací v Update Management s definicí pro každou klasifikaci.

### <a name="windows"></a>Windows

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizace pro určitý problém, která řeší kritickou chybu nesouvisející se zabezpečením.        |
|Aktualizace zabezpečení     | Aktualizace pro problém související se zabezpečením určitého produktu.        |
|Kumulativní aktualizace     | Kumulativní sada oprav hotfix, které jsou zabaleny dohromady pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Kumulativní sada oprav hotfix, které se aplikují na aplikaci.        |
|Aktualizace definic     | Aktualizace virů nebo jiných definičních souborů.        |
|Nástroje     | Nástroj nebo funkce, které pomáhají dokončit jednu nebo více úloh.        |
|Aktuální zprávy     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalován.        |

### <a name="linux-2"></a>Linux

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro konkrétní problém nebo problém související se zabezpečením určitého produktu.         |
|Další aktualizace     | Všechny ostatní aktualizace, které nejsou v podstatě důležité nebo nejsou aktualizace zabezpečení.        |

V případě systému Linux může Update Management rozlišovat mezi kritickými a bezpečnostními aktualizacemi v cloudu a současně zobrazovat data posouzení z důvodu obohacení dat v cloudu. Pro opravy Update Management spoléhá na data klasifikace, která jsou k dispozici v počítači. Na rozdíl od jiných distribucí nemá CentOS k dispozici tyto informace v poli. Pokud máte počítače CentOS nakonfigurované tak, aby vracely data zabezpečení pro následující příkaz, Update Management bude možné provést opravy na základě klasifikací.

```bash
sudo yum -q --security check-update
```

V tuto chvíli není podporovaná metoda, která umožňuje povolit nativní klasifikaci – dostupnost dat v CentOS. V tuto chvíli se zákazníkům, kteří si ji sami povolili, poskytne jenom osvědčená podpora.

## <a name="firstparty-predownload"></a>Rozšířená nastavení

Update Management spoléhá na web Windows Update stažení a instalaci aktualizací Windows. V důsledku toho respektuje mnohá nastavení, která používá web Windows Update. Pokud použijete nastavení k povolení aktualizací jiných než Windows, Update Management bude tyto aktualizace spravovat taky. Pokud chcete povolit stahování aktualizací ještě před tím, než dojde k nasazení aktualizace, nasazení aktualizací může rychlejší a méně pravděpodobně překročit časový interval pro správu a údržbu.

### <a name="pre-download-updates"></a>Předběžné stažení aktualizací

Pokud chcete konfigurovat automatické stahování aktualizací v Zásady skupiny, můžete nastavit [nastavení konfigurace automatických aktualizací](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. Tím se stáhnou aktualizace potřebné na pozadí, ale neinstalují se. Tím se zachovává Update Management kontroly plánů, ale aktualizace se můžou stahovat mimo Update Management časový interval pro správu a údržbu. To může zabránit tomu, aby časový **interval pro správu a údržbu překročil** chyby v Update Management.

Můžete ho také nastavit pomocí PowerShellu, spustit následující PowerShell v systému, ve kterém chcete automaticky stahovat aktualizace.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Zakázat automatickou instalaci

Virtuální počítače Azure mají automatickou instalaci aktualizací, které jsou ve výchozím nastavení povolené. To může způsobit, že se aktualizace nainstalují předtím, než je naplánujete jejich instalaci pomocí Update Management. Toto chování můžete zakázat nastavením `NoAutoUpdate` klíče registru na. `1` Následující fragment kódu prostředí PowerShell vám ukáže jeden ze způsobů, jak to provést.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Povolit aktualizace pro ostatní produkty Microsoftu

Ve výchozím nastavení web Windows Update poskytuje pouze aktualizace pro Windows. Pokud při **aktualizaci systému Windows povolíte aktualizace pro další produkty společnosti Microsoft**, máte k dispozici aktualizace pro další produkty, včetně oprav zabezpečení pro SQL Server nebo jiný software první strany. Tato možnost se nedá nakonfigurovat pomocí Zásady skupiny. Spusťte následující PowerShell v systémech, u kterých chcete povolit opravy na základě první strany, a Update Management bude toto nastavení akceptovat.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a>Opravy třetích stran ve Windows

Update Management spoléhá na místně nakonfigurované úložiště aktualizací, které opraví podporované systémy Windows. Toto je buď služba WSUS, nebo web Windows Update. Nástroje, [jako](/sccm/sum/tools/updates-publisher
) je System Center Updates Publisher (Updates Publisher), umožňují publikovat vlastní aktualizace služby WSUS. Tento scénář umožňuje Update Management opravit počítače, které používají System Center Configuration Manager jako úložiště aktualizací se softwarem třetích stran. Informace o tom, jak nakonfigurovat aplikaci Updates Publisher, najdete v tématu [install Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Plánování sítě

Následující adresy jsou vyžadovány konkrétně pro Update Management. Komunikace s těmito adresami probíhá přes port 443.

|Veřejné Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

U počítačů s Windows musíte taky u všech koncových bodů vyžadovaných nástrojem web Windows Update umožňovat provoz.  Aktualizovaný seznam požadovaných koncových bodů najdete v [problémech souvisejících s HTTP/proxy serverem](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Pokud máte místní [web Windows Update Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment), musíte taky na serveru, který určíte v [klíči služby WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry), povolený provoz.

U počítačů se systémem Red Hat Linux uveďte požadované koncové body na [IP adresách pro servery RHUI Content Delivery](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) . Další distribuce pro Linux najdete v dokumentaci pro poskytovatele.

Další informace o portech, které Hybrid Runbook Worker vyžaduje, najdete v tématu [porty rolí Hybrid Worker](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Doporučuje se používat adresy uvedené při definování výjimek. Pro IP adresy můžete stáhnout [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se aktualizuje týdně a odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres.

Při konfiguraci počítačů, které nemají přístup k Internetu, postupujte podle pokynů v tématu [připojení počítačů bez přístupu k Internetu](../azure-monitor/platform/gateway.md) .

## <a name="search-logs"></a>Hledat protokoly

Kromě podrobností, které jsou k dispozici v Azure Portal, můžete hledat v protokolech. Na stránkách řešení vyberte **Log Analytics**. Otevře se podokno **prohledávání protokolu** .

Můžete se také seznámit s postupem přizpůsobení dotazů nebo jejich použití od různých klientů a dalších návštěv:  [Dokumentace k](
https://dev.loganalytics.io/)rozhraní API pro hledání Log Analytics.

### <a name="sample-queries"></a>Ukázkové dotazy

V následujících částech najdete ukázky dotazů na protokoly aktualizací, které jsou shromážděné tímto řešením:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Dotazy na vyhodnocení pro jeden virtuální počítač Azure (Windows)

Hodnotu VMUUID nahraďte identifikátorem GUID virtuálního počítače, na který se dotaz provádí. VMUUID, která se má použít, můžete najít spuštěním následujícího dotazu v protokolech Azure Monitor:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Chybějící souhrn aktualizací

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Dotazy na posuzování virtuálních počítačů Azure (Linux)

U některých Linux distribuce se neshoduje [](https://en.wikipedia.org/wiki/Endianness) s hodnotou VMUUID, která pochází z Azure Resource Manager a co je uložená v protokolech Azure monitor. Následující dotaz vyhledá shodu na základě typu endian. Vyměňte hodnoty VMUUID pomocí formátu big-endian a little endian identifikátoru GUID, aby byly výsledky vráceny správně. VMUUID, která se má použít, můžete najít spuštěním následujícího dotazu v protokolech Azure Monitor:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Chybějící souhrn aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Dotazy k vyhodnocení pro více virtuálních počítačů

##### <a name="computers-summary"></a>Souhrn počítačů

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Chybějící souhrn aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="computers-list"></a>Seznam počítačů

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Používání dynamických skupin

Update Management poskytuje možnost cílit na dynamickou skupinu virtuálních počítačů Azure nebo mimo Azure pro nasazení aktualizací. Tyto skupiny jsou vyhodnocovány v době nasazení, takže není nutné upravovat nasazení, aby bylo možné přidat počítače.

> [!NOTE]
> Při vytváření nasazení aktualizace musíte mít správná oprávnění. Další informace najdete v tématu [instalace aktualizací](#install-updates).

### <a name="azure-machines"></a>Počítače Azure

Tyto skupiny jsou definovány dotazem, když je zahájeno nasazení aktualizace, členové této skupiny budou vyhodnocováni. Dynamické skupiny nefungují s klasickými virtuálními počítači. Při definování dotazu lze společně použít následující položky k naplnění dynamické skupiny.

* Subscription
* Skupiny prostředků
* Umístění
* Tags

![Výběr skupin](./media/automation-update-management/select-groups.png)

Chcete-li zobrazit náhled výsledků dynamické skupiny, klikněte na tlačítko **Náhled** . V této verzi Preview se v této době zobrazuje členství ve skupině. v tomto příkladu hledáme počítače s **rolí** značek, které se rovnají **BackendServer**. Pokud se tato značka přidá do dalších počítačů, přidají se do všech budoucích nasazení v této skupině.

![skupiny náhledu](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Počítače mimo Azure

Pro počítače, které nejsou v Azure, se při vytváření dynamické skupiny používají taky uložená hledání, která se označují jako skupiny počítačů. Informace o tom, jak vytvořit uložené hledání, najdete v tématu [Vytvoření skupiny počítačů](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po vytvoření skupiny ji můžete vybrat ze seznamu uložených hledání. Kliknutím na **Náhled** zobrazíte v daném čase počítače v uloženém hledání.

![Výběr skupin](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integrace se System Center Configuration Managerem

Zákazníci, kteří investovali do System Center Configuration Manager pro správu počítačů, serverů a mobilních zařízení, spoléhají také na sílu a splatnost Configuration Manager, které jim pomohou při správě aktualizací softwaru. Configuration Manager je součástí cyklu správy aktualizací softwaru (SUM).

Informace o tom, jak integrovat řešení pro správu pomocí System Center Configuration Manager, najdete v tématu věnovaném [integraci System Center Configuration Manager s Update Management](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Chování při zahrnutí

Zařazení aktualizace umožňuje zadat konkrétní aktualizace, které se mají použít. Jsou nainstalovány opravy nebo balíčky, které jsou součástí produktu. Když jsou zahrnuté opravy nebo balíčky a zároveň se vybere klasifikace, nainstalují se i zahrnuté položky a položky, které odpovídají této klasifikaci.

Je důležité, abyste věděli, že vyloučení přepisují. Pokud například definujete pravidlo `*`vyloučení, nebudou nainstalovány žádné opravy ani balíčky, protože všechny jsou vyloučené. Vyloučené opravy se pořád na počítači zobrazují jako chybějící. Pro počítače se systémem Linux, pokud je zahrnut balíček, ale obsahuje závislý balíček, který byl vyloučen, není balíček nainstalován.

## <a name="patch-linux-machines"></a>Oprava počítačů se systémem Linux

Následující části vysvětlují možné problémy s opravami systému Linux.

### <a name="unexpected-os-level-upgrades"></a>Neočekávané upgrady na úrovni operačního systému

U některých variant systému Linux, například Red Hat Enterprise Linux, může dojít k upgradům na úrovni operačního systému prostřednictvím balíčků. To může vést k tomu, Update Management spustí, kde se změní číslo verze operačního systému. Vzhledem k tomu, že Update Management používá stejné metody k aktualizaci balíčků, které by správce použil místně na počítači se systémem Linux, je toto chování záměrné.

Chcete-li se vyhnout aktualizaci verze operačního systému pomocí Update Management spuštění, použijte funkci **vyloučení** .

V Red Hat Enterprise Linux je název balíčku, který se má vyloučit, RedHat-Release-Server. x86_64.

![Balíčky, které se mají vyloučit pro Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Důležité opravy/opravy zabezpečení nejsou aplikovány

Když nasadíte aktualizace na počítač se systémem Linux, můžete vybrat možnost klasifikace aktualizací. Tím se vyfiltrují aktualizace, které se aplikují na počítač, který splňuje zadaná kritéria. Tento filtr se použije lokálně na počítači při nasazení aktualizace.

Vzhledem k tomu, že Update Management provádí obohacení aktualizace v cloudu, můžou být některé aktualizace příznakem v Update Management s dopadem na zabezpečení, a to i v případě, že místní počítač tyto informace nemá. V důsledku toho, pokud použijete důležité aktualizace na počítači se systémem Linux, může dojít k aktualizacím, které nejsou označeny jako bezpečnostní dopad na tento počítač a aktualizace se nepoužijí.

Update Management ale přesto může tento počítač ohlásit jako nevyhovující předpisům, protože obsahuje další informace o příslušné aktualizaci.

Nasazení aktualizací podle klasifikace aktualizací nefunguje na CentOS. Pokud chcete správně nasadit aktualizace pro CentOS, vyberte všechny klasifikace, abyste měli jistotu, že se aktualizace aplikují. Pro SUSE vyberte *jenom* jiné aktualizace, protože klasifikace může vést k tomu, že se nainstalují taky některé aktualizace zabezpečení, pokud se jako první vyžaduje aktualizace zabezpečení související s zypperu (Správce balíčků) nebo její závislosti. Toto chování je omezení zypperu. V některých případech může být nutné znovu spustit nasazení aktualizace. Chcete-li ověřit, zkontrolujte protokol aktualizace.

## <a name="remove-a-vm-from-update-management"></a>Odebrání virtuálního počítače z Update Management

Odebrání virtuálního počítače z Update Management:

* Ve vašem pracovním prostoru Log Analytics odeberte virtuální počítač z uloženého hledání pro konfiguraci `MicrosoftDefaultScopeConfig-Updates`oboru. Uložená hledání najdete v části **Obecné** v pracovním prostoru.
* Odeberte [agenta Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [agenta Log Analytics pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Další postup

Přejděte k kurzu, kde se dozvíte, jak spravovat aktualizace pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)

* K zobrazení podrobných dat aktualizace použijte prohledávání protokolů v protokolu [Azure monitor](../log-analytics/log-analytics-log-searches.md) .
* [Vytvořte výstrahy](automation-tutorial-update-management.md#configure-alerts) pro stav nasazení aktualizace.

* Další informace o tom, jak pracovat s Update Management přes REST API, najdete v tématu [Konfigurace aktualizací softwaru](/rest/api/automation/softwareupdateconfigurations) .
* Další informace o řešení potíží s Update Management najdete v tématu [řešení potíží Update Management](troubleshoot/update-management.md)
