---
title: Nasazení & spravovat instance kontejnerů Azure
description: Automatizace úloh a pracovních postupů, které vytvářejí a spravují nasazení kontejnerů v instanci kontejnerů Azure pomocí Azure Logic Apps
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046289"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Nasazení a správa instancí kontejnerů Azure pomocí Azure Logic Apps

Pomocí Aplikací Logiky Azure a konektoru Azure Container Instance můžete nastavit automatizované úlohy a pracovní postupy, které nasazují a spravují [skupiny kontejnerů](../container-instances/container-instances-container-groups.md). Konektor instance kontejneru podporuje následující akce:

* Vytvoření nebo odstranění skupiny kontejnerů
* Získání vlastností skupiny kontejnerů
* Získání seznamu skupin kontejnerů
* Získání protokolů instance kontejneru

Tyto akce použijte ve svých aplikacích logiky pro úlohy, jako je spuštění úlohy kontejneru v reakci na aktivační událost logic Apps. Můžete také mít jiné akce použít výstup z akce instance kontejneru. 

Tento konektor poskytuje pouze akce, takže ke spuštění aplikace logiky, použijte samostatnou aktivační událost, jako je například aktivační **událost opakování** ke spuštění úlohy kontejneru v pravidelných intervalech. Nebo budete muset aktivovat nasazení skupiny kontejnerů po události, jako je například doručení e-mailu aplikace Outlook. 

Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) a [jak vytvářet a spravovat instance kontejneru](../container-instances/container-instances-quickstart.md)

* Aplikace logiky, kde chcete získat přístup k instancí kontejneru. Chcete-li použít akci, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **opakování.**

## <a name="add-a-container-instance-action"></a>Přidání akce Instance kontejneru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

   * V posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. 

     -nebo-

   * Mezi kroky, kam chcete přidat akci, přesuňte ukazatel myši na šipku mezi kroky. 
   Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Do vyhledávacího pole zadejte jako filtr "instanci kontejneru". V seznamu akcí vyberte akci konektoru instance Azure Container Instance, kterou chcete.

1. Zadejte název připojení. 

1. Poskytněte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

  Vyberte například **Vytvořit skupinu kontejnerů** a zadejte vlastnosti pro skupinu kontejnerů a jednu nebo více instancí kontejneru ve skupině, jak je znázorněno na následujícím obrázku (částečný detail):

  ![Vytvoření skupiny kontejnerů](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, zkontrolujte [referenční stránku](/connectors/aci/) konektoru nebo odkaz skupiny kontejnerů [YAML](../container-instances/container-instances-reference-yaml.md).

## <a name="next-steps"></a>Další kroky

* Podívejte se na [ukázkovou aplikaci logiky,](https://github.com/Azure-Samples/aci-logicapps-integration) která spouští kontejner v instanci kontejneru Azure a analyzuje mínění e-mailu nebo textu na Twitteru.

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)