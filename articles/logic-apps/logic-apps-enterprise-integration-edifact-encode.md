---
title: Kódování zprávy EDIFACT – Azure Logic Apps | Microsoft Docs
description: Ověření EDI a vygenerování XML pomocí kodéru zpráv EDIFACT v podniku integrační balíček pro Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: eaad2561254d858af99f06e576d67c05838e1220
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299888"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Kódování zprávy EDIFACT pro Azure Logic Apps s Enterprise integračního balíčku

Konektor EDIFACT kódování zpráv může ověřit EDI a vlastnosti specifické pro partnery, Generovat dokument XML pro každou sadu transakce a vyžádat technických potvrzení, funkční potvrzení nebo obojí.
Pokud chcete použít tento konektor, je nutné přidat konektor k existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure; můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure. Musí mít účet integrace k používání konektoru EDIFACT kódování zprávy. 
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , jsou již definováni ve vašem účtu integrace
* [Smlouvy EDIFACT](logic-apps-enterprise-integration-edifact.md) , již je definována v účtu integrace

## <a name="encode-edifact-messages"></a>Kódování zprávy EDIFACT

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor EDIFACT kódování zprávy nemá aktivačních událostí, proto musíte přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V návrháři aplikace logiky přidejte aktivační událost a potom přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "EDIFACT" jako filtr. Vyberte buď **zakódovat zprávu EDIFACT název smlouvy** nebo **kódovat EDIFACT zprávu pomocí identity**.
   
    ![hledání EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Pokud jste nevytvořili dříve všechna připojení k vašemu účtu integrace, se zobrazí výzva k vytvoření připojení nyní. Název připojení a vyberte integrační účet, který chcete připojit.

    ![Vytvoření připojení účtu integrace](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název pro připojení. |
    | Integrace účet * |Zadejte název pro váš účet integrace. Ujistěte se, že integrace účet a logiku aplikace jsou ve stejné oblasti Azure. |

5.  Když jste hotovi, by měla vypadat podobně jako tento příklad podrobné informace o připojení. Dokončete vytváření připojení, zvolte **vytvořit**.

    ![Podrobnosti připojení účtu integrace](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Připojení je nyní vytvořena.

    ![Integrace připojení účet vytvořili](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Kódování zprávy EDIFACT podle název smlouvy

Pokud jste se rozhodli kódování zprávy EDIFACT podle názvu smlouvy, otevřete **název EDIFACT smlouvy** seznamu, zadejte nebo vyberte název smlouvy EDIFACT. Zadejte zprávu XML ke kódování.

![Zadejte název smlouvy EDIFACT a zpráva XML ke kódování](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Kódování zprávy EDIFACT podle identity

Pokud si zvolíte ke kódování zprávy EDIFACT podle identity, zadejte identifikátor odesílatele, kvalifikátor odesílatel, příjemce identifikátor a příjemce kvalifikátor podle konfigurace v EDIFACT smlouvě. Vyberte zprávy XML ke kódování.

![Zadejte identit pro odesílatele a příjemce, vyberte zprávy XML ke kódování](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Kódování EDIFACT podrobnosti

Konektor kódování EDIFACT provádí tyto úlohy: 

* Smlouvu můžete vyřešit odpovídající odesílatele kvalifikátor & identifikátor a kvalifikátor příjemce a identifikátor
* Serializuje výměnu EDI, převodu kódu XML zprávy EDI transakce sady v výměnu.
* Použije sadu transakce hlavičku a přípojného segmenty
* Generuje představuje počet řízení výměnu, číslem skupiny řízení a číslem transakce sadu ovládacího prvku pro každý odchozí výměnu
* Nahradí oddělovače v datové části dat
* Ověří EDI a vlastnosti specifické pro partnery
  * Ověření schématu elementů transakce sady dat pro schéma zprávy.
  * Ověření EDI provádět transakce set datové prvky.
  * Rozšířené ověření provádět transakce set datové prvky.
* Vygeneruje dokument XML pro každou sadu transakce.
* Požádá o potvrzení o Technical nebo funkčnosti (Pokud je nakonfigurovaná).
  * Jako potvrzení o technické označuje zprávy CONTRL přijetí výměnu.
  * Jako potvrzení o funkční CONTRL zpráva znamená přijetí nebo odmítnutí přijaté výměnu, skupiny nebo zprávy, se seznamem chyby nebo nepodporované funkce

## <a name="view-swagger-file"></a>Soubor Swagger zobrazení
Chcete-li zobrazit podrobnosti Swagger pro konektor EDIFACT, najdete v části [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Další postup
[Další informace o integračního balíčku Enterprise](logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku") 

