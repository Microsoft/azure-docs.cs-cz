---
title: Řešení pro správu aktualizací v Azure
description: Tento článek vám objasní, jak toto řešení používat ke správě aktualizací pro počítače s Windows a Linuxem.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/23/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3bd3c4f6501000f2490bc26cf7c6ff0345d3e7cc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="update-management-solution-in-azure"></a>Řešení pro správu aktualizací v Azure

Řešení správy aktualizací ve službě Azure automation umožňuje spravovat aktualizace operačního systému pro Windows a Linux počítače nasazené v Azure, místní prostředí nebo jiných poskytovatelů cloudu. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Správu aktualizací pro virtuální počítače můžete povolit přímo ze svého účtu [Azure Automation](automation-offering-get-started.md).
Informace o povolení správy aktualizací pro virtuální počítače z účtu Automation najdete v tématu [Správa aktualizací pro několik virtuálních počítačů](manage-update-multi.md). Můžete také povolit správu aktualizací pro jeden virtuální počítač na stránce virtuální počítač na portálu Azure. Tento scénář je k dispozici pro obě [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) a [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuálních počítačů.

## <a name="solution-overview"></a>Přehled řešení

Počítače spravované pomocí správy aktualizací následující konfigurace pro provádění nasazení hodnocení a aktualizace:

* Microsoft Monitoring agenta pro Windows nebo Linux
* Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
* Funkci Hybrid Runbook Worker služby Automation
* Microsoft Update nebo Windows Server Update Services pro počítače s Windows

Následující diagram znázorňuje koncepční zobrazení chování a tok dat způsobu řešení vyhodnocuje a bezpečnostní aktualizace se vztahuje na všechny připojené serveru Windows a Linux počítačů v pracovním prostoru.

![Tok procesu správy aktualizace](media/automation-update-management/update-mgmt-updateworkflow.png)

Poté, co počítač provede kontrolu shody aktualizací, agent předává informace hromadně k analýze protokolů. Na počítačích s Windows se kontrola kompatibility ve výchozím nastavení provádí každých 12 hodin. Kromě plánu vyhledávání se zahájí kontroly shody aktualizací do 15 minut, pokud je restartován Microsoft Monitoring Agent (MMA), před instalací aktualizace a po instalaci aktualizace. U počítače s Linuxem se kontrola aktualizací ve výchozím nastavení provádí každé 3 hodiny a také během 15 minut v případě restartování agenta MMA.

Řešení podá zprávu o aktuálnosti počítače podle toho, s jakým zdrojem je nakonfigurována synchronizace. Pokud je počítač s Windows nakonfigurovaný tak, aby ukládal data do služby WSUS, v závislosti na času poslední synchronizace služby WSUS s Microsoft Update se výsledky můžou lišit od toho, co ukazuje Microsoft Update. Toto je stejný pro počítače Linux, které jsou nakonfigurovány k místním úložišti a veřejné úložišti sestavy.

Na počítače, které vyžadují aktualizace softwaru, můžete tyto aktualizace nasadit a nainstalovat tak, že vytvoříte plánované nasazení. Do oboru nasazení pro počítače s Windows nejsou zahrnuty aktualizace klasifikované jako *Volitelné*, pouze požadované aktualizace. Plánované nasazení definuje, jaký cílové počítače přijímat příslušné aktualizace, buď explicitně zadat počítače, nebo výběrem [skupinu počítačů](../log-analytics/log-analytics-computer-groups.md) který je založen na protokolu hledání konkrétní sady počítačů. Zadáte také plán pro schválení a vyhrazení časového období, kdy je možné aktualizace nainstalovat. Aktualizace se instalují podle runbooků ve službě Azure Automation. Tyto runbooky není možné zobrazit a nevyžadují žádnou konfiguraci. Při vytvoření nasazení aktualizací se vytvoří plán, který v zadanou dobu spustí hlavní runbook aktualizace pro zahrnuté počítače. Tento hlavní runbook spouští podřízený runbook na každém agentovi, který provádí instalaci požadovaných aktualizací.

V den a čas, který zadáte v nasazení aktualizací, spustí cílové počítače paralelně nasazení. Nejprve se provede kontrola pro ověření, že se aktualizace stále vyžadují, a pak se aktualizace nainstalují. U klientských počítačů WSUS, pokud nejsou schválené aktualizace ve službě WSUS, aktualizace nasazení se nezdaří.

## <a name="clients"></a>Klienti

### <a name="supported-client-types"></a>Podporované klientské typy

Následující tabulka uvádí seznam podporovaných operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Podporuje pouze aktualizovat vyhodnocování         |
|Windows Server 2008 R2 SP1 a vyšší     |Prostředí Windows PowerShell 4.0 nebo vyšší se vyžaduje ([stáhnout WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)).</br> Windows PowerShell 5.1 ([stáhnout 5.1 WMF](https://www.microsoft.com/download/details.aspx?id=54616)) se doporučuje pro větší spolehlivost.         |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

### <a name="unsupported-client-types"></a>Nepodporovaný klient typy

Následující tabulka uvádí operační systémy, které nejsou podporovány:

|Operační systém  |Poznámky  |
|---------|---------|
|Klient Windows     | Klientské operační systémy (Windows 7, Windows 10 atd.) nejsou podporovány.        |
|Windows Server 2016 Nano Server     | Nepodporuje se       |

### <a name="client-requirements"></a>Požadavky na klienta

#### <a name="windows"></a>Windows

Agenty se systémem Windows musí být nakonfigurované pro komunikaci se serverem Windows Server Update Services (WSUS) nebo mají přístup ke službě Microsoft Update. Správa aktualizací může být použita pomocí nástroje System Center Configuration Manager, další informace o scénářích integrace najdete [integraci se System Center Configuration Manager pomocí správy aktualizací](oms-solution-updatemgmt-sccmintegration.md#configuration). [Agenta Windows](../log-analytics/log-analytics-agent-windows.md) se vyžaduje. Tento agent se nainstaluje automaticky, pokud se registrace virtuální počítač Azure.

#### <a name="linux"></a>Linux

Pro Linux počítač musí mít přístup k aktualizaci úložiště, což může být privátní nebo veřejné. Toto řešení nepodporuje OMS agenta pro Linux nakonfigurovaný tak, aby sestavy několik pracovních prostorů analýzy protokolů.

Další informace o tom, jak nainstalovat agenta OMS pro Linux a stáhnout nejnovější verzi najdete v tématu [Operations Management Suite agenta pro Linux](https://github.com/microsoft/oms-agent-for-linux). Informace o tom, jak nainstalovat agenta OMS pro Windows, najdete v tématu popisujícím [agenta Operations Management Suite pro Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit a spravovat nasazení aktualizací, musíte konkrétní oprávnění. Další informace o těchto oprávnění naleznete [přístupu na základě na Role - Správa aktualizací](automation-role-based-access-control.md#update-management)

## <a name="solution-components"></a>Součásti řešení

Toto řešení se skládá z následujících prostředků, které se přidají do vašeho účtu Automation, a přímo připojených agentů nebo skupiny pro správu připojené k Operations Manageru.

### <a name="hybrid-worker-groups"></a>Skupiny Hybrid Worker

Povolíte-li toto řešení, všechny počítač se systémem Windows přímo připojené k pracovní prostor analýzy protokolů je automaticky nakonfigurovaná jako hybridní pracovní proces Runbooku na podporu sady runbook, zahrnuté v tomto řešení. Pro každý počítač systému Windows spravovat řešení, je uveden v části stránku hybridních pracovních skupin jako skupinu hybridních pracovních procesů systému pro účet služby Automation následující zásady vytváření názvů *Hostname FQDN_GUID*. Cílem nemůže tyto skupiny se sadami runbook ve vašem účtu, jinak se nezdaří. Tyto skupiny jsou určeny pouze pro podporu tohoto řešení pro správu.

Počítače s Windows ale můžete přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation pro podporu runbooků Automation, pokud používáte stejný účet pro toto řešení i pro členství ve skupině Hybrid Runbook Worker. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

### <a name="management-packs"></a>Sady Management Pack

Pokud skupině pro správu System Center Operations Manager je připojený k pracovnímu prostoru analýzy protokolů, jsou nainstalované následující sady management Pack v nástroji Operations Manager. Tyto sady Management Pack se po přidání tohoto řešení nainstalují také na přímo připojené počítače s Windows. U těchto sad Management Pack není nutné nic konfigurovat ani spravovat.

* Aktualizace Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Aktualizace sady pro správu nasazení

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-non-azure-machines-are-onboarded"></a>Potvrďte, že jsou počítače mimo Azure zařazený nemá

Potvrďte přímo připojené počítače komunikují pomocí analýzy protokolů po několika minutách můžete spustit následující vyhledávání protokolu:

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

Na počítači s Windows můžete zkontrolovat následující k ověření připojení agenta s analýzy protokolů:

1. Otevřete Microsoft Monitoring Agent v Ovládacích panelech a na **Azure Log Analytics** agenta na kartě zobrazí zpráva s oznámením: **Microsoft Monitoring Agent se úspěšně připojila k analýze protokolů** .
2. Otevřete protokol událostí systému Windows, přejděte do **Application and Services Logs\Operations Manager** a vyhledejte ID události 3000 a 5002 ze zdrojového konektoru Service Connector. Tyto události znamenat počítač zaregistrován s pracovní prostor analýzy protokolů a přijímá konfigurace.

Pokud agenta není schopna komunikovat s analýzy protokolů a je nakonfigurován pro komunikaci přes internet prostřednictvím brány firewall nebo proxy server, potvrzení, brány firewall nebo proxy server byl správně nakonfigurován kontrolou [konfiguraci sítě pro Agent webu Windows](../log-analytics/log-analytics-agent-windows.md) nebo [konfiguraci sítě pro agenta systému Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Pokud vaše systémy Linux jsou nakonfigurovány ke komunikaci s server proxy nebo brány OMS a jsou registrace toto řešení, aktualizovat *proxy.conf* oprávnění ke skupině omiuser udělit oprávnění u souboru čtení tak, že provedete následující příkazy: `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Stav nově přidaných agentů systému Linux bude po provedení vyhodnocení **Aktualizovaný**. Tento proces může trvat až 6 hodin.

Pokud chcete potvrdit skupinu správy nástroje Operations Manager komunikuje s analýzy protokolů, najdete v části [ověření integrace nástroje Operations Manager s analýzy protokolů](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Řešení shromažďuje informace o aktualizacích systému pro agenty Windows a inicializuje instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Řešení shromažďuje informace o aktualizacích systému od agentů systému Linux a zahajuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu.</br>Přímé připojení z agenta Operations Manageru ke službě Log Analytics není potřeba. Do pracovního prostoru analýzy protokolů se předají data ze skupiny pro správu. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Pro každý spravovaný počítač s Windows se kontrola provádí dvakrát denně. Každých 15 minut se volá rozhraní Windows API pro zadání dotazu na čas poslední aktualizace, podle kterého zjistí, jestli se změnil stav, a pokud ano, zahájí se kontrola kompatibility. Pro každý spravovaný počítač s Linuxem se kontrola provádí každé tři hodiny.

Může trvat 30 minut až 6 hodin, než se na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

## <a name="viewing-update-assessments"></a>Zobrazení posouzení aktualizací

Klikněte na **správy aktualizací** na účtu automation k zobrazení stavu počítačů.

Toto zobrazení obsahuje informace o vašich počítačů, chybějící aktualizace, nasazení aktualizací a plánovaná aktualizace nasazení.

Můžete spustit protokolu vyhledávání, který vrátí informace na počítači, aktualizace nebo nasazení tak, že vyberete položku v seznamu. Tím se otevře **hledání protokolů** stránku pomocí dotazu pro vybranou položku.

## <a name="installing-updates"></a>Instalace aktualizací

Po posouzení aktualizací pro všechny počítače s Linuxem a Windows ve vašem prostředí můžete nechat nainstalovat požadované aktualizace vytvořením *nasazení aktualizací*. Nasazení aktualizací je plánovaná instalace požadovaných aktualizací pro jeden nebo více počítačů. Kromě počítače nebo skupiny počítačů, které mají být součástí rozsahu nasazení, zadáte datum a čas nasazení. Další informace o skupinách počítačů najdete v tématu [Skupiny počítačů v Log Analytics](../log-analytics/log-analytics-computer-groups.md). Pokud zahrnete skupiny počítačů ve vašem nasazení aktualizace, členství ve skupině je pouze jednou vyhodnocena v době vytvoření plánu. Další změny ve skupině se neprojeví. Můžete to obejít tak, že naplánované nasazení aktualizací odstraníte a znovu vytvoříte.

> [!NOTE]
> Virtuální počítače s Windows nasazené z Azure Marketplace jsou standardně nastaveny na přijímání automatických aktualizací ze služby Windows Update. Toto chování se po přidání tohoto řešení nebo virtuálních počítačů s Windows do vašeho pracovního prostoru nezmění. Pokud nespravujete aktivně aktualizace s tímto řešením, použije se výchozí chování (Automatické aktualizace) se vztahuje.

Pokud se chcete vyhnout tomu, aby se aktualizace používaly mimo časové období údržby v Ubuntu, změňte konfiguraci Unattended-Upgrade tak, aby automatické aktualizace byly zakázány. Informace o postupu této konfigurace najdete v tématu [Téma Automatické aktualizace v příručce k Ubuntu Serveru](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Pro virtuální počítače vytvořené z imagí Red Hat Enterprise Linux (RHEL) na vyžádání dostupných v Azure Marketplace jsou registrované pro přístup k infrastruktuře [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) nasazené v Azure. Všechny ostatní distribuce Azure musí být aktualizované z online úložiště souborů distribuce podle podporované metody.

## <a name="viewing-missing-updates"></a>Chybějící aktualizace zobrazení

Klikněte na tlačítko **chybějící aktualizace** Chcete-li zobrazit seznam aktualizací, které chybí z vašeho počítače. Každá aktualizace je uvedena v seznamu a zobrazí informace s ohledem na počet počítačů, které vyžadují aktualizaci, operační systém a odkaz na další informace. Jednotlivé aktualizace můžete vybrat a **hledání protokolů** stránka zobrazí a další podrobnosti o aktualizacích.

## <a name="viewing-update-deployments"></a>Zobrazení nasazení aktualizace

Klikněte **nasazení aktualizací** zobrazíte seznam existující nasazení aktualizací. Kliknutím na některé z nasazení aktualizace v tabulce otevře **aktualizace spustit nasazení** stránky pro toto nasazení aktualizace.

![Přehled výsledků nasazení aktualizace](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>Vytvoření nasazení aktualizace

Kliknutím na vytvořit nové nasazení aktualizace **nasazení aktualizace plánu** tlačítka v horní části obrazovky, otevřete **nové nasazení aktualizace** stránky. Je nutné zadat hodnoty pro vlastnosti v následující tabulce:

| Vlastnost | Popis |
| --- | --- |
| Název |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Linux nebo Windows|
| Počítače, které chcete aktualizovat |Zvolte hledání, uloženo nebo vyberte počítač z rozevíracího seznamu a vyberte jednotlivé počítače |
|Klasifikace aktualizací|Vyberte všechny klasifikace aktualizací, které budete potřebovat|
|Aktualizace, které chcete vyloučit|Zadejte všechny články znalostní báze vyloučit bez předpony 'KB.|
|Nastavení plánu|Vyberte čas spuštění a vyberte buď jednou nebo opakovaně opakování|
| Časové období údržby |Počet minut nastavit pro aktualizace. Hodnota nemůže být menší než 30 minut a více než 6 hodin |

## <a name="update-classifications"></a>Klasifikace aktualizací

Následující tabulka obsahuje seznam klasifikace aktualizací v správy aktualizací společně s definici pro každé klasifikace.

### <a name="windows"></a>Windows

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizaci pro určitý problém, která řeší kritickou nesouvisející zabezpečení chybu.        |
|Aktualizace zabezpečení     | Aktualizaci pro problém produktu, související se zabezpečením.        |
|Kumulativní aktualizace     | Úhrnnou sadu oprav hotfix, které jsou spojených oprav pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu, distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Úhrnnou sadu oprav hotfix, které se použijí pro aplikaci.        |
|Aktualizace definic     | Aktualizace jiných souborů definic virů či.        |
|Nástroje     | Nástroj nebo funkci sloužící k dokončení jedné nebo více úloh.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalována.        |

### <a name="linux"></a>Linux

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro určitý problém nebo problém s produktu, související se zabezpečením.         |
|Další aktualizace     | Všechny další aktualizace, které nejsou kritické ve své podstatě aktualizace nebo aktualizace zabezpečení.        |

## <a name="search-logs"></a>Protokoly vyhledávání

Kromě podrobností, které jsou k dispozici na portálu můžete provést hledání protokoly. S **sledování změn** otevřený, klikněte na stránce **analýzy protokolů**, otevře se **hledání protokolů** stránky

### <a name="sample-queries"></a>Ukázkové dotazy

Následující tabulka obsahuje ukázkový protokol hledání aktualizace záznamů shromažďují toto řešení:

| Dotaz | Popis |
| --- | --- |
|Aktualizace</br>&#124;kde UpdateState == "Potřebné" a volitelné hodnotu false</br>&#124;Počítače, název, KBID, klasifikace, PublishedDate projektu |Všechny počítače s chybějícími aktualizacemi</br>Přidejte jednu z těchto omezit operačního systému:</br>OSType = "Windows"</br>OSType == "Linux" |
| Aktualizace</br>&#124;kde UpdateState == "Potřebné" a volitelné hodnotu false</br>&#124;kde počítač == "ContosoVM1.contoso.com"</br>&#124;Počítače, název, KBID, produktu, PublishedDate projektu |Chybějící aktualizace v určitém počítači (nahraďte hodnotu názvem svého počítače)|
| Událost</br>&#124;kde EventLevelName == "Chyba" a každý počítač v ((aktualizace &#124; where (klasifikace == "Aktualizace zabezpečení" nebo klasifikaci == "Kritické aktualizace")</br>&#124;kde UpdateState == "Potřebné" a volitelné hodnotu false </br>&#124;DISTINCT Computer)) |Chybové události pro počítače s chybějícími požadovanými důležitými aktualizacemi nebo aktualizacemi zabezpečení |
| Aktualizace</br>&#124;kde UpdateState == "Potřebné" a volitelné hodnotu false</br>&#124;odlišné název |Konkrétní chybějící aktualizace ve všech počítačích |
| UpdateRunProgress</br>&#124;kde InstallationStatus == "se nezdařilo" </br>&#124;shrnout AggregatedValue = count() pomocí počítače, název, UpdateRunName |Počítače s aktualizacemi, které se nezdařilo spustit aktualizaci</br>Přidejte jednu z těchto omezit operačního systému:</br>OSType = "Windows"</br>OSType == "Linux" |
| Aktualizace</br>&#124;kde OSType == "Linux"</br>&#124;kde UpdateState! = "Není skutečně potřeba" a (klasifikace == "Kritické aktualizace" nebo klasifikaci == "Aktualizace zabezpečení")</br>&#124;shrnout AggregatedValue = count() počítačem. |Seznam všechny počítače se systémem Linux, které mají k dispozici aktualizace balíčku, který řeší chybu zabezpečení, důležité aktualizace nebo zabezpečení |
| UpdateRunProgress</br>&#124;kde UpdateRunName == "DeploymentName"</br>&#124;shrnout AggregatedValue = count() počítačem.|Počítače aktualizované při této hromadné postupné aktualizaci (nahraďte hodnotu názvem vašeho nasazení aktualizací) |

## <a name="integrate-with-system-center-configuration-manager"></a>Integrace se System Center Configuration Managerem

Zákazníci, kteří investovali do System Center Configuration Manageru pro správu počítačů, serverů a mobilních zařízení využívají jeho odolnost a další přednosti také při správě aktualizací softwaru jako součást cyklu správy softwarových aktualizací (SUM).

Další informace o integraci do řešení pro správu pomocí nástroje System Center Configuration Manager naleznete v tématu [integraci se System Center Configuration Manager pomocí správy aktualizací](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patching-linux-machines"></a>Opravy počítače se systémem Linux

Následující části popisují možné problémy s Linux opravy.

### <a name="package-exclusion"></a>Vyloučení balíčku

Na některé varianty Linux, jako je například Red Hat Enterprise Linux může dojít, upgrady operačního systému úrovni prostřednictvím balíčků. To může vést k správě aktualizací spustí kde změní číslo verze operačního systému. Vzhledem k tomu, že správa aktualizace používá stejné metody aktualizovat balíčky způsobem správce místně na počítače se systémem Linux, toto chování je záměrné.

Abyste se vyhnuli, aktualizace verze operačního systému pomocí správy aktualizací běží, používáte **vyloučení** funkce.

V systému Red Hat Enterprise Linux by byl název balíčku pro vyloučení: redhat. verze server.x86_64

![Balíčky pro vyloučení pro Linux](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>Není použita opravy zabezpečení

Při nasazování aktualizace pro počítač s Linuxem, můžete vybrat klasifikace aktualizací. Tento filtrování aktualizací, které se použijí na ty, které splňují zadaná kritéria. Tento filtr se použijí místně na počítači, při nasazení aktualizace. Protože aktualizovat správu provádí aktualizace obohacení v cloudu, může příznakem některé aktualizace ve správě aktualizovat tak, že má dopad na zabezpečení, i když v místním počítači nemá tyto informace. Výsledkem je Pokud použijete důležité aktualizace pro počítač s Linuxem, může mít aktualizace, které nejsou označeny tak, že na tomto počítači má dopad na zabezpečení a získat nebyly použity. Aktualizace správy však může hlásit stále tento počítač se nekompatibilní, protože obsahuje další informace o příslušné aktualizace.

Nasazení aktualizací pomocí klasifikace aktualizace nemusí fungovat na openSUSE Linux z důvodu jiného oprav modelu použít.

## <a name="troubleshooting"></a>Řešení potíží

Tato část obsahuje informace, které vám pomohou s řešením potíží s řešením pro správu aktualizací.

Pokud při pokusech o připojení řešení nebo virtuálního počítače dochází k potížím, zkontrolujte, jestli jsou v protokolu událostí **Protokoly aplikací a služeb\Operations Manager** události s ID události 4502 a zprávou události obsahující **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. V následující tabulce najdete konkrétní chybové zprávy a možné řešení pro každou z nich.

| Zpráva | Důvod | Řešení |
|----------|----------|----------|
| Nepodařilo se zaregistrovat počítač pro správu oprav,</br>registrace se nezdařila s výjimkou</br>System.InvalidOperationException: {"Zpráva":"Počítač už je</br>registrovaný k jinému účtu. "} | Počítač už je připojený k jinému pracovnímu prostoru pro řešení Update Management | Proveďte vyčištění starých artefaktů [odstraněním hybridních runbooků](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|
| Nelze zaregistrovat počítač pro správu opravy, registrace se nezdařila s výjimkou</br>System.Net.Http.HttpRequestException: Při odesílání požadavku došlo k chybě. ---></br>System.Net.WebException: Nadřízené připojení</br>bylo uzavřeno: Došlo k neočekávané</br>chybě při příjmu. ---> System.ComponentModel.Win32Exception:</br>Klient a server nemůžou komunikovat,</br>protože nepoužívají společný algoritmus. | Proxy server, brána nebo brána firewall blokuje komunikaci | [Zkontrolujte požadavky sítě](automation-offering-get-started.md#network-planning)|
| Nepodařilo se zaregistrovat počítač pro správu oprav,</br>registrace se nezdařila s výjimkou</br>Newtonsoft.Json.JsonReaderException: Chyba při analýze hodnoty kladného nekončena. | Proxy server, brána nebo brána firewall blokuje komunikaci | [Zkontrolujte požadavky sítě](automation-offering-get-started.md#network-planning)|
| Certifikát předložený službu \<wsid\>. oms.opinsights.azure.com</br>nebyl vydaný certifikační autoritou</br>používanou pro služby Microsoft. Kontakt</br>správce sítě a zjistěte, jestli nepoužívají proxy server bránící</br>komunikaci prostřednictvím protokolu TLS/SSL. |Proxy server, brána nebo brána firewall blokuje komunikaci | [Zkontrolujte požadavky sítě](automation-offering-get-started.md#network-planning)|
| Nepodařilo se zaregistrovat počítač pro správu oprav,</br>registrace se nezdařila s výjimkou</br>AgentService.HybridRegistration.</br>PowerShell.Certificates.CertificateCreationException:</br>Vytvoření certifikátu podepsaného svým držitelem se nezdařilo. ---></br>System.UnauthorizedAccessException: Přístup byl odepřen. | Chyba při generování certifikátu podepsaného svým držitelem | Ověřte, že má systémový účet</br>oprávnění ke čtení ze složky:</br>**C:\ProgramData\Microsoft\**</br>** Crypto\RSA**|

## <a name="next-steps"></a>Další postup

Pokračujte v kurzu se dozvíte, jak spravovat aktualizace pro virtuální počítače Windows.

> [!div class="nextstepaction"]
> [Spravovat aktualizace a opravy pro virtuální počítače Windows Azure](automation-tutorial-update-management.md)

* K zobrazení podrobných údajů o aktualizaci použijte Hledání v protokolu služby [Log Analytics](../log-analytics/log-analytics-log-searches.md).
* [Vytvářejte výstrahy](../log-analytics/log-analytics-alerts.md) při zjištění, že v počítačích chybí důležité aktualizace nebo že má počítač zakázané automatické aktualizace.
