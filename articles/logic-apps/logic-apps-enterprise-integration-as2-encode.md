---
title: Kódování zprávy AS2 – Azure Logic Apps | Microsoft Docs
description: Jak používat kodér AS2 v podniku integrační balíček pro Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: fe8a2b00f15fa737c8ed343a47e1cab1c260346b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297926"
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Kódování zprávy AS2 pro Azure Logic Apps s Enterprise integračního balíčku

K navázání zabezpečení a spolehlivost při přenosu zprávy, pomocí konektoru AS2 kódování zprávy. Tento konektor poskytuje digitální podpis, šifrování a potvrzení prostřednictvím zpráv dispozice oznámení (MDN), což také vede k podpoře pro Non-Odvolatelnost.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure; můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure. Musí mít účet integrace k používání konektoru AS2 kódování zprávy.
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , jsou již definováni ve vašem účtu integrace
* [Smlouvy AS2](logic-apps-enterprise-integration-as2.md) , již je definována v účtu integrace

## <a name="encode-as2-messages"></a>Kódování zprávy AS2

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor AS2 kódování zprávy nemá aktivačních událostí, proto musíte přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V návrháři aplikace logiky přidejte aktivační událost a potom přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "AS2" filtru. Vyberte **AS2 - AS2 kódování zpráv**.
   
    ![Vyhledejte "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Pokud jste nevytvořili dříve všechna připojení k vašemu účtu integrace, se zobrazí výzva k vytvoření připojení nyní. Název připojení a vyberte integrační účet, který chcete připojit. 
   
    ![Vytvoření připojení k účtu integrace](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název pro připojení. |
    | Integrace účet * |Zadejte název pro váš účet integrace. Ujistěte se, že integrace účet a logiku aplikace jsou ve stejné oblasti Azure. |

5.  Když jste hotovi, by měla vypadat podobně jako tento příklad podrobné informace o připojení. Dokončete vytváření připojení, zvolte **vytvořit**.
   
    ![Podrobné informace o integraci připojení](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Po vytvoření připojení, jak je znázorněno v tomto příkladu, zadejte podrobnosti pro **AS2-z**, **AS2-na identifikátory** podle konfigurace v smlouvě, a **textu**, který je datovou část zprávy.
   
    ![Zadejte povinné pole](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Podrobnosti kodér AS2

Konektor kódování AS2 provádí tyto úlohy: 

* Platí hlavičky AS2/HTTP
* Přihlásí odchozí zprávy (Pokud je nakonfigurováno)
* Šifruje odchozích zpráv (Pokud je nakonfigurováno)
* Komprimuje zprávy (Pokud je nakonfigurováno)

## <a name="try-this-sample"></a>Zkuste tuto ukázku

Pokud chcete vyzkoušet nasazení plně funkční logiku aplikace a ukázkový scénář AS2, najdete v článku [AS2 šablona aplikace logiky a scénář](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Zobrazení swagger
Najdete v článku [swagger podrobnosti](/connectors/as2/). 

## <a name="next-steps"></a>Další postup
[Další informace o integračního balíčku Enterprise](logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku") 

