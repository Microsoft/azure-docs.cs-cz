---
title: Vytváření vlastních zásad zabezpečení v Azure Security Center | Microsoft Docs
description: Definice vlastních zásad Azure monitorují Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 34dbace304ccf70891ef53dd768de60d87e26967
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666631"
---
# <a name="using-custom-security-policies-preview"></a>Použití vlastních zásad zabezpečení (Preview)

Kvůli lepšímu zabezpečení systémů a prostředí Azure Security Center vygeneruje doporučení zabezpečení. Tato doporučení jsou založená na osvědčených postupech, které jsou součástí obecných výchozích zásad zabezpečení dodaných všem zákazníkům. Můžou také pocházet ze zkušeností Security Center v oboru a zákonných standardů.

Pomocí této funkce ve verzi Preview můžete přidat *vlastní iniciativy.* Pak dostanete doporučení, pokud vaše prostředí nedodržuje zásady, které vytvoříte. Všechny vlastní podněty, které vytvoříte, se zobrazí společně s integrovanou iniciativou na řídicím panelu dodržování předpisů, který je popsaný v kurzu. [tím se vylepšit dodržování předpisů](security-center-compliance-dashboard.md).

Jak je [popsáno v](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) dokumentaci k Azure Policy, když zadáte umístění pro vlastní iniciativu, musí se jednat o skupinu pro správu nebo o předplatné. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Přidání vlastní iniciativy do předplatného 

1. Na bočním panelu Security Center otevřete stránku **zásady zabezpečení** .

1. Vyberte předplatné nebo skupinu pro správu, ke kterým chcete přidat vlastní podnět.

    [![výběru předplatného, pro které vytvoříte vlastní zásadu.](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Je nutné přidat vlastní standardy na úrovni předplatného (nebo vyšší), aby je bylo možné vyhodnotit a zobrazit v Security Center. 
    >
    > Když přidáte vlastní Standard, přiřadí se k tomuto oboru *iniciativa* . Proto doporučujeme vybrat nejširší rozsah vyžadovaný pro toto přiřazení.

1. Na stránce Zásady zabezpečení v části vaše vlastní iniciativy (Preview) klikněte na **Přidat vlastní iniciativu**.

    [![klikněte na * * přidat vlastní iniciativu * *.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Zobrazí se následující stránka:

    ![Vytvořit nebo přidat zásadu](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na stránce přidat vlastní podněty si Projděte seznam vlastních zásad, které jsou už ve vaší organizaci vytvořené. Pokud se vám zobrazí ten, který chcete přidružit k předplatnému, klikněte na **Přidat**. Pokud v seznamu, který vyhovuje vašim potřebám, není iniciativa, tento krok přeskočte.

1. Vytvoření nové vlastní iniciativy:

    1. Klikněte na **vytvořit nový**.
    1. Zadejte umístění a název definice.
    1. Vyberte zásady, které chcete zahrnout, a klikněte na **Přidat**.
    1. Zadejte požadované parametry.
    1. Klikněte na možnost **Uložit**.
    1. Na stránce přidat vlastní iniciativy klikněte na aktualizovat a vaše nová iniciativa se zobrazí jako dostupná.
    1. Klikněte na **Přidat** a přiřaďte ho k vašemu předplatnému.

    > [!NOTE]
    > Vytváření nových iniciativ vyžaduje přihlašovací údaje vlastníka předplatného. Další informace o rolích Azure najdete [v tématu oprávnění v Azure Security Center](security-center-permissions.md).

    Vaše nová iniciativa se projeví a dopad můžete zobrazit následujícími dvěma způsoby:

    * Na bočním panelu Security Center v části zásady & Kompatibilita vyberte **dodržování předpisů**. Otevře se řídicí panel dodržování předpisů, ve kterém se zobrazí vaše nová vlastní iniciativa spolu s integrovanými iniciativami.
    
    * Začnete dostávat doporučení, pokud vaše prostředí nedodržuje zásady, které jste definovali.

1. Pokud si chcete zobrazit výsledná doporučení pro vaši zásadu, otevřete stránku doporučení kliknutím na **doporučení** na postranním panelu. Doporučení se zobrazí s popiskem "vlastní" a budou k dispozici do přibližně jedné hodiny.

    [![vlastní doporučení](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit vlastní zásady zabezpečení. 

Další související materiály najdete v následujících článcích: 

- [Přehled zásad zabezpečení](tutorial-security-policy.md)
- [Seznam předdefinovaných zásad zabezpečení](security-center-policy-definitions.md)