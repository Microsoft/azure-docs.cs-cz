---
title: Dekódovat X12 zprávy – Azure Logic Apps | Microsoft Docs
description: Ověření EDI a generování potvrzení s X12 decoder zpráva v podniku integrační balíček pro Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 14e9f0eefb705eea59a00390494871f5668b0a01
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298657"
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekódovat X12 zprávy pro Azure Logic Apps s Enterprise integračního balíčku

S konektorem dekódování X12 zprávy můžete ověřit obálky proti smlouvy s obchodním partnerem, ověření EDI a vlastnosti specifické pro partnery, rozdělení mimoúrovňové křižovatky do nastaví transakce nebo zachovat celý mimoúrovňové křižovatky a generovat potvrzování pro zpracování transakcí. Pokud chcete použít tento konektor, je nutné přidat konektor k existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure; můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure. Musí mít účet integrace k používání konektoru dekódování X12 zprávy.
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , jsou již definováni ve vašem účtu integrace
* [X12 smlouvy](logic-apps-enterprise-integration-x12.md) , již je definována v účtu integrace

## <a name="decode-x12-messages"></a>Dekódovat X12 zprávy

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor dekódování X12 zpráva nemá, aktivační události, je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V návrháři aplikace logiky přidejte aktivační událost a potom přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "x12" filtru. Vyberte **X12-dekódovat X12 zpráva**.
   
    ![Vyhledejte "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Pokud jste nevytvořili dříve všechna připojení k vašemu účtu integrace, se zobrazí výzva k vytvoření připojení nyní. Název připojení a vyberte integrační účet, který chcete připojit. 

    ![Zadejte podrobnosti připojení účtu integrace](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název pro připojení. |
    | Integrace účet * |Zadejte název pro váš účet integrace. Ujistěte se, že integrace účet a logiku aplikace jsou ve stejné oblasti Azure. |

5.  Když jste hotovi, by měla vypadat podobně jako tento příklad podrobné informace o připojení. Dokončete vytváření připojení, zvolte **vytvořit**.
   
    ![Podrobnosti připojení účtu integrace](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Po připojení se vytvoří, jak je znázorněno v tomto příkladu vyberte X12 plochý soubor zprávy dekódovat.

    ![Integrace připojení účet vytvořili](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Příklad:

    ![Vyberte X12 s plochou zpráva souboru pro dekódování](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Zpráva skutečný obsah nebo datové části pro pole zpráv dobrý nebo špatná, je zakódovaný pomocí base64. Ano je nutné zadat výraz, který zpracovává tento obsah.
   > Tady je příklad, který zpracovává obsah ve formátu XML, které můžete zadat v zobrazení kódu nebo pomocí Tvůrce výrazů v návrháři.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Příklad obsahu](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 dekódovat podrobnosti

X12 dekódování konektor provádí tyto úlohy:

* Ověří obálky proti obchodování smlouvu pro partnery
* Ověří EDI a vlastnosti specifické pro partnery
  * Ověření strukturální EDI a rozšířeného schématu ověření
  * Ověření struktury obálky výměnu.
  * Ověření schématu obálky pro schéma řízení.
  * Ověření schématu elementů transakce sady dat pro schéma zprávy.
  * Ověření EDI provádět transakce set datové prvky. 
* Ověří, zda jsou čísla řízení sadu výměnu, skupiny a transakce není duplicitní položky
  * Ověří číslo výměnu řízení proti dříve přijaté mimoúrovňové křižovatky.
  * Ověří číslo skupiny ovládací prvek pro další skupiny řízení čísla v výměnu.
  * Ověří, že transakce nastavit počet ovládacího prvku na jiné transakci sadu řízení čísla v této skupině.
* Rozdělí výměnu do nastaví transakce, nebo zachovává celý výměnu:
  * Rozdělení výměnu jako sady transakce – pozastavení sady transakce Chyba: výměnu rozdělení do transakce nastaví a analyzuje každá sada transakce. 
  X12 dekódování akce výstupy nastaví pouze tyto transakce, který selže ověření do `badMessages`a výstupy nastaví zbývající transakce `goodMessages`.
  * Rozdělení výměnu jako sady transakce – pozastavení výměnu o chybě: výměnu rozdělení do transakce nastaví a analyzuje každá sada transakce. 
  Pokud jeden nebo více transakcí nastaví v výměnu nezdaří ověření, X12 dekódování akce výstupy nastaví všechny transakce v tomto výměnu k `badMessages`.
  * Zachovat výměnu – pozastavení sady transakce o chybě: zachování výměnu a celý výměnu dávkové zpracování. 
  X12 dekódování akce výstupy nastaví pouze tyto transakce, který selže ověření do `badMessages`a výstupy nastaví zbývající transakce `goodMessages`.
  * Zachovat výměnu – pozastavení výměnu o chybě: zachování výměnu a celý výměnu dávkové zpracování. 
  Pokud jeden nebo více transakcí nastaví v výměnu nezdaří ověření, X12 dekódování akce výstupy nastaví všechny transakce v tomto výměnu k `badMessages`. 
* Generuje Technical nebo funkčnosti potvrzení (Pokud je nakonfigurovaná).
  * Potvrzení o technické generuje v důsledku hlavičky ověření. Technické potvrzení hlásí stav zpracování služby výměnu záhlaví a přípojného adresu příjemce.
  * Potvrzení o funkční generuje v důsledku ověření obsahu. Funkční potvrzení sestavy každou došlo k chybě při zpracování přijatých dokumentu

## <a name="view-the-swagger"></a>Zobrazení swagger
Najdete v článku [swagger podrobnosti](/connectors/x12/). 

## <a name="next-steps"></a>Další postup
[Další informace o integračního balíčku Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku") 

