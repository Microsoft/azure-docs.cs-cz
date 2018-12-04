---
title: Připojení ke službě Azure Security Center úrovně Standard pro zvýšení zabezpečení | Dokumentace Microsoftu
description: " Zjistěte, jak k připojení k Azure Security Center úrovně Standard pro rozšířené zabezpečení. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: rkarlin
ms.openlocfilehash: 2ea94e79fdcc331554f42e2396d954af7cb979d8
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850070"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Připojení ke službě Azure Security Center úrovně Standard pro zvýšení zabezpečení
Upgrade na Security Center úrovně Standard využívat správu rozšířené zabezpečení a ochrana před internetovými útoky pro vašimi hybridními cloudovými úlohami.  Můžete zkusit Standard zdarma. Zobrazit Security Center [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/) Další informace.

Security Center úrovně Standard zahrnuje:

- **Zabezpečení hybridní** – získejte jednotný přehled o zabezpečení ve všech místních i cloudových úloh. Aplikovat zásady zabezpečení a průběžně vyhodnocovat zabezpečení vašimi hybridními cloudovými úlohami pro zajištění dodržování bezpečnostních standardů. Shromažďujte, prohledávejte a analyzujte data o zabezpečení z nejrůznějších zdrojů, včetně bran firewall a dalších partnerských řešení.
- **Rozšířená detekce hrozeb** -pomocí pokročilých analýz a grafu inteligentního zabezpečení Microsoftu k získejte výhodu nad vyvíjejícími se kybernetickými útoky.  Využijte integrované analýzy chování a strojové učení k identifikaci útoků a zneužití nultého dne. Monitorujte v sítích, počítačích a cloudových službách příchozí útoky a aktivity po porušení zabezpečení. Zjednodušte vyšetřování pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
- **Řízení přístupu a aplikací** -Blokujte malware a další nechtěné aplikace použitím doporučení na seznam povolených upravených pro vaše konkrétní úlohy a využívajících strojové učení. Omezte plochu vystavenou síťovým útokům s just-in-time řízený přístup k portům pro správu na virtuálních počítačích Azure, výrazně tím omezit vystavení útokům hrubou silou a jiným síťovým útokům.

## <a name="detecting-unprotected-resources"></a>Zjišťování nechráněných prostředcích     
Security Center automaticky rozpozná veškerá předplatná a pracovní prostory Azure, které ještě nepřešly na úroveň Security Center Standard. To zahrnuje předplatná Azure využívající úroveň Security Center Free volné a pracovní prostory, které nemají povolené řešení zabezpečení.

Celý předplatného Azure můžete upgradovat na úroveň Standard, která dědí všechny prostředky v rámci předplatného, nebo můžete definovat jedinečná zásada Upgrade skupiny s konkrétní prostředek pouze. Pokud nastavení zásad skupiny prostředků jsou jedinečné, Security Center nebude po upgradu předplatné na úroveň Standard přepsat cenové zásady. Použití standardní úroveň na předplatné se vztahuje pouze na počítače v rámci předplatného, které generují sestavy do pracovních prostorů vytvořených službou Security Center. Použití standardní úroveň do pracovního prostoru se vztahuje na všechny prostředky vytváření sestav do pracovního prostoru.

> [!NOTE]
> Můžete chtít spravovat náklady a omezit objem dat shromážděných pro řešení podle omezení na konkrétní sadu agentů. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) vám umožní použít obor s řešení a cílit na podmnožině počítačů v pracovním prostoru.  Pokud používáte cílení na řešení, Security Center obsahuje pracovní prostor tak, že nemusí řešení.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Upgrade pracovního prostoru nebo předplatnému Azure
Chcete-li upgradovat předplatné nebo si pracovní prostor na úroveň Standard:
1. V hlavní nabídce služby Security Center vyberte **Začínáme**.
  ![Začínáme](./media/security-center-onboarding/get-started.png)
2. V části **Upgrade** zobrazí Security Center seznam předplatných a pracovních prostorů, ke kterým se můžete připojit. 
   - Po kliknutí na **Využít zkušební verzi** se zobrazí seznam všech předplatných a pracovních prostorů, které jsou ve stavu bezplatné zkušební verze.
   -    Předplatná a pracovní prostory, které si nemůžete zdarma vyzkoušet, můžete upgradovat.
   -    V bezplatné zkušební verzi můžete začít používat vybrané pracovní prostory a předplatná.
3.  Pokud chcete začít používat zkušební verzi vybraných předplatných, klikněte na **Zahájit zkušební období**.
  ![Vyberte předplatné](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > Funkce bezplatné služby Security Center se použijí pro virtuální počítače Azure pouze. Bezplatné možnosti nejsou použity pro vaše počítače umístěné mimo Azure. Pokud vyberete Standard, funkce edice Standard platí pro všechny virtuální počítače Azure a mimo Azure počítačích odesílajících sestavy do pracovního prostoru. Doporučujeme použít standardní pro pokročilé zabezpečení pro Azure a prostředků mimo Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Připojení počítače mimo Azure
Security Center může monitorovat stav zabezpečení počítačů mimo Azure, tyto prostředky je však nejprve potřeba připojit. Můžete přidat počítače umístěné mimo Azure z **Začínáme** okno nebo **Compute** okno. Projdeme obě metody.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Přidat počítače umístěné mimo Azure z **Začínáme**

1. Vraťte se na **Začínáme**.   
2. Vyberte kartu **Začínáme**.

  ![Mimo službu Azure](./media/security-center-onboarding/non-azure.png)

3. V části **Přidat počítače umístěné mimo Azure** klikněte na **Konfigurovat**. Zobrazí se seznam vašich pracovních prostorů Log Analytics. Seznam obsahuje i výchozí pracovní prostor, který pro vás vytvořila služba Security Center při povolení automatického zřizování, pokud existuje. Vyberte tento nebo jiný pracovní prostor, který chcete použít.

  ![Přidání počítače umístěného mimo Azure][7]

Pokud máte existující pracovní prostory, jsou uvedeny v části **přidat počítače umístěné mimo Azure**. Můžete přidat počítače do existujícího pracovního prostoru nebo vytvořte nový pracovní prostor. Pokud chcete vytvořit nový pracovní prostor, vyberte odkaz **přidat nový pracovní prostor**.

### <a name="add-new-non-azure-computers-from-compute"></a>Přidat počítače umístěné mimo Azure z **Compute**

**Vytvořte nový pracovní prostor a přidejte počítače**

1. V části **přidat počítače umístěné mimo Azure**vyberte **přidat nový pracovní prostor**.

   ![Přidat nový pracovní prostor][4]

2. V části **zabezpečení a Audit**vyberte **pracovní prostor OMS** vytvořte nový pracovní prostor.
> [!NOTE]
> Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.
3. V části **pracovní prostor OMS**, zadejte informace pro váš pracovní prostor.
4. V části **pracovní prostor OMS**vyberte **OK**.  Když vyberete OK, se zobrazí odkaz pro stažení agenta Windows nebo Linuxem a klíči pro ID vašeho pracovního prostoru, které použijete při konfiguraci agenta.
5. V části **zabezpečení a Audit**vyberte **OK**.

**Vyberte existující pracovní prostor a přidat počítače**

Pomocí následujícího pracovního postupu můžete přidat počítač **připojování**, jak je znázorněno výše. Můžete také přidat počítač pomocí následujícího pracovního postupu z **Compute**. V tomto příkladu používáme **Compute**.

1. Vraťte se do hlavní nabídky služby Security Center a **přehled** řídicího panelu.

   ![Přehled][5]

2. Vyberte **výpočty a aplikace**.
3. V části **výpočty a aplikace**vyberte **přidat počítače**.

   ![Okno Compute][6]

4. V části **přidat počítače umístěné mimo Azure**, vyberte pracovní prostor a připojení počítače k klikněte na tlačítko **Add Computers**.

   ![Přidat počítače][7]

 **Přímý Agent** okno obsahuje odkaz ke stažení agenta Windows nebo Linuxem, jakož i ID pracovního prostoru a klíče, které použijete při konfiguraci agenta.   

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak připojit Azure a prostředků mimo Azure, aby bylo možné využívat pokročilé zabezpečení Security Center.  Další zprovozněné prostředky, najdete v tématu

- [Povolení shromažďování dat](security-center-enable-data-collection.md)
- [Sestava analýzy hrozeb](security-center-threat-report.md)
- [Právě přístupu k virtuálnímu počítači](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
