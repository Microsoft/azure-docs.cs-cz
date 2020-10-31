---
title: Použití externí mezipaměti v Azure API Management | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat a používat externí mezipaměť v Azure API Management. Použití externí mezipaměti vám umožní překonat některá omezení integrované mezipaměti.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095930"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Použití externí mezipaměti kompatibilní s Redis v Azure API Management

Kromě používání integrované mezipaměti umožňuje Azure API Management ukládat odpovědi do mezipaměti v externí mezipaměti kompatibilní s Redis, třeba Azure cache pro Redis.

Použití externí mezipaměti vám umožní překonat několik omezení integrované mezipaměti:

* Vyhněte se pravidelnému vymazání mezipaměti během API Management aktualizací
* Větší kontrola nad konfigurací mezipaměti
* Ukládání více dat do mezipaměti, než jakou vaše API Management úrovně umožňuje
* Použití mezipaměti s úrovní spotřeby API Management
* Povolení ukládání do mezipaměti v [API Management branách pro samoobslužné hostování](self-hosted-gateway-overview.md)

Podrobnější informace o ukládání do mezipaměti najdete v popisu [zásad ukládání do mezipaměti API Management](api-management-caching-policies.md) a [vlastního ukládání do mezipaměti Azure API Management](api-management-sample-cache-by-key.md).

![Přineste si vlastní mezipaměť do APIM](media/api-management-howto-cache-external/overview.png)

Naučíte se:

> [!div class="checklist"]
> * Přidat externí mezipaměť v API Management

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu je potřeba provést následující:

+ [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)
+ Vysvětlení [ukládání do mezipaměti v Azure API Management](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Vytvoření mezipaměti Azure pro Redis

V této části se dozvíte, jak vytvořit mezipaměť Azure pro Redis v Azure. Pokud už máte mezipaměť Azure pro Redis, v rámci nebo mimo Azure můžete <a href="#add-external-cache">Přejít</a> k další části.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"> </a> Nasazení Redis Cache do Kubernetes

Pro ukládání do mezipaměti jsou brány v místním prostředí závislé výhradně na externích mezipamětech. Aby bylo možné ukládat do mezipaměti efektivní brány pro místní hostování a mezipaměť, na které spoléhají, musí být umístěna blízko sebe, aby se minimalizovala latence vyhledávání a ukládání. Nejlepší možností je nasadit mezipaměť Redis do stejného clusteru Kubernetes nebo v blízkosti samostatného clusteru. Pomocí tohoto [odkazu](https://github.com/kubernetes/examples/tree/master/guestbook) se dozvíte, jak nasadit Redis Cache do clusteru Kubernetes.

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Přidat externí mezipaměť

Pomocí následujících kroků přidejte externí mezipaměť Azure pro Redis do Azure API Management.

![Snímek obrazovky, který ukazuje, jak přidat externí mezipaměť Azure pro Redis do Azure API Management.](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Nastavení **použít z** určuje oblast Azure nebo umístění brány v místním prostředí, které bude používat nakonfigurovanou mezipaměť. Mezipaměti nakonfigurované jako **výchozí** budou přepsány mezipamětí s určitou platnou oblastí nebo hodnotou umístění.
>
> Pokud se například API Management hostuje v oblastech Východní USA, jihovýchodní Asie a Západní Evropa a jsou nakonfigurované dvě mezipaměti, jeden pro **výchozí** a druhý pro **jihovýchodní Asie** , API Management v **jihovýchodní Asie** bude používat vlastní mezipaměť, zatímco ostatní dvě oblasti použijí **výchozí** položku mezipaměti.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Přidání mezipaměti Azure pro Redis ze stejného předplatného

1. V Azure Portal přejděte k instanci API Management.
2. V nabídce na levé straně vyberte kartu **externí mezipaměť** .
3. Klikněte na tlačítko **+Přidat** .
4. V rozevíracím poli **instance mezipaměti** vyberte mezipaměť.
5. Vyberte **výchozí** nebo zadejte požadovanou oblast do rozevíracího pole **použít z** .
6. Klikněte na **Uložit** .

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Přidání mezipaměti Azure pro Redis hostované mimo aktuální předplatné Azure nebo obecně Azure

1. V Azure Portal přejděte k instanci API Management.
2. V nabídce na levé straně vyberte kartu **externí mezipaměť** .
3. Klikněte na tlačítko **+Přidat** .
4. V rozevíracím poli **instance mezipaměti** vyberte **vlastní** .
5. Vyberte **výchozí** nebo zadejte požadovanou oblast do rozevíracího pole **použít z** .
6. Zadejte do pole **připojovací řetězec** svou mezipaměť Azure pro připojovací řetězec Redis.
7. Klikněte na **Uložit** .

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>Přidání mezipaměti Redis do samoobslužné brány

1. V Azure Portal přejděte k instanci API Management.
2. V nabídce na levé straně vyberte kartu **externí mezipaměť** .
3. Klikněte na tlačítko **+Přidat** .
4. V rozevíracím poli **instance mezipaměti** vyberte **vlastní** .
5. Zadejte požadované umístění místní hostované brány nebo **výchozí** v rozevíracím poli **použít z** .
6. Zadejte připojovací řetězec Redis Cache do pole **připojovací řetězec** .
7. Klikněte na **Uložit** .

## <a name="use-the-external-cache"></a>Použít externí mezipaměť

Jakmile je externí mezipaměť nakonfigurovaná v Azure API Management, dá se použít prostřednictvím zásad ukládání do mezipaměti. Podrobné pokyny najdete v tématu [Přidání ukládání do mezipaměti pro zlepšení výkonu v Azure API Management](api-management-howto-cache.md) .

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky

* Další informace o zásadách ukládání do mezipaměti najdete v části [Zásady ukládání do mezipaměti][Caching policies] v článku [Zásady API Managementu][API Management policy reference].
* Informace o ukládání položek do mezipaměti podle klíče pomocí výrazů zásad najdete v článku [Vlastní ukládání do mezipaměti ve službě Azure API Management](api-management-sample-cache-by-key.md).

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
