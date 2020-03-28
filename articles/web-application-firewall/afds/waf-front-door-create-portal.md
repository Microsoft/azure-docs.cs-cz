---
title: 'Kurz: Vytvoření zásad WAF pro Azure Front Door – portál Azure'
description: V tomto kurzu se dozvíte, jak vytvořit zásadu brány firewall webových aplikací (WAF) pomocí portálu Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: be66a93ea4a518b26d973d222caf58e73b6986a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475837"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Kurz: Vytvoření zásady brány firewall webových aplikací na Azure Front Door pomocí portálu Azure

V tomto kurzu se můžete naučit vytvořit základní zásady brány Azure Web Application Firewall (WAF) a použít ji na front-endového hostitele na Azure Front Door.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásad waf
> * Přidružit k hostiteli front-endu
> * Konfigurace pravidel WAF

## <a name="prerequisites"></a>Požadavky

Vytvořte profil předních dveří podle pokynů popsaných v [úvodním panelu: Vytvoření profilu předních dveří](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Vytvoření zásady brány firewall webových aplikací

Nejprve vytvořte základní zásadu WAF se spravovanou výchozí sadou pravidel (DRS) pomocí portálu. 

1. V levé horní části obrazovky vyberte **Vytvořit prostředek**>hledání **waf**>vyberte **bránu firewall webové aplikace (Náhled)** > vyberte **Vytvořit**.
2. Na kartě **Základy** na stránce **Zásady Vytvoření waf** zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **Zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné            |Vyberte název předplatného front door.|
    | Skupina prostředků          |Vyberte název skupiny prostředků předních dveří.|
    | Název zásad             |Zadejte jedinečný název zásad WAF.|

   ![Vytvoření zásad waf](../media/waf-front-door-create-portal/basic.png)

3. Na kartě **Přidružení** na stránce **zásad Vytvořit waf** vyberte Přidat hostitele **front-endu**, zadejte následující nastavení a pak vyberte **Přidat**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Dveří              | Vyberte název profilu předních dveří.|
    | Front-end hostitele           | Vyberte název hostitele předních dveří a pak vyberte **Přidat**.|
    
    > [!NOTE]
    > Pokud je front-endový hostitel přidružen k zásadě WAF, zobrazí se jako šedě. Nejprve je nutné odebrat front-endového hostitele z přidružené zásady a potom znovu přidružit front-endového hostitele k nové zásadě WAF.
1. Vyberte **Zkontrolovat + vytvořit**, pak vyberte **Vytvořit**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurace pravidel brány firewall webových aplikací (volitelné)

### <a name="change-mode"></a>Změnit režim

Při vytváření zásad WAF je výchozí zásada WAF v režimu **zjišťování.** V režimu **zjišťování** WAF neblokuje žádné požadavky, místo toho jsou požadavky odpovídající pravidlům WAF zaznamenány v protokolech WAF.
Chcete-li zobrazit waf v akci, můžete změnit nastavení režimu z **Detekce** na **prevenci**. V režimu **prevence** jsou požadavky, které odpovídají pravidlům definovaným ve výchozí sadě pravidel (DRS), blokovány a protokolovány v protokolech WAF.

 ![Změna režimu zásad WAF](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Vlastní pravidla

Vlastní pravidlo můžete vytvořit tak, že v části **Vlastní pravidla** vyberete Přidat **vlastní pravidlo.** Tím se spustí stránka konfigurace vlastního pravidla. Níže je uveden příklad konfigurace vlastního pravidla pro blokování požadavku, pokud řetězec dotazu obsahuje **blockme**.

![Změna režimu zásad WAF](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Výchozí sada pravidel (DRS)

Výchozí sada pravidel spravovaná Azure je ve výchozím nastavení povolená. Chcete-li zakázat jednotlivé pravidlo v rámci skupiny pravidel, rozbalte pravidla v rámci této skupiny pravidel, zaškrtněte **políčko** před číslem pravidla a na kartě výše vyberte **Zakázat.** Chcete-li změnit typy akcí pro jednotlivá pravidla v sadě pravidel, zaškrtněte políčko před číslem pravidla a pak zaškrtněte kartu **Změnit akci** výše.

 ![Změnit sadu pravidel WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o azure web application firewall](../overview.md)
> [další informace o Azure Front Door](../../frontdoor/front-door-overview.md)