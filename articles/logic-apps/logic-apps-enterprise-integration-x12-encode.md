---
title: Kódování X12 zprávy – Azure Logic Apps | Microsoft Docs
description: Ověření EDI a převést kódováním XML zprávy s X12 zprávy kodér v podniku integrační balíček pro Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: ec350560f4aca4e9a0b4f00892b690012c9f08dd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299003"
---
# <a name="encode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Kódování X12 zprávy pro Azure Logic Apps s Enterprise integračního balíčku

Konektor kódovat X12 zpráva může ověřit EDI a vlastnosti specifické pro partnery, převést kódováním XML zprávy EDI transakce sady v výměnu a vyžádat technických potvrzení, funkční potvrzení nebo obojí.
Pokud chcete použít tento konektor, je nutné přidat konektor k existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure; můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure. Musí mít účet integrace k používání konektoru kódovat X12 zprávy.
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , jsou již definováni ve vašem účtu integrace
* [X12 smlouvy](logic-apps-enterprise-integration-x12.md) , již je definována v účtu integrace

## <a name="encode-x12-messages"></a>Kódování X12 zprávy

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor kódovat X12 zpráva nemá, aktivační události, je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V návrháři aplikace logiky přidejte aktivační událost a potom přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "x12" filtru. Vyberte buď **X12-dekódovat X12 zprávu název smlouvy** nebo **X12-dekódovat X12 zprávu identity**.
   
    ![Vyhledejte "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Pokud jste nevytvořili dříve všechna připojení k vašemu účtu integrace, se zobrazí výzva k vytvoření připojení nyní. Název připojení a vyberte integrační účet, který chcete připojit. 
   
    ![účet připojení integrace](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název pro připojení. |
    | Integrace účet * |Zadejte název pro váš účet integrace. Ujistěte se, že integrace účet a logiku aplikace jsou ve stejné oblasti Azure. |

5.  Když jste hotovi, by měla vypadat podobně jako tento příklad podrobné informace o připojení. Dokončete vytváření připojení, zvolte **vytvořit**.

    ![Integrace připojení účet vytvořili](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Připojení je nyní vytvořena.

    ![Podrobnosti připojení účtu integrace](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kódování X12 zprávy podle název smlouvy

Pokud jste zvolili pro kódování X12 zprávy podle název smlouvy, otevřete **název X12 smlouvy** seznamu, zadejte nebo vyberte vaše stávající X12 smlouvy. Zadejte zprávu XML ke kódování.

![Zadejte X12 název smlouvy a zpráva XML ke kódování](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Kódování X12 zprávy podle identity

Pokud zvolíte možnost kódování X12 zprávy podle identity, zadejte identifikátor odesílatele, kvalifikátor odesílatel, příjemce identifikátor a příjemce kvalifikátor jako nakonfigurovaný v vaší X12 smlouvy. Vyberte zprávy XML ke kódování.
   
![Zadejte identit pro odesílatele a příjemce, vyberte zprávy XML ke kódování](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 kódování podrobnosti

X12 kódovat konektor provádí tyto úlohy:

* Smlouva řešení pomocí odpovídajících vlastností kontextu odesílatele a příjemce.
* Serializuje výměnu EDI, převodu kódu XML zprávy EDI transakce sady v výměnu.
* Použije sadu transakce hlavičku a přípojného segmenty
* Generuje představuje počet řízení výměnu, číslem skupiny řízení a číslem transakce sadu ovládacího prvku pro každý odchozí výměnu
* Nahradí oddělovače v datové části dat
* Ověří EDI a vlastnosti specifické pro partnery
  * Ověření schématu elementů transakce sady dat proti zpráva schématu
  * Ověření EDI provádět transakce set datové prvky.
  * Rozšířené ověření provádět transakce set datové prvky.
* Požádá o potvrzení o Technical nebo funkčnosti (Pokud je nakonfigurovaná).
  * Potvrzení o technické generuje v důsledku hlavičky ověření. Technické potvrzení hlásí stav zpracování služby výměnu záhlaví a přípojného adresa příjemce
  * Potvrzení o funkční generuje v důsledku ověření obsahu. Funkční potvrzení sestavy každou došlo k chybě při zpracování přijatých dokumentu

## <a name="view-the-swagger"></a>Zobrazení swagger
Najdete v článku [swagger podrobnosti](/connectors/x12/). 

## <a name="next-steps"></a>Další postup
[Další informace o integračního balíčku Enterprise](logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku") 

