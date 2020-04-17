---
title: Řešení správy aktualizací v Azure
description: Tento článek popisuje, jak používat řešení Azure Update Management ke správě aktualizací pro počítače s Windows a Linuxem.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: eaba4bf7760e150f2477ee743c797f94784b8506
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535498"
---
# <a name="update-management-solution-in-azure"></a>Řešení správy aktualizací v Azure

Řešení správy aktualizací v Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače s Windows a Linuxem v Azure, v místních prostředích a v jiných cloudových prostředích. Můžete rychle posoudit stav dostupných aktualizací na všech počítačích agentů a spravovat proces instalace požadovaných aktualizací pro servery.

Službu Správa aktualizací pro virtuální počítače (VM) můžete povolit pomocí následujících metod:

- Z vašeho [účtu Azure Automation](automation-onboard-solutions-from-automation-account.md) pro jeden nebo více počítačů Azure a ručně pro počítače než Azure.

- Pro jeden virtuální počítač Azure ze stránky virtuálního počítače na webu Azure Portal. Tento scénář je k dispozici pro [virtuální počítače s Linuxem](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) a [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Pro [více virtuálních počítačů Azure](manage-update-multi.md) jejich výběrem ze stránky Virtuální **počítače** na webu Azure Portal. 

> [!NOTE]
> Řešení správy aktualizací vyžaduje propojení pracovního prostoru Log Analytics s vaším účtem Automation. Konečný seznam podporovaných oblastí najdete v [tématu mapování pracovního prostoru Azure](./how-to/region-mappings.md). Mapování oblasti nemají vliv na možnost spravovat virtuální počítače v samostatné oblasti od vašeho účtu automatizace.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

K dispozici je šablona Azure [Resource Manager,](automation-update-management-deploy-template.md) která umožňuje nasadit řešení správy aktualizací do nového nebo stávajícího účtu Automation a pracovního prostoru Log Analytics ve vašem předplatném.

## <a name="solution-overview"></a>Přehled řešení

Počítače spravované službou Správa aktualizací používají k provádění vyhodnocení a aktualizaci nasazení následující konfigurace:

* Agent Log Analytics pro Windows nebo Linux
* Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
* Funkci Hybrid Runbook Worker služby Automation
* Služba Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače se systémem Windows

Následující diagram znázorňuje, jak řešení posuzuje a aplikuje aktualizace zabezpečení na všechny připojené počítače se systémem Windows Server a Linux v pracovním prostoru:

![Tok procesu správy aktualizací](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management můžete nativně nasadit na počítače v různých předplatných stejného tenanta.

Po vydání balíčku trvá 2 až 3 hodiny, než se oprava zobrazí pro počítače s Linuxem pro posouzení. Pro počítače se systémem Windows trvá 12 až 15 hodin, než se oprava zobrazí k posouzení po jeho vydání.

Po dokončení kontroly pro aktualizaci dodržování předpisů agent předává informace hromadně protokoly Azure Monitor. V počítači se systémem Windows je kontrola dodržování předpisů ve výchozím nastavení spouštěna každých 12 hodin.

Kromě plánu kontroly je kontrola dodržování předpisů aktualizace zahájena do 15 minut od restartování agenta Log Analytics, před instalací aktualizace a po instalaci aktualizace.

U počítače s Linuxem se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud je agent Analýzy protokolů restartován, kontrola dodržování předpisů se spustí do 15 minut.

Řešení hlásí, jak aktuální je počítač založen na tom, s jakým zdrojem jste nakonfigurováni pro synchronizaci. Pokud je počítač se systémem Windows nakonfigurován tak, aby se hlásil službě WSUS, v závislosti na tom, kdy byl server WSUS naposledy synchronizován se službou Microsoft Update, mohou se výsledky lišit od toho, co zobrazuje služba Microsoft Update. Toto chování je stejné pro počítače s Linuxem, které jsou nakonfigurovány pro hlášení místní horečnaté místo veřejného repo.

> [!NOTE]
> Chcete-li správně hlásit do služby, správa aktualizací vyžaduje určité adresy URL a porty, které mají být povoleny. Další informace o těchto požadavcích naleznete v [tématu Plánování sítě pro hybridní pracovníky](automation-hybrid-runbook-worker.md#network-planning).

Můžete nasadit a nainstalovat aktualizace softwaru v počítačích, které vyžadují aktualizace vytvořením plánovaného nasazení. Aktualizace klasifikované jako *volitelné* nejsou zahrnuty v oboru nasazení pro počítače se systémem Windows. V oboru nasazení jsou zahrnuty pouze požadované aktualizace.

Naplánované nasazení definuje, které cílové počítače obdrží příslušné aktualizace. Činí tak buď explicitním zadáním určitých počítačů, nebo výběrem [skupiny počítačů,](../azure-monitor/platform/computer-groups.md) která je založena na hledání protokolu konkrétní sady počítačů (nebo na [dotazu Azure,](automation-update-management-query-logs.md) který dynamicky vybírá virtuální počítače Azure na základě zadaných kritérií). Tyto skupiny se liší od [konfigurace oboru](../azure-monitor/insights/solution-targeting.md), který se používá pouze k určení, které počítače získat sady Management Pack, které umožňují řešení.

Můžete také zadat plán pro schválení a nastavení časového období, během kterého lze nainstalovat aktualizace. Toto období se nazývá okno údržby. 20minutové rozpětí okna údržby je vyhrazeno pro restartování, za předpokladu, že je potřeba jeden a vybrali jste vhodnou možnost restartování. Pokud oprava trvá déle, než bylo očekáváno a v okně údržby je méně než 20 minut, restartování nedojde.

Aktualizace se instalují podle runbooků ve službě Azure Automation. Tyto sady Runbook nelze zobrazit a nevyžadují žádnou konfiguraci. Při vytvoření nasazení aktualizace vytvoří plán, který spustí runbook hlavní aktualizace v určený čas pro zahrnuté počítače. Hlavní runbook spustí podřízený runbook u každého agenta k instalaci požadovaných aktualizací.

K datu a času zadanému v nasazení aktualizace cílové počítače spustit nasazení paralelně. Před instalací je spuštěna prohledávač, která ověří, zda jsou aktualizace stále vyžadovány. Pro klientské počítače služby WSUS, pokud aktualizace nejsou schváleny v služba WSUS, nasazení aktualizace se nezdaří.

Mít počítač registrovaný pro správu aktualizací ve více než jednom pracovním prostoru Log Analytics (označované také jako multihoming) není podporováno.

## <a name="clients"></a>Klienti

### <a name="supported-client-types"></a>Podporované typy klientů

V následující tabulce jsou uvedeny podporované operační systémy pro hodnocení aktualizací. Oprava vyžaduje hybridní pracovník runbook. Informace o požadavcích na hybridní pracovník sady Runbook naleznete v instalačních příručkách pro instalaci [pracovníka hybridnísady Runbook systému Windows](automation-windows-hrw-install.md) a [hybridního pracovníka sady Runbook pro Systém Windows](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2019 (jádro/standard datového centra)<br><br>Windows Server 2016 (jádro/standard datového centra/standard)<br><br>Windows Server 2012 R2 (datové centrum/standardní)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM a SP1 Standard)| Správa aktualizací podporuje pouze provádění hodnocení pro tento operační systém, opravy nejsou podporovány, protože [hybridní pracovník sady Runbook](automation-windows-hrw-install.md) není podporován pro systém Windows Server 2008 R2. |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenti Linuxu vyžadují přístup k úložišti aktualizací. Opravy založené na `yum` klasifikaci vyžaduje vrácení dat zabezpečení, která CentOS nemá ve svých verzích RTM. Další informace o opravách založených na klasifikaci na CentOS naleznete v [tématu Aktualizace klasifikace na Linuxu](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenti Linuxu vyžadují přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenti Linuxu vyžadují přístup k úložišti aktualizací.        |
|Ubuntu 14.04 LTS, 16.04 LTS a 18.04 (x86/x64)      |Agenti Linuxu vyžadují přístup k úložišti aktualizací.         |

> [!NOTE]
> Škálovací sady virtuálních strojů Azure lze spravovat prostřednictvím správy aktualizací. Správa aktualizací funguje na samotných instancích a ne na základní bitové kopii. Aktualizace budete muset naplánovat přírůstkovým způsobem, aby se neaktualizovaly všechny instance virtuálních aplikací najednou.
> Uzly pro škálovací sady virtuálních strojů můžete přidat podle kroků uvedených v části [Připojení počítače jiného než Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Nepodporované typy klientů

V následující tabulce jsou uvedeny nepodporované operační systémy:

|Operační systém  |Poznámky  |
|---------|---------|
|Klient Windows     | Klientské operační systémy (například Windows 7 a Windows 10) nejsou podporovány.        |
|Windows Server 2016 Nano Server     | Není podporováno.       |
|Uzly služeb Azure Kubernetes | Není podporováno. Použití procesu oprav popsaného v [části Použití aktualizací zabezpečení a jádra u uzlů Linuxu ve službě Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Požadavky na klienty

Následující informace popisují požadavky klientů specifických pro operační systém. Další pokyny naleznete v [tématu Plánování sítě](#ports).

#### <a name="windows"></a>Windows

Agenti systému Windows musí být nakonfigurováni pro komunikaci se serverem služby WSUS nebo vyžadují přístup k webu Microsoft Update.

Pomocí nástroje Správa aktualizací můžete použít nástroj Configuration Manager. Další informace o scénářích integrace najdete [v tématu Integrace nástroje Configuration Manager se správou aktualizací](oms-solution-updatemgmt-sccmintegration.md#configuration). Je vyžadován [agent systému Windows analýzy protokolů.](../azure-monitor/platform/agent-windows.md) Agent se nainstaluje automaticky, pokud zapisujete virtuální počítač Azure.

Ve výchozím nastavení jsou virtuální počítače s Windows, které jsou nasazené z Azure Marketplace, nastaveny tak, aby přijímaly automatické aktualizace ze služby Windows Update. Toto chování se nezmění, když přidáte toto řešení nebo přidáte virtuální počítače windows do pracovního prostoru. Pokud aktualizace aktivně nespravujete pomocí tohoto řešení, použije se výchozí chování (pro automatické použití aktualizací).

> [!NOTE]
> Uživatel může upravit zásady skupiny tak, aby restartování počítače může provádět pouze uživatel, nikoli systém. Spravované počítače se mohou zaseknout, pokud správa aktualizací nemá práva k restartování počítače bez ruční interakce uživatele.
>
> Další informace naleznete v [tématu Konfigurace nastavení zásad skupiny pro automatické aktualizace](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Pro Linux počítač vyžaduje přístup k úložišti aktualizací. Úložiště aktualizací může být soukromé nebo veřejné. Pro interakci se správou aktualizací je vyžadována tls 1.1 nebo TLS 1.2. Agent analýzy protokolů pro Linux, který je nakonfigurovaný tak, aby se hlásil do více než jednoho pracovního prostoru Log Analytics, není tímto řešením podporován. Počítač musí mít také nainstalovanou aplikaci Python 2.x.

Informace o tom, jak nainstalovat agenta Log Analytics pro Linux a stáhnout nejnovější verzi, naleznete v [tématu Log Analytics agent pro Linux](../azure-monitor/platform/agent-linux.md). Informace o instalaci agenta Log Analytics pro Windows najdete v [tématu Připojení počítačů se systémem Windows k Azure Monitoru](../log-analytics/log-analytics-windows-agent.md).

Virtuální počítače, které byly vytvořeny z image Red Hat Enterprise Linux (RHEL) na vyžádání, které jsou dostupné na Azure Marketplace, jsou registrované pro přístup k [infrastruktuře aktualizací Red Hat (RHUI),](../virtual-machines/workloads/redhat/redhat-rhui.md) která se nasadí v Azure. Jakákoli jiná distribuce Linuxu musí být aktualizována z online úložiště souborů distribuce pomocí podporovaných metod distribuce.

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit a spravovat nasazení aktualizací, potřebujete určitá oprávnění. Informace o těchto oprávněních naleznete v [tématu Přístup založený na rolích – správa aktualizací](automation-role-based-access-control.md#update-management-permissions).

## <a name="solution-components"></a>Součásti řešení

Řešení se skládá z následujících prostředků. Tyto prostředky jsou automaticky přidány do účtu automatizace při povolení řešení. 

### <a name="hybrid-worker-groups"></a>Skupiny Hybrid Worker

Po povolení tohoto řešení je každý počítač se systémem Windows, který je přímo připojen k pracovnímu prostoru Log Analytics, automaticky nakonfigurován jako hybridní pracovník sady Runbook pro podporu sad Runbook, které jsou součástí tohoto řešení.

Každý počítač se systémem Windows, který je spravován řešením, je uveden v podokně **Hybridní pracovní skupiny** jako **hybridní pracovní skupina systému** pro účet automatizace. Řešení používají *hostname FQDN_GUID* konvence pojmenování. Na tyto skupiny nelze cílit pomocí runbooků ve vašem účtu. Pokud se pokusíte, pokus se nezdaří. Tyto skupiny jsou určeny pouze pro podporu tohoto řešení pro správu.

Počítač se systémem Windows můžete přidat do skupiny Hybridní pracovní doba runbooku ve vašem účtu Automation pro podporu runbooků automatizace, pokud používáte stejný účet pro řešení i členství ve skupině Hybridní runbook Worker. Tato funkce byla přidána ve verzi 7.2.12024.0 hybridního pracovníka runbooku.

### <a name="management-packs"></a>Sady Management Pack

Pokud je vaše skupina pro správu nástroje Operations Manager system center [připojena k pracovnímu prostoru Analýzy protokolů](../azure-monitor/platform/om-agents.md), jsou v nástroji Operations Manager nainstalovány následující sady Management Pack. Tyto sady Management Pack jsou také nainstalovány na přímo připojených počítačích se systémem Windows po přidání řešení. Sady Management Pack nemusíte konfigurovat ani spravovat:

* Aktualizace Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Aktualizace sady pro správu nasazení

> [!NOTE]
> Pokud máte skupinu pro správu Nástroje pro správu nástroje Operations Manager 1807 nebo 2019 připojenou k pracovnímu prostoru Analýzy protokolů s agenty nakonfigurovanými ve skupině pro správu pro shromažďování dat protokolu, je třeba přepsat následující pravidlo, abyste je mohli spravovat pomocí správy aktualizací: Přepsat parametr **IsAutoRegistrationEnabled** a nastavit na **hodnotu True** v pravidle **Microsoft.IntelligencePacks.Automation.HybridAgent.Init.**

Další informace o aktualizaci sad Management Pack řešení naleznete v [tématu Připojení nástroje Operations Manager k protokolům Azure Monitor .](../azure-monitor/platform/om-agents.md)

> [!NOTE]
> Pro počítače s agentem Operations Manger, které mají být plně spravovány službou Update Management, musí být agent aktualizován na agenta Log Analytics pro Windows nebo Linux. Informace o aktualizaci agenta naleznete v tématu [Jak upgradovat agenta nástroje Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). V prostředích, která používají nástroj Operations Manager, musíte používat nástroj System Center Operations Manager 2012 R2 UR 14 nebo novější.

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Řešení shromažďuje informace o aktualizacích systému od agentů systému Windows a poté zahájí instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Řešení shromažďuje informace o aktualizacích systému od agentů Linuxu a poté iniciuje instalaci požadovaných aktualizací podporovaných distribucí. |
| Skupina pro správu Operations Manageru |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu.<br/><br/>Přímé připojení agenta Operations Manager u protokolů Azure Monitor není vyžadováno. Data se předávají ze skupiny pro správu do pracovního prostoru Log Analytics. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Prohledávací program se provádí dvakrát denně pro každý spravovaný počítač se systémem Windows. Každých 15 minut rozhraní API systému Windows je volána k dotazování na čas poslední aktualizace k určení, zda se změnil stav. Pokud se stav změnil, je zahájena kontrola dodržování předpisů.

Prohledávací program se provádí každou hodinu pro každý spravovaný počítač s Linuxem.

Zobrazení aktualizovaných dat ze spravovaných počítačů může trvat 30 minut až 6 hodin.

Průměrné využití dat protokoly Azure Monitor pro počítač pomocí správy aktualizací je přibližně 25 megabajtů (MB) za měsíc. Tato hodnota je pouze aproximace a může se změnit v závislosti na vašem prostředí. Doporučujeme sledovat vaše prostředí, abyste měli přehled o přesném využití. Další informace o analýze využití dat naleznete v [tématu Správa využití a nákladů](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Plánování sítě

Následující adresy jsou vyžadovány speciálně pro správu aktualizací. Komunikace s těmito adresami probíhá přes port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

U počítačů se systémem Windows je také nutné povolit provoz na všechny koncové body vyžadované službou Windows Update. Aktualizovaný seznam požadovaných koncových bodů najdete v [problémech souvisejících s http/proxy serverem](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Pokud máte místní [server Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment), musíte také povolit přenos na server zadaný v [klíči služby WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Pro počítače S Red Hat Linux, najdete [ip adresy pro servery pro doručování obsahu RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) pro požadované koncové body. Další distribuce Linuxu najdete v dokumentaci k poskytovateli.

Další informace o portech požadovaných pro hybridní pracovník runbooku naleznete v [tématu Hybridní porty rolí pracovního procesu](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Při definování výjimek doporučujeme používat uvedené adresy. Pro IP adresy si můžete stáhnout [rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor je aktualizován každý týden a odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres.

Podle pokynů v [části Připojení počítačů bez přístupu k internetu](../azure-monitor/platform/gateway.md) nakonfigurujte počítače, které nemají přístup k internetu.

## <a name="update-classifications"></a>Update classifications

V následujících tabulkách jsou uvedeny klasifikace aktualizací ve správě aktualizací s definicí pro každou klasifikaci.

### <a name="windows"></a>Windows

|Classification  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizace pro konkrétní problém, který řeší kritickou chybu nesouvisející se zabezpečením.        |
|Aktualizace zabezpečení     | Aktualizace problému souvisejícího se zabezpečením týkajícího se produktu.        |
|Kumulativní aktualizace     | Kumulativní sada oprav hotfix, které jsou zabaleny společně pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu, které jsou distribuovány mimo vydání produktu.        |
|Aktualizace Service Pack     | Kumulativní sada oprav hotfix, které jsou použity v aplikaci.        |
|Aktualizace definic     | Aktualizace virů nebo jiných definičních souborů.        |
|nástroje     | Nástroj nebo funkce, která pomáhá dokončit jeden nebo více úkolů.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalován.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classification  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro konkrétní problém nebo problém související se zabezpečením produktu.         |
|Další aktualizace     | Všechny ostatní aktualizace, které nejsou kritické povahy nebo které nejsou aktualizace zabezpečení.        |

Pro Linux může správa aktualizací rozlišovat mezi důležitými aktualizacemi a aktualizacemi zabezpečení v cloudu a zároveň zobrazovat data hodnocení kvůli obohacení dat v cloudu. Pro opravy, Správa aktualizací spoléhá na klasifikační data k dispozici na počítači. Na rozdíl od jiných distribucí centos nemá tyto informace k dispozici ve verzi RTM. Pokud máte počítače CentOS nakonfigurované tak, aby vracely data zabezpečení pro následující příkaz, správa aktualizací může být opravena na základě klasifikací.

```bash
sudo yum -q --security check-update
```

V současné době neexistuje žádná podporovaná metoda, která by umožnila dostupnost nativních dat klasifikace v CentOS. V tuto chvíli je zákazníkům, kteří to mohli povolit sami, poskytována pouze podpora s maximálním úsilím. 

Chcete-li klasifikovat aktualizace na Red Hat Enterprise verze 6, musíte nainstalovat plugin mňam-security. Na Red Hat Enterprise Linux 7, plugin je již součástí yum sám, není třeba nic instalovat. Další informace naleznete v následujícím [článku znalostní báze](https://access.redhat.com/solutions/10021)Red Hat .

## <a name="integrate-with-configuration-manager"></a>Integrace s Configuration Managerem

Zákazníci, kteří investovali do nástroje Microsoft Endpoint Configuration Manager pro správu počítačů, serverů a mobilních zařízení, se také spoléhají na sílu a vyspělost nástroje Configuration Manager, který jim pomáhá spravovat aktualizace softwaru. Configuration Manager je součástí jejich cyklu správy aktualizací softwaru (SUMA).

Informace o integraci řešení správy s nástrojem Configuration Manager naleznete v [tématu Integrace nástroje Configuration Manager se správou aktualizací](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-updates-on-windows"></a>Aktualizace jiných výrobců ve Windows

Správa aktualizací spoléhá na místně nakonfigurované úložiště aktualizací pro aktualizaci podporovaných systémů Windows. Toto je služba WSUS nebo služba Windows Update. Nástroje, jako [je vydavatel aktualizací systémového centra](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Vydavatel aktualizací), umožňují importovat a publikovat vlastní aktualizace pomocí služby WSUS. Tento scénář umožňuje službu Správa aktualizací aktualizovat počítače, které používají nástroj Configuration Manager jako úložiště aktualizací pomocí softwaru jiného výrobce. Informace o konfiguraci aplikace Updates Publisher naleznete v [tématu Instalace aplikace Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Povolit správu aktualizací

Chcete-li začít aktualizovat systémy, musíte povolit řešení správy aktualizací. Níže jsou uvedeny doporučené a podporované metody pro na palubě řešení:

- [Z virtuálního počítače](automation-onboard-solutions-from-vm.md)

- [Z procházení více strojů](automation-onboard-solutions-from-browse.md)

- [Z vašeho účtu Automation](automation-onboard-solutions-from-automation-account.md)

- [S runbookem Azure Automation](automation-onboard-solutions.md)

- [Se šablonou Azure Resource Manager](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Další kroky

Projděte si [nejčastější dotazy](automation-faq.md) k automatizaci Azure a přečtěte si běžné otázky týkající se tohoto řešení.
