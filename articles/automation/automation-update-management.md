---
title: Řešení pro správu aktualizací v Azure
description: Tento článek je určený k vám pomohou pochopit, jak používat řešení pro správu aktualizací Azure ke správě aktualizací pro systém Windows a Linux počítače.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8013a221f818b2aca8163e5f5195cae17c8a6a95
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232297"
---
# <a name="update-management-solution-in-azure"></a>Řešení pro správu aktualizací v Azure

Řešení správy aktualizací ve službě Azure Automation můžete použít ke správě aktualizací operačního systému pro Windows a Linux počítače, na kterých jsou nasazené v Azure, v prostředích místní nebo v jiných poskytovatelů cloudu. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Správa aktualizací můžete povolit pro virtuální počítače přímo z vašeho účtu Azure Automation. Informace o povolení správy aktualizací pro virtuální počítače z vašeho účtu Automation najdete v tématu [spravovat aktualizace pro víc virtuálních počítačů](manage-update-multi.md). Můžete také povolit správu aktualizací pro jeden virtuální počítač z podokna virtuálního počítače na portálu Azure. Tento scénář je k dispozici pro [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) a [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuálních počítačů.

## <a name="solution-overview"></a>Přehled řešení

Počítače, které jsou spravovány službou Management aktualizovat pomocí následující konfigurace provést vyhodnocení a nasazení aktualizací:

* Microsoft Monitoring Agent (MMA) pro Windows nebo Linux
* Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
* Funkci Hybrid Runbook Worker služby Automation
* Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače se systémem Windows

Následující diagram znázorňuje koncepční zobrazení chování a tok dat způsobu řešení vyhodnocuje a bezpečnostní aktualizace se vztahuje na všechny připojené serveru Windows a Linux počítačů v pracovním prostoru:

![Tok procesu správy aktualizace](media/automation-update-management/update-mgmt-updateworkflow.png)

Poté, co počítač provede kontrolu shody aktualizací, agent předává informace hromadně k analýze protokolů Azure. Na počítači s Windows nebude provedena kontrola dodržování předpisů ve výchozím nastavení každých 12 hodin. 

Kromě plánu vyhledávání se zahájí kontroly shody aktualizací do 15 minut, pokud je restartován MMA, před instalací aktualizací a po instalaci aktualizace. 

Pro počítač se systémem Linux prohledávání shody probíhá každých 3 hodiny ve výchozím nastavení. Pokud je restartován agenta MMA, kontrolu kompatibility je zahájeno 15 minut.

Řešení se sestavy, jak aktuální počítač je založena na co zdroje můžete nakonfigurovat tak, aby synchronizaci se službou. Pokud je počítač Windows nakonfigurovaný tak, aby odesílaly WSUS, v závislosti na tom, kdy WSUS poslední synchronizaci se službou Microsoft Update, výsledky mohou lišit od co Microsoft Updates zobrazuje. Je to stejné nastavení pro počítače Linux, které jsou nakonfigurovány na sestavu do místního úložiště místo do veřejného úložiště.

> [!NOTE]
> Správa aktualizací správně informuje o službu, vyžaduje určité adres URL a portů, aby byl povolen. Další informace o těchto požadavcích najdete v tématu [sítě plánování hybridní pracovní procesy](automation-hybrid-runbook-worker.md#network-planning).

Na počítače, které vyžadují aktualizace softwaru, můžete tyto aktualizace nasadit a nainstalovat tak, že vytvoříte plánované nasazení. Aktualizace klasifikované jako *volitelné* nejsou zahrnuty v oboru nasazení pro počítače se systémem Windows. Pouze požadované aktualizace jsou součástí oboru nasazení. 

Plánované nasazení definuje, jaký cílové počítače přijímat použitelné aktualizace explicitním zadáním počítače nebo výběrem [skupinu počítačů](../log-analytics/log-analytics-computer-groups.md) založený na protokolu hledání konkrétní sady počítačů. Můžete také zadat plán pro schválení a určit určitou dobu během které můžete nainstalovat aktualizace. 

Aktualizace se instalují podle runbooků ve službě Azure Automation. Nelze zobrazit tyto sady runbook a sady runbook nevyžadují žádnou konfiguraci. Při nasazení aktualizace, vytvoří nasazení aktualizace plánu, který spustí runbook s hlavní aktualizace v určeném čase pro zahrnuté počítače. Hlavní runbook spouští podřízený runbook na každého agenta k provedení instalace požadovaných aktualizací.

Na datum a čas zadaný v nasazení aktualizace cílových počítačích spustit nasazení paralelně. Před instalací se provádí kontrolu ověření, že aktualizace jsou stále vyžadují. U klientských počítačů WSUS, pokud nejsou schválené aktualizace ve službě WSUS, aktualizace nasazení se nezdaří.

## <a name="clients"></a>Klienti

### <a name="supported-client-types"></a>Podporované klientské typy

Následující tabulka uvádí seznam podporovaných operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Podporuje pouze aktualizovat vyhodnocování.         |
|Windows Server 2008 R2 SP1 a novější     |Je potřeba .NET framework 4.5 nebo novější. ([Stáhnout rozhraní .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Vyžaduje se prostředí Windows PowerShell 4.0 nebo novější. ([Stáhnout WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 se doporučuje pro větší spolehlivost.  ([Stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Na základě klasifikace opravy vyžaduje 'yum' vrátit data zabezpečení, která CentOS nemá mimo pole.         |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14.04 LTS a 16.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

### <a name="unsupported-client-types"></a>Nepodporovaný klient typy

Následující tabulka uvádí operační systémy, které nejsou podporovány:

|Operační systém  |Poznámky  |
|---------|---------|
|Klient Windows     | Klientské operační systémy (například Windows 7 a Windows 10) nejsou podporovány.        |
|Windows Server 2016 Nano Server     | Nepodporuje se.       |

### <a name="client-requirements"></a>Požadavky na klienta

#### <a name="windows"></a>Windows

Agenty se systémem Windows musí být nakonfigurován pro komunikaci serveru WSUS nebo musí mít přístup ke službě Microsoft Update. Správa aktualizací můžete pomocí nástroje System Center Configuration Manager. Další informace o scénářích integrace najdete v tématu [integraci se System Center Configuration Manager pomocí správy aktualizací](oms-solution-updatemgmt-sccmintegration.md#configuration). [Agenta Windows](../log-analytics/log-analytics-agent-windows.md) se vyžaduje. Agent se nainstaluje automaticky, pokud jste registrace virtuální počítač Azure.

#### <a name="linux"></a>Linux

Pro Linux počítač musí mít přístup k úložiště aktualizací. Úložiště aktualizací může být privátní nebo veřejné. Toto řešení nepodporuje agenta Operations Management Suite (OMS) pro Linux, který je nakonfigurovaný k více pracovní prostory analýzy protokolů sestavy.

Informace o tom, které chcete nainstalovat agenta OMS pro Linux a ke stažení nejnovější verze najdete v tématu [Operations Management Suite agenta pro Linux](https://github.com/microsoft/oms-agent-for-linux). Informace o tom, jak nainstalovat agenta OMS systému Windows najdete v tématu [Operations Management Suite agenta pro Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit a spravovat nasazení aktualizací, musíte konkrétní oprávnění. Další informace o těchto oprávnění naleznete v tématu [přístupu podle rolí - správy aktualizací](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Součásti řešení

Řešení se skládá z těchto prostředků. Prostředky jsou přidány do vašeho účtu Automation. Jsou to buď přímo připojené agenty či ve skupině pro správu připojení nástroje Operations Manager.

### <a name="hybrid-worker-groups"></a>Skupiny Hybrid Worker

Povolíte-li toto řešení, jako hybridní pracovní proces Runbooku na podporu sady runbook, které jsou zahrnuté v tomto řešení se automaticky nakonfiguruje jakýkoli počítač systému Windows, který je přímo připojené k pracovní prostor analýzy protokolů.

Každý počítač systému Windows, který je spravovaný nástrojem řešení, je uvedena ve **skupinám Hybrid worker** jako **skupinu hybridních pracovních procesů systému** pro účet služby Automation. Řešení použít zásady vytváření názvů *Hostname FQDN_GUID*. Cílem nemůže tyto skupiny se sadami runbook ve vašem účtu. Se nezdaří, pokud se pokusíte. Tyto skupiny jsou určená pro podporu jenom řešení správy.

Počítače se systémem Windows můžete přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation na podporu automatizace sady runbook, pokud používáte stejný účet pro řešení a členství ve skupině hybridní pracovní proces Runbooku. Tato funkce se přidal ve verzi 7.2.12024.0 hybridní pracovní proces Runbooku.

### <a name="management-packs"></a>Sady Management Pack

Pokud skupině pro správu System Center Operations Manager je připojený k pracovnímu prostoru analýzy protokolů, jsou nainstalované následující sady management Pack v nástroji Operations Manager. Tyto sady management Pack jsou také nainstalované na počítačích s Windows přímo připojené po přidání řešení. Nemusíte konfigurovat nebo spravovat tyto sady management Pack.

* Aktualizace Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Aktualizace sady pro správu nasazení

Další informace o tom, jak jsou aktualizovány sady management Pack řešení najdete v tématu [připojení nástroje Operations Manager k analýze protokolů](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Ověřte, zda počítače mimo Azure zařazený nemá

Potvrďte, že jsou přímo připojené počítače komunikuje s analýzy protokolů po několika minutách můžete spustit jeden následující vyhledávání protokolu.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Na počítači s Windows můžete zkontrolovat následující informace k ověření připojení agenta k analýze protokolů:

1. V Ovládacích panelech otevřete **agenta Microsoft Monitoring Agent**. Na **Azure Log Analytics** agenta na kartě zobrazí následující zprávu: **Microsoft Monitoring Agent se úspěšně připojila k analýze protokolů**.
2. Otevřete protokol událostí systému Windows. Přejděte na **aplikace a Správce služby Logs\Operations** a vyhledejte události ID 3000 a 5002 ID události ze zdroje **konektoru služby**. Tyto události znamenat, že počítač zaregistrován s pracovní prostor analýzy protokolů a přijímá konfigurace.

Pokud agenta nemůže komunikovat s analýzy protokolů a agenta je nakonfigurován pro komunikaci přes internet prostřednictvím brány firewall nebo proxy server, zkontrolujte, jestli je správně nakonfigurované brány firewall nebo proxy server. Zjistěte, jak ověřit, jestli je server brány firewall nebo proxy server správně nakonfigurovaný, najdete v tématu [konfiguraci sítě pro agenta Windows](../log-analytics/log-analytics-agent-windows.md) nebo [konfiguraci sítě pro agenta systému Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Pokud vaše systémy Linux jsou nakonfigurované pro komunikaci pomocí serveru proxy nebo brány OMS a jste registrace toto řešení, aktualizace *proxy.conf* omiuser skupině udělit oprávnění číst oprávnění u souboru pomocí následující příkazy:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nově přidaný agenty Linux zobrazit stav **aktualizované** po posouzení nebylo provedeno. Tento proces může trvat až 6 hodin.

Potvrďte, že skupiny pro správu nástroje Operations Manager komunikuje s analýzy protokolů, najdete v části [integraci ověření nástroje Operations Manager s analýzy protokolů](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které podporuje toto řešení:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů v systému Windows a poté zahájí instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Řešení shromažďuje informace o aktualizacích systému od Linux agentů a poté zahájí instalaci požadovaných aktualizací na podporované distribuce. |
| Skupina pro správu Operations Manageru |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu.<br/>Přímé připojení k analýze protokolů z agenta nástroje Operations Manager není povinné. Do pracovního prostoru analýzy protokolů se předají data ze skupiny pro správu. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Kontroly se provádí dvakrát denně pro každého spravovaného počítače systému Windows. Každých 15 minut, se nazývá rozhraní API systému Windows k dotazu pro čas poslední aktualizace k určení, zda došlo ke změně stavu. Pokud došlo ke změně stavu se zahájí kontrolu dodržování předpisů. 

Pro všechny spravované počítače se systémem Linux, se provádí kontrolu každých 3 hodiny.

Může trvat až 30 minut, 6 hodin pro řídicí panel zobrazit aktualizovaná data ze spravovaných počítačů.

## <a name="viewing-update-assessments"></a>Vyhodnocování aktualizace zobrazení

V účtu Automation vyberte **správy aktualizací** zobrazíte stav vašeho počítače.

Toto zobrazení obsahuje informace o vašich počítačů, chybějící aktualizace, nasazení aktualizací a plánovaná aktualizace nasazení. V **dodržování předpisů sloupec**, zobrazí se při posledním na počítači byla vyhodnocena. V **aktualizovat agenta připravenosti** sloupec, můžete zobrazit, jestliže stavu služby Agent webu Windows update. Pokud nastane problém, vyberte odkaz přejdete k řešení potíží s dokumentace, která může pomoci, že zjistíte, jaké kroky pro odstranění problému.

Pokud chcete spustit vyhledávání protokolu, který vrátí informace o počítači, aktualizace nebo nasazení, vyberte položku v seznamu. **Hledání protokolů** otevře se podokno pomocí dotazu pro vybranou položku:

![Aktualizace správy výchozí zobrazení](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Instalace aktualizací

Po aktualizace jsou vyhodnocení pro všechny systémy Linux a Windows počítače v pracovním prostoru, můžete nainstalovat požadované aktualizace vytvořením *nasazení aktualizace*. Nasazení aktualizací je plánovaná instalace požadovaných aktualizací pro jeden nebo více počítačů. Můžete určit datum a čas pro nasazení a počítač nebo skupinu počítačů, které chcete zahrnout do oboru nasazení. Další informace o skupinách počítačů najdete v tématu [Skupiny počítačů v Log Analytics](../log-analytics/log-analytics-computer-groups.md).

 Když zahrnete skupiny počítačů ve vašem nasazení aktualizace, členství ve skupině vyhodnotí jenom jednou, v době vytvoření plánu. Do skupiny následné změny se neprojeví. Chcete-li tento problém obejít, odstraňte nasazení plánované aktualizace a znovu vytvořit.

> [!NOTE]
> Virtuální počítače s Windows, které jsou nasazeny v Azure Marketplace ve výchozím nastavení jsou nastaveny na příjem automatických aktualizací ze služby Windows Update. Toto chování nemění, když přidáte toto řešení nebo přidat virtuální počítače s Windows do pracovního prostoru. Pokud aktualizace není aktivně spravovat pomocí tohoto řešení, použije se výchozí chování (pro automatické použití aktualizace).

Abyste se vyhnuli použití mimo okno údržby na Ubuntu aktualizace, překonfigurujte Unattended Upgrade balíček, který chcete zakázat automatické aktualizace. Informace o tom, jak nakonfigurovat balíček najdete v tématu [tématu automatických aktualizací v Průvodci Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Virtuální počítače, které byly vytvořeny z bitové kopie systému Red Hat Enterprise Linux (RHEL) na vyžádání, které jsou k dispozici v Azure Marketplace jsou registrované pro přístup k [Red Hat aktualizace infrastruktury (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) který je nasazen v Azure. Jakýkoli jiný distribuční Linux musí být aktualizované z úložiště online souborů distribučním podle následujících podporovaných metod distribuční.

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Vyberte **chybějící aktualizace** Chcete-li zobrazit seznam aktualizací, které chybí z vašeho počítače. Každá aktualizace je uvedena v seznamu a lze vybrat. Informace o počtu počítačů, které vyžadují aktualizaci, operační systém a odkaz Další informace se zobrazí. **Hledání protokolů** podokně se zobrazují podrobnosti o aktualizacích.

## <a name="view-update-deployments"></a>Nasazení aktualizací zobrazení

Vyberte **aktualizace nasazení** zobrazíte seznam existující nasazení aktualizací. Vyberte některé z nasazení aktualizace v tabulce, čímž otevřete **aktualizace spustit nasazení** podokně pro toto nasazení aktualizace.

![Přehled výsledků aktualizace nasazení](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>Vytvořte nebo upravte nasazení aktualizací

Chcete-li vytvořit nové nasazení aktualizací, vyberte **nasazení aktualizace plánu**. **Nové nasazení aktualizace** otevře se podokno. Zadejte hodnoty pro pomocí vlastností popsaných v následující tabulce:

| Vlastnost | Popis |
| --- | --- |
|Název |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Vyberte **Linux** nebo **Windows**.|
|Počítače, které chcete aktualizovat |Uložené hledání nebo vyberte **počítač** z rozevíracího seznamu a potom vyberte jednotlivé počítače. |
|Klasifikace aktualizací|Vyberte všechny klasifikace aktualizací, které potřebujete. CentOS nepodporují mimo pole.|
|Aktualizace k vyloučení|Zadejte aktualizace, které chcete vyloučit. Pro systém Windows, zadejte v článku KB bez **KB** předponu. Pro Linux zadejte název balíčku nebo použít zástupný znak.  |
|Nastavení plánu|Vyberte čas spuštění a pak vyberte buď **jednou** nebo **opakovaná** pro opakování.|| Časové období údržby |Počet minut nastavit pro aktualizace. Hodnota nemůže být menší než 30 minut nebo déle než 6 hodin. |

## <a name="update-classifications"></a>Klasifikace aktualizací

V následujících tabulkách jsou uvedeny klasifikace aktualizací v nástroji Správa aktualizací s definicí pro každé klasifikace.

### <a name="windows"></a>Windows

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizaci pro určitý problém, která řeší kritickou nesouvisející zabezpečení chybu.        |
|Aktualizace zabezpečení     | Aktualizaci pro problém produktu, související se zabezpečením.        |
|Kumulativní aktualizace     | Úhrnnou sadu oprav hotfix, které jsou spojených oprav pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu, distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Úhrnnou sadu oprav hotfix, které se použijí pro aplikaci.        |
|Aktualizace definic     | Aktualizace jiných souborů definic virů či.        |
|Nástroje     | Nástroj nebo funkci, která pomáhá dokončení jedné nebo více úloh.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalována.        |

### <a name="linux"></a>Linux

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro určitý problém nebo problém s produktu, související se zabezpečením.         |
|Další aktualizace     | Všechny další aktualizace, které nejsou důležité ve své podstatě nebo nejsou aktualizace zabezpečení.        |

Pro systémy Linux správy aktualizací možné rozlišit mezi kritické a bezpečnostní aktualizace v cloudu při zobrazování dat hodnocení kvůli obohacení dat v cloudu. Pro instalaci oprav, Správa aktualizací spoléhá na klasifikaci dat na počítači k dispozici. Na rozdíl od jiných distribuce CentOS tyto informace k dispozici ihned nemá. Pokud máte počítače CentOS nakonfigurované v způsob, jak vrátit data zabezpečení pro následující příkaz, správu aktualizací budou moci oprava podle klasifikace.

```bash
sudo yum -q --security check-update
```

Aktuálně neexistuje žádná metoda podporovaná metoda umožňující nativní klasifikace data dostupnosti na CentOS. V tomto okamžiku pouze best effort podpora je k dispozici pro zákazníky, kteří mohou povolili to samostatně.

## <a name="ports"></a>Porty

Následující adresy se vyžadují speciálně pro správu aktualizací. Probíhá komunikace na tyto adresy přes port 443.

|Veřejný Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

Další informace o portech, které vyžaduje hybridní pracovní proces Runbooku najdete v tématu [hybridní pracovní proces role porty](automation-hybrid-runbook-worker.md#hybrid-worker-role).

## <a name="search-logs"></a>Protokoly vyhledávání

Kromě podrobností, které jsou k dispozici na portálu Azure můžete provést hledání na protokoly. V **sledování změn** podokně, vyberte **analýzy protokolů**. **Hledání protokolů** otevře se podokno.

### <a name="sample-queries"></a>Ukázkové dotazy

Následující tabulka obsahuje ukázkové protokolu vyhledá aktualizace záznamů, které byly shromážděny sadou tohoto řešení:

| Dotaz | Popis |
| --- | --- |
|Aktualizace</br>&#124;kde UpdateState == "Potřebné" a volitelné hodnotu false</br>&#124;Počítače, název, KBID, klasifikace, PublishedDate projektu |Všechny počítače s chybějícími aktualizacemi</br>Přidejte jednu z těchto omezit operačního systému:</br>OSType! = "Linux"</br>OSType == "Linux" |
| Aktualizace</br>&#124;kde UpdateState == "Potřebné" a volitelné hodnotu false</br>&#124;kde počítač == "ContosoVM1.contoso.com"</br>&#124;Počítače, název, KBID, produktu, PublishedDate projektu |Chybějící aktualizace v určitém počítači (nahraďte hodnotu názvem svého počítače)|
| Událost</br>&#124;kde EventLevelName == "Chyba" a každý počítač v ((aktualizace &#124; where (klasifikace == "Aktualizace zabezpečení" nebo klasifikaci == "Kritické aktualizace")</br>&#124;kde UpdateState == "Potřebné" a volitelné hodnotu false </br>&#124;DISTINCT Computer)) |Chybové události pro počítače s chybějícími požadovanými důležitými aktualizacemi nebo aktualizacemi zabezpečení |
| Aktualizace</br>&#124;kde UpdateState == "Potřebné" a volitelné hodnotu false</br>&#124;odlišné název |Konkrétní chybějící aktualizace ve všech počítačích |
| UpdateRunProgress</br>&#124;kde InstallationStatus == "se nezdařilo" </br>&#124;shrnout AggregatedValue = count() pomocí počítače, název, UpdateRunName |Počítače s aktualizacemi, které se nezdařilo spustit aktualizaci</br>Přidejte jednu z těchto omezit operačního systému:</br>OSType = "Windows"</br>OSType == "Linux" |
| Aktualizace</br>&#124;kde OSType == "Linux"</br>&#124;kde UpdateState! = "Není skutečně potřeba" a (klasifikace == "Kritické aktualizace" nebo klasifikaci == "Aktualizace zabezpečení")</br>&#124;shrnout AggregatedValue = count() počítačem. |Seznam všechny počítače se systémem Linux, které mají k dispozici aktualizace balíčku, který řeší chybu zabezpečení, důležité aktualizace nebo zabezpečení |
| UpdateRunProgress</br>&#124;kde UpdateRunName == "DeploymentName"</br>&#124;shrnout AggregatedValue = count() počítačem.|Počítače aktualizované při této hromadné postupné aktualizaci (nahraďte hodnotu názvem vašeho nasazení aktualizací) |

## <a name="integrate-with-system-center-configuration-manager"></a>Integrace se System Center Configuration Managerem

Zákazníci, kteří investovaly v nástroji System Center Configuration Manager pro správu počítačů, serverů a mobilních zařízení také závisí na sílu a vyspělosti nástroje Configuration Manager pomáhá jim Správa aktualizací softwaru. Configuration Manager je součástí jejich cyklus správy (SUM) aktualizace softwaru.

Další informace o integraci do řešení pro správu pomocí nástroje System Center Configuration Manager naleznete v tématu [integraci se System Center Configuration Manager pomocí správy aktualizací](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patch-linux-machines"></a>Počítače se systémem Linux oprava

Následující části popisují možné problémy s Linux opravy.

### <a name="unexpected-os-level-upgrades"></a>Neočekávané upgrady úrovni operačního systému

Na některé varianty Linux, jako je například Red Hat Enterprise Linux může dojít k upgradu operačního systému na úrovni prostřednictvím balíčků. Může to vést ke spuštění správy aktualizací kde změní číslo verze operačního systému. Protože správa aktualizace používá stejné metody aktualizovat balíčky, které správce využije místně na počítače se systémem Linux, toto chování je záměrné.

Abyste se vyhnuli, aktualizace verze operačního systému pomocí správy aktualizací spustí, použijte **vyloučení** funkce.

V systému Red Hat Enterprise Linux je název balíčku pro vyloučení redhat. verze server.x86_64.

![Balíčky pro vyloučení pro Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Kritické / opravy zabezpečení se nepoužijí.

Když nasazujete aktualizace pro počítač s Linuxem, můžete vybrat klasifikace aktualizací. Tento filtrování aktualizací, které se použijí na ty, které splňují zadaná kritéria. Tento filtr se použijí místně na počítači, při nasazení aktualizace.

Protože aktualizovat správu provádí aktualizace obohacení v cloudu, některé aktualizace může být příznakem ve správě aktualizovat tak, že má dopad na zabezpečení, i v případě, že místní počítač nemá tyto informace. Výsledkem je Pokud použijete důležité aktualizace pro počítač s Linuxem, mohou existovat aktualizace, které nejsou označeny jako s zabezpečení dopad na, aktualizace a počítač nejsou nainstalovány.

Aktualizace správy však stále hlásit tento počítač se nekompatibilní, protože obsahuje další informace o příslušné aktualizace.

Nasazení aktualizací pomocí klasifikace aktualizace nefunguje na CentOS mimo pole. Pro SUSE výběr *pouze* jiné aktualizace podle klasifikace může vést k některé zabezpečení aktualizuje také nainstalovat Pokud aktualizace zabezpečení související s zypper (Správce balíčků) nebo jeho závislostí je potřeba nejdřív. Jedná se o omezení systému zypper. V některých případech může být nutné znovu spustit nasazení aktualizace, chcete-li ověřit pomocí protokolu aktualizace.

## <a name="troubleshooting"></a>Řešení potíží

Tato část obsahuje informace, které pomohou při odstraňování problémů s řešením správy aktualizací.

### <a name="windows"></a>Windows

Pokud dojde k potížím při pokusu připojit virtuální počítač nebo řešení, podívejte se **aplikace a Správce služby Logs\Operations** zprávy protokolu událostí v místním počítači pro události, které mají 4502 ID události a události, který obsahovat **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. Následující tabulka obsahuje pro každý specifické chybové zprávy a možná řešení:

| Zpráva | Důvod | Řešení |
|----------|----------|----------|
| Nepodařilo se zaregistrovat počítač pro správu oprav,<br/>registrace se nezdařila s výjimkou<br/>System.InvalidOperationException: {"Zpráva":"Počítač už je<br/>registrovaný k jinému účtu. "} | Počítač je již zařazený, nemá do jiného pracovního prostoru pro správu aktualizací. | Vyčistit stará artefaktů podle [odstraňuje se skupina Hybrid Runbook](automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group).|
| Nelze zaregistrovat počítač pro správu opravy, registrace se nezdařila s výjimkou<br/>System.Net.Http.HttpRequestException: Při odesílání požadavku došlo k chybě. ---><br/>System.Net.WebException: Nadřízené připojení<br/>bylo uzavřeno: Došlo k neočekávané<br/>chybě při příjmu. ---> System.ComponentModel.Win32Exception:<br/>Klient a server nemůžou komunikovat,<br/>protože nepoužívají společný algoritmus. | Proxy nebo brány nebo brána firewall blokuje komunikaci. | [Zkontrolujte požadavky na síť](automation-hybrid-runbook-worker.md#network-planning).|
| Nepodařilo se zaregistrovat počítač pro správu oprav,<br/>registrace se nezdařila s výjimkou<br/>Newtonsoft.Json.JsonReaderException: Chyba při analýze hodnoty kladného nekončena. | Proxy nebo brány nebo brána firewall blokuje komunikaci. | [Zkontrolujte požadavky na síť](automation-hybrid-runbook-worker.md#network-planning).|
| Certifikát předložený službu \<wsid\>. oms.opinsights.azure.com<br/>nebyl vydaný certifikační autoritou<br/>používanou pro služby Microsoft. Kontakt<br/>správce sítě a zjistěte, jestli nepoužívají proxy server bránící<br/>komunikaci prostřednictvím protokolu TLS/SSL. |Proxy nebo brány nebo brána firewall blokuje komunikaci. | [Zkontrolujte požadavky na síť](automation-hybrid-runbook-worker.md#network-planning).|
| Nepodařilo se zaregistrovat počítač pro správu oprav,<br/>registrace se nezdařila s výjimkou<br/>AgentService.HybridRegistration.<br/>PowerShell.Certificates.CertificateCreationException:<br/>Vytvoření certifikátu podepsaného svým držitelem se nezdařilo. ---><br/>System.UnauthorizedAccessException: Přístup byl odepřen. | Chyba při generování certifikátu podepsaného svým držitelem. | Ověřte, že má systémový účet<br/>oprávnění ke čtení ze složky:<br/>**C:\ProgramData\Microsoft\**<br/>** Crypto\RSA**|

### <a name="linux"></a>Linux

Pokud hromadné postupné aktualizace se nepodaří spustit na počítač s Linuxem, zkopírujte následující souboru protokolu a zachovat pro účely odstraňování potíží:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

Pokud dojde k selhání během aktualizace spustit po úspěšně spustí v systému Linux, zjistíte z úlohy, výstup z napadeného počítače v spustit. Možná ze Správce balíčků váš počítač, který můžete prozkoumat a provést akci pro specifické chybové zprávy. Správa aktualizací vyžaduje správce balíčků jako v pořádku pro nasazení aktualizace úspěšná.

Aktualizace balíčků v některých případech může narušovat aktualizovat správy brání nasazení aktualizací z dokončení. Pokud uvidíte, že, budete muset tyto balíčky vyloučit z budoucí aktualizace spustí nebo je nainstalovat ručně sami.

Pokud oprav problém nelze vyřešit, zkopírujte následující souboru protokolu a zachovat ji **před** další nasazení aktualizace spustí pro účely odstraňování potíží:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Další postup

Pokračujte v kurzu se dozvíte, jak spravovat aktualizace pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Spravovat aktualizace a opravy pro virtuální počítače Windows Azure](automation-tutorial-update-management.md)

* Pomocí protokolu vyhledávání v [analýzy protokolů](../log-analytics/log-analytics-log-searches.md) zobrazíte podrobnější data.
* [Vytvářet výstrahy](../log-analytics/log-analytics-alerts.md) při zjištění kritických aktualizací jako chybějící z počítačů, nebo pokud počítač má funkce Automatické aktualizace zakázaná.
