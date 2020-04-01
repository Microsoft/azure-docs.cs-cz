---
title: Shromažďování dat v Azure Security Center | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nainstalovat agenta analýzy protokolů a nastavit pracovní prostor Analýzy protokolů, do kterého se mají shromažďovat data ukládat.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 61d0a57c541837ab3aebf65e47d757f7ecbe7e40
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435986"
---
# <a name="data-collection-in-azure-security-center"></a>Shromažďování dat v Azure Security Center
Security Center shromažďuje data z vašich virtuálních počítačů (VM), škálovacísady virtuálních počítačů, kontejnery IaaS a počítačů, které nejsou místní (včetně místních), za účelem monitorování slabých míst zabezpečení a hrozeb. Data se shromažďují pomocí agenta Analýzy protokolů, který čte různé konfigurace související se zabezpečením a protokoly událostí z počítače a zkopíruje data do pracovního prostoru pro analýzu. Příklady takových dat jsou: typ a verze operačního systému, protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel. Agent analýzy protokolů také zkopíruje soubory s výpisem stavu systému do vašeho pracovního prostoru.

Shromažďování dat je nutné poskytnout přehled o chybějící aktualizace, nesprávně nakonfigurované nastavení zabezpečení operačního systému, stav ochrany koncového bodu a ochrany zdraví a ohrožení. 

Tento článek popisuje, jak nainstalovat agenta analýzy protokolů a nastavit pracovní prostor Analýzy protokolů, do kterého se mají shromažďovat data ukládat. Obě operace jsou nutné k povolení shromažďování dat. 

> [!NOTE]
> - Shromažďování dat je potřeba jenom pro výpočetní prostředky (virtuální počítače, škálovací sady virtuálních počítačů, kontejnery IaaS a počítače mimo Azure). Azure Security Center můžete využívat i v případě, že agenty nezřazujete. budete však mít omezené zabezpečení a výše uvedené funkce nejsou podporovány.  
> - Seznam podporovaných platforem najdete [v tématu Podporované platformy v Azure Security Center](security-center-os-coverage.md).
> - Ukládání dat v Log Analytics, ať už používáte nový nebo existující pracovní prostor, může být účtovány další poplatky za ukládání dat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Povolit automatické zřizování agenta analýzy protokolů<a name="auto-provision-mma"></a>

Chcete-li shromažďovat data z počítačů, měli byste mít nainstalován agent a analýzy protokolů. Instalaci agenta lze provést automaticky (doporučeno) nebo můžete nainstalovat agenta ručně.  

>[!NOTE]
> Automatické zřizování je ve výchozím nastavení vypnuté. Chcete-li nastavit Centrum zabezpečení tak, aby ve výchozím nastavení instalovatelé automatickézřivací nastavení, nastavte ji **na zapnuto**.
>

Když je zapnuté automatické zřizování, Security Center zřídí agenta analýzy protokolů na všech podporovaných virtuálních počítačích Azure a všech nových, které jsou vytvořené. Automatické zřizování se důrazně doporučuje, ale k dispozici je také ruční instalace agenta. [Přečtěte si, jak nainstalovat rozšíření Agent analýzy protokolů](#manual-agent).



Povolení automatického zřizování agenta analýzy protokolů:
1. V hlavní nabídce Centra zabezpečení vyberte **Nastavení & ceny**.
2. Klikněte na příslušné předplatné

   ![Výběr předplatného][7]

3. Vyberte **shromažďování dat**.
4. V části **Automatické zřizování**vyberte **Zapnuto,** chcete-li povolit automatické zřizování.
5. Vyberte **Uložit**.

   ![Povolení automatického zřizování][1]

>[!NOTE]
> - Pokyny k zajištění již existující instalace naleznete [v tématu Automatické zřizování v případě předchozí instalace agenta](#preexisting).
> - Pokyny k ručnímu zřizování najdete [v tématu Ruční instalace rozšíření agenta analýzy protokolů](#manual-agent).
> - Pokyny k vypnutí automatického zřizování najdete v [tématu Vypnutí automatického zřizování](#offprovisioning).
> - Pokyny, jak napojit Centrum zabezpečení pomocí PowerShellu, najdete [v tématu Automatizace registrace Centra zabezpečení Azure pomocí PowerShellu](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfigurace pracovního prostoru
Data shromážděná službou Security Center se ukládají v pracovních prostorech služby Log Analytics. Můžete vybrat, že data shromážděná z virtuálních aplikací Azure budou uložena v pracovních prostorech vytvořených Centrem zabezpečení nebo v existujícím pracovním prostoru, který jste vytvořili. 

Konfigurace pracovního prostoru je nastavena na jedno předplatné a mnoho předplatných může používat stejný pracovní prostor.

### <a name="using-a-workspace-created-by-security-center"></a>Použití pracovního prostoru vytvořeného centrem zabezpečení

Centrum zabezpečení může automaticky vytvořit výchozí pracovní prostor, do kterého budou data ukládat. 

Výběr pracovního prostoru vytvořeného centrem zabezpečení:

1. V části **Výchozí konfigurace pracovního prostoru**vyberte Použít pracovní prostor (pracovní prostory) vytvořené centrem zabezpečení.
   ![Vybrat cenovou úroveň][10] 

1. Klikněte na **Uložit**.<br>
    Security Center vytvoří novou skupinu prostředků a výchozí pracovní prostor v této geografické poloze a připojí agenta k tomuto pracovnímu prostoru. Konvence pojmenování pro pracovní prostor a skupinu prostředků je:<br>
   **Pracovní prostor: DefaultWorkspace-[subscription-ID]-[geo]<br> Skupina prostředků: DefaultResourceGroup-[geo]**

   Pokud předplatné obsahuje virtuální počítače z více geografických lokací, pak Security Center vytvoří více pracovních prostorů. Pro zachování pravidel ochrany osobních údajů je vytvořeno více pracovních prostorů.
1. Security Center automaticky povolí řešení Security Center v pracovním prostoru podle cenové úrovně nastavené pro předplatné. 

> [!NOTE]
> Cenová úroveň Log Analytics pracovních prostorů vytvořených službou Security Center nemá vliv na fakturaci centra zabezpečení. Security Center se vždy fakturuje podle zásad zabezpečení Security Center a řešení nainstalovaných v pracovním prostoru. U úrovně Free Security Center povolí řešení *SecurityCenterFree* ve výchozím pracovním prostoru. U úrovně Standard Security Center povolí řešení *SecurityCenterFree* ve výchozím pracovním prostoru.
> Ukládání dat v Log Analytics může být účtovány další poplatky za ukládání dat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

Další informace o existujících účtech analýzy protokolů naleznete [v tématu Existing log analytics customers](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Použití existujícího pracovního prostoru

Pokud již máte existující pracovní prostor Analýzy protokolů, můžete použít stejný pracovní prostor.

Chcete-li použít existující pracovní prostor Log Analytics, musíte mít oprávnění ke čtení a zápisu v pracovním prostoru.

> [!NOTE]
> Řešení povolená v existujícím pracovním prostoru se použijí na virtuální počítače Azure, které jsou k němu připojené. U placených řešení by to mohlo vést k dodatečným poplatkům. Z hlediska ochrany osobních údajů se ujistěte, že se vybraný pracovní prostor nachází ve správné geografické oblasti.
> Ukládání dat do analýzy protokolů může být účtovány další poplatky za ukládání dat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

Výběr existujícího pracovního prostoru Analýzy protokolů:

1. V části **Výchozí konfigurace pracovního prostoru**vyberte Použít jiný pracovní **prostor**.

   ![Výběr existujícího pracovního prostoru][2]

2. V rozbalené nabídce vyberte pracovní prostor pro ukládání shromážděných dat.

   > [!NOTE]
   > V rozbalovací nabídce jsou k dispozici všechny pracovní prostory ve všech vašich předplatných. Další informace najdete v [tématu výběr pracovního prostoru mezi odběry.](security-center-enable-data-collection.md#cross-subscription-workspace-selection) Musíte mít oprávnění k přístupu do pracovního prostoru.
   >
   >

3. Vyberte **Uložit**.
4. Po výběru **možnosti Uložit**budete dotázáni, zda chcete překonfigurovat monitorované virtuální počítače, které byly dříve připojeny k výchozímu pracovnímu prostoru.

   - Pokud chcete, aby se nové nastavení pracovního prostoru použilo jenom na nové virtuální počítače, vyberte **ne.** Nové nastavení pracovního prostoru platí pouze pro instalace nových agentů. nově zjištěné virtuální chodů, které nemají nainstalován agent a služby Log Analytics.
   - Pokud chcete, aby se nové nastavení pracovního prostoru použilo na všechny virtuální počítače, vyberte **Ano.** Kromě toho každý virtuální virtuální movitý virtuální movitý virtuální míchaný k pracovnímu prostoru vytvořenému centrem zabezpečení se navíc znovu připojí k novému cílovému pracovnímu prostoru.

   > [!NOTE]
   > Pokud vyberete Ano, nesmíte odstranit pracovní prostor (y) vytvořené Security Center, dokud všechny virtuální chody byly znovu připojeny k novému cílovému pracovnímu prostoru. Tato operace se nezdaří, pokud je pracovní prostor odstraněn příliš brzy.
   >
   >

   - Chcete-li operaci zrušit, vyberte **možnost Storno.**

     ![Výběr existujícího pracovního prostoru][3]

5. Vyberte cenovou úroveň pro požadovaný pracovní prostor, který chcete nastavit agenta analýzy protokolů. <br>Chcete-li použít existující pracovní prostor, nastavte cenovou úroveň pro pracovní prostor. Tím nainstalujete řešení centra zabezpečení do pracovního prostoru, pokud již není k dispozici.

    a.  V hlavní nabídce Centra zabezpečení vyberte **Nastavení cenové &**.
     
    b.  Vyberte požadovaný pracovní prostor, ve kterém chcete agenta připojit.
        ![Vyberte][7] pracovní prostor c. Nastavte cenovou úroveň.
        ![Vybrat cenovou úroveň][9]
   
   >[!NOTE]
   >Pokud je v pracovním prostoru již povoleno řešení **Zabezpečení** nebo **SecurityCenterFree,** ceny se nastaví automaticky. 

## <a name="cross-subscription-workspace-selection"></a>Výběr pracovního prostoru mezi odběry
Když vyberete pracovní prostor, do kterého chcete data ukládat, budou k dispozici všechny pracovní prostory ve všech vašich předplatných. Výběr pracovního prostoru z jiného předplatného vám umožňuje shromažďovat data z virtuálních počítačů, které běží v jiných předplatných, a ukládat je do vybraného pracovního prostoru. Tento výběr je užitečný, pokud ve vaší organizaci používáte centralizovaný pracovní prostor a chcete ho použít pro shromažďování dat o zabezpečení. Další informace o správě pracovních prostorů naleznete v [tématu Správa přístupu k pracovním prostorům](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Úroveň shromažďování dat
Výběr vrstvy shromažďování dat v Azure Security Center ovlivní pouze úložiště událostí zabezpečení v pracovním prostoru služby Log Analytics. Agent Log Analytics bude stále shromažďovat a analyzovat události zabezpečení potřebné pro ochranu před hrozbami Centra zabezpečení Azure, bez ohledu na to, kterou úroveň událostí zabezpečení se rozhodnete uložit do pracovního prostoru Log Analytics (pokud existuje). Volba ukládání událostí zabezpečení v pracovním prostoru vám umožní prozkoumávání, vyhledávání a auditování těchto událostí v pracovním prostoru. 
> [!NOTE]
> Ukládání dat do analýzy protokolů může být účtovány další poplatky za ukládání dat. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Můžete zvolit správné zásady filtrování pro vaše předplatná a pracovní prostory ze čtyř sad událostí, které se mají uložit do pracovního prostoru: 

- **Žádné** – Zakázat úložiště událostí zabezpečení. Toto je výchozí nastavení.
- **Minimální** – menší sada událostí pro zákazníky, kteří chtějí minimalizovat objem událostí.
- **Běžné** – Jedná se o sadu událostí, která uspokojí většinu zákazníků a umožňuje jim úplnou auditní stopu.
- **Všechny události** – pro zákazníky, kteří se chtějí ujistit, že jsou uloženy všechny události.


> [!NOTE]
> Tyto sady událostí zabezpečení jsou k dispozici pouze na úrovni Standard centra zabezpečení. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
Tyto sady byly navrženy tak, aby řešily typické scénáře. Ujistěte se, že vyhodnotit, který z nich vyhovuje vašim potřebám před jeho implementací.
>
>

Abychom zjistili události, které budou patřit do **sad běžných** a **minimálních** událostí, spolupracovali jsme se zákazníky a oborovými standardy, abychom se dozvěděli o nefiltrované frekvenci každé události a jejich použití. V tomto procesu jsme použili následující pokyny:

- **Minimální** – Ujistěte se, že tato sada zahrnuje pouze události, které mohou znamenat úspěšné porušení a důležité události, které mají velmi nízký objem. Tato sada například obsahuje úspěšné a neúspěšné přihlášení uživatele (ID události 4624, 4625), ale neobsahuje odhlásit, což je důležité pro auditování, ale není smysluplné pro detekci a má relativně vysoký objem. Většina objemu dat této sady je události přihlášení a událost vytvoření procesu (ID události 4688).
- **Běžné** – v této sadě poskytněte úplný záznam auditu uživatele. Tato sada například obsahuje přihlášení uživatele i odhlášení uživatele (ID události 4634). Zahrnujeme auditování akcí, jako jsou změny skupiny zabezpečení, operace protokolu Kerberos řadiče domény klíče a další události doporučené průmyslovými organizacemi.

Události, které mají velmi nízký objem, byly zahrnuty do společné sady jako hlavní motivace k výběru přes všechny události je snížit objem a ne odfiltrovat konkrétní události.

Zde je úplný rozpis ID událostí zabezpečení a suty aplikací pro každou sadu:

| Datová vrstva | Shromážděné indikátory událostí |
| --- | --- |
| Minimální | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Společné | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Pokud používáte objekt zásad skupiny (GPO), doporučujeme povolit zásady auditu Událost vytváření procesů 4688 a pole *CommandLine* uvnitř události 4688. Další informace o události vytváření procesů 4688 naleznete v [nejčastějších dotazech](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)centra zabezpečení . Další informace o těchto zásadách auditu naleznete v [tématu Doporučení zásad auditu](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Chcete-li povolit shromažďování dat pro [adaptivní ovládací prvky aplikací](security-center-adaptive-application.md), security center konfiguruje místní zásady nástroje AppLocker v režimu auditování tak, aby umožňovaly všechny aplikace. To způsobí, že AppLocker generovat události, které jsou pak shromažďovány a využívány Security Center. Je důležité si uvědomit, že tato zásada nebude nakonfigurována na všech počítačích, na kterých již nakonfigurované zásady nástroje AppLocker. 
> - Chcete-li shromáždit [ID události platformy](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)filtrování systému Windows 5156 , musíte povolit [připojení platformy filtrování auditu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

Jak zvolit zásady filtrování:
1. Na stránce **Shromažďování dat** vyberte zásady filtrování v části **Události zabezpečení**.
2. Vyberte **Uložit**.

   ![Zvolit zásady filtrování][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatické zřizování v případě již existujícíinstalace agenta<a name="preexisting"></a> 

Následující případy použití určují, jak funguje automatické zřizování v případech, kdy již existuje agent nebo rozšíření nainstalováno. 

- Agent log Analytics je nainstalován v počítači, ale ne jako rozšíření (přímý agent)<br>
Pokud agent analýzy protokolů je nainstalován přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nainstaluje rozšíření Agent log Analytics a může upgradovat agenta analýzy protokolů na nejnovější verzi.
Nainstalovaný agent bude nadále podávat zprávy do svého již nakonfigurovaného pracovního prostoru a navíc bude podavat do pracovního prostoru nakonfigurovaného v Centru zabezpečení (Multi-navádění je podporováno v počítačích se systémem Windows).
Pokud je nakonfigurovaný pracovní prostor uživatelský mj.<br>
<br>
U počítačů s Linuxem agent multi-navádění ještě není podporováno – proto pokud je zjištěna instalace existujícího agenta, nedojde k automatickému zřizování a konfigurace počítače se nezmění.
<br>
Pro existující počítače na předplatná na palubě Security Center před 2019-03-17, kdy bude zjištěna existující agent, rozšíření agent a log Analytics agent nebude nainstalován a počítač nebude ovlivněna. Pro tyto počítače naleznete v doporučení "Vyřešit problémy se stavem agenta monitorování na vašich počítačích" k vyřešení problémů s instalací agenta na těchto počítačích.

  
- V počítači je nainstalován agent Operations Manager system center<br>
Centrum zabezpečení nainstaluje rozšíření agenta analýzy protokolů vedle sebe do existujícího nástroje Operations Manager. Stávající agent nástroje Operations Manager bude nadále normálně podávat zprávy serveru Nástroje operations manageru. Všimněte si, že agent Operations Manager a Agent analýzy protokolů sdílejí společné knihovny za běhu, které budou během tohoto procesu aktualizovány na nejnovější verzi.
Poznámka : Pokud je nainstalován agent Operations Manager verze 2012, **nezapínajte** automatické zřizování.<br>

- Je k dispozici již existující rozšíření virtuálního mísy.<br>
    - Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Centrum zabezpečení nepřepisuje existující připojení k uživatelským pracovním prostorům. Security Center bude ukládat data zabezpečení z virtuálního počítači v pracovním prostoru již připojené, za předpokladu, že "zabezpečení" nebo "securityFree" řešení bylo nainstalováno na něm. Security Center může upgradovat verzi rozšíření na nejnovější verzi v tomto procesu.  
    - Chcete-li zjistit, do kterého pracovního prostoru stávající rozšíření odesílá data, spusťte test [na ověření připojení pomocí Centra zabezpečení Azure](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Případně můžete otevřít pracovní prostory Log Analytics, vybrat pracovní prostor, vybrat virtuální hod a podívat se na připojení agenta Log Analytics. 
    - Pokud máte prostředí, kde je agent Log Analytics nainstalován na klientských pracovních stanicích a vykazovává se do existujícího pracovního prostoru Log Analytics, projděte si seznam [operačních systémů podporovaných Službou Zabezpečení Azure a](security-center-os-coverage.md) ujistěte se, že je váš operační systém podporovaný. Další informace naleznete [v tématu Existing log analytics customers](./faq-azure-monitor-logs.md).
 
### <a name="turn-off-automatic-provisioning"></a>Vypnutí automatického zřizování<a name="offprovisioning"></a>
Automatické zřizování prostředků můžete kdykoli vypnout vypnutím tohoto nastavení v zásadách zabezpečení. 


1. Vraťte se do hlavní nabídky Centra zabezpečení a vyberte zásady zabezpečení.
2. V řádku předplatného, pro které chcete zakázat automatické zřizování, klikněte na **Upravit nastavení.**
3. V **zásadách zabezpečení –** okno Shromažďování dat v části **Automatické zřizování** vyberte **Vypnuto**.
4. Vyberte **Uložit**.

   ![Zakázání automatického zřizování][6]

Pokud je automatické zřizování zakázáno (vypnuto), výchozí oddíl konfigurace pracovního prostoru se nezobrazí.

Pokud vypnete automatické poskytování poté, co bylo dříve zapnuto:
-   Agenti nebudou zřízeni na nových virtuálních počítačích.
-   Centrum zabezpečení zastaví shromažďování dat z výchozího pracovního prostoru.
 
> [!NOTE]
>  Zakázání automatické zřizování neodebere agenta analýzy protokolů z virtuálních stránek Azure, kde byl agent zřízen. Informace o odebrání rozšíření OMS naleznete v tématu [Jak odebrat rozšíření OMS nainstalovaná aplikací Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Ruční zřizování agentů<a name="manual-agent"></a>
 
Agenta analýzy protokolů lze nainstalovat ručně několika způsoby. Při ruční instalaci se ujistěte, že jste zakázali automatické zřizování.

### <a name="operations-management-suite-vm-extension-deployment"></a>Nasazení rozšíření rozšíření služby Operations Management Suite pro virtuální počítače 

Agenta analýzy protokolů můžete nainstalovat ručně, aby centrum zabezpečení shromažďovalo data zabezpečení z virtuálních počítačů a poskytovalo doporučení a výstrahy.
1. Vyberte Automatické zřizování – VYPNUTO.
2. Vytvořte pracovní prostor a nastavte cenovou úroveň pro pracovní prostor, který chcete nastavit agenta analýzy protokolů:

   a.  V hlavní nabídce Centra zabezpečení vyberte **zásady zabezpečení**.
     
   b.  Vyberte pracovní prostor, ve kterém chcete agenta připojit. Ujistěte se, že pracovní prostor je ve stejném předplatném, které používáte v Centru zabezpečení a že máte oprávnění ke čtení a zápisu v pracovním prostoru.
       ![Výběr pracovního prostoru][8]
3. Nastavte cenovou úroveň.
   ![Vybrat cenovou úroveň][9] 
   >[!NOTE]
   >Pokud je v pracovním prostoru již povoleno řešení **Zabezpečení** nebo **SecurityCenterFree,** ceny se nastaví automaticky. 
   > 

4. Pokud chcete nasadit agenty na nové virtuální počítače pomocí šablony Správce prostředků, nainstalujte rozšíření virtuálního počítače OMS:

   a.  [Instalace rozšíření virtuálního počítače OMS pro Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalace rozšíření virtuálního počítače OMS pro Linux](../virtual-machines/extensions/oms-linux.md)
5. Pokud chcete nasadit rozšíření na existující virtuální počítače, postupujte podle pokynů v části [Shromažďování dat o virtuálních počítačích Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > V části **Shromáždit data o událostech a výkonu** jsou volitelné.
   >
6. Chcete-li k nasazení rozšíření použít Prostředí PowerShell, použijte následující příklad Prostředí PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Přejděte na **Log Analytics** a klikněte na **Upřesnit nastavení**.
    
      ![Nastavení analýzy protokolů][11]

   2. Zkopírujte hodnoty z **id pracovního prostoru** a **primárního klíče**.
  
      ![Kopírování hodnot][12]

   3. Naplňte veřejnou konfiguraci a soukromou konfiguraci těmito hodnotami:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Při instalaci na virtuální počítač s Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Při instalaci na virtuální počítač s Linuxem:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Pokyny, jak napojit Centrum zabezpečení pomocí PowerShellu, najdete [v tématu Automatizace registrace Centra zabezpečení Azure pomocí PowerShellu](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Řešení potíží

-   Chcete-li zjistit problémy s automatickou instalací zřízení, přečtěte si informace [o stavu agenta monitorování](security-center-troubleshooting-guide.md#mon-agent).

-  Informace o identifikaci požadavků na síť monitorovacího agenta naleznete [v tématu Poradce při potížích se síťovými požadavky monitorovacího agenta](security-center-troubleshooting-guide.md#mon-network-req).
-   Informace o identifikaci problémů s ručním přihlašováním najdete v [tématu Řešení problémů s připojením sady Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Identifikace problémů s nemonitorovanými virtuálními počítači a počítači:

    Virtuální počítač nebo počítač není monitorován Security Center, pokud počítač není spuštěn a rozšíření agenta Log Analytics. V počítači může být místní agent již nainstalován, například přímý agent OMS nebo agent nástroje System Center Operations Manager. Počítače s těmito agenty jsou identifikovány jako nemonitorované, protože tito agenti nejsou plně podporováni v Centru zabezpečení. Chcete-li plně využít všech funkcí Security Center, je vyžadováno rozšíření agenta Log Analytics.

    Další informace o důvodech, proč Centrum zabezpečení nemůže úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování, naleznete [v tématu Sledování problémů se stavem agenta](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Další kroky
Tento článek ukazuje, jak funguje shromažďování dat a automatické zřizování v Centru zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

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
