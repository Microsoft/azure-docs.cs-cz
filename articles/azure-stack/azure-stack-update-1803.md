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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: a74e77f84aa70519015a589cbc6e7478c0c41592
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318805"
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

- Nainstalujte **opravu AzS Hotfix – 1.0.180312.1-sestavení 20180222.2** před instalací aktualizace 1803 zásobník Azure. Tato oprava hotfix aktualizace programu Windows Defender a je k dispozici při stahování aktualizací pro Azure zásobníku.

  Chcete-li nainstalovat opravu hotfix, postupujte podle běžné postupy pro [instalace aktualizací pro Azure zásobníku](azure-stack-apply-updates.md). Název aktualizace zobrazí jako **opravu AzS Hotfix – 1.0.180312.1**a obsahuje následující soubory: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Po nahrávání těchto souborů do účtu úložiště a kontejneru, spusťte instalaci z dlaždice aktualizace v portálu pro správu. 
  
  Na rozdíl od aktualizace zásobníku Azure instalací této aktualizace se nemění verzi Azure zásobníku. Pokud chcete potvrdit, instalaci této aktualizace, zobrazí se seznam **nainstalované aktualizace**.



### <a name="new-features"></a>Nové funkce 
Tato aktualizace zahrnuje následující vylepšení a opravy pro Azure zásobníku.

- **Aktualizace zásobníku Azure tajné klíče** – (účty a certifikáty). Další informace o správě tajných klíčů najdete v tématu [otočit tajné klíče v Azure zásobníku](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatické přesměrování na HTTPS** při použití protokolu HTTP pro přístup na portály správce a uživatele. Toto vylepšení byla provedena na základě [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) zpětnou vazbu pro Azure zásobníku. 

- <!-- 2202621  --> **Přístup na webu Marketplace** – nyní lze otevřít v Azure Marketplace zásobníku pomocí [+ nový](https://ms.portal.azure.com/#create/hub) možnost z v rámci portálů správců a uživatelů stejným způsobem jako v Azure portálů.
 
- <!-- 2202621 --> **Azure monitorování** – zásobník Azure přidává [Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) na portály správců a uživatelů. To zahrnuje nové průzkumníci pro protokoly metriky a aktivity. Toto monitorování Azure přístup z externí sítě, port **13012** musí být otevřený v konfiguraci brány firewall. Další informace o porty vyžadované službou Azure zásobníku najdete v tématu [zásobník Azure datacenter integrace - Publikování koncové body](azure-stack-integrate-endpoints.md).

   Také jako součást tohoto změnit, v části **další služby**, *protokoly auditu* nyní se zobrazí jako *protokoly aktivity*. Funkce je nyní konzistentní s portálem Azure. 

- <!-- 1664791 --> **Zhuštěné soubory** – když přidáte novou bitovou kopii do protokolů Azure nebo přidat bitovou kopii prostřednictvím marketplace syndikace bitovou kopii je převeden do zhuštěného souboru. Bitové kopie, které byly přidány před použitím Azure zásobníku verze 1803 nelze převést. Místo toho musíte použít marketplace syndikace odeslat znovu tyto bitové kopie využít této funkce. 
 
   Zhuštěné soubory jsou efektivní formátu použít ke snížení využití prostoru úložiště a zlepšit vstupně-výstupní operace.  Další informace najdete v tématu [Fsutil zhuštěných](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) pro systém Windows Server. 

### <a name="fixed-issues"></a>Opravené problémy

- <!-- 1739988 --> Interní Vyrovnávání zatížení (ILB) nyní správně zpracovává adresy MAC u virtuálních počítačů v back-end, což způsobí, že ILB odpojení pakety na back endovou síť při použití instance systému Linux na síť back-end. ILB funguje bez problémů s instancí systému Windows na back endovou síť. 

- <!-- 1805496 --> Problém kde by se připojení VPN mezi Azure zásobníku odpojeny z důvodu zásobník Azure pomocí různých nastavení pro IKE zásady než Azure. Hodnoty SALifetime (čas) a SALiftetime (bajty) není kompatibilní s Azure a změnily v 1803 tak, aby odpovídaly nastavení Azure. Hodnota SALifetime (v sekundách) před 1803 bylo v 1803 14 400 baudů a nyní změny 27 000. Hodnota pro SALifetime (bajty) před 1803 byla 819,200 a změny 33,553,408 v 1803.

- <!-- 2209262 --> Problém IP, kde je připojení k síti VPN dříve viditelné v portálu. povolení nebo nastavením předávání IP adres ale nemá žádný vliv. Tato funkce zapnutá ve výchozím nastavení a umožňuje změnit to není dosud podporován.  Ovládací prvek byla odebrána z portálu. 

- <!-- 1766332 --> Azure zásobník nepodporuje zásady na základě brány sítě VPN, i když možnost se zobrazí na portálu.  Možnost byla odebrána z portálu. 

- <!-- 1868283 --> Azure zásobníku teď zabraňuje, změnu velikosti virtuálního počítače, který je vytvořen s dynamickými disky. 

- <!-- 1756324 --> Data o využití pro virtuální počítače je teď oddělená v hodinových intervalech. To je konzistentní s Azure. 

- <!--  2253274 --> Problém kde v portálů správců a uživatelů v okně nastavení pro virtuální síť podsítě nepodaří načíst. Jako alternativní řešení, pomocí prostředí PowerShell a [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) rutiny zobrazit a spravovat tyto informace.

- Když vytvoříte virtuální počítač, zprávu *nejde zobrazit ceny* už se zobrazí při výběru velikost pro velikost virtuálního počítače.

- Různé opravy pro výkon, stabilitu, zabezpečení a operační systém, který je používán zásobník Azure.


### <a name="changes"></a>Změny
- Způsob, jak změnit stav na nově vytvořený nabídku z *privátní* k *veřejné* nebo *vyřazení* došlo ke změně. Další informace najdete v tématu [vytvořit nabídku](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace    
<!-- 2328416 --> Během instalace aktualizace 1803 může být výpadek služby objektů blob a interních služeb, které používají služby objektů blob. To zahrnuje některé operace virtuálního počítače. To výpadek může způsobit chyby klienta operace nebo výstrahy ze služeb, které nelze získat přístup k datům. Tento problém vyřeší sám po dokončení instalace aktualizace. 



### <a name="post-update-steps"></a>Postup po aktualizaci
- Po instalaci 1803 nainstalujte všechny použitelné opravy hotfix. Další informace naleznete v následujících článcích znalostní báze knowledge base, a také naše [obsluhy zásad](azure-stack-servicing-policy.md).

  - [KB 4341390 - oprava Hotfix Azure zásobníku 1.0.180424.12](https://support.microsoft.com/en-us/help/4341390).

- Po instalaci této aktualizace, zkontrolujte konfiguraci brány firewall zajistit [nezbytné porty](azure-stack-integrate-endpoints.md) jsou otevřené. Například tato aktualizace zavádí *Azure monitorování* který zahrnuje změnu protokolů auditu na protokoly aktivity. Díky této změně portu 13012 se teď používá a je třeba také otevřít.  


### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Toto jsou známé problémy po instalaci pro sestavení **20180323.2**.

#### <a name="portal"></a>Portál
- <!-- 2332636 - IS -->  Při použití služby AD FS pro systém identit Azure zásobníku a aktualizace na tuto verzi Azure zásobníku, výchozí vlastník předplatného výchozí zprostředkovatel se resetuje do vestavěné **CloudAdmin** uživatele.  
  Alternativní řešení: Chcete-li tento problém vyřešit, po instalaci této aktualizace, použijte krok 3 ze [automation aktivační události. ke konfiguraci vztahu důvěryhodnosti zprostředkovatele v zásobníku Azure deklarací](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) postup resetovat vlastníka předplatného výchozího zprostředkovatele.   

- Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro Azure zásobníku integrované systémy, používat https://aka.ms/newsupportrequest.

- <!-- 2050709 --> V portálu pro správu není možné upravit úložiště metriky pro služby objektů Blob, tabulka služby nebo služby front. Přejděte do úložiště, a potom vyberte objekt blob, tabulka nebo fronty služby dlaždice, otevře se nové okno zobrazující graf metriky pro tuto službu. Pokud pak vyberte Úpravy z horní části dlaždici grafu metriky, v okně upravit graf otevře, ale nezobrazí možnost upravit metriky.

- Nemusí být možné zobrazit úložiště nebo výpočetní prostředky na portálu správce. Příčinou tohoto problému je k chybě při instalaci aktualizace, která způsobí, že aktualizace, která se nesprávně hlásit jako úspěšné. Pokud chcete tento problém opakuje, obraťte se na technickou podporu společnosti Microsoft pro pomoc.

- Může se zobrazit prázdný řídicí panel portálu. Chcete-li obnovit řídicí panel, vyberte ikonu ozubené kolečko v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.

- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.

- Nelze zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení ověřte oprávnění pomocí prostředí PowerShell.

- V řídicím panelu portálu pro správu se nezdaří aktualizace dlaždice zobrazíte informace o aktualizacích. Chcete-li vyřešit tento problém, klikněte na dlaždici jej aktualizovat.

- V portálu pro správu, můžete se setkat kritickou výstrahu pro *Microsoft.Update.Admin* součásti. Název výstrahy, popis a nápravu všech zobrazit jako:  
    - *Chyba: šablonu pro FaultType ResourceProviderTimeout nebyl nalezen.*

  Tuto výstrahu můžete ignorovat. 


#### <a name="health-and-monitoring"></a>Sledování stavu a
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

  Obě výstrahy můžete bezpečně ignorovat. Se automaticky zavře v čase.  


#### <a name="marketplace"></a>Marketplace
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.



#### <a name="compute"></a>Compute
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

- Když vytvoříte sadu na portálu přejděte na dostupnosti **nový** > **výpočetní** > **sadu dostupnosti**, můžete vytvořit pouze skupiny dostupnosti s doména selhání a aktualizace domény 1. Jako řešení, při vytváření nového virtuálního počítače, vytvořte skupinu dostupnosti pomocí prostředí PowerShell, rozhraní příkazového řádku, nebo v portálu.

- Když vytvoříte virtuální počítače na portálu pro uživatele Azure zásobníku, portál zobrazí nesprávný počet datových disků, které můžete připojit do série D virtuálních počítačů. Všechny podporované řady D virtuální počítače zvládne tolik datových disků jako konfiguraci Azure.

- Když image virtuálního počítače se nepodaří vytvořit, položku se nezdařilo, nelze odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

-  Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování časový limit namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

- <!-- 1662991 --> Diagnostika virtuálních počítačů Linux není podporována v zásobníku Azure. Při nasazení virtuálního počítače s Linuxem pomocí diagnostiky virtuálních počítačů, které jsou povolené, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem pomocí nastavení pro diagnostiku.  


#### <a name="networking"></a>Sítě
- Po vytvoření virtuálního počítače a související s veřejnou IP adresu, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení pravděpodobně fungovat, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původně přidružený virtuální počítač a ne do nového.



- Azure zásobníku podporuje jediný *brány místní sítě* podle IP adresy. To platí ve všech předplatných klienta. Po vytvoření první místní síťové brány připojení, další pokusy o vytvoření prostředku brány místní sítě se stejnou adresou IP jsou blokovány.

- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna na vlastní selhání serveru DNS. Aktualizované nastavení nejsou nabídnutých do virtuálních počítačů v této virtuální sítě.

- Azure zásobník nepodporuje přidávání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.

- <!-- 2096388 --> Portál pro správu nelze použít k aktualizaci pravidla pro skupinu zabezpečení sítě. 

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

- <!-- IS, ASDK --> Speciální znaky, včetně mezery a tečky, nejsou podporovány v **rodiny** název při vytváření SKU pro poskytovatele prostředků SQL a databáze MySQL.

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
