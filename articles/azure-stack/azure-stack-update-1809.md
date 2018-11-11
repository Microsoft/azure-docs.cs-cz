---
title: Aktualizace služby Azure Stack 1809 | Dokumentace Microsoftu
description: Další informace o co je nového v aktualizaci 1809 pro integrované systémy Azure Stack, včetně známých problémů a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: f44b267a28abd64acdd6bc74a43f1c5be8daf0ab
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515603"
---
# <a name="azure-stack-1809-update"></a>Aktualizace služby Azure Stack 1809

*Platí pro: integrované systémy Azure Stack*

Tento článek popisuje obsah balíčku 1809 aktualizace. Balíček aktualizace zahrnuje vylepšení, oprav a známé problémy pro tuto verzi sady Azure Stack. Tento článek také obsahuje odkaz, takže si můžete stáhnout aktualizace. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo sestavení aktualizace Azure Stack 1809 je **1.1809.0.90**.  

### <a name="new-features"></a>Nové funkce

Tato aktualizace zahrnuje následující vylepšení pro službu Azure Stack:

- V této verzi služby Azure Stack integrované systémy podporuje konfigurace 4 až 16 uzlů. Můžete použít [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) vám pomůžou při plánování kapacity služby Azure Stack a konfigurace.

- <!--  2712869   | IS  ASDK -->  **Klient služby Azure Stack syslog (obecná dostupnost)** tento klient umožňuje předávání auditů, upozornění a protokoly zabezpečení spojených s infrastrukturou Azure Stack na syslog serveru nebo zabezpečení informací a událostí (SIEM) software pro správu externí do služby Azure Stack. Klient syslog nyní podporuje určující port, na kterém naslouchá syslog server.

   V této verzi klienta syslog je obecně dostupná, a můžou používat v produkčním prostředí.

   Další informace najdete v tématu [předávání syslog Azure Stack](azure-stack-integrate-security.md).

- Teď můžete [přesunout prostředek registrace](azure-stack-registration.md#move-a-registration-resource) v Azure mezi skupinami prostředků, aniž byste museli znovu zaregistrovat. Poskytovatele cloud Solution Provider (CSP) můžete také přesunout registrace prostředků mezi předplatnými, tak dlouho, dokud novém i starém předplatná jsou mapovány na stejné ID partnera CSP. Toto neovlivní existující mapování tenanta zákazníka. 

### <a name="fixed-issues"></a>Oprava potíží

<!-- TBD - IS ASDK -->
- Na portálu jsou nyní grafu paměti reporting zdarma nebo používané kapacity přesná. Nyní můžete kolik virtuálních počítačů, budete moct vytvořit spolehlivěji předpovídat.

<!-- TBD - IS ASDK --> 
- Opravili jsme problém, ve kterém jste vytvořili virtuální počítače na portálu user portal Azure Stack, a na portálu zobrazuje nesprávný počet datových disků, které můžete připojit k DS-series virtuálních počítačů. Virtuálních počítačů řady DS může obsahovat libovolný počet datových disků jako konfiguraci Azure.

- Následující problémy se spravovanými disky jsou opravené v 1809 a také řeší. 1808 [Azure Stack Hotfix 1.1808.5.110](https://support.microsoft.com/help/4468920/): 

   <!--  2966665 – IS, ASDK --> 
   - Byl opraven problém, v které připojování datových disků SSD na premium velikost spravovaného disku virtuální počítače (DS, DSv2, Fs, Fs_V2) se nezdařilo s chybou: *nepovedlo se aktualizovat disky pro virtuální počítač 'vmname' Chyba: operaci nelze provést, protože požadovaná Typ účtu úložiště 'Premium_LRS' není podporován pro velikost virtuálního počítače "Standard_DS/Ds_V2/FS/Fs_v2)*. 
   
   - Vytvoření spravovaného disku virtuálního počítače s použitím **createOption**: **připojit** selže s následující chybou: *dlouho běžící operace se nezdařila se stavem "Se nezdařilo". Další informace: "vnitřní spuštění došlo k chybě."*
   Kód chyby: InternalExecutionError ErrorMessage: došlo k chybě vnitřního spuštění.
   
   Tento problém byl vyřešen.

- <!-- 2702741 -  IS, ASDK --> Oprava potíží, ve které veřejné IP adresy, které se nasadily pomocí dynamického přidělování metoda nebyly zaručit zachování po zastavte a Navraťte vydání. Nyní jsou zachovány.

- <!-- 3078022 - IS, ASDK --> Pokud se virtuální počítač zastavit a uvolnit před. 1808 ho nebylo možné znovu alokovat po 1808 aktualizaci.  Tento problém je vyřešen v 1809. Instance, které byly v tomto stavu a nebylo možné spustit, může být spuštěný v 1809 s touto opravou. Tento problém vyřešit zabrání také nevyskytla.

<!-- 3090289 – IS, ASDK --> 
- Byl opraven problém, kde po instalaci aktualizace. 1808, může dojít k následujícím problémům při nasazování virtuálních počítačů se spravovanými disky:

   1. Pokud předplatné bylo vytvořeno před aktualizací. 1808, nasazení virtuálního počítače se spravovanými disky může selhat s interní chybovou zprávu. Chcete chybu vyřešit, postupujte podle těchto kroků pro každé předplatné:
      1. Portál pro klienty, přejděte na **předplatná** a vyhledejte předplatné. Klikněte na tlačítko **poskytovatelů prostředků**, klikněte na **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
      2. V rámci stejného předplatného, přejděte na **řízení přístupu (IAM)** a ověřte, že **Azure Stack – spravovaný Disk** je uvedena.
   2. Pokud jste nakonfigurovali prostředí s více tenanty, nasazování virtuálních počítačů v rámci služby předplacené asociovaná s adresářem hosta může selhat s interní chybovou zprávu. Chcete-li chybu vyřešit, postupujte takto:
      1. Použít [. 1808 Azure Stack opravu Hotfix](https://support.microsoft.com/help/4471992).
      2. Postupujte podle kroků v [v tomto článku](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) změna konfigurace všech vašich adresářů hosta.


### <a name="changes"></a>Změny

Žádné.

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

Tuto aktualizaci nainstaluje následující aktualizace:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Další informace o těchto ohrožení zabezpečení, klikněte na výše uvedené odkazy nebo najdete v článcích znalostní báze Microsoft [4457131](https://support.microsoft.com/help/4457131) a [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Požadavky

- Instalace nejnovější opravy Hotfix Azure Stack. 1808 před použitím 1809. Další informace najdete v tématu [KB 4471992 – Azure Stack opravu Hotfix Azure Stack opravu Hotfix 1.1808.7.113](https://support.microsoft.com/help/4471992/).

  > [!TIP]  
  > Předplatit následující *RRS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následujícími parametry do ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Při spuštění [testovací AzureStack](azure-stack-diagnostic-test.md) po 1809 aktualizaci, zobrazí se zpráva s upozorněním z řadič správy základní desky (BMC). Toto upozornění můžete ignorovat.

- <!-- 2468613 - IS --> Při instalaci této aktualizace, může se zobrazit upozornění s názvem *chyba – šablona pro typ FaultType UserAccounts.New chybí.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se automaticky zavře po dokončení instalace této aktualizace.

- <!-- 2489559 - IS --> Nepokoušejte se vytvářet virtuální počítače při instalaci této aktualizace. Další informace o správě aktualizací najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS --> Pokud jste použili aktualizaci do služby Azure Stack od výrobce OEM **k dispozici je aktualizace** oznámení se nemusí zobrazit v portálu pro správu Azure Stack. Chcete-li nainstalovat služby Microsoft update, stáhnout a naimportovat ručně podle pokynů tady [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Postup po aktualizaci

> [!Important]  
> Příprava vašeho nasazení Azure stacku pro rozšíření hostitele, který je povolený další balíček aktualizace. Příprava systému podle následujících pokynů k [Příprava hostitele rozšíření pro službu Azure Stack](azure-stack-extension-host-prepare.md).

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace naleznete následující články znalostní báze, stejně jako naše [zásady obsluhy](azure-stack-servicing-policy.md).  
- [KB 4471993 – Azure Stack opravu Hotfix Azure Stack Hotfix 1.1809.3.96](https://support.microsoft.com/help/4471993/)  

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Toto jsou známé problémy této verze sestavení po instalaci.

### <a name="portal"></a>Portál

- Technická dokumentace služby Azure Stack se zaměřuje na nejnovější verzi. Z důvodu portálu změn mezi verzemi co se zobrazí při použití na portálech Azure Stack se může lišit od naleznete v dokumentaci.

<!-- 2930718 - IS ASDK --> 
- Na portálu správce při přístupu k podrobnosti libovolné předplatné uživatele po zavření okna a kliknutím na **poslední**, se nezobrazí název předplatného uživatele.

<!-- 3060156 - IS ASDK --> 
- Na portálech správce i uživatele, kliknutím na nastavení portálu a následným výběrem **odstranit všechna nastavení a privátní řídicí panely** nefunguje podle očekávání. Zobrazí se oznámení o chybě. 

<!-- 2930799 - IS ASDK --> 
- Na portálech pro správce i uživatele v části **všechny služby**, asset **plány DDoS protection** je nesprávně uveden. Není k dispozici ve službě Azure Stack. Pokud se pokusíte k jeho vytvoření, zobrazí se chyba s informacemi o tom, že na portálu nepovedlo vytvořit položku marketplace. 

<!-- 2930820 - IS ASDK --> 
- Na portálech pro správce i uživatele Pokud dáte vyhledat "Dockeru," položka je nesprávně vrátila. Není k dispozici ve službě Azure Stack. Pokud se pokusíte k jeho vytvoření, zobrazí se okno s uvedením chyby. 

<!-- 2967387 – IS, ASDK --> 
- Účet, který používáte k přihlášení k portálu Azure Stack správce nebo uživatel zobrazí jako **neidentifikovaný uživatel**. Tato zpráva se zobrazí, když buď nemá účet *první* nebo *poslední* zadaný název. Chcete-li tento problém obejít, upravte uživatelský účet první nebo poslední název. Musíte pak Odhlásit se a přihlaste se zpět na portál.  

<!--  2873083 - IS ASDK --> 
-  Při použití na portálu vytvořit škálovací sadu virtuálních počítačů (VMSS), nastavte *velikost instance* rozevírací seznam nenačte správně při použití aplikace Internet Explorer. Chcete-li tento problém vyřešit, použijte jiný prohlížeč při použití portálu k vytvoření VMSS.  

<!-- 2931230 – IS  ASDK --> 
- Plány, které jsou přidány na předplatné uživatele jako doplňkový plán nelze odstranit, i když odebrat plán ze předplatné uživatele. Plán zůstane, dokud se také odstraní předplatné, které odkazují na doplňkový plán. 

<!--2760466 – IS  ASDK --> 
- Při instalaci nového prostředí Azure Stack, na kterém běží tato verze, upozornění, která informuje o *vyžadována aktivace* se nemusí zobrazit. [Aktivace](azure-stack-registration.md) se vyžaduje, abyste mohli používat marketplace syndikace.  

<!-- TBD - IS ASDK --> 
- Se nemá používat dva typy pro správu předplatného, které byly představeny s nástrojem verzi 1804. Typy předplatného jsou **měření předplatné**, a **využití předplatného**. Tyto typy předplatného jsou viditelné v novým prostředím Azure Stack od verze 1804, ale ještě nejsou připravené k použití. By měla dál používat **výchozí zprostředkovatel** typu předplatného.

<!-- TBD - IS ASDK --> 
- Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

<!-- TBD - IS ASDK --> 
- Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení pomocí prostředí PowerShell mohl ověřit oprávnění.


### <a name="health-and-monitoring"></a>Monitorování stavu a

<!-- TBD - IS -->
- Může se zobrazit následující upozornění se zobrazí opakovaně a zmizí v systému Azure Stack:
   - *Instance role infrastruktury není k dispozici*
   - *Uzel jednotka škálování je offline*
   
  Spustit [testovací AzureStack](azure-stack-diagnostic-test.md) rutina pro ověření stavu instance rolí infrastruktury a škálování jednotek uzlů. Pokud nejsou zjištěny žádné problémy podle [testovací AzureStack](azure-stack-diagnostic-test.md), tato upozornění můžete ignorovat. Pokud se zjistí problém, pokuste se spustit instanci role infrastruktury nebo uzlu pomocí portálu pro správu nebo prostředí PowerShell.

<!-- 1264761 - IS ASDK -->  
- Může se zobrazit upozornění **stavu řadiče** komponenta, která mají následující podrobnosti:  

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


<!-- 2812138 | IS --> 
- Může se zobrazit upozornění pro **úložiště** komponenta, která má následující údaje:

   - Název: Chyba interní komunikace se službou Storage  
   - ZÁVAŽNOST: kritické  
   - KOMPONENTA: úložiště  
   - Popis: Chyba interní komunikace se službou úložiště došlo k chybě při odesílání požadavků na tyto uzly.  

    Upozornění můžete ignorovat, ale budete muset ručně zavřete výstrahu.

<!-- 2368581 - IS, ASDK --> 
- Operátory Azure stacku, pokud se zobrazí upozornění na nedostatek paměti a virtuální počítače klientů selhání nasazení **Chyba při vytváření virtuálních počítačů Fabric**, je možné, že toto razítko Azure Stack je nedostatek dostupné paměti. Použití [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) nejlépe pochopit dostupné kapacity pro vaše úlohy.

### <a name="compute"></a>Compute

<!-- 3235634 – IS, ASDK -->
- K nasazení virtuálních počítačů s velikostí, který obsahuje **v2** přípony; například **Standard_A2_v2**, zadejte příponu jako **Standard_A2_v2** (malá písmena v). Nepoužívejte **Standard_A2_V2** (velká písmena V). To funguje v globální Azure a nekonzistence ve službě Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Při vytváření nového virtuálního počítače (VM) pomocí portálu Azure Stack, a vyberte velikost virtuálního počítače, zobrazí se sloupec USD za měsíc s **není k dispozici** zprávy. Tento sloupec by se neměl zobrazit; zobrazení virtuální počítač cenové sloupec není podporován ve službě Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Při použití [ **přidat AzsPlatformImage** rutiny](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), je nutné použít **- OsUri** parametr jako identifikátor URI, kde je odeslána na disk účtu úložiště. Pokud používáte místní cesta na disku, rutina selže s následující chybou: *dlouho běžící operace se nezdařila se stavem "Failed"*. 

<!--  2795678 – IS, ASDK --> 
- Při použití portálu k vytvoření virtuálních počítačů (VM) o velikosti virtuálních počítačů úrovně premium (DS, Ds_v2, služby FS, FSv2) virtuální počítač se vytvoří v účtu úložiště úrovně standard. Vytvoření účtu úložiště úrovně standard neovlivňuje funkčně vstupně-výstupních operací, nebo fakturace. 

   Můžete bezpečně ignorovat upozornění, že: *jste se rozhodli použít standardní disk o velikosti, která podporuje prémiové disky. To může mít vliv na výkon operačního systému a nedoporučuje se používat. Zvažte raději použití storage úrovně premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Prostředí pro vytváření virtuálního počítače škálovací sady (VMSS) poskytuje založené na CentOS 7.2 jako možnost pro nasazení. Vzhledem k tomu, že obrázek není k dispozici ve službě Azure Stack, vyberte jiný operační systém pro vaše nasazení nebo pomocí šablony Azure Resource Manageru zadáním jiné image CentOS, který byl stažen před jejich nasazením na Marketplace operátorem.  

<!-- 2724873 - IS --> 
- Při použití rutiny Powershellu **Start AzsScaleUnitNode** nebo **Stop-AzsScaleunitNode** ke správě jednotek škálování, první pokus o spuštění nebo zastavení jednotka škálování může selhat. Pokud se rutina nezdaří při prvním spuštění, spusťte rutinu znovu. Druhé spuštění by měl úspěšně dokončit operaci. 

<!-- TBD - IS ASDK --> 
- Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.  

<!-- 2724961- IS ASDK --> 
- Když se zaregistrujete **Microsoft.Insight** poskytovatele prostředků v nastavení předplatného a vytvoření virtuálního počítače s Windows pomocí hostovaného operačního systému diagnostických povolena, graf procento využití procesoru na stránce Přehled virtuálních počítačů nezobrazuje data metrik.

   Najít data metrik, jako je například grafu procento využití procesoru pro virtuální počítač, přejděte do okna metrik a zobrazit všechny podporované metriky hosta virtuálního počítače Windows.



### <a name="networking"></a>Sítě  

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


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.


### <a name="usage"></a>Využití  

<!-- TBD - IS ASDK --> 
- Veřejná data IP adres pro měření využití zobrazuje stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítko, které ukazuje, kdy se záznam vytvořil. V současné době nelze pomocí těchto dat pro monitorování přesné použití veřejné IP adresy.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Stáhnout aktualizaci.

Můžete stáhnout aktualizace balíčku Azure Stack 1809 z [tady](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Další postup

- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  
- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).  
