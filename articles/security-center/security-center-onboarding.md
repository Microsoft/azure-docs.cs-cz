---
title: Registrace k Azure Security Center Standard pro lepší zabezpečení | Microsoft Docs
description: " Zjistěte, jak chcete připojit k Azure Security Center Standard pro rozšířené zabezpečení. "
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
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: d83beecfc5a8f6b8a01c64e809bc84c6fd0238bf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31416485"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Registrace k Azure Security Center Standard pro rozšířené zabezpečení
Upgradujte Security Center standardní využívat výhod správy lepší zabezpečení a ochrana před internetovými útoky pro zatížení hybridní cloud.  Můžete zkusit Standard zdarma 60 dnů. Najdete v Centru zabezpečení [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/) Další informace.

Security Center Standard zahrnuje:

- **Hybridní zabezpečení** – získejte jednotný pohled na zabezpečení pro všechny místní a cloudové úlohy. Použijte zásady zabezpečení a průběžně vyhodnocení zabezpečení vašich hybridní cloudové úloh k zajištění dodržování standardů zabezpečení. Shromažďujte, prohledávejte a analyzujte data o zabezpečení z nejrůznějších zdrojů, včetně bran firewall a dalších partnerských řešení.
- **Rozšířené detekce hrozeb** -použití pokročilé analýzy a Microsoft Graph inteligentního zabezpečení získat okraj přes vyvíjející se internetovými útoky.  Využijte integrované analýzy chování a strojové učení k identifikaci útoků a zneužití nultého dne. Monitorujte v sítích, počítačích a cloudových službách příchozí útoky a aktivity po porušení zabezpečení. Zjednodušte vyšetřování pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
- **Ovládací prvky pro přístup a aplikace** -blokování malwaru a další nežádoucí aplikace s použitím povolených doporučení přizpůsobit pro konkrétní úlohy a používá technologii strojové učení. Snižte sítě prostor pro útoky s za běhu, řízené přístup k portům správy na virtuálních počítačích Azure, zásadně snižuje vystavení hrubou silou a další síťovým útokům.

## <a name="detecting-unprotected-resources"></a>Zjišťování nechráněné prostředky     
Security Center automaticky rozpozná veškerá předplatná a pracovní prostory Azure, které ještě nepřešly na úroveň Security Center Standard. To zahrnuje předplatná Azure využívající úroveň Security Center Free volné a pracovní prostory, které nemají povolené řešení zabezpečení.

Celý předplatného Azure můžete upgradovat na úroveň Standard, který zdědí všechny prostředky v rámci předplatného, nebo můžete definovat jedinečné zásady pro určité skupiny zdrojů pouze upgrade. Pokud jsou nastavení zásad skupiny prostředků jedinečný, Security Center nebude přepsat cenové zásady při upgradu odběru na úrovni Standard. Použití standardní vrstvy do předplatného se vztahuje pouze na virtuální počítače v rámci předplatného, které podávají zprávy pracovních prostorů vytvořit pomocí služby Security Center. Použití standardní vrstvy do pracovního prostoru se vztahují na všechny prostředky do pracovního prostoru generování sestav.

> [!NOTE]
> Můžete spravovat vaše náklady a omezit množství dat vybraných pro řešení omezením na konkrétní sadu agenty. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) vám umožní použít obor na řešení a cíle podmnožině počítačů v pracovním prostoru.  Pokud používáte cílení na řešení, Security Center uvádí prostoru nemá řešení.
>
>

## <a name="upgrade-an-azure-subscription"></a>Upgrade předplatné Azure
K upgradu všech odběrů Standard:
1. V části v hlavní nabídce Security Center, vyberte **registrace**.
2. V části **registrace k pokročilým zabezpečením**, Security Center uvádí odběry, které jsou způsobilé pro registraci. Můžete upgradovat všechny odběry v seznamu vyberete **použít standardní plán**.

  ![Upgradovat všech odběrů][1]

K upgradu předplatné jednotlivých Standard: můžete upgradovat předplatné z **registrace** výběrem **použít úroveň Standard**. Upgrade skupiny prostředků v rámci předplatného na Standard, vyberte předplatné:
1. Vyberte předplatné.  **Zásady zabezpečení** poskytuje informace o skupině prostředků obsažené v rámci předplatného.
2. Vyberte předplatné nebo skupinu prostředků.

  ![Upgradovat všech odběrů][2]

3. Vyberte **standardní** k upgradu volné na Standard.
4. Vyberte **Uložit**.

> [!NOTE]
> Upgrade předplatné na standardní bude zapnout [automatické zřizování](security-center-enable-data-collection.md) Pokud byla předtím zakázaná. Doporučujeme, abyste automatické zřizování monitorování agentů.
>
>

## <a name="upgrade-a-workspace"></a>Upgrade pracovního prostoru
Použití Standard do pracovního prostoru se vztahuje na všechny prostředky do pracovního prostoru generování sestav.

1. Vraťte se na **registrace** okno.
2. Vyberte pracovní prostor.

  ![Upgrade pracovního prostoru][8]

3. Vyberte **standardní** k upgradu.  
4. Vyberte **Uložit**.

   > [!NOTE]
   > Není scénář, kde nemusí mít volné nebo Standard použitá do pracovního prostoru. Pokud vyberete Free, bezplatné funkce služby Security Center platí pro virtuální počítače Azure jenom. Volné možnosti nejsou použity pro počítače mimo Azure. Pokud vyberete Standard, standardní možnosti platí pro všechny virtuální počítače Azure a vytváření sestav do pracovního prostoru počítače mimo Azure. Doporučujeme, abyste použili Standard pro rozšířené zabezpečení pro Azure a prostředky mimo Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Zařadit počítače mimo Azure
Security Center může monitorovat stav zabezpečení počítačů mimo Azure, tyto prostředky je však nejprve potřeba připojit. Můžete přidat počítače mimo Azure z **registrace** okno nebo z **výpočetní** okno. Projdeme obě metody.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Přidat nové počítače mimo Azure z registrace

1. Vraťte se do **registrace**.   
2. Vyberte **chcete přidat nové počítače mimo Azure**.

  ![Přidání počítače umístěného mimo Azure][3]

Pokud máte existující pracovní prostory, jsou uvedeny v části **přidat nové počítače bez Azure**. Můžete přidat k existujícímu pracovnímu prostoru počítače nebo vytvořit nový pracovní prostor. Pokud chcete vytvořit nový pracovní prostor, vyberte odkaz **přidat nový pracovní prostor**.

Projdeme obě metody:

- Vytvořte nový pracovní prostor a přidejte počítače
- Vyberte existující pracovní prostor a přidejte počítač

**Vytvořte nový pracovní prostor a přidejte počítače**

1. V části **přidat nové počítače mimo Azure**, vyberte **přidat nový pracovní prostor**.

   ![Přidat nový pracovní prostor][4]

2. V části **zabezpečení a Audit**, vyberte **pracovním prostorem OMS** vytvořit nový pracovní prostor.
3. V části **pracovním prostorem OMS**, zadejte informace pro vašeho pracovního prostoru.
4. V části **pracovním prostorem OMS**, vyberte **OK**.  Po výběru OK, zobrazí se odkaz pro stahování agenta systému Windows nebo Linux a klíče pro vaše ID pracovního prostoru pro použití v konfiguraci agenta.
5. V části **zabezpečení a Audit**, vyberte **OK**.

**Vyberte existující pracovní prostor a přidejte počítač**

Můžete přidat do počítače pomocí následujícího pracovního postupu z **registrace**, jak je uvedeno výše. Můžete také přidat do počítače pomocí následujícího pracovního postupu z **výpočetní**. V tomto příkladu používáme **výpočetní**.

1. Vrátit do hlavní nabídky Security Center a **přehled** řídicího panelu.

   ![Přehled][5]

2. Vyberte **výpočetní** dlaždici.
3. V části **výpočetní**, vyberte **přidat počítače**.

   ![Okno Compute][6]

4. V části **přidat nové počítače mimo Azure**, vyberte pracovní prostor počítače a klikněte na tlačítko **Add Computers**.

   ![Přidejte počítače][7]

 **Přímé agenta** obsahuje odkaz na stažení agenta systému Windows nebo Linux a klíče pro vaše ID pracovního prostoru pro použití v konfiguraci agenta.   

## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli, jak připojit Azure a prostředky mimo Azure, aby bylo možné využívat Security Center pokročilým zabezpečením.  Více s prostředkům zařazený nemá v tématu

- [Povolení shromažďování dat](security-center-enable-data-collection.md)
- [Sestava analýzy hrozeb](security-center-threat-report.md)
- [Přístup jenom na dobu virtuálních počítačů](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
