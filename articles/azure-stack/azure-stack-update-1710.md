---
title: Azure Stack 1710 Update (Build 20171020.1) | Dokumentace Microsoftu
description: Další informace o tom, co je ve verzi 1710 aktualizace pro Azure Stack integrované systémy, známé problémy a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cf870551a3dbd9b5ea0ef6f886dc6451e43b2c25
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377186"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure Stack 1710 Update (Build 20171020.1)

*Platí pro: integrované systémy Azure Stack*

Tento článek popisuje vylepšení a opravy v tomto balíčku aktualizace – známé problémy pro tuto verzi a kde se stáhnout aktualizaci. Známé problémy jsou rozděleny do známé problémy přímo související s proces aktualizace a známé problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="improvements-and-fixes"></a>Vylepšení a oprav

Tato aktualizace zahrnuje následující vylepšení kvality a opravy pro službu Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 vylepšení a oprav

- Aktualizace pro Windows Server 2016: 10. října 2017 – KB4041691 (14393.1770 sestavení operačního systému. Zobrazit [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) Další informace.

### <a name="additional-quality-improvements-and-fixes"></a>Další kvalitou vylepšení a oprav

- Přidání rutin Powershellu privilegovaných koncový bod pomáhají s řešením aktualizace serveru pro Protokol NTP (Network Time).
- Přidání podpory pro aktualizace privilegovaných koncový bod Just Enough Administration (JEA) koncového bodu moduly a rutiny seznamu povolených IP adres. 
- Oprava chyb místním jazyce privilegovaných koncový bod.
- Přidali jsme možnost pravidelná Změna přihlašovacích údajů brány.
- Odebrat CBLocalAdmin účet místního správce. 
- Oprava obsah šablona upozornění prezenčního signálu Chcete-li Ujistěte se, že prezenční signál výstrahy pracovní správně po aktualizaci.
- Oprava zprostředkovatele prostředků infrastruktury kvůli vypršení časového limitu během operací FRU řešení. 
- Přidali jsme možnost pro cloudové vývojáře k používání profilů rozhraní API Azure Resource Manageru ve službě Azure Stack.
- Zakázat službu Windows Update na nasazení virtuálního počítače (DVM). 
- Odebrat uzel power zapnuto/vypnuto akce z uživatelského rozhraní.
- Různé jiné výkon a stabilita opravy. 
 
## <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

Tato část obsahuje známé problémy, které můžete narazit při instalaci aktualizace 1710.

> [!IMPORTANT]
> Pokud se aktualizace nezdaří, když později pokusu o pokračování aktualizace, je nutné použít `Resume-AzureStackUpdate` rutiny z privileged koncového bodu. Běh aktualizace pomocí portálu správce. (Jde o známý problém v této verzi.) Další informace najdete v tématu [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).

| Příznak  | Příčina  | Řešení |
|---------|---------|---------|
|Když provádíte aktualizace, podobný následující chybě<br>může dojít během kroku "Úložiště hostitele restartovat uzel úložiště"<br> akční plán aktualizace.<br><br>**{"name": "Restartujte úložiště hostitelů", "Popis": "Restartujte<br> úložiště hostitele. chybová zpráva","": "Zadejte restartování Role<br> 'BareMetal' vyvolána k výjimce: \n\n počítače<br> HostName-05 se přeskočí. Nepovedlo se načíst jeho LastBootUpTime<br> prostřednictvím služby WMI se následující chybová zpráva:<br> RPC server není k dispozici.<br> (Výjimka z HRESULT: 0x800706BA). \nat Restart hostitel** | Tento problém je způsoben potenciální vadné ovladače k dispozici v některých konfiguracích. | 1. Přihlaste se k webovému rozhraní řadič pro správu základní desky a restartujte hostitele, který je identifikován v chybové zprávě.<br><br>2. Obnovit aktualizace pomocí privilegovaných koncový bod. |
| Když provádíte aktualizace, zobrazí se proces aktualizace zpomalí<br> a po kroku neprovádět průběh "krok: provádí se krok 2.4 – instalace<br> aktualizace"akční plán aktualizace.<br><br>Tento krok je následována řadou kopírování procesy .nupkg<br> soubory do sdílené složky interní infrastrukturu. Příklad:<br><br>**Kopírování souborů 1 z content\PerfCollector\VirtualMachines k <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Nebo, zobrazí se zpráva:<br><br>**WarningMessage:Task: Volání rozhraní "LiveUpdate"<br> role Cloud\Fabric\VirtualMachines se nezdařilo:<br> zadejte "LiveUpdate" role vyvoláno VirtualMachines<br> výjimky: není dostatek místa na disku .**  | Problém je způsoben nezaplňuje disky na virtuálním počítači s infrastrukturou a problém v systému Windows Server horizontální navýšení kapacity souborový Server (SOFS), který bude doručen v následné aktualizaci souborů protokolu. | Prosím požádejte o pomoc Microsoft zákaznický servis a podporu (CSS). | 
| Když provádíte aktualizace, podobný následující chybě<br> může dojít během kroku "krok: provádí se krok 2.13.2 – aktualizace<br> *VM_Name*"z akční plán aktualizace. (Virtuální počítač<br> Název se může lišit.)<br><br>**ActionPlanInstanceWarning OSN/MachineName:<br> WarningMessage:Task: volání rozhraní "LiveUpdate"<br> roli se nepovedlo Cloud\Fabric\WAS: zadejte "LiveUpdate" role<br> 'Byla' vyvolal výjimku: při došlo k CHYBĚ úložiště<br> inicializace: došlo k chybě při pokusu o vytvoření rozhraní API<br> volat službu Microsoft Storage: {"Message": "vypršení časového limitu<br> došlo k chybě při komunikaci se Service Fabric.<br> Typ výjimky: TimeoutException.<br> Zpráva o výjimce: Vypršel časový limit operace. "}**  | Problém je způsoben Windows Server, který bude vyřešen v následné aktualizace vypršel časový limit vstupně-výstupních operací. | Požádejte prosím o pomoc Microsoft CSS.
| Když provádíte aktualizace, podobný následující chybě<br> může dojít během kroku "kroku 21 restartování virtuálních počítačů SQL server."<br><br>**Zadejte "LiveUpdateRestart" role vyvoláno VirtualMachines<br> výjimka: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> dotazování na virtuální počítač MachineName-Sql01. – 10/13/2017 5:23:50: 00 VerboseMessage: [virtuálních počítačů: LiveUpdateRestart]<br> virtuální počítač je označený jako vysoké dostupnosti. – 10/13/2017 5:23:50: 00<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] na<br>MS. Internal.ServerClusters.ExceptionHelp.Build na<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(logická platnost) na Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() na <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() na Microsoft.FailoverClusters.PowerShell.<br> FCCmdlet.ProcessRecord() - 10/13/2017 5:23:50: 00 upozornění<br>zprávy: úloha: vyvolání sady rozhraní "LiveUpdateRestart"<br> role Cloud\Fabric\VirtualMachines se nezdařilo:** | Tomuto problému může dojít, pokud virtuální počítač se nepodařilo restartovat. | Požádejte prosím o pomoc Microsoft CSS.
| Když provádíte aktualizace, může dojít k chybě podobný následujícímu:<br><br>**2017-10-22T01:37:37.5369944Z typ "Vypnout" Role "SQL"<br> vyvolal výjimku: došlo k chybě pozastavuje uzel<br> .v 's45r1004 Sql01' Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 na<br> Vypnutí, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>aplikací \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: 50 na řádek < ScriptBlock&#62;,<br> <No file>: 18 na řádek < ScriptBlock&#62;, < žádný soubor&#62;: řádek 16** | Tomuto problému může dojít, pokud virtuální počítač nelze vložit do pozastaveného stavu k vyprázdnění role. | Požádejte prosím o pomoc Microsoft CSS.
| Když provádíte aktualizace, může dojít k některým z následujících chyb:<br><br>**Typ "Ověřit" Role "ADFS" způsobila výjimku: ověření<br> pro roli služby AD FS/grafu se nezdařilo s chybou: Chyba při kontrole služby AD FS<br> koncový bod pro zjišťování *endpoint_URI*: výjimka volání<br> " GetResponse"s"0"argumenty v počtu:" vzdálený server<br> vrátil chybu: (503) Server není k dispozici. "Invoke-<br>ADFSGraphValidation**<br><br>**Typ "Ověřit" Role "ADFS" způsobila výjimku: ověření<br> pro roli služby AD FS/grafu se nezdařilo s chybou: Chyba při načítání<br> vlastnosti služby AD FS: Nelze se připojit k <br>NET.TCP://localhost: 1 500/zásad. Pokus o připojení trval po<br> pro časové období 00:00:02.0498923. Kód chyby TCP<br> 10061: může být nevytváří žádné připojení, protože cíl<br> počítač aktivně odmítl 127.0.0.1:1500.<br> na Invoke-ADFSGraphValidation** | Akční plán aktualizace nemůže ověřit stav služby Active Directory Federation Services (AD FS). | Požádejte prosím o pomoc Microsoft CSS.

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Tato část obsahuje známé problémy s sestavení 20171020.1 po instalaci.

### <a name="portal"></a>Portál

- Není možné zobrazit na portálu správce prostředků výpočetního výkonu a úložiště. To znamená, že došlo k chybě při instalaci aktualizace a že aktualizace byla nahlášena nesprávně jako úspěšně dokončený. Pokud k tomuto problému dochází, požádejte o pomoc Microsoft CSS.
- Může se zobrazit prázdný řídicí panel portálu. Obnovit řídicí panel, vyberte ikonu ozubeného kolečka v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.
- Když máte zobrazeny vlastnosti skupinu prostředků **přesunout** je tlačítko neaktivní. Toto chování je očekávané. Přesunutí skupin prostředků mezi předplatnými není aktuálně podporováno.
- Pro všechny pracovní postupy, kde v rozevíracím seznamu vyberte předplatné, skupinu prostředků nebo umístění může docházet jeden nebo více z následujících problémů:

   - Může se zobrazit prázdný řádek v horní části seznamu. Byste měli stále moci vybrat položky podle očekávání.
   - Pokud seznam položek v rozevíracím seznamu je krátký, nebudete moci zobrazit některé názvy položek.
   - Pokud máte více předplatných uživatele, může být prázdný rozevírací seznam skupin prostředků. 

   Poslední dva problémy obejít, můžete zadat název předplatného nebo skupiny prostředků (pokud ho znáte) nebo můžete místo toho použít PowerShell.
- Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.
- Nemůžete zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení můžete ověřit oprávnění s použitím prostředí PowerShell.
- **Service Health** okno se nepodaří načíst. Když otevřete okno Service Health na správce nebo uživatel portálu Azure Stack chybovou zprávu a nenačte informace. Toto je očekávané chování. I když můžete vybrat a otevřít Service Health, tato funkce ještě není k dispozici, ale budou implementovány v budoucí verzi služby Azure Stack.
 

### <a name="backup"></a>Backup

- Nepovolujte infrastruktura zálohování na **infrastruktura zálohování** okno.

### <a name="health-and-monitoring"></a>Monitorování stavu a

- Pokud restartování instance role infrastrukturu, můžete obdržet zprávu s oznámením, že při restartování se nepovedlo. Ale ve skutečnosti úspěšně restartování.

### <a name="marketplace"></a>Marketplace
- Při pokusu o přidání položky do Tržiště Azure Stack s použitím **přidat z Azure** možnost, ne všechny položky mohou být viditelná pro stahování.
- Uživatele můžete přejít na kompletní nabídku marketplace bez předplatného a vidí správce různé věci, třeba plány a nabídky. Tyto položky jsou pro uživatele nefunkční.

### <a name="compute"></a>Compute
- Uživatelům se zobrazí možnost k vytvoření virtuálního počítače s geograficky redundantním úložištěm. Tato konfigurace způsobuje, že vytváření virtuálních počítačů k selhání.
- Můžete nakonfigurovat virtuální počítač dostupnosti pouze s jednu doménu selhání a aktualizační doména jednoho.
- Neexistuje žádné možnosti marketplace k vytvoření škálovací sady virtuálních počítačů. Můžete vytvořit škálovací sadu pomocí šablony.
- Nastavení škálování pro škálovací sady virtuálních počítačů nejsou k dispozici na portálu. Jako alternativní řešení můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdíly mezi verzemi prostředí PowerShell, je nutné použít `-Name` parametr namísto `-VMScaleSetName`.
 
### <a name="networking"></a>Sítě
- Nelze vytvořit nástroj pro vyrovnávání zatížení s veřejnou IP adresu pomocí portálu. Jako alternativní řešení můžete použít PowerShell k vytvoření nástroje pro vyrovnávání zatížení.
- Když vytvoříte nástroj pro vyrovnávání zatížení sítě, musíte vytvořit pravidlo překladu adres sítě. Pokud to neuděláte, obdržíte chybu při pokusu o přidání pravidla NAT po vytvoření nástroje pro vyrovnávání zatížení.
- Po byla vytvořena a přidružené tuto IP adresu virtuálního počítače, nelze zrušit přidružení veřejné IP adresy z virtuálního počítače (VM). Zrušení přidružení se zobrazí postup, ale zůstane spojená s původní virtuální počítač dříve přiřazenou veřejnou IP adresu. K tomuto chování dochází i v případě, že znovu přiřadit IP adresu do nového virtuálního počítače (obvykle označované jako *prohození virtuálních IP adres*). Všechny budoucí pokusy o připojení přes tuto IP adresu výsledkem připojení do původně přidružený virtuální počítač a ne do nového. V současné době musí používáte jenom nové veřejné IP adresy pro vytvoření nového virtuálního počítače.
 
### <a name="sqlmysql"></a>SQL/MySQL
- To může trvat až hodinu, než bude tenanti můžou vytvořit databází v nové SQL nebo MySQL SKU. 
- Vytváření položek přímo na SQL a MySQL hostitelské servery, které se neprovádí přes poskytovatele prostředků se nepodporuje a jejím výsledkem neodpovídající stavu.
 
### <a name="app-service"></a>App Service
- Dřív, než vytvoří jejich první funkce Azure v rámci předplatného, musí uživatel zaregistrovat zprostředkovatele prostředku úložiště.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Vyměnitelná jednotka (FRU) postupy pole

- Během Hromadná postupná aktualizace nejsou opravit offline image. Pokud je potřeba nahradit uzel jednotek škálování, pracujete s OEM dodavatele hardwaru a ujistěte se, že nahrazené uzel obsahuje nejnovější úroveň opravy.

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Můžete stáhnout balíček aktualizace 1710 [tady](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).
- Informace o tom, jak použít aktualizace najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
