---
title: 'Kurz: Vytvoření zásad WAF pro front-in Azure – Azure Portal'
description: V tomto kurzu se naučíte vytvořit zásadu firewallu webových aplikací (WAF) pomocí Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122237"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Kurz: Vytvoření zásad brány firewall webových aplikací na frontách Azure pomocí Azure Portal

V tomto kurzu se dozvíte, jak vytvořit základní zásadu firewallu webových aplikací Azure (WAF) a použít ji pro front-end hostitele v Azure front-endu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásady WAF
> * Přidružit k hostiteli front-endu
> * Konfigurace pravidel WAF

## <a name="prerequisites"></a>Požadavky

Vytvořte si [přední dveře](../../frontdoor/quickstart-create-front-door.md) nebo profil [front-Premium Standard/Premium](../../frontdoor/standard-premium/create-front-door-portal.md) . 

## <a name="create-a-web-application-firewall-policy"></a>Vytvoření zásady firewallu webových aplikací

Nejdřív vytvořte základní zásadu WAF se sadou spravovaných výchozích pravidel (DRS) pomocí portálu. 

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > hledat **WAF** > vyberte **Firewall webových aplikací (WAF)** > vyberte **vytvořit**.

1. Na kartě **základy** na stránce **vytvořit zásadu WAF** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Zásady pro              | Vyberte **globální WAF (přední dveře)**. |
    | SKU front dveří          | Vyberte z SKU Basic, Standard a Premium. |
    | Předplatné            | Vyberte název předplatného pro přední dveře.|
    | Skupina prostředků          | Vyberte název skupiny prostředků front dveří.|
    | Název zásad             | Zadejte jedinečný název pro zásady WAF.|
    | Stav zásad            | Nastaví se jako **povolené**. | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Snímek stránky s vytvořením zásady s použitím tlačítka pro kontrolu a vytvoření a seznam pro předplatné, skupinu prostředků a název zásady.":::

1. Na kartě **přidružení** na stránce **vytvořit zásadu WAF** vyberte **+ přidružit profil front-dveří**, zadejte následující nastavení a pak vyberte **Přidat**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Profil front-dveří              | Vyberte název profilu front-dveří. |
    | Domény          | Vyberte domény, ke kterým chcete zásady WAF přidružit, a pak vyberte **Přidat**. |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="Snímek obrazovky s profilem stránky přidružit profil přední dveře":::
    
    > [!NOTE]
    > Pokud je doména přidružená k zásadě WAF, zobrazí se jako šedá. Nejdřív je potřeba odebrat doménu z přidružených zásad a pak znovu přidružit doménu k nové zásadě WAF.

1. Vyberte **Zkontrolovat a vytvořit** a pak **Vytvořit**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurace pravidel firewallu webových aplikací (volitelné)

### <a name="change-mode"></a>Režim změny

Když vytvoříte zásadu WAF, použije se výchozí zásada WAF v režimu **detekce** . V režimu **detekce** neblokuje WAF žádné požadavky, místo toho se protokolují požadavky, které odpovídají pravidlům WAF, do protokolů WAF.
Pokud chcete zobrazit WAF v akci, můžete změnit nastavení režimu z **detekce** na **prevence**. V režimu **prevence** se požadavky, které odpovídají pravidlům definovaným ve výchozí sadě pravidel (DRS), zablokují a přihlásily v protokolech WAF.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Snímek obrazovky oddílu nastavení zásad Přepínač Mode je nastaven na hodnotu prevence.":::

### <a name="custom-rules"></a>Vlastní pravidla

Vlastní pravidlo můžete vytvořit tak, že v části **vlastní pravidla** vyberete **Přidat vlastní pravidlo** . Tím se spustí stránka Konfigurace vlastního pravidla. 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="Snímek obrazovky se stránkou vlastních pravidel":::

Níže je uveden příklad konfigurace vlastního pravidla pro blokování požadavku, pokud řetězec dotazu obsahuje **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Snímek obrazovky se stránkou konfigurace vlastního pravidla zobrazující nastavení pravidla, které kontroluje, zda proměnná řetězce dotazu obsahuje hodnotu blockme.":::

### <a name="default-rule-set-drs"></a>Výchozí sada pravidel (DRS)

Výchozí sada pravidel spravovaná v Azure je ve výchozím nastavení povolená. Aktuální výchozí verze je DefaultRuleSet_1.0. V rozevíracím seznamu jsou k dispozici v rozevíracím seznamu WAF **spravovaná pravidla** **a v** nedávné době k dispozici RuleSet Microsoft_DefaultRuleSet_1 1.

Pokud chcete zakázat individuální pravidlo, **zaškrtněte políčko před** číslem pravidla a v horní části stránky vyberte **Zakázat** . Chcete-li změnit typy akcí pro jednotlivá pravidla v rámci sady pravidel, zaškrtněte políčko před číslem pravidla a potom vyberte **akci změny** v horní části stránky.

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="Snímek obrazovky se stránkou spravovaná pravidla, která zobrazuje sadu pravidel, skupiny pravidel, pravidla a tlačítka akcí povolit, zakázat a změnit." lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny související prostředky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o službě Azure front-dveří](../../frontdoor/front-door-overview.md) 
>  [Přečtěte si další informace o službě Azure front dveří Standard/Premium](../../frontdoor/standard-premium/overview.md) .
