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
ms.openlocfilehash: ee9594e7aa3b55f7eb6fc7ba41783708555bd15e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513767"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Zřízení samoobslužné brány v Azure API Management

Zřízení prostředku brány v instanci Azure API Management je předpokladem pro nasazení samoobslužné brány. Tento článek vás provede kroky k zřízení prostředku brány v API Management.

> [!NOTE]
> Funkce brány pro samoobslužné hostování je ve verzi Preview. V rámci verze Preview je brána v místním prostředí dostupná jenom pro vývojáře a úrovně Premium bez dalších poplatků. Úroveň pro vývojáře je omezená na jediné nasazení samoobslužné brány.

## <a name="prerequisites"></a>Předpoklady

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Zřídit samoobslužnou bránu

1. V části **Nastavení**vyberte **brány** .
2. Klikněte na tlačítko **+ Přidat**.
3. Zadejte **název** a **oblast** brány.
> [!TIP]
> **Oblast** určuje zamýšlené umístění uzlů brány, které budou přidruženy k tomuto prostředku brány. Je sémanticky ekvivalentní s podobnou vlastností přidruženou k libovolnému prostředku Azure, ale může jim být přiřazena libovolná hodnota řetězce.
4. Volitelně zadejte **Popis** prostředku brány.
5. V případě potřeby můžete v části **rozhraní API** vybrat **+** k přidružení jednoho nebo více rozhraní API k tomuto prostředku brány.
> [!TIP]
> Rozhraní API můžete přidružit a odebrat z brány na kartě **Nastavení** rozhraní API.

> [!IMPORTANT]
> Ve výchozím nastavení se k novému prostředku brány nepřidruží žádná existující rozhraní API. Proto se pokusy o jejich vyvolání přes novou bránu povedou `404 Resource Not Found` odezvy.
6. Klikněte na tlačítko **Přidat**.

V instanci API Management byl nyní prostředek brány zřízen. Můžete pokračovat v nasazení brány.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md) .
* Další informace o [nasazení samoobslužné brány do Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Další informace o [nasazení samoobslužné brány do Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
