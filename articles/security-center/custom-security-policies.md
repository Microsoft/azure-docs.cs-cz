---
title: Vytvoření vlastních zásad zabezpečení v Azure Security Center | Dokumenty společnosti Microsoft
description: Definice vlastních zásad Azure monitorované službou Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258256"
---
# <a name="using-custom-security-policies"></a>Použití vlastních zásad zabezpečení

Azure Security Center generuje doporučení zabezpečení, které vám pomůže zabezpečit vaše systémy a prostředí. Tato doporučení jsou založena na osvědčených postupech v oboru, které jsou začleněny do obecných výchozích zásad zabezpečení dodávaných všem zákazníkům. Mohou také pocházet ze znalostí Security Center průmyslu a regulačních norem.

Pomocí této funkce můžete přidat *vlastní* iniciativy. Pokud vaše prostředí nedodržuje zásady, které vytvoříte, obdržíte doporučení. Všechny vlastní iniciativy, které vytvoříte, se zobrazí společně s předdefinovanými iniciativami na řídicím panelu dodržování předpisů popsaném v kurzu [Vylepšete dodržování předpisů](security-center-compliance-dashboard.md).

Jak je popsáno [zde](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) v dokumentaci zásad Azure, když zadáte umístění pro vlastní iniciativu, musí to být skupina pro správu nebo předplatné. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Přidání vlastní iniciativy do předplatného 

1. Na postranním panelu centra zabezpečení otevřete stránku **zásad zabezpečení.**

1. Vyberte předplatné nebo skupinu pro správu, do které chcete přidat vlastní iniciativu.

    [![Výběr předplatného, pro které vytvoříte vlastní zásady](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Chcete-li je vyhodnotit a zobrazit v Centru zabezpečení, je nutné přidat vlastní standardy na úrovni předplatného (nebo vyšší). 
    >
    > Když přidáte vlastní standard, přiřadí *iniciativu* k tomuto oboru. Proto doporučujeme vybrat nejširší obor požadovaný pro toto přiřazení.

1. Na stránce Zásady zabezpečení klikněte v části Vaše vlastní iniciativy na **Přidat vlastní iniciativu**.

    [![Klikněte na **Přidat vlastní iniciativu**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Zobrazí se následující stránka:

    ![Vytvoření nebo přidání zásady](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na stránce Přidat vlastní iniciativy zkontrolujte seznam vlastních zásad, které již byly ve vaší organizaci vytvořeny. Pokud vidíte jedno, které chcete přiřadit k předplatnému, klikněte na **Přidat**. Pokud v seznamu není žádná iniciativa, která by splňovala vaše potřeby, tento krok přeskočte.

1. Vytvoření nové vlastní iniciativy:

    1. Klepněte na **tlačítko Vytvořit nový**.
    1. Zadejte umístění a název definice.
    1. Vyberte zásady, které chcete zahrnout, a klepněte na tlačítko **Přidat**.
    1. Zadejte všechny požadované parametry.
    1. Klikněte na **Uložit**.
    1. Na stránce Přidat vlastní iniciativy klikněte na Aktualizovat a nová iniciativa se zobrazí jako dostupná.
    1. Klikněte na **Přidat** a přiřaďte ho k předplatnému.

    > [!NOTE]
    > Vytváření nových iniciativ vyžaduje pověření vlastníka předplatného. Další informace o rolích Azure najdete [v tématu Oprávnění v Azure Security Center](security-center-permissions.md).

    Nová iniciativa se projeví a můžete vidět dopad následujícími dvěma způsoby:

    * Na postranním panelu Centra zabezpečení včásti Zásady & dodržování předpisů vyberte **vyberte dodržování předpisů**. Otevře se řídicí panel dodržování předpisů, který vám vedle integrovaných iniciativ ukáže vaši novou vlastní iniciativu.
    
    * Doporučení začnete dostávat, pokud vaše prostředí nedodržuje zásady, které jste definovali.

1. Pokud chcete zobrazit výsledná doporučení pro zásady, kliknutím na **Doporučení** z postranního panelu otevřete stránku doporučení. Doporučení se zobrazí s popiskem "Vlastní" a budou k dispozici přibližně do jedné hodiny.

    [![Vlastní doporučení](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vytvořit vlastní zásady zabezpečení. 

Další související materiály naleznete v následujících článcích: 

- [Přehled zásad zabezpečení](tutorial-security-policy.md)
- [Seznam předdefinovaných zásad zabezpečení](security-center-policy-definitions.md)