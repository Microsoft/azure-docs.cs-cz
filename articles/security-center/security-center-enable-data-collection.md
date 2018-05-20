---
title: Shromažďování dat v Azure Security Center | Microsoft Docs
description: " Informace o povolení shromažďování dat v Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: terrylan
ms.openlocfilehash: 847127c96f23bbeb3cf3a5d1c9768af6e0cc0dc4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="data-collection-in-azure-security-center"></a>Shromažďování dat v Azure Security Center
Security Center shromažďuje data z Azure virtuální počítače (VM) a počítače mimo Azure pro monitorování ohrožení zabezpečení a hrozbami. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru. Mezi příklady těchto údajů patří: typ a verze operačního systému, protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy, přihlášený uživatel a ID klienta. Microsoft Monitoring Agent taky zkopíruje soubory se stavem systému do pracovního prostoru.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Zapněte automatické zřizování služby Microsoft Monitoring Agent     
Automatické zřizování je ve výchozím nastavení vypnuté. Pokud je povoleno automatické zřizování, podporované Security Center zřizuje agenta Microsoft Monitoring Agent na všech virtuálních počítačích Azure a všechny nové, které jsou vytvořeny. Automatické zřizování důrazně doporučujeme, ale instalace ručního agenta je také k dispozici. [Naučte se nainstalovat rozšíření pro Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

> [!NOTE]
> Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky. Další informace najdete v tématu [vypnout automatické zřizování](security-center-enable-data-collection.md#disable-automatic-provisioning) v tomto článku. I když automatické zřizování je zakázán, jsou povoleny snímků disku virtuálního počítače a kolekce artefaktů.
>
>

Povolení automatického zřizování agenta Microsoft Monitoring Agent:
1. V části v hlavní nabídce Security Center, vyberte **zásady zabezpečení**.
2. Vyberte předplatné.

  ![Výběr předplatného][7]

3. V části **Zásady zabezpečení** vyberte **Shromažďování dat**.
4. V části **automatické zřizování**, vyberte **na** zapněte automatické zřizování.
5. Vyberte **Uložit**.

  ![Povolení automatického zřizování][1]

## <a name="default-workspace-configuration"></a>Výchozí konfigurace pracovního prostoru
Data shromážděná pomocí služby Security Center je uložen v pracovních prostorů analýzy protokolů.  Můžete vybrat, zda má data shromážděná z virtuálních počítačů Azure uložená v pracovních prostorů vytvořit pomocí služby Security Center nebo v existujícímu pracovnímu prostoru, kterou jste vytvořili.

Použít stávající pracovní prostor analýzy protokolů:
- V pracovním prostoru musí být přidružen vašem vybraném předplatném Azure.
- Minimálně musí mít čtení oprávnění pro přístup k pracovním prostoru.

Vyberte existující pracovní prostor analýzy protokolů:

1. V části **výchozí konfigurace prostoru**, vyberte **použijte jiný pracovní prostor**.

   ![Vyberte existující pracovní prostor][2]

2. V rozevírací nabídce vyberte pracovní prostor pro ukládání shromážděných dat.

  > [!NOTE]
  > V vyžádání nabídku jsou k dispozici všechny pracovní prostory ve všech vašich předplatných. V tématu [mezi výběr předplatného prostoru](security-center-enable-data-collection.md#cross-subscription-workspace-selection) Další informace.
  >
  >

3. Vyberte **Uložit**.
4. Po výběru **Uložit**, zobrazí se dotaz, pokud byste chtěli reconfigure monitorovat virtuální počítače.

   - Vyberte **ne** Pokud chcete, aby nová nastavení pracovního prostoru na pouze nové virtuální počítače používat. Nové nastavení pracovního prostoru se vztahují pouze na nové instalace agenta; nově zjištěných virtuálních počítačů, které nemají Microsoft Monitoring Agent nainstalována.
   - Vyberte **Ano** Pokud chcete, aby nová nastavení pracovního prostoru pro použití na všech virtuálních počítačích. Kromě toho každých virtuálních počítačů připojených k Security Center vytvořit pracovní prostor je znovu na nový cílový pracovní prostor.

   > [!NOTE]
   > Pokud vyberete Ano, odstraníte nesmí pracovních prostorů vytvářet pomocí služby Security Center, dokud se všechny virtuální počítače mají úspěšné automatické připojení k nové cílového pracovního prostoru. Tato operace selže, pokud je příliš brzké odstranit pracovní prostor.
   >
   >

   - Vyberte **zrušit** na tlačítko Storno.

     ![Vyberte existující pracovní prostor][3]

## <a name="cross-subscription-workspace-selection"></a>Mezi prostoru výběr předplatného
Když vyberete pracovní prostor pro ukládání dat, jsou k dispozici všechny pracovní prostory ve všech vašich předplatných. Mezi předplatnými výběr pracovního prostoru vám umožní shromažďovat data z virtuálních počítačů spuštěných v různých předplatných a uložit v pracovním prostoru podle svého výběru. Tato funkce funguje pro virtuální počítače se systémem Linux a Windows.

> [!NOTE]
> Výběr pracovního prostoru mezi předplatnými je součástí úroveň Free Azure Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>
>

## <a name="data-collection-tier"></a>Úrovně shromažďování dat
Security Center může snížit objem událostí při zachování dostatek události pro šetření, auditování a detekce hrozeb. Můžete shromáždit pomocí agenta právo filtrování zásady pro vaše předplatné a pracovní prostory ze čtyř sad události.

- **Všechny události** – pro zákazníky, kteří chtějí zajistěte, aby všechny události se shromažďují. Toto je výchozí hodnota.
- **Běžné** – to je sada událostí, které splňuje většina zákazníků a umožňuje jim úplné kontrolní záznam.
- **Minimální** – menší sadu událostí pro zákazníky, kteří chtějí minimalizovat objem událostí.
- **Žádný** – zakázat shromažďování událostí zabezpečení ze zabezpečení a protokoly AppLocker. Pro zákazníky, kteří tuto možnost zvolte řídicí panely jejich zabezpečení mají pouze proaktivní vyhodnocování jako antimalwarových, základní a aktualizace a protokoly brány Windows Firewall.

> [!NOTE]
> Tyto sady události zabezpečení jsou k dispozici pouze na úrovni Standard Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
Tyto sady byly navrženy pro adres typické scénáře. Zajistěte, aby k vyhodnocení, které z nich vyhovuje vašim potřebám před implementací.
>
>

K určení události, které bude patřit **běžné** a **minimální** sad událostí, jsme pracovali s zákazníků a oborových standardů, další informace o nefiltrované frekvenci jednotlivých událostí a jejich využití. V tomto procesu jsme použili následující pokyny:

- **Minimální** – Ujistěte se, že tato sada zahrnuje jenom události, které může znamenat úspěšné porušení zabezpečení a důležité události, které mají velmi nízké svazku. Například tato sada obsahuje úspěšná a neúspěšná přihlášení uživatele (událost ID 4624 4625), ale neobsahuje odhlášení, což je důležité pro auditování, ale nemá význam pro zjišťování a má poměrně velkým objemem. Většina datový svazek této sady je události přihlášení a proces vytvoření události (událost ID 4688).
- **Běžné** -poskytují záznam pro audit úplné uživatelské v této sadě. Tato sada obsahuje například uživatelské přihlášení a odhlášení uživatele (událost ID 4634). Zahrnuta auditování akce jako změny skupiny zabezpečení, operacích Kerberos řadiče domény klíče a dalších událostí, které doporučuje organizace odvětví.

Události, které mají velmi nízké svazku byly zahrnuté v běžné nastavit jako hlavní motivace zvolit si že ho přes všechny události je snížit objem a nechcete filtrovat konkrétní události.

Tady je úplnému selhání zabezpečení a AppLocker událost ID pro každou sadu:

| Datová vrstva | Indikátory událostí |
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
> Pokud používáte objekt zásad skupiny (GPO), doporučujeme, abyste povolili zásady auditu proces vytváření událostí 4688 a *CommandLine* pole v rámci události 4688. Další informace o procesu vytvoření události 4688 najdete v tématu Security Center [– nejčastější dotazy](security-center-faq.md#what-happens-when-data-collection-is-enabled). Další informace o těchto zásady auditu najdete v tématu [doporučení zásady auditu](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
>
>

Chcete-li zvolit filtrování zásady:
1. Na **zásadu zabezpečení shromažďování dat** okně vyberte zásady vaší filtrování v rámci **události zabezpečení**.
2. Vyberte **Uložit**.

   ![Zvolte filtrování zásad][5]

## <a name="disable-automatic-provisioning"></a>Vypnout automatické zřizování
Můžete zakázat automatické zřizování z prostředků kdykoli vypnutím tohoto nastavení v zásadě zabezpečení. Automatické zřizování důrazně doporučujeme, aby bylo možné získat výstrahy zabezpečení a doporučení ohledně aktualizací systému, ohrožení zabezpečení operačního systému a endpoint protection.

> [!NOTE]
> Vypnutím automatického zřizování neodeberete agenta Microsoft Monitoring Agent z virtuálních počítačů Azure, na kterých byl agent zřízen.
>
>

1. Vrátit do hlavní nabídky Security Center a vyberte zásady zabezpečení.
2. Vyberte předplatné, pro které chcete vypnout automatické zřizování.
3. Na **zásady zabezpečení – shromažďování dat** okno, v části **automatické zřizování** vyberte **vypnout**.
4. Vyberte **Uložit**.

  ![Zakázat automatické zřizování][6]

Pokud je automatické zřizování zakázáno (vypnuto), výchozí prostoru konfigurační oddíl se nezobrazí.

## <a name="next-steps"></a>Další postup
Tento článek ukázal, jak shromažďování dat a automatické zřizování v Centru zabezpečení funguje. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
- [Zabezpečení dat v Azure Security Center](security-center-data-security.md) -další způsob správy a zabezpečení ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
