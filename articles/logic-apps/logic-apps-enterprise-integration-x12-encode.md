---
title: Kódování zpráv X12
description: Ověřování XML a převod zpráv kódovaných v jazyce XML pomocí kodéru zpráv X12 v Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792344"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kódování zpráv X12 v Azure Logic Apps pomocí Enterprise Integration Packu

Pomocí konektoru pro kódování zpráv X12 můžete ověřit vlastnosti specifické pro EDI a partnera, převést zprávy kódované XML na sady transakcí EDI ve výměně a požádat o technické potvrzení, funkční potvrzení nebo obojí.
Pokud chcete tento konektor použít, musíte ho přidat do existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady jsou položky, které potřebujete:

* Účet Azure; Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free) .
* [Účet pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který je už definovaný a přidružený k vašemu předplatnému Azure. K používání konektoru zpráv X12 pro kódování musíte mít účet pro integraci.
* Alespoň dva [partneři](logic-apps-enterprise-integration-partners.md) , kteří jsou již definováni v účtu integrace
* [Smlouva X12](logic-apps-enterprise-integration-x12.md) , která je už definovaná v účtu integrace

## <a name="encode-x12-messages"></a>Kódování zpráv X12

1. [Vytvořte aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor zpráv X12 ve službě encode neobsahuje aktivační události, takže musíte přidat Trigger pro spuštění aplikace logiky, jako je Trigger žádosti. V návrháři aplikace logiky přidejte Trigger a pak přidejte akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "X12" pro svůj filtr. Vyberte buď **X12-encode pro X12 zprávu podle názvu smlouvy** , nebo **X12-encode pro X12 zprávy podle identity**.
   
    ![Vyhledejte "X12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Pokud jste dříve nevytvořili žádná připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení nyní. Zadejte název připojení a vyberte účet pro integraci, který chcete připojit. 
   
    ![připojení k účtu pro integraci](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Jsou vyžadovány vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název připojení. |
    | Účet pro integraci * |Zadejte název účtu pro integraci. Ujistěte se, že váš účet pro integraci a aplikace logiky jsou ve stejném umístění Azure. |

5.  Až budete hotovi, vaše podrobnosti o připojení by měly vypadat podobně jako v tomto příkladu. Chcete-li dokončit vytváření připojení, klikněte na tlačítko **vytvořit**.

    ![vytvořilo se připojení k účtu pro integraci.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Připojení je nyní vytvořeno.

    ![podrobnosti připojení účtu pro integraci](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kódovat zprávy X12 podle názvu smlouvy

Pokud jste se rozhodli kódovat zprávy X12 podle názvu smlouvy, otevřete seznam **X12 smlouvy** , zadejte nebo vyberte svou stávající smlouvu X12. Zadejte zprávu XML, která se má zakódovat.

![Zadejte název smlouvy X12 a zprávu XML ke kódování.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Kódování zpráv X12 podle identit

Pokud se rozhodnete zakódovat zprávy X12 podle identity, zadejte identifikátor odesilatele, kvalifikátor odesílatele, identifikátor přijímače a kvalifikátor přijímače podle konfigurace ve vaší smlouvě X12. Vyberte zprávu XML, která se má zakódovat.
   
![Zadejte identity pro odesílatele a přijímače, vyberte zprávu XML ke kódování.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Podrobnosti kódování X12

Konektor X12 encode provádí tyto úlohy:

* Řešení smlouvy porovnáním vlastností kontextu odesílatele a přijímače.
* Serializace výměny EDI a převádí zprávy kódované XML na sady transakcí EDI ve výměně.
* Použije záhlaví sady transakcí a segmenty přípojných vozidel.
* Vygeneruje řídicí číslo výměny, řídicí číslo skupiny a řídicí číslo sady transakcí pro každý odchozí výměnu.
* Nahradí oddělovače v datech datové části.
* Ověří vlastnosti EDI a pro konkrétní partnery.
  * Ověřování schématu datových elementů transakce-sady podle schématu zprávy
  * Ověřování EDI bylo provedeno v datových prvcích sady transakcí.
  * Rozšířené ověřování provedeno v datových prvcích sady transakcí
* Požaduje technické nebo funkční potvrzení (Pokud je nakonfigurované).
  * Technické potvrzení vygeneruje výsledek ověřování hlaviček. Technické potvrzení oznamuje stav zpracování záhlaví a přípojného vozidla pro přijímač adres.
  * Funkční potvrzení generuje výsledek ověření těla. Funkční potvrzení oznamuje každou chybu, ke které došlo při zpracování přijatého dokumentu.

## <a name="view-the-swagger"></a>Zobrazit Swagger
Podívejte se na [Podrobnosti Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Další kroky
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack") 

