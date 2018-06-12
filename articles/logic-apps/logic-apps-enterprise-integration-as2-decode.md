---
title: Dekódovat AS2 zprávy – Azure Logic Apps | Microsoft Docs
description: Jak používat dekodér AS2 v podniku integrační balíček pro Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: a5ca615d984d07513b12399b6f7e7901490f2e41
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298827"
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekódovat AS2 zprávy pro Azure Logic Apps s Enterprise integračního balíčku 

K vytvoření zabezpečení a spolehlivost při přenosu zprávy, pomocí konektoru zpráva dekódovat AS2. Tento konektor poskytuje digitální podpis, dešifrování a potvrzení prostřednictvím zpráv dispozice oznámení (MDN).

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure; můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure. Musí mít účet integrace k používání konektoru zpráva dekódovat AS2.
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , jsou již definováni ve vašem účtu integrace
* [Smlouvy AS2](logic-apps-enterprise-integration-as2.md) , již je definována v účtu integrace

## <a name="decode-as2-messages"></a>Dekódovat AS2 zprávy

1. [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Konektor dekódovat AS2 zpráva nemá aktivačních událostí, je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V návrháři aplikace logiky přidejte aktivační událost a potom přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "AS2" filtru. Vyberte **AS2 - dekódovat AS2 zpráva**.
   
    ![Vyhledejte "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Pokud jste nevytvořili dříve všechna připojení k vašemu účtu integrace, se zobrazí výzva k vytvoření připojení nyní. Název připojení a vyberte integrační účet, který chcete připojit.
   
    ![Vytvoření připojení integrace](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název pro připojení. |
    | Integrace účet * |Zadejte název pro váš účet integrace. Ujistěte se, že integrace účet a logiku aplikace jsou ve stejné oblasti Azure. |

5.  Když jste hotovi, by měla vypadat podobně jako tento příklad podrobné informace o připojení. Dokončete vytváření připojení, zvolte **vytvořit**.

    ![Podrobné informace o integraci připojení](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Po vytvoření připojení, jak je znázorněno v tomto příkladu, vyberte **textu** a **hlavičky** z výstupů požadavku.
   
    ![Vytvoření připojení integrace](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Příklad:

    ![Vyberte textu a hlaviček ze žádosti výstupy](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Podrobnosti decoder AS2

Konektor dekódovat AS2 provádí tyto úlohy: 

* Zpracuje hlavičky AS2/HTTP
* Ověří podpis (Pokud je nakonfigurováno)
* Dešifruje zprávy (Pokud je nakonfigurováno)
* Dekomprimuje zprávy (Pokud je nakonfigurováno)
* Sloučí přijaté MDN s původní odchozí zprávy
* Aktualizace a záznamy v databázi nepopiratelnosti korelaci
* Zapíše záznamy pro generování sestav o stavu AS2
* Výstupní datové části obsahu jsou kódováním base64
* Určuje, jestli MDN je nutné a jestli MDN by měla být synchronní nebo asynchronní na základě konfigurace smlouvy AS2
* Generuje synchronní nebo asynchronní MDN (podle konfigurace smlouvy)
* Nastaví na MDN korelace tokeny a vlastnosti

## <a name="try-this-sample"></a>Zkuste tuto ukázku

Pokud chcete vyzkoušet nasazení plně funkční logiku aplikace a ukázkový scénář AS2, najdete v článku [AS2 šablona aplikace logiky a scénář](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Zobrazení swagger
Najdete v článku [swagger podrobnosti](/connectors/as2/). 

## <a name="next-steps"></a>Další postup
[Další informace o Enterprise integračního balíčku](logic-apps-enterprise-integration-overview.md) 

