---
title: Zřízení samoobslužné brány v Azure API Management | Microsoft Docs
description: Přečtěte si, jak zřídit bránu pro místní hostování v Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74075293"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Zřízení samoobslužné brány v Azure API Management

Zřízení prostředku brány v instanci Azure API Management je předpokladem pro nasazení samoobslužné brány. Tento článek vás provede kroky k zřízení prostředku brány v API Management.

> [!NOTE]
> Funkce brány pro samoobslužné hostování je ve verzi Preview. V rámci verze Preview je brána v místním prostředí dostupná jenom pro vývojáře a úrovně Premium bez dalších poplatků. Úroveň pro vývojáře je omezená na jediné nasazení samoobslužné brány.

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Zřízení brány v místním prostředí

1. V části **Nastavení**vyberte **brány** .
2. Klikněte na tlačítko **+ Přidat**.
3. Zadejte **název** a **oblast** brány.
> [!TIP]
> **Oblast** určuje zamýšlené umístění uzlů brány, které budou přidruženy k tomuto prostředku brány. Je sémanticky ekvivalentní s podobnou vlastností přidruženou k libovolnému prostředku Azure, ale může jim být přiřazena libovolná hodnota řetězce.

4. Volitelně zadejte **Popis** prostředku brány.
5. Volitelně můžete vybrat **+** v části **rozhraní API** k přidružení jednoho nebo více rozhraní API k tomuto prostředku brány.
> [!TIP]
> Rozhraní API můžete přidružit a odebrat z brány na kartě **Nastavení** rozhraní API.

> [!IMPORTANT]
> Ve výchozím nastavení se k novému prostředku brány nepřidruží žádná existující rozhraní API. Proto se pokusy o jejich vyvolání přes novou bránu budou poznamenat `404 Resource Not Found` odpovědi.

6. Klikněte na tlačítko **Add** (Přidat).

V instanci API Management byl nyní prostředek brány zřízen. Můžete pokračovat v nasazení brány.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md) .
* Další informace o [nasazení samoobslužné brány do Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Další informace o [nasazení samoobslužné brány do Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
