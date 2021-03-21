---
title: Připojení skupiny pro správu k Azure Security Center
description: Naučte se používat dodanou definici Azure Policy k povolení Azure Security Center pro všechna předplatná ve skupině pro správu.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 01036343e2585adf7c09ad3f0d236948a537fc29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016555"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Povolit Security Center u všech předplatných ve skupině pro správu

Pomocí Azure Policy můžete povolit Azure Security Center na všech předplatných Azure v rámci stejné skupiny pro správu (MG). To je pohodlnější než přístup k nim jednotlivě z portálu a funguje i v případě, že odběry patří různým vlastníkům. 

Připojení skupiny pro správu a všech jejích předplatných:

1. Jako uživatel s oprávněním **Správce zabezpečení** otevřete Azure Policy a vyhledejte definici **Enable Azure Security Center v rámci vašeho předplatného**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Definice Azure Policy povolit Azure Security Center ve vašem předplatném":::

1. Vyberte **přiřadit** a ujistěte se, že jste rozsah nastavili na úroveň mg.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="Přiřazení Azure Security Center povolení definice v předplatném":::

    > [!TIP]
    > Kromě oboru nejsou vyžadovány žádné parametry.

1. Vyberte **vytvořit úlohu nápravy** , abyste zajistili, že všechna existující předplatná, která nemají Security Center povolená, budou připojená.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Vytvoření úlohy nápravy pro povolení definice Azure Policy Azure Security Center ve vašem předplatném":::

1. Při přiřazení definice bude:

    1. Detekuje všechna předplatná v MG, která ještě nejsou zaregistrovaná v Security Center.
    1. Označte tato předplatná jako nedodržující předpisy.
    1. Označte "vyhovuje" všem registrovaným předplatným (bez ohledu na to, jestli mají v Azure Defender zapnutý nebo vypnutý).

    Úloha nápravy pak umožní Security Center zdarma, a to v nevyhovujících předplatných.

> [!IMPORTANT]
> Definice zásady umožní povolit Security Center jenom u **stávajících** předplatných. Pokud chcete zaregistrovat nově vytvořená předplatná, otevřete kartu dodržování předpisů, vyberte příslušné nekompatibilní odběry a vytvořte úlohu nápravy. Tento krok opakujte, pokud máte jedno nebo více nových předplatných, která chcete monitorovat pomocí Security Center.

## <a name="optional-modifications"></a>Volitelné změny

Existují různé způsoby, jak upravit definici Azure Policy: 

- **Definovat dodržování předpisů jinak** – Zadaná zásada klasifikuje všechna předplatná v mg, které ještě nejsou zaregistrované v Security Center jako "nekompatibilní". Můžete ho nastavit na všechna předplatná bez Azure Defenderu.

    Poskytnutá definice definuje *jedno* z následujících nastavení ceny jako vyhovující. To znamená, že předplatné nastavené na úroveň Standard nebo Free je kompatibilní.

    > [!TIP]
    > Když je plán Azure Defenderu povolený, popíše se v definici zásady, která je v nastavení standardní. Když je zakázaný, je to zadarmo. Další informace o rozdílech mezi těmito plány najdete v tématu [Security Center bezplatné vs Azure Defender](security-center-pricing.md). 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Pokud ho změníte na následující, budou klasifikovány jenom předplatná nastavená na standard, jako dodržující předpisy:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Definování některých plánů Azure Defenderu, které se použijí při povolování Security Center** – Zadaná zásada umožňuje Security Center bez jakéhokoli volitelného plánu Azure Defenderu. Můžete se rozhodnout povolit jednu nebo více z nich.

    Oddíl zadané definice `deployment` obsahuje parametr `pricingTier` . Ve výchozím nastavení je tato možnost nastavena na hodnotu `free` , ale lze ji změnit. 


## <a name="next-steps"></a>Další kroky:

Teď, když jste připojili celou skupinu pro správu, můžete povolit rozšířenou ochranu Azure Defenderu. 

> [!div class="nextstepaction"]
> [Povolení Azure Defenderu](enable-azure-defender.md)