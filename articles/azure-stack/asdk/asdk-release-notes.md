---
title: Zpráva k vydání verze Microsoft Azure Stack Development Kit | Dokumentace Microsoftu
description: Vylepšení, oprav a známých problémů pro Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 12/21/2018
ms.openlocfilehash: c60ba4f4106ddd0c3fc643288894fb55d3d27f8c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816454"
---
# <a name="asdk-release-notes"></a>Zpráva k vydání verze ASDK 
 
Tento článek obsahuje informace o vylepšení, oprav a známé problémy v Azure Stack Development Kit (ASDK). Pokud si nejste jistí, kterou verzi používáte, můžete si [použití portálu ke kontrole](../azure-stack-updates.md#determine-the-current-version).

> Udržujte si s tím, co se přihlásíte k odběru je novinkou ASDK [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanálu](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-118110101"></a>Sestavení 1.1811.0.101

### <a name="new-features"></a>Nové funkce

Toto sestavení obsahuje následující vylepšení a oprav pro Azure Stack:  

- Existuje sada nový minimální a doporučené hardwarové a softwarové požadavky pro ASDK. Tyto nové doporučené specifikace jsou dokumentovány v článku [aspektech plánování nasazení Azure Stack](asdk-deploy-considerations.md). Jako platformu Azure Stack se vyvinula, další služby jsou teď k dispozici a může vyžadovat další prostředky. Zvýšená specifikace odrážet tato revidované doporučení.

### <a name="fixed-issues"></a>Oprava potíží

<!-- TBD - IS ASDK --> 
- Opravili jsme problém, ve kterém veřejná data IP adres pro měření využití jsme si ukázali, stejné **EventDateTime** hodnotu pro každý záznam místo **TimeDate** razítko, které ukazuje, kdy se záznam vytvořil. Teď můžete tato data pro přesné monitorování použití veřejné IP adresy.

<!-- 3099544 – IS, ASDK --> 
- Opravili jsme problém, ke které došlo při vytváření nového virtuálního počítače (VM) pomocí portálu Azure Stack. Výběr velikosti virtuálního počítače způsobila sloupci USD/měsíc pro zobrazení **není k dispozici** zprávy. Tento sloupec nezobrazuje; zobrazení virtuální počítač cenové sloupec není podporován ve službě Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Opravili jsme problém, ve kterém portálu správce při přístupu k podrobnosti libovolné předplatné uživatele po zavření okna a kliknutím na **poslední**, nenacházely se název předplatného uživatele. Název předplatného uživatele se teď zobrazí.

<!-- 3060156 - IS ASDK --> 
- Opravili jsme problém, správce i uživatele portálech: Kliknutím na nastavení portálu a následným výběrem **odstranit všechna nastavení a privátní řídicí panely** nebude fungovat podle očekávání a se zobrazí oznámení o chybě. 

<!-- 2930799 - IS ASDK --> 
- Opravili jsme problém na portálech pro správce i uživatele: v části **všechny služby**, asset **plány DDoS protection** byl nesprávně uveden, když není k dispozici ve službě Azure Stack.
 
<!--2760466 – IS  ASDK --> 
- Opravili jsme problém, ke které došlo při instalaci nové prostředí Azure Stack v němž výstraha informující **vyžadována aktivace** nezobrazovala. Nyní správně zobrazuje.

<!--1236441 – IS  ASDK --> 
- Opravili jsme problém, která bránila použití zásady RBAC pro skupinu uživatelů při použití služby AD FS.

<!--3463840 - IS, ASDK --> 
- Oprava potíží s infrastruktura zálohování nedaří zálohovat z důvodu nedostupná souborového serveru od veřejnou síť virtuálních IP adres. Tato oprava infrastruktura zálohování služba přejde zpět k síti infrastruktury veřejných. Pokud jste nainstalovali nejnovější Azure Stack opravu hotfix pro 1809, která řeší tento problém, 1811 aktualizace nebude provádět žádné další změny. 

<!-- 2967387 – IS, ASDK --> 
- Opravili jsme problém, ve kterém se účet použitý k přihlášení k portálu správce nebo uživatele služby Azure Stack zobrazovat jako **neidentifikovaný uživatel**. Tato zpráva se zobrazí, když účet buď neměl *první* nebo *poslední* zadaný název.   

<!--  2873083 - IS ASDK --> 
- Opravili jsme problém, který pomocí portálu k vytvoření virtuálního počítače škálovací sady (VMSS) *velikost instance* rozevírací seznam se nenačetl správně při použití aplikace Internet Explorer. Tento prohlížeč teď funguje správně.  

<!-- 3190553 - IS ASDK -->
- Opravili jsme problém, který vygeneroval hlučného výstrahy indikující, že byla infrastruktury Instance Role není k dispozici nebo uzel jednotek škálování byla ve stavu offline.

### <a name="known-issues"></a>Známé problémy

#### <a name="portal"></a>Portál  

<!-- 2930820 - IS ASDK --> 
- Na portálech pro správce i uživatele Pokud dáte vyhledat "Dockeru," položka je nesprávně vrátila. Není k dispozici ve službě Azure Stack. Pokud se pokusíte k jeho vytvoření, zobrazí se okno s uvedením chyby. 

<!-- 2931230 – IS  ASDK --> 
- Plány, které jsou přidány na předplatné uživatele jako doplňkový plán nelze odstranit, i když odebrat plán ze předplatné uživatele. Plán zůstane, dokud se také odstraní předplatné, které odkazují na doplňkový plán. 

<!-- TBD - IS ASDK --> 
- Se nemá používat dva typy pro správu předplatného, které byly představeny s nástrojem verzi 1804. Typy předplatného jsou **měření předplatné**, a **využití předplatného**. Tyto typy předplatného jsou viditelné v novým prostředím Azure Stack od verze 1804, ale ještě nejsou připravené k použití. By měla dál používat **výchozí zprostředkovatel** typu předplatného.

<!-- TBD - IS ASDK --> 
- Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

<!-- TBD - IS ASDK --> 
- Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.

#### <a name="compute"></a>Compute 

<!-- 3235634 – IS, ASDK -->
- K nasazení virtuálních počítačů s velikostí, který obsahuje **v2** přípony; například **Standard_A2_v2**, zadejte příponu jako **Standard_A2_v2** (malá písmena v). Nepoužívejte **Standard_A2_V2** (velká písmena V). To funguje v globální Azure a nekonzistence ve službě Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Při použití [ **přidat AzsPlatformImage** rutiny](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), je nutné použít **- OsUri** parametr jako identifikátor URI, kde je odeslána na disk účtu úložiště. Pokud používáte místní cesta na disku, rutina selže s následující chybou: *Dlouho běžící operace se nezdařila se stavem "Failed"*. 

<!--  2795678 – IS, ASDK --> 
- Při použití portálu k vytvoření virtuálních počítačů (VM) o velikosti virtuálních počítačů úrovně premium (DS, Ds_v2, služby FS, FSv2) virtuální počítač se vytvoří v účtu úložiště úrovně standard. Vytvoření účtu úložiště úrovně standard neovlivňuje funkčně vstupně-výstupních operací, nebo fakturace. Můžete bezpečně ignorovat upozornění, že: **Rozhodli jste se použít standardní disk o velikosti, která podporuje prémiové disky. To může mít vliv na výkon operačního systému a nedoporučuje se používat. Zvažte raději použití storage úrovně premium (SSD).**

<!-- 2967447 - IS, ASDK --> 
- Prostředí pro vytváření virtuálního počítače škálovací sady (VMSS) poskytuje založené na CentOS 7.2 jako možnost pro nasazení. Vzhledem k tomu, že obrázek není k dispozici ve službě Azure Stack, vyberte jiný operační systém pro vaše nasazení nebo pomocí šablony Azure Resource Manageru zadáním jiné image CentOS, který byl stažen před jejich nasazením na Marketplace operátorem.  

<!-- TBD - IS ASDK --> 
- Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.  

<!-- 2724961- IS ASDK --> 
- Když se zaregistrujete **Microsoft.Insight** poskytovatele prostředků v nastavení předplatného a vytvoření virtuálního počítače s Windows pomocí hostovaného operačního systému diagnostických povolena, graf procento využití procesoru na stránce Přehled virtuálních počítačů nezobrazuje data metrik.

   Najít data metrik, jako je například grafu procento využití procesoru pro virtuální počítač, přejděte do okna metrik a zobrazit všechny podporované metriky hosta virtuálního počítače Windows.

<!-- 3507629 - IS, ASDK --> 
- Spravované disky vytvoří dva nové [compute typy kvót](../azure-stack-quota-types.md#compute-quota-types) omezit maximální kapacita spravované disky, které je možné zřídit. Ve výchozím nastavení 2048 GiB přidělených pro každý typ kvóty spravované disky. Však může dojít k následujícím problémům:

   - Vytvořené před aktualizací. 1808 kvót kvóta Managed Disks se zobrazí hodnoty 0 na portálu správce, i když je přiděleno 2048 GiB. Můžete zvýšit nebo snížit hodnotu podle skutečných potřeb a nově nastavené hodnoty kvóty na přepíše výchozí hodnotu 2048 GiB.
   - Při aktualizaci hodnoty kvóty na hodnotu 0, je ekvivalentní výchozí hodnotu 2 048 GB. Jako alternativní řešení nastavte hodnoty kvóty na 1.

<!-- TBD - IS ASDK --> 
- Po použití 1811 aktualizace, může dojít k následujícím problémům při nasazování virtuálních počítačů se spravovanými disky:

   1. Pokud předplatné bylo vytvořeno před aktualizací. 1808, nasazení virtuálního počítače se spravovanými disky může selhat s interní chybovou zprávu. Chcete chybu vyřešit, postupujte podle těchto kroků pro každé předplatné:
      1. Portál pro klienty, přejděte na **předplatná** a vyhledejte předplatné. Klikněte na tlačítko **poskytovatelů prostředků**, klikněte na **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
      2. V rámci stejného předplatného, přejděte na **řízení přístupu (IAM)** a ověřte, že **Azure Stack – spravovaný Disk** je uvedena.
   2. Pokud jste nakonfigurovali prostředí s více tenanty, nasazování virtuálních počítačů v rámci služby předplacené asociovaná s adresářem hosta může selhat s interní chybovou zprávu. Pokud chcete chybu vyřešit, postupujte podle kroků v [v tomto článku](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) změna konfigurace všech vašich adresářů hosta.

#### <a name="networking"></a>Sítě

<!-- 1766332 - IS ASDK --> 
- V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

<!-- 16309153 - IS ASDK --> 
- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.

<!-- 2529607 - IS ASDK --> 
- Během Azure Stack *tajný klíč otočení*, je období, ve kterém veřejné IP adresy nedostupné přibližně 2 až 5 minut.

<!-- 2664148 - IS ASDK --> 
-   Ve scénářích, kde tenanta je přístup ke své virtuální počítače s použitím tunelu S2S VPN kterými se mohou setkat scénář, kde pokusy o připojení selhat, pokud místní podsíť byl přidán do místní síťové brány po již vytvořit bránu. 

#### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

<!-- #### Usage -->  

<!-- #### Identity -->

## <a name="build-11809090"></a>Sestavení 1.1809.0.90

### <a name="new-features"></a>Nové funkce
Toto sestavení obsahuje následující vylepšení a oprav pro Azure Stack.  

<!--  2712869   | IS  ASDK -->  
- **Klient služby Azure Stack syslog (obecná dostupnost)** tento klient umožňuje předávání auditů, upozornění a protokoly zabezpečení spojených s infrastrukturou Azure Stack na syslog serveru nebo zabezpečení informací a událostí (SIEM) software pro správu externí do služby Azure Stack. Klient syslog nyní podporuje určující port, na kterém naslouchá syslog server.

V této verzi klienta syslog je obecně dostupná, a můžou používat v produkčním prostředí.

Další informace najdete v tématu [předávání syslog Azure Stack](../azure-stack-integrate-security.md).

### <a name="fixed-issues"></a>Oprava potíží

<!-- TBD - IS ASDK -->
- Na portálu jsou nyní grafu paměti reporting zdarma nebo používané kapacity přesná. Nyní můžete kolik virtuálních počítačů, budete moct vytvořit spolehlivěji předpovídat.

<!-- TBD - IS ASDK --> 
- Opravili jsme problém, ve kterém jste vytvořili virtuální počítače na portálu user portal Azure Stack, a na portálu zobrazuje nesprávný počet datových disků, které můžete připojit k DS-series virtuálních počítačů. Virtuálních počítačů řady DS může obsahovat libovolný počet datových disků jako konfiguraci Azure.

- <!-- 2702741 -  IS, ASDK --> Oprava potíží, ve které veřejné IP adresy, které se nasadily pomocí dynamického přidělování metoda nebyly zaručit zachování po zastavte a Navraťte vydání. Nyní jsou zachovány.

- <!-- 3078022 - IS, ASDK --> Pokud se virtuální počítač zastavit a uvolnit před. 1808 ho nebylo možné znovu alokovat po 1808 aktualizaci.  Tento problém je vyřešen v 1809. Instance, které byly v tomto stavu a nebylo možné spustit, může být spuštěný v 1809 s touto opravou. Tento problém vyřešit zabrání také nevyskytla.

<!-- TBD -  IS ASDK --> 
- Selhání vytvoření image virtuálního počítače neúspěšné položky, který se nedá odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení vytvořte nové image virtuálního počítače s fiktivní virtuálního pevného disku, který je možné vytvářet přes Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-oprava SizeBytes – 1 GB). Tento proces by měla vyřešit problém, který zabraňuje odstranění položky. Pak 15 minut po vytvoření fiktivní image můžete úspěšně ho odstranit.

  Potom můžete opakovat stahování image virtuálního počítače, která původně selhala.

- **Různé opravy** výkonu, stability, zabezpečení a operační systém, který se používá ve službě Azure Stack

### <a name="changes"></a>Změny

### <a name="known-issues"></a>Známé problémy

#### <a name="portal"></a>Portál  

<!-- 2515955   | IS ,ASDK--> 
- *Všechny služby* nahradí *další služby* na portálech správce a uživatele Azure stacku. Teď můžete použít *všechny služby* jako alternativu k navigaci na portálech Azure Stack stejným způsobem jako v Azure Portal.

<!--  TBD – IS, ASDK --> 
- *Základní A* velikostí virtuálních počítačů byly ukončeny pro [vytvoření škálovací sady virtuálních počítačů](../azure-stack-compute-add-scalesets.md) (VMSS) prostřednictvím portálu. Pokud chcete vytvořit VMSS se tato velikost, pomocí Powershellu nebo šablony.

#### <a name="health-and-monitoring"></a>Monitorování stavu a

<!-- 1264761 - IS ASDK -->  
- Může se zobrazit upozornění *stavu řadiče* komponenta, která mají následující podrobnosti:  

   Upozornění #1:
   - JMÉNO:  Infrastrukturu role není v pořádku
   - ZÁVAŽNOST: Upozornění
   - KOMPONENTY: Stav kontroleru
   - POPIS: Kontroler stavu prezenčního signálu skener není k dispozici. To může mít vliv sestav o stavu a metriky.  

  Upozornění #2:
   - JMÉNO:  Infrastrukturu role není v pořádku
   - ZÁVAŽNOST: Upozornění
   - KOMPONENTY: Stav kontroleru
   - POPIS: Stav řadiče skener selhání není k dispozici. To může mít vliv sestav o stavu a metriky.

  Obě výstrahy můžete bezpečně ignorovat a bude automaticky ukončena v čase.  

<!-- 2368581 - IS. ASDK --> 
- Operátory Azure stacku, pokud se zobrazí upozornění na nedostatek paměti a virtuální počítače klientů selhání nasazení *Chyba při vytváření virtuálních počítačů Fabric*, je možné, že toto razítko Azure Stack je nedostatek dostupné paměti. Použití [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) nejlépe pochopit dostupné kapacity pro vaše úlohy.


#### <a name="compute"></a>Compute 

<!-- 3164607 – IS, ASDK -->
- Opětovné odpojený disk do stejného virtuálního počítače (VM) se stejným názvem a logická jednotka se nezdaří s chybou jako **nelze připojit datový disk "datadisk" k virtuálnímu počítači "vm1"**. Protože disk je v tuto chvíli odpojuje nebo poslední operace odpojení nebyla úspěšná, dojde k chybě. Počkejte, dokud je zcela odpojit disk a potom opakujte operaci nebo odstraňte nebo odpojte disk explicitně znovu. Alternativním řešením je se znovu připojit s jiným názvem nebo na různých logických jednotkách. 

<!-- 3235634 – IS, ASDK -->
- K nasazení virtuálních počítačů s velikostí, který obsahuje **v2** přípony; například **Standard_A2_v2**, zadejte příponu jako **Standard_A2_v2** (malá písmena v). Nepoužívejte **Standard_A2_V2** (velká písmena V). To funguje v globální Azure a nekonzistence ve službě Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Při vytváření nového virtuálního počítače (VM) pomocí portálu Azure Stack, a vyberte velikost virtuálního počítače, zobrazí se sloupec USD za měsíc s **není k dispozici** zprávy. Tento sloupec by se neměl zobrazit; zobrazení virtuální počítač cenové sloupec není podporován ve službě Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Při použití [ **přidat AzsPlatformImage** rutiny](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), je nutné použít **- OsUri** parametr jako identifikátor URI, kde je odeslána na disk účtu úložiště. Pokud používáte místní cesta na disku, rutina selže s následující chybou: *Dlouho běžící operace se nezdařila se stavem "Failed"*. 

<!--  2966665 – IS, ASDK --> 
- Připojení SSD spravované datové disky úrovně premium velikosti disku virtuálního počítače (DS, DSv2, Fs, Fs_V2) selže s chybou:  *Nepovedlo se aktualizovat disky pro virtuální počítač 'vmname' Chyba: Požadovaná operace nejde provést, protože typ účtu úložiště 'Premium_LRS' není podporován pro velikost virtuálního počítače "Standard_DS/Ds_V2/FS/Fs_v2)*

   Chcete-li tento problém obejít, použijte *Standard_LRS* datových disků místo *Premium_LRS disky*. Použití *Standard_LRS* datových disků nedojde ke změně vstupně-výstupních operací nebo fakturační náklady.  

<!--  2795678 – IS, ASDK --> 
- Při použití portálu k vytvoření virtuálních počítačů (VM) o velikosti virtuálních počítačů úrovně premium (DS, Ds_v2, služby FS, FSv2) virtuální počítač se vytvoří v účtu úložiště úrovně standard. Vytvoření účtu úložiště úrovně standard neovlivňuje funkčně vstupně-výstupních operací, nebo fakturace. 

   Můžete bezpečně ignorovat upozornění, že: *Rozhodli jste se použít standardní disk o velikosti, která podporuje prémiové disky. To může mít vliv na výkon operačního systému a nedoporučuje se používat. Zvažte raději použití storage úrovně premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Škálovací sada virtuálních počítačů (VMSS) vytvořte prostředí založené na CentOS 7.2 poskytuje jako možnost pro nasazení. Vzhledem k tomu, že obrázek není k dispozici ve službě Azure Stack, vyberte jiný operační systém pro vaše nasazení, nebo pomocí šablony Azure Resource Manageru zadáním jiné image CentOS, který byl stažen před jejich nasazením na Marketplace operátorem.

<!-- TBD -  IS ASDK --> 
- Nastavení škálování pro škálovací sady virtuálních počítačů nejsou k dispozici na portálu. Jako alternativní řešení můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdíly mezi verzemi prostředí PowerShell, je nutné použít `-Name` parametr namísto `-VMScaleSetName`.



<!-- TBD -  IS ASDK --> 
- Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

<!-- 1662991 - IS ASDK --> 
- Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.

<!-- 2724961- IS ASDK --> 
- Když se zaregistrujete **Microsoft.Insight** poskytovatele prostředků v nastavení předplatného a vytvoření virtuálního počítače s Windows pomocí hostovaného operačního systému diagnostických povolena, graf procento využití procesoru na stránce Přehled virtuálního počítače nebudou moci zobrazit data metrik.
 
  Graf procento využití procesoru pro virtuální počítač, najdete **metriky** blade a zobrazit všechny podporované Windows virtuálního počítače hosta metriky.

 

#### <a name="networking"></a>Sítě
<!-- 1766332 - IS, ASDK --> 
- V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

<!-- 16309153 -  IS ASDK --> 
- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.

<!-- 2702741 -  IS ASDK --> 
- Chcete-li být zachována po zastavte a Navraťte vydání není zaručena veřejné IP adresy, které jsou nasazeny pomocí metody dynamického přidělení.

<!-- 2529607 - IS ASDK --> 
- Během Azure Stack *tajný klíč otočení*, je období, ve kterém veřejné IP adresy nedostupné přibližně 2 až 5 minut.

<!-- 2664148 - IS ASDK --> 
- Ve scénářích, kde tenanta je přístup ke své virtuální počítače s použitím tunelu S2S VPN kterými se mohou setkat scénář, kde pokusy o připojení selhat, pokud místní podsítě byl přidán do místní síťové brány po již vytvořit bránu. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

<!-- TBD - IS ASDK --> 
- Aby bylo možné horizontální navýšení kapacity infrastruktury (pracovních procesů, správy, role front-endu), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.  


#### <a name="usage"></a>Využití  
<!-- TBD -  IS ASDK --> 
- Data o využití veřejné IP adresy využití měřičů zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítko, které ukazuje, kdy se záznam vytvořil. V současné době nelze pomocí těchto dat pro monitorování přesné použití veřejné IP adresy.

<!-- #### Identity -->

## <a name="build-11808097"></a>Sestavení 1.1808.0.97

### <a name="new-features"></a>Nové funkce
Toto sestavení obsahuje následující vylepšení a oprav pro Azure Stack.  

<!-- 1658937 | ASDK, IS --> 
- **Spustit zálohování podle předem daného plánu** – jako zařízení, Azure Stack můžete teď automaticky aktivovat zálohování infrastruktury pravidelně a Eliminujte zásahu člověka. Azure Stack se také automaticky vyčištění z externí sdílené složky pro zálohy, které jsou starší než doba uchování definované. Další informace najdete v tématu [povolit zálohování pro Azure Stack s prostředím PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Čas do celkový čas zálohování za přenosy dat přidaný.** Další informace najdete v tématu [povolit zálohování pro Azure Stack s prostředím PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS --> 
- **Záložní kapacita externí nyní zobrazuje správné kapacity z externí sdílené složky.** (Dříve to bylo pevně zakódovat až 10 GB.) Další informace najdete v tématu [povolit zálohování pro Azure Stack s prostředím PowerShell](../azure-stack-backup-enable-backup-powershell.md).
 
<!-- 2753130 |  IS, ASDK   -->  
- **Šablony Azure Resource Manageru teď podporují elementu podmínku** – teď můžete nasadit prostředků v šabloně Azure Resource Manageru pomocí podmínku. Můžete navrhnout šablonu k nasazení prostředků na základě podmínky, jako jsou hodnocení, pokud hodnota parametru je k dispozici. Informace o použití šablony jako podmínku, naleznete v tématu [podmíněné nasazení prostředku](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) a [části proměnných šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) v dokumentaci k Azure. 

   Můžete také použít šablony, které [nasadit prostředky do více než jedno předplatné nebo skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **Podpora verzí prostředků Microsoft.Network rozhraní API se aktualizovala** zahrnující podporu pro rozhraní API verze 2017-10-01 z 2015-06-15 pro Azure Stack síťovým prostředkům. Podpora verzí prostředků mezi 2017-10-01 a 2015-06-15 není součástí této verze. Najdete [důležité informace týkající se služby Azure Stack sítě](../user/azure-stack-network-differences.md) pro rozdíly funkcí.

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack má přidanou podporu pro zpětné vyhledávání DNS pro externí koncové koncové body infrastruktury Azure stacku** (to je pro portál, adminportal, správu a adminmanagement). To umožňuje službě Azure Stack je externí koncový bod názvů přeložit IP adresu.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack teď podporuje přidání další síťové rozhraní existujícímu virtuálnímu počítači.**  Tato funkce je dostupná prostřednictvím portálu, Powershellu a rozhraní příkazového řádku. Další informace najdete v tématu [přidání nebo odebrání síťových rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) v dokumentaci k Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Zlepšení přesnosti a odolnost proti chybám se provedly sítě měřiče využití**. Měření využití sítě jsou nyní přesnější a rozdělení předplatná účet pozastavit, výpadek období a konflikty časování.

<!-- 2297790 | IS, ASDK -->  
- **Vylepšení klienta služby Azure Stack syslog (funkce preview)**. Tento klient umožňuje předávání auditu a protokolů spojených s infrastrukturou Azure Stack na syslog serveru nebo zabezpečení informací a událostí (SIEM) software pro správu externí do služby Azure Stack. Klient syslog nyní podporuje protokol TCP pomocí prostého textu nebo šifrování TLS 1.2, druhá možnost je výchozí konfigurace. Připojení TLS lze nakonfigurovat buď jen pro server, nebo vzájemné ověřování.

  Nakonfigurujte syslog server jak komunikuje klienta syslog (jako je protokol, šifrování a ověřování), použijte rutinu Set-SyslogServer. Tato rutina je k dispozici z privileged koncového bodu (období). 

- <!-- ASDK --> **Položky galerie pro Škálovací sady virtuálních počítačů jsou teď integrované**.  Škálovací sada virtuálních počítačů položky galerie jsou teď k dispozici na portálech uživatele a správce bez nutnosti si je stáhnout. 

- <!-- IS, ASDK --> **Škálovací sada virtuálních počítačů škálování**.  Můžete použít portál pro [škálování Škálovací sady virtuálních počítačů](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).   

- <!-- 2489570 | IS ASDK--> **Podpora pro vlastní konfigurace zásad IPSec/IKE** pro [bran VPN Gateway ve službě Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Položka marketplace Kubernetes**. Teď můžete nasadit clustery Kubernetes pomocí [položky Kubernetes Marketplace](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Uživatele můžete vybrat položku Kubernetes a vyplňte několik parametrů k nasazení clusteru Kubernetes do služby Azure Stack. Účelem šablony je usnadňují uživatelům, aby instalační program nasazení Kubernetes pro vývoj/testování v několika krocích.

<!-- ####### | IS, ASDK -->  
- **Azure Resource Manageru obsahuje název oblasti.** V této verzi načtené z Azure Resource Manageru objekty teď bude zahrnovat atribut názvu oblasti. Pokud stávající skript prostředí PowerShell přímo předá objekt jiná rutina, skript může vést k chybě a selhání. To je chování kompatibilní s Azure Resource Manageru a vyžaduje odečítané atribut oblasti volajícího klienta. Další informace o Azure Resource Manageru najdete v tématu [dokumentaci k Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/).

<!-- TBD | IS, ASDK -->  
- **Přesun předplatných mezi delegované poskytovatele.** Mezi nové nebo existující delegované poskytovatele odběry, které patří do stejného tenanta adresáře nyní se můžete přesunout předplatné. Předplatná, která patří do předplatného výchozí zprostředkovatel můžete také přesunout do delegovaný předplatná poskytovatele ve stejném tenantu Active Directory. Další informace najdete v části [delegování nabídek v Azure stacku](../azure-stack-delegated-provider.md).
 
<!-- 2536808 IS ASDK --> 
- **Vylepšené čas vytvoření virtuálního počítače** pro virtuální počítače, které jsou vytvořené pomocí Image stáhnout z webu Azure marketplace.

### <a name="fixed-issues"></a>Oprava potíží
- <!-- IS ASDK--> Opravili jsme problém pro vytvoření dostupnosti na portálu, což vedlo sada domény selhání a aktualizační doména 1.

<!-- TBD | ASDK, IS --> 
- Různá vylepšení provedly se proces aktualizace provádět spolehlivější. Kromě toho byly provedeny opravy základní infrastruktury, což zvyšuje vyprázdnění uzlu, a tím minimalizovat potenciální výpadek pro úlohy během aktualizace.

<!--2292271 | ASDK, IS --> 
- Opravili jsme problém kde upravené kvótu neuplatnilo ke stávajícím předplatným.  Teď když plán přidružené předplatné uživatele, zvýšit kvótu pro síťový prostředek, který je součástí nabídky nové omezení platí pro již existujících předplatných, jakož i nová předplatná.

<!-- 2448955 | IS ASDK --> 
- Teď můžete úspěšně dotazovat protokolů aktivit pro systémy, které jsou nasazené v časovém pásmu UTC + N.    

<!-- 2319627 |  ASDK, IS --> 
- Předběžné kontroly zálohování konfiguračních parametrů (cesta/uživatelské jméno/heslo/šifrovací klíč) už nastaví nesprávné nastavení pro konfiguraci zálohování. (Dříve, nesprávné nastavení byly nastaveny do zálohy a zálohy by dojde k selhání při aktivaci.)

<!-- 2215948 |  ASDK, IS --> 
- Zálohování seznamu nyní aktualizuje, když je ručně odstranit zálohy z externí sdílené složky.

<!-- 2360715 |  ASDK, IS -->  
- Při nastavování integrace datových center už přístup k souboru metadat služby AD FS ze sdílené složky. Další informace najdete v tématu [nastavení integrace služby AD FS tím, že poskytuje soubor metadat federace](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Opravili jsme problém, že mu uživatelům přiřadit stávající veřejnou IP adresu, která byla dříve přidělili síťové rozhraní nebo nástroj pro vyrovnávání zatížení na nové síťové rozhraní nebo nástroj pro vyrovnávání zatížení.  

<!-- 2551834 - IS, ASDK --> 
- Když vyberete *přehled* pro účet úložiště na portálech na správce nebo uživatele *Essentials* podokně správně teď zobrazuje všechny požadované informace. 

<!-- 2551834 - IS, ASDK --> 
- Když vyberete *značky* pro účet úložiště na portálech na správce nebo uživatele, zobrazí informace o správně.

<!-- TBD - IS ASDK --> 
- Tato verze služby Azure Stack řeší problém, která bránila použití aktualizací ovladače z balíčků rozšíření výrobce OEM.

<!-- 2055809- IS ASDK --> 
- Opravili jsme problém, který zabránil můžete odstraňovat virtuální počítače v okně výpočty, když virtuální počítač se nepodařilo vytvořit.  

<!--  2643962 IS ASDK -->  
- Upozornění pro *nízká kapacita paměti* již nezobrazuje správně.

<!--  TBD ASDK --> 
- Virtuální počítač, který je hostitelem koncového bodu oprávnění (období) bylo zvýšeno na 4GB. V ASDK je tento virtuální počítač s názvem AzS-ERCS01.

- <!--  TBD – IS, ASDK --> *Základní A* velikostí virtuálních počítačů byly ukončeny pro [vytvoření škálovací sady virtuálních počítačů](../azure-stack-compute-add-scalesets.md) (VMSS) prostřednictvím portálu. Pokud chcete vytvořit VMSS se tato velikost, pomocí Powershellu nebo šablony. 

### <a name="known-issues"></a>Známé problémy

#### <a name="portal"></a>Portál  
- <!-- 2967387 – IS, ASDK --> Účet, který používáte k přihlášení k portálu Azure Stack správce nebo uživatel zobrazí jako **neidentifikovaný uživatel**. K tomu dojde, pokud nemá buď *první* nebo *poslední* zadaný název. Chcete-li tento problém obejít, upravte uživatelský účet první nebo poslední název. Musíte pak Odhlásit se a přihlaste se zpět na portál. 

-  <!--  2873083 - IS ASDK --> Při použití na portálu vytvořit škálovací sadu virtuálních počítačů (VMSS), nastavte *velikost instance* rozevírací seznam nenačte správně při použití aplikace Internet Explorer. Chcete-li tento problém vyřešit, použijte jiný prohlížeč při použití portálu k vytvoření VMSS.  

#### <a name="portal"></a>Portál  
<!-- 2931230 – IS  ASDK --> 
- Plány, které jsou přidány na předplatné uživatele jako doplňkový plán nelze odstranit, i když odebrat plán ze předplatné uživatele. Plán zůstane, dokud se také odstraní předplatné, které odkazují na doplňkový plán. 

<!--2760466 – IS  ASDK --> 
- Při instalaci nového prostředí Azure Stack, na kterém běží tato verze, upozornění, která informuje o *vyžadována aktivace* se nemusí zobrazit. [Aktivace](../azure-stack-registration.md) se vyžaduje, abyste mohli používat marketplace syndikace. 

<!-- TBD - IS ASDK --> 
- Se nemá používat dva typy pro správu předplatného, které byly představeny s nástrojem verzi 1804. Typy předplatného jsou **měření předplatné**, a **využití předplatného**. Tyto typy předplatného jsou **měření předplatné**, a **využití předplatného**. Tyto typy předplatného jsou viditelné v novým prostředím Azure Stack od verze 1804, ale ještě nejsou připravené k použití. By měla dál používat **předplatné poskytovatele. výchozí** typu.

<!-- 2403291 - IS ASDK --> 
- Nemusí mít užívání vodorovný posuvník v dolní části portály správce a uživatele. Pokud nemůžete použít vodorovný posuvník, left pomocí popisu cesty přejít na předchozí okno na portálu tak, že vyberete název okna je chcete zobrazit v seznamu s popisem cesty v horní části portálu.
  ![Navigace s popisem cesty](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

<!-- TBD -  IS ASDK --> 
- Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.

<!--  TBD | ASDK -->  
- Výchozí časové pásmo pro vaše nasazení Azure Stack získáte nastaví na čas UTC. Časové pásmo můžete vybrat při instalaci Azure Stack, ale to se automaticky vrátí k UTC jako výchozí během instalace.

#### <a name="health-and-monitoring"></a>Monitorování stavu a
<!-- 1264761 - IS ASDK -->  
- Může se zobrazit upozornění *stavu řadiče* komponenta, která mají následující podrobnosti:  

   Upozornění #1:
   - JMÉNO:  Infrastrukturu role není v pořádku
   - ZÁVAŽNOST: Upozornění
   - KOMPONENTY: Stav kontroleru
   - POPIS: Kontroler stavu prezenčního signálu skener není k dispozici. To může mít vliv sestav o stavu a metriky.  

  Upozornění #2:
   - JMÉNO:  Infrastrukturu role není v pořádku
   - ZÁVAŽNOST: Upozornění
   - KOMPONENTY: Stav kontroleru
   - POPIS: Stav řadiče skener selhání není k dispozici. To může mít vliv sestav o stavu a metriky.

  Obě výstrahy můžete bezpečně ignorovat a bude automaticky ukončena v čase.  

<!-- 2368581 - IS. ASDK --> 
- Operátory Azure stacku, pokud se zobrazí upozornění na nedostatek paměti a virtuální počítače klientů selhání nasazení *Chyba při vytváření virtuálních počítačů Fabric*, je možné, že toto razítko Azure Stack je nedostatek dostupné paměti. Použití [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) nejlépe pochopit dostupné kapacity pro vaše úlohy.

<!-- TBD - IS. ASDK --> 
- Při spuštění **testovací AzureStack** rutinu na privilegované koncový bod (období), **výkonu instancí Azure Stack infrastrukturu Role** test vydá zprávu UPOZORNIT ERCS virtuálního počítače. Můžete bezpečně ignorovat UPOZORNIT a pokračovat v používání ASDK.

#### <a name="compute"></a>Compute
<!-- 2494144 - IS, ASDK --> 
- Když vyberete velikost virtuálního počítače pro nasazení virtuálního počítače, některé velikosti virtuálních počítačů řady F-Series se nezobrazí jako součást modulu pro výběr velikost při vytváření virtuálního počítače. Tyto velikosti virtuálních počítačů se nezobrazí v oblasti pro výběr: *F8s_v2*, *F16s_v2*, *F32s_v2*, a *F64s_v2*.  
  Jako alternativní řešení použijte jednu z následujících metod nasazení virtuálního počítače. V každé metodě je třeba zadat velikost virtuálního počítače, které chcete použít.

- <!-- 3099544 – IS, ASDK --> Při vytváření nového virtuálního počítače (VM) pomocí portálu Azure Stack, a vyberte velikost virtuálního počítače, zobrazí se sloupec USD za měsíc s **není k dispozici** zprávy. Tento sloupec by se neměl zobrazit; zobrazení virtuální počítač cenové sloupec není podporován ve službě Azure Stack.

- <!-- 2869209 – IS, ASDK --> Při použití [ **přidat AzsPlatformImage** rutiny](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), je nutné použít **- OsUri** parametr jako identifikátor URI, kde je odeslána na disk účtu úložiště. Pokud používáte místní cesta na disku, rutina selže s následující chybou: *Dlouho běžící operace se nezdařila se stavem "Failed"*. 

- <!--  2966665 – IS, ASDK --> Připojení SSD spravované datové disky úrovně premium velikosti disku virtuálního počítače (DS, DSv2, Fs, Fs_V2) selže s chybou:  *Nepovedlo se aktualizovat disky pro virtuální počítač 'vmname' Chyba: Požadovaná operace nejde provést, protože typ účtu úložiště 'Premium_LRS' není podporován pro velikost virtuálního počítače "Standard_DS/Ds_V2/FS/Fs_v2)*

   Chcete-li tento problém obejít, použijte *Standard_LRS* datových disků místo *Premium_LRS disky*. Použití *Standard_LRS* datových disků nedojde ke změně vstupně-výstupních operací nebo fakturační náklady.  

- <!--  2795678 – IS, ASDK --> Při použití portálu k vytvoření virtuálních počítačů (VM) o velikosti virtuálních počítačů úrovně premium (DS, Ds_v2, služby FS, FSv2) virtuální počítač se vytvoří v účtu úložiště úrovně standard. Vytvoření účtu úložiště úrovně standard neovlivňuje funkčně vstupně-výstupních operací, nebo fakturace. 

   Můžete bezpečně ignorovat upozornění, že: *Rozhodli jste se použít standardní disk o velikosti, která podporuje prémiové disky. To může mít vliv na výkon operačního systému a nedoporučuje se používat. Zvažte raději použití storage úrovně premium (SSD).*

- <!-- 2967447 - IS, ASDK --> Škálovací sada virtuálních počítačů (VMSS) vytvořte prostředí založené na CentOS 7.2 poskytuje jako možnost pro nasazení. Vzhledem k tomu, že obrázek není k dispozici ve službě Azure Stack, vyberte jiný operační systém pro vaše nasazení nebo pomocí šablony ARM zadáním jiné image CentOS, který byl stažen před jejich nasazením na Marketplace operátorem.

<!-- TBD -  IS ASDK --> 
- Nastavení škálování pro škálovací sady virtuálních počítačů nejsou k dispozici na portálu. Jako alternativní řešení můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdíly mezi verzemi prostředí PowerShell, je nutné použít `-Name` parametr namísto `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Při vytváření virtuálních počítačů na portálu user portal Azure Stack na portálu zobrazuje nesprávný počet datových disků, které můžete připojit virtuální počítač řady D series. Všechny podporované řady D series virtuálních počítačů může obsahovat libovolný počet datových disků jako konfiguraci Azure.

<!-- TBD -  IS ASDK --> 
- Selhání vytvoření image virtuálního počítače neúspěšné položky, který se nedá odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení vytvořte nové image virtuálního počítače s fiktivní virtuálního pevného disku, který je možné vytvářet přes Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-oprava SizeBytes – 1 GB). Tento proces by měla vyřešit problém, který zabraňuje odstranění položky. Pak 15 minut po vytvoření fiktivní image můžete úspěšně ho odstranit.

  Potom můžete opakovat stahování image virtuálního počítače, která původně selhala.

<!-- TBD -  IS ASDK --> 
- Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

<!-- 1662991 - IS ASDK --> 
- Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.

<!-- 2724961- IS ASDK --> 
- Když se zaregistrujete **Microsoft.Insight** poskytovatele prostředků v nastavení předplatného a vytvoření virtuálního počítače s Windows pomocí hostovaného operačního systému diagnostických povolena, nezobrazí stránka s přehledem virtuálních počítačů typu data metrik. 

 

#### <a name="networking"></a>Sítě
<!-- 1766332 - IS, ASDK --> 
- V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě pomocí stejné IP adresy jsou zablokované.

<!-- 16309153 -  IS ASDK --> 
- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna vlastní selhání serveru DNS. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.

<!-- 2702741 -  IS ASDK --> 
- Chcete-li být zachována po zastavte a Navraťte vydání není zaručena veřejné IP adresy, které jsou nasazeny pomocí metody dynamického přidělení.

<!-- 2529607 - IS ASDK --> 
- Během Azure Stack *tajný klíč otočení*, je období, ve kterém veřejné IP adresy nedostupné přibližně 2 až 5 minut.

<!-- 2664148 - IS ASDK --> 
- Ve scénářích, kde tenanta je přístup ke své virtuální počítače s použitím tunelu S2S VPN kterými se mohou setkat scénář, kde pokusy o připojení selhat, pokud místní podsítě byl přidán do místní síťové brány po již vytvořit bránu. 


#### <a name="sql-and-mysql"></a>SQL a MySQL
<!-- TBD - ASDK --> 
- Databáze hostování servery musí být vyhrazená pro použití podle prostředku zprostředkovatele a uživatelského zatížení. Nelze použít instanci, která se používá v jakékoli další příjemce, včetně služeb App Services.

<!-- IS, ASDK --> 
- Speciální znaky, mezery a tečky, včetně nepodporuje **řady** název při vytváření SKU pro poskytovatele prostředků SQL nebo MySQL.

#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

<!-- TBD - IS ASDK --> 
- Aby bylo možné horizontální navýšení kapacity infrastruktury (pracovních procesů, správy, role front-endu), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.  

<!-- TBD - IS ASDK --> 
- App Service můžete nasadit jenom do *předplatné poskytovatele. výchozí* v tuto chvíli.

#### <a name="usage"></a>Využití  
<!-- TBD -  IS ASDK --> 
- Data o využití veřejné IP adresy využití měřičů zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítko, které ukazuje, kdy se záznam vytvořil. V současné době nelze pomocí těchto dat pro monitorování přesné použití veřejné IP adresy.

<!-- #### Identity -->

