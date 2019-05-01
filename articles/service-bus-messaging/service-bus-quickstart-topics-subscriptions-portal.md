---
title: Rychlý start – použití webu Azure portal k vytvoření témata a odběry Service Bus | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak vytvořit téma služby Service Bus a odběrů tématu pomocí webu Azure portal.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: e943dbff71fdbd98773587c8ac5a62dd081b49a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698867"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Rychlý start: Vytvoření tématu služby Service Bus a odběrů tématu pomocí webu Azure portal
V tomto rychlém startu použijete Azure portal k vytvoření tématu služby Service Bus a pak vytvořte odběry tohoto tématu. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co jsou témata a předplatné služby Service Bus?
Témata a předplatné služby Service Bus podporují komunikační model zasílání zpráv *publikování/přihlášení*. Součásti distribuované aplikace při používání témat a předplatných nekomunikují navzájem přímo. Místo toho si zprávy vyměňují prostřednictvím tématu, které slouží jako zprostředkovatel.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Rozdíl od front služby Service Bus, ve kterých každou zprávu zpracuje jeden spotřebitel, témata a předplatné nabízejí jeden mnoho forma komunikace pomocí vzorec publikovat/odebírat. K jednomu tématu můžete zaregistrovat několik předplatných. Při odeslání zprávy do tématu bude zpráva zpřístupněná všem předplatným, aby ji nezávisle zpracovala. Předplatné tématu se podobá virtuální frontě, která obdrží kopii zpráv, které byly odeslány do tématu. Máte taky možnost zaregistrovat pravidla filtru pro téma na základě předplatných, která vám umožní filtrovat nebo omezit přijímaných zpráv pro téma podle předplatných tématu.

Témata a odběry Service Bus umožňují škálovat pro zpracování velkého počtu zpráv ve velkém počtu uživatelů a aplikací.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="next-steps"></a>Další postup
Zjistěte, jak odesílat zprávy do tématu a příjem těchto zpráv prostřednictvím předplatného, najdete v následujícím článku: vyberte programovací jazyk v obsahu. 

> [!div class="nextstepaction"]
> [Publikování a odběr zpráv](service-bus-dotnet-how-to-use-topics-subscriptions.md)
