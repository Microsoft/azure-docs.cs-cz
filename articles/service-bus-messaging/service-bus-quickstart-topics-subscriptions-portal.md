---
title: Vytvoření témat a předplatných služby Service Bus pomocí portálu Azure
description: 'Úvodní příručka: V tomto rychlém startu se dozvíte, jak vytvořit téma služby Service Bus a odběry tohoto tématu pomocí portálu Azure.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: a1e330a62a58daaa3fb2a2e8758d14791a3208c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76260816"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Úvodní příručka: Pomocí portálu Azure vytvořte téma služby Service Bus a odběry tématu.
V tomto rychlém startu použijete portál Azure k vytvoření tématu služby Service Bus a potom k vytvoření předplatných tohoto tématu. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co jsou témata a předplatné služby Service Bus?
Témata a předplatné služby Service Bus podporují komunikační model zasílání zpráv *publikování/přihlášení*. Součásti distribuované aplikace při používání témat a předplatných nekomunikují navzájem přímo. Místo toho si zprávy vyměňují prostřednictvím tématu, které slouží jako zprostředkovatel.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Na rozdíl od front service bus, ve kterém je každá zpráva zpracována jedním příjemcem, témata a odběry poskytují formu komunikace 1:N pomocí vzoru publikování nebo odběru. K jednomu tématu můžete zaregistrovat několik předplatných. Při odeslání zprávy do tématu bude zpráva zpřístupněná všem předplatným, aby ji nezávisle zpracovala. Předplatné tématu se podobá virtuální frontě, která obdrží kopii zpráv, které byly odeslány do tématu. Volitelně můžete zaregistrovat pravidla filtru pro téma na základě předplatného, což umožňuje filtrovat nebo omezit, které zprávy na téma jsou přijímány podle tématu odběry.

Témata a předplatná služby Service Bus umožňují škálovat pro zpracování velkého počtu zpráv napříč velkým počtem uživatelů a aplikací.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Informace o tom, jak odesílat zprávy k tématu a přijímat tyto zprávy prostřednictvím předplatného, naleznete v následujícím článku: vyberte programovací jazyk v tématu. 

> [!div class="nextstepaction"]
> [Publikování a přihlášení k odběru zpráv](service-bus-dotnet-how-to-use-topics-subscriptions.md)
