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
ms.openlocfilehash: d5432c794c26e350b23fe47aa1574422143ca4b2
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521953"
---
# <a name="using-custom-security-policies-preview"></a>Použití vlastních zásad zabezpečení (Preview)

Kvůli lepšímu zabezpečení systémů a prostředí Azure Security Center vygeneruje doporučení zabezpečení. Tato doporučení jsou založená na osvědčených postupech, které jsou součástí obecných výchozích zásad zabezpečení dodaných všem zákazníkům. Můžou také pocházet ze zkušeností Security Center v oboru a zákonných standardů.

Pomocí této funkce ve verzi Preview můžete přidat *vlastní iniciativy.* Pak se dostanou doporučení, pokud vaše počítače nevyhovují vytvořeným zásadám.

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Přidání vlastní iniciativy do předplatného 

1. Na bočním panelu Security Center otevřete stránku **zásady zabezpečení** .

1. Vyberte předplatné nebo skupinu pro správu, ke kterým chcete přidat vlastní podnět.

    [![výběru předplatného, pro které vytvoříte vlastní zásadu.](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
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
    1. Klikněte na **Uložit**.
    1. Na stránce přidat vlastní iniciativy klikněte na aktualizovat a vaše nová iniciativa se zobrazí jako dostupná.
    1. Klikněte na **Přidat** a přiřaďte ho k vašemu předplatnému.

    > [!NOTE]
    > Vytváření nových iniciativ vyžaduje přihlašovací údaje vlastníka předplatného. Další informace o rolích Azure najdete [v tématu oprávnění v Azure Security Center](security-center-permissions.md).

1. Pokud si chcete zobrazit výsledná doporučení pro vaši zásadu, otevřete stránku doporučení kliknutím na **doporučení** na postranním panelu. Doporučení se zobrazí s označením "vlastní" a bude k dispozici po dobu až 30 minut.

    [![vlastní doporučení](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit vlastní zásady zabezpečení. 

Další související materiály najdete v následujících článcích: 

- [Přehled zásad zabezpečení](tutorial-security-policy.md)
- [Seznam předdefinovaných zásad zabezpečení](security-center-policy-definitions.md)