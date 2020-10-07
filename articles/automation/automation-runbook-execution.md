---
title: Spouštění runbooků ve službě Azure Automation
description: Tento článek poskytuje přehled o zpracování runbooků v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 883cf48fd38d79544d08a68f2c18fc2d2efb4706
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776285"
---
# <a name="runbook-execution-in-azure-automation"></a>Spouštění runbooků ve službě Azure Automation

Automatizace procesů v Azure Automation umožňuje vytvářet a spravovat PowerShell, pracovní postup PowerShellu a grafické Runbooky. Podrobnosti najdete v tématu [Azure Automation Runbooky](automation-runbook-types.md). 

Automatizace spouští vaše Runbooky na základě logiky definované uvnitř nich. Pokud je Runbook přerušený, restartuje se na začátku. Toto chování vyžaduje, abyste napsali Runbooky, které podporují restart, pokud dojde k přechodným problémům.

Spuštění sady Runbook v Azure Automation vytvoří úlohu, která je jedinou instancí spuštění sady Runbook. Každá úloha přistupuje k prostředkům Azure navázáním připojení k vašemu předplatnému Azure. Úloha může k prostředkům ve vašem datovém centru přistupovat jenom v případě, že tyto prostředky jsou přístupné z veřejného cloudu.

Azure Automation přiřadí pracovnímu procesu spuštění jednotlivých úloh během provádění Runbooku. I když jsou pracovní procesy sdíleny pomocí mnoha účtů Azure, úlohy z různých účtů Automation jsou od sebe izolované. Nemůžete řídit, které služby pracovního procesu vaše práce požaduje.

Když zobrazíte seznam runbooků v Azure Portal, zobrazí se stav každé úlohy, která byla spuštěna pro jednotlivé sady Runbook. Azure Automation ukládá protokoly úloh po dobu maximálně 30 dnů.

Následující diagram znázorňuje životní cyklus úlohy Runbooku pro [Runbooky PowerShellu](automation-runbook-types.md#powershell-runbooks), [Runbooky pracovních postupů PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks)a [grafické Runbooky](automation-runbook-types.md#graphical-runbooks).

![Stavy úlohy – workflow PowerShellu](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Prostředí pro spuštění sady Runbook

Runbooky v Azure Automation můžou běžet buď v izolovaném prostoru Azure, nebo v [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). 

Když jsou Runbooky navržené pro ověřování a spouštění u prostředků v Azure, spouštějí se v izolovaném prostoru Azure, což je sdílené prostředí, které může používat víc úloh. Úlohy používající stejný izolovaný prostor (sandbox) jsou vázány omezeními prostředků izolovaného prostoru (sandbox). Prostředí Azure izolovaného prostoru (sandbox) nepodporuje interaktivní operace. Zabraňuje přístupu ke všem nezpracovaným serverům COM. Vyžaduje také použití místních souborů MOF pro Runbooky, které provádí volání Win32.

Můžete také použít [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ke spouštění Runbooků přímo na počítači, který je hostitelem role a na místních prostředcích v prostředí. Azure Automation ukládá a spravuje Runbooky a pak je doručí do jednoho nebo více přiřazených počítačů.

>[!NOTE]
>Aby bylo možné spustit Hybrid Runbook Worker pro Linux, musí být vaše skripty podepsané a odpovídajícím způsobem nakonfigurován pracovní proces. Alternativně [musí být ověřování podpisu](automation-linux-hrw-install.md#turn-off-signature-validation)vypnuto.

V následující tabulce jsou uvedeny některé úlohy spuštění sady Runbook s doporučeným spouštěcím prostředím uvedeným pro každé z nich.

|Úloha|Doporučení|Poznámky|
|---|---|---|
|Integrace s prostředky Azure|Izolovaný prostor Azure|Hostovaná v Azure je ověřování jednodušší. Pokud používáte Hybrid Runbook Worker na virtuálním počítači Azure, můžete [použít ověřování Runbooku se spravovanými identitami](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Získání optimálního výkonu pro správu prostředků Azure|Izolovaný prostor Azure|Skript se spouští ve stejném prostředí, které má méně latence.|
|Minimalizace provozních nákladů|Izolovaný prostor Azure|Neexistují žádné režijní náklady na výpočetní výkon a nepotřebují virtuální počítač.|
|Spustit dlouho běžící skript|Hybrid Runbook Worker|Izolované prostory Azure mají [omezení prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interakce s místními službami|Hybrid Runbook Worker|Přímý přístup k hostitelskému počítači nebo k prostředkům v jiných cloudových prostředích nebo v místním prostředí. |
|Vyžadování softwaru a spustitelných souborů třetích stran|Hybrid Runbook Worker|Můžete spravovat operační systém a instalovat software.|
|Monitorování souboru nebo složky pomocí Runbooku|Hybrid Runbook Worker|Použijte [úlohu sledovacího](automation-watchers-tutorial.md) procesu na Hybrid Runbook Worker.|
|Spuštění skriptu náročného na prostředky|Hybrid Runbook Worker| Izolované prostory Azure mají [omezení prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Použití modulů s konkrétními požadavky| Hybrid Runbook Worker|Tady je několik příkladů:</br> WinSCP – závislost na winscp.exe </br> Správa služby IIS – závislost při povolování nebo správě služby IIS|
|Instalace modulu pomocí instalačního programu|Hybrid Runbook Worker|Moduly pro izolovaný prostor (sandbox) musí podporovat kopírování.|
|Používejte Runbooky nebo moduly, které vyžadují .NET Framework verze odlišná od 4.7.2.|Hybrid Runbook Worker|Podpora izolovaného prostoru (sandbox) Azure podporuje .NET Framework 4.7.2 a upgrade na jinou verzi se nepodporuje.|
|Spouštění skriptů vyžadujících zvýšení oprávnění|Hybrid Runbook Worker|Izolované prostory neumožňují zvýšení oprávnění. Pomocí Hybrid Runbook Worker můžete vypnout nástroj řízení uživatelských účtů a použít příkaz [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) při spuštění příkazu, který vyžaduje zvýšení úrovně oprávnění.|
|Spustit skripty, které vyžadují přístup k rozhraní WMI (Windows Management Instrumentation) (WMI)|Hybrid Runbook Worker|Úlohy spuštěné v izolovaných prostorech v cloudu nemůžou získat přístup k poskytovateli rozhraní WMI. |

## <a name="temporary-storage-in-a-sandbox"></a>Dočasné úložiště v izolovaném prostoru

Pokud potřebujete vytvořit dočasné soubory jako součást logiky sady Runbook, můžete použít složku Temp (tj. `$env:TEMP` ) v izolovaném prostoru Azure pro Runbooky běžící v Azure. Jediným omezením je nemůžete použít více než 1 GB místa na disku, což je kvóta pro každou izolovaný prostor (sandbox). Při práci s pracovními postupy PowerShell může tento scénář způsobovat problém, protože workflowy PowerShellu používají kontrolní body a skript se může opakovat v jiném izolovaném prostoru (sandboxu).

Pomocí hybridního izolovaného prostoru (sandbox) můžete použít na `C:\temp` základě dostupnosti úložiště na Hybrid Runbook Worker. V případě doporučení pro virtuální počítače Azure byste ale neměli používat [dočasný disk](../virtual-machines/managed-disks-overview.md#temporary-disk) v systému Windows nebo Linux pro data, která je třeba zachovat.

## <a name="resources"></a>Zdroje a prostředky

Vaše Runbooky musí zahrnovat logiku pro práci s [prostředky](/rest/api/resources/resources), například virtuální počítače, síť a prostředky v síti. Prostředky jsou vázané na předplatné Azure a runbooky vyžadují odpovídající přihlašovací údaje pro přístup k jakémukoli prostředku. Příklad zpracování prostředků v sadě Runbook najdete v tématu [zpracování prostředků](manage-runbooks.md#handle-resources).

## <a name="security"></a>Zabezpečení

Azure Automation používá [Azure Security Center (ASC)](../security-center/security-center-intro.md) k zajištění zabezpečení pro vaše prostředky a detekci kompromisů v systémech Linux. Zabezpečení je zajištěno napříč vašimi úlohami, bez ohledu na to, jestli jsou prostředky v Azure, nebo ne. Informace najdete [v tématu Úvod do ověřování v Azure Automation](automation-security-overview.md).

ASC umisťuje omezení pro uživatele, kteří můžou na virtuálním počítači spouštět libovolné skripty, buď podepsané, nebo bez znaménka. Pokud jste uživatel s kořenovým přístupem k VIRTUÁLNÍmu počítači, musíte počítač explicitně nakonfigurovat pomocí digitálního podpisu nebo ho vypnout. V opačném případě můžete spustit skript, který bude po vytvoření účtu Automation používat aktualizace operačního systému a povolit příslušné funkce.

## <a name="subscriptions"></a>Předplatná

[Předplatné](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) Azure je smlouvou s Microsoftem pro použití jedné nebo několika cloudových služeb, pro které se vám účtují poplatky. Pro Azure Automation je každé předplatné propojené s účtem Azure Automation a v účtu můžete [vytvořit víc předplatných](manage-runbooks.md#work-with-multiple-subscriptions) .

## <a name="credentials"></a>Přihlašovací údaje

Sada Runbook vyžaduje příslušné [přihlašovací údaje](shared-resources/credentials.md) pro přístup k jakémukoli prostředku bez ohledu na to, jestli se jedná o systémy Azure nebo třetích stran. Tyto přihlašovací údaje jsou uložené v Azure Automation, Key Vault atd.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation využívá [Azure monitor](../azure-monitor/overview.md) ke sledování operací počítače. Operace vyžadují Log Analytics pracovní prostor a [agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

### <a name="log-analytics-agent-for-windows"></a>Agent Log Analytics pro Windows

[Agent Log Analytics pro systém Windows](../azure-monitor/platform/agent-windows.md) spolupracuje s Azure monitor ke správě virtuálních počítačů a fyzických počítačů s Windows. Počítače můžou běžet v Azure nebo v prostředí mimo Azure, například v místním datovém centru.

>[!NOTE]
>Agent Log Analytics pro systém Windows byl dříve označován jako Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Agent Log Analytics pro Linux

[Agent Log Analytics pro Linux](../azure-monitor/platform/agent-linux.md) funguje podobně jako agent pro systém Windows, ale připojuje počítače se systémem linux k Azure monitor. Agent se instaluje s uživatelským účtem **nxautomation** , který umožňuje provedení příkazů vyžadujících oprávnění root, například na Hybrid Runbook Worker. Účet **nxautomation** je systémový účet, který nevyžaduje heslo.

Během [instalace procesu Hybrid Runbook](automation-linux-hrw-install.md)Worker pro Linux musí být přítomen účet **nxautomation** s odpovídajícími oprávněními sudo. Pokud se pokusíte nainstalovat pracovní proces a účet není přítomen nebo nemáte příslušná oprávnění, instalace se nezdařila.

Neměli byste měnit oprávnění ke `sudoers.d` složce nebo jejímu vlastnictví. Pro účet **nxautomation** se vyžaduje oprávnění sudo a neměla by být odebrána oprávnění. Omezení tohoto omezení na určité složky nebo příkazy může mít za následek zásadní změnu.

K dispozici jsou protokoly pro agenta Log Analytics a účet **nxautomation** :

* /var/opt/Microsoft/omsagent/log/omsagent.log – protokol agenta Log Analytics
* /var/opt/Microsoft/omsagent/Run/automationworker/Worker.log – protokol pracovních procesů služby Automation

>[!NOTE]
>Uživatel **nxautomation** povolený jako součást Update Management provádí pouze podepsané Runbooky.

## <a name="runbook-permissions"></a>Oprávnění runbooků

Sada Runbook potřebuje k ověřování do Azure oprávnění prostřednictvím přihlašovacích údajů. Viz [Správa účtů spustit jako Azure Automation](manage-runas-account.md).

## <a name="modules"></a>Moduly

Azure Automation podporuje řadu výchozích modulů, včetně některých modulů AzureRM (AzureRM. Automation) a modulu obsahujícího několik interních rutin. Podporované jsou taky instalované moduly, včetně AZ Modules (AZ. Automation), které se v tuto chvíli používají v předvolbách pro AzureRM moduly. Podrobnosti o modulech, které jsou k dispozici pro vaše Runbooky a konfigurace DSC, najdete v tématu [Správa modulů v Azure Automation](shared-resources/modules.md).

## <a name="certificates"></a>Certifikáty

Azure Automation používá k ověřování do Azure [certifikáty](shared-resources/certificates.md) nebo je přidává do prostředků Azure nebo třetích stran. Certifikáty se bezpečně ukládají pro přístup prostřednictvím runbooků a konfigurací DSC.

Vaše Runbooky můžou používat certifikáty podepsané svým držitelem, které nejsou podepsané certifikační autoritou (CA). Viz [Vytvoření nového certifikátu](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Úlohy

Azure Automation podporuje prostředí pro spouštění úloh ze stejného účtu Automation. Jedna sada Runbook může mít v jednom okamžiku více spuštěných úloh. Další úlohy, které spouštíte najednou, častěji je lze odeslat do stejného izolovaného prostoru (sandbox). 

Úlohy spuštěné ve stejném procesu izolovaného prostoru (sandbox) můžou navzájem ovlivňovat. Jedním z příkladů je spuštění rutiny [Connect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) . Provedení této rutiny odpojí každou úlohu Runbooku v procesu sdíleného izolovaného prostoru (sandboxu). Příklad práce s tímto scénářem najdete v tématu [prevence souběžných úloh](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>Úlohy PowerShellu spouštěné z Runbooku, který běží v izolovaném prostoru Azure, nemusí běžet v úplném [jazykovém režimu PowerShellu](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="job-statuses"></a>Stavy úlohy

Následující tabulka popisuje stavy, které jsou pro úlohu možné. Můžete zobrazit shrnutí stavu všech úloh sady Runbook nebo přejít k podrobnostem o konkrétní úloze Runbooku v Azure Portal. Můžete také nakonfigurovat integraci s vaším pracovním prostorem Log Analytics pro přeposílání datových proudů úloh Runbooku. Další informace o integraci s protokoly Azure Monitor najdete v tématu [přeposílání datových proudů úloh a datových proudů úloh z automatizace do Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md). Příklad práce se stavy v Runbooku najdete v tématu o tom, jak [získat stavy úloh](manage-runbooks.md#obtain-job-statuses) .

| Status | Popis |
|:--- |:--- |
| Dokončeno |Úloha se úspěšně dokončila. |
| Neúspěšný |Nepovedlo se zkompilovat grafickou sadu Runbook pracovního postupu nebo PowerShellu. PowerShellový Runbook se nepovedlo spustit, nebo má úlohu výjimku. Viz [Azure Automation typy runbooků](automation-runbook-types.md).|
| Selhání, čekání na prostředky |Úloha se nezdařila, protože dosáhla limitu [reálného podílu](#fair-share) třikrát a zároveň začíná ze stejného kontrolního bodu nebo od začátku Runbooku. |
| Ve frontě |Úloha čeká, než budou dostupné prostředky pracovního procesu automatizace, aby bylo možné ji spustit. |
| Obnovování |Systém obnovuje úlohu poté, co byla pozastavena. |
| Spuštěno |Úloha je spuštěná. |
| Spuštění, čekání na prostředky |Úloha byla uvolněna, protože dosáhla spravedlivého limitu sdílení. Brzy bude pokračovat od posledního kontrolního bodu. |
| Spouštění |Úloha byla přiřazena k pracovnímu procesu a systém ho spouští. |
| Zastaveno |Úlohu uživatel zastavil před tím, než se dokončila. |
| Zastavování |Systém zastavuje úlohu. |
| Dočasně blokován. |Platí jenom pro [Runbooky grafických a powershellového pracovního postupu](automation-runbook-types.md) . Úlohu pozastavil uživatel, systém nebo příkaz v Runbooku. Pokud sada Runbook nemá kontrolní bod, začne od začátku. Pokud má kontrolní bod, může se znovu spustit a obnovit z posledního kontrolního bodu. Systém zastaví sadu Runbook pouze v případě, že dojde k výjimce. Ve výchozím nastavení `ErrorActionPreference` je proměnná nastavena na pokračovat, což znamená, že úloha je spuštěná na chybu. Pokud je proměnná preference nastavená na hodnotu zastavit, úloha se při chybě pozastaví.  |
| Pozastavování |Platí jenom pro [Runbooky grafických a powershellového pracovního postupu](automation-runbook-types.md) . Systém se pokouší pozastavit úlohu na žádost uživatele. Runbook se může pozastavit až po dosažení následujícího kontrolního bodu. Pokud již prošl poslední kontrolní bod, byl dokončen před tím, než bude možné ho pozastavit. |

## <a name="activity-logging"></a>Protokolování aktivit

Spuštění sad Runbook v Azure Automation zapisuje podrobnosti do protokolu aktivit pro účet Automation. Podrobnosti o používání protokolu najdete v tématu [načtení podrobností z protokolu aktivit](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Výjimky

Tato část popisuje některé způsoby, jak zpracovávat výjimky nebo přerušované problémy ve vašich sadách Runbook. Příkladem je výjimka protokolu WebSocket. Správné zpracování výjimek zabraňuje přechodným chybám v síti, které způsobují selhání vašich runbooků.

### <a name="erroractionpreference"></a>ErrorActionPreference

Proměnná [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) určuje, jak prostředí PowerShell reaguje na neukončující chybu. Ukončení chyb vždy končí a nejsou ovlivněny `ErrorActionPreference` .

Pokud sada Runbook používá `ErrorActionPreference` , je obvykle neukončující chyba, například `PathNotFound` z rutiny [GET-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) zastaví dokončení sady Runbook. Následující příklad ukazuje použití `ErrorActionPreference` . Konečný příkaz [pro zápis do výstupu](/powershell/module/microsoft.powershell.utility/write-output) se nikdy nespustí, protože se skript zastaví.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Vyzkoušet catch finally

[Try catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) se ve skriptech PowerShellu používá ke zpracování ukončujících chyb. Skript může pomocí tohoto mechanismu zachytit konkrétní výjimky nebo obecné výjimky. `catch`Příkaz by měl být použit ke sledování nebo pokusu o zpracování chyb. Následující příklad se pokusí stáhnout soubor, který neexistuje. Zachytí `System.Net.WebException` výjimku a vrátí poslední hodnotu pro jakoukoliv jinou výjimku.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

Operaci [throw](/powershell/module/microsoft.powershell.core/about/about_throw) lze použít k vygenerování ukončující chyby. Tento mechanismus může být užitečný při definování vlastní logiky v Runbooku. Pokud skript splňuje kritérium, které by ho mělo zastavit, může použít `throw` příkaz k zastavení. Následující příklad používá tento příkaz k zobrazení požadovaného parametru funkce.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>chyby

Vaše Runbooky musí zpracovávat chyby. Azure Automation podporuje dva typy chyb prostředí PowerShell, ukončení a neukončení. 

Ukončení chyb zastaví spuštění Runbooku, když k nim dojde. Sada Runbook se zastavila se stavem úlohy selhalo.

Neukončující chyby umožňují skriptu pokračovat i po jejich výskytu. Příkladem neukončující chyby je jeden, který nastane, když sada Runbook používá `Get-ChildItem` rutinu s cestou, která neexistuje. PowerShell uvidí, že cesta neexistuje, vyvolá chybu a pokračuje do další složky. Chyba v tomto případě nenastaví stav úlohy Runbooku na neúspěšnou a úloha může být dokonce dokončená. Chcete-li vynutit zastavení sady Runbook při neukončující chybě, můžete použít `ErrorAction Stop` rutinu.

## <a name="calling-processes"></a>Volání procesů

Runbooky, které běží v izolovaném prostoru (sandbox) Azure, nepodporují volající procesy, jako jsou spustitelné soubory (soubory **. exe** ) nebo podprocesy. Důvodem je to, že izolovaný postup Azure je sdílený proces spuštěný v kontejneru, který nemusí mít přístup ke všem základním rozhraním API. V případě scénářů vyžadujících software třetí strany nebo volání pro podprocesy byste měli spustit sadu Runbook na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Vlastnosti zařízení a aplikací

Úlohy Runbooku v Azure sandboxu nemají přístup k žádným charakteristikám zařízení nebo aplikací. Nejběžnější rozhraní API, které se používá k dotazování metrik výkonu ve Windows, je rozhraní WMI s některými běžnými metrikami paměti a využitím procesoru. Nezáleží ale na tom, jaké rozhraní API se používá, protože úlohy spuštěné v cloudu nemají přístup k implementaci služby WBEM (Web-Based Enterprise Management) od Microsoftu. Tato platforma je postavená na model CIM (Common Information Model) (CIM), která poskytuje oborové standardy pro definování charakteristik zařízení a aplikací.

## <a name="webhooks"></a>Webhooky

Externí služby, například Azure DevOps Services a GitHub, mohou spustit sadu Runbook v Azure Automation. K provedení tohoto typu spuštění služba používá [Webhook](automation-webhooks.md) prostřednictvím jediné žádosti HTTP. Použití Webhooku umožňuje spouštět Runbooky bez implementace úplné Azure Automation funkce.

## <a name="shared-resources"></a><a name="fair-share"></a>Sdílené prostředky

Pro sdílení prostředků mezi všemi Runbooky v cloudu Azure používá koncept nazvaný poctivá sdílená složka. Při použití spravedlivého sdílení Azure dočasně uvolní nebo zastaví jakoukoli úlohu, která běží déle než tři hodiny. Úlohy pro [powershellové Runbooky](automation-runbook-types.md#powershell-runbooks) a [Runbooky v Pythonu](automation-runbook-types.md#python-runbooks) se zastaví a nerestartují a stav úlohy se zastaví.

V případě dlouhotrvajících úloh Azure Automation se doporučuje použít Hybrid Runbook Worker. Hybridní pracovní procesy Runbooku nejsou omezené na poctivé sdílení a nemají omezení, jak dlouho může být sada Runbook spuštěna. Ostatní [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) úlohy platí pro Azure Sandbox a hybridní pracovní procesy Runbooku. I když hybridní pracovní procesy Runbooku nejsou omezené o tři hodiny, měli byste vyvíjet Runbooky, aby běžely na pracovních procesech, které podporují restart z neočekávaných potíží s místní infrastrukturou.

Další možností je optimalizace sady Runbook pomocí podřízených runbooků. Sada Runbook může například projít stejnou funkcí u několika prostředků, například s databázovou operací na několika databázích. Tuto funkci můžete přesunout do [podřízeného Runbooku](automation-child-runbooks.md) a nechat ji volat pomocí [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook). Podřízené runbooky se spouštějí paralelně v samostatných procesech.

Použití podřízených runbooků zkrátí celkovou dobu, po kterou se nadřazený Runbook dokončí. Sada Runbook může pomocí rutiny [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) kontrolovat stav úlohy podřízeného Runbooku, pokud má ještě více operací po dokončení podřízeného prvku.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak začít s PowerShellovým runbookm, najdete v tématu [kurz: vytvoření Runbooku PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md).
* Informace o práci se sadami Runbook najdete [v tématu Správa runbooků v Azure Automation](manage-runbooks.md).
* Podrobnosti o PowerShellu najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation#automation).
