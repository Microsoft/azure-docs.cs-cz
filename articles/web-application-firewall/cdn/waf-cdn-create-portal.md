---
title: 'Kurz: Vytvoření zásad WAF pro Azure CDN-Azure Portal'
description: V tomto kurzu se dozvíte, jak vytvořit zásadu firewallu webových aplikací (WAF) na Azure CDN pomocí Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 9579d0da3347bdd4ecc627662cee42f909cbfaf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92132767"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Kurz: vytvoření zásady WAF na Azure CDN pomocí Azure Portal

V tomto kurzu se dozvíte, jak vytvořit základní zásadu firewallu webových aplikací (WAF) Azure a použít ji pro koncový bod v Azure Content Delivery Network (CDN).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásady WAF
> * Přidružte ho ke koncovému bodu CDN. Zásady WAF můžete přidružit pouze k koncovým bodům, které jsou hostovány na **Azure CDN Standard od společnosti Microsoft** SKU.
> * Konfigurace pravidel WAF

## <a name="prerequisites"></a>Předpoklady

Pomocí pokynů v rychlém startu vytvořte profil Azure CDN a koncový bod [: vytvořte Azure CDN profil a koncový bod](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Vytvoření zásady firewallu webových aplikací

Nejdřív vytvořte základní zásadu WAF se spravovanými výchozí sadou pravidel (DRS) pomocí portálu.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**>hledat **WAF**>vyberte **Firewall webových aplikací** > vyberte **vytvořit**.
2. Na kartě **základy** na stránce **vytvořit zásadu WAF** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Zásady pro            |Vyberte Azure CDN (Preview).|
    | Předplatné            |Vyberte název předplatného pro přední dveře.|
    | Skupina prostředků          |Vyberte název skupiny prostředků front dveří.|
    | Název zásad             |Zadejte jedinečný název pro zásady WAF.|

   :::image type="content" source="../media/waf-cdn-create-portal/basic.png" alt-text="Snímek stránky vytvořit zásadu s použitím revize + vytvoření a hodnot zadaných pro různá nastavení" border="false":::

3. Na kartě **přidružení** na stránce **vytvořit zásadu WAF** vyberte **přidat koncový bod CDN**, zadejte následující nastavení a pak vyberte **Přidat**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Profil CDN              | Vyberte název profilu CDN.|
    | Koncový bod           | Vyberte název koncového bodu a pak vyberte **Přidat**.|
    
    > [!NOTE]
    > Pokud je koncový bod přidružen k zásadě WAF, zobrazí se šedě. Nejprve je nutné odebrat koncový bod z přidružených zásad a pak znovu přidružit koncový bod k nové zásadě WAF.
1. Vyberte **Zkontrolovat a vytvořit** a pak **Vytvořit**.

## <a name="configure-web-application-firewall-policy-optional"></a>Konfigurace zásad firewallu webových aplikací (volitelné)

### <a name="change-mode"></a>Režim změny

Ve výchozím nastavení jsou zásady WAF v režimu *detekce* při vytváření zásad WAF. V režimu *detekce* WAF neblokuje žádné požadavky. Požadavky, které odpovídají pravidlům WAF, se místo toho protokolují v protokolech WAF.

Pokud chcete zobrazit WAF v akci, můžete změnit nastavení režimu z *detekce* na *prevence*. V režimu *prevence* se požadavky, které odpovídají pravidlům definovaným ve výchozí sadě pravidel (DRS), zablokují a přihlásily v protokolech WAF.

 :::image type="content" source="../media/waf-cdn-create-portal/policy.png" alt-text="Snímek obrazovky oddílu nastavení zásad Přepínač Mode je nastaven na hodnotu prevence." border="false":::

### <a name="custom-rules"></a>Vlastní pravidla

Pokud chcete vytvořit vlastní pravidlo, v části **vlastní pravidla** vyberte **Přidat vlastní pravidlo** . Otevře se stránka Konfigurace vlastního pravidla. Existují dva typy vlastních pravidel: pravidlo **shody pravidla** a **Omezení četnosti** .

Následující snímek obrazovky ukazuje vlastní pravidlo shody k blokování požadavku, pokud řetězec dotazu obsahuje hodnotu **blockme**.

:::image type="content" source="../media/waf-cdn-create-portal/custommatch.png" alt-text="Snímek obrazovky se stránkou konfigurace vlastního pravidla zobrazující nastavení pravidla, které kontroluje, zda proměnná řetězce dotazu obsahuje hodnotu blockme." border="false":::

Pravidla omezení přenosové rychlosti vyžadují dvě další pole: **Trvání omezení četnosti** a mezní **hodnota omezení četnosti (požadavky)** , jak je znázorněno v následujícím příkladu:

:::image type="content" source="../media/waf-cdn-create-portal/customrate.png" alt-text="Snímek obrazovky se stránkou konfigurace pravidla pro omezení přenosové rychlosti. Zobrazí se pole seznam trvání omezení četnosti a prahová hodnota omezení četnosti (požadavky)." border="false":::

### <a name="default-rule-set-drs"></a>Výchozí sada pravidel (DRS)

Ve výchozím nastavení je povolená výchozí sada pravidel spravované v Azure. Pokud chcete v rámci skupiny pravidel zakázat jednotlivá pravidla, rozbalte pravidla v této skupině pravidel, zaškrtněte políčko před číslem pravidla a na kartě výše vyberte **Zakázat** . Chcete-li změnit typy akcí pro jednotlivá pravidla v rámci sady pravidel, zaškrtněte políčko před číslem pravidla a potom vyberte kartu **Akce změny** výše.

 :::image type="content" source="../media/waf-cdn-create-portal/managed2.png" alt-text="Snímek obrazovky se stránkou spravovaná pravidla, která zobrazuje sadu pravidel, skupiny pravidel, pravidla a tlačítka akcí povolit, zakázat a změnit. Je zaškrtnuto jedno pravidlo." border="false":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny související prostředky.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si o bráně firewall webových aplikací Azure](../overview.md)
