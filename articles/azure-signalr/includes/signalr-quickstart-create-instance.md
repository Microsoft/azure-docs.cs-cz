---
title: zahrnout soubor
description: zahrnout soubor
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: f738daab7ddcf0403f546e7c9ffeaeccb66bc6b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355635"
---
## <a name="create-an-azure-signalr-service-instance"></a>Vytvoření instance služby Azure SignalR Service

Vaše aplikace se připojí k instanci služby SignalR Service v Azure.

1. Vyberte tlačítko Nový v levém horním rohu portálu Azure Portal. Do vyhledávacího pole na obrazovce Nový zadejte *SignalR Service* a stiskněte klávesu Enter.

    ![Snímek obrazovky ukazuje hledání služby signalizace v Azure Portal.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Ve výsledcích hledání vyberte **SignalR Service** a pak vyberte **Vytvořit**.

1. Zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název prostředku** | Globálně jedinečný název | Název, který identifikuje novou instanci služby SignalR Service. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se nová instance služby SignalR Service vytvoří. | 
    | **[Skupina prostředků](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které se má instance služby SignalR Service vytvořit. | 
    | **Umístění** | USA – západ | Zvolte [oblast](https://azure.microsoft.com/regions/) ve vaší blízkosti. |
    | **Cenová úroveň** | Free | Vyzkoušejte si službu Azure SignalR Service zdarma. |
    | **Počet jednotek** |  Nelze použít | Počet jednotek určuje, kolik připojení může instance služby SignalR Service přijmout. To lze nakonfigurovat jen na úrovni Standard. |
    | **Režim služby** |  Bez serveru | Pro použití s Azure Functions nebo REST API. |

    ![Snímek obrazovky znázorňující kartu základy signálu s hodnotami.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Pokud chcete začít nasazovat instanci služby SignalR Service, vyberte **Vytvořit**.

1. Po nasazení instance ho otevřete na portálu a najděte jeho stránku nastavení. Nastavte režim služby na možnost bez *serveru* jenom v případě, že používáte službu Azure Signal service prostřednictvím Azure Functions vazby nebo REST API. V opačném případě je ponechte v *klasickém* nebo *výchozím nastavení* .