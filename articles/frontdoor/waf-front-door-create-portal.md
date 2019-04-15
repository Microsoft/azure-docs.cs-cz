---
title: Vytvořit zásadu brány firewall webových aplikací pro Azure branou pomocí webu Azure portal
titlesuffix: Azure web application firewall
description: Zjistěte, jak vytvořit zásadu brány firewall (WAF) webové aplikace pomocí webu Azure portal.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/8/2019
ms.author: kumud;tyao
ms.openlocfilehash: 26db3a67c3efbd0ba2a5c58facd0c07175f7ed12
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564135"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Vytvořit zásadu WAF pro Azure branou pomocí webu Azure portal

Tento článek popisuje, jak vytvořit zásadu brány firewall (WAF) základní webové aplikace a použít ji pro front-endu hostitele v Azure branou.

## <a name="prerequisites"></a>Požadavky

Vytvoření profilu branou podle pokynů v tématu [rychlý start: Vytvoření profilu branou](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>Vytvořit zásadu WAF

Nejprve vytvořte zásadu základní WAF s spravované výchozí pravidlo nastavte (DRS) pomocí portálu. 

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**> vyhledejte **WAF**> vyberte **firewallu webových aplikací (Preview)** > vyberte  **Vytvoření**.
2. V **Základy** karty **vytvořit zásadu WAF** stránky, zadejte nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a pak vyberte **revize + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné            |Vyberte název vašeho odběru branou.|
    | Skupina prostředků          |Vyberte název skupiny prostředků branou.|
    | Název zásady             |Zadejte jedinečný název pro zásadu WAF.|

   ![Vytvořit zásadu WAF](./media/waf-front-door-create-portal/basic.png)

3. V **přidružení** karty **vytvořit zásadu WAF** stránce **přidat hostitele front-endu**, zadejte následující nastavení a pak vyberte **přidat**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Přední dveře              | Vyberte název svého profilu branou.|
    | Hostitel front-endu           | Vyberte název hostitele vašeho branou a potom vyberte **přidat**.|
    
    > [!NOTE]
    > Pokud je hostitel front-endu přidružit k zásadě WAF, je zobrazeno jako zobrazena šedě. Musíte nejprve odeberte hostitele front-endu z přidružených zásad a znovu přidružit hostitele front-endu do nové zásady WAF.
1. Vyberte **revize + vytvořit**a pak vyberte **vytvořit**.

## <a name="configure-waf-rules-optional"></a>Konfigurace pravidla firewallu webových aplikací (volitelné)

### <a name="change-mode"></a>Změnit režim

Při vytváření zásad WAF ve WAF výchozí zásady je **detekce** režimu. V **detekce** režimu, waf služby nebrání žádné požadavky, místo toho se protokolují požadavky odpovídající pravidla firewallu webových aplikací na protokolů WAF.
Chcete-li WAF se zobrazí v akci, můžete změnit nastavení režimu z **detekce** k **ochrany před únikem informací**. V **ochrany před únikem informací** režimu, požadavky, že shoda pravidla, které jsou definovány ve výchozí pravidlo nastavte (DRS) blokované a zaznamená při protokolů WAF.

 ![Režim zásad změn WAF](./media/waf-front-door-create-portal/policy.png)

### <a name="default-rule-set-drs"></a>Výchozí sada pravidel (DRS)

Ve výchozím nastavení zapnutá spravované v Azure nastavit výchozí pravidlo. Zakázat jednotlivá pravidla v rámci skupiny pravidel, rozbalte položku pravidla v rámci dané pravidlo skupiny, vyberte **zaškrtávací políčko** před číslo pravidla a vyberte **zakázat** na kartě výše. Chcete-li změnit typy akcí pro jednotlivá pravidla v pravidle, zaškrtněte políčko u číslo pravidla a pak vyberte **změňte akci** nahoře kartu.

 ![Změnit sadu pravidel WAF](./media/waf-front-door-create-portal/managed.png)

## <a name="next-steps"></a>Další postup

- Další informace o [firewallu webových aplikací](waf-overview.md).
- Další informace o [Azure branou](front-door-overview.md).




