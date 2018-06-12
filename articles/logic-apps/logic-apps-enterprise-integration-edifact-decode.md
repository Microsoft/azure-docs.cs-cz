---
title: Dekódovat EDIFACT zprávy – Azure Logic Apps | Microsoft Docs
description: Ověření EDI a generování potvrzení s dekodér zpráva EDIFACT v podniku integrační balíček pro Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bfb64b4abfca6e2a113489b79405b5df2b53c049
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299150"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekódovat EDIFACT zprávy pro Azure Logic Apps s Enterprise integračního balíčku

Konektor dekódovat EDIFACT zpráva může ověřit EDI a vlastnosti specifické pro partnery, rozdělení mimoúrovňové křižovatky do nastaví transakce nebo zachovat celý mimoúrovňové křižovatky a generovat potvrzování pro zpracování transakcí. Pokud chcete použít tento konektor, je nutné přidat konektor k existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure; můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure. Musí mít účet integrace k používání konektoru dekódovat EDIFACT zprávy. 
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , jsou již definováni ve vašem účtu integrace
* [Smlouvy EDIFACT](logic-apps-enterprise-integration-edifact.md) , již je definována v účtu integrace

## <a name="decode-edifact-messages"></a>Dekódovat EDIFACT zprávy

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor dekódovat EDIFACT zpráva nemá aktivačních událostí, je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V návrháři aplikace logiky přidejte aktivační událost a potom přidat akci do aplikace logiky.

3. Do vyhledávacího pole zadejte "EDIFACT" jako filtr. Vyberte **dekódovat EDIFACT zpráva**.
   
    ![hledání EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Pokud jste nevytvořili dříve všechna připojení k vašemu účtu integrace, se zobrazí výzva k vytvoření připojení nyní. Název připojení a vyberte integrační účet, který chcete připojit.
   
    ![Vytvoření účtu integrace](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název pro připojení. |
    | Integrace účet * |Zadejte název pro váš účet integrace. Ujistěte se, že integrace účet a logiku aplikace jsou ve stejné oblasti Azure. |

4. Po dokončení vytvoření připojení, zvolte **vytvořit**. Podrobné informace o připojení by měl vypadat podobně jako tento příklad:

    ![Podrobnosti účtu integrace](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Po vytvoření připojení, jak je znázorněno v tomto příkladu, vyberte zprávu plochý soubor EDIFACT dekódovat.

    ![Integrace připojení účet vytvořili](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Příklad:

    ![Vyberte zprávu plochý soubor EDIFACT pro dekódování](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Podrobnosti decoder EDIFACT

Konektor dekódovat EDIFACT provádí tyto úlohy: 

* Ověří obálky proti obchodování smlouvu pro partnery.
* Přeloží smlouvu porovnáním odesílatele kvalifikátor & identifikátor a příjemce kvalifikátor & identifikátor.
* Rozdělí výměnu do více transakcí, když výměnu má více než jednu transakci na základě smlouvy na získat konfiguraci nastavení.
* Provede zpětný překlad výměnu.
* Ověří EDI a vlastnosti specifické pro partnery, včetně:
  * Ověření struktury výměnu obálky
  * Ověření schématu pro schéma řízení obálky
  * Ověření schématu elementů transakce sady dat pro schéma zpráv
  * Ověření EDI provádět transakce set datové prvky.
* Ověří, že čísla řízení sadu výměnu, skupiny a transakce není duplicitní položky (Pokud je nakonfigurováno) 
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
* Generuje Technical (řízení) nebo funkční potvrzení (Pokud je nakonfigurovaná).
  * Potvrzení o technické nebo CONTRL ACK hlásí výsledky syntaktické kontrolu dokončení přijaté výměnu.
  * Potvrzení o funkční uznává přijmout nebo odmítnout přijaté výměnu nebo skupinu

## <a name="view-swagger-file"></a>Soubor Swagger zobrazení
Chcete-li zobrazit podrobnosti Swagger pro konektor EDIFACT, najdete v části [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Další postup
[Další informace o integračního balíčku Enterprise](logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku") 

