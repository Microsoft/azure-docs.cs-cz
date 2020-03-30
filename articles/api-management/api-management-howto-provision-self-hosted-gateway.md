---
title: Zřízení brány s vlastním hostitelem ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si, jak zřídit samoobslužnou bránu ve správě rozhraní Azure API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075293"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Zřízení brány s vlastním hostitelem ve správě Rozhraní API Azure

Zřizování prostředku brány v instanci Azure API Management je předpokladem pro nasazení brány hostované sami. Tento článek vás provede kroky k zřízení prostředku brány ve správě rozhraní API.

> [!NOTE]
> Funkce brány s vlastním hostitelem je ve verzi Preview. Během předběžné verze je brána s vlastním hostitelem dostupná pouze na úrovních Vývojář a Premium bez dalších poplatků. Úroveň pro vývojáře je omezena na jedno nasazení brány hostované sami sebou.

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Zřízení brány v místním prostředí

1. V části **Nastavení vyberte brány** v části **Nastavení**.
2. Klikněte na tlačítko **+ Přidat**.
3. Zadejte **název** a **oblast** brány.
> [!TIP]
> **Oblast** určuje zamýšlené umístění uzlů brány, které budou přidruženy k tomuto prostředku brány. Je sémanticky ekvivalentní podobné vlastnosti přidružené k jakékoli prostředky Azure, ale může být přiřazena libovolná hodnota řetězce.

4. Volitelně zadejte **popis** prostředku brány.
5. Volitelně můžete **+** v části **API** přidružit k tomuto prostředku brány jedno nebo více api.
> [!TIP]
> Rozhraní API můžete přidružit a odebrat z brány na kartě **Nastavení** rozhraní API.

> [!IMPORTANT]
> Ve výchozím nastavení nebude žádná z existujících api přidružena k novému prostředku brány. Proto pokusy o jejich vyvolání prostřednictvím `404 Resource Not Found` nové brány bude mít za následek odpovědi.

6. Klikněte na **Přidat**.

Teď byl prostředek brány zřízen v instanci správy rozhraní API. Můžete pokračovat k nasazení brány.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v [tématu Přehled samoobslužné brány správy rozhraní Azure API.](self-hosted-gateway-overview.md)
* Další informace o nasazení brány s [vlastním hostitelem do Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Další informace o nasazení brány s [vlastním hostitelem do Dockeru](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
