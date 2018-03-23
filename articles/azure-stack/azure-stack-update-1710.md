---
title: Aktualizace zásobníku Azure 1710 (sestavení 20171020.1) | Microsoft Docs
description: Další informace o tom, co je v aktualizaci 1710 pro Azure zásobníku integrované systémy, známé problémy a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 1a482f1d2f3eef8775bb7b64d4f6749f69fa5471
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-1710-update-build-201710201"></a>Aktualizace zásobníku Azure 1710 (sestavení 20171020.1)

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje vylepšení a opravy v tomto balíčku aktualizace – známé problémy pro tuto verzi a toho, kde se stáhnout aktualizaci. Známé problémy jsou rozdělené do známé problémy přímo související s proces aktualizace a známé problémy s sestavení (po instalaci).

> [!IMPORTANT]
> Tento balíček aktualizace je pouze pro Azure zásobníku integrované systémy. Tento balíček aktualizace nevztahují na Azure zásobníku Development Kit.

## <a name="improvements-and-fixes"></a>Vylepšení a opravy

Tato aktualizace zahrnuje následující vylepšení kvality a opravy pro Azure zásobníku.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 vylepšení a opravy

- Aktualizace pro systém Windows Server 2016: 10. října 2017 – KB4041691 (sestavení 14393.1770 operačního systému. V tématu [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) Další informace.

### <a name="additional-quality-improvements-and-fixes"></a>Další kvalitou vylepšení a opravy

- Přidat privilegované koncový bod rutiny prostředí PowerShell pro aktualizaci serveru protokolu NTP (Network Time) a odstranění potíží.
- Byla přidána podpora pro aktualizaci modulů koncový bod privilegované koncový bod právě dostatečně správy JEA () a rutiny seznamu povolených IP adres. 
- Chyby opravené jazyce v privilegované koncový bod.
- Přidání možnosti otočit přihlašovací údaje brány.
- Odebrat CBLocalAdmin účet místního správce. 
- Pevné obsahu výstrahy šablony prezenčního signálu chcete Ujistěte se, že prezenčního signálu výstrahy pracovní správně po aktualizaci.
- Pevné zprostředkovatele prostředků infrastruktury řešení překročení časového limitu během operací FRU. 
- Přidání možnosti pro vývojáře v cloudu používat profily rozhraní API Správce prostředků Azure v zásobníku Azure.
- Zakázat službu Windows Update na nasazení virtuálního počítače (DVM). 
- Odebrat uzel power zapnout nebo vypnout akce z uživatelského rozhraní.
- Různé ostatní výkonu a stability opravy. 
 
## <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

Tato část obsahuje známé problémy, které mohou nastat při instalaci aktualizace 1710.

> [!IMPORTANT]
> Pokud se aktualizace nezdaří, pokud později pokusíte obnovit aktualizace je nutné použít `Resume-AzureStackUpdate` rutiny z privilegovaných koncový bod. Pokračovat aktualizace pomocí portálu správce. (Jde o známý problém v této verzi.) Další informace najdete v tématu [monitorování aktualizací v zásobníku Azure pomocí privilegované koncový bod](azure-stack-monitor-update.md).

| Příznaky  | Příčina  | Řešení |
|---------|---------|---------|
|Při aktualizaci, chyba podobná následující<br>může dojít během kroku "Úložiště hostitelů restartovat uzel úložiště"<br> akce plánu aktualizace.<br><br>**{"název": "Restartujte úložiště hostitelů", "Popis": "Restartujte<br> úložiště hostitelů. chybová zpráva","": "Zadejte restartování Role<br> 'BareMetal' vyvolá k výjimce: \n\n počítač<br> HostName-05 bylo přeskočeno. Nepodařilo se načíst jeho LastBootUpTime<br> prostřednictvím služby WMI se následující chybová zpráva:<br> RPC server nedostupný.<br> (Výjimka z HRESULT: 0x800706BA). \nat Restart hostitel** | Tento problém je způsoben potenciální vadný ovladač existuje v některých konfiguracích. | 1. Přihlaste se k rozhraní základní desky řadiče (BMC) pro správu webových a restartujte hostitele, který je identifikován v chybové zprávě.<br><br>2. Obnovení aktualizace pomocí privilegované koncový bod. |
| Při provádění aktualizace proces aktualizace se stalace<br> a není poskytnutí přehledu o pokroku po kroku "krok: spuštěním kroku 2.4 - instalace<br> Aktualizovat"akce plánu aktualizace.<br><br>Tento krok je následována řadu procesy kopírování .nupkg<br> soubory ke sdíleným složkám interní infrastruktury. Příklad:<br><br>**Kopírování souborů 1 z content\PerfCollector\VirtualMachines k <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Nebo, zobrazí se zpráva:<br><br>**WarningMessage:Task: Volání rozhraní 'LiveUpdate'<br> role Cloud\Fabric\VirtualMachines se nezdařilo:<br> zadejte 'LiveUpdate' role vyvolá VirtualMachines<br> výjimka: není dostatek místa na disku .**  | Problém je způsoben soubory protokolu naplňování disky na virtuální počítač infrastruktury a o problém v systému Windows Server Škálováním na více systémů souborovým serverem (SOFS), bude dodána v další aktualizaci. | O pomoc obraťte se na oddělení zákaznických služeb a podpory společnosti Microsoft. | 
| Při aktualizaci, chyba podobná následující<br> může dojít během kroku "krok: spuštěním kroku 2.13.2 - aktualizace<br> *VM_Name*"v plánu akce aktualizace. (Virtuální počítač<br> Název se může lišit.)<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: volání rozhraní 'LiveUpdate' z<br> role Cloud\Fabric\WAS se nezdařilo: Zadejte 'LiveUpdate' role<br> 'Byl' způsobila výjimku: došlo k CHYBĚ během úložiště<br> inicializace: došlo k chybě při pokusu o vytvoření rozhraní API<br> volání do služby Microsoft Storage: {"Zpráva": "vypršení časového limitu<br> došlo k chybě při komunikaci se službou Service Fabric.<br> Typ výjimky: TimeoutException.<br> Zpráva o výjimce: Vypršel časový limit operace. "}**  | Problém je způsoben vypršení časového limitu vstupně-výstupních operací v systému Windows Server, který bude opraven v další aktualizaci. | Požádejte o pomoc Microsoft CSS.
| Při aktualizaci, chyba podobná následující<br> může dojít během kroku "krok 21 restartování virtuálních počítačů SQL serveru."<br><br>**Typ 'LiveUpdateRestart' Role vyvolá VirtualMachines<br> výjimka: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> dotazování pro virtuální počítač MachineName-Sql01. - 13/10/2017 5:23:50: 00 VerboseMessage: [virtuálních počítačů: LiveUpdateRestart]<br> virtuální počítač je označený jako vysoké dostupnosti. – 13/10/2017 5:23:50: 00<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] v<br>MS. Internal.ServerClusters.ExceptionHelp.Build v<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(Boolean force) v Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() v <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() v Microsoft.FailoverClusters.PowerShell.<br> FCCmdlet.ProcessRecord() - 13/10/2017 5:23:50: 00 upozornění<br>zpráva: úloha: volání rozhraní 'LiveUpdateRestart' z<br> role Cloud\Fabric\VirtualMachines se nezdařilo:** | Tento problém může dojít, pokud virtuální počítač se nepodařilo restartovat. | Požádejte o pomoc Microsoft CSS.
| Když provedete aktualizaci, může dojít k chybě podobný následujícímu:<br><br>**2017-10-22T01:37:37.5369944Z typ "Vypnout" Role 'SQL'<br> vyvolal výjimku: došlo k chybě pozastavení uzlu<br> nástroje 's45r1004-Sql01' Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 v<br> Vypnutí, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>aplikace \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: 50 v řádku < ScriptBlock&#62;,<br> <No file>: řádek 18 v < ScriptBlock&#62;, < žádný soubor&#62;: řádek 16** | Tento problém může dojít, pokud virtuální počítač nelze vložit do pozastaveném stavu na vyprazdňování role. | Požádejte o pomoc Microsoft CSS.
| Při aktualizaci, může dojít k některým z těchto chyb:<br><br>**Typ "Ověřit" Role "ADFS" způsobila výjimku: ověření<br> pro roli služby AD FS nebo grafu se nezdařilo s chybou: Chyba při kontrole služby AD FS<br> testu Koncový bod *endpoint_URI*: výjimka volání<br> " GetResponse"s"0"argumenty:" vzdáleného serveru<br> došlo k chybě: (503) serveru není k dispozici. "v Invoke -<br>ADFSGraphValidation**<br><br>**Typ "Ověřit" Role "ADFS" způsobila výjimku: ověření<br> pro roli služby AD FS nebo grafu se nezdařilo s chybou: Chyba při načítání<br> vlastnosti služby AD FS: Nelze se připojit k <br>net.tcp://localhost: 1 500 nebo zásad. Pokus o připojení už bylo<br> pro časový rozsah 00:00:02.0498923. Kód chyby TCP<br> 10061: žádné připojení může vytvořit, protože cíl<br> počítač je aktivně odmítl 127.0.0.1:1500.<br> v Invoke-ADFSGraphValidation** | Plán akcí aktualizace nelze ověřit stav služby Active Directory Federation Services (AD FS). | Požádejte o pomoc Microsoft CSS.

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Tato část obsahuje známé problémy s sestavení 20171020.1 po instalaci.

### <a name="portal"></a>Portál

- Nemusí být možné zobrazit úložiště nebo výpočetní prostředky na portálu správce. To znamená, že došlo k chybě při instalaci aktualizace a že aktualizace byla nahlášena nesprávně jako úspěšné. Pokud k tomuto problému dochází, požádejte o pomoc Microsoft CSS.
- Může se zobrazit prázdný řídicí panel portálu. Chcete-li obnovit řídicí panel, vyberte ikonu ozubené kolečko v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.
- Když máte zobrazeny vlastnosti skupiny prostředků, **přesunout** tlačítko k dispozici. Toto chování je očekávané. Přesunutí skupin prostředků mezi předplatnými není aktuálně podporován.
- Pro jakýkoli pracovní postup, kde v rozevíracím seznamu vyberte předplatné, skupinu prostředků nebo umístění může zaznamenat jeden nebo více z následujících důvodů:

   - Může se zobrazit prázdný řádek v horní části seznamu. By měl mít pořád povolený výběr položky podle očekávání.
   - Pokud je seznam položek v rozevíracím seznamu krátký, asi nebudete moci zobrazit některé názvy položek.
   - Pokud máte více předplatných uživatele, rozevíracího seznamu skupiny prostředků může být prázdná. 

   Obejít poslední dva problémy, můžete zadat název předplatné nebo skupinu prostředků (pokud ho znáte), nebo můžete místo toho použít PowerShell.
- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.
- Nemůžete se moci zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení můžete ověřit oprávnění pomocí prostředí PowerShell.
  
### <a name="backup"></a>Backup

- Nepovolujte infrastruktura zálohování na **infrastruktura zálohování** okno.

### <a name="health-and-monitoring"></a>Sledování stavu a

- Pokud restartujete instance role infrastruktury, zobrazí se zpráva s oznámením, že restartování se nezdařilo. Ale restartování ve skutečnosti bylo úspěšné.

### <a name="marketplace"></a>Marketplace
- Při pokusu o přidání položky do zásobníku Azure marketplace s použitím **přidat z Azure** možnost, všechny položky můžou být vidět ke stažení.
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.

### <a name="compute"></a>Compute
- Uživatelé mají možnost vytvoření virtuálního počítače pomocí geograficky redundantní úložiště. Tato konfigurace způsobuje, že vytvoření virtuálního počítače k selhání.
- Můžete nakonfigurovat dostupnost virtuálního počítače nastavit pouze u domény selhání jednoho a doméně aktualizace jednoho.
- Neexistuje žádné zkušenosti marketplace pro vytvoření sady škálování virtuálního počítače. Můžete vytvořit škálování nastavit pomocí šablony.
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.
 
### <a name="networking"></a>Sítě
- Nástroj pro vyrovnávání zatížení s veřejnou IP adresu nelze vytvořit pomocí portálu. Jako alternativní řešení můžete použít PowerShell k vytvoření nástroje pro vyrovnávání zatížení.
- Když vytvoříte Vyrovnávání zatížení sítě, musíte vytvořit pravidlo překladu adres sítě. Pokud to neuděláte, obdržíte chybu při pokusu přidat pravidlo NAT po vytvoření nástroje pro vyrovnávání zatížení.
- Veřejnou IP adresu z virtuálního počítače (VM) nelze zrušit přidružení, po virtuálního počítače byla vytvořena a související s touto adresou IP. Zrušení přidružení se zobrazí postup, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu. K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původně přidružený virtuální počítač a ne do nového. V současné době je nutné pouze použít nové veřejné IP adresy pro vytvoření nového virtuálního počítače.
 
### <a name="sqlmysql"></a>SQL/MySQL
- To může trvat až jednu hodinu, než klienti databáze můžete vytvářet v nové SQL nebo MySQL SKU. 
- Vytvoření položky přímo na SQL a MySQL hostitelské servery, které nejsou prováděné poskytovatelem prostředků není podporována a může mít za následek neodpovídající stavu.
 
### <a name="app-service"></a>App Service
- Uživatel musí zaregistrovat zprostředkovatele prostředku úložiště dřív, než vytvoří jejich první funkce Azure v rámci předplatného.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Postupy pole replaceable jednotka (FRU)

- Během hromadné postupné aktualizace nejsou opravit offline bitové kopie. Pokud potřebujete nahradit uzlu jednotek škálování, pracujete s OEM dodavatele hardwaru a ujistěte se, že nahrazené uzel má nejnovější úroveň opravy.

## <a name="download-the-update"></a>Stažení aktualizace

Můžete si stáhnout balíček aktualizace 1710 [zde](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací v zásobníku Azure najdete v tématu [správě aktualizací v přehledu Azure zásobníku](azure-stack-updates.md).
- Informace o tom, jak použít aktualizace najdete v tématu [aktualizace v zásobníku Azure](azure-stack-apply-updates.md).
