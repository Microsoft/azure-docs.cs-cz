---
title: zahrnout soubor
description: zahrnout soubor
services: signalr
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 97b8ef5e260a853ecdffb3c502f8a5051dd0e143
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006213"
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
    | **Umístění** | USA – západ | Zvolte [oblast](https://azure.microsoft.com/regions/) ve vaší blízkosti. |
    | **Cenová úroveň** | Free | Vyzkoušejte si službu Azure SignalR Service zdarma. |
    | **Počet jednotek** |  Neuvedeno | Počet jednotek určuje, kolik připojení může instance služby SignalR Service přijmout. To lze nakonfigurovat jen na úrovni Standard. |

    ![Vytvoření služby SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Pokud chcete začít nasazovat instanci služby SignalR Service, vyberte **Vytvořit**.
