---
title: Shromažďování dat v Azure Security Center | Microsoft Docs
description: Tento článek popisuje, jak nainstalovat agenta Log Analytics a nastavit pracovní prostor Log Analytics, do kterého se mají ukládat shromážděná data.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 795661912633f0d225aef4de8ea7620a8766e096
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766986"
---
# <a name="data-collection-in-azure-security-center"></a>Shromažďování dat v Azure Security Center
Security Center shromažďuje data z vašich virtuálních počítačů Azure, virtuálních počítačů a kontejnerů IaaS a jiných než Azure (včetně místních) počítačů, které monitorují chyby zabezpečení a hrozby. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do pracovního prostoru pro účely analýzy. Příklady takových dat: typ a verze operačního systému, protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel. Agent Log Analytics také kopíruje soubory s výpisem stavu systému do vašeho pracovního prostoru.

Shromažďování dat je nutné, aby poskytovala přehled o chybějících aktualizacích, nesprávně nakonfigurovaných nastavení zabezpečení operačního systému, stavu aplikace Endpoint Protection a detekci hrozeb. 

Tento článek popisuje, jak nainstalovat agenta Log Analytics a nastavit pracovní prostor Log Analytics, do kterého se mají ukládat shromážděná data. Pro povolení shromažďování dat jsou vyžadovány obě operace. 

> [!NOTE]
> - Shromažďování dat je potřeba jenom pro výpočetní prostředky (virtuální počítače, virtuální počítače, sady škálování na IaaS a počítače mimo Azure). Můžete využít výhod Azure Security Center i v případě, že nezřizujete agenty. budete však mít omezené zabezpečení a výše uvedené možnosti nejsou podporovány.  
> - Seznam podporovaných platforem najdete [v tématu podporované platformy v Azure Security Center](security-center-os-coverage.md).
> - Pokud se data ukládají v Log Analytics, ať už používáte nový nebo existující pracovní prostor, můžou se za úložiště dat účtovat další poplatky. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

## Povolit automatické zřizování agenta Log Analytics<a name="auto-provision-mma"></a>

Chcete-li shromažďovat data z počítačů, měli byste mít nainstalovaného agenta Log Analytics. Instalaci agenta je možné provést automaticky (doporučeno) nebo můžete agenta nainstalovat ručně.  

>[!NOTE]
> Automatické zřizování je ve výchozím nastavení vypnuté. Pokud chcete nastavit Security Center pro instalaci automatického zřizování ve výchozím nastavení, nastavte na **zapnuto**.
>

Když je Automatické zřizování zapnuté, Security Center zřídí agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Automatické zřizování se důrazně doporučuje, ale je k dispozici také ruční instalace agenta. [Přečtěte si, jak nainstalovat rozšíření agenta Log Analytics](#manual-agent).



Povolení automatického zřizování agenta Log Analytics:
1. V hlavní nabídce Security Center vyberte **cenové & nastavení**.
2. Klikněte na příslušné předplatné.

   ![Výběr předplatného][7]

3. Vyberte **shromažďování dat**.
4. V části **Automatické zřizování**vyberte **zapnuto** , pokud chcete povolit Automatické zřizování.
5. Vyberte **Save** (Uložit).

   ![Povolení automatického zřizování][1]

>[!NOTE]
> - Pokyny, jak zřídit stávající instalaci, najdete v tématu [Automatické zřizování v případě předem existující instalace agenta](#preexisting).
> - Pokyny k ručnímu zřizování najdete v tématu [Ruční instalace rozšíření agenta Log Analytics](#manual-agent).
> - Pokyny k vypnutí automatického zřizování najdete v tématu vypnutí [automatického zřizování](#offprovisioning).
> - Pokyny, jak připojit Security Center pomocí prostředí PowerShell, najdete v tématu [Automatizace připojování Azure Security Center pomocí prostředí PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfigurace pracovního prostoru
Data shromážděná službou Security Center se ukládají v pracovních prostorech služby Log Analytics. Můžete vybrat, aby se data shromážděná z virtuálních počítačů Azure ukládala v pracovních prostorech vytvořených pomocí Security Center nebo v existujícím pracovním prostoru, který jste vytvořili. 

Konfigurace pracovního prostoru je nastavená na předplatné a mnoho předplatných může používat stejný pracovní prostor.

### <a name="using-a-workspace-created-by-security-center"></a>Použití pracovního prostoru vytvořeného nástrojem Security Center

Security Center může automaticky vytvořit výchozí pracovní prostor, do kterého se budou ukládat data. 

Výběr pracovního prostoru vytvořeného nástrojem Security Center:

1. V části **výchozí konfigurace pracovního prostoru**vyberte použít pracovní prostory vytvořené službou Security Center.
   ![výběru cenové úrovně][10] 

1. Klikněte na **Uložit**.<br>
    Security Center vytvoří novou skupinu prostředků a výchozí pracovní prostor v tomto geografickém umístění a připojí agenta k tomuto pracovnímu prostoru. Konvence pojmenování pro pracovní prostor a skupinu prostředků:<br>
   **Pracovní prostor: DefaultWorkspace-[ID předplatného]-[Geo]<br> skupina prostředků: DefaultResourceGroup-[geografické]**

   Pokud předplatné obsahuje virtuální počítače z několika geografických umístění, Security Center vytvoří několik pracovních prostorů. Pro zachování pravidel ochrany osobních údajů se vytvořilo několik pracovních prostorů.
1. Security Center automaticky povolí Security Center řešení v pracovním prostoru podle cenové úrovně nastavené pro předplatné. 

> [!NOTE]
> Cenová úroveň Log Analytics pracovních prostorů vytvořených pomocí Security Center nemá vliv na Security Center fakturace. Security Center se vždy fakturuje podle zásad zabezpečení Security Center a řešení nainstalovaných v pracovním prostoru. U úrovně Free Security Center povolí řešení *SecurityCenterFree* ve výchozím pracovním prostoru. U úrovně Standard Security Center povolí řešení *SecurityCenterFree* ve výchozím pracovním prostoru.
> Ukládání dat v Log Analytics může pro úložiště dat nabývat další poplatky. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

Další informace o existujících účtech Log Analytics najdete v tématu [existující zákazníci Log Analytics](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Použití existujícího pracovního prostoru

Pokud již máte Log Analytics pracovní prostor, můžete chtít použít stejný pracovní prostor.

Pokud chcete používat stávající pracovní prostor Log Analytics, musíte mít v pracovním prostoru oprávnění ke čtení a zápisu.

> [!NOTE]
> Řešení povolená u stávajícího pracovního prostoru se použijí na virtuální počítače Azure, které jsou k němu připojené. Pro placená řešení to může mít za následek další poplatky. V případě požadavků na ochranu osobních údajů se ujistěte, že vybraný pracovní prostor je ve správné geografické oblasti.
> Ukládání dat do Log Analytics může mít za následek další poplatky za ukládání dat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

Vyberte existující pracovní prostor Log Analytics:

1. V části **výchozí konfigurace pracovního prostoru**vyberte **použít jiný pracovní prostor**.

   ![Vyberte existující pracovní prostor.][2]

2. V rozevírací nabídce vyberte pracovní prostor pro ukládání shromážděných dat.

   > [!NOTE]
   > V nabídce pro přijetí změn jsou k dispozici všechny pracovní prostory ve všech vašich předplatných. Další informace najdete v tématu [Výběr pracovního prostoru pro různé odběry](security-center-enable-data-collection.md#cross-subscription-workspace-selection) . Musíte mít oprávnění pro přístup k pracovnímu prostoru.
   >
   >

3. Vyberte **Save** (Uložit).
4. Po výběru možnosti **Uložit**se zobrazí dotaz, zda chcete překonfigurovat monitorované virtuální počítače, které byly dříve připojeny k výchozímu pracovnímu prostoru.

   - Tuto **možnost vyberte** , pokud chcete, aby se nové nastavení pracovního prostoru projevilo jenom pro nové virtuální počítače. Nové nastavení pracovního prostoru platí jenom pro nové instalace agenta. nově zjištěné virtuální počítače, které nemají nainstalovaného agenta Log Analytics.
   - Vyberte **Ano** , pokud chcete, aby se nové nastavení pracovního prostoru projevilo na všech virtuálních počítačích. Kromě toho se každý virtuální počítač připojený k Security Center vytvořenému pracovnímu prostoru znovu připojí k novému cílovému pracovnímu prostoru.

   > [!NOTE]
   > Pokud vyberete Ano, nesmíte odstranit pracovní prostory vytvořené pomocí Security Center, dokud se všechny virtuální počítače znovu nepřipojí k novému cílovému pracovnímu prostoru. Tato operace se nezdařila, pokud je pracovní prostor odstraněn příliš brzy.
   >
   >

   - Vyberte **Zrušit** a operaci se zruší.

     ![Vyberte existující pracovní prostor.][3]

5. Vyberte cenovou úroveň požadovaného pracovního prostoru, pro který chcete nastavit agenta Log Analytics. <br>Pokud chcete použít existující pracovní prostor, nastavte cenovou úroveň pracovního prostoru. Tím se v pracovním prostoru nainstaluje řešení Security Center, pokud ještě není přítomné.

    a.  V hlavní nabídce Security Center vyberte **cenové & nastavení**.
     
    b.  Vyberte požadovaný pracovní prostor, ve kterém chcete agenta připojit.
        ![vyberte pracovní prostor][7] c. Nastavte cenovou úroveň.
        ![výběru cenové úrovně][9]
   
   >[!NOTE]
   >Pokud má pracovní prostor již povolené řešení **Security** nebo **SecurityCenterFree** , ceny se nastaví automaticky. 

## <a name="cross-subscription-workspace-selection"></a>Výběr pracovního prostoru mezi předplatnými
Když vyberete pracovní prostor, do kterého se budou ukládat vaše data, budou dostupné všechny pracovní prostory ve všech vašich předplatných. Výběr pracovního prostoru z jiného předplatného vám umožňuje shromažďovat data z virtuálních počítačů, které běží v jiných předplatných, a ukládat je do vybraného pracovního prostoru. Tento výběr je užitečný, pokud ve vaší organizaci používáte centralizovaný pracovní prostor a chcete ho použít pro shromažďování dat o zabezpečení. Další informace o tom, jak spravovat pracovní prostory, najdete v tématu [Správa přístupu k pracovnímu prostoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Úroveň shromažďování dat
Výběr vrstvy shromažďování dat v Azure Security Center ovlivní pouze úložiště událostí zabezpečení v pracovním prostoru služby Log Analytics. Agent Log Analytics bude stále shromažďovat a analyzovat události zabezpečení vyžadované pro detekci hrozeb Azure Security Center, bez ohledu na to, jakou úroveň událostí zabezpečení jste si zvolili pro uložení v pracovním prostoru Log Analytics (pokud existuje). Volba ukládání událostí zabezpečení v pracovním prostoru vám umožní prozkoumávání, vyhledávání a auditování těchto událostí v pracovním prostoru. 
> [!NOTE]
> Ukládání dat do Log Analytics může mít za následek další poplatky za ukládání dat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Můžete zvolit správnou zásadu filtrování pro vaše předplatná a pracovní prostory ze čtyř sad událostí, které se budou ukládat do svého pracovního prostoru: 

- **Žádné** – zakažte úložiště událostí zabezpečení. Toto je výchozí nastavení.
- **Minimální** – menší sada událostí pro zákazníky, kteří chtějí minimalizovat objem událostí.
- **Společné** – jedná se o sadu událostí, které vyhovují většině zákazníků a umožňují jim úplný záznam pro audit.
- **Všechny události** – pro zákazníky, kteří chtějí mít jistotu, že budou uloženy všechny události.


> [!NOTE]
> Tyto bezpečnostní sady událostí jsou k dispozici pouze na úrovni Standard Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
Tyto sady byly navržené tak, aby vycházely z typických scénářů. Nezapomeňte vyhodnotit, který z nich vyhovuje vašim potřebám, než ho implementujete.
>
>

Chcete-li zjistit události, které budou patřit do **běžných** a **minimálních** sad událostí, pracovali se zákazníky a oborovými standardy, abyste se dozvěděli o nefiltrované frekvenci jednotlivých událostí a jejich využití. V tomto procesu jsme použili následující pokyny:

- **Minimální** – Ujistěte se, že tato sada pokrývá jenom události, které můžou indikovat úspěšné porušení zabezpečení a důležité události, které mají velmi malý svazek. Tato sada například obsahuje úspěšné a neúspěšné přihlášení uživatele (ID události 4624, 4625), ale neobsahuje odhlášení, které je důležité pro auditování, ale ne smysluplné pro detekci a má poměrně vysoký objem. Většina dat v této sadě je události přihlášení a událost vytvoření procesu (ID události 4688).
- **Common** – v této sadě uveďte úplný záznam pro audit uživatelů. Například tato sada obsahuje přihlašovací údaje uživatele i odhlášení uživatele (ID události 4634). Zahrnujeme i akce auditování, jako jsou změny skupiny zabezpečení, klíčové operace protokolu Kerberos řadiče domény a další události, které jsou doporučeny pro oborové organizace.

Do společné sady byly zahrnuty události s velmi nízkým objemem, jako je hlavní motivace a jejich výběr přes všechny události, je snížit objem a Nefiltrovat konkrétní události.

Zde je úplný rozpis ID událostí zabezpečení a zámků aplikací pro každou sadu:

| Datová vrstva | Shromážděné indikátory událostí |
| --- | --- |
| Poskytuje | 1102, 4624, 4625, 4657, 4663, 4688,, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Společné | 1, čl. 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, |
| | 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> - Pokud používáte objekt GPO (Zásady skupiny Object), doporučuje se povolit událost vytvoření procesu zásady auditu 4688 a pole *CommandLine* v události 4688. Další informace o události vytvoření procesu 4688 najdete v tématu [Nejčastější dotazy](security-center-faq.md#what-happens-when-data-collection-is-enabled)k Security Center. Další informace o těchto zásadách auditu najdete v tématu [doporučení zásad auditu](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Pokud chcete povolit shromažďování dat pro [Adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center v režimu auditování nakonfiguruje místní zásadu AppLockeru, aby povolovala všechny aplikace. Tím dojde k tomu, že AppLocker generuje události, které se pak shromažďují a využívají Security Center. Je důležité si uvědomit, že tato zásada nebude nakonfigurovaná na žádném počítači, na kterém je už nakonfigurovaná zásada AppLockeru. 
> - Aby bylo možné shromažďovat události platformy Windows Filtering [ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), je nutné povolit [připojení platformy pro filtrování auditu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/set/Subcategory: "filtrování připojení platformy"/Success: Enable).
>

Výběr zásad filtrování:
1. Na stránce **shromažďování dat** vyberte zásadu filtrování v části **události zabezpečení**.
2. Vyberte **Save** (Uložit).

   ![Zvolit zásadu filtrování][5]

### Automatické zřizování v případech již existující instalace agenta<a name="preexisting"></a> 

Následující případy použití určují, jak Automatické zřizování funguje v případech, kdy už je agent nebo rozšíření nainstalované. 

- Agent Log Analytics je nainstalován v počítači, ale ne jako rozšíření (přímý Agent).<br>
Pokud je agent Log Analytics nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nainstaluje rozšíření agenta Log Analytics a může upgradovat agenta Log Analytics na nejnovější verzi.
Nainstalovaný Agent bude pokračovat v hlášení již nakonfigurovaných pracovních prostorů a dále bude hlásit pracovnímu prostoru nakonfigurovanému v Security Center (pro počítače se systémem Windows je podporována podpora více domovských stránek).
Pokud je nakonfigurovaným pracovním prostorem pracovní prostor uživatele (ve výchozím nastavení není Security Center), bude nutné do něj nainstalovat řešení "Security/" securityFree ", aby bylo možné Security Center zahájit zpracování událostí z virtuálních počítačů a počítačů, které do daného pracovního prostoru hlásí.<br>
<br>
Pro počítače se systémem Linux zatím není podporována podpora více domovských stránek agenta, takže pokud je zjištěna existující instalace agenta, Automatické zřizování nebude provedeno a konfigurace počítače nebude změněna.
<br>
Pro existující počítače v předplatných, které jsou připojené k Security Center před 2019-03-17, když se zjistí existující agent, nebude rozšíření agenta Log Analytics nainstalované a počítač nebude ovlivněný. Pro tyto počítače si Projděte doporučení "vyřešit problémy se stavem agenta monitorování na vašich počítačích" a vyřešte problémy s instalací agenta na těchto počítačích.

  
- Agent System Center Operations Manager je nainstalován v počítači.<br>
Security Center nainstaluje rozšíření agenta Log Analytics po straně na stávající Operations Manager. Stávající agent Operations Manager bude pokračovat v hlášení na Server Operations Manager normálně. Všimněte si, že agent Operations Manager a Agent Log Analytics sdílejí běžné běhové knihovny, které se během tohoto procesu aktualizují na nejnovější verzi.
Poznámka – Pokud je nainstalovaná verze agenta Operations Manager 2012 **, nepovolujte** Automatické zřizování.<br>

- Existuje již existující rozšíření virtuálního počítače.<br>
    - Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Security Center nepřepisují existující připojení k pracovním prostorům uživatelů. Security Center bude ukládat data zabezpečení z virtuálního počítače v pracovním prostoru, který je už připojený, a to za předpokladu, že je na něm nainstalované řešení Security nebo securityFree. Security Center může v tomto procesu upgradovat verzi rozšíření na nejnovější verzi.  
    - Chcete-li zjistit, který pracovní prostor existující rozšíření odesílá data, spusťte test a [Ověřte připojení pomocí Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Případně můžete otevřít Log Analytics pracovní prostory, vybrat pracovní prostor, vybrat virtuální počítač a podívat se na připojení agenta Log Analytics. 
    - Pokud máte prostředí, ve kterém je agent Log Analytics nainstalovaný na klientských pracovních stanicích a vytváření sestav do existujícího pracovního prostoru Log Analytics, Projděte si seznam [operačních systémů podporovaných Azure Security Center](security-center-os-coverage.md) , abyste se ujistili, že je váš operační systém podporovaný. Další informace najdete v tématu [existující zákazníci Log Analytics](security-center-faq.md#existingloganalyticscust).
 
### Vypnout automatické zřizování<a name="offprovisioning"></a>
Automatické zřizování z prostředků můžete kdykoli vypnout vypnutím tohoto nastavení v zásadách zabezpečení. 


1. Vraťte se do hlavní nabídky Security Center a vyberte zásady zabezpečení.
2. Na řádku předplatného, pro které chcete zakázat Automatické zřizování, klikněte na **Upravit nastavení** .
3. V okně **zásady zabezpečení – shromažďování dat** v části **Automatické zřizování** vyberte **vypnuto**.
4. Vyberte **Save** (Uložit).

   ![Zakázat Automatické zřizování][6]

Pokud je Automatické zřizování zakázané (vypnuté), výchozí oddíl konfigurace pracovního prostoru se nezobrazí.

Pokud vypnete automatické zřizování po jeho předchozí instalaci:
-   Agenti nebudou zřízeni na nových virtuálních počítačích.
-   Security Center zastaví shromažďování dat z výchozího pracovního prostoru.
 
> [!NOTE]
>  Při vypnutí automatického zřizování se agent Log Analytics neodebere z virtuálních počítačů Azure, ve kterých se agent zřídil. Informace o odebrání rozšíření OMS najdete v tématu [návody odebrání rozšíření OMS nainstalovaných pomocí Security Center](security-center-faq.md#remove-oms).
>
    
## Ruční zřizování agentů<a name="manual-agent"></a>
 
Existuje několik způsobů, jak ručně nainstalovat agenta Log Analytics. Při ruční instalaci se ujistěte, že jste zakázali Automatické zřizování.

### <a name="operations-management-suite-vm-extension-deployment"></a>Nasazení rozšíření VM Operations Management Suite 

Můžete ručně nainstalovat agenta Log Analytics, aby Security Center mohl shromažďovat data zabezpečení z vašich virtuálních počítačů a poskytovat doporučení a výstrahy.
1. Vyberte možnost Automatické zřizování – vypnuto.
2. Vytvořte pracovní prostor a nastavte cenovou úroveň pracovního prostoru, pro který chcete nastavit agenta Log Analytics:

   a.  V hlavní nabídce Security Center vyberte **zásady zabezpečení**.
     
   b.  Vyberte pracovní prostor, ve kterém chcete agenta připojit. Ujistěte se, že je pracovní prostor ve stejném předplatném, které používáte v Security Center a máte oprávnění ke čtení a zápisu v pracovním prostoru.
       ![vyberte pracovní prostor][8]
3. Nastavte cenovou úroveň.
   ![výběru cenové úrovně][9] 
   >[!NOTE]
   >Pokud má pracovní prostor již povolené řešení **Security** nebo **SecurityCenterFree** , ceny se nastaví automaticky. 
   > 

4. Pokud chcete nasadit agenty na nové virtuální počítače pomocí šablony Správce prostředků, nainstalujte rozšíření virtuálního počítače OMS:

   a.  [Instalace rozšíření pro virtuální počítače OMS pro Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalace rozšíření pro virtuální počítače OMS pro Linux](../virtual-machines/extensions/oms-linux.md)
5. Pokud chcete nasadit rozšíření na stávající virtuální počítače, postupujte podle pokynů v [tématu shromáždění dat o službě Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > Část **shromažďování údajů o událostech a výkonu** je nepovinná.
   >
6. Pokud chcete k nasazení rozšíření použít PowerShell, použijte následující příklad PowerShellu:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Přejděte na **Log Analytics** a klikněte na **Upřesnit nastavení**.
    
      ![Nastavení Log Analytics][11]

   2. Zkopírujte hodnoty z **ID pracovního prostoru** a **primárního klíče**.
  
      ![Kopírovat hodnoty][12]

   3. Naplňte veřejnou konfiguraci a soukromou konfiguraci pomocí těchto hodnot:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Při instalaci na virtuální počítač s Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Při instalaci na virtuální počítač se systémem Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Pokyny, jak připojit Security Center pomocí prostředí PowerShell, najdete v tématu [Automatizace připojování Azure Security Center pomocí prostředí PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Řešení potíží

-   Informace o tom, jak identifikovat Automatické zřizování problémů, najdete v tématu [monitorování problémů se stavem agenta](security-center-troubleshooting-guide.md#mon-agent).

-  Informace o tom, jak identifikovat požadavky na síť agenta monitorování, najdete v tématu [řešení potíží s požadavky na síť agenta](security-center-troubleshooting-guide.md#mon-network-req)
-   Pokud chcete identifikovat problémy ručního připojování, přečtěte si téma řešení potíží s [připojováním Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Identifikace problémů nemonitorovaných virtuálních počítačů a počítačů:

    VIRTUÁLNÍ počítač nebo počítač se nemonitoruje Security Center, pokud počítač nepoužívá rozšíření Microsoft Monitoring Agent. Počítač může mít již nainstalovaný místní agent, například agenta OMS Direct nebo Agent System Center Operations Manager. Počítače s těmito agenty jsou označeny jako nemonitorované, protože tito agenti nejsou v Security Center plně podporovaná. Pokud chcete naplno využívat všechny schopnosti služby Security Center, potřebujete rozšíření Microsoft Monitoring Agent.

    Další informace o důvodech, Security Center nelze úspěšně monitorovat virtuální počítače a počítače inicializované pro Automatické zřizování, najdete v tématu [monitorování problémů se stavem agenta](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Další kroky
Tento článek ukazuje, jak funguje shromažďování dat a Automatické zřizování v Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
