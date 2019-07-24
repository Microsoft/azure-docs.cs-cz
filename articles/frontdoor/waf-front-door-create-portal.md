---
title: Vytvoření zásady firewallu webových aplikací pro přední dveře Azure pomocí Azure Portal
titlesuffix: Azure web application firewall
description: Naučte se vytvářet zásady firewallu webových aplikací (WAF) pomocí Azure Portal.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: abaef0fb521d848134885a06591b0656c60c67e6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846389"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Vytvoření zásady WAF pro přední dveře Azure pomocí Azure Portal

Tento článek popisuje, jak vytvořit základní zásadu firewallu webových aplikací Azure (WAF) a použít ji pro front-end hostitele v Azure front-endu.

## <a name="prerequisites"></a>Požadavky

Pomocí pokynů popsaných v [tématu rychlý Start vytvořte profil front-dveří: Vytvořte profil](quickstart-create-front-door.md)front-dveří. 

## <a name="create-a-waf-policy"></a>Vytvoření zásady WAF

Nejdřív vytvořte základní zásadu WAF se sadou spravovaných výchozích pravidel (DRS) pomocí portálu. 

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**> hledat **WAF**> vyberte **Firewall webových aplikací (Preview)** > vyberte **vytvořit**.
2. Na kartě **základy** na stránce **vytvořit zásadu WAF** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Subscription            |Vyberte název předplatného pro přední dveře.|
    | Resource group          |Vyberte název skupiny prostředků front dveří.|
    | Název zásady             |Zadejte jedinečný název pro zásady WAF.|

   ![Vytvoření zásady WAF](./media/waf-front-door-create-portal/basic.png)

3. Na kartě **přidružení** na stránce **vytvořit zásadu WAF** vyberte **Přidat hostitele front-end**, zadejte následující nastavení a pak vyberte **Přidat**:

    | Nastavení                 | Value                                              |
    | ---                     | ---                                                |
    | Přední dveře              | Vyberte název profilu front-dveří.|
    | Hostitel s front-endu           | Vyberte název hostitele front-dveří a pak vyberte **Přidat**.|
    
    > [!NOTE]
    > Pokud je hostitel se front-end přidružený k zásadám WAF, zobrazí se jako šedý. Nejdřív je potřeba odebrat hostitele front-end z přidružených zásad a pak znovu přidružit hostitele front-end k novým zásadám WAF.
1. Vyberte **zkontrolovat + vytvořit**a pak vyberte **vytvořit**.

## <a name="configure-waf-rules-optional"></a>Konfigurace pravidel WAF (volitelné)

### <a name="change-mode"></a>Režim změny

Když vytvoříte zásadu WAF, použije se výchozí zásada WAF v režimu **detekce** . V režimu **detekce** neblokuje WAF žádné požadavky, místo toho se protokolují požadavky, které odpovídají pravidlům WAF, do protokolů WAF.
Pokud chcete zobrazit WAF v akci, můžete změnit nastavení režimu z **detekce** na **prevence**. V režimu **prevence** se požadavky, které odpovídají pravidlům definovaným ve výchozí sadě pravidel (DRS), zablokují a přihlásily v protokolech WAF.

 ![Změnit režim zásad WAF](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Vlastní pravidla

Vlastní pravidlo můžete vytvořit tak, že v části **vlastní pravidla** vyberete **Přidat vlastní pravidlo** . Tím se spustí stránka Konfigurace vlastního pravidla. Níže je uveden příklad konfigurace vlastního pravidla pro blokování požadavku, pokud řetězec dotazu obsahuje **blockme**.

![Změnit režim zásad WAF](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Výchozí sada pravidel (DRS)

Výchozí sada pravidel spravovaná v Azure je ve výchozím nastavení povolená. Pokud chcete v rámci skupiny pravidel zakázat jednotlivá pravidla, rozbalte pravidla v této skupině pravidel, zaškrtněte políčko před  číslem pravidla a na kartě výše vyberte **Zakázat** . Chcete-li změnit typy akcí pro jednotlivá pravidla v rámci sady pravidel, zaškrtněte políčko před číslem pravidla a potom vyberte kartu **Akce změny** výše.

 ![Změnit sadu pravidel WAF](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [bráně firewall webových aplikací Azure](waf-overview.md).
- Přečtěte si další informace o [službě Azure front-dveří](front-door-overview.md).
