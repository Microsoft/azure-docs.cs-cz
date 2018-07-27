---
title: Shromažďování dat ve službě Azure Security Center | Dokumentace Microsoftu
description: " Informace o povolení shromažďování dat ve službě Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2018
ms.author: rkarlin
ms.openlocfilehash: a5151d1f9498b29c79638445a58a8337abff8961
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281918"
---
# <a name="data-collection-in-azure-security-center"></a>Shromažďování dat ve službě Azure Security Center
Security Center shromažďuje data z Azure virtual machines (VM) a počítače mimo Azure monitorovat ohrožení zabezpečení a hrozby. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru. Mezi tato data patří: typ operačního systému a verze, protokoly operačního systému (protokoly událostí Windows), spuštěné procesy, název počítače, IP adresy, přihlášený uživatel, událostí nástroje AppLocker a ID tenanta. Microsoft Monitoring Agent také zkopíruje soubory se stavem systému do pracovního prostoru.

> [!NOTE]
> Chcete-li povolit shromažďování dat pro [adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center je nakonfiguruje místní zásady nástroje AppLocker v režimu auditování povolit všechny aplikace. To způsobí, že nástroj AppLocker generovat události, které jsou pak shromážděná a využít Security Center. Je důležité si uvědomit, že tyto zásady nenakonfigurují v počítačích, na kterých je už nakonfigurovaná zásada nástroje AppLocker. 
>

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Povolit automatické zřizování agenta Microsoft Monitoring Agent     
Automatické zřizování je ve výchozím nastavení vypnuté. Pokud je povoleno automatické zřizování, Security Center zřídí agenta Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a nových, které jsou vytvořeny. Automatické zřizování se důrazně doporučuje, ale ruční instalaci agentů je také k dispozici. [Zjistěte, jak nainstalovat rozšíření Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

> [!NOTE]
> - Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky. Další informace najdete v tématu [vypnout automatické zřizování](security-center-enable-data-collection.md#disable-automatic-provisioning) v tomto článku. Shromažďování artefaktů a snímků disku virtuálního počítače jsou povolené i v případě, že je zakázáno automatické zřizování.
>

Povolení automatického zřizování agenta Microsoft Monitoring Agent:
1. V hlavní nabídce služby Security Center, vyberte **zásady zabezpečení**.
2. Vyberte předplatné.

  ![Výběr předplatného][7]

3. V části **Zásady zabezpečení** vyberte **Shromažďování dat**.
4. V části **automatického zřizování**vyberte **na** chcete povolit automatické zřizování.
5. Vyberte **Uložit**.

  ![Povolení automatického zřizování][1]

## <a name="default-workspace-configuration"></a>Výchozí konfigurace pracovního prostoru
Data shromážděná službou Security Center se ukládají v pracovních prostorech Log Analytics.  Můžete se rozhodnout, jestli chcete data shromážděná z virtuálních počítačů Azure ukládat v pracovních prostorů vytvořených službou Security Center nebo existujícího pracovního prostoru, který jste vytvořili.

Použití existující pracovní prostor Log Analytics:
- Pracovní prostor musí být přidružen vašem vybraném předplatném Azure.
- Minimálně musí mít čtení oprávnění pro přístup k pracovnímu prostoru.

Vyberte existující pracovní prostor Log Analytics:

1. V části **výchozí konfigurace pracovního prostoru**vyberte **použít jiný pracovní prostor**.

   ![Vyberte existující pracovní prostor][2]

2. Z rozevírací nabídky vyberte pracovní prostor pro ukládání shromážděných dat.

  > [!NOTE]
  > V rozevírací nabídku o přijetí změn jsou k dispozici všechny pracovní prostory ve všech vašich předplatných. Zobrazit [napříč výběr pracovního prostoru předplatného](security-center-enable-data-collection.md#cross-subscription-workspace-selection) Další informace.
  >
  >

3. Vyberte **Uložit**.
4. Po výběru **Uložit**, zobrazí se výzva, pokud chcete změnit konfiguraci monitorovaných virtuálních počítačů.

   - Vyberte **ne** Pokud chcete nová nastavení pracovního prostoru pro použití na pouze nové virtuální počítače. Nový pracovní prostor nastavení platí pouze pro nové instalace agentů; nově zjištěných virtuálních počítačů, které nemají agenta Microsoft Monitoring Agent nainstalována.
   - Vyberte **Ano** Pokud chcete nová nastavení pracovního prostoru pro použití na všechny virtuální počítače. Kromě toho každý virtuální počítač připojený ke službě Security Center vytvořit pracovní prostor znovu nepřipojí do nového cílového pracovního prostoru.

   > [!NOTE]
   > Pokud vyberete Ano, nemůže odstranit pracovních prostorů vytvořených službou Security Center, dokud se všechny virtuální počítače mají úspěšné automatické připojení k novému pracovnímu prostoru cíl. Tato operace selže, pokud pracovní prostor je příliš brzké odstranit.
   >
   >

   - Vyberte **zrušit** na zrušení operace.

     ![Vyberte existující pracovní prostor][3]

## <a name="cross-subscription-workspace-selection"></a>Výběr pracovního prostoru předplatného pro různé
Když vyberete pracovní prostor pro ukládání vašich dat, jsou k dispozici všechny pracovní prostory ve všech vašich předplatných. Mezi předplatnými výběr pracovního prostoru vám umožní shromažďovat data z virtuálních počítačů spuštěných v různých předplatných a uloží je v pracovním prostoru podle vašeho výběru. Tato funkce funguje pro oba virtuální počítače se systémem Linux a Windows.

> [!NOTE]
> Výběr pracovního prostoru mezi předplatnými je součástí Azure Security Center úrovně Free. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>
>

## <a name="data-collection-tier"></a>Úrovně shromažďování dat
Security Center může snížit objem událostí při zachování dostatek události pro šetření, auditování a detekce hrozeb. Můžete použít právo filtrování zásady pro předplatná a pracovní prostory z čtyři sady události shromážděné agentem.

- **Všechny události** – pro zákazníky, kteří chtějí Ujistěte se, že všechny události se budou shromažďovat. Toto je výchozí.
- **Běžné** – to je sadu událostí, která splňuje většina zákazníků a umožňuje jim úplný záznam pro audit.
- **Minimální** – menší sadu protokolovaných událostí pro zákazníky, kteří chtějí minimalizovat objem událostí.
- **Žádný** – zakázat shromažďování událostí zabezpečení ze zabezpečení a protokolů AppLocker. Pro zákazníky, kteří tuto možnost zvolte jejich zabezpečení řídicích panelů mají pouze protokoly brány Windows Firewall a proaktivní posouzení, jako jsou antimalware, Směrný plán a aktualizace.

> [!NOTE]
> Tyto sady události zabezpečení jsou k dispozici pouze v Security Center úrovně Standard. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
Tyto sady byly navržených pro typické scénáře. Ujistěte se, že k vyhodnocení, která z nich nejlépe vyhovuje vašim potřebám před implementací.
>
>

K určení událostí, které bude patřit **běžné** a **minimální** události sady jsme ve spolupráci se zákazníky a oborové standardy, další informace o nefiltrované četnost každé události a jejich využití. V tomto procesu jsme použili následující pokyny:

- **Minimální** – Ujistěte se, že tato sada zahrnuje pouze události, které mohou uvádět úspěšné porušení zabezpečení a důležité události, které obsahují velmi malé množství. Například tato sada obsahuje úspěšné i neúspěšné přihlášení uživatele (událost ID 4624 4625), ale neobsahuje odhlášení, který je důležitý pro auditování, ale ne smysl pro zjišťování a má relativně velkým objemem. Většina objemu dat této sady se události přihlášení a proces vytvoření události (událost ID 4688).
- **Běžné** – zadejte úplné uživatelské záznam pro audit v této sadě. Tato sada obsahuje například uživatelských přihlášení a odhlášení uživatele (událost ID 4634). Zahrnujeme auditování akce, jako je změnami skupin zabezpečení, operace protokolu Kerberos řadič klíče domény a další události, které doporučuje organizace odvětví.

Události, které mají velmi malé množství byly součástí běžnou sadu jako hlavní motivace rozhodnout se, že všechny události přímo je snížit objem a nechcete vyfiltrovat konkrétní události.

Tady je úplný přehled zabezpečení a AppLocker event ID pro každou sadu:

| Datová vrstva | Shromažďovat události ukazatelů |
| --- | --- |
| Minimální | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Společné | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Pokud používáte objekt zásad skupiny (GPO), doporučujeme, abyste povolili zásady auditu 4688 události vytváření procesů a *CommandLine* pole v rámci události 4688. Další informace o procesu vytvoření události 4688 zobrazit Security Center [nejčastější dotazy k](security-center-faq.md#what-happens-when-data-collection-is-enabled). Další informace o těchto zásady auditu najdete v tématu [doporučení k zásadám auditu](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Chcete-li povolit shromažďování dat pro [adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center je nakonfiguruje místní zásady nástroje AppLocker v režimu auditování povolit všechny aplikace. To způsobí, že nástroj AppLocker generovat události, které jsou pak shromážděná a využít Security Center. Je důležité si uvědomit, že tyto zásady nenakonfigurují v počítačích, na kterých je už nakonfigurovaná zásada nástroje AppLocker. 
>

Chcete-li zvolit filtrování zásad:
1. Na **zásadu zabezpečení shromažďování dat** okně vyberte vaše zásady filtrování v rámci **události zabezpečení**.
2. Vyberte **Uložit**.

   ![Zvolte zásady filtrování][5]

## <a name="disable-automatic-provisioning"></a>Vypnout automatické zřizování
Můžete vypnout automatické zřizování z prostředků v každém okamžiku vypnutím toto nastavení v zásadách zabezpečení. Automatické zřizování se důrazně doporučujeme zajistí výstrahy zabezpečení a doporučení týkající se aktualizací systému, ohrožení zabezpečení operačního systému a endpoint protection.

> [!NOTE]
> Vypnutím automatického zřizování neodeberete agenta Microsoft Monitoring Agent z virtuálních počítačů Azure, na kterých byl agent zřízen.
>
>

1. Vraťte se do hlavní nabídky služby Security Center a vyberte zásady zabezpečení.
2. Vyberte předplatné, pro které chcete vypnout automatické zřizování.
3. Na **zásady zabezpečení – shromažďování dat** okně v části **automatického zřizování** vyberte **vypnout**.
4. Vyberte **Uložit**.

  ![Vypnout automatické zřizování][6]

Když automatické zřizování je zakázaný (vypnuto), konfigurační oddíl výchozí pracovní prostor se nezobrazí.

## <a name="next-steps"></a>Další postup
Tento článek vám ukázali, jak shromažďování dat a automatické zřizování v Security Center funguje. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
- [Zabezpečení dat ve službě Azure Security Center](security-center-data-security.md) – zjistěte, jak data správy a ochrany ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
