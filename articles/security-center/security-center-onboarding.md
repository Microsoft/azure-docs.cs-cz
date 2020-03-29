---
title: Vylepšené zabezpečení úrovně Standard – Azure Security Center
description: " Přečtěte si, jak se nalodit na Azure Security Center Standard pro lepší zabezpečení. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: be26a9d4c66412518079de303ac0764d979c3e7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912048"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Připojení ke službě Azure Security Center úrovně Standard pro zvýšení zabezpečení
Upgradujte na Security Center Standard, abyste využili výhod rozšířené správy zabezpečení a ochrany před hrozbami pro hybridní cloudové úlohy. Můžete zkusit Standard zdarma. Další informace naleznete na [stránce S cenách](https://azure.microsoft.com/pricing/details/security-center/) Centra zabezpečení.

Standardní úroveň Security Center zahrnuje:

- **Hybridní zabezpečení** – Získejte jednotný přehled o zabezpečení napříč všemi místními a cloudovými úlohami. Používejte zásady zabezpečení a průběžně vyhodnocujte zabezpečení hybridních cloudových úloh, abyste zajistili dodržování bezpečnostních standardů. Shromažďujte, vyhledávejte a analyzujte data zabezpečení z různých zdrojů, včetně bran firewall a dalších partnerských řešení.
- **Výstrahy zabezpečení** – Pomocí pokročilých analýz a microsoftintelligent security graph získáte výhodu nad vyvíjejícími se kybernetickými útoky. Využijte integrovanou analýzu chování a strojové učení k identifikaci útoků a zero-day exploitů. Monitorujte sítě, počítače a cloudové služby pro příchozí útoky a aktivity po porušení zabezpečení. Zjednodušte vyšetřování pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
- **Ovládání přístupu a aplikací** – Zablokujte malware a další nežádoucí aplikace použitím doporučení pro zařazení na seznam povolených, která jsou přizpůsobena vašim konkrétním pracovním zátěžím a poháněná strojovým učením. Snižte povrch síťového útoku pomocí řízeného přístupu k portům pro správu na virtuálních počítačích Azure, který umožňuje just-in-time, výrazně snížit vystavení hrubé síle a dalším síťovým útokům.

## <a name="detecting-unprotected-resources"></a>Zjišťování nechráněných prostředků
Security Center automaticky rozpozná veškerá předplatná a pracovní prostory Azure, které ještě nepřešly na úroveň Security Center Standard. To zahrnuje předplatná Azure využívající úroveň Security Center Free volné a pracovní prostory, které nemají povolené řešení zabezpečení.

Můžete upgradovat celé předplatné Azure na úroveň Standard, která je zděděna všemi podporovanými prostředky v rámci předplatného. Použití úrovně Standard na pracovní prostor se vztahuje na všechny prostředky, které se hlásí do pracovního prostoru.

> [!NOTE]
> Můžete chtít spravovat náklady a omezit množství dat shromážděných pro řešení tím, že je omezíte na určitou sadu agentů. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) umožňuje použít obor na řešení a cílit na podmnožinu počítačů v pracovním prostoru. Pokud používáte cílení na řešení, Centrum zabezpečení uvádí, že pracovní prostor nemá řešení.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Upgrade předplatného nebo pracovního prostoru Azure
Upgrade předplatného nebo pracovního prostoru na standardní:
1. V hlavní nabídce služby Security Center vyberte **Začínáme**.
  ![Začínáme](./media/security-center-onboarding/get-started.png)
2. V části **Upgrade** zobrazí Security Center seznam předplatných a pracovních prostorů, ke kterým se můžete připojit. 
   - Po kliknutí na **Využít zkušební verzi** se zobrazí seznam všech předplatných a pracovních prostorů, které jsou ve stavu bezplatné zkušební verze.
   -    Předplatná a pracovní prostory, které si nemůžete zdarma vyzkoušet, můžete upgradovat.
   -    V bezplatné zkušební verzi můžete začít používat vybrané pracovní prostory a předplatná.
3.  Pokud chcete začít používat zkušební verzi vybraných předplatných, klikněte na **Zahájit zkušební období**.
  ![Vybrat předplatné](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > Funkce Security Center zdarma se použijí pouze na virtuální počítače Azure a v mss. Funkce Free se nepoužijí na vaše počítače mimo Azure. Pokud vyberete Standardní, možnosti Standard se použijí na všechny virtuální počítače Azure, škálovací sady virtuálních počítačů a počítače, které nejsou azure, které se hlásí do pracovního prostoru. Doporučujeme použít standard k zajištění rozšířeného zabezpečení pro vaše prostředky Azure i jiné prostředky než Azure.
   >

## <a name="onboard-non-azure-computers"></a>Palubní počítače, které nejsou v Azure
Security Center může monitorovat stav zabezpečení počítačů mimo Azure, tyto prostředky je však nejprve potřeba připojit. Můžete přidat počítače, které nejsou Azure z okna **Začínáme** nebo z okna **Compute.** Projdeme si obě metody.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Přidání nových počítačů, které nejsou azure, ze **služby Začínáme**

1. Vraťte se k **souboru Začínáme**.
2. Vyberte kartu **Začínáme**.

   ![Mimo Azure](./media/security-center-onboarding/non-azure.png)

3. V části **Přidat počítače umístěné mimo Azure** klikněte na **Konfigurovat**. Zobrazí se seznam vašich pracovních prostorů Log Analytics. Seznam obsahuje i výchozí pracovní prostor, který pro vás vytvořila služba Security Center při povolení automatického zřizování, pokud existuje. Vyberte tento nebo jiný pracovní prostor, který chcete použít.

   ![Přidání počítače umístěného mimo Azure][7]

Pokud máte existující pracovní prostory, jsou uvedeny v části **Přidat nové počítače mimo Azure**. Počítače můžete přidat do existujícího pracovního prostoru nebo vytvořit nový pracovní prostor. Chcete-li vytvořit nový pracovní prostor, vyberte odkaz **přidat nový pracovní prostor**.

### <a name="add-new-non-azure-computers-from-compute"></a>Přidání nových počítačů, které nejsou azure, z **výpočetních prostředků**

**Vytvoření nového pracovního prostoru a přidání počítače**

1. V části **Přidat nové počítače, které nejsou azure**, vyberte přidat nový pracovní **prostor**.

   ![Přidání nového pracovního prostoru][4]

2. V části **Zabezpečení a auditování**vyberte **pracovní prostor OMS** a vytvořte nový pracovní prostor.
   > [!NOTE]
   > Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.
3. V části **Pracovní prostor OMS**zadejte informace o pracovním prostoru.
4. V části **Pracovní prostor OMS**vyberte **OK**. Po výběru ok získáte odkaz pro stažení agenta systému Windows nebo Linux a klíče pro ID pracovního prostoru, které chcete použít při konfiguraci agenta.
5. V části **Zabezpečení a audit**vyberte možnost **OK**.

**Výběr existujícího pracovního prostoru a přidání počítače**

Počítač můžete přidat podle následujícího pracovního postupu z **přiřazování**, jak je znázorněno výše. Počítač můžete přidat také podle pracovního postupu z **výpočetní ho dispozice**. V tomto příkladu používáme **Compute**.

1. Vraťte se do hlavní nabídky Centra zabezpečení a **panelu Přehled.**

   ![Přehled][5]

2. Vyberte **Možnost Vypočítat & aplikace**.
3. V části **Compute & apps**vyberte Přidat **počítače**.

   ![Okno Compute][6]

4. V části **Přidat nové počítače, které nejsou azure**, vyberte pracovní prostor, ke kterému chcete počítač připojit, a klepněte na tlačítko Přidat **počítače**.

   ![Přidání počítačů][7]

   Okno **Direct Agent** obsahuje odkaz pro stažení agenta systému Windows nebo Linux, stejně jako ID pracovního prostoru a klíče, které mají být používány při konfiguraci agenta.

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak na palubě prostředků Azure a non-Azure, aby bylo možné využívat pokročilé zabezpečení Centra zabezpečení. Další informace o vašich palubních zdrojích najdete v tématu

- [Povolení shromažďování dat](security-center-enable-data-collection.md)
- [Sestava analýzy hrozeb](security-center-threat-report.md)
- [Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
