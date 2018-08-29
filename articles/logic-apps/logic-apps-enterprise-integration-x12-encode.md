---
title: Kódování X12 zprávy – Azure Logic Apps | Dokumentace Microsoftu
description: Ověřování EDI a převodu kódu XML zprávy s X12 zprávy kodér v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.date: 01/27/2017
ms.openlocfilehash: 3ed5cb61fef5f07913f11c4e4df309d720d5b901
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123501"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kódování X12 zpráv ve službě Azure Logic Apps sadou Enterprise Integration Pack

Konektor kódovat X12 zpráva může ověřit EDI a vlastnosti specifické pro partnera, převést na XML kódování zprávy EDI sady transakcí v výměna a požádat o technické potvrzení a funkční potvrzení.
Pokud chcete použít tento konektor, musí přidání konektoru do existující aktivační událost ve své aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure. Musíte mít účtu pro integraci k používání konektoru kódovat X12 zprávy.
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , která jsou již definovány v účtu integrace
* [X12 smlouvy](logic-apps-enterprise-integration-x12.md) , která je již definována v účtu integrace

## <a name="encode-x12-messages"></a>Kódování X12 zprávy

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor kódovat X12 zpráva nemá aktivačních událostí, proto musíte přidat aktivační událost pro spuštění aplikace logiky jako trigger požadavku. V návrháři aplikace logiky přidávat aktivační události a pak přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "x12" filtr. Vyberte buď **X12 – kódování X12 zprávy podle názvu smlouvy** nebo **X12 – kódování X12 zprávy podle identit**.
   
    ![Vyhledejte "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Pokud jste nevytvořili dříve všechna připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení. Název připojení a vyberte účet pro integraci, kterou chcete připojit. 
   
    ![připojení účtu integrace](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název vašeho připojení. |
    | Účet integrace * |Zadejte název účtu pro integraci. Ujistěte se, že integrační účet a logiku aplikace jsou ve stejném umístění Azure. |

5.  Až budete hotovi, vaše podrobnosti připojení by měl vypadat podobně jako tento příklad. Chcete-li dokončit vytváření připojení, zvolte **vytvořit**.

    ![Vytvoření připojení k účtu integrace](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Připojení je vytvořený.

    ![Podrobnosti připojení účtu integrace](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kódování X12 zprávy podle názvu smlouvy

Pokud jste se rozhodli kódování X12 zprávy podle názvu smlouvy, otevřete **název X12 smlouvy** seznamu, zadejte nebo vyberte existující X12 smlouvy. Zadejte zprávu XML ke kódování.

![Zadejte X12 název smlouvy a zpráva XML k zakódování](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Kódování X12 zprávy podle identit

Pokud se rozhodnete ke kódování X12 zprávy podle identit, zadejte identifikátor odesílatele, kvalifikátor odesílatele, příjemce identifikátor a kvalifikátor příjemce podle konfigurace v vaše X12 smlouvy. Vyberte zpráva XML k zakódování.
   
![Zadejte identit pro odesílatele a příjemce, vyberte zpráva XML k zakódování](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 kódování podrobnosti

X12 kódovat connector provádí tyto úlohy:

* Řešení smlouvy to provede spárováním odpovídajících odesílatele a příjemce vlastnosti kontextu.
* Serializuje výměna EDI, převádění XML kódované zprávy EDI sady transakcí v výměna.
* Platí segmenty záhlaví a koncová část sady transakcí
* Generuje kontrolní číslo výměny, kontrolní číslo skupiny a kontrolní číslo sady transakcí pro každou odchozí výměny
* Nahradí oddělovače v datové části
* Ověří EDI a vlastnosti specifické pro partnera
  * Ověření schématu datové sady transakcí prvky proti zprávě schématu
  * Ověřování EDI provedla sada transakcí datové prvky.
  * Rozšíření ověřování provedla sada transakcí datových prvků
* Požádá o potvrzení technické a/nebo funkční (Pokud je nakonfigurovaná).
  * Technické potvrzení vygeneruje jako výsledek ověření záhlaví. Technické potvrzení hlásí stav zpracování výměny záhlaví a koncová část adresa příjemce
  * Funkční potvrzení vygeneruje jako výsledek ověření textu. Funkční potvrzení sestavy každou došlo k chybě při zpracování přijatých dokumentu

## <a name="view-the-swagger"></a>Zobrazení swaggeru
Zobrazit [swagger podrobnosti](/connectors/x12/). 

## <a name="next-steps"></a>Další postup
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack") 

