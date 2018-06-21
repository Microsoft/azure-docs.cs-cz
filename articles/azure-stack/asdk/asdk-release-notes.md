---
title: Poznámky k verzi Microsoft Azure zásobníku Development Kit | Microsoft Docs
description: Vylepšení, opravy a známé problémy pro Azure zásobníku Development Kit.
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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: bbd9bb0d56dd61fd0a32531ac425a1dbc1aa8923
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295786"
---
# <a name="azure-stack-development-kit-release-notes"></a>Poznámky k verzi Azure zásobníku Development Kit  
Tyto poznámky k verzi obsahují informace o vylepšení, opravy a známých problémů v Azure zásobníku Development Kit. Pokud si nejste jistí, kterou verzi používáte, můžete [použití portálu ke kontrole](.\.\azure-stack-updates.md#determine-the-current-version).

> Vždy aktuální s co je nového v ASDK se přihlásíte k odběru [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanálu](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Sestavení 1.1805.1.47

> [!TIP]  
> Na základě názorů zákazníků, dojde k aktualizaci verze schématu používán pro Microsoft Azure zásobníku. Počínaje touto aktualizací 1805, nové schéma lépe představuje aktuální verze cloudu.  
> 
> Verze schématu je nyní *Version.YearYearMonthMonth.MinorVersion.BuildNumber* kde sady druhý a třetí znamenat verze a verze. Například 1805.1 představuje *verze výrobní* (RTM) verzi 1805.  


### <a name="new-features"></a>Nové funkce 
Toto sestavení obsahuje následující vylepšení a opravy pro Azure zásobníku.  

- <!-- 2297790 - IS, ASDK --> **Nyní zahrnuje Azure zásobníku *Syslog* klienta** jako *funkce ve verzi preview*. Tento klient umožňuje odesílání protokoly auditu a zabezpečení spojených s infrastrukturou Azure zásobníku se Syslog serveru nebo zabezpečení informace a událostí správy (SIEM) softwarem, který je externí do protokolů Azure. Klient pro Syslog v současné době podporuje pouze připojení bez ověření UDP přes výchozí port 514. Datová část každou zprávu Syslog je naformátován společné formátu událostí (CEF). 

  Chcete-li nakonfigurovat klienta Syslog, použijte **Set-SyslogServer** rutiny v privilegované koncový bod. 

  S touto verzí preview může se zobrazit následující tři výstrahy. Když sada protokolů Azure, patří tyto výstrahy *popisy* a *nápravy* pokyny. 
  - TITLE: Integrity kódu vypnuto  
  - TITLE: Integrity kódu v režimu auditování 
  - TITLE: Uživatelský účet vytvořený

  Když tato funkce je ve verzi preview, nemělo by v produkčním prostředí spoléhat.   


### <a name="fixed-issues"></a>Opravené problémy
- Vyřešili problém, který blokovaný [otevření nové žádosti o podporu z rozevíracího seznamu](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z portálu pro správu. Tato možnost teď funguje tak, jak má. 

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán Azure zásobníku


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- <!-- 2551834 - IS, ASDK --> Když vyberete **přehled** pro účet úložiště na portály na správce nebo uživatele, informace z *Essentials* podokně nezobrazí.  V podokně Essentials se zobrazí informace o účtu jako jeho *skupiny prostředků*, *umístění*, a *ID předplatného*.  Další možnosti pro přehled jsou dostupné, jako je *služby* a *monitorování*, stejně jako možnosti k *otevřít v Průzkumníkovi* nebo *odstranit účet úložiště* .  

  Chcete-li zobrazit informace k dispozici, použijte [Get azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) rutiny prostředí PowerShell. 

- <!-- 2551834 - IS, ASDK --> Když vyberete **značky** pro účet úložiště na portály na správce nebo uživatele informace nepodaří načíst a nezobrazí.  

  Chcete-li zobrazit informace k dispozici, použijte [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) rutiny prostředí PowerShell.

- <!-- TBD - IS ASDK --> Nepoužívejte nové typy pro správu předplatného *měření předplatné*, a *spotřeba předplatné*. Tyto nové typy předplatného zavedených verzí 1804 ale ještě nejsou připravené k použití. By měly být nadále používat *výchozí zprostředkovatel* typ předplatného.  

- <!-- 2403291 - IS ASDK --> Nemusí mít použití vodorovného posuvníku podél dolního okraje portálů správců a uživatelů. Pokud máte přístup k vodorovného posuvníku, pomocí popisu cesty a přejděte do předchozího okna portálu výběrem název okna je chcete zobrazit ze seznamu s popisem cesty, které se nachází v horní levé části portálu.
  ![Popis cesty](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.

- <!-- TBD -  IS ASDK --> Nelze zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení ověřte oprávnění pomocí prostředí PowerShell.


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

  Obě výstrahy můžete bezpečně ignorovat a bude automaticky zavřete v čase.  

- <!-- 2392907 – ASDK -->   Můžete se setkat *kritické* výstrahy pro **nízká kapacita paměti**. Tato výstraha má následující popis: *oblast spotřebovala víc než 95.00 % dostupné paměti. Vytváření virtuálních počítačů s velkými objemy paměti může selhat.*

  Tuto výstrahu můžete generovat, je-li Azure zásobníku se nesprávně účty pro využití paměti v sadě pro vývoj Azure zásobníku.  

  Tuto výstrahu můžete ignorovat a tento problém nemá vliv na umístění virtuálních počítačů. 

- <!-- 2368581 - IS. ASDK --> Operátor zásobník Azure, pokud se zobrazí upozornění na nedostatek paměti a klientské virtuální počítače se nepodařilo nasadit s *Chyba při vytváření virtuálních počítačů Fabric*, je možné, že Azure zásobníku razítko nedostatek dostupné paměti. Použití [Azure zásobníku kapacity Planne](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) nejlépe pochopit dostupné kapacity pro zatížení. 


#### <a name="compute"></a>Compute
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


- <!-- TBD -  IS ASDK --> Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Když vytvoříte virtuální počítače na portálu pro uživatele Azure zásobníku, portál zobrazí nesprávný počet datových disků, které můžete připojit D řady virtuálních počítačů. Všechny podporované řady D virtuální počítače zvládne tolik datových disků jako konfiguraci Azure.

- <!-- TBD -  IS ASDK --> Když image virtuálního počítače se nepodaří vytvořit, položku se nezdařilo, nelze odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

- <!-- TBD -  IS ASDK --> Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování časový limit namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

- <!-- 1662991 - IS ASDK --> Diagnostika virtuálních počítačů Linux není podporována v zásobníku Azure. Při nasazení virtuálního počítače s Linuxem pomocí diagnostiky virtuálních počítačů, které jsou povolené, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem pomocí nastavení pro diagnostiku. 

#### <a name="networking"></a>Sítě
- <!-- TBD - IS ASDK --> Trasy definované uživatelem nelze vytvořit buď portálu správce nebo uživatele. Jako alternativní řešení použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> V části **sítě**, pokud kliknete na tlačítko **vytvořit bránu VPN** nastavit připojení k síti VPN **na základě zásad** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **na základě trasy** možnost je podporována v zásobníku Azure.

- <!-- 2388980 -  IS ASDK --> Po vytvoření virtuálního počítače a související s veřejnou IP adresu, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení pravděpodobně fungovat, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původní virtuální počítač a ne do nového.


- <!-- 2292271 - IS ASDK --> Pokud zvýšíte limit kvóty pro síťového prostředku, který je součástí služby nabídka a plán, který je přidružený k předplatnému klienta, nový limit neplatí pro toto předplatné. Nový limit však platí pro nové odběry, které jsou vytvořeny po kvótu vzroste. 

  Chcete-li tento problém obejít, použijte plán rozšíření zvýšit kvótu sítě, pokud je již přidružena k odběru plánu. Další informace najdete v tématu Jak [zpřístupnit plán rozšíření](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nelze odstranit odběr, který má zónu DNS prostředky nebo prostředky směrovací tabulku s ním spojená. Úspěšně odstraňte odběr, musíte nejprve odstranit zónu DNS a směrovací tabulku prostředky z předplatného klienta. 


- <!-- 1902460 -  IS ASDK --> Azure zásobníku podporuje jediný *brány místní sítě* podle IP adresy. To platí ve všech předplatných klienta. Po vytvoření první místní síťové brány připojení, další pokusy o vytvoření prostředku brány místní sítě se stejnou adresou IP jsou blokovány.

- <!-- 16309153 -  IS ASDK --> Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna na vlastní selhání serveru DNS. Aktualizované nastavení nejsou nabídnutých do virtuálních počítačů v této virtuální sítě.
 
- <!-- TBD -  IS ASDK --> Azure zásobník nepodporuje přidávání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.


#### <a name="sql-and-mysql"></a>SQL a MySQL 
- <!-- TBD - ASDK --> Databázi hostitelské servery musí být vyhrazená pro použití prostředků zprostředkovatele a uživatelského úlohami. Nelze použít instanci, která se používá jakékoli další příjemce, včetně aplikační služby.

- <!-- IS, ASDK --> Speciální znaky, včetně mezery a tečky, nejsou podporovány v **rodiny** název při vytváření SKU pro poskytovatele prostředků SQL a databáze MySQL. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- <!-- TBD - IS ASDK --> Aby bylo možné škálovat infrastrukturu (pracovních procesů, správy, front-end rolí), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočet.  

- <!-- TBD - IS ASDK --> Aplikace služby lze nasadit pouze do *výchozí zprostředkovatel odběru* v tuto chvíli. V budoucí aktualizaci, bude do nové nasazení služby App Service *měření předplatné* která byla zavedena v zásobníku 1804 Azure. Při měření podporuje pro použití, všechna existující nasazení budou migrovány tento nový typ odběru.

#### <a name="usage"></a>Využití  
- <!-- TBD -  IS ASDK --> Využití veřejné IP adresy využití měření data zobrazují stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítka, který ukazuje vytvoření záznamu. Tato data v současné době nelze použít pro monitorování přesné veřejnou IP adresu využití.

<!-- #### Identity -->



## <a name="build-201805131"></a>Sestavení 20180513.1

### <a name="new-features"></a>Nové funkce 
Toto sestavení obsahuje následující vylepšení a opravy pro Azure zásobníku.  

- <!-- 1759172 - IS, ASDK --> **Nový správce předplatných**. U 1804 jsou dva nové typy předplatného dostupné na portálu. Tyto nové typy předplatného jsou kromě výchozí zprostředkovatel odběru a viditelné s nové instalace zásobník Azure od verze 1804. *Nepoužívejte tyto nové typy předplatné s touto verzí Azure zásobníku*. Nemůžeme se oznamujeme dostupnost používat tyto typy předplatného se pomocí budoucí aktualizaci. 

  Tyto nové typy předplatného jsou viditelné, ale součástí větší změnu pro zabezpečení výchozí zprostředkovatel odběru a aby bylo snazší nasazování sdílené prostředky, jako je hostování SQL servery. 

  Nyní k dispozici tři předplatné typy jsou:  
  - Výchozí zprostředkovatel odběru: nadále používat tento typ předplatného. 
  - Měření předplatné: *nepoužívejte tento typ předplatného.*
  - Spotřeba předplatné: *nepoužívejte tento typ předplatného*

### <a name="fixed-issues"></a>Opravené problémy
- <!-- IS, ASDK -->  V portálu pro správu budete muset už aktualizovat dlaždici aktualizace, než se zobrazí informace. 

- <!-- 2050709 - IS, ASDK -->  Portál pro správu teď můžete upravit úložiště metriky pro služby objektů Blob, tabulka služby a služby front.

- <!-- IS, ASDK --> V části **sítě**, po kliknutí na tlačítko **připojení** nastavit připojení k síti VPN **Site-to-site (IPsec)** je nyní k dispozici jenom možnost. 

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán Azure zásobníku

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Další verze vypršel časový limit s touto aktualizací  
Toto jsou nyní k dispozici, ale nevyžadují zásobník Azure aktualizace 1804.
- **Aktualizace do sady sledování nástroje Microsoft Azure zásobníku System Center Operations Manager**. Nová verze (1.0.3.0) Microsoft System Center Operations Manager monitorování Pack pro zásobník Azure je k dispozici pro [Stáhnout](https://www.microsoft.com/download/details.aspx?id=55184). S touto verzí můžete použít objekty služby při přidání připojené nasazení Azure zásobníku. Tato verze taky funkce prostředí správy aktualizací, které umožňuje provádět akci nápravy přímo z prostředí nástroje Operations Manager. Existují také nové řídicí panely, které zobrazují poskytovatelů prostředků, jednotek škálování a škálování jednotky uzly.

- **Nový Azure zásobníku verze prostředí PowerShell správce 1.3.0**.  Azure PowerShell zásobníku 1.3.0 je nyní k dispozici pro instalaci. Tato verze obsahuje příkazy pro všechny poskytovatele správce prostředků ke správě Azure zásobníku.  V této verzi se obsah přestanou z Githubu nástroje zásobník Azure [úložiště](https://github.com/Azure/AzureStack-Tools). 

   Podrobné informace o instalaci, postupujte podle [pokyny](.\.\azure-stack-powershell-install.md) nebo [pomoci](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) obsahu pro modul zásobník Azure 1.3.0. 

- **Počáteční verze referenční dokumentace Azure zásobníku rozhraní API Rest**. [Referenční dokumentace rozhraní API pro všechny poskytovatele prostředků Azure zásobníku správce](https://docs.microsoft.com/rest/api/azure-stack/) je nyní publikována. 

### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- <!-- TBD - IS ASDK --> Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro Azure zásobníku Development Kit použít https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Nemusí mít použití vodorovného posuvníku podél dolního okraje portálů správců a uživatelů. Pokud máte přístup k vodorovného posuvníku, pomocí popisu cesty a přejděte do předchozího okna portálu výběrem název okna je chcete zobrazit ze seznamu s popisem cesty, které se nachází v horní levé části portálu.
  ![Popis cesty](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.

- <!-- TBD -  IS ASDK --> Nelze zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení ověřte oprávnění pomocí prostředí PowerShell.

-   <!-- TBD -  IS ASDK --> V portálu pro správu může se zobrazit Kritická výstraha pro součást Microsoft.Update.Admin. Název výstrahy, popis a nápravu všech zobrazit jako:  
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
- <!-- TBD -  IS ASDK --> Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Když vytvoříte virtuální počítače na portálu pro uživatele Azure zásobníku, portál zobrazí nesprávný počet datových disků, které můžete připojit k řady DS virtuálních počítačů. Řady DS virtuální počítače zvládne tolik datových disků jako konfiguraci Azure.

- <!-- TBD -  IS ASDK --> Když image virtuálního počítače se nepodaří vytvořit, položku se nezdařilo, nelze odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

- <!-- TBD -  IS ASDK --> Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování časový limit namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

- <!-- 1662991 - IS ASDK --> Diagnostika virtuálních počítačů Linux není podporována v zásobníku Azure. Při nasazení virtuálního počítače s Linuxem pomocí diagnostiky virtuálních počítačů, které jsou povolené, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem pomocí nastavení pro diagnostiku. 

#### <a name="networking"></a>Sítě
- <!-- 1766332 - IS, ASDK --> V části **sítě**, pokud kliknete na tlačítko **vytvořit bránu VPN** nastavit připojení k síti VPN **na základě zásad** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **na základě trasy** možnost je podporována v zásobníku Azure.

- <!-- 2388980 -  IS ASDK --> Po vytvoření virtuálního počítače a související s veřejnou IP adresu, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení pravděpodobně fungovat, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původní virtuální počítač a ne do nového.

- <!-- 2292271 - IS ASDK --> Pokud zvýšíte limit kvóty pro síťového prostředku, který je součástí služby nabídka a plán, který je přidružený k předplatnému klienta, nový limit neplatí pro toto předplatné. Nový limit však platí pro nové odběry, které jsou vytvořeny po kvótu vzroste. 

  Chcete-li tento problém obejít, použijte plán rozšíření zvýšit kvótu sítě, pokud je již přidružena k odběru plánu. Další informace najdete v tématu Jak [zpřístupnit plán rozšíření](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nelze odstranit odběr, který má zónu DNS prostředky nebo prostředky směrovací tabulku s ním spojená. Úspěšně odstraňte odběr, musíte nejprve odstranit zónu DNS a směrovací tabulku prostředky z předplatného klienta. 


- <!-- 1902460 -  IS ASDK --> Azure zásobníku podporuje jediný *brány místní sítě* podle IP adresy. To platí ve všech předplatných klienta. Po vytvoření první místní síťové brány připojení, další pokusy o vytvoření prostředku brány místní sítě se stejnou adresou IP jsou blokovány.

- <!-- 16309153 -  IS ASDK --> Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna na vlastní selhání serveru DNS. Aktualizované nastavení nejsou nabídnutých do virtuálních počítačů v této virtuální sítě.
 
- <!-- TBD -  IS ASDK --> Azure zásobník nepodporuje přidávání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.


#### <a name="sql-and-mysql"></a>SQL a MySQL 
- <!-- TBD - ASDK --> Databázi hostitelské servery musí být vyhrazená pro použití prostředků zprostředkovatele a uživatelského úlohami. Nelze použít instanci, která se používá jakékoli další příjemce, včetně aplikační služby.

- <!-- IS, ASDK --> Speciální znaky, včetně mezery a tečky, nejsou podporovány v **rodiny** název při vytváření SKU pro poskytovatele prostředků SQL a databáze MySQL. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- <!-- TBD -  IS ASDK --> Aby bylo možné škálovat infrastrukturu (pracovních procesů, správy, front-end rolí), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočet.
 
#### <a name="usage"></a>Využití  
- <!-- TBD -  IS ASDK --> Využití veřejné IP adresy využití měření data zobrazují stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítka, který ukazuje vytvoření záznamu. Tato data v současné době nelze použít pro monitorování přesné veřejnou IP adresu využití.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Stažení nástroje Azure zásobníku z Githubu
- Při použití *vyvolání webrequest* rutiny prostředí PowerShell ke stažení protokolů Azure nástroje z Githubu, obdržíte chybu:     
    -  *vyvolání webrequest: požadavek byl přerušen: Nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu poslední zastaralá podpora Githubu Tlsv1 a Tlsv1.1 kryptografických standardů (výchozí nastavení pro prostředí PowerShell). Další informace najdete v tématu [slabé kryptografické standardy odebrání oznámení](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Sestavení 20180302.1

### <a name="new-features-and-fixes"></a>Nové funkce a opravy
Nové funkce a opravy vydané pro Azure zásobníku integrované systémy verzi 1803 použít pro Azure zásobníku Development Kit. Najdete v článku [nové funkce](.\.\azure-stack-update-1803.md#new-features) a [pevné problémy](.\.\azure-stack-update-1803.md#fixed-issues) části 1803 zásobník Azure aktualizovat poznámky k verzi pro podrobnosti.  
> [!IMPORTANT]    
> Některé z uvedených v položek **nové funkce** a **pevné problémy** části se vztahují pouze k zásobník Azure integrované systémy.

### <a name="changes"></a>Změny
- Způsob, jak změnit stav na nově vytvořený nabídku z *privátní* k *veřejné* nebo *vyřazení* došlo ke změně. Další informace najdete v tématu [vytvořit nabídku](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro Azure zásobníku Development Kit použít https://aka.ms/azurestackforum.    

- <!-- 2050709 --> V portálu pro správu není možné upravit úložiště metriky pro služby objektů Blob, tabulka služby nebo služby front. Přejděte do úložiště, a potom vyberte objekt blob, tabulka nebo fronty služby dlaždice, otevře se nové okno zobrazující graf metriky pro tuto službu. Pokud pak vyberte Úpravy z horní části dlaždici grafu metriky, v okně upravit graf otevře, ale nezobrazí možnost upravit metriky.  

- Zobrazí **je vyžadována aktivace** upozornění s výzvou k registraci vaší Azure zásobníku Development Kit. Toto chování je očekávané.

- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.

- Nelze zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení ověřte oprávnění pomocí prostředí PowerShell.

- V řídicím panelu portálu pro správu se nezdaří aktualizace dlaždice zobrazíte informace o aktualizacích. Chcete-li vyřešit tento problém, klikněte na dlaždici jej aktualizovat.

-   V portálu pro správu může se zobrazit Kritická výstraha pro součást Microsoft.Update.Admin. Název výstrahy, popis a nápravu všech zobrazit jako:  
    - *Chyba: šablonu pro FaultType ResourceProviderTimeout nebyl nalezen.*

    Tuto výstrahu můžete ignorovat. 

- V portálu pro správu i v portálu user portal, v okně Přehled nepodaří načíst, když vyberete v okně Přehled pro účty úložiště, které byly vytvořeny ve starší verzi rozhraní API (Příklad: 2015-06-15). 

  Jako alternativní řešení, pomocí prostředí PowerShell spustit **Start-ResourceSynchronization.ps1** skript k obnovení přístup podrobnosti o účtu úložiště. [Skript je k dispozici z Githubu]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)a je nutné spustit s přihlašovací údaje Správce služby na hostiteli development kit Pokud použijete ASDK.  

- **Stav služby** okno nepodaří načíst. Když otevřete okno Stav služby je správce nebo uživatel portálu Azure zásobníku zobrazí chybu a nenačte informace. Toto je očekávané chování. I když můžete vybrat a otevřete stav služby, tato funkce ještě není k dispozici, ale bude implementované v budoucí verzi systému Azure zásobníku.


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

- Na portálu správy Azure zásobníku může zobrazit kritickou výstrahu s názvem **čekající na vyřízení vypršení platnosti certifikátu externí**.  Tuto výstrahu můžete ignorovat a ovlivnit operace Azure zásobníku Development Kit. 


#### <a name="marketplace"></a>Marketplace
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.
 
#### <a name="compute"></a>Compute
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

- Když vytvoříte virtuální počítače na portálu pro uživatele Azure zásobníku, portál zobrazí nesprávný počet datových disků, které můžete připojit k řady DS virtuálních počítačů. Řady DS virtuální počítače zvládne tolik datových disků jako konfiguraci Azure.

- Když image virtuálního počítače se nepodaří vytvořit, položku se nezdařilo, nelze odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

-  Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování časový limit namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

- <!-- 1662991 --> Diagnostika virtuálních počítačů Linux není podporována v zásobníku Azure. Při nasazení virtuálního počítače s Linuxem pomocí diagnostiky virtuálních počítačů, které jsou povolené, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem pomocí nastavení pro diagnostiku. 


#### <a name="networking"></a>Sítě
- V části **sítě**, pokud kliknete na tlačítko **připojení** nastavit připojení k síti VPN **VNet-to-VNet** je uveden jako typ možné připojení. Nevybírejte tuto možnost. V současné době pouze **Site-to-site (IPsec)** možnost je podporována.

- Po vytvoření virtuálního počítače a související s veřejnou IP adresu, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení pravděpodobně fungovat, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původní virtuální počítač a ne do nového.



- Azure zásobníku podporuje jediný *brány místní sítě* podle IP adresy. To platí ve všech předplatných klienta. Po vytvoření první místní síťové brány připojení, další pokusy o vytvoření prostředku brány místní sítě se stejnou adresou IP jsou blokovány.

- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna na vlastní selhání serveru DNS. Aktualizované nastavení nejsou nabídnutých do virtuálních počítačů v této virtuální sítě.
 
- Azure zásobník nepodporuje přidávání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.



#### <a name="sql-and-mysql"></a>SQL a MySQL 
- Může trvat až jednu hodinu, než uživatelé mohou vytvářet databází v nové SQL nebo MySQL SKU.

- Databázi hostitelské servery musí být vyhrazená pro použití prostředků zprostředkovatele a uživatelského úlohami. Nelze použít instanci, která se používá jakékoli další příjemce, včetně aplikační služby.

- <!-- IS, ASDK --> Speciální znaky, včetně mezery a tečky, nejsou podporovány v **rodiny** nebo **vrstvy** názvy při vytváření SKU pro poskytovatele prostředků SQL a databáze MySQL.

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





