---
title: Kódování zpráv X12
description: Ověření EDI a převod zpráv kódovaných XML pomocí kodéru zpráv X12 v aplikacích Azure Logic Apps s podnikovou integrací Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792344"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kódování zpráv X12 v aplikacích Azure Logic Apps pomocí sady Enterprise Integration Pack

Pomocí konektoru zpráv Encode X12 můžete ověřit vlastnosti EDI a specifické pro partnera, převést zprávy kódované XML do sad transakcí EDI ve výměně a požádat o technické potvrzení, funkční potvrzení nebo obojí.
Chcete-li použít tento konektor, musíte přidat konektor do existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Zde jsou položky, které potřebujete:

* Účet Azure; můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účet integrace,](logic-apps-enterprise-integration-create-integration-account.md) který už je definovaný a přidružený k vašemu předplatnému Azure. Chcete-li použít konektor zprávy Encode X12, musíte mít účet integrace.
* Alespoň dva [partneři,](logic-apps-enterprise-integration-partners.md) kteří jsou již definováni ve vašem integračním účtu
* [Smlouva X12,](logic-apps-enterprise-integration-x12.md) která je již definována ve vašem integračním účtu

## <a name="encode-x12-messages"></a>Kódování zpráv X12

1. [Vytvořte aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor zpráv Encode X12 nemá aktivační události, takže je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V Návrháři aplikace logiky přidejte aktivační událost a pak přidejte akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte pro filtr "x12". Vyberte buď **X12 - Zakódovat zprávu X12 podle názvu smlouvy** nebo **X12 - Zakódovat do X12 zprávy podle identit**.
   
    ![Hledat "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Pokud jste dříve nevytvořili žádná připojení ke svému účtu integrace, budete vyzváni k vytvoření tohoto připojení nyní. Pojmenujte připojení a vyberte účet integrace, který chcete připojit. 
   
    ![připojení integračního účtu](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Jsou vyžadovány vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název připojení. |
    | Integrační účet * |Zadejte název účtu integrace. Ujistěte se, že váš účet integrace a aplikace logiky jsou ve stejném umístění Azure. |

5.  Po dokončení by měly vypadat podobně jako v tomto příkladu podrobnosti o připojení. Chcete-li dokončit vytváření připojení, zvolte **Vytvořit**.

    ![vytvořeno připojení účtu integrace](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Vaše připojení je nyní vytvořeno.

    ![podrobnosti o připojení účtu integrace](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kódování zpráv X12 podle názvu smlouvy

Pokud jste se rozhodli kódovat zprávy X12 podle názvu smlouvy, otevřete seznam **Název smlouvy X12,** zadejte nebo vyberte stávající smlouvu X12. Zadejte zprávu XML, kterou chcete zakódovat.

![Zadejte název smlouvy X12 a zprávu XML pro kódování.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Kódování zpráv X12 podle identit

Pokud se rozhodnete kódovat zprávy X12 podle identit, zadejte identifikátor odesílatele, kvalifikátor odesílatele, identifikátor příjemce a kvalifikátor příjemce, jak je nakonfigurován o smlouvě X12. Vyberte zprávu XML, kterou chcete zakódovat.
   
![Poskytněte identity odesílateli a příjemci, vyberte zprávu XML, kterou chcete kódovat](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 Kódovat podrobnosti

Konektor X12 Encode provádí tyto úkoly:

* Řešení dohody odpovídajícími vlastnostmi kontextu odesílatele a příjemce.
* Serializuje výměnu EDI a převádí zprávy kódované XML do sad transakcí EDI v výměně.
* Použije segmenty záhlaví a segmenty přívěsu sady transakcí.
* Generuje kontrolní číslo výměny, číslo řízení skupiny a řídicí číslo sady transakcí pro každou odchozí výměnu.
* Nahrazuje oddělovače v datech datové části.
* Ověří EDI a vlastnosti specifické pro partnera.
  * Ověření schématu datových prvků sady transakcí proti schématu zprávy
  * Ověření EDI provedené u datových prvků sady transakcí.
  * Rozšířené ověření provedené u datových prvků sady transakcí
* Požaduje technické a/nebo funkční potvrzení (pokud je nakonfigurováno).
  * Technické potvrzení generuje v důsledku ověření záhlaví. Technické potvrzení hlásí stav zpracování výměnné hlavičky a přípojného vozidla příjemcem adresy
  * Funkční potvrzení generuje v důsledku ověření těla. Funkční potvrzení hlásí každou chybu, ke které došlo při zpracování přijatého dokumentu

## <a name="view-the-swagger"></a>Zobrazit naparování
Podívejte se na [podrobnosti o chvástání](/connectors/x12/). 

## <a name="next-steps"></a>Další kroky
[Další informace o balíčku Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Další informace o balíčku Enterprise Integration Pack") 

