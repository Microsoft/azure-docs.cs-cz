---
title: 'Kurz: Vytvoření zásad WAF pro front-in Azure – Azure Portal'
description: V tomto kurzu se naučíte vytvořit zásadu firewallu webových aplikací (WAF) pomocí Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: 7c7ea5297276ed9a1d1f2ca8f4190997dcab57c3
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602213"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Kurz: Vytvoření zásad brány firewall webových aplikací na frontách Azure pomocí Azure Portal

V tomto kurzu se dozvíte, jak vytvořit základní zásadu firewallu webových aplikací Azure (WAF) a použít ji pro front-end hostitele v Azure front-endu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásady WAF
> * Přidružit k hostiteli front-endu
> * Konfigurace pravidel WAF

## <a name="prerequisites"></a>Požadavky

Vytvořte profil front-dveří podle pokynů popsaných v tématu [rychlý Start: vytvoření profilu front-dveří](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Vytvoření zásady firewallu webových aplikací

Nejdřív vytvořte základní zásadu WAF se sadou spravovaných výchozích pravidel (DRS) pomocí portálu. 

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**>hledat **WAF**>vyberte **Firewall webových aplikací (Preview)** > vyberte **vytvořit**.
2. Na kartě **základy** na stránce **vytvořit zásadu WAF** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné            |Vyberte název předplatného pro přední dveře.|
    | Skupina prostředků          |Vyberte název skupiny prostředků front dveří.|
    | Název zásad             |Zadejte jedinečný název pro zásady WAF.|

   ![Vytvoření zásady WAF](../media/waf-front-door-create-portal/basic.png)

3. Na kartě **přidružení** na stránce **vytvořit zásadu WAF** vyberte **Přidat hostitele front-end**, zadejte následující nastavení a pak vyberte **Přidat**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Přední dveře              | Vyberte název profilu front-dveří.|
    | Hostitel s front-endu           | Vyberte název hostitele front-dveří a pak vyberte **Přidat**.|
    
    > [!NOTE]
    > Pokud je hostitel se front-end přidružený k zásadám WAF, zobrazí se jako šedý. Nejdřív je potřeba odebrat hostitele front-end z přidružených zásad a pak znovu přidružit hostitele front-end k novým zásadám WAF.
1. Vyberte **zkontrolovat + vytvořit**a pak vyberte **vytvořit**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurace pravidel firewallu webových aplikací (volitelné)

### <a name="change-mode"></a>Režim změny

Když vytvoříte zásadu WAF, použije se výchozí zásada WAF v režimu **detekce** . V režimu **detekce** neblokuje WAF žádné požadavky, místo toho se protokolují požadavky, které odpovídají pravidlům WAF, do protokolů WAF.
Pokud chcete zobrazit WAF v akci, můžete změnit nastavení režimu z **detekce** na **prevence**. V režimu **prevence** se požadavky, které odpovídají pravidlům definovaným ve výchozí sadě pravidel (DRS), zablokují a přihlásily v protokolech WAF.

 ![Změnit režim zásad WAF](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Vlastní pravidla

Vlastní pravidlo můžete vytvořit tak, že v části **vlastní pravidla** vyberete **Přidat vlastní pravidlo** . Tím se spustí stránka Konfigurace vlastního pravidla. Níže je uveden příklad konfigurace vlastního pravidla pro blokování požadavku, pokud řetězec dotazu obsahuje **blockme**.

![Vlastní pravidla](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Výchozí sada pravidel (DRS)

Výchozí sada pravidel spravovaná v Azure je ve výchozím nastavení povolená. Pokud chcete v rámci skupiny pravidel zakázat jednotlivá pravidla, rozbalte pravidla v této skupině pravidel, **zaškrtněte políčko před** číslem pravidla a na kartě výše vyberte **Zakázat** . Chcete-li změnit typy akcí pro jednotlivá pravidla v rámci sady pravidel, zaškrtněte políčko před číslem pravidla a potom vyberte kartu **Akce změny** výše.

 ![Změnit sadu pravidel WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny související prostředky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o službě Azure front-dveří](../../frontdoor/front-door-overview.md)