---
title: Shromažďování dat ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento článek popisuje, jak nainstalovat agenta Log Analytics a nastavit pracovní prostor Log Analytics, do kterého se mají ukládat shromážděná data.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 71c30e0a86f67a2e2826859032144aa491c0cee1
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597027"
---
# <a name="data-collection-in-azure-security-center"></a>Shromažďování dat ve službě Azure Security Center
Security Center shromažďuje data z vašich virtuálních počítačů Azure, virtuálních počítačů a kontejnerů IaaS a jiných než Azure (včetně místních) počítačů, které monitorují chyby zabezpečení a hrozby. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do pracovního prostoru pro účely analýzy. Příkladem takových dat jsou: operační systém typu a verzi, protokoly operačního systému (protokoly událostí Windows), spuštěné procesy, název počítače, IP adresy a přihlášeného uživatele. Agent Log Analytics také kopíruje soubory s výpisem stavu systému do vašeho pracovního prostoru.

Shromažďování dat je nutné, aby poskytovala přehled o chybějících aktualizacích, nesprávně nakonfigurovaných nastavení zabezpečení operačního systému, stavu aplikace Endpoint Protection a detekci hrozeb. 

Tento článek popisuje, jak nainstalovat agenta Log Analytics a nastavit pracovní prostor Log Analytics, do kterého se mají ukládat shromážděná data. Operace je potřeba povolit shromažďování dat. 

> [!NOTE]
> - Shromažďování dat je potřeba jenom pro výpočetní prostředky (virtuální počítače, virtuální počítače, sady škálování na IaaS a počítače mimo Azure). Můžete využívat výhody Azure Security Center i v případě, že nemusíte zřizovat agentů; ale bude mít omezenou zabezpečení a možnosti uvedené výše se nepodporují.  
> - Seznam podporovaných platforem najdete [v tématu podporované platformy v Azure Security Center](security-center-os-coverage.md).
> - Pokud se data ukládají v Log Analytics, ať už používáte nový nebo existující pracovní prostor, můžou se za úložiště dat účtovat další poplatky. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

## Povolit automatické zřizování agenta Log Analytics<a name="auto-provision-mma"></a>

Chcete-li shromažďovat data z počítačů, měli byste mít nainstalovaného agenta Log Analytics. Instalaci agenta je možné provést automaticky (doporučeno) nebo můžete agenta nainstalovat ručně.  

>[!NOTE]
> Automatické zřizování je ve výchozím nastavení vypnuté. Pokud chcete nastavit Security Center pro instalaci automatického zřizování ve výchozím nastavení, nastavte na **zapnuto**.
>

Když je Automatické zřizování zapnuté, Security Center zřídí agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Automatické zřizování se důrazně doporučuje, ale ruční instalaci agentů je také k dispozici. [Přečtěte si, jak nainstalovat rozšíření agenta Log Analytics](#manual-agent).



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
Data shromážděná službou Security Center se ukládají v pracovních prostorech služby Log Analytics. Můžete vybrat na data shromážděná z virtuálních počítačů Azure ukládat v pracovních prostorů vytvořených službou Security Center nebo existujícího pracovního prostoru, který jste vytvořili. 

Konfigurace pracovního prostoru je nastavena na jedno předplatné a mnoha předplatnými může použít stejný pracovní prostor.

### <a name="using-a-workspace-created-by-security-center"></a>Použití pracovního prostoru vytvořených službou Security Center

Centrum zabezpečení můžete automaticky vytvořit výchozí pracovní prostor, ve kterých se mají uložit data. 

Výběr pracovního prostoru vytvořených službou Security Center:

1. V části **výchozí konfigurace pracovního prostoru**vyberte použít pracovní prostory vytvořené službou Security Center.
   ![výběru cenové úrovně][10] 

1. Klikněte na možnost **Uložit**.<br>
    Security Center vytvoří nový prostředek skupiny a výchozí pracovní prostor v této informace o zeměpisné poloze a agent se připojí k tomuto pracovnímu prostoru. Zásady vytváření názvů pracovní prostor a skupiny prostředků je:<br>
   **Pracovní prostor: DefaultWorkspace-[ID předplatného]-[Geo]<br> skupina prostředků: DefaultResourceGroup-[geografické]**

   Pokud předplatné obsahuje virtuální počítače z více geolocations, Security Center vytvoří několik pracovních prostorů. Zachovat data pravidla ochrany osobních údajů se vytvoří víc pracovních prostorů.
1. Security Center automaticky povolí řešení Security Center v pracovním prostoru pro jednotlivé cenové úrovně pro předplatné. 

> [!NOTE]
> Cenová úroveň pracovních prostorů vytvořených službou Security Center Log Analytics nemá vliv na fakturaci Security Center. Security Center se vždy fakturuje podle zásad zabezpečení Security Center a řešení nainstalovaných v pracovním prostoru. U úrovně Free Security Center povolí řešení *SecurityCenterFree* ve výchozím pracovním prostoru. U úrovně Standard Security Center povolí řešení *SecurityCenterFree* ve výchozím pracovním prostoru.
> Ukládání dat v Log Analytics může pro úložiště dat nabývat další poplatky. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

Další informace o existujících účtech Log Analytics najdete v tématu [existující zákazníci Log Analytics](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Použití existujícího pracovního prostoru

Pokud již máte Log Analytics pracovní prostor, můžete chtít použít stejný pracovní prostor.

Pokud chcete použít stávající pracovní prostor Log Analytics, musíte mít oprávnění v pracovním prostoru pro čtení a zápisu.

> [!NOTE]
> Řešení povolená ve existujícího pracovního prostoru se použijí na virtuální počítače Azure, které jsou k němu připojená. U placených řešení to může vést k dalším poplatkům. Pro data aspekty ochrany osobních údajů Ujistěte se, že váš vybraný pracovní prostor je vhodné geografické oblasti.
> Ukládání dat do Log Analytics může mít za následek další poplatky za ukládání dat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

Vyberte existující pracovní prostor Log Analytics:

1. V části **výchozí konfigurace pracovního prostoru**vyberte **použít jiný pracovní prostor**.

   ![Vyberte existující pracovní prostor][2]

2. Z rozevírací nabídky vyberte pracovní prostor pro ukládání shromážděných dat.

   > [!NOTE]
   > V rozevírací nabídku o přijetí změn jsou k dispozici všechny pracovní prostory ve všech vašich předplatných. Další informace najdete v tématu [Výběr pracovního prostoru pro různé odběry](security-center-enable-data-collection.md#cross-subscription-workspace-selection) . Musíte mít oprávnění pro přístup k pracovnímu prostoru.
   >
   >

3. Vyberte **Save** (Uložit).
4. Po výběru možnosti **Uložit**se zobrazí dotaz, zda chcete překonfigurovat monitorované virtuální počítače, které byly dříve připojeny k výchozímu pracovnímu prostoru.

   - Tuto **možnost vyberte** , pokud chcete, aby se nové nastavení pracovního prostoru projevilo jenom pro nové virtuální počítače. Nové nastavení pracovního prostoru platí jenom pro nové instalace agenta. nově zjištěné virtuální počítače, které nemají nainstalovaného agenta Log Analytics.
   - Vyberte **Ano** , pokud chcete, aby se nové nastavení pracovního prostoru projevilo na všech virtuálních počítačích. Kromě toho každý virtuální počítač připojený ke službě Security Center vytvořit pracovní prostor znovu nepřipojí do nového cílového pracovního prostoru.

   > [!NOTE]
   > Pokud vyberete Ano, nemůže odstranit pracovních prostorů vytvořených službou Security Center, dokud se všechny virtuální počítače mají úspěšné automatické připojení k novému pracovnímu prostoru cíl. Tato operace selže, pokud pracovní prostor je příliš brzké odstranit.
   >
   >

   - Vyberte **Zrušit** a operaci se zruší.

     ![Vyberte existující pracovní prostor][3]

5. Vyberte cenovou úroveň požadovaného pracovního prostoru, pro který chcete nastavit agenta Log Analytics. <br>Pokud chcete použít existující pracovní prostor, nastavte cenovou úroveň pracovního prostoru. Tím se nainstaluje security Center řešení v pracovním prostoru, pokud již není k dispozici.

    a.  V hlavní nabídce Security Center vyberte **cenové & nastavení**.
     
    b.  Vyberte požadovaný pracovní prostor, ve kterém chcete agenta připojit.
        ![vyberte pracovní prostor][7] c. Nastavte cenovou úroveň.
        ![výběru cenové úrovně][9]
   
   >[!NOTE]
   >Pokud má pracovní prostor již povolené řešení **Security** nebo **SecurityCenterFree** , ceny se nastaví automaticky. 

## <a name="cross-subscription-workspace-selection"></a>Výběr pracovního prostoru mezi předplatnými
Když vyberete pracovní prostor, ve kterých se mají ukládat data, jsou k dispozici všechny pracovní prostory ve vašich předplatných. Výběr pracovního prostoru z jiného předplatného vám umožňuje shromažďovat data z virtuálních počítačů, které běží v jiných předplatných, a ukládat je do vybraného pracovního prostoru. Tento výběr je užitečný, pokud ve vaší organizaci používáte centralizovaný pracovní prostor a chcete ho použít pro shromažďování dat o zabezpečení. Další informace o tom, jak spravovat pracovní prostory, najdete v tématu [Správa přístupu k pracovnímu prostoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Úrovně shromažďování dat
Výběr vrstvy shromažďování dat v Azure Security Center ovlivní pouze úložiště událostí zabezpečení v pracovním prostoru služby Log Analytics. Agent Log Analytics bude stále shromažďovat a analyzovat události zabezpečení vyžadované pro detekci hrozeb Azure Security Center, bez ohledu na to, jakou úroveň událostí zabezpečení jste si zvolili pro uložení v pracovním prostoru Log Analytics (pokud existuje). Volba ukládání událostí zabezpečení v pracovním prostoru vám umožní prozkoumávání, vyhledávání a auditování těchto událostí v pracovním prostoru. 
> [!NOTE]
> Ukládání dat do Log Analytics může mít za následek další poplatky za ukládání dat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Můžete si vybrat vpravo filtrování zásady pro předplatná a pracovní prostory z čtyř sad událostí, které mají být uloženy ve vašem pracovním prostoru: 

- **Žádné** – zakažte úložiště událostí zabezpečení. Toto je výchozí nastavení.
- **Minimální** – menší sada událostí pro zákazníky, kteří chtějí minimalizovat objem událostí.
- **Společné** – jedná se o sadu událostí, které vyhovují většině zákazníků a umožňují jim úplný záznam pro audit.
- **Všechny události** – pro zákazníky, kteří chtějí mít jistotu, že budou uloženy všechny události.


> [!NOTE]
> Tyto sady události zabezpečení jsou k dispozici pouze v Security Center úrovně Standard. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
Tyto sady byly navržených pro typické scénáře. Ujistěte se, že k vyhodnocení, která z nich nejlépe vyhovuje vašim potřebám před implementací.
>
>

Chcete-li zjistit události, které budou patřit do **běžných** a **minimálních** sad událostí, pracovali se zákazníky a oborovými standardy, abyste se dozvěděli o nefiltrované frekvenci jednotlivých událostí a jejich využití. V tomto procesu jsme použili následující pokyny:

- **Minimální** – Ujistěte se, že tato sada pokrývá jenom události, které můžou indikovat úspěšné porušení zabezpečení a důležité události, které mají velmi malý svazek. Tato sada například obsahuje úspěšné a neúspěšné přihlášení uživatele (ID události 4624, 4625), ale neobsahuje odhlášení, které je důležité pro auditování, ale ne smysluplné pro detekci a má poměrně vysoký objem. Většina objemu dat této sady se události přihlášení a proces vytvoření události (událost ID 4688).
- **Common** – v této sadě uveďte úplný záznam pro audit uživatelů. Například tato sada obsahuje přihlašovací údaje uživatele i odhlášení uživatele (ID události 4634). Zahrnujeme auditování akce, jako je změnami skupin zabezpečení, operace protokolu Kerberos řadič klíče domény a další události, které doporučuje organizace odvětví.

Události, které mají velmi malé množství byly součástí běžnou sadu jako hlavní motivace rozhodnout se, že všechny události přímo je snížit objem a nechcete vyfiltrovat konkrétní události.

Tady je úplný přehled zabezpečení a AppLocker event ID pro každou sadu:

| Datová vrstva | Shromažďovat události ukazatelů |
| --- | --- |
| Minimální | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Společné | 1, čl. 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Pokud používáte objekt GPO (Zásady skupiny Object), doporučuje se povolit událost vytvoření procesu zásady auditu 4688 a pole *CommandLine* v události 4688. Další informace o události vytvoření procesu 4688 najdete v tématu [Nejčastější dotazy](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)k Security Center. Další informace o těchto zásadách auditu najdete v tématu [doporučení zásad auditu](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Pokud chcete povolit shromažďování dat pro [Adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center v režimu auditování nakonfiguruje místní zásadu AppLockeru, aby povolovala všechny aplikace. To způsobí, že nástroj AppLocker generovat události, které jsou pak shromážděná a využít Security Center. Je důležité si uvědomit, že tyto zásady nenakonfigurují v počítačích, na kterých je už nakonfigurovaná zásada nástroje AppLocker. 
> - Aby bylo možné shromažďovat události platformy Windows Filtering [ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), je nutné povolit [připojení platformy pro filtrování auditu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/set/Subcategory: "filtrování připojení platformy"/Success: Enable).
>

Chcete-li zvolit filtrování zásad:
1. Na stránce **shromažďování dat** vyberte zásadu filtrování v části **události zabezpečení**.
2. Vyberte **Save** (Uložit).

   ![Zvolte zásady filtrování][5]

### Automatické zřizování v případech již existující instalace agenta<a name="preexisting"></a> 

Tyto případy použití určit, jak automatické zřizování lze použít v případech, kdy je již agent nebo rozšíření nainstalované. 

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

- Existující rozšíření virtuálního počítače je k dispozici<br>
    - Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Security Center nemůže přepsat existující připojení k pracovním prostorům uživatele. Security Center bude ukládat data zabezpečení z virtuálního počítače v pracovním prostoru, který je už připojený, a to za předpokladu, že je na něm nainstalované řešení Security nebo securityFree. Security Center může v tomto procesu upgradovat verzi rozšíření na nejnovější verzi.  
    - Chcete-li zjistit, který pracovní prostor existující rozšíření odesílá data, spusťte test a [Ověřte připojení pomocí Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Případně můžete otevřít Log Analytics pracovní prostory, vybrat pracovní prostor, vybrat virtuální počítač a podívat se na připojení agenta Log Analytics. 
    - Pokud máte prostředí, ve kterém je agent Log Analytics nainstalovaný na klientských pracovních stanicích a vytváření sestav do existujícího pracovního prostoru Log Analytics, Projděte si seznam [operačních systémů podporovaných Azure Security Center](security-center-os-coverage.md) , abyste se ujistili, že je váš operační systém podporovaný. Další informace najdete v tématu [existující zákazníci Log Analytics](./faq-azure-monitor-logs.md).
 
### Vypnout automatické zřizování<a name="offprovisioning"></a>
Můžete vypnout automatické zřizování z prostředků v každém okamžiku vypnutím toto nastavení v zásadách zabezpečení. 


1. Vraťte se do hlavní nabídky služby Security Center a vyberte zásady zabezpečení.
2. Na řádku předplatného, pro které chcete zakázat Automatické zřizování, klikněte na **Upravit nastavení** .
3. V okně **zásady zabezpečení – shromažďování dat** v části **Automatické zřizování** vyberte **vypnuto**.
4. Vyberte **Save** (Uložit).

   ![Vypnout automatické zřizování][6]

Když automatické zřizování je zakázaný (vypnuto), konfigurační oddíl výchozí pracovní prostor se nezobrazí.

Je-li vypnout automatické zřizování po dříve na:
-   Agenti se zřídí na nové virtuální počítače.
-   Security Center přestane shromažďování dat z výchozího pracovního prostoru.
 
> [!NOTE]
>  Při vypnutí automatického zřizování se agent Log Analytics neodebere z virtuálních počítačů Azure, ve kterých se agent zřídil. Informace o odebrání rozšíření OMS najdete v tématu [návody odebrání rozšíření OMS nainstalovaných pomocí Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## Ruční zřizování agentů<a name="manual-agent"></a>
 
Existuje několik způsobů, jak ručně nainstalovat agenta Log Analytics. Při instalaci ručně, ujistěte se, že zakážete automatické zřizování.

### <a name="operations-management-suite-vm-extension-deployment"></a>Nasazení rozšíření Operations Management Suite virtuálního počítače 

Můžete ručně nainstalovat agenta Log Analytics, aby Security Center mohl shromažďovat data zabezpečení z vašich virtuálních počítačů a poskytovat doporučení a výstrahy.
1. Vyberte automatického zřizování – vypnuto.
2. Vytvořte pracovní prostor a nastavte cenovou úroveň pracovního prostoru, pro který chcete nastavit agenta Log Analytics:

   a.  V hlavní nabídce Security Center vyberte **zásady zabezpečení**.
     
   b.  Vyberte pracovní prostor, ve které máte v úmyslu připojení agenta. Zajistěte, aby že pracovní prostor je ve stejném předplatném, použijte ve službě Security Center a zda máte oprávnění ke čtení/zápisu v pracovním prostoru.
       ![vyberte pracovní prostor][8]
3. Nastavte cenovou úroveň.
   ![výběru cenové úrovně][9] 
   >[!NOTE]
   >Pokud má pracovní prostor již povolené řešení **Security** nebo **SecurityCenterFree** , ceny se nastaví automaticky. 
   > 

4. Pokud chcete nasadit agenty na nové virtuální počítače pomocí šablony Resource Manageru, nainstalujte rozšíření virtuálního počítače OMS:

   a.  [Instalace rozšíření pro virtuální počítače OMS pro Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalace rozšíření pro virtuální počítače OMS pro Linux](../virtual-machines/extensions/oms-linux.md)
5. Pokud chcete nasadit rozšíření na stávající virtuální počítače, postupujte podle pokynů v [tématu shromáždění dat o službě Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > Část **shromažďování údajů o událostech a výkonu** je nepovinná.
   >
6. Použití Powershellu k nasazení rozšíření, použijte následující příklad Powershellu:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Přejděte na **Log Analytics** a klikněte na **Upřesnit nastavení**.
    
      ![Nastavení log analytics][11]

   2. Zkopírujte hodnoty z **ID pracovního prostoru** a **primárního klíče**.
  
      ![Zkopírujte hodnoty][12]

   3. Naplnění config veřejné a privátní konfigurace s těmito hodnotami:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Při instalaci na virtuálním počítači s Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Při instalaci na virtuální počítač s Linuxem:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Pokyny, jak připojit Security Center pomocí prostředí PowerShell, najdete v tématu [Automatizace připojování Azure Security Center pomocí prostředí PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Řešení potíží

-   Informace o tom, jak identifikovat Automatické zřizování problémů, najdete v tématu [monitorování problémů se stavem agenta](security-center-troubleshooting-guide.md#mon-agent).

-  Informace o tom, jak identifikovat požadavky na síť agenta monitorování, najdete v tématu [řešení potíží s požadavky na síť agenta](security-center-troubleshooting-guide.md#mon-network-req)
-   Pokud chcete identifikovat problémy ručního připojování, přečtěte si téma řešení potíží s [připojováním Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Identifikace problémů nemonitorovaných virtuálních počítačů a počítačů:

    Virtuální počítač nebo počítač je nemonitorovaný službou Security Center, pokud počítač není spuštěný rozšíření Microsoft Monitoring Agent. Počítač může mít již nainstalovaný místní agent, například agenta OMS Direct nebo Agent System Center Operations Manager. Počítače s těmito agenty se identifikují jako nemonitorované, protože tyto agenty plně nepodporuje ve službě Security Center. Pokud chcete naplno využívat všechny schopnosti služby Security Center, potřebujete rozšíření Microsoft Monitoring Agent.

    Další informace o důvodech, Security Center nelze úspěšně monitorovat virtuální počítače a počítače inicializované pro Automatické zřizování, najdete v tématu [monitorování problémů se stavem agenta](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Další kroky
Tento článek vám ukázali, jak shromažďování dat a automatické zřizování v Security Center funguje. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Azure Security Center – nejčastější dotazy](faq-general.md) – Přečtěte si nejčastější dotazy o použití této služby.
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
