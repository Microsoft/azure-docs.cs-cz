---
title: zahrnout soubor
description: zahrnout soubor
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 0a228f6d8b4f72acd3783f27bf192fe6bd13f988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392144"
---
## <a name="create-an-azure-signalr-service-instance"></a>Vytvoření instance služby Azure SignalR

Vaše aplikace se připojí k instanci služby SignalR Service v Azure.

1. Vyberte tlačítko Nový v levém horním rohu portálu Azure Portal. Do vyhledávacího pole na obrazovce Nový zadejte *SignalR Service* a stiskněte klávesu Enter.

    ![Vyhledání služby SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Ve výsledcích hledání vyberte **SignalR Service** a pak vyberte **Vytvořit**.

1. Zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název prostředku** | Globálně jedinečný název | Název, který identifikuje novou instanci služby SignalR Service. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se nová instance služby SignalR Service vytvoří. | 
    | **[Skupina prostředků](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které se má instance služby SignalR Service vytvořit. | 
    | **Umístění** | Západní USA | Zvolte [oblast](https://azure.microsoft.com/regions/) ve vaší blízkosti. |
    | **Cenová úroveň** | Zadarmo | Vyzkoušejte si službu Azure SignalR Service zdarma. |
    | **Počet jednotek** |  Nevztahuje se | Počet jednotek určuje, kolik připojení může instance služby SignalR Service přijmout. To lze nakonfigurovat jen na úrovni Standard. |
    | **Režim služby** |  Řešení bez serverů | Pro použití s Azure Functions nebo REST API. |

    ![Vytvoření služby SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Pokud chcete začít nasazovat instanci služby SignalR Service, vyberte **Vytvořit**.

1. Po nasazení instance ho otevřete na portálu a najděte jeho stránku nastavení. Nastavte režim služby na možnost bez *serveru* jenom v případě, že používáte službu Azure Signal service prostřednictvím Azure Functions vazby nebo REST API. V opačném případě je ponechte v *klasickém* nebo *výchozím nastavení* .