---
title: Aktualizace služby Azure Stack 1804 | Dokumentace Microsoftu
description: Další informace o tom, co je ve verzi 1804 aktualizace pro Azure Stack integrované systémy, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 496aea1195885c582d3529d7ddb43210aad5fea1
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990128"
---
# <a name="azure-stack-1804-update"></a>Aktualizace služby Azure Stack 1804

*Platí pro: integrované systémy Azure Stack*

Tento článek popisuje vylepšení a opravy v balíčku aktualizace 1804, známé problémy pro tuto verzi a kde se stáhnout aktualizaci. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu    
Číslo sestavení aktualizace Azure Stack 1804 je **20180513.1**.   

### <a name="new-features"></a>Nové funkce
Tato aktualizace zahrnuje následující vylepšení pro službu Azure Stack.

- <!-- 15028744 - IS -->  **Visual Studio – podpora pro odpojené nasazení Azure Stack pomocí služby AD FS**. V sadě Visual Studio teď můžete přidat předplatná a k ověření pomocí služby AD FS federovaných přihlašovacích údajů uživatele. 
 
- <!-- 1779474, 1779458 - IS --> **Použijte virtuální počítače řady Av2 a F**. Azure Stack teď můžete použít virtuální počítače na základě velikostí virtuálních počítačů řady Av2 a F-series. Další informace najdete v části [velikostí virtuálních počítačů, které jsou podporované ve službě Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Nový správce předplatných**. S 1804 existují dva nové typy předplatného k dispozici na portálu. Tyto nové typy předplatného jsou pomocí nové instalace služby Azure Stack od verze 1804 kromě předplatné poskytovatele. výchozí a viditelné. *Nepoužívejte tyto nové typy předplatného s touto verzí služby Azure Stack*. Oznámíme později dostupnosti používat tyto typy předplatného se pomocí budoucí aktualizaci. 

  Pokud aktualizujete na verzi 1804 Azure Stack, nejsou viditelné dva nové typy předplatného. Ale integrované systémy pro nová nasazení služby Azure Stack a instalace sady Azure Stack Development Kit verzi 1804 nebo novější mají přístup ke všem typům tři předplatného.  

  Tyto nové typy předplatného jsou součástí větší změny pro zabezpečení předplatné poskytovatele. výchozí a aby bylo snazší nasadit sdílené prostředky, jako je hostitelem SQL servery. Protože budeme přidávat další části této větší změny s budoucími aktualizacemi ke službě Azure Stack, může dojít ke ztrátě prostředky nasazené v rámci tyto nové typy předplatného. 

  Jsou nyní viditelné typy tři předplatného:  
  - Výchozí předplatné poskytovatele: nadále používat tento typ předplatného. 
  - Monitorování míry využívání předplatného: *nepoužívejte tento typ předplatného.*
  - Využití předplatného: *nepoužívejte tento typ předplatného*

  



## <a name="fixed-issues"></a>Opravené problémy

- <!-- IS, ASDK -->  V portálu pro správu máte již aktualizovat aktualizovat dlaždici předtím, než se zobrazí informace.
 
- <!-- 2050709  -->  Teď můžete na portálu pro správu k úpravě metrik úložiště pro službu Blob service, služba Table service a služba front.
 
- <!-- IS, ASDK --> V části **sítě**, po kliknutí na **připojení** nastavit připojení k síti VPN, **Site-to-site (IPsec)** je teď k dispozici jenom možnost.

- **Různé opravy** výkonu, stability, zabezpečení a operační systém, který se používá ve službě Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Další verze vypršel časový limit v této aktualizaci  
Následující jsou teď k dispozici, ale které nevyžadují aktualizaci Azure Stack 1804.
- **Aktualizace pro Microsoft Azure Stack System Center Operations Manageru sada sledování**. Nová verze (1.0.3.0) Microsoft System Center Operations Manager monitorování Pack pro službu Azure Stack je k dispozici pro [Stáhnout](https://www.microsoft.com/download/details.aspx?id=55184). S touto verzí můžete použít instanční objekty při přidání připojené nasazení Azure stacku. Tato verze obsahuje taky prostředí správy aktualizací, které vám umožní převzít nápravné akce přímo z nástroje Operations Manager. Existují také nové řídicí panely, které zobrazení poskytovatelů prostředků a jednotek škálování, škálování jednotek uzlů.

- **Nové Azure Stack verze prostředí PowerShell správce 1.3.0**.  Azure Stack Powershellu 1.3.0 je teď dostupná k instalaci. Tato verze obsahuje příkazy pro všechny poskytovatele prostředků správce ke správě služby Azure Stack.  V této verzi se přestanou nějaký obsah z Githubu nástroje Azure Stack [úložiště](https://github.com/Azure/AzureStack-Tools). 

   Podrobné informace o instalaci, postupujte [pokyny](azure-stack-powershell-install.md) nebo [pomáhají](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) obsahu pro Azure Stack modul 1.3.0. 

- **Počáteční verze Reference k rozhraní API Rest Azure Stack**. [Reference k rozhraní API pro všechny poskytovatele prostředků Azure stacku správu](https://docs.microsoft.com/rest/api/azure-stack/) je publikováno. 


## <a name="before-you-begin"></a>Než začnete    

### <a name="prerequisites"></a>Požadavky
- Instalace služby Azure Stack [1803 aktualizovat](azure-stack-update-1803.md) před instalací aktualizace Azure Stack 1804.    

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace   
- Během instalace aktualizace 1804, může se zobrazit upozornění s názvem *chyba – šablona pro typ FaultType UserAccounts.New chybí.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se automaticky zavře po dokončení aktualizace na verzi 1804.   
 
- <!-- TBD - IS --> Nepokoušejte se vytvářet virtuální počítače při instalaci této aktualizace. Další informace o správě aktualizací najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Postup po aktualizaci
Po dokončení instalace 1804 nainstalujte všechny příslušné opravy hotfix. Další informace naleznete následující články znalostní báze, stejně jako naše [zásady obsluhy](azure-stack-servicing-policy.md).  
 - [KB 4344114 – oprava Hotfix Azure Stack 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Toto jsou známé problémy po instalaci pro sestavení **20180513.1**.

#### <a name="portal"></a>Portál
- <!-- 1272111 - IS --> Po instalaci nebo aktualizaci pro tuto verzi sady Azure Stack, nebudete moci zobrazit jednotky škálování služby Azure Stack v portálu pro správu.  
  Alternativní řešení: Pomocí Powershellu zobrazit informace o jednotkách škálování. Další informace najdete v tématu [pomáhají](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) obsahu pro Azure Stack modul 1.3.0. 

- <!-- 2332636 - IS -->  Když používáte službu AD FS pro Azure Stack identitu systému a aktualizace pro tuto verzi sady Azure Stack, výchozí vlastník výchozí předplatné poskytovatele se resetují na předdefinované **CloudAdmin** uživatele.  
  Alternativní řešení: Chcete-li tento problém vyřešit, po instalaci této aktualizace, použijte krok 3 ze [deklarací aktivování automatizace ke konfiguraci vztahu důvěryhodnosti zprostředkovatele ve službě Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) postup resetovat vlastníka výchozí předplatné poskytovatele.   

- <!-- TBD - IS ASDK --> Některé typy pro správu předplatných nejsou k dispozici.  Při upgradu služby Azure Stack na tuto verzi předplatné dva typy, které byly [představený poprvé ve verzi 1804](#new-features) nejsou viditelné v konzole. To se očekává. Typy předplatného není k dispozici jsou *měření předplatné*, a *využití předplatného*. Tyto typy předplatného jsou viditelné v novým prostředím Azure Stack od verze 1804, ale ještě nejsou připravené k použití. By měla dál používat *výchozí zprostředkovatel* typu předplatného.  


- <!-- TBD -  IS ASDK -->Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro službu Azure Stack integrované systémy, používat https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Nemusí mít užívání vodorovný posuvník v dolní části portály správce a uživatele. Pokud nemůžete použít vodorovný posuvník, left pomocí popisu cesty přejít na předchozí okno na portálu tak, že vyberete název okna je chcete zobrazit v seznamu s popisem cesty v horní části portálu.
  ![Navigace s popisem cesty](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Nemusí být možné zobrazit na portálu správce prostředků výpočetního výkonu a úložiště. Příčinou tohoto problému je chyba při instalaci aktualizace, která způsobí, že aktualizace, která se správně hlášené jako úspěšně dokončený. Pokud k tomuto problému dochází, obraťte se na zákaznické podpory Microsoftu o pomoc.

- <!-- TBD - IS --> Může se zobrazit prázdný řídicí panel portálu. Obnovit řídicí panel, vyberte ikonu ozubeného kolečka v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.

- <!-- TBD - IS ASDK --> Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

- <!-- TBD - IS ASDK --> Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.

- <!-- TBD - IS ASDK --> V portálu pro správu, může se zobrazit kritickou výstrahu pro *Microsoft.Update.Admin* komponenty. Název výstrahy, popis a nápravu všech zobrazit jako:  
    - *Chyba: chybí šablona pro FaultType ResourceProviderTimeout.*

  Toto upozornění můžete ignorovat. 


#### <a name="health-and-monitoring"></a>Monitorování stavu a
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

  Obě výstrahy můžete bezpečně ignorovat. Zavře se automaticky v čase.  
 

#### <a name="compute"></a>Compute
- <!-- TBD - IS --> Když vyberete velikost virtuálního počítače pro nasazení virtuálního počítače, některé velikosti virtuálních počítačů řady F-Series se nezobrazí jako součást modulu pro výběr velikost při vytváření virtuálního počítače. Tyto velikosti virtuálních počítačů se nezobrazí v selektoru: *F8s_v2*, *F16s_v2*, *F32s_v2*, a *F64s_v2*.  
  Jako alternativní řešení použijte jednu z následujících metod nasazení virtuálního počítače. V každé metodě je třeba zadat velikost virtuálního počítače, které chcete použít.

  - **Šablona Azure Resource Manageru:** při použití šablony, nastavte *vmSize* v šabloně na požadovanou velikost virtuálního počítače. Například následující se používá k nasazení virtuálního počítače, který používá *F32s_v2* velikost:  

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

- <!-- TBD - IS ASDK --> Při vytváření virtuálních počítačů na portálu user portal Azure Stack na portálu zobrazuje nesprávný počet datových disků, které můžete připojit k virtuální počítač řady D series. Všechny podporované řady D series virtuálních počítačů může obsahovat libovolný počet datových disků jako konfiguraci Azure.

- <!-- TBD - IS ASDK --> Selhání vytvoření image virtuálního počítače neúspěšné položky, který se nedá odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení vytvořte nové image virtuálního počítače s fiktivní virtuálního pevného disku, který je možné vytvářet přes Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-oprava SizeBytes – 1 GB). Tento proces by měla vyřešit problém, který zabraňuje odstranění položky. Pak 15 minut po vytvoření fiktivní image můžete úspěšně ho odstranit.

  Potom můžete zkusit znovu stáhněte image virtuálního počítače, která původně selhala.

- <!-- TBD - IS ASDK --> Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

- <!-- 1662991 IS ASDK --> Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.  


#### <a name="networking"></a>Sítě
- <!-- 1766332 - IS ASDK --> V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

- <!-- 2388980 - IS ASDK --> Po vytvoření virtuálního počítače a přidružené k veřejné IP adresy, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení přidružení zdánlivě fungovat, ale zůstane spojená s původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používaly pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že znovu přiřadit IP adresu do nového virtuálního počítače (obvykle označované jako *prohození virtuálních IP adres*). Všechny budoucí pokusy o připojení přes tuto IP adresu výsledkem připojení do původně přidružený virtuální počítač a ne do nového.

- <!-- 2292271 - IS ASDK --> Při zvýšení maximální kvóty pro sítě prostředek, který je součástí nabídky a plán, který je přidružený k předplatnému klienta služby, nové omezení neplatí pro toto předplatné. Nový limit se však nevztahují na nových předplatných, které jsou vytvořeny po zvýšení této kvóty. 

  Chcete-li tento problém vyřešit, použijte doplňkový plán na zvýšení této kvóty sítě při v plánu je už přidružený k předplatnému. Další informace najdete v tématu Jak [zpřístupnit doplňkový plán](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

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

#### <a name="sql-and-mysql"></a>SQL a MySQL

- <!-- TBD - IS --> Vytvoření položek na serverech, daný hostitel SQL nebo MySQL se podporuje jenom poskytovatele prostředků. Neodpovídající stavu může dojít položky vytvořené na hostitelském serveru, které nebyly vytvořeny pomocí zprostředkovatele prostředků.  

- <!-- IS, ASDK --> Speciální znaky, mezery a tečky, včetně nepodporuje **řady** nebo **úroveň** názvy při vytváření SKU pro poskytovatele prostředků SQL nebo MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Po aktualizaci na verzi 1804 Azure Stack, můžete dál používat poskytovatele prostředků SQL nebo MySQL, které jste předtím nasadili.  Doporučujeme, abyste že aktualizujete SQL nebo MySQL, až bude dostupná nová verze. Jako je Azure Stack použití aktualizací pro poskytovatele prostředků SQL nebo MySQL postupně.  Pokud používáte verzi 1802, nejprve použít verzi 1803 a následně neaktualizují na verzi 1804.      
>   
> Instalace aktualizace 1804 nemá vliv na aktuální použití funkce poskytovatele prostředků SQL nebo MySQL vašich uživatelů.
> Bez ohledu na verzi poskytovatele prostředků, které můžete použít vaše data uživatelů ve své databáze není dokázal a zůstala přístupná.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- <!-- TBD - IS ASDK --> Aby bylo možné horizontální navýšení kapacity infrastruktury (pracovních procesů, správy, role front-endu), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.

- <!-- TBD - IS ASDK --> App Service můžete nasadit jenom do **výchozí předplatné poskytovatele** v tuto chvíli.  V budoucí aktualizaci služby App Service nasadí do nového předplatného měření zavedené ve verzi 1804 Azure Stack a všechna existující nasazení budou migrovány na tento nový odběr také.

#### <a name="usage"></a>Využití  
- <!-- TBD - IS ASDK --> Data o využití veřejné IP adresy využití měřičů zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítko, které ukazuje, kdy se záznam vytvořil. V současné době nelze pomocí těchto dat pro monitorování přesné použití veřejné IP adresy.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Stáhnout aktualizaci.
Můžete stáhnout aktualizace balíčku Azure Stack 1804 z [tady](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Další informace najdete v tématech
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).
- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
