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
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fe37a62d364d53d03a232b6fdc41e38a9ae2e30f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227983"
---
# <a name="azure-stack-1807-update"></a>Aktualizace služby Azure Stack 1807

*Platí pro: integrované systémy Azure Stack*

Tento článek popisuje obsah balíčku 1807 aktualizace. Tato aktualizace zahrnuje vylepšení, oprav a známé problémy pro tuto verzi sady Azure Stack a kde se stáhnout aktualizaci. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu    
Číslo sestavení aktualizace Azure Stack 1807 je **1.1807.XX.X**.  


### <a name="new-features"></a>Nové funkce
Tato aktualizace zahrnuje následující vylepšení pro službu Azure Stack.

- <!-- 1658937 | ASDK, IS --> **Spustit zálohování podle předem daného plánu** – jako zařízení, Azure Stack můžete teď automaticky aktivovat zálohování infrastruktury pravidelně a Eliminujte zásahu člověka. Azure Stack se také automaticky vyčištění z externí sdílené složky pro zálohy, které jsou starší než doba uchování definované. 

- <!-- 2496385 | ASDK, IS --> **Čas do celkový čas zálohování za přenosy dat přidaný.**

-   <!-- 1702130 | ASDK, IS --> **Záložní kapacita externí nyní zobrazuje správné kapacity z externí sdílené složky.** (Dříve to bylo pevně zakódovat až 10 GB.)

- <!-- 2508488 |  IS   -->   Rozšířit kapacitu podle [přidáním uzlů jednotky škálování](azure-stack-add-scale-node.md).



### <a name="fixed-issues"></a>Opravené problémy

- <!-- 448955 | IS ASDK --> Teď můžete úspěšně dotazovat protokolů aktivit pro systémy, které jsou nasazené v časovém pásmu UTC + N.    

- <!-- 2319627 |  ASDK, IS --> Předběžné kontroly zálohování konfiguračních parametrů (cesta/uživatelské jméno/heslo/šifrovací klíč) už nastaví nesprávné nastavení pro konfiguraci zálohování. (Dříve, nesprávné nastavení byly nastaveny do zálohy a zálohy by pak selže při tirggered.)

- <!-- 2215948 |  ASDK, IS --> Zálohování seznamu nyní aktualizuje, když je ručně odstranit zálohy z externí sdílené složky.

- <!-- 2332636 - IS -->  Aktualizace na tuto verzi už obnoví výchozí vlastníkem předplatného poskytovatele výchozí integrovanou CloudAdmin uživatele při nasazení se službou AD FS.

- <!-- 2360715 |  ASDK, IS -->  Při nastavování integrace datových center už přístup k souboru metadat služby AD FS ze sdílené složky. Další informace najdete v tématu [nastavení integrace služby AD FS tím, že poskytuje soubor metadat federace](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Opravili jsme problém, že mu uživatelům přiřadit stávající veřejnou IP adresu, která byla dříve přidělili síťové rozhraní nebo nástroj pro vyrovnávání zatížení na nové síťové rozhraní nebo nástroj pro vyrovnávání zatížení.  

-   <!--2292271 | ASDK, IS --> Opravili jsme problém kde upravené kvótu neuplatnilo ke stávajícím předplatným.  Teď když plán přidružený k předplatnému klienta můžete zvýšit kvótu pro síťový prostředek, který je součástí nabídky, nové omezení platí pro existující předplatné, jakož i nová předplatná.

- **Různé opravy** výkonu, stability, zabezpečení a operační systém, který se používá ve službě Azure Stack.


<!-- ### Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Než začnete    

### <a name="prerequisites"></a>Požadavky
- Instalace služby Azure Stack [aktualizovat 1805](azure-stack-update-1805.md) před instalací aktualizace Azure Stack 1807.  Došlo k dispozici žádná aktualizace 1806.  

- Před zahájením instalace aktualizace 1807, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) ověřte stav služby Azure Stack a vyřešte všechny provozní problémy najít. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci.

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace   
- <!-- 2468613 - IS --> Při instalaci této aktualizace, může se zobrazit upozornění s názvem *chyba – šablona pro typ FaultType UserAccounts.New chybí.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se automaticky zavře po dokončení instalace této aktualizace.   

- <!-- 2489559 - IS --> Nepokoušejte se vytvářet virtuální počítače při instalaci této aktualizace. Další informace o správě aktualizací najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Postup po aktualizaci
*Nejsou žádné kroky po aktualizaci pro aktualizaci 1807.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Toto jsou známé problémy této verze sestavení po instalaci.

### <a name="portal"></a>Portál  
- <!-- 2551834 - IS, ASDK --> Když vyberete **přehled** portálech na správce nebo uživatele, informace z účtu úložiště *Essentials* podokně nezobrazí.  V podokně Essentials se zobrazí informace o účtu jako jeho *skupiny prostředků*, *umístění*, a *ID předplatného*.  Další možnosti pro přehled jsou přístupné, jako je *služby* a *monitorování*, stejně jako možnosti *otevřít v Průzkumníkovi* nebo *odstranit účet úložiště* .

  Chcete-li zobrazit není k dispozici informace, použijte [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) rutiny Powershellu.

- <!-- 2551834 - IS, ASDK --> Když vyberete **značky** pro účet úložiště na portálech na správce nebo uživatele, informace se nepodaří načíst a nejsou zobrazeny.  

  Chcete-li zobrazit není k dispozici informace, použijte [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) rutiny Powershellu. 

- <!-- TBD - IS ASDK --> Některé typy pro správu předplatných nejsou k dispozici.  Při upgradu služby Azure Stack na tuto verzi předplatné dva typy, které byly [představený poprvé ve verzi 1804](azure-stack-update-1804.md#new-features) nejsou viditelné v konzole. To se očekává. Typy předplatného není k dispozici jsou *měření předplatné*, a *využití předplatného*. Tyto typy předplatného jsou viditelné v novým prostředím Azure Stack od verze 1804, ale ještě nejsou připravené k použití. By měla dál používat *výchozí zprostředkovatel* typu předplatného.  

- <!-- 2403291 - IS ASDK --> Nemusí mít užívání vodorovný posuvník v dolní části portály správce a uživatele. Pokud nemůžete použít vodorovný posuvník, left pomocí popisu cesty přejít na předchozí okno na portálu tak, že vyberete název okna je chcete zobrazit v seznamu s popisem cesty v horní části portálu.
  ![Navigace s popisem cesty](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Nemusí být možné zobrazit na portálu správce prostředků výpočetního výkonu a úložiště. Příčinou tohoto problému je chyba při instalaci aktualizace, která způsobí, že aktualizace, která se správně hlášené jako úspěšně dokončený. Pokud k tomuto problému dochází, obraťte se na zákaznické podpory Microsoftu o pomoc.

- <!-- TBD - IS --> Může se zobrazit prázdný řídicí panel portálu. Obnovit řídicí panel, vyberte ikonu ozubeného kolečka v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.

- <!-- TBD - IS ASDK --> Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

- <!-- TBD - IS ASDK --> Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.


### <a name="health-and-monitoring"></a>Monitorování stavu a
- <!-- 1264761 - IS ASDK -->  Může se zobrazit upozornění *stavu řadiče* komponenta, která mají následující podrobnosti:  

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

- <!-- 2368581 - IS. ASDK --> Operátory Azure stacku, pokud se zobrazí upozornění na nedostatek paměti a virtuální počítače klientů selhání nasazení *Chyba při vytváření virtuálních počítačů Fabric*, je možné, že toto razítko Azure Stack je nedostatek dostupné paměti. Použití [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) nejlépe pochopit dostupné kapacity pro vaše úlohy.


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

- <!-- TBD - IS ASDK --> Selhání vytvoření image virtuálního počítače neúspěšné položky, který se nedá odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení vytvořte nové image virtuálního počítače s fiktivní virtuálního pevného disku, který je možné vytvářet přes Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-oprava SizeBytes – 1 GB). Tento proces by měla vyřešit problém, který zabraňuje odstranění položky. Pak 15 minut po vytvoření fiktivní image můžete úspěšně ho odstranit.

  Potom můžete opakovat stahování image virtuálního počítače, která původně selhala.

- <!-- TBD - IS ASDK --> Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

- <!-- 1662991 IS ASDK --> Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.  


### <a name="networking"></a>Sítě  

- <!-- 1766332 - IS ASDK --> V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

- <!-- 2304134 IS ASDK --> Nelze odstranit odběr, který má prostředky zóny DNS nebo prostředky směrovací tabulku s ním spojená. Chcete-li úspěšně odstranit předplatné, musíte nejprve odstranit zónu DNS a směrovací tabulky prostředků z předplatného tenanta.


- <!-- 1902460 - IS ASDK --> Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

- <!-- 16309153 - IS ASDK --> Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.

- <!-- TBD - IS ASDK --> Azure Stack nepodporuje přidávání další síťová rozhraní k instanci virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.

- <!-- 2096388 IS --> Na portálu pro správu nelze použít k aktualizaci pravidel pro skupiny zabezpečení sítě.

    Alternativní řešení pro službu App Service: Pokud je potřeba k instance Kontroleru pomocí vzdálené plochy, upravte pravidla zabezpečení v rámci skupiny zabezpečení sítě pomocí Powershellu.  Níže jsou příklady toho, jak *povolit*a potom obnovit konfiguraci tak, aby *Odepřít*:  

    - *Povolte:*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn’t work. Need to set properties again even in case of edit

      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Odepřít:*

        ```powershell

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn’t work. Need to set properties again even in case of edit

        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
        ```


### <a name="sql-and-mysql"></a>SQL a MySQL

- <!-- TBD - IS --> Vytvoření položek na serverech, daný hostitel SQL nebo MySQL se podporuje jenom poskytovatele prostředků. Neodpovídající stavu může dojít položky vytvořené na hostitelském serveru, které nebyly vytvořeny pomocí zprostředkovatele prostředků.  

- <!-- IS, ASDK --> Speciální znaky, mezery a tečky, včetně nepodporuje **řady** nebo **úroveň** názvy při vytváření SKU pro poskytovatele prostředků SQL nebo MySQL.


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


## <a name="see-also"></a>Další informace najdete v tématech
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).
- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
