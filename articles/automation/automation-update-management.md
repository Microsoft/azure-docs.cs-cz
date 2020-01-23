---
title: Řešení Update Management v Azure
description: Tento článek popisuje, jak pomocí řešení Azure Update Management spravovat aktualizace pro počítače se systémem Windows a Linux.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 9e03ba960ab6542198372d75de7e0d34bf8d9e1b
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513316"
---
# <a name="update-management-solution-in-azure"></a>Řešení Update Management v Azure

Řešení Update Management v Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače se systémem Windows a Linux v Azure, v místních prostředích a u jiných poskytovatelů cloudu. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Můžete povolit Update Management pro virtuální počítače přímo z účtu Azure Automation. Další informace najdete v tématu [Správa aktualizací pro několik virtuálních počítačů](manage-update-multi.md). Můžete také povolit Update Management pro virtuální počítač ze stránky virtuálního počítače v Azure Portal. Tento scénář je k dispozici pro virtuální počítače se systémy [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) a [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

> [!NOTE]
> Řešení Update Management vyžaduje propojení pracovního prostoru Log Analytics s vaším účtem Automation. Konečný seznam podporovaných oblastí najdete v tématu [mapování pracovních prostorů Azure](./how-to/region-mappings.md). Mapování oblastí neovlivňují možnost spravovat virtuální počítače v samostatné oblasti z účtu Automation.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Přehled řešení

Počítače spravované pomocí Update Management k vyhodnocení a nasazení aktualizací používají následující konfigurace:

* Agenta Microsoft Monitoring Agent (MMA) pro Windows nebo Linux
* Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
* Funkci Hybrid Runbook Worker služby Automation
* Služby Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače s Windows

Následující diagram znázorňuje, jak řešení vyhodnocuje a aplikuje aktualizace zabezpečení na všechny připojené počítače s Windows serverem a Linux v pracovním prostoru:

![Tok procesu Update Management](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management můžete nativně nasadit na počítače v různých předplatných stejného tenanta.

Po uvolnění balíčku trvá tato oprava 2 až 3 hodiny, než se oprava zobrazí pro počítače se systémem Linux pro posouzení. U počítačů s Windows trvá 12 až 15 hodin, než se oprava zobrazí po jejím vydání.

Když počítač dokončí kontrolu shody aktualizací, agent přepošle informace hromadně do Azure Monitor protokolů. V počítači se systémem Windows se kontrola kompatibility ve výchozím nastavení spouští každých 12 hodin.

Kromě plánu vyhledávání se kontrola kompatibility aktualizací zahájila do 15 minut od restartování MMA, před instalací aktualizace a po instalaci aktualizace.

U počítače se systémem Linux se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud se agent MMA restartuje, zahájí se kontrola kompatibility do 15 minut.

Řešení informuje o tom, jak aktuální počítač vychází ze zdroje, se kterým jste nakonfigurovali synchronizaci. Pokud je počítač s Windows nakonfigurovaný tak, aby hlásil službu WSUS, v závislosti na tom, kdy se služba WSUS naposledy synchronizoval s Microsoft Update, se výsledky můžou lišit od toho, co Microsoft Update zobrazuje. Toto chování je stejné pro počítače se systémem Linux, které jsou nakonfigurovány k hlášení do místního úložiště místo do veřejného úložiště.

> [!NOTE]
> Aby bylo možné řádně ohlásit službu, Update Management vyžaduje, aby byly povoleny určité adresy URL a porty. Další informace o těchto požadavcích najdete v tématu [Plánování sítě pro hybridní pracovní procesy](automation-hybrid-runbook-worker.md#network-planning).

Na počítače, které vyžadují aktualizace softwaru, můžete tyto aktualizace nasadit a nainstalovat tak, že vytvoříte plánované nasazení. Aktualizace klasifikované jako *volitelné* nejsou zahrnuty v oboru nasazení pro počítače se systémem Windows. V rozsahu nasazení jsou zahrnuté jenom požadované aktualizace.

Plánované nasazení definuje, které cílové počítače obdrží příslušné aktualizace. K tomu slouží buď explicitním zadáním určitých počítačů, nebo výběrem [skupiny počítačů](../azure-monitor/platform/computer-groups.md) , která je založena na hledání protokolu konkrétní sady počítačů (nebo na [dotazu Azure](automation-update-management-query-logs.md) , který dynamicky vybírá virtuální počítače Azure na základě zadaných kritérií). Tyto skupiny se liší od [Konfigurace oboru](../azure-monitor/insights/solution-targeting.md), která se používá pouze k určení, které počítače získají sady Management Pack umožňující řešení.

Také zadáte plán, který schválíte a nastavíte časové období, během kterého lze nainstalovat aktualizace. Toto období se nazývá časový interval pro správu a údržbu. Pro restartování je vyhrazeno 20 minut časového období údržby, za předpokladu, že je potřeba, a vybrali jste vhodnou možnost restartování. Pokud oprava trvá déle, než se čekalo a v časovém intervalu údržby je méně než 20 minut, neproběhne restart.

Aktualizace se instalují podle runbooků ve službě Azure Automation. Tyto Runbooky nemůžete zobrazit a nevyžadují žádnou konfiguraci. Při vytvoření nasazení aktualizace se vytvoří plán, který spustí sadu Runbook s aktualizací v zadaném čase pro zahrnuté počítače. Hlavní runbook spouští podřízený Runbook na každém agentovi pro instalaci požadovaných aktualizací.

V datu a čase zadaném v nasazení aktualizace spustí cílové počítače paralelně nasazení. Před instalací se spustí Kontrola, aby se ověřilo, že se aktualizace pořád vyžadují. V případě klientských počítačů služby WSUS, pokud aktualizace nejsou schváleny ve službě WSUS, nasazení aktualizace se nezdařilo.

Počítač zaregistrovaný pro Update Management ve více než jednom pracovním prostoru Log Analytics (více domovských) se nepodporuje.

## <a name="clients"></a>Klienti

### <a name="supported-client-types"></a>Podporované klientské typy

Následující tabulka uvádí podporované operační systémy pro posouzení aktualizací. Oprava vyžaduje Hybrid Runbook Worker. Informace o požadavcích na Hybrid Runbook Worker najdete v pokynech k instalaci pro instalaci [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) a [Linux Hybrid Runbook Worker](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM a SP1 Standard)| Update Management podporuje jenom vyhodnocení pro tento operační systém, takže se nepodporuje aktualizace, protože [Hybrid Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) není pro Windows Server 2008 R2 podporovaná. |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Oprava založená na klasifikaci vyžaduje, `yum`, aby vracela data zabezpečení, která CentOS ve svých verzích RTM. Další informace o opravách na základě klasifikace na CentOS najdete v tématu [klasifikace aktualizací v systému Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14,04 LTS, 16,04 LTS a 18,04 (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

> [!NOTE]
> Sady škálování virtuálních počítačů Azure je možné spravovat prostřednictvím Update Management. Update Management pracuje na samotných instancích a nikoli na základní imagi. Aktualizace budete muset naplánovat přírůstkově, takže se neaktualizují všechny instance virtuálních počítačů najednou.
> Uzly pro sadu škálování virtuálního počítače můžete přidat pomocí postupu v části připojení [počítače mimo Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Nepodporované klientské typy

V následující tabulce jsou uvedeny nepodporované operační systémy:

|Operační systém  |Poznámky  |
|---------|---------|
|Klient Windows     | Klientské operační systémy (například Windows 7 a Windows 10) nejsou podporovány.        |
|Windows Server 2016 Nano Server     | Není podporováno.       |
|Uzly služby Azure Kubernetes | Není podporováno. Použijte proces opravy popsaný v tématu [použití aktualizací zabezpečení a jádra pro uzly Linux ve službě Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md) .|

### <a name="client-requirements"></a>Požadavky na klienty

Následující informace popisují požadavky klienta na konkrétní operační systémy. Další pokyny najdete v tématu [Plánování sítě](#ports).

#### <a name="windows"></a>Windows

Agenti systému Windows musí být nakonfigurováni pro komunikaci se serverem WSUS, nebo musí mít přístup k Microsoft Update.

Update Management můžete použít s Configuration Manager. Další informace o integračních scénářích najdete v tématu věnovaném [integraci Configuration Manager s Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). [Agent pro Windows](../azure-monitor/platform/agent-windows.md) je povinný. Pokud se připojujete k virtuálnímu počítači Azure, Agent se nainstaluje automaticky.

Ve výchozím nastavení jsou virtuální počítače s Windows, které jsou nasazené z Azure Marketplace, nastavené na příjem automatických aktualizací ze služby web Windows Update. Toto chování se nemění, když přidáte toto řešení nebo do svého pracovního prostoru přidáte virtuální počítače s Windows. Pokud nespravujete aktivně aktualizace pomocí tohoto řešení, platí výchozí chování (pro automatické použití aktualizací).

> [!NOTE]
> Uživatel může upravit Zásady skupiny, aby bylo možné restartovat počítač pouze tímto uživatelem, nikoli systémem. Spravované počítače můžou zablokovat, pokud Update Management nemá práva k restartování počítače bez ruční interakce uživatele.
>
> Další informace najdete v tématu [Konfigurace nastavení zásady skupiny pro automatické aktualizace](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Pro Linux musí mít počítač přístup k úložišti aktualizací. Úložiště aktualizací může být privátní nebo veřejné. K interakci s Update Management je potřeba protokol TLS 1,1 nebo TLS 1,2. Pro toto řešení se nepodporuje agent Log Analytics pro Linux, který je nakonfigurovaný tak, aby hlásil do více než jednoho pracovního prostoru Log Analytics. Počítač musí mít také nainstalovaný Python 2. x.

Informace o tom, jak nainstalovat agenta Log Analytics pro Linux a stáhnout nejnovější verzi, najdete v tématu [Log Analytics Agent pro Linux](https://github.com/microsoft/oms-agent-for-linux). Informace o tom, jak nainstalovat agenta Log Analytics pro Windows, najdete v tématu [připojení počítačů s Windows k Azure monitor](../log-analytics/log-analytics-windows-agent.md).

Virtuální počítače vytvořené z imagí Red Hat Enterprise Linux na vyžádání (RHEL), které jsou k dispozici v Azure Marketplace jsou registrované pro přístup k [infrastruktuře RHUI (Red Hat Update Infrastructure)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) , která je nasazená v Azure. Jakákoli jiná distribuce systému Linux musí být aktualizována z online úložiště souborů distribuce pomocí podporovaných metod distribuce.

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit a spravovat nasazení aktualizací, potřebujete specifická oprávnění. Další informace o těchto oprávněních najdete v tématu věnovaném [přístupu na základě rolí – Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Součásti řešení

Řešení se skládá z následujících zdrojů. Prostředky se přidají do vašeho účtu Automation. Jsou buď přímo připojení agenti, nebo ve skupině pro správu připojené Operations Manager.

### <a name="hybrid-worker-groups"></a>Skupiny Hybrid Worker

Po povolení tohoto řešení se všechny počítače se systémem Windows, které jsou přímo připojené k vašemu pracovnímu prostoru Log Analytics, automaticky nakonfigurují jako Hybrid Runbook Worker pro podporu runbooků, které jsou součástí tohoto řešení.

Každý počítač se systémem Windows, který je spravován řešením, je uveden v podokně **Hybrid Worker** Groups jako **systémová skupina hybridních pracovních procesů** pro účet Automation. Řešení používají konvenci pojmenování názvů *hostitelů FQDN_GUID* . Tyto skupiny nemůžete cílit na tyto sady Runbook ve vašem účtu. Pokud se pokusíte, pokus se nezdaří. Tyto skupiny jsou určené pro podporu pouze řešení pro správu.

Počítače s Windows můžete přidat do skupiny Hybrid Runbook Worker v účtu Automation, abyste mohli podporovat Runbooky Automation, pokud použijete stejný účet pro řešení i pro členství v Hybrid Runbook Worker skupině. Tato funkce se přidala do 7.2.12024.0 verze Hybrid Runbook Worker.

### <a name="management-packs"></a>Sady Management Pack

Pokud je vaše skupina pro správu System Center Operations Manageru připojená k pracovnímu prostoru Log Analytics, nainstalují se do Operations Manageru následující sady Management Pack. Tyto sady Management Pack se po přidání do řešení nainstalují také přímo na připojené počítače s Windows. Sady Management Pack nemusíte konfigurovat ani spravovat:

* Aktualizace Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Aktualizace sady pro správu nasazení

> [!NOTE]
> Předpokládejme, že máte skupinu pro správu Operations Manager 1807 nebo 2019 s agenty nakonfigurovanými na úrovni skupiny pro správu a přidružíte je k pracovnímu prostoru. Aktuální alternativní řešení, které se má zobrazit, je přepsání **IsAutoRegistrationEnabled** na **hodnotu true** v pravidle **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Další informace o tom, jak se aktualizují sady Management Pack řešení, najdete v tématu [připojení Operations Manager k Azure monitor protokolů](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Systémy s agentem Operations Manageru: aby byl agent plně spravovaný pomocí Update Management, musí být agent aktualizovaný na MMA. Informace o tom, jak aktualizovat agenta, najdete v tématu [Postup upgradu agenta Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). V prostředích, která používají Operations Manager, je nutné, abyste spustili System Center Operations Manager 2012 R2 UR 14 nebo novějším.

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů systému Windows a poté inicializuje instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Řešení shromažďuje informace o aktualizacích systému od agentů systému Linux a následně inicializuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu.<br/><br/>Přímé připojení od agenta Operations Manager do Azure Monitor protokolů není vyžadováno. Data se přesměrovávají ze skupiny pro správu do pracovního prostoru Log Analytics. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

U každého spravovaného počítače se systémem Windows se kontrola provádí dvakrát denně. Každých 15 minut se volá rozhraní Windows API, které se dotazuje na čas poslední aktualizace, aby se zjistilo, jestli se změnil stav. Pokud se stav změnil, je zahájeno prohledávání dodržování předpisů.

Kontrola se provádí každou hodinu každého spravovaného počítače se systémem Linux.

Zobrazení aktuálních dat o spravovaných počítačích může trvat 30 minut až 6 hodin.

Průměrné využití dat pomocí Azure Monitor protokolů pro počítač používající Update Management je přibližně 25 megabajtů (MB) měsíčně. Tato hodnota je jenom aproximace a v závislosti na vašem prostředí se může změnit. Doporučujeme monitorovat své prostředí, abyste mohli sledovat přesné využití.

## <a name="ports"></a>Plánování sítě

Následující adresy jsou vyžadovány konkrétně pro Update Management. Komunikace s těmito adresami probíhá přes port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

U počítačů s Windows musíte taky u všech koncových bodů vyžadovaných nástrojem web Windows Update umožňovat provoz. Aktualizovaný seznam požadovaných koncových bodů najdete v [problémech souvisejících s HTTP/proxy serverem](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Pokud máte místní [web Windows Update Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment), musíte taky na serveru, který určíte v [klíči služby WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry), povolený provoz.

V případě počítačů se systémem Red Hat Linux naleznete v části [IP adresy pro RHUI Content Delivery Server](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) pro požadované koncové body. Další distribuce pro Linux najdete v dokumentaci ke zprostředkovateli.

Další informace o portech, které Hybrid Runbook Worker vyžaduje, najdete v tématu [porty rolí Hybrid Worker](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Doporučujeme používat adresy uvedené při definování výjimek. Pro IP adresy můžete stáhnout [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se aktualizuje týdně a odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres.

Při konfiguraci počítačů, které nemají přístup k Internetu, postupujte podle pokynů v tématu [připojení počítačů bez přístupu k Internetu](../azure-monitor/platform/gateway.md) .

## <a name="view-update-assessments"></a>Zobrazení posouzení aktualizací

V účtu Automation vyberte **Update Management** a zobrazte stav vašich počítačů.

Toto zobrazení poskytuje informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací. Ve sloupci **dodržování předpisů** si můžete prohlédnout čas, kdy byl počítač naposledy vyhodnocen. Ve sloupci **připravenosti agenta aktualizace** můžete kontrolovat stav agenta aktualizace. Pokud se vyskytne problém, vyberte odkaz pro řešení potíží, které vám pomůžou problém vyřešit.

Pokud chcete spustit prohledávání protokolů, které vrátí informace o počítači, aktualizaci nebo nasazení, vyberte odpovídající položku v seznamu. Otevře se podokno **prohledávání protokolu** s dotazem na vybranou položku:

![Update Management výchozí zobrazení](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Vyberte **chybějící aktualizace** a zobrazte tak seznam aktualizací, které na vašich počítačích chybí. Každá aktualizace je uvedena a je možné ji vybrat. Zobrazí se informace o počtu počítačů, které vyžadují aktualizaci, o operačním systému a o odkazech. V podokně **prohledávání protokolu** se zobrazí další podrobnosti o aktualizacích.

![Chybějící aktualizace](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Klasifikace aktualizací

V následujících tabulkách jsou uvedeny klasifikace aktualizací v Update Management s definicí pro každou klasifikaci.

### <a name="windows"></a>Windows

|Classification  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizace pro určitý problém, která řeší kritickou chybu nesouvisející se zabezpečením.        |
|Aktualizace zabezpečení     | Aktualizace pro problém související se zabezpečením určitého produktu.        |
|Kumulativní aktualizace     | Kumulativní sada oprav hotfix, které jsou zabaleny dohromady pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Kumulativní sada oprav hotfix, které se aplikují na aplikaci.        |
|Aktualizace definic     | Aktualizace virů nebo jiných definičních souborů.        |
|nástroje     | Nástroj nebo funkce, které pomáhají dokončit jednu nebo více úloh.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalován.        |

### <a name="linux-2"></a>Linux

|Classification  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro konkrétní problém nebo problém související se zabezpečením určitého produktu.         |
|Další aktualizace     | Všechny ostatní aktualizace, které nejsou v podstatě důležité nebo které nejsou aktualizacemi zabezpečení.        |

V případě systému Linux může Update Management rozlišovat mezi důležitými aktualizacemi a aktualizacemi zabezpečení v cloudu a současně zobrazuje data posouzení z důvodu obohacení dat v cloudu. Pro opravy Update Management spoléhá na data klasifikace, která jsou k dispozici v počítači. Na rozdíl od jiných distribucí nemá CentOS tyto informace dostupné ve verzi RTM. Pokud máte počítače CentOS nakonfigurované tak, aby vracely data zabezpečení pro následující příkaz, Update Management se může opravit na základě klasifikací.

```bash
sudo yum -q --security check-update
```

V současné době není podporována žádná podporovaná metoda pro povolení nativní klasifikace – dostupnost dat v CentOS. V tuto chvíli se zákazníkům, kteří si můžou tuto možnost sami povolit, poskytnou jenom podporu s lepší námahou. 

Chcete-li klasifikovat aktualizace na Red Hat Enterprise verze 6, je nutné nainstalovat modul plug-in Yumu-Security. V Red Hat Enterprise Linux 7 je modul plug-in již součástí samotného Yumu, není nutné nic instalovat. Další informace najdete v následujícím [článku znalostní báze](https://access.redhat.com/solutions/10021)Red Hat.

## <a name="integrate-with-configuration-manager"></a>Integrace s Configuration Managerem

Zákazníci, kteří investovali do koncového bodu Microsoft Endpoint Configuration Manager pro správu počítačů, serverů a mobilních zařízení, využívají také sílu a splatnost Configuration Manager, které jim pomohou při správě aktualizací softwaru. Configuration Manager je součástí cyklu správy aktualizací softwaru (SUM).

Informace o tom, jak integrovat řešení pro správu pomocí Configuration Manager, najdete v tématu věnovaném [integraci Configuration Manager s Update Management](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-patches-on-windows"></a>Opravy třetích stran ve Windows

Update Management spoléhá na místně nakonfigurované úložiště aktualizací, které opraví podporované systémy Windows. Toto je buď služba WSUS, nebo web Windows Update. Nástroje, jako je [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher), umožňují publikovat vlastní aktualizace služby WSUS. Tento scénář umožňuje Update Management opravit počítače, které používají Configuration Manager jako úložiště aktualizací se softwarem třetích stran. Informace o tom, jak nakonfigurovat aplikaci Updates Publisher, najdete v tématu [install Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Oprava počítačů se systémem Linux

V následujících oddílech se vysvětlují možné problémy s opravou distribuce Linux.

### <a name="unexpected-os-level-upgrades"></a>Neočekávané upgrady na úrovni operačního systému

U některých variant systému Linux, například Red Hat Enterprise Linux, může dojít k upgradům na úrovni operačního systému prostřednictvím balíčků. To může vést k tomu, Update Management spustí, kde se změní číslo verze operačního systému. Vzhledem k tomu, že Update Management používá stejné metody k aktualizaci balíčků, které by správce použil místně na počítači se systémem Linux, je toto chování záměrné.

Chcete-li se vyhnout aktualizaci verze operačního systému pomocí Update Management spuštění, použijte funkci **vyloučení** .

V Red Hat Enterprise Linux název balíčku, který se má vyloučit, je RedHat-Release-Server. x86_64.

![Balíčky, které se mají vyloučit pro Linux](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>Důležité opravy/opravy zabezpečení nejsou aplikovány

Když nasadíte aktualizace na počítač se systémem Linux, můžete vybrat možnost klasifikace aktualizací. Tato možnost filtruje aktualizace, které jsou aplikovány na počítač, který splňuje zadaná kritéria. Tento filtr se použije lokálně na počítači při nasazení aktualizace.

Vzhledem k tomu, že Update Management provádí obohacení aktualizace v cloudu, můžou být některé aktualizace příznakem v Update Management, protože mají dopad na zabezpečení, a to i v případě, že místní počítač tyto informace nemá. V důsledku toho, pokud použijete důležité aktualizace na počítači se systémem Linux, může dojít k aktualizacím, které nejsou označeny jako bezpečnostní dopad na daný počítač, a proto se aktualizace nepoužijí. Update Management ale přesto může tento počítač ohlásit jako nekompatibilní, protože obsahuje další informace o příslušné aktualizaci.

Nasazení aktualizací podle klasifikace aktualizací nefunguje na verzích RTM CentOS. Chcete-li správně nasadit aktualizace pro CentOS, vyberte všechny klasifikace, aby bylo zajištěno, že budou aktualizace aplikovány. V případě SUSE je třeba vybrat *pouze* **jiné aktualizace** , protože klasifikace může způsobit instalaci některých aktualizací zabezpečení, pokud jsou nejprve požadovány aktualizace zabezpečení související s zypperu (Správce balíčků) nebo její závislosti. Toto chování je omezení zypperu. V některých případech může být nutné znovu spustit nasazení aktualizace. Chcete-li ověřit, zkontrolujte protokol aktualizace.

### <a name="multi-tenant"></a>Nasazení aktualizací mezi klienty

Pokud máte počítače v jiném tenantovi Azure, které vám Update Management, že je potřeba opravit, musíte k jejich naplánování použít následující alternativní řešení. Pomocí rutiny [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) s přepínačem `-ForUpdate` můžete vytvořit plán a použít rutinu [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) a předat počítače do jiného tenanta do parametru `-NonAzureComputer`. Následující příklad ukazuje, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Povolit Update Management

Aby bylo možné začít opravovat systémy, je nutné povolit řešení Update Management. Existuje mnoho způsobů, jak počítače připojit k Update Management. Níže jsou doporučené a podporované způsoby připojení řešení:

* [Z virtuálního počítače](automation-onboard-solutions-from-vm.md)
* [Z prohlížení více počítačů](automation-onboard-solutions-from-browse.md)
* [Z účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [S Azure Automation sadou Runbook](automation-onboard-solutions.md)

## <a name="next-steps"></a>Další kroky

Pomocí následujícího kurzu se naučíte spravovat aktualizace pro virtuální počítače s Windows:

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)

* K zobrazení podrobných dat aktualizace použijte prohledávání protokolů v protokolu [Azure monitor](../log-analytics/log-analytics-log-searches.md) .
* [Vytvořte výstrahy](automation-tutorial-update-management.md#configure-alerts) pro stav nasazení aktualizace.

* Informace o tom, jak pracovat s Update Management přes REST API, najdete v tématu [Konfigurace aktualizací softwaru](/rest/api/automation/softwareupdateconfigurations).
* Další informace o řešení potíží s Update Management najdete v tématu [řešení potíží s Update Management](troubleshoot/update-management.md).
