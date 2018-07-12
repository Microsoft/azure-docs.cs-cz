---
title: Připojení ke službě Azure Security Center úrovně Standard pro zvýšení zabezpečení | Dokumentace Microsoftu
description: " Zjistěte, jak k připojení k Azure Security Center úrovně Standard pro rozšířené zabezpečení. "
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
ms.date: 07/10/2018
ms.author: terrylan
ms.openlocfilehash: a68c0ecc15f81efe6045f4c298b48f9809916297
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38479457"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Připojení ke službě Azure Security Center úrovně Standard pro zvýšení zabezpečení
Upgrade na Security Center úrovně Standard využívat správu rozšířené zabezpečení a ochrana před internetovými útoky pro vašimi hybridními cloudovými úlohami.  Můžete vyzkoušet zdarma po dobu 60 dnů Standard. Zobrazit Security Center [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/) Další informace.

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

## <a name="upgrade-an-azure-subscription"></a>Upgradovat předplatné Azure
Všechna předplatná upgradu na úroveň Standard:
1. V hlavní nabídce služby Security Center vyberte **Připojení k rozšířenému zabezpečení**.
2. V části **připojení k rozšířenému zabezpečení**, Security Center seznam předplatných, které jsou způsobilé pro registraci. Můžete upgradovat všechny z uvedených předplatných tak, že vyberete **použít standardní plán**.

  ![Upgrade všech předplatných][1]

Upgrade samostatného předplatného na úroveň Standard: můžete upgradovat předplatné z **připojování** tak, že vyberete **použít úroveň Standard**. Skupinu prostředků v rámci předplatného upgradu na úroveň Standard, vyberte předplatné:
1. Vyberte předplatné.  **Zásady zabezpečení** poskytuje informace o skupině prostředků obsažených v rámci předplatného.
2. Vyberte předplatné nebo skupinu prostředků.

  ![Upgrade všech předplatných][2]

3. Vyberte **standardní** upgrade z edice Free na Standard.
4. Vyberte **Uložit**.

> [!NOTE]
> Upgradujte předplatné na úroveň Standard se zapnou [automatické zřizování](security-center-enable-data-collection.md) Pokud byla zakázaná. Doporučujeme, abyste automatické zřizování agentů monitorování.
>
>

## <a name="upgrade-a-workspace"></a>Upgrade pracovního prostoru
Použití Standard do pracovního prostoru se vztahuje na všechny prostředky vytváření sestav do pracovního prostoru.

1. Vraťte se **připojování** okno.
2. Vyberte pracovní prostor.

  ![Upgrade pracovního prostoru][8]

3. Vyberte **standardní** k upgradu.  
4. Vyberte **Uložit**.

   > [!NOTE]
   > Je scénář, ve kterém nemáte Free nebo Standard použitý pro váš pracovní prostor. Pokud vyberete Free, se použijí bezplatné funkce služby Security Center pro virtuální počítače Azure pouze. Bezplatné možnosti nejsou použity pro vaše počítače umístěné mimo Azure. Pokud vyberete Standard, funkce edice Standard platí pro všechny virtuální počítače Azure a mimo Azure počítačích odesílajících sestavy do pracovního prostoru. Doporučujeme použít standardní pro pokročilé zabezpečení pro Azure a prostředků mimo Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Připojení počítače mimo Azure
Security Center může monitorovat stav zabezpečení počítačů mimo Azure, tyto prostředky je však nejprve potřeba připojit. Můžete přidat počítače umístěné mimo Azure z **připojování** okno nebo **Compute** okno. Projdeme obě metody.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Přidat počítače umístěné mimo Azure z registrace

1. Vraťte se na **připojování**.   
2. Vyberte **chcete přidat počítače umístěné mimo Azure**.

  ![Přidání počítače umístěného mimo Azure][3]

Pokud máte existující pracovní prostory, jsou uvedeny v části **přidat počítače umístěné mimo Azure**. Můžete přidat počítače do existujícího pracovního prostoru nebo vytvořte nový pracovní prostor. Pokud chcete vytvořit nový pracovní prostor, vyberte odkaz **přidat nový pracovní prostor**.

Projdeme obě metody:

- Vytvořte nový pracovní prostor a přidejte počítače
- Vyberte existující pracovní prostor a přidat počítače

**Vytvořte nový pracovní prostor a přidejte počítače**

1. V části **přidat počítače umístěné mimo Azure**vyberte **přidat nový pracovní prostor**.

   ![Přidat nový pracovní prostor][4]

2. V části **zabezpečení a Audit**vyberte **pracovní prostor OMS** vytvořte nový pracovní prostor.
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

 **Přímý Agent** okno obsahuje odkaz ke stažení agenta Windows nebo Linuxem a klíči pro ID vašeho pracovního prostoru, které použijete při konfiguraci agenta.   

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak připojit Azure a prostředků mimo Azure, aby bylo možné využívat pokročilé zabezpečení Security Center.  Další zprovozněné prostředky, najdete v tématu

- [Povolení shromažďování dat](security-center-enable-data-collection.md)
- [Sestava analýzy hrozeb](security-center-threat-report.md)
- [Právě přístupu k virtuálnímu počítači](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
