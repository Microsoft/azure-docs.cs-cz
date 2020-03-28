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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175243"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Vytvoření tématu pomocí webu Azure Portal
1. Na stránce **Obor názvů sběrnice** vyberte **témata** v levé nabídce.
2. Na panelu nástrojů vyberte **+ Téma.** 
4. Zadejte **název** tématu. U ostatních možností ponechte jejich výchozí hodnoty.
5. Vyberte **Vytvořit**.

    ![Vytvořit téma](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Vytvoření předplatných tématu
1. Vyberte **téma,** které jste vytvořili v předchozí části. 
    
    ![Vybrat téma](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Na stránce **Téma služby Service Bus** vyberte **odběry** v levé nabídce a pak na panelu nástrojů vyberte **+ Odběr.** 
    
    ![Tlačítko Přidat předplatné](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Na stránce **Vytvořit odběr** zadejte pro **název** předplatného **S1** a pak vyberte **Vytvořit**. 

    ![Vytvořit stránku předplatného](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Opakujte předchozí krok dvakrát vytvořit odběry s názvem **S2** a **S3**.