---
title: Aktualizace služby Azure Stack 1807 | Dokumentace Microsoftu
description: Další informace o co je nového v aktualizaci 1807 pro integrované systémy Azure Stack, včetně známých problémů a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 5e5ebac221060058a44f7605f77d33a133f5e72c
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887219"
---
# <a name="azure-stack-1807-update"></a>Aktualizace služby Azure Stack 1807

*Platí pro: integrované systémy Azure Stack*

Tento článek popisuje obsah balíčku 1807 aktualizace. Tato aktualizace zahrnuje vylepšení, oprav a známé problémy pro tuto verzi sady Azure Stack a kde se stáhnout aktualizaci. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo sestavení aktualizace Azure Stack 1807 je **1.1807.0.76**.  

### <a name="new-features"></a>Nové funkce

Tato aktualizace zahrnuje následující vylepšení pro službu Azure Stack.

- <!-- 1658937 | ASDK, IS --> **Spustit zálohování podle předem daného plánu** – jako zařízení, Azure Stack můžete teď automaticky aktivovat zálohování infrastruktury pravidelně a Eliminujte zásahu člověka. Azure Stack se také automaticky vyčištění z externí sdílené složky pro zálohy, které jsou starší než doba uchování definované. Další informace najdete v tématu [povolit zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).

- <!-- 2496385 | ASDK, IS -->  **Čas do celkový čas zálohování za přenosy dat přidaný.** Další informace najdete v tématu [povolit zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).

-   <!-- 1702130 | ASDK, IS -->  **Záložní kapacita externí nyní zobrazuje správné kapacity z externí sdílené složky.** (Dříve to bylo pevně zakódovat až 10 GB.) Další informace najdete v tématu [povolit zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).

- <!-- 2508488 |  IS   -->  **Rozšířit kapacitu** podle [přidáním uzlů jednotky škálování](azure-stack-add-scale-node.md).

- <!-- 2753130 |  IS, ASDK   -->  **Šablony Azure Resource Manageru teď podporují elementu podmínku** – teď můžete nasadit prostředků v šabloně Azure Resource Manageru pomocí podmínku. Můžete navrhnout šablonu k nasazení prostředků na základě podmínky, jako jsou hodnocení, pokud hodnota parametru je k dispozici. Informace o použití šablony jako podmínku, naleznete v tématu [podmíněné nasazení prostředku](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) a [části proměnných šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) v dokumentaci k Azure. 

   Můžete také použít šablony, které [nasadit prostředky do více než jedno předplatné nebo skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

- <!--2753073 | IS, ASDK -->  **Podpora verzí prostředků Microsoft.Network rozhraní API se aktualizovala** zahrnující podporu pro rozhraní API verze 2017-10-01 z 2015-06-15 pro Azure Stack síťovým prostředkům.  Podpora verzí prostředků mezi 2017-10-01 a 2015-06-15 není součástí této verze, ale budou zahrnuty v budoucí verzi.  Najdete [důležité informace týkající se služby Azure Stack sítě](user/azure-stack-network-differences.md) pro rozdíly funkcí.

- <!-- 2272116 | IS, ASDK   -->  **Azure Stack má přidanou podporu pro zpětné vyhledávání DNS pro externí koncové koncové body infrastruktury Azure stacku** (to je pro portál, adminportal, správu a adminmanagement). To umožňuje službě Azure Stack je externí koncový bod názvů přeložit IP adresu.

- <!-- 2780899 |  IS, ASDK   --> **Azure Stack teď podporuje přidání další síťové rozhraní existujícímu virtuálnímu počítači.**  Tato funkce je dostupná prostřednictvím portálu, Powershellu a rozhraní příkazového řádku. Další informace najdete v tématu [přidání nebo odebrání síťových rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) v dokumentaci k Azure. 

- <!-- 2222444 | IS, ASDK   -->  **Zlepšení přesnosti a odolnost proti chybám se provedly sítě měřiče využití**.  Měření využití sítě jsou nyní přesnější a rozdělení předplatná účet pozastavit, výpadek období a konflikty časování.

- <!-- 2753080 | IS -->  **K dispozici oznámení o aktualizaci.** Připojené služby Azure Stack nasazení teď pravidelně zkontroluje zabezpečeného koncového bodu a určit, jestli jsou aktualizace dostupné pro váš cloud. Toto oznámení se zobrazí v aktualizovat dlaždici, jako by po ručně vyhledávání a import nové aktualizace. Další informace o [Správa aktualizací pro Azure Stack](azure-stack-updates.md).

- <!-- 2297790 | IS, ASDK -->  **Vylepšení klienta služby Azure Stack syslog (funkce preview)**. Tento klient umožňuje předávání auditu a protokolů spojených s infrastrukturou Azure Stack na syslog serveru nebo zabezpečení informací a událostí (SIEM) software pro správu externí do služby Azure Stack. Klient syslog nyní podporuje protokol TCP pomocí prostého textu nebo šifrování TLS 1.2, druhá možnost je výchozí konfigurace. Připojení TLS lze nakonfigurovat buď jen pro server, nebo vzájemné ověřování.

  Pokud chcete konfigurovat, jak klient syslog komunikuje (jako je protokol, šifrování a ověřování) se na server syslog, použijte *Set-SyslogServer* rutiny. Tato rutina je k dispozici z privileged koncového bodu (období).

  Přidání certifikátu na straně klienta pro vzájemné ověřování klienta TLS 1.2 syslog, použijte rutinu Set-SyslogClient v období.

  V této verzi preview zobrazí se mnohem většího počtu audity a upozornění. 

  Protože tato funkce je stále ve verzi preview, není tedy spoléhat na v produkčním prostředí.

  Další informace najdete v tématu [předávání syslog Azure Stack](azure-stack-integrate-security.md).

<!-- - | ####### | IS, ASDK |  **Azure Resource Manager includes the region name.** With this release, objects retrieved from the Azure Resource Manager will now include the region name attribute. If an existing PowerShell script directly passes the object to another cmdlet, the script may produce an error and fail. This is Azure Resource Manager compliant behavior, and requires the calling client to subtract the region attribute. For more information about the Azure Resource Manager see [Azure Resource Manager Documentation](https://docs.microsoft.com/azure/azure-resource-manager/). verify 8-10 mdb -->

- <!-- TBD | IS, ASDK -->  **Změny funkcí delegované poskytovatele.** Počínaje 1807 delegované poskytovatele modelu je zjednodušená za účelem lepší bylo v souladu s modelem prodejce v systému Azure a delegovat poskytovatelé nebudete moct vytvořit další delegovaný zprostředkovatele v podstatě sloučení modelu a provádění delegované poskytovatele funkce je k dispozici na jedné úrovni. Pokud chcete povolit přechod na nový model a správy předplatných, předplatná uživatelů můžete nyní mezi nové nebo existující delegované poskytovatele předplatnými přesunout, které patří do stejného tenanta adresáře. Předplatná uživatelů, které patří do předplatného výchozí zprostředkovatel můžete také přesunout do delegovaný předplatná poskytovatele ve stejném tenantu Active Directory.  Další informace najdete v části [delegování nabídek v Azure stacku](azure-stack-delegated-provider.md).

- <!-- 2536808 IS ASDK --> **Vylepšené čas vytvoření virtuálního počítače** pro virtuální počítače, které jsou vytvořené pomocí Image stáhnout z webu Azure marketplace.

- <!-- TBD | IS, ASDK -->  **Vylepšení použitelnosti Azure Stack Capacity Planner**. Azure Stack [Capacity Planner](http://aka.ms/azstackcapacityplanner) teď nabízí zjednodušené prostředí pro vkládání mezipaměti S2D a S2D kapacitu při definování skladové položky řešení. Odebrali jsme limit 1 000 virtuálních počítačů.


### <a name="fixed-issues"></a>Opravené problémy

- <!-- TBD | ASDK, IS --> Různá vylepšení provedly se proces aktualizace provádět spolehlivější. Kromě toho opravy byly provedeny na základní infrastrukturu, která minimalizovat potenciální výpadek pro úlohy během aktualizace.

- <!--2292271 | ASDK, IS --> Opravili jsme problém kde upravené kvótu neuplatnilo ke stávajícím předplatným. Teď když plán přidružené předplatné uživatele, zvýšit kvótu pro síťový prostředek, který je součástí nabídky nové omezení platí pro již existujících předplatných, jakož i nová předplatná.

- <!-- 448955 | IS ASDK --> Teď můžete úspěšně dotazovat protokolů aktivit pro systémy, které jsou nasazené v časovém pásmu UTC + N.    

- <!-- 2319627 |  ASDK, IS --> Předběžné kontroly zálohování konfiguračních parametrů (cesta/uživatelské jméno/heslo/šifrovací klíč) už nastaví nesprávné nastavení pro konfiguraci zálohování. (Dříve, nesprávné nastavení byly nastaveny do zálohy a zálohy by dojde k selhání při aktivaci.)

- <!-- 2215948 |  ASDK, IS --> Zálohování seznamu nyní aktualizuje, když je ručně odstranit zálohy z externí sdílené složky.

- <!-- 2332636 | IS -->  Aktualizace na tuto verzi už obnoví výchozí vlastníkem předplatného poskytovatele výchozí integrovanou **CloudAdmin** uživatele při nasazení se službou AD FS.

- <!-- 2360715 |  ASDK, IS -->  Při nastavování integrace datových center už přístup k souboru metadat služby AD FS ze sdílené složky Azure. Další informace najdete v tématu [nastavení integrace služby AD FS tím, že poskytuje soubor metadat federace](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Opravili jsme problém, že mu uživatelům přiřadit stávající veřejnou IP adresu, která byla dříve přidělili síťové rozhraní nebo nástroj pro vyrovnávání zatížení na nové síťové rozhraní nebo nástroj pro vyrovnávání zatížení.  

- <!-- 2551834 - IS, ASDK --> Když vyberete *přehled* pro účet úložiště na portálech na správce nebo uživatele *Essentials* podokně správně teď zobrazuje všechny požadované informace. 

- <!-- 2551834 - IS, ASDK --> Když vyberete *značky* pro účet úložiště na portálech na správce nebo uživatele, zobrazí informace o správně.

- <!-- TBD - IS ASDK --> Tato verze služby Azure Stack řeší problém, která bránila použití aktualizací ovladače z balíčků rozšíření výrobce OEM.

-   <!-- 2055809- IS ASDK --> Opravili jsme problém, který zabránil můžete odstraňovat virtuální počítače v okně výpočty, když virtuální počítač se nepodařilo vytvořit.  

- <!--  2643962 IS ASDK -->  Upozornění pro *nízká kapacita paměti* již nezobrazuje správně.

- **Různé opravy** výkonu, stability, zabezpečení a operační systém, který se používá ve službě Azure Stack.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures
Azure Stack používá instalace jádra serveru systému Windows Server 2016 infrastruktury klíče hostitele. Tato verze nainstaluje následující aktualizace systému Windows Server 2016 na serverech infrastruktury pro Azure Stack: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Další informace o těchto ohrožení zabezpečení, klikněte na výše uvedené odkazy nebo najdete v článcích znalostní báze Microsoft [4338814](https://support.microsoft.com/help/4338814) a [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Než začnete

### <a name="prerequisites"></a>Požadavky

- Instalace služby Azure Stack [aktualizovat 1805](azure-stack-update-1805.md) před instalací aktualizace Azure Stack 1807.  Došlo k dispozici žádná aktualizace 1806.  

- Nainstalujte nejnovější dostupné [aktualizaci nebo opravu hotfix pro verzi 1805](azure-stack-update-1805.md#post-update-steps).  
  > [!TIP]  
  > Předplatit následující *RRS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Před zahájením instalace aktualizace 1807, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) ověřte stav služby Azure Stack a vyřešte všechny provozní problémy najít. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci.

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- <!-- 2468613 - IS --> Při instalaci této aktualizace, může se zobrazit upozornění s názvem *chyba – šablona pro typ FaultType UserAccounts.New chybí.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se automaticky zavře po dokončení instalace této aktualizace.

- <!-- 2489559 - IS --> Nepokoušejte se vytvářet virtuální počítače při instalaci této aktualizace. Další informace o správě aktualizací najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md#plan-for-updates).

- <!-- 2830461 - IS --> V některých případech při aktualizaci vyžaduje pozornost, odpovídající nemusí být vygenerována výstraha. Přesné stavu se stále projeví na portálu a není ovlivněn.

### <a name="post-update-steps"></a>Postup po aktualizaci

- <!-- 2933866 – IS --> **Vylepšený stav instalace aktualizace se nezdařila.** Tato verze přináší dvě nové kategorie stavu operátory přineseme další informace o instalaci aktualizace se nezdařila. Jsou dvě kategorie *PreparationFailed*, a *InstallationFailed*. Po instalaci této verze, může se zobrazit informace pro chyby při předchozí instalaci aktualizace revize tak, aby odrážela tyto nové kategorie. 

<!-- *There are no post-update steps for update 1807.* -->

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Toto jsou známé problémy této verze sestavení po instalaci.

### <a name="portal"></a>Portál
- <!--2760466 – IS  ASDK --> Při instalaci nového prostředí Azure Stack, na kterém běží tato verze, upozornění, která informuje o *vyžadována aktivace* se nemusí zobrazit. [Aktivace](azure-stack-registration.md) se vyžaduje, abyste mohli používat marketplace syndikace.  

- <!-- TBD - IS ASDK --> Dva typy pro správu předplatného, které byly [představený poprvé ve verzi 1804](azure-stack-update-1804.md#new-features) by se neměly. Typy předplatného jsou **měření předplatné**, a **využití předplatného**. Tyto typy předplatného jsou viditelné v novým prostředím Azure Stack od verze 1804, ale ještě nejsou připravené k použití. By měla dál používat **výchozí zprostředkovatel** typu předplatného.

- <!-- 2403291 - IS ASDK --> Nemusí mít užívání vodorovný posuvník v dolní části portály správce a uživatele. Pokud nemůžete použít vodorovný posuvník, left pomocí popisu cesty přejít na předchozí okno na portálu tak, že vyberete název okna je chcete zobrazit v seznamu s popisem cesty v horní části portálu.

  ![Navigace s popisem cesty](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Nemusí být možné zobrazit na portálu správce prostředků výpočetního výkonu a úložiště. Příčinou tohoto problému je chyba při instalaci aktualizace, která způsobí, že aktualizace, která se správně hlášené jako úspěšně dokončený. Pokud k tomuto problému dochází, obraťte se na zákaznické podpory Microsoftu o pomoc.

- <!-- TBD - IS --> Může se zobrazit prázdný řídicí panel portálu. Obnovit řídicí panel, vyberte ikonu ozubeného kolečka v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.

- <!-- TBD - IS ASDK --> Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

- <!-- TBD - IS ASDK --> Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.


### <a name="health-and-monitoring"></a>Monitorování stavu a
- <!-- 1264761 - IS ASDK -->  Může se zobrazit upozornění **stavu řadiče** komponenta, která mají následující podrobnosti:  

   Upozornění #1:
   - Název: Infrastrukturu role není v pořádku
   - ZÁVAŽNOST: upozornění
   - KOMPONENTY: Kontroler stavu
   - Popis: Kontroler stavu prezenčního signálu skener není k dispozici. To může mít vliv sestav o stavu a metriky.  

  Upozornění #2:
   - Název: Infrastrukturu role není v pořádku
   - ZÁVAŽNOST: upozornění
   - KOMPONENTY: Kontroler stavu
   - Popis: Stav řadiče skener selhání není k dispozici. To může mít vliv sestav o stavu a metriky.

  Obě výstrahy můžete bezpečně ignorovat a bude automaticky zavřít v čase.  


- <!-- 2812138 | IS --> Může se zobrazit upozornění pro **úložiště** komponenta, která mají následující podrobnosti:

   - Název: Chyba interní komunikace se službou Storage  
   - ZÁVAŽNOST: kritické  
   - KOMPONENTA: úložiště  
   - Popis: Chyba interní komunikace se službou úložiště došlo k chybě při odesílání požadavků na tyto uzly.  

    Upozornění můžete ignorovat, ale budete muset ručně zavřete výstrahu.

- <!-- 2368581 - IS. ASDK --> Operátory Azure stacku, pokud se zobrazí upozornění na nedostatek paměti a virtuální počítače klientů selhání nasazení **Chyba při vytváření virtuálních počítačů Fabric**, je možné, že toto razítko Azure Stack je nedostatek dostupné paměti. Použití [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) nejlépe pochopit dostupné kapacity pro vaše úlohy.


### <a name="compute"></a>Compute

- <!-- 2724873 - IS --> Při použití rutiny Powershellu **Start AzsScaleUnitNode** nebo **Stop-AzsScaleunitNode** ke správě jednotek škálování, první pokus o spuštění nebo zastavení jednotka škálování může selhat. Pokud se rutina nezdaří při prvním spuštění, spusťte rutinu znovu. Druhé spuštění by měl úspěšně dokončit operaci. 

- <!-- 2494144 - IS, ASDK --> Když vyberete velikost virtuálního počítače pro nasazení virtuálního počítače, některé velikosti virtuálních počítačů řady F-Series se nezobrazí jako součást modulu pro výběr velikost při vytváření virtuálního počítače. Tyto velikosti virtuálních počítačů se nezobrazí v selektoru: *F8s_v2*, *F16s_v2*, *F32s_v2*, a *F64s_v2*.  
  Jako alternativní řešení použijte jednu z následujících metod nasazení virtuálního počítače. V každé metodě je třeba zadat velikost virtuálního počítače, které chcete použít.

  - **Šablona Azure Resource Manageru:** při použití šablony, nastavte *vmSize* v šabloně na velikost virtuálního počítače, který chcete použít. Například následující položka se používá k nasazení virtuálního počítače, který používá *F32s_v2* velikost:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** můžete použít [az vm vytvořit](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) příkaz a zadejte velikost virtuálního počítače jako parametr, podobně jako `--size "Standard_F32s_v2"`.

  - **Prostředí PowerShell:** pomocí prostředí PowerShell můžete použít [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) s parametrem, který určuje velikost virtuálního počítače, podobně jako `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Nastavení škálování pro škálovací sady virtuálních počítačů nejsou k dispozici na portálu. Jako alternativní řešení můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdíly mezi verzemi prostředí PowerShell, je nutné použít `-Name` parametr namísto `-VMScaleSetName`.

- <!-- TBD - IS --> Při vytváření skupiny dostupnosti na portálu nastavit tak, že přejdete do **nový** > **Compute** > **dostupnosti**, můžete vytvořit pouze doména selhání a aktualizační doména 1 skupinu dostupnosti. Alternativním řešením je při vytváření nového virtuálního počítače, vytvořte dostupnosti pomocí Powershellu, rozhraní příkazového řádku, nebo v rámci portálu.

- <!-- TBD - IS ASDK --> Při vytváření virtuálních počítačů na portálu user portal Azure Stack na portálu zobrazuje nesprávný počet datových disků, které můžete připojit k DS-series virtuálních počítačů. Virtuálních počítačů řady DS může obsahovat libovolný počet datových disků jako konfiguraci Azure.

- <!-- TBD - IS ASDK --> Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

- <!-- 1662991 IS ASDK --> Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.  

- <!-- 2724961- IS ASDK --> Když se zaregistrujete **Microsoft.Insight** poskytovatele prostředků v nastavení předplatného a vytvoření virtuálního počítače s Windows pomocí hostovaného operačního systému diagnostických povolena, nezobrazí stránka s přehledem virtuálních počítačů typu data metrik. 

   Data metrik, jako je graf procento využití procesoru pro virtuální počítač, najdete **metriky** blade a zobrazit všechny podporované Windows virtuálního počítače hosta metriky.

### <a name="networking"></a>Sítě  

- <!-- 1766332 - IS ASDK --> V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

- <!-- 1902460 - IS ASDK --> Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

- <!-- 16309153 - IS ASDK --> Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.

- <!-- 2702741 -  IS ASDK --> Chcete-li být zachována po zastavte a Navraťte vydání není zaručena veřejné IP adresy, které jsou nasazeny pomocí metody dynamického přidělení.

- <!-- 2529607 - IS ASDK --> Během Azure Stack *tajný klíč otočení*, je období, ve kterém veřejné IP adresy nedostupné přibližně 2 až 5 minut.

-   <!-- 2664148 - IS ASDK --> Ve scénářích, kde tenanta je přístup ke své virtuální počítače s použitím tunelu S2S VPN kterými se mohou setkat scénář, kde pokusy o připojení selhat, pokud místní podsítě byl přidán do místní síťové brány po již vytvořit bránu. 


### <a name="sql-and-mysql"></a>SQL a MySQL



- <!-- No Number - IS, ASDK -->  Speciální znaky, mezery a tečky, včetně nepodporuje **řady** název při vytváření SKU pro poskytovatele prostředků SQL nebo MySQL. 

- <!-- TBD - IS --> Vytvoření položek na serverech, daný hostitel SQL nebo MySQL se podporuje jenom poskytovatele prostředků. Neodpovídající stavu může dojít položky vytvořené na hostitelském serveru, které nebyly vytvořeny pomocí zprostředkovatele prostředků.  

> [!NOTE]  
> <!-- TBD - IS --> Po aktualizaci pro tuto verzi sady Azure Stack, můžete dál používat poskytovatele prostředků SQL nebo MySQL, které jste předtím nasadili.  Doporučujeme, abyste že aktualizujete SQL nebo MySQL, až bude dostupná nová verze. Jako je Azure Stack použití aktualizací pro poskytovatele prostředků SQL nebo MySQL postupně. Pokud používáte verzi 1804, nejprve aplikujte verze 1805 a následně neaktualizují na 1807.  
>  
> Instalace této aktualizace nemá vliv na aktuální použití funkce poskytovatele prostředků SQL nebo MySQL vašich uživatelů. 
> Bez ohledu na verzi poskytovatele prostředků, které můžete použít vaše data uživatelů ve své databáze není dokázal a zůstala přístupná.  

### <a name="app-service"></a>App Service

- <!-- 2352906 - IS ASDK --> Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- <!-- 2489178 - IS ASDK --> Aby bylo možné horizontální navýšení kapacity infrastruktury (pracovních procesů, správy, role front-endu), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.

- <!-- TBD - IS ASDK --> App Service můžete nasadit jenom do *předplatné poskytovatele. výchozí* v tuto chvíli. V budoucí aktualizaci, bude nasazení služby App Service do nové *měření předplatné* , která byla zavedena ve verzi 1804 Azure Stack. Při použití podporuje měření, všechna existující nasazení budou migrovány na tento nový typ předplatného.


### <a name="usage"></a>Využití  
- <!-- TBD - IS ASDK --> Data o využití veřejné IP adresy využití měřičů zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítko, které ukazuje, kdy se záznam vytvořil. V současné době nelze pomocí těchto dat pro monitorování přesné použití veřejné IP adresy.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Stáhnout aktualizaci.
Můžete stáhnout aktualizace balíčku Azure Stack 1807 z [tady](https://aka.ms/azurestackupdatedownload).


## <a name="next-steps"></a>Další postup
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  
- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).  
