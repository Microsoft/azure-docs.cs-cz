---
title: 'Kurz: Vytvoření zásad WAF pro Azure CDN – portál Azure'
description: V tomto kurzu se dozvíte, jak vytvořit zásadu brány firewall webových aplikací (WAF) na Azure CDN pomocí portálu Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485591"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Kurz: Vytvoření zásadwaf na Azure CDN pomocí portálu Azure

Tento kurz ukazuje, jak vytvořit základní zásady azure web application firewall (WAF) a použít ji na koncový bod v síti pro doručování obsahu Azure (CDN).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásad waf
> * Přidružit ke koncovému bodu CDN
> * Konfigurace pravidel WAF

## <a name="prerequisites"></a>Požadavky

Vytvořte profil a koncový bod Azure CDN podle pokynů v [úvodním panelu: Vytvoření profilu a koncového bodu Azure CDN](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Vytvoření zásady brány firewall webových aplikací

Nejprve vytvořte základní zásadu WAF se spravovanou výchozí sadou pravidel (DRS) pomocí portálu.

1. V levém horním rohu obrazovky vyberte **Vytvořit zdroj**>hledání **waf**>vyberte **bránu firewall webové aplikace** > vyberte **Vytvořit**.
2. Na kartě **Základy** na stránce **Zásady Vytvoření waf** zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **Zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Politika pro            |Vyberte Azure CDN (preview).|
    | Předplatné            |Vyberte název předplatného front door.|
    | Skupina prostředků          |Vyberte název skupiny prostředků předních dveří.|
    | Název zásad             |Zadejte jedinečný název zásad WAF.|

   ![Vytvoření zásad waf](../media/waf-cdn-create-portal/basic.png)

3. Na kartě **Přidružení** na stránce **zásad Vytvořit waf** vyberte **Přidat koncový bod CDN**, zadejte následující nastavení a pak vyberte **Přidat**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Profil CDN              | Vyberte název profilu CDN.|
    | Koncový bod           | Vyberte název koncového bodu a pak vyberte **Přidat**.|
    
    > [!NOTE]
    > Pokud je koncový bod přidružen k zásadě WAF, zobrazí se šedě. Nejprve je nutné odebrat koncový bod z přidružené zásady a potom znovu přidružit koncový bod k nové zásadě WAF.
1. Vyberte **Zkontrolovat + vytvořit**, pak vyberte **Vytvořit**.

## <a name="configure-web-application-firewall-policy-optional"></a>Konfigurace zásad brány firewall webových aplikací (volitelné)

### <a name="change-mode"></a>Změnit režim

Ve výchozím nastavení je zásada WAF v režimu *zjišťování* při vytváření zásad WAF. V režimu *detekce* WAF neblokuje žádné požadavky. Místo toho jsou požadavky odpovídající pravidlům WAF zaznamenány v protokolech WAF.

Chcete-li zobrazit waf v akci, můžete změnit nastavení režimu z *Detekce* na *prevenci*. V režimu *prevence* jsou požadavky, které odpovídají pravidlům definovaným ve výchozí sadě pravidel (DRS), blokovány a protokolovány v protokolech WAF.

 ![Změna režimu zásad WAF](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Vlastní pravidla

Chcete-li vytvořit vlastní pravidlo, vyberte **přidat vlastní pravidlo** v části Vlastní **pravidla.** Otevře se stránka konfigurace vlastního pravidla. Existují dva typy vlastních pravidel: **pravidlo shody** a pravidlo **omezení rychlosti.**

Následující snímek obrazovky ukazuje vlastní pravidlo shody pro blokování požadavku, pokud řetězec dotazu obsahuje **blockme**hodnoty .

![Změna režimu zásad WAF](../media/waf-cdn-create-portal/custommatch.png)

Pravidla limitu rychlosti vyžadují dvě další pole: **doba trvání limitu rychlosti** a **prahová hodnota limitu rychlosti (požadavky),** jak je znázorněno v následujícím příkladu:

![Změna režimu zásad WAF](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Výchozí sada pravidel (DRS)

Výchozí sada pravidel spravované Azure je ve výchozím nastavení povolená. Chcete-li zakázat jednotlivé pravidlo v rámci skupiny pravidel, rozbalte pravidla v rámci této skupiny pravidel, zaškrtněte políčko před číslem pravidla a na kartě výše vyberte **Zakázat.** Chcete-li změnit typy akcí pro jednotlivá pravidla v sadě pravidel, zaškrtněte políčko před číslem pravidla a pak zaškrtněte kartu **Změnit akci** výše.

 ![Změnit sadu pravidel WAF](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o bráně firewall webových aplikací Azure](../overview.md)
