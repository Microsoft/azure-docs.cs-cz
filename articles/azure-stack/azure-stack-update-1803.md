---
title: Aktualizace zásobníku Azure 1803 | Microsoft Docs
description: Další informace o tom, co je v aktualizaci 1803 pro Azure zásobníku integrované systémy, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 26c77b706f17f49eff782e6d0d73087050739874
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-1803-update"></a>Azure aktualizace 1803 zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje vylepšení a opravy v balíčku aktualizace 1803, známé problémy pro tuto verzi a toho, kde se stáhnout aktualizaci. Známé problémy se dál dělí na problémy, které jsou přímo souvisí s proces aktualizace a problémy s sestavení (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je pouze pro Azure zásobníku integrované systémy. Tento balíček aktualizace nevztahují na Azure zásobníku Development Kit.

## <a name="build-reference"></a>Odkaz na sestavení    
Číslo sestavení aktualizace 1803 zásobník Azure je **20180329.1**.


## <a name="before-you-begin"></a>Než začnete    
> [!IMPORTANT]    
> Nepokoušejte se vytvořit virtuální počítače při instalaci této aktualizace. Další informace o správě aktualizací najdete v tématu [správě aktualizací v přehledu Azure zásobníku](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Požadavky
- Nainstalovat Azure zásobníku [aktualizovat 1802](azure-stack-update-1802.md) před instalací aktualizace 1803 zásobník Azure.    


### <a name="post-update-steps"></a>Postup po aktualizaci
- Po instalaci 1803 nainstalujte všechny použitelné opravy hotfix. Další informace naleznete v následujících článcích znalostní báze knowledge base, a také naše [obsluhy zásad](azure-stack-servicing-policy.md).

  - [KB 4103348 – rozhraní API funkce síťový adaptér služby dojde k chybě při pokusu o instalaci aktualizace Azure zásobníku](https://support.microsoft.com/en-us/help/4103348)

- Po instalaci této aktualizace, zkontrolujte konfiguraci brány firewall zajistit [nezbytné porty](azure-stack-integrate-endpoints.md) jsou otevřené. Například tato aktualizace zavádí monitorování Azure, která zahrnuje změnu protokolů auditu na protokoly aktivity. Díky této změně portu 13012 se teď používá a je třeba také otevřít.  

### <a name="new-features"></a>Nové funkce 
Tato aktualizace zahrnuje následující vylepšení a opravy pro Azure zásobníku.

- **Aktualizace zásobníku Azure tajné klíče** – (účty a certifikáty). Další informace o správě tajných klíčů najdete v tématu [otočit tajné klíče v Azure zásobníku](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatic redirect to HTTPS** when you use HTTP to access the administrator and user portals. This improvement was made based on [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) feedback for Azure Stack. 

- <!-- 2202621  --> **Access the Marketplace** – You can now open the Azure Stack Marketplace by using the [+New](https://ms.portal.azure.com/#create/hub) option from within the admin and user portals the same way you do in the Azure portals.
 
- <!-- 2202621 --> **Azure Monitor** - Azure Stack adds [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to the admin and user portals. This includes new explorers for metrics and activity logs. To access this Azure Monitor from external networks, port **13012** must be open in firewall configurations. For more information about ports required by Azure Stack, see [Azure Stack datacenter integration - Publish endpoints](azure-stack-integrate-endpoints.md).

   Také jako součást tohoto změnit, v části **další služby**, *protokoly auditu* nyní se zobrazí jako *protokoly aktivity*. Funkce je nyní konzistentní s portálem Azure. 

- <!-- 1664791 --> **Sparse files** -  When you add a New image to Azure Stack, or add an image through marketplace syndication, the image is converted to a sparse file. Images that were added prior to using Azure Stack version 1803 cannot be converted. Instead, you must use marketplace syndication to resubmit those images to take advantage of this feature. 
 
   Zhuštěné soubory jsou efektivní formátu použít ke snížení využití prostoru úložiště a zlepšit vstupně-výstupní operace.  Další informace najdete v tématu [Fsutil zhuštěných](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) pro systém Windows Server. 

### <a name="fixed-issues"></a>Opravené problémy

- <!-- 1739988 --> Internal Load Balancing (ILB) now properly handles MAC addresses for back-end VMs, which causes ILB to drop packets to the back-end network when using Linux instances on the back-end network. ILB works fine with Windows instances on the back-end network. 

- <!-- 1805496 --> An issue where VPN Connections between Azure Stack would become disconnected due to Azure Stack using different settings for the IKE policy than Azure.  The values now match the values in Azure. 

- <!-- 2209262 --> The IP issue where VPN Connections was previously visible in the portal; however enabling or toggling IP Forwarding has no effect. The feature is turned on by default and the ability to change this not yet supported.  The control has been removed from the portal. 

- <!-- 1766332 --> Azure Stack does not support Policy Based VPN Gateways, even though the option appears in the Portal.  The option has been removed from the Portal. 

- <!-- 1868283 --> Azure Stack now prevents resizing of a virtual machine that is created with dynamic disks. 

- <!-- 1756324 --> Usage data for virtual machines is now separated at hourly intervals. This is consistent with Azure. 

- <!--  2253274 --> The issue where in the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and manage this information.

- Když vytvoříte virtuální počítač, zprávu *nejde zobrazit ceny* už se zobrazí při výběru velikost pro velikost virtuálního počítače.

- Různé opravy pro výkon, stabilitu, zabezpečení a operační systém, který je používán zásobník Azure.


### <a name="changes"></a>Změny
- Způsob, jak změnit stav na nově vytvořený nabídku z *privátní* k *veřejné* nebo *vyřazení* došlo ke změně. Další informace najdete v tématu [vytvořit nabídku](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace    
<!-- 2328416 --> During installation of the 1803 update, there can be downtime of the blob service and internal services that use blob service. This includes some virtual machine operations. This down time can cause failures of tenant operations or alerts from services that can’t access data. This issue resolves itself when the update completes installation. 


### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Toto jsou známé problémy po instalaci pro sestavení **20180323.2**.

#### <a name="portal"></a>Portál
- Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro Azure zásobníku integrované systémy, používat https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Nemusí být možné zobrazit úložiště nebo výpočetní prostředky na portálu správce. Příčinou tohoto problému je k chybě při instalaci aktualizace, která způsobí, že aktualizace, která se nesprávně hlásit jako úspěšné. Pokud chcete tento problém opakuje, obraťte se na technickou podporu společnosti Microsoft pro pomoc.

- Může se zobrazit prázdný řídicí panel portálu. Chcete-li obnovit řídicí panel, vyberte ikonu ozubené kolečko v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.

- Když máte zobrazeny vlastnosti prostředku nebo skupinu prostředků, **přesunout** tlačítko k dispozici. Toto chování je očekávané. Přesun prostředků nebo skupiny prostředků mezi skupinami prostředků nebo předplatného není aktuálně podporován.

- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.

- Nelze zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení ověřte oprávnění pomocí prostředí PowerShell.

- V řídicím panelu portálu pro správu se nezdaří aktualizace dlaždice zobrazíte informace o aktualizacích. Chcete-li vyřešit tento problém, klikněte na dlaždici jej aktualizovat.

- V portálu pro správu, můžete se setkat kritickou výstrahu pro *Microsoft.Update.Admin* součásti. Název výstrahy, popis a nápravu všech zobrazit jako:  
    - *Chyba: šablonu pro FaultType ResourceProviderTimeout nebyl nalezen.*

  Tuto výstrahu můžete ignorovat. 


<!-- #### Health and monitoring --> 

#### <a name="marketplace"></a>Marketplace
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.



#### <a name="compute"></a>Compute
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

- Když vytvoříte sadu na portálu přejděte na dostupnosti **nový** > **výpočetní** > **sadu dostupnosti**, můžete vytvořit pouze skupiny dostupnosti s doména selhání a aktualizace domény 1. Jako řešení, při vytváření nového virtuálního počítače, vytvořte skupinu dostupnosti pomocí prostředí PowerShell, rozhraní příkazového řádku, nebo v portálu.

- Když vytvoříte virtuální počítače na portálu pro uživatele Azure zásobníku, portál zobrazí nesprávný počet datových disků, které můžete připojit k řady DS virtuálních počítačů. Řady DS virtuální počítače zvládne tolik datových disků jako konfiguraci Azure.

- Když image virtuálního počítače se nepodaří vytvořit, položku se nezdařilo, nelze odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

-  Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování časový limit namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Sítě
- Po vytvoření virtuálního počítače a související s veřejnou IP adresu, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení pravděpodobně fungovat, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původně přidružený virtuální počítač a ne do nového.



- Azure zásobníku podporuje jediný *brány místní sítě* podle IP adresy. To platí ve všech předplatných klienta. Po vytvoření první místní síťové brány připojení, další pokusy o vytvoření prostředku brány místní sítě se stejnou adresou IP jsou blokovány.

- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna na vlastní selhání serveru DNS. Aktualizované nastavení nejsou nabídnutých do virtuálních počítačů v této virtuální sítě.

- Azure zásobník nepodporuje přidávání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.

- <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Alternativní řešení pro službu App Service: Pokud potřebujete vzdálenou plochu instance řadiče, můžete upravit pravidla zabezpečení v rámci skupiny zabezpečení sítě pomocí prostředí PowerShell.  Následují příklady *povolit*a potom obnovte konfiguraci *Odepřít*:  
    
    - *Povolte:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
- Než budete pokračovat, zkontrolujte důležité upozornění v [před zahájením](#before-you-begin) téměř začátek tyto poznámky k verzi.

- Může trvat až jednu hodinu, než uživatelé mohou vytvářet databází v nové nasazení SQL nebo MySQL.

- Vytvoření položek na serverech, že hostitel SQL nebo MySQL je podporována pouze poskytovatele prostředků. Položky vytvořené na hostitelském serveru, které nebyly vytvořeny pomocí poskytovatele prostředků může mít za následek neodpovídající stavu.  


> [!NOTE]  
> Po provedení aktualizace na 1803 zásobník Azure, můžete nadále používat zprostředkovatele prostředků SQL a MySQL, které jste předtím nasadili.  Doporučujeme, abyste že aktualizujete SQL a MySQL, když je dostupná nová verze. Zásobník Azure, jako je aktualizace pro poskytovatele prostředků SQL a MySQL postupně.  Pokud používáte verzi 1711, nejprve použít verzi 1712 pak 1802 a potom aktualizovat 1803.      
>   
> Instalace aktualizace 1803 nemá vliv na aktuální použití funkce SQL nebo MySQL poskytovatelů prostředků, které vaši uživatelé.
> Bez ohledu na verzi poskytovateli prostředků, které používáte vaše data uživatelů v své databáze není dotyku a zůstávají dostupné.    



#### <a name="app-service"></a>App Service
- Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- Aby bylo možné škálovat infrastrukturu (pracovních procesů, správy, front-end rolí), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočet.


#### <a name="usage"></a>Využití  
- Využití veřejné IP adresy využití měření data zobrazují stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítka, který ukazuje vytvoření záznamu. Tato data v současné době nelze použít pro monitorování přesné veřejnou IP adresu využití.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Stažení nástroje Azure zásobníku z Githubu
- Při použití *vyvolání webrequest* rutiny prostředí PowerShell ke stažení protokolů Azure nástroje z Githubu, obdržíte chybu:     
    -  *vyvolání webrequest: požadavek byl přerušen: Nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu poslední zastaralá podpora Githubu Tlsv1 a Tlsv1.1 kryptografických standardů (výchozí nastavení pro prostředí PowerShell). Další informace najdete v tématu [slabé kryptografické standardy odebrání oznámení](https://githubengineering.com/crypto-removal-notice/).

  Chcete-li vyřešit tento problém, přidejte `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` do horní části skript, který chcete vynutit konzole PowerShell pro stahování z úložiště Githubu TLSv1.2.


## <a name="download-the-update"></a>Stažení aktualizace
Můžete si stáhnout balíček aktualizace 1803 zásobník Azure z [zde](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Další informace najdete v tématech
- Použití privilegovaných koncového bodu (období) ke sledování a obnovit aktualizací naleznete v části [monitorování aktualizací v zásobníku Azure pomocí privilegované koncový bod](azure-stack-monitor-update.md).
- Přehled správy aktualizací v zásobníku Azure najdete v tématu [správě aktualizací v přehledu Azure zásobníku](azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace se zásobníkem Azure najdete v tématu [aktualizace v zásobníku Azure](azure-stack-apply-updates.md).
