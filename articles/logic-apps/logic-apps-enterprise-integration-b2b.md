---
title: Vytváření řešení B2B - Azure Logic Apps | Microsoft Docs
description: Přijímat data v aplikacích logiky pomocí funkcí B2B v podniku integračního balíčku
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: a27a413ba9a0d974cf90fe842d5fc325ab308a56
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298113"
---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Přijímat data v aplikacích logiky s funkcemi B2B v podniku integračního balíčku

Po vytvoření účtu integrace s partnery a smlouvy, jste připraveni k vytvoření pracovního postupu obchodní – firmy (B2B) pro svou aplikaci logiky s [Enterprise integračního balíčku](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Požadavky

Použití AS2 a X12 akce, musí mít účet integrace podnikové sítě. Další informace [postup vytvoření účtu integrace Enterprise](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Vytvoření aplikace logiky s konektory B2B

Postupujte podle těchto kroků můžete vytvořit aplikaci logiky B2B, které používá AS2 a X12 akce přijímat data z obchodního partnera:

1. Vytvoření aplikace logiky, pak [propojit aplikaci se svým účtem integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Přidat **požadavku - obdrží žádost HTTP při** aktivační svou aplikaci logiky.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Chcete-li přidat **dekódovat AS2** akce, vyberte **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Chcete-li všechny akce s klíčem, který chcete filtrovat, zadejte slovo **as2** do vyhledávacího pole.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Vyberte **AS2 – zpráva dekódovat AS2** akce.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Přidat **textu** , kterou chcete použít jako vstup. V tomto příkladu vyberte textu požadavku HTTP, která aktivuje aplikaci logiky. Nebo zadejte výraz, který vstup hlavičky v **HLAVIČKY** pole:

    @triggerOutputs() [záhlaví]

7. Přidejte požadované **hlavičky** pro AS2, které můžete najít v hlavičkách žádosti protokolu HTTP. V tomto příkladu vyberte hlavičky požadavku HTTP, které spouštějí aplikace logiky.

8. Nyní přidejte akce dekódování X12 zprávy. Vyberte **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Chcete-li všechny akce s klíčem, který chcete filtrovat, zadejte slovo **x12** do vyhledávacího pole.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Vyberte **X12-dekódovat X12 zpráva** akce.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Nyní je nutné určit vstup pro tuto akci. Tento vstup je výstup z předchozí akce AS2.

    Zpráva skutečný obsah je v objektu JSON a s kódováním base64, musíte zadat výraz jako vstup. 
    Zadejte následující výraz v **X12 s PLOCHOU dekódování na zprávu souboru** vstupní pole:
    
    @base64ToString(body('Decode_AS2_message')? ['AS2Message']? ['Content'])

    Nyní přidáte kroky pro dekódování X12 data přijata z obchodního partnera a výstupní položek v objektu JSON. 
    Oznámit partnerovi přijímání dat, můžete odeslat zpět odpověď obsahující AS2 zpráva dispozice oznámení (MDN) v akce odpovědi HTTP.

12. Chcete-li přidat **odpovědi** akce, zvolte **přidat akci**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Chcete-li všechny akce s klíčem, který chcete filtrovat, zadejte slovo **odpovědi** do vyhledávacího pole.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Vyberte **odpovědi** akce.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Pro přístup MDN z výstupu **dekódování X12 zpráva** akce, nastavte odpovědi **textu** pole s tímto výrazem:

    @base64ToString(body('Decode_AS2_message')? ['OutgoingMdn']? ['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Uložte svou práci.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Nyní dokončení nastavení aplikace logiky B2B. V reálné aplikaci, můžete chtít uložit dekódované X12 dat v úložišti obchodní (LOB) aplikace nebo data. K připojení vlastní obchodní aplikace a použijte tato rozhraní API v aplikaci logiky, můžete přidat další akce nebo můžete zapsat vlastní rozhraní API.

## <a name="features-and-use-cases"></a>Funkce a případy použití

* AS2 a X12 dekódovat a kódování akce umožňují výměnu dat mezi obchodních partnerů pomocí standardních protokolů v aplikacích logiky.
* Chcete-li vyměňovat data s obchodními partnery, můžete použít AS2 a X12 s nebo bez navzájem.
* Akce B2B můžete jednoduše vytvořit partnery a smluv ve vašem účtu integrace a využívat v aplikaci logiky.
* Když rozšíříte svou aplikaci logiky s další akce, můžete odesílat a přijímat data mezi jiným aplikacím a službám, jako je SalesForce.

## <a name="learn-more"></a>Další informace
[Další informace o Enterprise integračního balíčku](logic-apps-enterprise-integration-overview.md)
