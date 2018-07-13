---
title: Aktualizace služby Azure Stack 1803 | Dokumentace Microsoftu
description: Další informace o tom, co je ve verzi 1803 aktualizace pro Azure Stack integrované systémy, známé problémy a kde se stáhnout aktualizaci.
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
ms.openlocfilehash: 11430a0d194a722c0c0520c936db3c08b1a6b863
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989567"
---
# <a name="azure-stack-1803-update"></a>Aktualizace služby Azure Stack 1803

*Platí pro: integrované systémy Azure Stack*

Tento článek popisuje vylepšení a opravy v balíčku aktualizace 1803, známé problémy pro tuto verzi a kde se stáhnout aktualizaci. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu    
Číslo sestavení aktualizace Azure Stack 1803 je **20180329.1**.


## <a name="before-you-begin"></a>Než začnete    
> [!IMPORTANT]    
> Nepokoušejte se vytvářet virtuální počítače při instalaci této aktualizace. Další informace o správě aktualizací najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Požadavky
- Instalace služby Azure Stack [aktualizace 1802](azure-stack-update-1802.md) před instalací aktualizace Azure Stack 1803.   

- Nainstalujte **opravu AzS Hotfix – 1.0.180312.1-sestavení 20180222.2** před instalací aktualizace Azure Stack 1803. Tato oprava hotfix aktualizace programu Windows Defender a je k dispozici při stahování aktualizací pro Azure Stack.

  Pokud chcete nainstalovat opravu hotfix, postupujte podle běžné postupy pro [instalace aktualizací pro Azure Stack](azure-stack-apply-updates.md). Název aktualizace se zobrazí jako **opravu AzS Hotfix – 1.0.180312.1**a obsahuje následující soubory: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Po odeslání těchto souborů do účtu úložiště a kontejner, spusťte instalaci z aktualizovat dlaždici v portálu pro správu. 
  
  Na rozdíl od aktualizace do služby Azure Stack instalace této aktualizace se nezmění na verzi služby Azure Stack. Pokud chcete potvrdit, tuto aktualizaci nenainstalujete, zobrazí se seznam **nainstalované aktualizace**.



### <a name="new-features"></a>Nové funkce 
Tato aktualizace zahrnuje následující vylepšení a oprav pro službu Azure Stack.

- **Aktualizace služby Azure Stack tajných klíčů** – (pro účty a certifikáty). Další informace o správě tajných kódů, najdete v části [otočit tajné kódy ve službě Azure Stack](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatické přesměrování na HTTPS** při přístupu na portálech správce a uživatele pomocí protokolu HTTP. Toto vylepšení byla provedena na základě [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) zpětná vazba pro Azure Stack. 

- <!-- 2202621  --> **Přístup na webu Marketplace** – teď můžete otevřít na webu Marketplace Azure Stack pomocí [+ nová](https://ms.portal.azure.com/#create/hub) možnost v rámci portály správce a uživatele stejným způsobem jako v Azure Portal.
 
- <!-- 2202621 --> **Azure Monitor** – přidá Azure Stack [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) pro správce a uživatele portálů. To zahrnuje nové průzkumníci pro metrik a protokolů aktivit. Pro přístup k této Azure Monitor z externích sítí, port **13012** musí být otevřený v konfiguraci brány firewall. Další informace o porty vyžadované službou Azure Stack najdete v tématu [integrace datových center Azure Stack – publikování koncových bodů](azure-stack-integrate-endpoints.md).

   Také jako součást tohoto změnit, v části **další služby**, *protokoly auditu* se teď zobrazí jako *protokoly aktivit*. Funkce je teď konzistentní vzhledem k aplikacím pomocí webu Azure portal. 

- <!-- 1664791 --> **Zhuštěné soubory** – při přidání nové image do služby Azure Stack, nebo přidat bitovou kopii prostřednictvím webu marketplace syndikace image je převeden do zhuštěného souboru. Bitové kopie, které byly přidány před použitím služby Azure Stack verze 1803 nelze převést. Místo toho musíte použít syndikace marketplace se znovu spustit těchto imagí, abyste mohli využít tuto funkci. 
 
   Zhuštěné soubory jsou efektivní formátu použít ke snížení využití prostoru úložiště a zlepšit vstupně-výstupních operací.  Další informace najdete v tématu [Fsutil zhuštěné](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) pro systém Windows Server. 

### <a name="fixed-issues"></a>Opravené problémy

- <!-- 1739988 --> Interní Vyrovnávání zatížení (ILB) nyní správně zpracovává adresy MAC pro virtuální počítače s back-end, což způsobí, že nástroje pro vyrovnávání zatížení při použití Linuxu instancí na back endovou síť vyřadit pakety do back endovou síť. ILB funguje bez problémů s instancí Windows na back endovou síť. 

- <!-- 1805496 --> Chyby, kde by se připojení VPN mezi Azure Stack odpojen z důvodu Azure Stack pomocí různých nastavení pro zásady protokolu IKE než Azure. Hodnoty SALifetime (čas) a SALiftetime (v bajtech) nejsou kompatibilní s Azure a jste změnili ve verzi 1803 tak, aby odpovídaly nastavení Azure. Hodnota pro SALifetime (v sekundách) před 1803 byla ve verzi 1803 14 400 fakturovatelných a nyní změny 27 000. Byla zadána hodnota pro SALifetime (v bajtech) před 1803 819,200 a změny ve verzi 1803 33,553,408.

- <!-- 2209262 --> Problém IP, kde bylo dříve vidět na portálu; připojení k síti VPN povolení a přepínání předávání IP adres ale nemá žádný vliv. Funkce je zapnutá ve výchozím nastavení a změnit to ještě není podporovaný.  Ovládací prvek byl odebrán z portálu. 

- <!-- 1766332 --> Azure Stack nepodporuje zásady na základě brány sítě VPN, přestože možnost se zobrazí na portálu.  Možnost byla odebrána z portálu. 

- <!-- 1868283 --> Azure Stack nyní brání, změna velikosti virtuálního počítače, který je vytvořen s dynamickými disky. 

- <!-- 1756324 --> Data o využití pro virtuální počítače je teď rozdělený v hodinových intervalech. To je konzistentní s Azure. 

- <!--  2253274 --> Problém kde portálech správce a uživatele v okně Nastavení podsítě virtuální sítě se nepodaří načíst. Jako alternativní řešení, pomocí prostředí PowerShell a [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) rutina pro zobrazení a správa těchto informací.

- Když vytvoříte virtuální počítač, zprávu *nejde zobrazit ceny* už se zobrazí při výběru velikosti pro velikost virtuálního počítače.

- Různé opravy pro výkon, stability, zabezpečení a operační systém, který se používá ve službě Azure Stack.


### <a name="changes"></a>Změny
- Způsob, jak změnit stav nabídku na nově vytvořený z *privátní* k *veřejné* nebo *Vyřazená z provozu* došlo ke změně. Další informace najdete v tématu [vytvořte nabídku](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace    
<!-- 2328416 --> Během instalace aktualizace 1803 může být výpadek služby blob service a interních služeb, které používají službu blob service. To zahrnuje některé operace virtuálního počítače. Toto časové prodlevy může způsobit chyby tenanta operations Console nebo výstrahy ze služeb, které nemůžou přistupovat k datům. Tento problém vyřeší sám po dokončení instalace aktualizace. 



### <a name="post-update-steps"></a>Postup po aktualizaci
- Po dokončení instalace 1803 nainstalujte všechny příslušné opravy hotfix. Další informace naleznete následující články znalostní báze, stejně jako naše [zásady obsluhy](azure-stack-servicing-policy.md).

  - [KB 4344115 – oprava Hotfix Azure Stack 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Po instalaci této aktualizace, zkontrolujte konfiguraci brány firewall, aby [nezbytné porty](azure-stack-integrate-endpoints.md) jsou otevřené. Například tato aktualizace zavádí *Azure Monitor* což zahrnuje změnu protokolů auditu do protokolů aktivit. Díky této změně portu 13012 se teď používá a také je třeba otevřít.  


### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Toto jsou známé problémy po instalaci pro sestavení **20180323.2**.

#### <a name="portal"></a>Portál
- <!-- 2332636 - IS -->  Když používáte službu AD FS pro Azure Stack identitu systému a aktualizace pro tuto verzi sady Azure Stack, výchozí vlastník výchozí předplatné poskytovatele se resetují na předdefinované **CloudAdmin** uživatele.  
  Alternativní řešení: Chcete-li tento problém vyřešit, po instalaci této aktualizace, použijte krok 3 ze [deklarací aktivování automatizace ke konfiguraci vztahu důvěryhodnosti zprostředkovatele ve službě Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) postup resetovat vlastníka výchozí předplatné poskytovatele.   

- Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro službu Azure Stack integrované systémy, používat https://aka.ms/newsupportrequest.

- <!-- 2050709 --> V portálu pro správu není možné upravit metrik úložiště pro službu Blob service, služba Table service nebo služby front. Když účtu služby Storage a potom vyberte objekt blob, tabulky nebo fronty služby dlaždice, otevře se nové okno, která zobrazuje graf metrik za danou službu. Pokud pak vyberete Úpravy od horního okraje grafu dlaždice metrik, v okně upravit graf se otevře, ale nezobrazuje možnost upravit metriky.

- Nemusí být možné zobrazit na portálu správce prostředků výpočetního výkonu a úložiště. Příčinou tohoto problému je chyba při instalaci aktualizace, která způsobí, že aktualizace, která se správně hlášené jako úspěšně dokončený. Pokud k tomuto problému dochází, obraťte se na zákaznické podpory Microsoftu o pomoc.

- Může se zobrazit prázdný řídicí panel portálu. Obnovit řídicí panel, vyberte ikonu ozubeného kolečka v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.

- Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

- Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.

- Na řídicím panelu portálu pro správu aktualizovat dlaždici selže k zobrazení informací o aktualizacích. Chcete-li vyřešit tento problém, kliknutím na dlaždici ji aktualizovat.

- V portálu pro správu, může se zobrazit kritickou výstrahu pro *Microsoft.Update.Admin* komponenty. Název výstrahy, popis a nápravu všech zobrazit jako:  
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


#### <a name="marketplace"></a>Marketplace
- Uživatelům můžete přejít na kompletní nabídku marketplace bez předplatného a vidí správce různé věci, třeba plány a nabídky. Tyto položky jsou pro uživatele nefunkční.



#### <a name="compute"></a>Compute
- Nastavení škálování pro škálovací sady virtuálních počítačů nejsou k dispozici na portálu. Jako alternativní řešení můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdíly mezi verzemi prostředí PowerShell, je nutné použít `-Name` parametr namísto `-VMScaleSetName`.

- Při vytváření skupiny dostupnosti na portálu nastavit tak, že přejdete do **nový** > **Compute** > **dostupnosti**, můžete vytvořit pouze doména selhání a aktualizační doména 1 skupinu dostupnosti. Alternativním řešením je při vytváření nového virtuálního počítače, vytvořte dostupnosti pomocí Powershellu, rozhraní příkazového řádku, nebo v rámci portálu.

- Při vytváření virtuálních počítačů na portálu user portal Azure Stack na portálu zobrazuje nesprávný počet datových disků, které můžete připojit k virtuální počítač řady D series. Všechny podporované řady D series virtuálních počítačů může obsahovat libovolný počet datových disků jako konfiguraci Azure.

- Selhání vytvoření image virtuálního počítače neúspěšné položky, který se nedá odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení vytvořte nové image virtuálního počítače s fiktivní virtuálního pevného disku, který je možné vytvářet přes Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-oprava SizeBytes – 1 GB). Tento proces by měla vyřešit problém, který zabraňuje odstranění položky. Pak 15 minut po vytvoření fiktivní image můžete úspěšně ho odstranit.

  Potom můžete zkusit znovu stáhněte image virtuálního počítače, která původně selhala.

-  Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

- <!-- 1662991 --> Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.  


#### <a name="networking"></a>Sítě
- Po vytvoření virtuálního počítače a přidružené k veřejné IP adresy, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení přidružení zdánlivě fungovat, ale zůstane spojená s původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používaly pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že znovu přiřadit IP adresu do nového virtuálního počítače (obvykle označované jako *prohození virtuálních IP adres*). Všechny budoucí pokusy o připojení přes tuto IP adresu výsledkem připojení do původně přidružený virtuální počítač a ne do nového.



- Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.

- Azure Stack nepodporuje přidávání další síťová rozhraní k instanci virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.

- <!-- 2096388 --> Na portálu pro správu nelze použít k aktualizaci pravidel pro skupiny zabezpečení sítě. 

    Alternativní řešení pro službu App Service: Pokud je potřeba k instance Kontroleru pomocí vzdálené plochy, upravte pravidla zabezpečení v rámci skupiny zabezpečení sítě pomocí Powershellu.  Níže jsou příklady toho, jak *povolit*a potom obnovit konfiguraci tak, aby *Odepřít*:  
    
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
- Než budete pokračovat, přečtěte si také důležitou poznámku v [před zahájením](#before-you-begin) dříve v těchto poznámkách k verzi.

- Může trvat až jednu hodinu, než se uživatelé můžou vytvářet databáze v nové nasazení SQL nebo MySQL.

- Vytvoření položek na serverech, daný hostitel SQL nebo MySQL se podporuje jenom poskytovatele prostředků. Neodpovídající stavu může dojít položky vytvořené na hostitelském serveru, které nebyly vytvořeny pomocí zprostředkovatele prostředků.  

- <!-- IS, ASDK --> Speciální znaky, mezery a tečky, včetně nepodporuje **řady** název při vytváření SKU pro poskytovatele prostředků SQL nebo MySQL.

> [!NOTE]  
> Po aktualizaci na verzi 1803 Azure Stack, můžete dál používat poskytovatele prostředků SQL nebo MySQL, které jste předtím nasadili.  Doporučujeme, abyste že aktualizujete SQL nebo MySQL, až bude dostupná nová verze. Jako je Azure Stack použití aktualizací pro poskytovatele prostředků SQL nebo MySQL postupně.  Pokud používáte verzi 1711, nejprve použít verzi 1712 pak 1802 a následně neaktualizují na verzi 1803.      
>   
> Instalace aktualizace 1803 nemá vliv na aktuální použití funkce poskytovatele prostředků SQL nebo MySQL vašich uživatelů.
> Bez ohledu na verzi poskytovatele prostředků, které můžete použít vaše data uživatelů ve své databáze není dokázal a zůstala přístupná.    



#### <a name="app-service"></a>App Service
- Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- Aby bylo možné horizontální navýšení kapacity infrastruktury (pracovních procesů, správy, role front-endu), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.


#### <a name="usage"></a>Využití  
- Data o využití veřejné IP adresy využití měřičů zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítko, které ukazuje, kdy se záznam vytvořil. V současné době nelze pomocí těchto dat pro monitorování přesné použití veřejné IP adresy.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Stažení nástroje pro Azure Stack z Githubu
- Při použití *invoke-webrequest* rutina prostředí PowerShell pro stažení služby Azure Stack nástroje z Githubu, zobrazí chybová zpráva:     
    -  *Invoke-webrequest: požadavek byl přerušen: Nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu poslední vyřazení Githubu podporu protokolu Tlsv1 a Tlsv1.1 kryptografických standardů (výchozí nastavení pro prostředí PowerShell). Další informace najdete v tématu [slabé kryptografické standardy odebrání oznámení](https://githubengineering.com/crypto-removal-notice/).

  Chcete-li tento problém vyřešit, přidejte `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` do horní části souboru, který chcete vynutit konzole Powershellu použít TLSv1.2 při stahování z úložišť GitHub.


## <a name="download-the-update"></a>Stáhnout aktualizaci.
Můžete stáhnout aktualizace balíčku Azure Stack 1803 z [tady](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Další informace najdete v tématech
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).
- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
