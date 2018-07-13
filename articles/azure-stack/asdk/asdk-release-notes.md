---
title: Zpráva k vydání verze Microsoft Azure Stack Development Kit | Dokumentace Microsoftu
description: Vylepšení, oprav a známých problémů pro Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 86ac1f1b5433104faa89e1f107fa36fc1da5f70e
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989890"
---
# <a name="azure-stack-development-kit-release-notes"></a>Zpráva k vydání verze Azure Stack Development Kit  
Tyto poznámky k verzi obsahují informace o vylepšení, oprav a známé problémy v Azure Stack Development Kit. Pokud si nejste jistí, kterou verzi používáte, můžete si [použití portálu ke kontrole](.\.\azure-stack-updates.md#determine-the-current-version).

> Udržujte si s tím, co se přihlásíte k odběru je novinkou ASDK [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanálu](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Sestavení 1.1805.1.47

> [!TIP]  
> Na základě názorů zákazníků, dojde k aktualizaci verze schématu používá pro Microsoft Azure Stack. Od této aktualizace 1805, nové schéma lépe představuje aktuální cloudovou verzi.  
> 
> Verze schématu je nyní *Version.YearYearMonthMonth.MinorVersion.BuildNumber* kde označují sad druhý a třetí verzi a verzi. Například 1805.1 představuje *verze pro výrobu* 1805 verzi (RTM).  


### <a name="new-features"></a>Nové funkce 
Toto sestavení obsahuje následující vylepšení a oprav pro Azure Stack.  

- <!-- 2297790 - IS, ASDK --> **Azure Stack je teď zahrnuje *Syslog* klienta** jako *funkce ve verzi preview*. Tento klient umožňuje předávání protokolů auditu a zabezpečení spojených s infrastrukturou Azure Stack na Syslog serveru nebo zabezpečení událostí (SIEM) software pro správu, která je externí do služby Azure Stack. Klient protokolu Syslog v současné době podporuje pouze neověřenými připojeními UDP přes výchozí port 514. Datová část každé zprávy Syslog formátována v Common Event Format (CEF). 

  Chcete-li nakonfigurovat klienta Syslog, použijte **Set-SyslogServer** rutiny v privilegovaných koncový bod. 

  V této verzi preview může se zobrazit následující tři výstrahy. Při zápisu ve službě Azure Stack, tyto výstrahy obsahují *popisy* a *nápravy* pokyny. 
  - Název: Integrita kódu. vypnuto  
  - TITLE: Integrita kódu v režimu auditování 
  - TITLE: Vytvořeného účtu uživatele

  Tato funkce je ve verzi preview a je byste se neměli spoléhat v produkčním prostředí.   


### <a name="fixed-issues"></a>Opravené problémy
- Opravili jsme problém, který blokovaný [otevřete novou žádost o podporu z rozevíracího seznamu](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z portálu pro správu. Tato možnost teď funguje očekávaným způsobem. 

- **Různé opravy** výkonu, stability, zabezpečení a operační systém, který se používá ve službě Azure Stack


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- <!-- 2551834 - IS, ASDK --> Když vyberete **přehled** portálech na správce nebo uživatele, informace z účtu úložiště *Essentials* podokně nezobrazí.  V podokně Essentials se zobrazí informace o účtu jako jeho *skupiny prostředků*, *umístění*, a *ID předplatného*.  Další možnosti pro přehled jsou přístupné, jako je *služby* a *monitorování*, stejně jako možnosti *otevřít v Průzkumníkovi* nebo *odstranit účet úložiště* .  

  Chcete-li zobrazit není k dispozici informace, použijte [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) rutiny Powershellu. 

- <!-- 2551834 - IS, ASDK --> Když vyberete **značky** pro účet úložiště na portálech na správce nebo uživatele, informace se nepodaří načíst a nejsou zobrazeny.  

  Chcete-li zobrazit není k dispozici informace, použijte [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) rutiny Powershellu.

- <!-- TBD - IS ASDK --> Nepoužívejte nové typy pro správu předplatného *měření předplatné*, a *využití předplatného*. Tyto nové typy předplatného byly představený poprvé ve verzi 1804, ale ještě nejsou připravené k použití. By měla dál používat *výchozí zprostředkovatel* typu předplatného.  

- <!-- 2403291 - IS ASDK --> Nemusí mít užívání vodorovný posuvník v dolní části portály správce a uživatele. Pokud nemůžete použít vodorovný posuvník, left pomocí popisu cesty přejít na předchozí okno na portálu tak, že vyberete název okna je chcete zobrazit v seznamu s popisem cesty v horní části portálu.
  ![Navigace s popisem cesty](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

- <!-- TBD -  IS ASDK --> Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.


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

  Obě výstrahy #1 a #2 můžete bezpečně ignorovat a bude automaticky zavřít v čase. 

  Můžete si také prohlédnout následující výstrahu k *kapacity*. Pro toto upozornění se může lišit procento paměti k dispozici identifikované v popisu:  

  Upozornění #3:
   - Název: Nedostatek paměti kapacity
   - ZÁVAŽNOST: kritické
   - KOMPONENTA: kapacity
   - Popis: Region spotřeboval více než 80,00 % dostupné paměti. Vytváření virtuálních počítačů s velkými objemy paměti může selhat.  

  V této verzi služby Azure Stack můžete toto upozornění aktivovat nesprávně. Pokud nasazení úspěšně tenantské virtuální počítače, můžete ignorovat toto upozornění. 
  
  Upozornění #3 nezavře automaticky. Pokud zavřít tuto výstrahu vytvoří Azure Stack stejná výstraha do 15 minut.  

- <!-- 2368581 - IS ASDK --> Operátory Azure stacku, pokud se zobrazí upozornění na nedostatek paměti a virtuální počítače klientů selhání nasazení *Chyba při vytváření virtuálních počítačů Fabric*, je možné, že toto razítko Azure Stack je nedostatek dostupné paměti. Použití [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) nejlépe pochopit dostupné kapacity pro vaše úlohy. 


#### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> Když vyberete velikost virtuálního počítače pro nasazení virtuálního počítače, některé velikosti virtuálních počítačů řady F-Series se nezobrazí jako součást modulu pro výběr velikost při vytváření virtuálního počítače. Tyto velikosti virtuálních počítačů se nezobrazí v selektoru: *F8s_v2*, *F16s_v2*, *F32s_v2*, a *F64s_v2*.  
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


- <!-- TBD -  IS ASDK --> Nastavení škálování pro škálovací sady virtuálních počítačů nejsou k dispozici na portálu. Jako alternativní řešení můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdíly mezi verzemi prostředí PowerShell, je nutné použít `-Name` parametr namísto `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Při vytváření virtuálních počítačů na portálu user portal Azure Stack na portálu zobrazuje nesprávný počet datových disků, které můžete připojit virtuální počítač řady D series. Všechny podporované řady D series virtuálních počítačů může obsahovat libovolný počet datových disků jako konfiguraci Azure.

- <!-- TBD -  IS ASDK --> Selhání vytvoření image virtuálního počítače neúspěšné položky, který se nedá odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení vytvořte nové image virtuálního počítače s fiktivní virtuálního pevného disku, který je možné vytvářet přes Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-oprava SizeBytes – 1 GB). Tento proces by měla vyřešit problém, který zabraňuje odstranění položky. Pak 15 minut po vytvoření fiktivní image můžete úspěšně ho odstranit.

  Potom můžete zkusit znovu stáhněte image virtuálního počítače, která původně selhala.

- <!-- TBD -  IS ASDK --> Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

- <!-- 1662991 - IS ASDK --> Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky. 

#### <a name="networking"></a>Sítě
- <!-- TBD - IS ASDK --> Trasy definované uživatelem nelze vytvořit buď v portálu pro správu nebo uživatele. Jako alternativní řešení použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

- <!-- 2388980 -  IS ASDK --> Po vytvoření virtuálního počítače a přidružené k veřejné IP adresy, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení přidružení zdánlivě fungovat, ale zůstane spojená s původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používaly pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že znovu přiřadit IP adresu do nového virtuálního počítače (obvykle označované jako *prohození virtuálních IP adres*). Všechny budoucí pokusy o připojení přes tuto IP adresu výsledkem připojení původního virtuálního počítače a ne do nové.


- <!-- 2292271 - IS ASDK --> Při zvýšení maximální kvóty pro sítě prostředek, který je součástí nabídky a plán, který je přidružený k předplatnému klienta služby, nové omezení neplatí pro toto předplatné. Nový limit se však nevztahují na nových předplatných, které jsou vytvořeny po zvýšení této kvóty. 

  Chcete-li tento problém vyřešit, použijte doplňkový plán na zvýšení této kvóty sítě při v plánu je už přidružený k předplatnému. Další informace najdete v tématu Jak [zpřístupnit doplňkový plán](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nelze odstranit odběr, který má prostředky zóny DNS nebo prostředky směrovací tabulku s ním spojená. Chcete-li úspěšně odstranit předplatné, musíte nejprve odstranit zónu DNS a směrovací tabulky prostředků z předplatného tenanta. 


- <!-- 1902460 -  IS ASDK --> Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

- <!-- 16309153 -  IS ASDK --> Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.
 
- <!-- TBD -  IS ASDK --> Azure Stack nepodporuje přidávání další síťová rozhraní k instanci virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.


#### <a name="sql-and-mysql"></a>SQL a MySQL 
- <!-- TBD - ASDK --> Databáze hostování servery musí být vyhrazená pro použití podle prostředku zprostředkovatele a uživatelského zatížení. Nelze použít instanci, která se používá v jakékoli další příjemce, včetně služeb App Services.

- <!-- IS, ASDK --> Speciální znaky, mezery a tečky, včetně nepodporuje **řady** název při vytváření SKU pro poskytovatele prostředků SQL nebo MySQL. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- <!-- TBD - IS ASDK --> Aby bylo možné horizontální navýšení kapacity infrastruktury (pracovních procesů, správy, role front-endu), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.  

- <!-- TBD - IS ASDK --> App Service můžete nasadit jenom do *předplatné poskytovatele. výchozí* v tuto chvíli. V budoucí aktualizaci, bude nasazení služby App Service do nové *měření předplatné* , která byla zavedena ve verzi 1804 Azure Stack. Při použití podporuje měření, všechna existující nasazení budou migrovány na tento nový typ předplatného.

#### <a name="usage"></a>Využití  
- <!-- TBD -  IS ASDK --> Data o využití veřejné IP adresy využití měřičů zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítko, které ukazuje, kdy se záznam vytvořil. V současné době nelze pomocí těchto dat pro monitorování přesné použití veřejné IP adresy.

<!-- #### Identity -->



## <a name="build-201805131"></a>Sestavení 20180513.1

### <a name="new-features"></a>Nové funkce 
Toto sestavení obsahuje následující vylepšení a oprav pro Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **Nový správce předplatných**. S 1804 existují dva nové typy předplatného k dispozici na portálu. Tyto nové typy předplatného jsou pomocí nové instalace služby Azure Stack od verze 1804 kromě předplatné poskytovatele. výchozí a viditelné. *Nepoužívejte tyto nové typy předplatného s touto verzí služby Azure Stack*. Oznámíme později dostupnosti používat tyto typy předplatného se pomocí budoucí aktualizaci. 

  Tyto nové typy předplatného jsou viditelné, ale součástí větší změny pro zabezpečení předplatné poskytovatele. výchozí a aby bylo snazší nasadit sdílené prostředky, jako je hostitelem SQL servery. 

  Jsou nyní k dispozici předplatné tři typy:  
  - Výchozí předplatné poskytovatele: nadále používat tento typ předplatného. 
  - Monitorování míry využívání předplatného: *nepoužívejte tento typ předplatného.*
  - Využití předplatného: *nepoužívejte tento typ předplatného*

### <a name="fixed-issues"></a>Opravené problémy
- <!-- IS, ASDK -->  V portálu pro správu máte již aktualizovat aktualizovat dlaždici předtím, než se zobrazí informace. 

- <!-- 2050709 - IS, ASDK -->  Teď můžete na portálu pro správu k úpravě metrik úložiště pro službu Blob service, služba Table service a služba front.

- <!-- IS, ASDK --> V části **sítě**, po kliknutí na **připojení** nastavit připojení k síti VPN, **Site-to-site (IPsec)** je teď k dispozici jenom možnost. 

- **Různé opravy** výkonu, stability, zabezpečení a operační systém, který se používá ve službě Azure Stack

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Další verze vypršel časový limit v této aktualizaci  
Následující jsou teď k dispozici, ale které nevyžadují aktualizaci Azure Stack 1804.
- **Aktualizace pro Microsoft Azure Stack System Center Operations Manageru sada sledování**. Nová verze (1.0.3.0) Microsoft System Center Operations Manager monitorování Pack pro službu Azure Stack je k dispozici pro [Stáhnout](https://www.microsoft.com/download/details.aspx?id=55184). S touto verzí můžete použít instanční objekty při přidání připojené nasazení Azure stacku. Tato verze obsahuje taky prostředí správy aktualizací, které vám umožní převzít nápravné akce přímo z nástroje Operations Manager. Existují také nové řídicí panely, které zobrazení poskytovatelů prostředků a jednotek škálování, škálování jednotek uzlů.

- **Nové Azure Stack verze prostředí PowerShell správce 1.3.0**.  Azure Stack Powershellu 1.3.0 je teď dostupná k instalaci. Tato verze obsahuje příkazy pro všechny poskytovatele prostředků správce ke správě služby Azure Stack.  V této verzi se přestanou nějaký obsah z Githubu nástroje Azure Stack [úložiště](https://github.com/Azure/AzureStack-Tools). 

   Podrobné informace o instalaci, postupujte [pokyny](.\.\azure-stack-powershell-install.md) nebo [pomáhají](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) obsahu pro Azure Stack modul 1.3.0. 

- **Počáteční verze Reference k rozhraní API Rest Azure Stack**. [Reference k rozhraní API pro všechny poskytovatele prostředků Azure stacku správu](https://docs.microsoft.com/rest/api/azure-stack/) je publikováno. 

### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- <!-- TBD - IS ASDK --> Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro Azure Stack Development Kit, použijte https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Nemusí mít užívání vodorovný posuvník v dolní části portály správce a uživatele. Pokud nemůžete použít vodorovný posuvník, left pomocí popisu cesty přejít na předchozí okno na portálu tak, že vyberete název okna je chcete zobrazit v seznamu s popisem cesty v horní části portálu.
  ![Navigace s popisem cesty](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

- <!-- TBD -  IS ASDK --> Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.

-   <!-- TBD -  IS ASDK --> V portálu pro správu může se zobrazit Kritická výstraha pro součást Microsoft.Update.Admin. Název výstrahy, popis a nápravu všech zobrazit jako:  
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
- Uživatele můžete přejít na kompletní nabídku marketplace bez předplatného a vidí správce různé věci, třeba plány a nabídky. Tyto položky jsou pro uživatele nefunkční.
 
#### <a name="compute"></a>Compute
- <!-- TBD -  IS ASDK --> Nastavení škálování pro škálovací sady virtuálních počítačů nejsou k dispozici na portálu. Jako alternativní řešení můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdíly mezi verzemi prostředí PowerShell, je nutné použít `-Name` parametr namísto `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Při vytváření virtuálních počítačů na portálu user portal Azure Stack na portálu zobrazuje nesprávný počet datových disků, které můžete připojit k DS-series virtuálních počítačů. Virtuálních počítačů řady DS může obsahovat libovolný počet datových disků jako konfiguraci Azure.

- <!-- TBD -  IS ASDK --> Selhání vytvoření image virtuálního počítače neúspěšné položky, který se nedá odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení vytvořte nové image virtuálního počítače s fiktivní virtuálního pevného disku, který je možné vytvářet přes Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-oprava SizeBytes – 1 GB). Tento proces by měla vyřešit problém, který zabraňuje odstranění položky. Pak 15 minut po vytvoření fiktivní image můžete úspěšně ho odstranit.

  Potom můžete zkusit znovu stáhněte image virtuálního počítače, která původně selhala.

- <!-- TBD -  IS ASDK --> Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

- <!-- 1662991 - IS ASDK --> Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky. 

#### <a name="networking"></a>Sítě
- <!-- 1766332 - IS, ASDK --> V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

- <!-- 2388980 -  IS ASDK --> Po vytvoření virtuálního počítače a přidružené k veřejné IP adresy, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení přidružení zdánlivě fungovat, ale zůstane spojená s původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používaly pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že znovu přiřadit IP adresu do nového virtuálního počítače (obvykle označované jako *prohození virtuálních IP adres*). Všechny budoucí pokusy o připojení přes tuto IP adresu výsledkem připojení původního virtuálního počítače a ne do nové.

- <!-- 2292271 - IS ASDK --> Při zvýšení maximální kvóty pro sítě prostředek, který je součástí nabídky a plán, který je přidružený k předplatnému klienta služby, nové omezení neplatí pro toto předplatné. Nový limit se však nevztahují na nových předplatných, které jsou vytvořeny po zvýšení této kvóty. 

  Chcete-li tento problém vyřešit, použijte doplňkový plán na zvýšení této kvóty sítě při v plánu je už přidružený k předplatnému. Další informace najdete v tématu Jak [zpřístupnit doplňkový plán](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nelze odstranit odběr, který má prostředky zóny DNS nebo prostředky směrovací tabulku s ním spojená. Chcete-li úspěšně odstranit předplatné, musíte nejprve odstranit zónu DNS a směrovací tabulky prostředků z předplatného tenanta. 


- <!-- 1902460 -  IS ASDK --> Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

- <!-- 16309153 -  IS ASDK --> Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.
 
- <!-- TBD -  IS ASDK --> Azure Stack nepodporuje přidávání další síťová rozhraní k instanci virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.


#### <a name="sql-and-mysql"></a>SQL a MySQL 
- <!-- TBD - ASDK --> Databáze hostování servery musí být vyhrazená pro použití podle prostředku zprostředkovatele a uživatelského zatížení. Nelze použít instanci, která se používá v jakékoli další příjemce, včetně služeb App Services.

- <!-- IS, ASDK --> Speciální znaky, mezery a tečky, včetně nepodporuje **řady** název při vytváření SKU pro poskytovatele prostředků SQL nebo MySQL. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- <!-- TBD -  IS ASDK --> Aby bylo možné horizontální navýšení kapacity infrastruktury (pracovních procesů, správy, role front-endu), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.
 
#### <a name="usage"></a>Využití  
- <!-- TBD -  IS ASDK --> Data o využití veřejné IP adresy využití měřičů zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítko, které ukazuje, kdy se záznam vytvořil. V současné době nelze pomocí těchto dat pro monitorování přesné použití veřejné IP adresy.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Stažení nástroje pro Azure Stack z Githubu
- Při použití *invoke-webrequest* rutina prostředí PowerShell pro stažení služby Azure Stack nástroje z Githubu, zobrazí chybová zpráva:     
    -  *Invoke-webrequest: požadavek byl přerušen: Nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu poslední vyřazení Githubu podporu protokolu Tlsv1 a Tlsv1.1 kryptografických standardů (výchozí nastavení pro prostředí PowerShell). Další informace najdete v tématu [slabé kryptografické standardy odebrání oznámení](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Sestavení 20180302.1

### <a name="new-features-and-fixes"></a>Nové funkce a opravy
Nové funkce a opravy, které jsou všeobecně dostupné pro Azure Stack integrované systémy pro verzi 1803 použít pro Azure Stack Development Kit. Najdete v článku [nové funkce](.\.\azure-stack-update-1803.md#new-features) a [opravili problémy](.\.\azure-stack-update-1803.md#fixed-issues) oddíly Azure Stack 1803 aktualizovat poznámky k verzi pro podrobnosti.  
> [!IMPORTANT]    
> Některé položky uvedené v **nové funkce** a **opravili problémy** oddíly jsou relevantní pouze pro integrované systémy Azure Stack.

### <a name="changes"></a>Změny
- Způsob, jak změnit stav nabídku na nově vytvořený z *privátní* k *veřejné* nebo *Vyřazená z provozu* došlo ke změně. Další informace najdete v tématu [vytvořte nabídku](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro Azure Stack Development Kit, použijte https://aka.ms/azurestackforum.    

- <!-- 2050709 --> V portálu pro správu není možné upravit metrik úložiště pro službu Blob service, služba Table service nebo služby front. Když účtu služby Storage a potom vyberte objekt blob, tabulky nebo fronty služby dlaždice, otevře se nové okno, která zobrazuje graf metrik za danou službu. Pokud pak vyberete Úpravy od horního okraje grafu dlaždice metrik, v okně upravit graf se otevře, ale nezobrazuje možnost upravit metriky.  

- Zobrazí **vyžadována aktivace** výstražné upozornění, s výzvou k registraci Azure Stack Development Kit. Toto chování je očekávané.

- Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

- Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.

- Na řídicím panelu portálu pro správu aktualizovat dlaždici selže k zobrazení informací o aktualizacích. Chcete-li vyřešit tento problém, kliknutím na dlaždici ji aktualizovat.

-   V portálu pro správu může se zobrazit Kritická výstraha pro součást Microsoft.Update.Admin. Název výstrahy, popis a nápravu všech zobrazit jako:  
    - *Chyba: chybí šablona pro FaultType ResourceProviderTimeout.*

    Toto upozornění můžete ignorovat. 

- V portálu pro správu i portál user portal, se okno přehledu nepodaří načíst při výběru okno přehledu pro účty úložiště, které byly vytvořeny ve starší verzi rozhraní API (Příklad: 2015-06-15). 

  Jako alternativní řešení, pomocí prostředí PowerShell pro spuštění **Start ResourceSynchronization.ps1** skript obnovení přístupu k podrobnosti o účtu úložiště. [Skript je k dispozici na Githubu]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)a je nutné spustit s přihlašovacími údaji správce služby na hostiteli development kit používáte ASDK.  

- **Service Health** okno se nepodaří načíst. Když otevřete okno Service Health na správce nebo uživatel portálu Azure Stack chybovou zprávu a nenačte informace. Toto je očekávané chování. I když můžete vybrat a otevřít Service Health, tato funkce ještě není k dispozici, ale budou implementovány v budoucí verzi služby Azure Stack.


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

- V portálu pro správu služby Azure Stack, může se zobrazit kritickou výstrahu s názvem **až do vypršení platnosti certifikátu externí**.  Toto upozornění můžete ignorovat a mít vliv na provoz Azure Stack Development Kit. 


#### <a name="marketplace"></a>Marketplace
- Uživatele můžete přejít na kompletní nabídku marketplace bez předplatného a vidí správce různé věci, třeba plány a nabídky. Tyto položky jsou pro uživatele nefunkční.
 
#### <a name="compute"></a>Compute
- Nastavení škálování pro škálovací sady virtuálních počítačů nejsou k dispozici na portálu. Jako alternativní řešení můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdíly mezi verzemi prostředí PowerShell, je nutné použít `-Name` parametr namísto `-VMScaleSetName`.

- Při vytváření virtuálních počítačů na portálu user portal Azure Stack na portálu zobrazuje nesprávný počet datových disků, které můžete připojit k DS-series virtuálních počítačů. Virtuálních počítačů řady DS může obsahovat libovolný počet datových disků jako konfiguraci Azure.

- Selhání vytvoření image virtuálního počítače neúspěšné položky, který se nedá odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení vytvořte nové image virtuálního počítače s fiktivní virtuálního pevného disku, který je možné vytvářet přes Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-oprava SizeBytes – 1 GB). Tento proces by měla vyřešit problém, který zabraňuje odstranění položky. Pak 15 minut po vytvoření fiktivní image můžete úspěšně ho odstranit.

  Potom můžete zkusit znovu stáhněte image virtuálního počítače, která původně selhala.

-  Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

- <!-- 1662991 --> Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky. 


#### <a name="networking"></a>Sítě
- V části **sítě**, vyberete-li **připojení** nastavit připojení k síti VPN, **připojení typu VNet-to-VNet** je uveden jako typ připojení je to možné. Nevybírejte tuto možnost. V současné době pouze **Site-to-site (IPsec)** možnost je podporována.

- Po vytvoření virtuálního počítače a přidružené k veřejné IP adresy, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení přidružení zdánlivě fungovat, ale zůstane spojená s původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používaly pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že znovu přiřadit IP adresu do nového virtuálního počítače (obvykle označované jako *prohození virtuálních IP adres*). Všechny budoucí pokusy o připojení přes tuto IP adresu výsledkem připojení původního virtuálního počítače a ne do nové.



- Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.
 
- Azure Stack nepodporuje přidávání další síťová rozhraní k instanci virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.



#### <a name="sql-and-mysql"></a>SQL a MySQL 
- Může trvat až jednu hodinu, než se uživatelé můžou vytvářet databáze v nových SQL nebo MySQL SKU.

- Databáze hostování servery musí být vyhrazená pro použití podle prostředku zprostředkovatele a uživatelského zatížení. Nelze použít instanci, která se používá v jakékoli další příjemce, včetně služeb App Services.

- <!-- IS, ASDK --> Speciální znaky, mezery a tečky, včetně nepodporuje **řady** nebo **úroveň** názvy při vytváření SKU pro poskytovatele prostředků SQL nebo MySQL.

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





