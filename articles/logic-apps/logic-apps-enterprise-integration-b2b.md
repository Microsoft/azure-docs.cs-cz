---
title: Vytváření podnikových integrací B2B
description: Příjem dat B2B v Azure Logic Apps s využitím Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 39966b8171296a8608b9436485f7682d114c8410
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793095"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Příjem dat B2B pomocí Azure Logic Apps a Enterprise Integration Pack

Po vytvoření účtu pro integraci, který má partnery a smlouvy, jste připraveni vytvořit pracovní postup B2B (Business to Business) pro vaši aplikaci logiky pomocí [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li použít akce AS2 a X12, je nutné mít účet Podniková integrace. Naučte [se vytvořit účet podniková integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Vytvoření aplikace logiky s konektory B2B

Pomocí těchto kroků můžete vytvořit aplikaci B2B Logic, která pro příjem dat z obchodního partnera používá akce AS2 a X12:

1. Vytvořte aplikaci logiky a pak [Připojte aplikaci k účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Přidejte **požadavek – když se** do aplikace logiky přijme požadavek HTTP triggeru.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Chcete-li přidat akci **dekódovat AS2** , vyberte **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Chcete-li filtrovat všechny akce, které požadujete, zadejte do vyhledávacího pole slovo **AS2** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Vyberte akci **AS2-dekódovat AS2 Message** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Přidejte **text** , který chcete použít jako vstup. 
   V tomto příkladu vyberte tělo požadavku HTTP, které aktivuje aplikaci logiky. Nebo zadejte výraz, který vstupní hlavičky v poli **Headers** :

    @triggerOutputs() [' headers ']

7. Přidejte požadovaná **záhlaví** pro AS2, která najdete v HLAVIČCE požadavku HTTP. 
   V tomto příkladu vyberte hlavičky požadavků HTTP, které aktivují aplikaci logiky.

8. Nyní přidejte akci dekódovat zprávu X12. Vyberte **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Chcete-li filtrovat všechny akce, které požadujete, zadejte do vyhledávacího pole slovo **X12** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Vyberte akci **X12-dekódovat X12 Message** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Nyní musíte zadat vstup pro tuto akci. 
    Tento vstup je výstupem z předchozí akce AS2.

    Skutečný obsah zprávy je v objektu JSON a má kódování Base64, takže musíte zadat výraz jako vstup. 
    Do pole **zpráva X12 plochého souboru pro dekódování** vstupního pole zadejte následující výraz:
    
    @base64ToString(tělo (' Decode_AS2_message ')? [' AS2Message']? [' Content '])

    Nyní přidejte kroky k dekódování dat X12 přijatých od obchodního partnera a výstupních položek v objektu JSON. 
    Chcete-li oznámit partnerovi, že data byla přijata, můžete v akci HTTP Response odeslat zpětnou odpověď obsahující oznámení o AS2i zprávy o zařazování (MDN).

12. Chcete-li přidat akci **odpovědi** , vyberte možnost **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Pokud chcete filtrovat všechny akce, které chcete, zadejte do vyhledávacího pole **odpověď** Wordu.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Vyberte akci **odpovědi** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Pokud chcete získat přístup k MDN z výstupu akce **dekódovat X12 Message** , nastavte pole **text** odpovědi s tímto výrazem:

    @base64ToString(tělo (' Decode_AS2_message ')? [' OutgoingMdn']? [' Content '])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Uložte svou práci.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Nyní jste dokončili nastavování vaší aplikace logiky B2B. V reálné aplikaci můžete ukládat dekódovat X12 data do obchodní aplikace (LOB) nebo do úložiště dat. Pokud chcete připojit své vlastní obchodní aplikace a používat tato rozhraní API ve vaší aplikaci logiky, můžete přidat další akce nebo napsat vlastní rozhraní API.

## <a name="features-and-use-cases"></a>Funkce a případy použití

* Akce dekódování a kódování AS2 a X12 umožňují výměnu dat mezi obchodními partnery pomocí standardních protokolů v Logic Apps.
* K výměně dat s obchodními partnery můžete použít AS2 a X12 s jinými nebo bez nich.
* Akce B2B vám pomůžou snadno vytvářet partnery a smlouvy v účtu pro integraci a využívat je v aplikaci logiky.
* Když rozšíříte aplikaci logiky s jinými akcemi, můžete odesílat a přijímat data mezi jinými aplikacemi a službami, jako je SalesForce.

## <a name="learn-more"></a>Další informace
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
