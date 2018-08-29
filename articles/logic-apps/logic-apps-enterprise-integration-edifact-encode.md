---
title: Kódování zprávy EDIFACT – Azure Logic Apps | Dokumentace Microsoftu
description: Ověřování EDI a generování kódu XML pomocí kodéru zpráv EDIFACT pro Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.date: 01/27/2017
ms.openlocfilehash: e1c990030a9fa9ad25950ccb24b36b82a8c69f5c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122349"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Kódování zpráv EDIFACT pro Azure Logic Apps sadou Enterprise Integration Pack

S konektorem zprávy EDIFACT kódování můžete ověřovat EDI a vlastnosti specifické pro partnera, Generovat dokument XML pro každou sadu transakcí a požádat o technické potvrzení a funkční potvrzení.
Pokud chcete použít tento konektor, musí přidání konektoru do existující aktivační událost ve své aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure. Integrační účet pro použití konektoru zpráv kódování EDIFACT musí mít. 
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , která jsou již definovány v účtu integrace
* [Smlouvy EDIFACT](logic-apps-enterprise-integration-edifact.md) , která je již definována v účtu integrace

## <a name="encode-edifact-messages"></a>Kódování zpráv EDIFACT

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor zprávy EDIFACT kódování nemá aktivačních událostí, proto musíte přidat aktivační událost pro spuštění aplikace logiky jako trigger požadavku. V návrháři aplikace logiky přidávat aktivační události a pak přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte jako filtr "EDIFACT". Vyberte buď **kódování zprávy EDIFACT podle názvu smlouvy** nebo **kódovat do zprávy EDIFACT podle identit**.
   
    ![hledání EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Pokud jste nevytvořili dříve všechna připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení. Název připojení a vyberte účet pro integraci, kterou chcete připojit.

    ![Vytvoření připojení k účtu integrace](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název vašeho připojení. |
    | Účet integrace * |Zadejte název účtu pro integraci. Ujistěte se, že integrační účet a logiku aplikace jsou ve stejném umístění Azure. |

5.  Až budete hotovi, vaše podrobnosti připojení by měl vypadat podobně jako tento příklad. Chcete-li dokončit vytváření připojení, zvolte **vytvořit**.

    ![Podrobnosti připojení účtu integrace](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Připojení je vytvořený.

    ![Vytvoření připojení k účtu integrace](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Kódování zprávy EDIFACT podle názvu smlouvy

Pokud jste se rozhodli kódování zprávy EDIFACT podle názvu smlouvy, otevřete **smlouvy EDIFACT název** seznamu, zadejte nebo vyberte název smlouvy EDIFACT. Zadejte zprávu XML ke kódování.

![Zadejte název smlouvy EDIFACT a zpráva XML k zakódování](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Kódování zprávy EDIFACT podle identit

Pokud se rozhodnete ke kódování zprávy EDIFACT podle identit, zadejte identifikátor odesílatele, kvalifikátor odesílatele, příjemce identifikátor a kvalifikátor příjemce podle konfigurace v smlouvy EDIFACT. Vyberte zpráva XML k zakódování.

![Zadejte identit pro odesílatele a příjemce, vyberte zpráva XML k zakódování](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Kódování EDIFACT podrobnosti

Kódování EDIFACT connector provádí tyto úlohy: 

* Vyřešit smlouvu to provede spárováním odpovídajících kvalifikátor odesílatele & identifikátoru a kvalifikátoru pro příjemce a identifikátor
* Serializuje výměna EDI, převádění XML kódované zprávy EDI sady transakcí v výměna.
* Platí segmenty záhlaví a koncová část sady transakcí
* Generuje kontrolní číslo výměny, kontrolní číslo skupiny a kontrolní číslo sady transakcí pro každou odchozí výměny
* Nahradí oddělovače v datové části
* Ověří EDI a vlastnosti specifické pro partnera
  * Ověření schématu datové sady transakcí prvky proti schématu zprávy.
  * Ověřování EDI provedla sada transakcí datové prvky.
  * Rozšíření ověřování provedla sada transakcí datových prvků
* Vygeneruje dokument XML pro každou sadu transakcí.
* Požádá o potvrzení technické a/nebo funkční (Pokud je nakonfigurovaná).
  * Jako technické potvrzení zpráva CONTRL znamená přijetí výměny.
  * Jako funkční potvrzení zprávy CONTRL znamená přijetí nebo zamítnutí přijatý výměny, skupiny nebo zprávy, se seznamem chyby nebo nepodporované funkce

## <a name="view-swagger-file"></a>Zobrazit soubor Swagger
Chcete-li zobrazit podrobnosti Swagger pro konektor EDIFACT, naleznete v tématu [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Další postup
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack") 

