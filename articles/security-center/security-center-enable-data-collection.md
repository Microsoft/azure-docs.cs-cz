---
title: Automatické nasazení agentů pro Azure Security Center | Microsoft Docs
description: Tento článek popisuje, jak nastavit Automatické zřizování agenta Log Analytics a dalších agentů používaných v Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 6130572cedaaabb9d63758a2bc25f6ebd0396562
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729857"
---
# <a name="auto-provisioning-agents-and-extensions-from-azure-security-center"></a>Automatické zřizování agentů a rozšíření z Azure Security Center

Security Center shromažďuje data z vašich virtuálních počítačů Azure, virtuálních počítačů a kontejnerů IaaS a jiných než Azure (včetně místních) počítačů, které monitorují chyby zabezpečení a hrozby. 

Shromažďování dat je nutné, aby poskytovala přehled o chybějících aktualizacích, nesprávně nakonfigurovaných nastavení zabezpečení operačního systému, stavu aplikace Endpoint Protection a ochraně před hrozbami. Shromažďování dat je potřeba jenom pro výpočetní prostředky (virtuální počítače, virtuální počítače, sady škálování na IaaS a počítače mimo Azure). Můžete využít výhod Azure Security Center i v případě, že nezřizujete agenty. budete však mít omezené zabezpečení a výše uvedené možnosti nejsou podporovány.  

Data se shromažďují pomocí:

- **Agent Log Analytics**, který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do vašeho pracovního prostoru pro účely analýzy. Příklady takových dat: typ a verze operačního systému, protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel.
- **Rozšíření zabezpečení**, jako je [Azure Policy doplněk pro Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), která také poskytují data Security Center týkající se specializovaných typů prostředků.

> [!TIP]
> Jak Security Center vzrostly, byly také vypěstovány typy prostředků, které lze monitorovat. Počet rozšíření se také zvětšil. Automatické zřizování se rozšířilo na podporu dalších typů prostředků, a to díky využití možností Azure Policy.

:::image type="content" source="./media/security-center-enable-data-collection/auto-provisioning-options.png" alt-text="Stránka nastavení automatického zřizování Security Center":::


## <a name="availability"></a>Dostupnost

| Aspekt                  | Podrobnosti                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stav vydaných verzí:          | **Funkce**: Automatické zřizování je všeobecně dostupné (GA).<br>**Agenti a rozšíření**: Agent Log Analytics pro virtuální počítače Azure je v cloudu, protože je ve verzi Preview, doplněk zásad pro KUBERNETES je GA.                |
| Stanov                | Free                                                                                                                                                                                                                         |
| Podporovaná umístění: | ![Ano](./media/icons/yes-icon.png) Počítače Azure<br>![Ne](./media/icons/no-icon.png) Počítače ARC Azure<br>![Ne](./media/icons/no-icon.png) Uzly Kubernetes<br>![Ne](./media/icons/no-icon.png) Virtual Machine Scale Sets |
| Cloud                 | ![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ano](./media/icons/yes-icon.png) US Gov, Čína gov, jiné gov                                                                                                      |
|                         |                                                                                                                                                                                                                              |


## <a name="why-use-auto-provisioning"></a>Proč používat Automatické zřizování?
Všechna agenty a rozšíření popsaná na této stránce *lze* nainstalovat ručně (viz [ruční instalace agenta Log Analytics](#manual-agent)). **Automatické zřizování** ale snižuje režijní náklady tím, že na stávající a nové počítače nainstaluje všechny požadované agenty a rozšíření, aby se zajistilo rychlejší pokrytí zabezpečení pro všechny podporované prostředky. 

Doporučujeme povolit Automatické zřizování, ale ve výchozím nastavení je zakázané.

## <a name="how-does-auto-provisioning-work"></a>Jak Automatické zřizování funguje?
Nastavení automatického zřizování Security Center mají přepínač pro každý typ podporovaného rozšíření. Pokud povolíte automatické zřizování rozšíření, přiřadíte příslušné pravidlo pro **nasazení, pokud neexistuje** , abyste se ujistili, že je rozšíření zřízené pro všechny stávající a budoucí prostředky tohoto typu.

> [!TIP]
> Přečtěte si další informace o Azure Policy efektů včetně nasazení, pokud neexistují v seznámení s [Azure Policymi efekty](../governance/policy/concepts/effects.md).

## <a name="enable-auto-provisioning-of-the-log-analytics-agent"></a>Povolit automatické zřizování agenta Log Analytics <a name="auto-provision-mma"></a>
Pokud je pro agenta Log Analytics zapnuté Automatické zřizování, Security Center nasadí agenta na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Seznam podporovaných platforem najdete [v tématu podporované platformy v Azure Security Center](security-center-os-coverage.md).

Povolení automatického zřizování agenta Log Analytics:

1. V nabídce Security Center vyberte **cenové & nastavení**.
1. Vyberte příslušné předplatné.
1. Na stránce **Automatické zřizování** nastavte stav agenta na **zapnuto**.
1. V podokně možnosti konfigurace Definujte pracovní prostor, který se má použít.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Možnosti konfigurace pro Automatické zřizování Log Analyticsch agentů k virtuálním počítačům" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Připojení virtuálních počítačů Azure k výchozím pracovním prostorům vytvořeným pomocí Security Center** -Security Center vytvoří novou skupinu prostředků a výchozí pracovní prostor ve stejném geografickém umístění a připojí agenta k tomuto pracovnímu prostoru. Pokud předplatné obsahuje virtuální počítače z několika geografických umístění, Security Center vytvoří několik pracovních prostorů, které zajistí dodržování požadavků na ochranu dat.

        Konvence pojmenování pro pracovní prostor a skupinu prostředků: 
        - Pracovní prostor: DefaultWorkspace-[ID_předplatného]-[zeměpisné umístění] 
        - Skupina prostředků: DefaultResourceGroup-[geografické] 

        Security Center automaticky povolí Security Center řešení v pracovním prostoru podle cenové úrovně nastavené pro předplatné. 

        > [!TIP]
        > Otázky týkající se výchozích pracovních prostorů najdete v těchto tématech:
        >
        > - [Účtují se mi Azure Monitor protokoly v pracovních prostorech vytvořených pomocí Security Center?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Kde je vytvořen výchozí pracovní prostor Log Analytics?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Můžu odstranit výchozí pracovní prostory vytvořené pomocí Security Center?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Připojení virtuálních počítačů Azure k jinému pracovnímu prostoru** – v rozevíracím seznamu vyberte pracovní prostor pro ukládání shromážděných dat. Rozevírací seznam obsahuje všechny pracovní prostory ve všech vašich předplatných. Tuto možnost můžete použít ke shromažďování dat z virtuálních počítačů spuštěných v různých předplatných a jejich uložení ve vybraném pracovním prostoru.  

        Pokud již máte pracovní prostor Log Analytics, můžete chtít použít stejný pracovní prostor (vyžaduje oprávnění ke čtení a zápisu v pracovním prostoru). Tato možnost je užitečná, pokud používáte centralizovaný pracovní prostor ve vaší organizaci a chcete ho použít pro shromažďování dat zabezpečení. Další informace najdete v podrobnějších informacích o [správě přístupu k datům a pracovním prostorům v Azure monitor](../azure-monitor/logs/manage-access.md).

        Pokud váš vybraný pracovní prostor už má povolené řešení Security nebo SecurityCenterFree, ceny se nastaví automaticky. V takovém případě nainstalujte řešení Security Center v pracovním prostoru:

        1. V nabídce Security Center otevřete **cenové & nastavení**.
        1. Vyberte pracovní prostor, do kterého budete připojovat agenty.
        1. Vyberte **Azure Defender v** nebo **Azure Defender vypnuto**.

1. V konfiguraci **událostí zabezpečení systému Windows** vyberte množství nezpracovaných dat události, která se mají uložit:
    - **Žádné** – zakažte úložiště událostí zabezpečení. Toto je výchozí nastavení.
    - **Minimální** – malá sada událostí pro případ, kdy chcete minimalizovat objem události.
    - **Společné** – sada událostí, které vyhovují většině zákazníků a poskytují úplný záznam pro audit.
    - **Všechny události** – pro zákazníky, kteří chtějí mít jistotu, že budou uloženy všechny události.

    > [!TIP]
    > Pokud chcete nastavit tyto možnosti na úrovni pracovního prostoru, přečtěte si téma [nastavení možnosti události zabezpečení na úrovni pracovního prostoru](#setting-the-security-event-option-at-the-workspace-level).
    > 
    > Další informace o těchto možnostech najdete v tématu [Možnosti událostí zabezpečení systému Windows pro agenta Log Analytics](#data-collection-tier).

1. V podokně Konfigurace vyberte **použít** .

1. Vyberte **Uložit**. Pokud je potřeba zřídit pracovní prostor, může instalace agenta trvat až 25 minut.

1. Zobrazí se dotaz, jestli chcete překonfigurovat monitorované virtuální počítače, které byly dřív připojené k výchozímu pracovnímu prostoru:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="Kontrola možností konfigurace monitorovaných virtuálních počítačů":::

    - **Ne** – nové nastavení pracovního prostoru se použije jenom u nově zjištěných virtuálních počítačů, které nemají nainstalovaného agenta Log Analytics.
    - **Ano** – nové nastavení pracovního prostoru se použije na všechny virtuální počítače a každý virtuální počítač, který je aktuálně připojený k Security Center vytvořenému pracovnímu prostoru, se znovu připojí k novému cílovému pracovnímu prostoru.

   > [!NOTE]
   > Pokud vyberete **Ano**, neodstraňujte pracovní prostory vytvořené pomocí Security Center, dokud se všechny virtuální počítače znovu nepřipojí k novému cílovému pracovnímu prostoru. Tato operace se nezdařila, pokud je pracovní prostor odstraněn příliš brzy.


## <a name="enable-auto-provisioning-of-extensions"></a>Povolení automatického zřizování rozšíření

Povolení automatického zřizování jiného rozšíření než agenta Log Analytics: 

1. V nabídce Security Center v Azure Portal vyberte **cenové & nastavení**.
1. Vyberte příslušné předplatné.
1. Vyberte **Automatické zřizování**.
1. Pokud povolujete Automatické zřizování pro agenta závislostí společnosti Microsoft, zajistěte, aby byl agent Log Analytics nastaven na automatické nasazení. 
1. Pro příslušné rozšíření přepněte stav na **zapnuto** .

    :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Přepnutím povolíte automatické zřizování pro doplněk zásad K8s.":::

1. Vyberte **Uložit**. Zásada Azure je přiřazena a je vytvořen úkol nápravy.

    |Linka  |Zásady  |
    |---------|---------|
    |Doplněk zásad pro Kubernetes|[Nasazení Azure Policy doplňku do clusterů služby Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
    |Microsoft Dependency Agent (Preview) (virtuální počítače s Windows)|[Nasazení agenta závislostí pro virtuální počítače s Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
    |Microsoft Dependency Agent (Preview) (virtuální počítače se systémem Linux)|[Nasazení agenta závislostí pro virtuální počítače se systémem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|



## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Možnosti událostí zabezpečení systému Windows pro agenta Log Analytics <a name="data-collection-tier"></a> 

Výběr úrovně shromažďování dat v Azure Security Center ovlivňuje jenom *ukládání* událostí zabezpečení v pracovním prostoru Log Analytics. Agent Log Analytics bude stále shromažďovat a analyzovat události zabezpečení vyžadované Security Center ochrany před hrozbami, a to bez ohledu na úroveň událostí zabezpečení, které se rozhodnete ukládat do svého pracovního prostoru. Když se rozhodnete ukládat události zabezpečení, umožníte tím šetření, vyhledávání a auditování těchto událostí ve vašem pracovním prostoru.

### <a name="requirements"></a>Požadavky 
K ukládání dat událostí zabezpečení systému Windows je vyžadován Azure Defender. [Přečtěte si další informace o Azure Defenderu](azure-defender.md).

Ukládání dat v Log Analytics může pro úložiště dat nabývat další poplatky. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="information-for-azure-sentinel-users"></a>Informace o uživatelích Sentinel Azure 
Uživatelé služby Azure Sentinel: Všimněte si, že kolekce událostí zabezpečení v rámci jednoho pracovního prostoru se dá nakonfigurovat buď z Azure Security Center, nebo pomocí Azure Sentinel, ale ne z obou. Pokud plánujete přidat službu Azure Sentinel do pracovního prostoru, který už získává výstrahy od Azure Security Center a je nastavená na shromažďovat události zabezpečení, máte dvě možnosti:
- Ponechte shromažďování událostí zabezpečení v Azure Security Center tak, jak je. Tyto události budete moct dotazovat a analyzovat v Azure Sentinel i v Azure Defenderu. Nebudete ale moct monitorovat stav připojení konektoru nebo změnit jeho konfiguraci v konfiguraci Azure Sentinel. Pokud je to pro vás důležité, zvažte druhou možnost.
- Zakažte shromažďování událostí zabezpečení v Azure Security Center (nastavením **událostí zabezpečení systému Windows** na **žádné** v konfiguraci agenta Log Analytics). Pak přidejte konektor události zabezpečení do Azure Sentinel. Stejně jako u první volby budete moct dotazovat a analyzovat události v konfiguraci Azure Sentinel i v Azure Defenderu nebo v programu ASC, ale teď budete moct monitorovat stav připojení konektoru nebo změnit jeho konfiguraci v rámci a jenom v Azure Sentinel.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Jaké typy událostí jsou uložené pro "běžné" a "minimální"?
Tyto sady byly navržené tak, aby vycházely z typických scénářů. Nezapomeňte vyhodnotit, který z nich vyhovuje vašim potřebám, než ho implementujete.

Pokud chcete zjistit události pro **společné** a **minimální** možnosti, pracovali jsme se zákazníky a oborovými standardy, abyste se dozvěděli o nefiltrované frekvenci jednotlivých událostí a jejich využití. V tomto procesu jsme použili následující pokyny:

- **Minimální** – Ujistěte se, že tato sada pokrývá jenom události, které můžou indikovat úspěšné porušení zabezpečení a důležité události, které mají velmi malý svazek. Tato sada například obsahuje úspěšné a neúspěšné přihlášení uživatele (ID události 4624, 4625), ale neobsahuje odhlášení, které je důležité pro auditování, ale ne smysluplné pro detekci a má poměrně vysoký objem. Většina dat v této sadě je události přihlášení a událost vytvoření procesu (ID události 4688).
- **Common** – v této sadě uveďte úplný záznam pro audit uživatelů. Například tato sada obsahuje přihlašovací údaje uživatele i odhlášení uživatele (ID události 4634). Zahrnujeme i akce auditování, jako jsou změny skupiny zabezpečení, klíčové operace protokolu Kerberos řadiče domény a další události, které jsou doporučeny pro oborové organizace.

Do společné sady byly zahrnuty události s velmi nízkým objemem, jako je hlavní motivace a jejich výběr přes všechny události, je snížit objem a Nefiltrovat konkrétní události.

Zde je úplný rozpis ID událostí zabezpečení a zámků aplikací pro každou sadu:

| Datová vrstva | Shromážděné indikátory událostí |
| --- | --- |
| Minimální | 1102, 4624, 4625, 4657, 4663, 4688,, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Společné | 1, čl. 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, [1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, |
| | 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> - Pokud používáte objekt GPO (Zásady skupiny Object), doporučuje se povolit událost vytvoření procesu zásady auditu 4688 a pole *CommandLine* v události 4688. Další informace o události vytvoření procesu 4688 najdete v tématu [Nejčastější dotazy](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)k Security Center. Další informace o těchto zásadách auditu najdete v tématu [doporučení zásad auditu](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Pokud chcete povolit shromažďování dat pro [Adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center v režimu auditování nakonfiguruje místní zásadu AppLockeru, aby povolovala všechny aplikace. Tím dojde k tomu, že AppLocker generuje události, které se pak shromažďují a využívají Security Center. Je důležité si uvědomit, že tato zásada nebude nakonfigurovaná na žádném počítači, na kterém je už nakonfigurovaná zásada AppLockeru. 
> - Aby bylo možné shromažďovat události platformy Windows Filtering [ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), je nutné povolit [připojení platformy pro filtrování auditu](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/set/Subcategory: "filtrování připojení platformy"/Success: Enable).
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>Nastavení možnosti události zabezpečení na úrovni pracovního prostoru

Můžete definovat úroveň dat událostí zabezpečení, která se mají ukládat na úrovni pracovního prostoru.

1. V nabídce Security Center v Azure Portal vyberte **cenové & nastavení**.
1. Vyberte příslušný pracovní prostor. Jedinými událostmi shromažďování dat pro pracovní prostor jsou události zabezpečení systému Windows, které jsou popsány na této stránce.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Nastavení dat událostí zabezpečení, která se mají ukládat v pracovním prostoru":::

1. Vyberte množství nezpracovaných dat události, která chcete uložit, a vyberte **Uložit**.

## <a name="manual-agent-provisioning"></a>Ruční zřizování agentů <a name="manual-agent"></a>
 
Ruční instalace agenta Log Analytics:

1. Zakažte Automatické zřizování.

1. Volitelně můžete vytvořit pracovní prostor.

1. Povolte v pracovním prostoru, na kterém instalujete agenta Log Analytics, službu Azure Defender:

    1. V nabídce Security Center vyberte **cenové & nastavení**.

    1. Nastavte pracovní prostor, do kterého instalujete agenta. Ujistěte se, že je pracovní prostor ve stejném předplatném, které používáte v Security Center a máte oprávnění ke čtení a zápisu pro tento pracovní prostor.

    1. Vyberte **Azure Defender on** a **Save (Uložit**).

       >[!NOTE]
       >Pokud má pracovní prostor již povolené řešení **Security** nebo **SecurityCenterFree** , ceny se nastaví automaticky. 

1. Pokud chcete nasadit agenty na nové virtuální počítače pomocí šablony Správce prostředků, nainstalujte agenta Log Analytics:

   - [Instalace agenta Log Analytics pro Windows](../virtual-machines/extensions/oms-windows.md)
   - [Instalace agenta Log Analytics pro Linux](../virtual-machines/extensions/oms-linux.md)

1. Pokud chcete nasadit agenty na svém stávajícím virtuálním počítači, postupujte podle pokynů v tématu [shromáždění dat o službě Azure Virtual Machines](../azure-monitor/vm/quick-collect-azurevm.md) (část **shromažďování dat o událostech a výkonu** je volitelná).

1. Pokud chcete použít PowerShell k nasazení agentů, postupujte podle pokynů v dokumentaci k virtuálním počítačům:

    - [Počítače s Windows](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Počítače s Linuxem](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> Pokyny, jak připojit Security Center pomocí prostředí PowerShell, najdete v tématu [Automatizace připojování Azure Security Center pomocí prostředí PowerShell](security-center-powershell-onboarding.md).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatické zřizování v případech již existující instalace agenta <a name="preexisting"></a> 

Následující případy použití určují, jak Automatické zřizování funguje v případech, kdy už je agent nebo rozšíření nainstalované. 

- **Agent Log Analytics je na počítači nainstalovaný, ale ne jako rozšíření (přímý Agent)** – Pokud je agent Log Analytics nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nainstaluje rozšíření agenta Log Analytics a může upgradovat agenta Log Analytics na nejnovější verzi.
Nainstalovaný Agent bude pokračovat v hlášení již nakonfigurovaných pracovních prostorů a dále bude hlásit pracovnímu prostoru nakonfigurovanému v Security Center (pro počítače se systémem Windows je podporována podpora více domovských stránek).
Pokud je nakonfigurovaným pracovním prostorem pracovní prostor uživatele (ve výchozím nastavení není Security Center), budete muset do něj nainstalovat řešení "Security" nebo "SecurityCenterFree", aby bylo možné Security Center zahájit zpracování událostí z virtuálních počítačů a počítačů, které do daného pracovního prostoru hlásí.

    Pro počítače se systémem Linux zatím není podporována podpora více domovských stránek agenta, takže pokud je zjištěna existující instalace agenta, Automatické zřizování nebude provedeno a konfigurace počítače nebude změněna.

    Pro existující počítače v předplatných, které jsou připojené k Security Center do 17. března 2019, když se zjistí existující agent, nebude rozšíření agenta Log Analytics nainstalované a počítač nebude ovlivněn. Pro tyto počítače si Projděte doporučení "vyřešit problémy se stavem agenta monitorování na vašich počítačích" a vyřešte problémy s instalací agenta na těchto počítačích.
  
- **V počítači je nainstalován agent System Center Operations Manager** – Security Center bude instalovat rozšíření agenta Log Analytics na straně sebe na stávající Operations Manager. Stávající agent Operations Manager bude pokračovat v hlášení na Server Operations Manager normálně. Agent Operations Manager a Agent Log Analytics sdílejí běžné běhové knihovny, které budou během tohoto procesu aktualizovány na nejnovější verzi. Pokud je nainstalovaná verze agenta Operations Manager 2012 **, nepovolujte** Automatické zřizování.

- K **dispozici již existující rozšíření virtuálního počítače**:
    - Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Security Center nepřepisují existující připojení k pracovním prostorům uživatelů. Security Center bude ukládat data zabezpečení z virtuálního počítače v pracovním prostoru, který je už připojený, a to za předpokladu, že je na něm nainstalované řešení Security nebo SecurityCenterFree. Security Center může v tomto procesu upgradovat verzi rozšíření na nejnovější verzi.
    - Chcete-li zjistit, který pracovní prostor existující rozšíření odesílá data, spusťte test a [Ověřte připojení pomocí Azure Security Center](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). Případně můžete otevřít Log Analytics pracovní prostory, vybrat pracovní prostor, vybrat virtuální počítač a podívat se na připojení agenta Log Analytics.
    - Pokud máte prostředí, ve kterém je agent Log Analytics nainstalovaný na klientských pracovních stanicích a vytváření sestav do existujícího pracovního prostoru Log Analytics, Projděte si seznam [operačních systémů podporovaných Azure Security Center](security-center-os-coverage.md) , abyste se ujistili, že je váš operační systém podporovaný. Další informace najdete v tématu [existující zákazníci Log Analytics](./faq-azure-monitor-logs.md).
 

## <a name="disable-auto-provisioning"></a>Zakázat Automatické zřizování <a name="offprovisioning"></a>

Když zakážete Automatické zřizování, agenti se na nových virtuálních počítačích nezřídí.

Vypnutí automatického zřizování agenta:

1. V nabídce Security Center na portálu vyberte **cenové & nastavení**.
1. Vyberte příslušné předplatné.
1. Vyberte **Automatické zřizování**.
1. Pro příslušného agenta přepněte stav na **vypnuto** .

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Přepne za účelem zakázání automatického zřizování podle typu agenta.":::

1. Vyberte **Uložit**. Pokud je Automatické zřizování zakázané, nezobrazuje se výchozí oddíl konfigurace pracovního prostoru:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Pokud je Automatické zřizování zakázané, buňka konfigurace je prázdná.":::


> [!NOTE]
>  Při vypnutí automatického zřizování se agent Log Analytics neodebere z virtuálních počítačů Azure, ve kterých se agent zřídil. Informace o odebrání rozšíření OMS najdete v tématu [návody odebrání rozšíření OMS nainstalovaných pomocí Security Center](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Řešení potíží

-   Informace o tom, jak identifikovat Automatické zřizování problémů, najdete v tématu [monitorování problémů se stavem agenta](security-center-troubleshooting-guide.md#mon-agent).

-  Informace o tom, jak identifikovat požadavky na síť agenta monitorování, najdete v tématu [řešení potíží s požadavky na síť agenta](security-center-troubleshooting-guide.md#mon-network-req)
-   Pokud chcete identifikovat problémy ručního připojování, přečtěte si téma řešení potíží s [připojováním Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Identifikace problémů nemonitorovaných virtuálních počítačů a počítačů:

    VIRTUÁLNÍ počítač nebo počítač není monitorovaný Security Center, pokud počítač nepoužívá rozšíření agenta Log Analytics. Počítač může mít již nainstalovaný místní agent, například agenta OMS Direct nebo Agent System Center Operations Manager. Počítače s těmito agenty jsou označeny jako nemonitorované, protože tito agenti nejsou v Security Center plně podporovaná. Pokud chcete naplno využívat všechny funkce služby Security Center, potřebujete rozšíření agenta Log Analytics.

    Další informace o důvodech, Security Center nelze úspěšně monitorovat virtuální počítače a počítače inicializované pro Automatické zřizování, najdete v tématu [monitorování problémů se stavem agenta](security-center-troubleshooting-guide.md#mon-agent).




## <a name="next-steps"></a>Další kroky
Tento článek ukazuje, jak funguje shromažďování dat a Automatické zřizování v Security Center. Další informace o Security Center najdete na následujících stránkách:

- [Azure Security Center – nejčastější dotazy](faq-general.md) – Přečtěte si nejčastější dotazy o použití této služby.
- [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.

Tento článek popisuje, jak nainstalovat agenta Log Analytics a nastavit pracovní prostor Log Analytics, do kterého se mají ukládat shromážděná data. Pro povolení shromažďování dat jsou vyžadovány obě operace. Pokud se data ukládají v Log Analytics, ať už používáte nový nebo existující pracovní prostor, můžou se za úložiště dat účtovat další poplatky. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

