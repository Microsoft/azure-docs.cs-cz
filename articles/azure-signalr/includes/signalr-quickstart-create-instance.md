---
title: zahrnout soubor
description: zahrnout soubor
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: ac77f0b6a1d90fd78db64618c1e03ade198a67c0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882164"
---
## <a name="create-an-azure-signalr-service-instance"></a>Vytvoření instance služby Azure SignalR Service

Vaše aplikace se připojí k instanci služby SignalR Service v Azure.

1. Vyberte tlačítko Nový v levém horním rohu portálu Azure Portal. Do vyhledávacího pole na obrazovce Nový zadejte *SignalR Service* a stiskněte klávesu Enter.

    ![Vyhledání služby SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Ve výsledcích hledání vyberte **SignalR Service** a pak vyberte **Vytvořit**.

1. Zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název prostředku** | Globálně jedinečný název | Název, který identifikuje novou instanci služby SignalR Service. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se nová instance služby SignalR Service vytvoří. | 
    | **[Skupina prostředků](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které se má instance služby SignalR Service vytvořit. | 
    | **Location** | USA – západ | Zvolte [oblast](https://azure.microsoft.com/regions/) ve vaší blízkosti. |
    | **Cenová úroveň** | Zdarma | Vyzkoušejte si službu Azure SignalR Service zdarma. |
    | **Počet jednotek** |  Nelze použít | Počet jednotek určuje, kolik připojení může instance služby SignalR Service přijmout. To lze nakonfigurovat jen na úrovni Standard. |
    | **Režim služby** |  Bezserverová | Pro použití s Azure Functions nebo REST API. |

    ![Vytvoření služby SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Pokud chcete začít nasazovat instanci služby SignalR Service, vyberte **Vytvořit**.

1. Po nasazení instance ho otevřete na portálu a najděte jeho stránku nastavení. Nastavte režim služby na možnost bez *serveru* jenom v případě, že používáte službu Azure Signal service prostřednictvím Azure Functions vazby nebo REST API. V opačném případě je ponechte v klasickém nebo *výchozím nastavení* .