---
title: Přehled Azure Automation Update Management
description: Tento článek poskytuje přehled funkce Update Management, která implementuje aktualizace pro počítače se systémem Windows a Linux.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.openlocfilehash: 0a79be9d879e9ccb7ae4583d0674cf2bb23aafa4
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485669"
---
# <a name="update-management-overview"></a>Přehled Update Managementu

Update Management v Azure Automation můžete použít ke správě aktualizací operačního systému pro virtuální počítače s Windows a Linux v Azure, v místních prostředích a v dalších cloudových prostředích. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

> [!NOTE]
> Počítač nakonfigurovaný s Update Management nemůžete použít ke spouštění vlastních skriptů z Azure Automation. Tento počítač může spustit pouze skript pro aktualizaci podepsaný společností Microsoft.

> [!NOTE]
> V tuto chvíli není podporována povolení Update Management přímo ze serveru s podporou ARC. V tématu [povolení Update Management ze svého účtu Automation](../../automation/update-management/enable-from-automation-account.md) můžete pochopit požadavky a povolit server.

K automatickému stažení a instalaci dostupných *důležitých* a *bezpečnostních oprav zabezpečení* na virtuálním počítači Azure si Projděte [Automatické opravy hosta virtuálního počítače](../../virtual-machines/windows/automatic-vm-guest-patching.md) pro virtuální počítače s Windows.

Než nasadíte Update Management a povolíte správu vašich počítačů, ujistěte se, že rozumíte informacím v následujících částech.  

## <a name="about-update-management"></a>O Update Management

Počítače spravované nástrojem Update Management při vyhodnocování a nasazení aktualizací spoléhají na následující:

* [Agent Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) pro Windows nebo Linux
* Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
* Automatizace Hybrid Runbook Worker (automaticky nainstalováno při povolení Update Management na počítači)
* Microsoft Update nebo [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) pro počítače se systémem Windows
* Soukromé nebo veřejné úložiště aktualizací pro počítače se systémem Linux

Následující diagram znázorňuje, jak Update Management vyhodnocuje a aplikuje aktualizace zabezpečení na všechny připojené servery se systémem Windows Server a Linux v pracovním prostoru:

![Pracovní postup Update Management](./media/overview/update-mgmt-updateworkflow.png)

Update Management lze použít k nasazování nativně do počítačů ve více předplatných ve stejném tenantovi.

Po uvolnění balíčku trvá tato oprava 2 až 3 hodiny, než se oprava zobrazí pro počítače se systémem Linux pro posouzení. U počítačů s Windows trvá 12 až 15 hodin, než se oprava zobrazí po jejím vydání. Když počítač dokončí kontrolu kompatibility aktualizací, agent přepošle informace hromadně do Azure Monitor protokolů. V počítači s Windows se kontrola kompatibility ve výchozím nastavení spouští každých 12 hodin. U počítače se systémem Linux se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud je agent Log Analytics restartován, spustí se kontrola kompatibility do 15 minut.

Kromě plánu skenování je vyhledávání kompatibility aktualizací spuštěno během 15 minut od restartování agenta Log Analytics, před instalací aktualizace a po instalaci aktualizace.

Update Management oznamuje, jak aktuální je počítač založený na zdroji, se kterým jste nakonfigurovali synchronizaci. Pokud je počítač s Windows nakonfigurovaný tak, aby hlásil [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS), v závislosti na tom, kdy se služba WSUS naposledy synchronizuje s Microsoft Update, se výsledky můžou lišit od toho, co Microsoft Update zobrazuje. Toto chování je stejné pro počítače se systémem Linux, které jsou nakonfigurované tak, aby hlásily do místního úložiště místo do veřejného úložiště.

> [!NOTE]
> Aby bylo možné řádně ohlásit službu, Update Management vyžaduje, aby byly povoleny určité adresy URL a porty. Další informace o těchto požadavcích najdete v tématu [Konfigurace sítě](../automation-hybrid-runbook-worker.md#network-planning).

Aktualizace softwaru můžete nasadit a nainstalovat na počítačích, které vyžadují aktualizace, vytvořením plánovaného nasazení. Aktualizace klasifikované jako volitelné nejsou zahrnuty v oboru nasazení pro počítače se systémem Windows. V rozsahu nasazení jsou zahrnuté jenom požadované aktualizace.

Plánované nasazení definuje, které cílové počítače obdrží příslušné aktualizace. K tomu slouží buď explicitním zadáním určitých počítačů, nebo výběrem [skupiny počítačů](../../azure-monitor/logs/computer-groups.md) , která je založena na hledání protokolu konkrétní sady počítačů (nebo na [dotazu Azure](query-logs.md) , který dynamicky vybírá virtuální počítače Azure na základě zadaných kritérií). Tyto skupiny se liší od [Konfigurace oboru](../../azure-monitor/insights/solution-targeting.md), která se používá k řízení cíle počítačů, které přijmou konfiguraci pro povolení Update Management. Tím se zabrání v provádění a hlášení shody aktualizací a instalaci schválených požadovaných aktualizací.

Při definování nasazení také zadáte plán, který schválíte a nastavíte časové období, během kterého lze nainstalovat aktualizace. Toto období se nazývá časový interval pro správu a údržbu. Pro restartování je vyhrazeno 20 minut časového období údržby, za předpokladu, že je potřeba, a vybrali jste vhodnou možnost restartování. Pokud oprava trvá déle, než se čekalo a v časovém intervalu údržby je méně než 20 minut, neproběhne restart.

Aktualizace se instalují po sadách runbook službou Azure Automation. Tyto Runbooky nemůžete zobrazit a nevyžadují žádnou konfiguraci. Při vytvoření nasazení aktualizace se vytvoří plán, který spustí sadu Runbook s aktualizací v zadaném čase pro zahrnuté počítače. Hlavní runbook spouští podřízený Runbook na každém agentovi pro instalaci požadovaných aktualizací.

V datu a čase zadaném v nasazení aktualizace spouštějí cílové počítače paralelně nasazení. Před instalací se spustí Kontrola, aby se ověřilo, že se aktualizace pořád vyžadují. V případě klientských počítačů služby WSUS, pokud aktualizace nejsou schváleny ve službě WSUS, nasazení aktualizace se nepovede.

Počítač zaregistrovaný pro Update Management ve více než jednom pracovním prostoru Log Analytics (označovaný také jako více domovských) se nepodporuje.

## <a name="clients"></a>Klienti

### <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí podporované operační systémy pro posouzení aktualizací a opravy. Oprava vyžaduje systémovou Hybrid Runbook Worker, která se automaticky nainstaluje, když povolíte virtuální počítač nebo server pro správu pomocí Update Management. Informace o Hybrid Runbook Worker systémových požadavcích najdete v tématu [nasazení Hybrid Runbook Worker Windows](../automation-windows-hrw-install.md) a [nasazení Hybrid Runbook Worker pro Linux](../automation-linux-hrw-install.md).

> [!NOTE]
> Posouzení aktualizací počítačů se systémem Linux je podporováno pouze v určitých oblastech, které jsou uvedeny v účtu Automation a v [tabulce mapování](../how-to/region-mappings.md#supported-mappings)Log Analytics pracovního prostoru.

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2019 (Datacenter/Standard, včetně jádra serveru)<br><br>Windows Server 2016 (Datacenter/Standard kromě jádra serveru)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM a SP1 Standard)| Update Management podporuje posouzení a opravy pro tento operační systém. [Hybrid Runbook Worker](../automation-windows-hrw-install.md) se podporuje pro Windows Server 2008 R2. |
|CentOS 6 a 7 (x64)      | Agenti Linux vyžadují přístup k úložišti aktualizací. Oprava založená na klasifikaci vyžaduje `yum` , aby vracela data zabezpečení, která CentOS ve svých verzích RTM. Další informace o opravách na základě klasifikace na CentOS najdete v tématu [klasifikace aktualizací v systému Linux](view-update-assessments.md#linux).          |
|Red Hat Enterprise 6 a 7 (x64)     | Agenti Linux vyžadují přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 12, 15 a 15,1 (x64)     | Agenti Linux vyžadují přístup k úložišti aktualizací. V případě SUSE 15. x se na počítači vyžaduje Python 3.      |
|Ubuntu 14,04 LTS, 16,04 LTS a 18,04 LTS (x64)      |Agenti Linux vyžadují přístup k úložišti aktualizací.         |

> [!NOTE]
> Update Management nepodporuje bezpečné automatizaci správy aktualizací napříč všemi instancemi v sadě škálování virtuálních počítačů Azure. [Automatické upgrady bitových kopií operačního systému](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) jsou doporučeným způsobem správy upgradu imagí operačního systému ve vaší sadě škálování.

### <a name="unsupported-operating-systems"></a>Nepodporované operační systémy

Následující tabulka uvádí operační systémy, které nejsou podporovány nástrojem Update Management:

|Operační systém  |Poznámky  |
|---------|---------|
|Klient Windows     | Klientské operační systémy (například Windows 7 a Windows 10) se nepodporují.<br> Pro virtuální počítače Azure s Windows (WVD) se doporučuje metoda.<br> pro správu aktualizací je [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) pro správu oprav klientských počítačů s Windows 10. |
|Windows Server 2016 Nano Server     | Nepodporováno       |
|Uzly služby Azure Kubernetes | Nepodporováno Použijte proces opravy popsaný v tématu [použití aktualizací zabezpečení a jádra pro uzly Linux ve službě Azure Kubernetes Service (AKS)](../../aks/node-updates-kured.md) .|

### <a name="system-requirements"></a>Požadavky na systém

Následující informace popisují požadavky specifické pro operační systém. Další pokyny najdete v tématu [Plánování sítě](#ports). Pokud chcete pochopit požadavky na TLS 1,2, přečtěte si téma [vynucení TLS 1,2 pro Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

Požadavky na software:

- Vyžaduje se .NET Framework 4,6 nebo novější. ([Stáhněte si .NET Framework](/dotnet/framework/install/guide-for-developers).
- Je vyžadován Windows PowerShell 5,1 ([Stáhnout Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).)

Agenti systému Windows musí být nakonfigurováni pro komunikaci se serverem WSUS nebo vyžadují přístup k Microsoft Update. Pro hybridní počítače doporučujeme nainstalovat agenta Log Analytics pro Windows tím, že nejprve připojíte počítač k [serverům s podporou ARC Azure](../../azure-arc/servers/overview.md)a pak pomocí Azure Policy přiřadíte [agenta nasazení Log Analytics k](../../governance/policy/samples/built-in-policies.md#monitoring) integrovaným zásadám Windows Azure ARC. Případně, pokud plánujete monitorovat počítače pomocí Azure Monitor pro virtuální počítače, použijte raději iniciativu [Enable Azure monitor pro virtuální počítače](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

Update Management můžete použít s Microsoft Endpoint Configuration Manager. Další informace o integračních scénářích najdete v tématu věnovaném [integraci Update Management s Windows Endpoint Configuration Manager](mecmintegration.md). Pro Windows servery spravované lokalitami ve vašem Configuration Manager prostředí se vyžaduje [agent Log Analytics pro Windows](../../azure-monitor/agents/agent-windows.md) .

Ve výchozím nastavení jsou virtuální počítače s Windows, které jsou nasazené z Azure Marketplace, nastavené na příjem automatických aktualizací ze služby web Windows Update. Toto chování se nemění při přidávání virtuálních počítačů s Windows do pracovního prostoru. Pokud nespravujete aktivně aktualizace pomocí Update Management, použije se výchozí chování (pro automatické použití aktualizací).

> [!NOTE]
> Můžete upravit Zásady skupiny, aby bylo možné restartovat počítač pouze tímto uživatelem, nikoli systémem. Spravované počítače můžou zablokovat, pokud Update Management nemá práva k restartování počítače bez ruční interakce uživatele. Další informace najdete v tématu [Konfigurace nastavení zásady skupiny pro automatické aktualizace](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Požadavky na software:

- Počítač vyžaduje přístup k úložišti aktualizací, které je buď soukromé, nebo veřejné.
- K interakci s Update Management je potřeba protokol TLS 1,1 nebo TLS 1,2.
- Je nainstalovaný Python 2. x.

> [!NOTE]
> Posouzení aktualizací počítačů se systémem Linux je podporováno pouze v určitých oblastech. Viz [Tabulka mapování](../how-to/region-mappings.md#supported-mappings)účtu služby Automation a Log Analytics pracovního prostoru.

Pro hybridní počítače doporučujeme nainstalovat agenta Log Analytics pro Linux, a to tak, že nejprve připojíte počítač k [serverům s podporou ARC Azure](../../azure-arc/servers/overview.md)a pak pomocí Azure Policy přiřadíte předdefinovanou zásadu pro [Log Analytics nasadit agenta Azure ARC pro počítače se systémem Linux](../../governance/policy/samples/built-in-policies.md#monitoring) . Případně, pokud plánujete monitorovat počítače pomocí Azure Monitor pro virtuální počítače, použijte raději iniciativu [Enable Azure monitor pro virtuální počítače](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

Virtuální počítače vytvořené z imagí Red Hat Enterprise Linux na vyžádání (RHEL), které jsou k dispozici v Azure Marketplace, se registrují pro přístup k [infrastruktuře RHUI (Red Hat Update Infrastructure)](../../virtual-machines/workloads/redhat/redhat-rhui.md) , která je nasazená v Azure. Jakákoli jiná distribuce systému Linux musí být aktualizována z online úložiště souborů distribuce pomocí metod podporovaných distribucí.

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit a spravovat nasazení aktualizací, potřebujete specifická oprávnění. Další informace o těchto oprávněních najdete v tématu věnovaném [přístupu na základě rolí – Update Management](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Update Management komponenty

Update Management používá prostředky popsané v této části. Tyto prostředky se automaticky přidají do vašeho účtu Automation, když povolíte Update Management.

### <a name="hybrid-runbook-worker-groups"></a>Skupiny Hybrid Runbook Worker

Po povolení Update Management se všechny počítače s Windows, které jsou přímo připojené k vašemu pracovnímu prostoru Log Analytics, automaticky nakonfigurují jako systémové Hybrid Runbook Worker, aby podporovaly Runbooky podporující Update Management.

Každý počítač s Windows, který je spravovaný pomocí Update Management, je uvedený v podokně Hybrid Worker Groups jako systémová skupina hybridních pracovních procesů pro účet Automation. Skupiny používají `Hostname FQDN_GUID` konvence vytváření názvů. Tyto skupiny nemůžete cílit na tyto sady Runbook ve vašem účtu. Pokud se pokusíte, pokus se nezdaří. Tyto skupiny jsou určené pouze pro podporu Update Management. Další informace o zobrazení seznamu počítačů s Windows nakonfigurovaných jako Hybrid Runbook Worker najdete v tématu [zobrazení procesů Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Počítač s Windows můžete přidat do skupiny uživatelů Hybrid Runbook Worker v účtu Automation pro podporu runbooků Automation, pokud používáte stejný účet pro Update Management a Hybrid Runbook Worker členství ve skupině. Tato funkce se přidala do 7.2.12024.0 verze Hybrid Runbook Worker.

### <a name="management-packs"></a>Sady Management Pack

Pokud je vaše skupina pro správu Operations Manager [připojená k pracovnímu prostoru Log Analytics](../../azure-monitor/agents/om-agents.md), v Operations Manager se nainstalují následující sady Management Pack. Tyto sady Management Pack jsou nainstalovány také pro Update Management na přímo připojených počítačích se systémem Windows. Sady Management Pack nemusíte konfigurovat ani spravovat:

* Aktualizace Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP (Management Pack pro nasazení aktualizací)

> [!NOTE]
> Máte-li skupinu pro správu Operations Manager 1807 nebo 2019 připojenou k pracovnímu prostoru Log Analytics a agenty konfigurované ve skupině pro správu pro shromažďování dat protokolu, je nutné parametr přepsat `IsAutoRegistrationEnabled` a v pravidle **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** ho nastavit na hodnotu true.

Další informace o aktualizacích sad Management Pack najdete v tématu [připojení Operations Manager k Azure monitor protokolů](../../azure-monitor/agents/om-agents.md).

> [!NOTE]
> Pokud Update Management chcete plně spravovat počítače pomocí agenta Log Analytics, musíte aktualizovat agenta Log Analytics pro Windows nebo agenta Log Analytics pro Linux. Informace o tom, jak aktualizovat agenta, najdete v tématu [Postup upgradu agenta Operations Manager](/system-center/scom/deploy-upgrade-agents). V prostředích, která používají Operations Manager, je nutné, abyste spustili System Center Operations Manager 2012 R2 UR 14 nebo novějším.

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-sources"></a>Podporované zdroje

Následující tabulka popisuje připojené zdroje, které Update Management podporuje:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Update Management shromažďuje informace o aktualizacích systému z agentů Windows a potom spustí instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Update Management shromažďuje informace o aktualizacích systému z agentů Linux a potom spustí instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Update Management shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu.<br/><br/>Přímé připojení od agenta Operations Manager do Azure Monitor protokolů není vyžadováno. Data se předávají ze skupiny pro správu do pracovního prostoru Log Analytics. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Update Management prohledává spravované počítače pro data pomocí následujících pravidel. Může trvat 30 minut a 6 hodin, než se na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

* Každý počítač se systémem Windows Update Management provádí kontrolu dvakrát denně pro každý počítač.

* Každý počítač se systémem Linux – Update Management prohledává každou hodinu.

Průměrné využití dat pomocí Azure Monitor protokolů pro počítač používající Update Management je přibližně 25 MB za měsíc. Tato hodnota je jenom aproximace a v závislosti na vašem prostředí se může změnit. Doporučujeme monitorovat své prostředí, abyste mohli sledovat přesné využití. Další informace o analýze využití dat protokolů Azure Monitor najdete v tématu [Správa využití a nákladů](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Plánování sítě

Podrobné informace o portech, adresách URL a dalších podrobných informacích o sítích požadovaných pro Update Management najdete v [Azure Automation konfiguraci sítě](../automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) .

U počítačů s Windows musíte taky u všech koncových bodů vyžadovaných nástrojem web Windows Update umožňovat provoz. Aktualizovaný seznam požadovaných koncových bodů najdete v [problémech souvisejících s HTTP/proxy serverem](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Pokud máte místní [web Windows Update Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment), musíte taky na serveru, který určíte v [klíči služby WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry), povolený provoz.

V případě počítačů se systémem Red Hat Linux naleznete v části [IP adresy pro RHUI Content Delivery Server](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) pro požadované koncové body. Další distribuce pro Linux najdete v dokumentaci ke zprostředkovateli.

Další informace o portech potřebných pro Hybrid Runbook Worker najdete v tématu [Update Management adres pro Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Pokud vaše zásady zabezpečení IT neumožňují, aby se počítače v síti připojovaly k Internetu, můžete nastavit [bránu Log Analytics](../../azure-monitor/agents/gateway.md) a potom nakonfigurovat počítač tak, aby se připojil přes bránu a Azure Automation a Azure monitor.

## <a name="update-classifications"></a>Update classifications

Následující tabulka definuje klasifikace, které Update Management podporuje pro aktualizace pro Windows.

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizace pro určitý problém, která řeší kritickou chybu nesouvisející se zabezpečením.        |
|Aktualizace zabezpečení     | Aktualizace pro problém související se zabezpečením určitého produktu.        |
|Kumulativní aktualizace     | Kumulativní sada oprav hotfix, které jsou zabaleny dohromady pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Kumulativní sada oprav hotfix, které se aplikují na aplikaci.        |
|Aktualizace definic     | Aktualizace virů nebo jiných definičních souborů.        |
|nástroje     | Nástroj nebo funkce, které pomáhají dokončit jednu nebo více úloh.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalován.        |

Následující tabulka definuje podporované klasifikace aktualizací pro Linux.

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro konkrétní problém nebo problém související se zabezpečením určitého produktu.         |
|Další aktualizace     | Všechny ostatní aktualizace, které nejsou v podstatě důležité nebo které nejsou aktualizacemi zabezpečení.        |

>[!NOTE]
>Klasifikace aktualizací pro počítače se systémem Linux je k dispozici pouze při použití v podporovaných oblastech veřejného cloudu Azure. Při použití Update Management v následujících národních cloudových oblastech není k dispozici žádná klasifikace aktualizací pro Linux:
>
>* Azure pro vládu USA
>* 21Vianet v Číně
>
> Místo klasifikace se aktualizace nahlásí v kategorii **ostatní aktualizace** .
>
> Update Management používá data publikovaná v podporovaných distribucích, konkrétně v jejich vydaných [oválech](https://oval.mitre.org/) (otevřené soubory zabezpečení a posouzení). Vzhledem k tomu, že přístup k Internetu je z těchto národních cloudů omezený, Update Management nemá přístup k souborům.

V případě systému Linux může Update Management rozlišovat mezi důležitými aktualizacemi a aktualizacemi zabezpečení v cloudu pod položkou **zabezpečení** klasifikace a **ostatními** a současně zobrazuje data vyhodnocení v důsledku rozšíření dat v cloudu. Pro opravy Update Management spoléhá na data klasifikace, která jsou k dispozici v počítači. Na rozdíl od jiných distribucí nemá CentOS tyto informace dostupné ve verzi RTM. Pokud máte počítače CentOS nakonfigurované tak, aby vracely data zabezpečení pro následující příkaz, Update Management se může opravit na základě klasifikací.

```bash
sudo yum -q --security check-update
```

V současné době není podporována žádná podporovaná metoda pro povolení nativní klasifikace – dostupnost dat v CentOS. V tuto chvíli se zákazníkům, kteří si tuto funkci povolili, poskytne omezená podpora.

Chcete-li klasifikovat aktualizace na Red Hat Enterprise verze 6, je nutné nainstalovat modul plug-in Yumu-Security. V Red Hat Enterprise Linux 7 je modul plug-in již součástí samotného Yumu a není nutné nic instalovat. Další informace najdete v následujícím [článku znalostní báze](https://access.redhat.com/solutions/10021)Red Hat.

Když naplánujete, aby se aktualizace spouštěla v počítači se systémem Linux, který je například nakonfigurován pro instalaci pouze aktualizací, které odpovídají klasifikaci **zabezpečení** , nainstalované aktualizace se mohou lišit od nástroje nebo jsou podmnožinou aktualizací, které odpovídají této klasifikaci. Když se provede posouzení aktualizací operačního systému, které čekají na váš počítač se systémem Linux, [otevřete](https://oval.mitre.org/) Update Management pro klasifikaci pomocí souborů.

Kategorizace se provádí pro aktualizace pro Linux jako **zabezpečení** nebo **jiné** na základě elipsových souborů, včetně aktualizací, které řeší problémy zabezpečení nebo chyby zabezpečení. Ale při spuštění plánu aktualizace se na počítači se systémem Linux spustí pomocí příslušného správce balíčků, jako je YUMU, APT nebo ZYPPERU, aby se nainstalovaly. Správce balíčků pro Linux distribuce může mít jiný mechanismus pro klasifikaci aktualizací, kde se výsledky mohou lišit od těch, které jsou získány ze souborů ELIPSy Update Management. Pokud chcete ručně kontrolovat počítač a porozumět tomu, které aktualizace jsou důležité pro správce balíčků, přečtěte si téma [řešení potíží s nasazením aktualizací pro Linux](../troubleshoot/update-management.md#updates-linux-installed-different).

## <a name="integrate-update-management-with-configuration-manager"></a>Integrace Update Management s Configuration Manager

Zákazníci, kteří investovali do služby Microsoft Endpoint Configuration Manager pro správu počítačů, serverů a mobilních zařízení, spoléhají také na sílu a splatnost Configuration Manager, které vám pomůžou se správou aktualizací softwaru. Informace o tom, jak integrovat Update Management s Configuration Manager, najdete v tématu věnovaném [integraci Update Management s Configuration Manager Windows Endpoint](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Aktualizace třetích stran ve Windows

Update Management spoléhá na místně nakonfigurované úložiště aktualizací, které aktualizuje podporované systémy Windows, a to buď WSUS, nebo web Windows Update. Nástroje, jako je [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) , umožňují importovat a publikovat vlastní aktualizace pomocí služby WSUS. Tento scénář umožňuje Update Management aktualizovat počítače, které používají Configuration Manager jako úložiště aktualizací se softwarem třetích stran. Informace o tom, jak nakonfigurovat aplikaci Updates Publisher, najdete v tématu [install Updates Publisher](/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Povolení řešení Update Management

Tady jsou způsoby, jak můžete povolit Update Management a vybrat počítače, které se mají spravovat:

- Použití šablony Azure [Správce prostředků](enable-from-template.md) k nasazení Update Management do nového nebo existujícího účtu Automation a Azure monitor pracovního prostoru Log Analytics v rámci vašeho předplatného. Nekonfiguruje obor počítačů, které by se měly spravovat, to se provádí jako samostatný krok po použití šablony.

- Z [účtu Automation](enable-from-automation-account.md) pro jeden nebo víc počítačů Azure a mimo Azure, včetně serverů s podporou ARC.

- Pomocí metody [Runbooku](enable-from-runbook.md) **Enable-AutomationSolution** .

- Pro [vybraný virtuální počítač Azure](enable-from-vm.md) ze stránky **virtuální počítače** v Azure Portal. Tento scénář je k dispozici pro virtuální počítače se systémy Linux a Windows.

- Pro [více virtuálních počítačů Azure](enable-from-portal.md) je můžete vybrat ze stránky **virtuální počítače** v Azure Portal.

> [!NOTE]
> Update Management vyžaduje propojení pracovního prostoru Log Analytics s vaším účtem Automation. Konečný seznam podporovaných oblastí najdete v tématu [mapování pracovních prostorů Azure](../how-to/region-mappings.md). Mapování oblastí neovlivňují možnost spravovat virtuální počítače v samostatné oblasti z účtu Automation.

## <a name="next-steps"></a>Další kroky

* Podrobnosti o práci s Update Management najdete v tématu [Správa aktualizací pro vaše virtuální počítače](manage-updates-for-vm.md).

* Přečtěte si nejčastější dotazy týkající se Update Management v [Azure Automationch nejčastějších dotazech](../automation-faq.md).
