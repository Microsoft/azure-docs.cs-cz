---
title: Řešení Update Management v Azure
description: Tento článek popisuje, jak pomocí řešení Azure Update Management spravovat aktualizace pro počítače se systémem Windows a Linux.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 9bdc8cf97513854cf6a92ffd078febca6302d35c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617407"
---
# <a name="update-management-solution-in-azure"></a>Řešení Update Management v Azure

**Řešení Update Management** v Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače s Windows a Linux v Azure, v místních prostředích a v dalších cloudových prostředích. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Update Management pro virtuální počítače můžete povolit pomocí následujících metod:

- Z [účtu Azure Automation](automation-onboard-solutions-from-automation-account.md) pro jeden nebo více počítačů Azure a ručně pro počítače mimo Azure.

- Pro jeden virtuální počítač Azure ze stránky virtuálního počítače v Azure Portal. Tento scénář je k dispozici pro virtuální počítače se systémy [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) a [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

- Pro [více virtuálních počítačů Azure](manage-update-multi.md) je můžete vybrat ze stránky virtuální počítače v Azure Portal. 

> [!NOTE]
> **Řešení Update Management** vyžaduje propojení pracovního prostoru Log Analytics s vaším účtem Automation. Konečný seznam podporovaných oblastí najdete v tématu [mapování pracovních prostorů Azure](./how-to/region-mappings.md). Mapování oblastí neovlivňují možnost spravovat virtuální počítače v samostatné oblasti z účtu Automation.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Šablona Azure [Správce prostředků](automation-update-management-deploy-template.md) je k dispozici, která vám umožní nasadit **řešení Update Management** do nového nebo existujícího účtu Automation a Log Analytics pracovní prostor ve vašem předplatném.

## <a name="solution-overview"></a>Přehled řešení

Počítače spravované pomocí Update Management pro vyhodnocení a nasazení aktualizací používají následující konfigurace:

* Agent Log Analytics pro Windows nebo Linux
* Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
* Funkci Hybrid Runbook Worker služby Automation
* Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače se systémem Windows

Následující diagram znázorňuje, jak řešení vyhodnocuje a aplikuje aktualizace zabezpečení na všechny připojené počítače s Windows serverem a Linux v pracovním prostoru:

![Tok procesu Update Management](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management můžete nativně nasadit na počítače v různých předplatných stejného tenanta.

Po uvolnění balíčku trvá tato oprava 2 až 3 hodiny, než se oprava zobrazí pro počítače se systémem Linux pro posouzení. U počítačů s Windows trvá 12 až 15 hodin, než se oprava zobrazí po jejím vydání.

Jakmile počítač dokončí kontrolu kompatibility aktualizací, agent přepošle informace hromadně do Azure Monitor protokolů. V počítači s Windows se kontrola kompatibility ve výchozím nastavení spouští každých 12 hodin.

Kromě plánu skenování je vyhledávání shody aktualizací zahájeno během 15 minut od restartování agenta Log Analytics, před instalací aktualizace a po instalaci aktualizace.

U počítače se systémem Linux se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud je agent Log Analytics restartován, zahájí se kontrola kompatibility do 15 minut.

Řešení oznamuje, jak aktuální je počítač založený na zdroji, se kterým jste nakonfigurovali synchronizaci. Pokud je počítač s Windows nakonfigurovaný tak, aby hlásil službu WSUS, v závislosti na tom, kdy se služba WSUS naposledy synchronizoval s Microsoft Update, se výsledky můžou lišit od toho, co Microsoft Update zobrazuje. Toto chování je stejné pro počítače se systémem Linux, které jsou nakonfigurované tak, aby hlásily do místního úložiště místo do veřejného úložiště.

> [!NOTE]
> Aby bylo možné řádně ohlásit službu, Update Management vyžaduje, aby byly povoleny určité adresy URL a porty. Další informace o těchto požadavcích najdete v tématu [Plánování sítě pro hybridní pracovní procesy](automation-hybrid-runbook-worker.md#network-planning).

Aktualizace softwaru můžete nasadit a nainstalovat na počítačích, které vyžadují aktualizace, vytvořením plánovaného nasazení. Aktualizace klasifikované jako *volitelné* nejsou zahrnuty v oboru nasazení pro počítače se systémem Windows. V rozsahu nasazení jsou zahrnuté jenom požadované aktualizace.

Plánované nasazení definuje, které cílové počítače obdrží příslušné aktualizace. K tomu slouží buď explicitním zadáním určitých počítačů, nebo výběrem [skupiny počítačů](../azure-monitor/platform/computer-groups.md) , která je založena na hledání protokolu konkrétní sady počítačů (nebo na [dotazu Azure](automation-update-management-query-logs.md) , který dynamicky vybírá virtuální počítače Azure na základě zadaných kritérií). Tyto skupiny se liší od [Konfigurace oboru](../azure-monitor/insights/solution-targeting.md), která se používá pouze k určení, které počítače získají sady Management Pack umožňující řešení.

Také zadáte plán, který schválíte a nastavíte časové období, během kterého lze nainstalovat aktualizace. Toto období se nazývá časový interval pro správu a údržbu. Pro restartování je vyhrazeno 20 minut časového období údržby, za předpokladu, že je potřeba, a vybrali jste vhodnou možnost restartování. Pokud oprava trvá déle, než se čekalo a v časovém intervalu údržby je méně než 20 minut, neproběhne restart.

Aktualizace se instalují podle runbooků ve službě Azure Automation. Tyto Runbooky nemůžete zobrazit a nevyžadují žádnou konfiguraci. Při vytvoření nasazení aktualizace se vytvoří plán, který spustí sadu Runbook s aktualizací v zadaném čase pro zahrnuté počítače. Hlavní runbook spouští podřízený Runbook na každém agentovi pro instalaci požadovaných aktualizací.

V datu a čase zadaném v nasazení aktualizace spouštějí cílové počítače paralelně nasazení. Před instalací se spustí Kontrola, aby se ověřilo, že se aktualizace pořád vyžadují. V případě klientských počítačů služby WSUS, pokud aktualizace nejsou schváleny ve službě WSUS, nasazení aktualizace se nepovede.

Počítač zaregistrovaný pro Update Management ve více než jednom pracovním prostoru Log Analytics (označovaný také jako více domovských) se nepodporuje.

## <a name="clients"></a>Klienti

### <a name="supported-client-types"></a>Podporované typy klientů

Následující tabulka uvádí podporované operační systémy pro posouzení aktualizací. Oprava vyžaduje Hybrid Runbook Worker. Informace o požadavcích na Hybrid Runbook Worker najdete v pokynech k instalaci pro instalaci [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) a [Linux Hybrid Runbook Worker](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM a SP1 Standard)| Update Management podporuje jenom vyhodnocení pro tento operační systém, takže se nepodporuje aktualizace, protože [Hybrid Runbook Worker](automation-windows-hrw-install.md) není pro Windows Server 2008 R2 podporovaná. |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenti Linux vyžadují přístup k úložišti aktualizací. Oprava založená na klasifikaci vyžaduje `yum` , aby vracela data zabezpečení, která CentOS ve svých verzích RTM. Další informace o opravách na základě klasifikace na CentOS najdete v tématu [klasifikace aktualizací v systému Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenti Linux vyžadují přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenti Linux vyžadují přístup k úložišti aktualizací.        |
|Ubuntu 14,04 LTS, 16,04 LTS a 18,04 (x86/x64)      |Agenti Linux vyžadují přístup k úložišti aktualizací.         |

> [!NOTE]
> Sady škálování virtuálních počítačů Azure je možné spravovat prostřednictvím Update Management. Update Management pracuje na samotných instancích a nikoli na základní imagi. Aktualizace budete muset naplánovat přírůstkově, takže se neaktualizují všechny instance virtuálních počítačů najednou.
> Uzly pro sadu škálování virtuálního počítače můžete přidat pomocí postupu v části připojení [počítače mimo Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Nepodporované typy klientů

V následující tabulce jsou uvedeny nepodporované operační systémy:

|Operační systém  |Poznámky  |
|---------|---------|
|Klient Windows     | Klientské operační systémy (například Windows 7 a Windows 10) se nepodporují.        |
|Windows Server 2016 Nano Server     | Není podporováno.       |
|Uzly služby Azure Kubernetes | Není podporováno. Použijte proces opravy popsaný v tématu [použití aktualizací zabezpečení a jádra pro uzly Linux ve službě Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md) .|

### <a name="client-requirements"></a>Požadavky na klienty

Následující informace popisují požadavky klienta na konkrétní operační systémy. Další pokyny najdete v tématu [Plánování sítě](#ports).

#### <a name="windows"></a>Windows

Agenti systému Windows musí být nakonfigurováni pro komunikaci se serverem WSUS nebo vyžadují přístup k Microsoft Update.

Update Management můžete použít s Configuration Manager. Další informace o integračních scénářích najdete v tématu věnovaném [integraci Configuration Manager s Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). Log Analytics je vyžadován [Agent pro Windows](../azure-monitor/platform/agent-windows.md) . Pokud se připojujete k virtuálnímu počítači Azure, Agent se nainstaluje automaticky.

Ve výchozím nastavení jsou virtuální počítače s Windows, které jsou nasazené z Azure Marketplace, nastavené na příjem automatických aktualizací ze služby web Windows Update. Toto chování se nemění, když přidáte toto řešení nebo do svého pracovního prostoru přidáte virtuální počítače s Windows. Pokud nespravujete aktivně aktualizace pomocí tohoto řešení, platí výchozí chování (pro automatické použití aktualizací).

> [!NOTE]
> Uživatel může upravit Zásady skupiny, aby bylo možné restartovat počítač pouze tímto uživatelem, nikoli systémem. Spravované počítače můžou zablokovat, pokud Update Management nemá práva k restartování počítače bez ruční interakce uživatele.
>
> Další informace najdete v tématu [Konfigurace nastavení zásady skupiny pro automatické aktualizace](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Pro Linux vyžaduje počítač přístup k úložišti aktualizací. Úložiště aktualizací může být privátní nebo veřejné. K interakci s Update Management je potřeba protokol TLS 1,1 nebo TLS 1,2. Pro toto řešení se nepodporuje agent Log Analytics pro Linux, který je nakonfigurovaný tak, aby hlásil do více než jednoho pracovního prostoru Log Analytics. Počítač musí mít také nainstalovaný Python 2. x.

Informace o tom, jak nainstalovat agenta Log Analytics pro Linux a stáhnout nejnovější verzi, najdete v tématu [Log Analytics Agent pro Linux](../azure-monitor/platform/agent-linux.md). Informace o tom, jak nainstalovat agenta Log Analytics pro Windows, najdete v tématu [připojení počítačů s Windows k Azure monitor](../log-analytics/log-analytics-windows-agent.md).

Virtuální počítače vytvořené z imagí Red Hat Enterprise Linux na vyžádání (RHEL), které jsou k dispozici v Azure Marketplace jsou registrované pro přístup k [infrastruktuře RHUI (Red Hat Update Infrastructure)](../virtual-machines/workloads/redhat/redhat-rhui.md) , která je nasazená v Azure. Jakákoli jiná distribuce systému Linux musí být aktualizována z online úložiště souborů distribuce pomocí podporovaných metod distribuce.

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit a spravovat nasazení aktualizací, potřebujete specifická oprávnění. Další informace o těchto oprávněních najdete v tématu věnovaném [přístupu na základě rolí – Update Management](automation-role-based-access-control.md#update-management-permissions).

## <a name="solution-components"></a>Součásti řešení

Řešení se skládá z následujících zdrojů. Tyto prostředky se po povolení řešení automaticky přidají do vašeho účtu Automation. 

### <a name="hybrid-worker-groups"></a>Skupiny Hybrid Worker

Po povolení tohoto řešení se všechny počítače se systémem Windows, které jsou přímo připojené k vašemu Log Analytics pracovního prostoru, automaticky nakonfigurují jako Hybrid Runbook Worker pro podporu runbooků, které jsou součástí tohoto řešení.

Každý počítač s Windows, který je spravovaný řešením, je uvedený v podokně Hybrid Worker Groups jako systémová skupina hybridních pracovních procesů pro účet Automation. Řešení používají konvence `Hostname FQDN_GUID` vytváření názvů. Tyto skupiny nemůžete cílit na tyto sady Runbook ve vašem účtu. Pokud se pokusíte, pokus se nezdaří. Tyto skupiny jsou určené pro podporu pouze tohoto řešení pro správu.

Počítač s Windows můžete přidat do skupiny Hybrid Runbook Worker v účtu Automation pro podporu runbooků Automation, pokud použijete stejný účet pro řešení i pro členství v Hybrid Runbook Worker skupině. Tato funkce se přidala do 7.2.12024.0 verze Hybrid Runbook Worker.

### <a name="management-packs"></a>Sady Management Pack

Pokud je vaše skupina pro správu System Center Operations Manager [připojená k pracovnímu prostoru Log Analytics](../azure-monitor/platform/om-agents.md), v Operations Manager se nainstalují následující sady Management Pack. Tyto sady Management Pack se nainstalují také na přímo připojené počítače s Windows po přidání řešení. Sady Management Pack nemusíte konfigurovat ani spravovat:

* Aktualizace Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Aktualizace sady pro správu nasazení

> [!NOTE]
> Máte-li skupinu pro správu Operations Manager 1807 nebo 2019 připojenou k pracovnímu prostoru Log Analytics a agenty konfigurované ve skupině pro správu pro shromažďování dat protokolu, je třeba přepsat `IsAutoRegistrationEnabled` parametr a nastavit jej na hodnotu true v pravidle **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Další informace o tom, jak se aktualizují sady Management Pack řešení, najdete v tématu [připojení Operations Manager k Azure monitor protokolů](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Pro počítače s agentem Operations Manageru, které je možné plně spravovat pomocí Update Management, musí být agent aktualizovaný na agenty Log Analytics pro Windows nebo agenta Log Analytics pro Linux. Informace o tom, jak aktualizovat agenta, najdete v tématu [Postup upgradu agenta Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). V prostředích, která používají Operations Manager, je nutné, abyste spustili System Center Operations Manager 2012 R2 UR 14 nebo novějším.

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů systému Windows a poté inicializuje instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Řešení shromažďuje informace o aktualizacích systému od agentů systému Linux a následně inicializuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu.<br/><br/>Přímé připojení od agenta Operations Manager do Azure Monitor protokolů není vyžadováno. Data se předávají ze skupiny pro správu do pracovního prostoru Log Analytics. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Pro každý spravovaný počítač s Windows se kontrola provádí dvakrát denně. Každých 15 minut se volá rozhraní Windows API, které se dotazuje na čas poslední aktualizace, aby se zjistilo, jestli se změnil stav. Pokud se stav změnil, je zahájeno prohledávání dodržování předpisů.

Kontrola se provádí každou hodinu každého spravovaného počítače se systémem Linux.

Může trvat 30 minut a 6 hodin, než se na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

Průměrné využití dat pomocí Azure Monitor protokolů pro počítač používající Update Management je přibližně 25 megabajtů (MB) měsíčně. Tato hodnota je jenom aproximace a v závislosti na vašem prostředí se může změnit. Doporučujeme monitorovat své prostředí, abyste mohli sledovat přesné využití. Další informace o analýze využití dat najdete v tématu [Správa využití a nákladů](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Plánování sítě

Následující adresy jsou vyžadovány konkrétně pro Update Management. Komunikace s těmito adresami probíhá přes port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

U počítačů s Windows musíte taky u všech koncových bodů vyžadovaných nástrojem web Windows Update umožňovat provoz. Aktualizovaný seznam požadovaných koncových bodů najdete v [problémech souvisejících s HTTP/proxy serverem](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Pokud máte místní [web Windows Update Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment), musíte taky na serveru, který určíte v [klíči služby WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry), povolený provoz.

V případě počítačů se systémem Red Hat Linux naleznete v části [IP adresy pro RHUI Content Delivery Server](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) pro požadované koncové body. Další distribuce pro Linux najdete v dokumentaci ke zprostředkovateli.

Další informace o portech potřebných pro Hybrid Runbook Worker najdete v tématu [Hybrid Worker portů rolí](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Doporučujeme používat adresy uvedené při definování výjimek. Pro IP adresy můžete stáhnout [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se aktualizuje týdně a odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres.

Při konfiguraci počítačů, které nemají přístup k Internetu, postupujte podle pokynů v tématu [připojení počítačů bez přístupu k Internetu](../azure-monitor/platform/gateway.md) .

## <a name="update-classifications"></a>Update classifications

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
|Nástroje     | Nástroj nebo funkce, které pomáhají dokončit jednu nebo více úloh.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalován.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

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

### <a name="third-party-updates-on-windows"></a>Aktualizace třetích stran ve Windows

Update Management spoléhá na místně nakonfigurované úložiště aktualizací pro aktualizaci podporovaných systémů Windows. Toto je buď služba WSUS, nebo web Windows Update. Nástroje, jako je [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher), umožňují importovat a publikovat vlastní aktualizace pomocí služby WSUS. Tento scénář umožňuje Update Management aktualizovat počítače, které používají Configuration Manager jako úložiště aktualizací se softwarem třetích stran. Informace o tom, jak nakonfigurovat aplikaci Updates Publisher, najdete v tématu [install Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Povolit Update Management

Chcete-li začít aktualizovat systémy, je nutné povolit řešení Update Management. Níže jsou uvedené doporučené a podporované metody připojení řešení:

- [Z virtuálního počítače](automation-onboard-solutions-from-vm.md)
- [Z prohlížení více počítačů](automation-onboard-solutions-from-browse.md)
- [Z účtu Automation](automation-onboard-solutions-from-automation-account.md)
- [S Azure Automation sadou Runbook](automation-onboard-solutions.md)
- [Se šablonou Azure Resource Manager](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější](automation-faq.md) dotazy k Azure Automation, kde najdete nejčastější dotazy k tomuto řešení.
