---
title: Aktualizace zásobníku Azure 1805 | Microsoft Docs
description: Další informace o co je nového v aktualizaci 1805 u Azure zásobníku integraci systémů, včetně známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 80ed0d2353fc6ea3a515c0d05475c713920abe46
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295742"
---
# <a name="azure-stack-1805-update"></a>Azure aktualizace 1805 zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje vylepšení a opravy v balíčku aktualizace 1805, známé problémy pro tuto verzi a kde se stáhnout aktualizaci. Známé problémy se dál dělí na problémy, které jsou přímo souvisí s proces aktualizace a problémy s sestavení (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je pouze pro Azure zásobníku integrované systémy. Tento balíček aktualizace nevztahují na Azure zásobníku Development Kit.

## <a name="build-reference"></a>Odkaz na sestavení    
Číslo sestavení aktualizace 1805 zásobník Azure je **1.1805.1.47**.  

> [!TIP]  
> Na základě názorů zákazníků, dojde k aktualizaci verze schématu používán pro Microsoft Azure zásobníku.  Počínaje touto aktualizací 1805, nové schéma lépe představuje aktuální verze cloudu.  
> 
> Verze schématu je nyní *Version.YearYearMonthMonth.MinorVersion.BuildNumber* kde sady druhý a třetí znamenat verze a verze. Například 1805.1 představuje *verze výrobní* (RTM) verzi 1805.  


### <a name="new-features"></a>Nové funkce
Tato aktualizace obsahuje následující vylepšení pro Azure zásobníku.
<!-- 2297790 - IS, ASDK --> 
- **Nyní zahrnuje Azure zásobníku *Syslog* klienta** jako *funkce ve verzi preview*. Tento klient umožňuje odesílání protokoly auditu a zabezpečení spojených s infrastrukturou Azure zásobníku se Syslog serveru nebo zabezpečení informace a událostí správy (SIEM) softwarem, který je externí do protokolů Azure. Klient pro Syslog v současné době podporuje pouze připojení bez ověření UDP přes výchozí port 514. Datová část každou zprávu Syslog je naformátován společné formátu událostí (CEF). 

  Chcete-li nakonfigurovat klienta Syslog, použijte **Set-SyslogServer** rutiny v privilegované koncový bod. 

  S touto verzí preview může se zobrazit následující tři výstrahy. Když sada protokolů Azure, patří tyto výstrahy *popisy* a *nápravy* pokyny. 
  - TITLE: Integrity kódu vypnuto  
  - TITLE: Integrity kódu v režimu auditování 
  - TITLE: Uživatelský účet vytvořený

  Když tato funkce je ve verzi preview, nemělo by v produkčním prostředí spoléhat.   




### <a name="fixed-issues"></a>Opravené problémy

<!-- # - applicability -->
- Vyřešili problém, který blokovaný [otevření nové žádosti o podporu z rozevíracího seznamu](azure-stack-manage-portals.md#quick-access-to-help-and-support) z portálu pro správu. Tato možnost teď funguje tak, jak má. 

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán zásobník Azure.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Než začnete    

### <a name="prerequisites"></a>Požadavky
- Nainstalovat Azure zásobníku [aktualizovat 1804](azure-stack-update-1804.md) před instalací aktualizace 1805 zásobník Azure.    
- Před zahájením instalace aktualizace 1805, spusťte [Test AzureStack](azure-stack-diagnostic-test.md) ověřit stav zásobník Azure a vyřešte všechny provozní problémy najít. Také zkontrolujte aktivní výstrahy a vyřešte všechny, které vyžadují nějakou akci. 

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace   
- Během instalace aktualizace 1805, může se zobrazit výstrahy s nadpis *chyba – šablona pro FaultType UserAccounts.New chybí.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se automaticky zavře po dokončení aktualizace 1805.   

- <!-- 2489559 - IS --> Nepokoušejte se vytvořit virtuální počítače při instalaci této aktualizace. Další informace o správě aktualizací, seSe [správě aktualizací v přehledu Azure zásobníku](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Postup po aktualizaci
Po instalaci 1805 nainstalujte všechny použitelné opravy hotfix. Další informace naleznete v následujících článcích znalostní báze knowledge base, a také naše [obsluhy zásad](azure-stack-servicing-policy.md).  
 - [KB 4340474 - oprava Hotfix Azure zásobníku 1.1805.4.53](https://support.microsoft.com/en-us/help/4340474).


## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Dále jsou po instalaci Známé problémy pro tuto verzi sestavení.

### <a name="portal"></a>Portál  
- <!-- 2551834 - IS, ASDK --> Když vyberete **přehled** pro účet úložiště na portály na správce nebo uživatele, informace z *Essentials* podokně nezobrazí.  V podokně Essentials se zobrazí informace o účtu jako jeho *skupiny prostředků*, *umístění*, a *ID předplatného*.  Další možnosti pro přehled jsou dostupné, jako je *služby* a *monitorování*, stejně jako možnosti k *otevřít v Průzkumníkovi* nebo *odstranit účet úložiště* . 

  Chcete-li zobrazit informace k dispozici, použijte [Get azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) rutiny prostředí PowerShell. 

- <!-- 2551834 - IS, ASDK --> Když vyberete **značky** pro účet úložiště na portály na správce nebo uživatele informace nepodaří načíst a nezobrazí.  

  Chcete-li zobrazit informace k dispozici, použijte [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) rutiny prostředí PowerShell.


- <!-- 2332636 - IS -->  Při použití služby AD FS pro systém identit Azure zásobníku a aktualizace na tuto verzi Azure zásobníku, výchozí vlastník předplatného výchozí zprostředkovatel se resetuje do vestavěné **CloudAdmin** uživatele.  
  Alternativní řešení: Chcete-li tento problém vyřešit, po instalaci této aktualizace, použijte krok 3 ze [automation aktivační události. ke konfiguraci vztahu důvěryhodnosti zprostředkovatele v zásobníku Azure deklarací](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) postup resetovat vlastníka předplatného výchozího zprostředkovatele.   

- <!-- TBD - IS ASDK --> Některé typy pro správu předplatného nejsou k dispozici.  Když upgradujete zásobník Azure na tuto verzi, typy dvě předplatného, které byly [představený poprvé ve verzi 1804](azure-stack-update-1804.md#new-features) nejsou viditelné v konzole. To se očekává. Typy předplatné není k dispozici jsou *měření předplatné*, a *spotřeba předplatné*. Tyto typy předplatného jsou viditelné v nové prostředí zásobníku Azure od verze 1804 ale ještě nejsou připravené k použití. By měly být nadále používat *výchozí zprostředkovatel* typ předplatného.  

- <!-- 2403291 - IS ASDK --> Nemusí mít použití vodorovného posuvníku podél dolního okraje portálů správců a uživatelů. Pokud máte přístup k vodorovného posuvníku, pomocí popisu cesty a přejděte do předchozího okna portálu výběrem název okna je chcete zobrazit ze seznamu s popisem cesty, které se nachází v horní levé části portálu.
  ![Popis cesty](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Nemusí být možné zobrazit úložiště nebo výpočetní prostředky na portálu správce. Příčinou tohoto problému je k chybě při instalaci aktualizace, která způsobí, že aktualizace, která se nesprávně hlásit jako úspěšné. Pokud chcete tento problém opakuje, obraťte se na technickou podporu společnosti Microsoft pro pomoc.

- <!-- TBD - IS --> Může se zobrazit prázdný řídicí panel portálu. Chcete-li obnovit řídicí panel, vyberte ikonu ozubené kolečko v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.

- <!-- TBD - IS ASDK --> Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.

- <!-- TBD - IS ASDK --> Nelze zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení ověřte oprávnění pomocí prostředí PowerShell.


### <a name="health-and-monitoring"></a>Sledování stavu a
- <!-- 1264761 - IS ASDK -->  Může se zobrazit výstrahy *stavu řadiče* součásti, které mají následující podrobnosti:  

   Výstrahy #1:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu prezenčního signálu skener není k dispozici. To může ovlivnit sestavy o stavu a metrik.  

  Výstrahy #2:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu skener chyby není k dispozici. To může ovlivnit sestavy o stavu a metrik.

  Obě výstrahy můžete bezpečně ignorovat a bude automaticky zavřete tak, aby v čase.  

- <!-- 2368581 - IS. ASDK --> Operátor zásobník Azure, pokud se zobrazí upozornění na nedostatek paměti a klientské virtuální počítače se nepodařilo nasadit s *Chyba při vytváření virtuálních počítačů Fabric*, je možné, že Azure zásobníku razítko nedostatek dostupné paměti. Použití [Azure zásobníku Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) nejlépe pochopit dostupné kapacity pro zatížení. 


### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> Když vyberete velikost virtuálního počítače pro nasazení virtuálního počítače, některé velikosti virtuálních počítačů F-Series nejsou viditelné v rámci selektoru velikost, při vytváření virtuálního počítače. Následující velikosti virtuálních počítačů se nezobrazí v modulu pro výběr: *F8s_v2*, *F16s_v2*, *F32s_v2*, a *F64s_v2*.  
  Jako řešení použijte jednu z následujících metod nasazení virtuálního počítače. V každé metody je třeba zadat velikost virtuálního počítače, který chcete použít.

  - **Šablona Azure Resource Manageru:** při použití šablony, nastavte *vmSize* v šabloně na velikost virtuálního počítače, kterou chcete použít. Například následující položku slouží k nasazení virtuálního počítače, který používá *F32s_v2* velikost:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** můžete použít [vytvořit virtuální počítač az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) příkaz a zadejte velikost virtuálního počítače jako parametr, podobně jako `--size "Standard_F32s_v2"`.

  - **Prostředí PowerShell:** pomocí prostředí PowerShell můžete použít [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) s parametrem, který určuje velikost virtuálního počítače, podobně jako `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

- <!-- TBD - IS --> Když vytvoříte sadu na portálu přejděte na dostupnosti **nový** > **výpočetní** > **sadu dostupnosti**, můžete vytvořit pouze skupiny dostupnosti s doména selhání a aktualizace domény 1. Jako řešení, při vytváření nového virtuálního počítače, vytvořte skupinu dostupnosti pomocí prostředí PowerShell, rozhraní příkazového řádku, nebo v portálu.

- <!-- TBD - IS ASDK --> Když vytvoříte virtuální počítače na portálu pro uživatele Azure zásobníku, portál zobrazí nesprávný počet datových disků, které můžete připojit k řady DS virtuálních počítačů. Řady DS virtuální počítače zvládne tolik datových disků jako konfiguraci Azure.

- <!-- TBD - IS ASDK --> Když image virtuálního počítače se nepodaří vytvořit, položku se nezdařilo, nelze odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

- <!-- TBD - IS ASDK --> Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování časový limit namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

- <!-- 1662991 IS ASDK --> Diagnostika virtuálních počítačů Linux není podporována v zásobníku Azure. Při nasazení virtuálního počítače s Linuxem pomocí diagnostiky virtuálních počítačů, které jsou povolené, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem pomocí nastavení pro diagnostiku.  


### <a name="networking"></a>Sítě
- <!-- TBD - IS ASDK --> Trasy definované uživatelem nelze vytvořit buď portálu správce nebo uživatele. Jako alternativní řešení použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS ASDK --> V části **sítě**, pokud kliknete na tlačítko **vytvořit bránu VPN** nastavit připojení k síti VPN **na základě zásad** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **na základě trasy** možnost je podporována v zásobníku Azure.

- <!-- 2388980 - IS ASDK --> Po vytvoření virtuálního počítače a související s veřejnou IP adresu, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení pravděpodobně fungovat, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původní virtuální počítač a ne do nového.

- <!-- 2292271 - IS ASDK --> Pokud zvýšíte limit kvóty pro síťového prostředku, který je součástí služby nabídka a plán, který je přidružený k předplatnému klienta, nový limit neplatí pro toto předplatné. Nový limit však platí pro nové odběry, které jsou vytvořeny po kvótu vzroste.

  Chcete-li tento problém obejít, použijte plán rozšíření zvýšit kvótu sítě, pokud je již přidružena k odběru plánu. Další informace najdete v tématu Jak [zpřístupnit plán rozšíření](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nelze odstranit odběr, který má zónu DNS prostředky nebo prostředky směrovací tabulku s ním spojená. Úspěšně odstraňte odběr, musíte nejprve odstranit zónu DNS a směrovací tabulku prostředky z předplatného klienta.


- <!-- 1902460 - IS ASDK --> Azure zásobníku podporuje jediný *brány místní sítě* podle IP adresy. To platí ve všech předplatných klienta. Po vytvoření první místní síťové brány připojení, další pokusy o vytvoření prostředku brány místní sítě se stejnou adresou IP jsou blokovány.

- <!-- 16309153 - IS ASDK --> Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna na vlastní selhání serveru DNS. Aktualizované nastavení nejsou nabídnutých do virtuálních počítačů v této virtuální sítě.

- <!-- TBD - IS ASDK --> Azure zásobník nepodporuje přidávání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.

- <!-- 2096388 IS --> Portál pro správu nelze použít k aktualizaci pravidla pro skupinu zabezpečení sítě.

    Alternativní řešení pro službu App Service: Pokud potřebujete vzdálenou plochu instance řadiče, můžete upravit pravidla zabezpečení v rámci skupiny zabezpečení sítě pomocí prostředí PowerShell.  Následují příklady *povolit*a potom obnovte konfiguraci *Odepřít*:  

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

- <!-- TBD - IS --> Vytvoření položek na serverech, že hostitel SQL nebo MySQL je podporována pouze poskytovatele prostředků. Položky vytvořené na hostitelském serveru, které nebyly vytvořeny pomocí poskytovatele prostředků může mít za následek neodpovídající stavu.  

- <!-- IS, ASDK --> Speciální znaky, včetně mezery a tečky, nejsou podporovány v **rodiny** nebo **vrstvy** názvy při vytváření SKU pro poskytovatele prostředků SQL a databáze MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Po provedení aktualizace na 1805 zásobník Azure, můžete nadále používat zprostředkovatele prostředků SQL a MySQL, které jste předtím nasadili.  Doporučujeme, abyste že aktualizujete SQL a MySQL, když je dostupná nová verze. Zásobník Azure, jako je aktualizace pro poskytovatele prostředků SQL a MySQL postupně. Pokud používáte verzi 1803, nejprve použít verzi 1804 a potom aktualizovat 1805.      
>   
> Instalace aktualizace 1805 nemá vliv na aktuální použití funkce SQL nebo MySQL poskytovatelů prostředků, které vaši uživatelé.
> Bez ohledu na verzi poskytovateli prostředků, které používáte vaše data uživatelů v své databáze není dotyku a zůstávají dostupné.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- <!-- 2489178 - IS ASDK --> Aby bylo možné škálovat infrastrukturu (pracovních procesů, správy, front-end rolí), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočet.

- <!-- TBD - IS ASDK --> Aplikace služby lze nasadit pouze do *výchozí zprostředkovatel odběru* v tuto chvíli. V budoucí aktualizaci, bude do nové nasazení služby App Service *měření předplatné* která byla zavedena v zásobníku 1804 Azure. Při měření podporuje pro použití, všechna existující nasazení budou migrovány tento nový typ odběru.


### <a name="usage"></a>Využití  
- <!-- TBD - IS ASDK --> Využití veřejné IP adresy využití měření data zobrazují stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítka, který ukazuje vytvoření záznamu. Tato data v současné době nelze použít pro monitorování přesné veřejnou IP adresu využití.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Stažení aktualizace
Můžete si stáhnout balíček aktualizace 1805 zásobník Azure z [zde](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Další informace najdete v tématech
- Použití privilegovaných koncového bodu (období) ke sledování a obnovit aktualizací naleznete v části [monitorování aktualizací v zásobníku Azure pomocí privilegované koncový bod](azure-stack-monitor-update.md).
- Přehled správy aktualizací v zásobníku Azure najdete v tématu [správě aktualizací v přehledu Azure zásobníku](azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace se zásobníkem Azure najdete v tématu [aktualizace v zásobníku Azure](azure-stack-apply-updates.md).
