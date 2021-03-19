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
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: e79248e16ca21ae84022f8ac7f280d93f489c6a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87050354"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Zřízení samoobslužné brány v Azure API Management

Zřízení prostředku brány v instanci Azure API Management je předpokladem pro nasazení samoobslužné brány. Tento článek vás provede kroky k zřízení prostředku brány v API Management.

## <a name="prerequisites"></a>Předpoklady

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Zřízení brány v místním prostředí

1. Vyberte **brány** z části **nasazení a infrastruktura**.
2. Klikněte na **+ Přidat**.
3. Zadejte **název** a **oblast** brány.
> [!TIP]
> **Oblast** určuje zamýšlené umístění uzlů brány, které budou přidruženy k tomuto prostředku brány. Je sémanticky ekvivalentní s podobnou vlastností přidruženou k libovolnému prostředku Azure, ale může jim být přiřazena libovolná hodnota řetězce.

4. Volitelně zadejte **Popis** prostředku brány.
5. Volitelně můžete vybrat **+** v části **rozhraní API** k přidružení jednoho nebo více rozhraní API k tomuto prostředku brány.
> [!IMPORTANT]
> Ve výchozím nastavení se k novému prostředku brány nepřidruží žádná existující rozhraní API. Proto se pokusy o jejich vyvolání přes novou bránu budou poznamenat `404 Resource Not Found` odpovědi.

6. Klikněte na **Přidat**.

V instanci API Management byl nyní prostředek brány zřízen. Můžete pokračovat v nasazení brány.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md) .
* Další informace o [nasazení samoobslužné brány do Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* Další informace o [nasazení samoobslužné brány do Docker](how-to-deploy-self-hosted-gateway-docker.md)
