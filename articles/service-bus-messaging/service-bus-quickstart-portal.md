---
title: Vytvoření fronty Service Bus pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak vytvořit obor názvů Service Bus a frontu v oboru názvů pomocí Azure Portal.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 79dd751c43443790aafc494d89ad45e3b6705a64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95799216"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Vytvoření oboru názvů Service Bus a fronty pomocí Azure Portal
V tomto rychlém startu se dozvíte, jak vytvořit obor názvů Service Bus a frontu pomocí [Azure Portal][Azure portal]. Také se dozvíte, jak získat autorizační přihlašovací údaje, které může klientská aplikace použít k odesílání a přijímání zpráv do fronty. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu se ujistěte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][] před tím, než začnete.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Další kroky
V tomto článku jste v oboru názvů vytvořili obor názvů Service Bus a frontu. Informace o tom, jak odesílat a přijímat zprávy z fronty, najdete v části v tématu **posílání a přijímání zpráv** v jednom z následujících rychlých startů. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
