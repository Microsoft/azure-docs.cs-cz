---
title: "Poznámky k verzi Microsoft Azure zásobníku Development Kit | Microsoft Docs"
description: "Vylepšení, opravy a známé problémy pro Azure zásobníku Development Kit."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 176b850120958a5ca5fdaece4831e2ed27ac0a04
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Poznámky k verzi Azure zásobníku Development Kit
Tyto poznámky k verzi obsahují informace o vylepšení, opravy a známých problémů v Azure zásobníku Development Kit. Pokud si nejste jistí, kterou verzi používáte, můžete [použití portálu ke kontrole](.\.\azure-stack-updates.md#determine-the-current-version).

> Nejnovější informace s co je nového v ASDK se přihlásíte k odběru [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanálu](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-201803021"></a>Sestavení 20180302.1

### <a name="new-features-and-fixes"></a>Nové funkce a opravy
Najdete v článku [nové funkce a opravy](.\.\azure-stack-update-1802.md#new-features-and-fixes) části k vydání verze update 1802 zásobník Azure pro Azure zásobníku integrované systémy.

> [!IMPORTANT]    
> Některé z uvedených v položek **nové funkce a opravy** části se vztahují pouze k Azure zásobníku integrované systémy.


### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro Azure zásobníku Development Kit použít https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Když máte zobrazeny vlastnosti prostředku nebo skupinu prostředků, **přesunout** tlačítko k dispozici. Toto chování je očekávané. Přesun prostředků nebo skupiny prostředků mezi skupinami prostředků nebo předplatného není aktuálně podporován.
 
- Zobrazí **je vyžadována aktivace** upozornění s výzvou k registraci vaší Azure zásobníku Development Kit. Toto chování je očekávané.

- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.

- Nelze zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení ověřte oprávnění pomocí prostředí PowerShell.

- V řídicím panelu portálu pro správu se nezdaří aktualizace dlaždice zobrazíte informace o aktualizacích. Chcete-li vyřešit tento problém, klikněte na dlaždici jej aktualizovat.

-   V portálu pro správu může se zobrazit Kritická výstraha pro součást Microsoft.Update.Admin. Název výstrahy, popis a nápravu všech zobrazit jako:  
    - *Chyba: šablonu pro FaultType ResourceProviderTimeout nebyl nalezen.*

    Tuto výstrahu můžete ignorovat. 

#### <a name="health-and-monitoring"></a>Sledování stavu a
Na portálu správy Azure zásobníku může zobrazit kritickou výstrahu s názvem **čekající na vyřízení vypršení platnosti certifikátu externí**.  Tuto výstrahu můžete ignorovat a ovlivnit operace Azure zásobníku Development Kit. 


#### <a name="marketplace"></a>Marketplace
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.
 
#### <a name="compute"></a>Compute
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

- Azure zásobníku podporuje používání pouze pevný typ virtuální pevné disky. Některé bitové kopie nabízeným přes marketplace v zásobníku Azure používají dynamické virtuální pevné disky, ale těch, které byly odebrány. Změna velikosti virtuálního počítače (VM) s dynamický disk, který je připojený ponechá virtuální počítač ve stavu selhání.

  Ke zmírnění tohoto problému, odstraňte virtuální počítač bez odstranění disku Virtuálního počítače, objekt blob souboru VHD v účtu úložiště. Potom převést virtuální pevný disk na dynamický disk na pevný disk a potom je znovu vytvořit virtuální počítač.

- Když vytvoříte virtuální počítače na portálu pro uživatele Azure zásobníku, portál zobrazí nesprávný počet datových disků, které můžete připojit k řady DS virtuálních počítačů. Řady DS virtuální počítače zvládne tolik datových disků jako konfiguraci Azure.

- Když image virtuálního počítače se nepodaří vytvořit, položku se nezdařilo, nelze odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

-  Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování časový limit namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Sítě
- V části **sítě**, pokud kliknete na tlačítko **připojení** nastavit připojení k síti VPN **VNet-to-VNet** je uveden jako typ možné připojení. Nevybírejte tuto možnost. V současné době pouze **Site-to-site (IPsec)** možnost je podporována.

- Po vytvoření virtuálního počítače a související s veřejnou IP adresu, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení pravděpodobně fungovat, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původně přidružený virtuální počítač a ne do nového.

- Interní Vyrovnávání zatížení (ILB) nesprávně zpracovává adresy MAC u virtuálních počítačů v back-end, což způsobí, že ILB pro přerušení při použití instance systému Linux v síti Back-End.  ILB funguje bez problémů s instancí systému Windows na Back-endovou síť.

-   Funkce předávání IP adres se zobrazí na portálu, ale povolení předávání IP adres nemá žádný vliv. Tato funkce ještě není podporována.

- Azure zásobníku podporuje jediný *brány místní sítě* podle IP adresy. To platí ve všech předplatných klienta. Po vytvoření první místní síťové brány připojení, další pokusy o vytvoření prostředku brány místní sítě se stejnou adresou IP jsou blokovány.

- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna na vlastní selhání serveru DNS. Aktualizované nastavení nejsou nabídnutých do virtuálních počítačů v této virtuální sítě.
 
- Azure zásobník nepodporuje přidávání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Alternativní řešení pro službu App Service: Pokud potřebujete vzdálenou plochu instance řadiče, můžete upravit pravidla zabezpečení v rámci skupiny zabezpečení sítě pomocí prostředí PowerShell.  Následují příklady *povolit*a potom obnovte konfiguraci *Odepřít*: 
    
    - *Povolte:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
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
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
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
- Může trvat až jednu hodinu, než uživatelé mohou vytvářet databází v nové SQL nebo MySQL SKU.

- Databázi hostitelské servery musí být vyhrazená pro použití prostředků zprostředkovatele a uživatelského úlohami. Nelze použít instanci, která se používá jakékoli další příjemce, včetně aplikační služby.

#### <a name="app-service"></a>App Service
- Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- Aby bylo možné škálovat infrastrukturu (pracovních procesů, správy, front-end rolí), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočet.
 
#### <a name="usage-and-billing"></a>Využití a fakturace
- Veřejná data měření využití IP adres zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítka, který ukazuje vytvoření záznamu. Tato data v současné době nelze použít pro monitorování přesné veřejnou IP adresu využití.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Stažení nástroje Azure zásobníku z Githubu
- Při použití *vyvolání webrequest* rutiny prostředí PowerShell ke stažení protokolů Azure nástroje z Githubu, obdržíte chybu:     
    -  *vyvolání webrequest: požadavek byl přerušen: Nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu poslední zastaralá podpora Githubu Tlsv1 a Tlsv1.1 kryptografických standardů (výchozí nastavení pro prostředí PowerShell). Další informace najdete v tématu [slabé kryptografické standardy odebrání oznámení](https://githubengineering.com/crypto-removal-notice/).

  Chcete-li vyřešit tento problém, přidejte `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` do horní části skript, který chcete vynutit konzole PowerShell pro stahování z úložiště Githubu TLSv1.2.


## <a name="build-201801032"></a>Sestavení 20180103.2

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

- Najdete v článku [nové funkce a opravy](.\.\azure-stack-update-1712.md#new-features-and-fixes) části k vydání verze update 1712 zásobník Azure pro Azure zásobníku integrované systémy.

    > [!IMPORTANT]
    > Některé z uvedených v položek **nové funkce a opravy** části se vztahují pouze k Azure zásobníku integrované systémy.

### <a name="known-issues"></a>Známé problémy
 
#### <a name="deployment"></a>Nasazení
- Čas serveru podle IP adresy musí zadat během nasazování.

#### <a name="infrastructure-management"></a>Správu infrastruktury
- Nepovolujte infrastruktura zálohování na **infrastruktura zálohování** okno.
- Baseboard management controller, (BMC) IP adresu a model se nezobrazí v uzlu jednotky škálování základní informace. Toto chování se očekává v Azure zásobníku Development Kit.

#### <a name="portal"></a>Portál
- Může se zobrazit prázdný řídicí panel portálu. Chcete-li obnovit řídicí panel, vyberte ikonu ozubené kolečko v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.
- Když máte zobrazeny vlastnosti skupiny prostředků, **přesunout** tlačítko k dispozici. Toto chování je očekávané. Přesunutí skupin prostředků mezi předplatnými není aktuálně podporován.
-  Pro jakýkoli pracovní postup, kde v rozevíracím seznamu vyberte předplatné, skupinu prostředků nebo umístění může zaznamenat jeden nebo více z následujících důvodů:

   - Může se zobrazit prázdný řádek v horní části seznamu. By měl mít pořád povolený výběr položky podle očekávání.
   - Pokud je seznam položek v rozevíracím seznamu krátký, asi nebudete moci zobrazit některé názvy položek.
   - Pokud máte více předplatných uživatele, rozevíracího seznamu skupiny prostředků může být prázdná. 

   Obejít poslední dva problémy, můžete zadat název předplatné nebo skupinu prostředků (pokud ho znáte), nebo můžete místo toho použít PowerShell.

- Zobrazí se **je vyžadována aktivace** upozornění s výzvou k registraci vaší Azure zásobníku Development Kit. Toto chování je očekávané.
- Pokud **součást** po kliknutí na odkaz z libovolného **Role infrastruktury** výstrahy, výsledná **přehled** okno se pokusí načíst a selže. Kromě toho ** přehled ** okno nemá vypršení časového limitu.
- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.
- Nemůžete se moci zobrazit oprávnění k předplatnému pomocí portálů zásobník Azure. Jako alternativní řešení můžete ověřit oprávnění pomocí prostředí PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Některé položky marketplace jsou odebírána v této verzi z důvodu kompatibility. Toto bude znovu povolit po další ověření.
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.
 
#### <a name="compute"></a>Compute
- Uživatelé mají možnost vytvoření virtuálního počítače pomocí geograficky redundantní úložiště. Tato konfigurace způsobuje, že vytvoření virtuálního počítače k selhání. 
- Můžete nakonfigurovat dostupnost virtuálního počítače nastavit pouze u domény selhání jednoho a doméně aktualizace jednoho.
- Neexistuje žádné zkušenosti marketplace pro vytvoření sady škálování virtuálního počítače. Můžete vytvořit škálování nastavit pomocí šablony.
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

#### <a name="networking"></a>Sítě
- Nástroj pro vyrovnávání zatížení s veřejnou IP adresu nelze vytvořit pomocí portálu. Jako alternativní řešení můžete použít PowerShell k vytvoření nástroje pro vyrovnávání zatížení.
- Když vytvoříte Vyrovnávání zatížení sítě, musíte vytvořit pravidlo překladu adres sítě. Pokud to neuděláte, obdržíte chybu při pokusu přidat pravidlo NAT po vytvoření nástroje pro vyrovnávání zatížení.
- V části **sítě**, pokud kliknete na tlačítko **připojení** nastavit připojení k síti VPN **VNet-to-VNet** je uveden jako typ možné připojení. Nevybírejte tuto možnost. V současné době pouze **Site-to-site (IPsec)** možnost je podporována.
- Veřejnou IP adresu z virtuálního počítače (VM) nelze zrušit přidružení, po virtuálního počítače byla vytvořena a související s touto adresou IP. Zrušení přidružení se zobrazí postup, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu. K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původně přidružený virtuální počítač a ne do nového. V současné době je nutné pouze použít nové veřejné IP adresy pro vytvoření nového virtuálního počítače.
- Operátory Azure zásobníku může nelze nasadit, odstraňovat, upravovat virtuální sítě nebo skupiny zabezpečení sítě. Tento problém je primárně zobrazit na následné aktualizace pokusy stejného balíčku. To je způsobeno problémem balení s aktualizaci, která je aktuálně šetření.
- Interní Vyrovnávání zatížení (ILB) nesprávně zpracovává adresy MAC pro virtuální počítače back-end, které dělí instance systému Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- To může trvat až jednu hodinu, než klienti databáze můžete vytvářet v nové SQL nebo MySQL SKU. 
- Vytvoření položky přímo na SQL a MySQL hostitelské servery, které nejsou prováděné poskytovatelem prostředků není podporována a může mít za následek neodpovídající stavu.

#### <a name="app-service"></a>App Service
- Uživatel musí zaregistrovat zprostředkovatele prostředku úložiště dřív, než vytvoří jejich první funkce Azure v rámci předplatného.
 
#### <a name="usage-and-billing"></a>Využití a fakturace
- Veřejná data měření využití IP adres zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítka, který ukazuje vytvoření záznamu. Tato data v současné době nelze použít pro monitorování přesné veřejnou IP adresu využití.

#### <a name="identity"></a>Identita

V Azure Active Directory Federation Services (ADFS) nasazené prostředí, **azurestack\azurestackadmin** účet už není vlastníkem předplatného výchozí zprostředkovatel. Místo protokolování do **portál pro správu nebo koncový bod adminmanagement** s **azurestack\azurestackadmin**, můžete použít **azurestack\cloudadmin** účet, takže která spravujete a používáte výchozí zprostředkovatel odběru.

> [!IMPORTANT]
> I v **azurestack\cloudadmin** účet je vlastníkem předplatného poskytovatele výchozí v prostředí služby AD FS nasadit, nemá oprávnění pro připojení RDP na hostiteli. Nadále používat **azurestack\azurestackadmin** účet nebo účet místního správce pro přihlášení, přístup a spravovat hostitele, podle potřeby.


## <a name="build-201711221"></a>Sestavení 20171122.1

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

- Najdete v článku [nové funkce a opravy](.\.\azure-stack-update-1711.md#new-features-and-fixes) části k vydání verze update 1711 zásobník Azure pro Azure zásobníku integrované systémy.

    > [!IMPORTANT]
    > Některé z uvedených v položek **nové funkce a opravy** části se vztahují pouze k Azure zásobníku integrované systémy.

### <a name="known-issues"></a>Známé problémy
 
#### <a name="deployment"></a>Nasazení
- Čas serveru podle IP adresy musí zadat během nasazování.

#### <a name="infrastructure-management"></a>Správu infrastruktury
- Nepovolujte infrastruktura zálohování na **infrastruktura zálohování** okno.
- Baseboard management controller, (BMC) IP adresu a model se nezobrazí v uzlu jednotky škálování základní informace. Toto chování se očekává v Azure zásobníku Development Kit.

#### <a name="portal"></a>Portál
- Může se zobrazit prázdný řídicí panel portálu. Chcete-li obnovit řídicí panel, vyberte ikonu ozubené kolečko v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.
- Když máte zobrazeny vlastnosti skupiny prostředků, **přesunout** tlačítko k dispozici. Toto chování je očekávané. Přesunutí skupin prostředků mezi předplatnými není aktuálně podporován.
-  Pro jakýkoli pracovní postup, kde v rozevíracím seznamu vyberte předplatné, skupinu prostředků nebo umístění může zaznamenat jeden nebo více z následujících důvodů:

   - Může se zobrazit prázdný řádek v horní části seznamu. By měl mít pořád povolený výběr položky podle očekávání.
   - Pokud je seznam položek v rozevíracím seznamu krátký, asi nebudete moci zobrazit některé názvy položek.
   - Pokud máte více předplatných uživatele, rozevíracího seznamu skupiny prostředků může být prázdná. 

   Obejít poslední dva problémy, můžete zadat název předplatné nebo skupinu prostředků (pokud ho znáte), nebo můžete místo toho použít PowerShell.

- Zobrazí se **je vyžadována aktivace** upozornění s výzvou k registraci vaší Azure zásobníku Development Kit. Toto chování je očekávané.
- Pokud **součást** po kliknutí na odkaz z libovolného **Role infrastruktury** výstrahy, výsledná **přehled** okno se pokusí načíst a selže. Kromě toho **přehled** okno nemá vypršení časového limitu.
- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.
- Nemůžete se moci zobrazit oprávnění k předplatnému pomocí portálů zásobník Azure. Jako alternativní řešení můžete ověřit oprávnění pomocí prostředí PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Při pokusu o přidání položky do zásobníku Azure marketplace s použitím **přidat z Azure** možnost, všechny položky můžou být vidět ke stažení.
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.
 
#### <a name="compute"></a>Compute
- Uživatelé mají možnost vytvoření virtuálního počítače pomocí geograficky redundantní úložiště. Tato konfigurace způsobuje, že vytvoření virtuálního počítače k selhání. 
- Můžete nakonfigurovat dostupnost virtuálního počítače nastavit pouze u domény selhání jednoho a doméně aktualizace jednoho.
- Neexistuje žádné zkušenosti marketplace pro vytvoření sady škálování virtuálního počítače. Můžete vytvořit škálování nastavit pomocí šablony.
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

#### <a name="networking"></a>Sítě
- Nástroj pro vyrovnávání zatížení s veřejnou IP adresu nelze vytvořit pomocí portálu. Jako alternativní řešení můžete použít PowerShell k vytvoření nástroje pro vyrovnávání zatížení.
- Když vytvoříte Vyrovnávání zatížení sítě, musíte vytvořit pravidlo překladu adres sítě. Pokud to neuděláte, obdržíte chybu při pokusu přidat pravidlo NAT po vytvoření nástroje pro vyrovnávání zatížení.
- V části **sítě**, pokud kliknete na tlačítko **připojení** nastavit připojení k síti VPN **VNet-to-VNet** je uveden jako typ možné připojení. Nevybírejte tuto možnost. V současné době pouze **Site-to-site (IPsec)** možnost je podporována.
- Veřejnou IP adresu z virtuálního počítače (VM) nelze zrušit přidružení, po virtuálního počítače byla vytvořena a související s touto adresou IP. Zrušení přidružení se zobrazí postup, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu. K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původně přidružený virtuální počítač a ne do nového. V současné době je nutné pouze použít nové veřejné IP adresy pro vytvoření nového virtuálního počítače.
- Operátory Azure zásobníku může nelze nasadit, odstraňovat, upravovat virtuální sítě nebo skupiny zabezpečení sítě. Tento problém je primárně zobrazit na následné aktualizace pokusy stejného balíčku. To je způsobeno problémem balení s aktualizaci, která je aktuálně šetření.
- Interní Vyrovnávání zatížení (ILB) nesprávně zpracovává adresy MAC pro virtuální počítače back-end, které dělí instance systému Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- To může trvat až jednu hodinu, než klienti databáze můžete vytvářet v nové SQL nebo MySQL SKU. 
- Vytvoření položky přímo na SQL a MySQL hostitelské servery, které nejsou prováděné poskytovatelem prostředků není podporována a může mít za následek neodpovídající stavu.

    > [!NOTE]
    > V tématu jednotlivých [SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) a [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) nastavit články pokyny verze kompatibility.

#### <a name="app-service"></a>App Service
- Uživatel musí zaregistrovat zprostředkovatele prostředku úložiště dřív, než vytvoří jejich první funkce Azure v rámci předplatného.
 
#### <a name="usage-and-billing"></a>Využití a fakturace
- Veřejná data měření využití IP adres zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítka, který ukazuje vytvoření záznamu. Tato data v současné době nelze použít pro monitorování přesné veřejnou IP adresu využití.

#### <a name="identity"></a>Identita

V Azure Active Directory Federation Services (ADFS) nasazené prostředí, **azurestack\azurestackadmin** účet už není vlastníkem předplatného výchozí zprostředkovatel. Místo protokolování do **portál pro správu nebo koncový bod adminmanagement** s **azurestack\azurestackadmin**, můžete použít **azurestack\cloudadmin** účet, takže která spravujete a používáte výchozí zprostředkovatel odběru.

> [!IMPORTANT]
> I v **azurestack\cloudadmin** účet je vlastníkem předplatného poskytovatele výchozí v prostředí služby AD FS nasadit, nemá oprávnění pro připojení RDP na hostiteli. Nadále používat **azurestack\azurestackadmin** účet nebo účet místního správce pro přihlášení, přístup a spravovat hostitele, podle potřeby.

