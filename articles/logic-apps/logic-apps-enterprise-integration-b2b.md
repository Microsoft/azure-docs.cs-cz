---
title: Vytvořte podnikové integrace B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Příjem dat B2B v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: ad7a29f4a554d599b17576921542b1ac6e403911
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127760"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Příjem dat B2B s Azure Logic Apps a Enterprise Integration Pack

Po vytvoření účtu pro integraci s partnery a dohody, budete chtít vytvořit pracovní postup (B2B) B2B pro vaši aplikaci logiky s [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Požadavky

Použití AS2 a X12 akce, musíte mít účet podnikové integrace. Přečtěte si [tom, jak vytvořit integrační účet organizace](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Vytvoření aplikace logiky s konektory B2B

Postupujte podle těchto kroků můžete vytvořit aplikaci logiky B2B, které používá AS2 a X12 akce přijímat data z obchodního partnera:

1. Vytvoření aplikace logiky, pak [propojit aplikaci se svým účtem integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Přidat **požadavek – přijetí požadavku HTTP při** trigger aplikace logiky.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Chcete-li přidat **dekódování AS2** akci, vyberte **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Chcete-li filtrovat všechny akce, který chcete, zadejte slovo **as2** do vyhledávacího pole.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Vyberte **AS2 - zpráva dekódování AS2** akce.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Přidat **tělo** , kterou chcete použít jako vstup. V tomto příkladu vyberte tělo požadavku HTTP, který aktivuje aplikace logiky. Nebo zadejte výraz, který vstupů záhlaví ve **záhlaví** pole:

    @triggerOutputs(["záhlaví"])

7. Přidejte požadované **záhlaví** pro AS2, které můžete vyhledat v hlavičkách žádosti protokolu HTTP. V tomto příkladu vyberte záhlaví požadavku HTTP, které aktivují aplikace logiky.

8. Nyní přidejte akce X12 dekódování zprávy. Vyberte **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Chcete-li filtrovat všechny akce, který chcete, zadejte slovo **x12** do vyhledávacího pole.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Vyberte **X12 – dekódování X12 zpráva** akce.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Nyní je nutné zadat vstup pro tuto akci. Tento vstup je výstup z předchozí akci AS2.

    Obsah zprávy skutečné je v objektu JSON a s kódováním base64, musejí zadat výraz jako vstup. 
    Zadejte následující výraz v **X12 plochého souboru zprávy do dekódování** vstupní pole:
    
    @base64ToString(body('Decode_AS2_message')? ["AS2Message']? ["Obsah"])

    Nyní přidáte další kroky k dekódování X12 dat od obchodního partnera a výstupní položky v objektu JSON. 
    Upozornit partnera, že data byla přijata, můžete odeslat zpět odpověď obsahující AS2 zpráva dispozice oznámení (MDN) v akci odpovědi HTTP.

12. Chcete-li přidat **odpovědi** akce, zvolte **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Chcete-li filtrovat všechny akce, který chcete, zadejte slovo **odpovědi** do vyhledávacího pole.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Vyberte **odpovědi** akce.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Pro přístup k MDN z výstupu **dekódování X12 zpráva** akce, nastavení odezvy **tělo** pole tento výraz:

    @base64ToString(body('Decode_AS2_message')? ["OutgoingMdn']? ["Obsah"])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Uložte svou práci.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Jsou teď provádí nastavení aplikace logiky B2B. V reálné aplikaci může být vhodné ukládat dekódovaný X12 data v úložišti obchodní (LOB) aplikace nebo data. Pro připojení vlastních obchodních aplikací a pomocí těchto rozhraní API ve vaší aplikaci logiky, můžete přidat další akce nebo napsat vlastní rozhraní API.

## <a name="features-and-use-cases"></a>Funkce a případy použití

* AS2 a X12 dekódovat a kódování akce umožňují výměnu dat mezi obchodními partnery s použitím standardních oborových protokolů v logic apps.
* K výměně dat s obchodními partnery, můžete použít AS2 a X12 s nebo bez něj mezi sebou.
* Akcí B2B vám pomůžou snadno vytvářet partnery a smlouvy v účtu integrace a využívat v aplikaci logiky.
* Když rozšíříte aplikace logiky s další akce, můžete odesílat a přijímat data mezi ostatním aplikacím a službám, jako je SalesForce.

## <a name="learn-more"></a>Další informace
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
