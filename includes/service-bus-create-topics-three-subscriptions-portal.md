---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60333430"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Vytvoření tématu pomocí webu Azure Portal
1. Na **služby Service Bus Namespace** stránce **témata** v nabídce vlevo.
2. Vyberte **+ téma** na panelu nástrojů. 
4. Zadejte **název** tématu. U ostatních možností ponechte jejich výchozí hodnoty.
5. Vyberte **Vytvořit**.

    ![Vytvořit téma](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Vytvořit odběry tématu
1. Vyberte **tématu** , kterou jste vytvořili v předchozí části. 
    
    ![Vyberte téma](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Na **téma služby Service Bus** stránce **předplatná** v levé nabídce a pak vyberte **+ odběr** na panelu nástrojů. 
    
    ![Přidejte tlačítko předplatné](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Na **vytvořit odběr** zadejte **S1** pro **název** pro dané předplatné a pak vyberte **vytvořit**. 

    ![Vytvoření odběru stránky](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Opakujte předchozí krok dvakrát pro vytvoření předplatných s názvem **S2** a **S3**.