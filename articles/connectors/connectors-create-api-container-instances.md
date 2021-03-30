---
title: Nasazení & Správa Azure Container Instances
description: Automatizace úloh a pracovních postupů, které vytvářejí a spravují nasazení kontejnerů v Azure Container Instances pomocí Azure Logic Apps
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "76046289"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Nasazení a Správa Azure Container Instances pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru instance kontejnerů Azure můžete nastavit automatizované úlohy a pracovní postupy, které nasazují a spravují [skupiny kontejnerů](../container-instances/container-instances-container-groups.md). Konektor instance kontejneru podporuje následující akce:

* Vytvoření nebo odstranění skupiny kontejnerů
* Získá vlastnosti skupiny kontejnerů.
* Získá seznam skupin kontejnerů.
* Získání protokolů instance kontejneru

Tyto akce můžete použít ve svých aplikacích logiky pro úlohy, jako je spuštění úlohy kontejneru v reakci na Trigger Logic Apps. Můžete také použít jiné akce výstup z akcí instance kontejneru. 

Tento konektor nabízí jenom akce, takže pokud chcete spustit aplikaci logiky, použijte samostatnou triggerovou proceduru, jako je například Trigger **opakování** , a spusťte úlohu kontejneru podle pravidelného plánu. Nebo je možné, že budete muset aktivovat nasazení skupiny kontejnerů po určité události, jako je například doručení e-mailu Outlooku. 

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) a [jak vytvářet a spravovat instance kontejnerů](../container-instances/container-instances-quickstart.md)

* Aplikace logiky, ke které chcete získat přístup k instancím kontejneru. Pokud chcete použít akci, spusťte aplikaci logiky s jinou triggerem, například triggerem **opakování** .

## <a name="add-a-container-instance-action"></a>Přidat akci instance kontejneru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

   * V posledním kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 

     -nebo-

   * Mezi kroky, do kterých chcete přidat akci, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
   Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "instance kontejneru". V seznamu akce vyberte akci konektoru služby Azure Container instance, kterou chcete.

1. Zadejte název připojení. 

1. Zadejte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

  Vyberte například **vytvořit skupinu kontejnerů** a zadejte vlastnosti pro skupinu kontejnerů a jednu nebo více instancí kontejnerů ve skupině, jak je znázorněno na následujícím obrázku (částečný detail):

  ![Vytvoření skupiny kontejnerů](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete v referenční [stránce](/connectors/aci/) konektoru nebo v odkazu na skupinu kontejnerů [YAML](../container-instances/container-instances-reference-yaml.md).

## <a name="next-steps"></a>Další kroky

* Podívejte se na [ukázkovou aplikaci logiky](https://github.com/Azure-Samples/aci-logicapps-integration) , která spouští kontejner v Azure Container Instances k analýze mínění e-mailu nebo textu Twitteru.

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)