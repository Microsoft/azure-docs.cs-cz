---
title: Použití externí mezipaměti ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat a používat externí mezipaměť ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: 2e8863eed774884a99de8643c9e497378368d166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072491"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Použití externí služby Azure Cache for Redis ve službě Azure API Management

Kromě využití integrované mezipaměti umožňuje Správa rozhraní Azure API také odpovědi na ukládání do mezipaměti v externí mezipaměti Azure pro Redis.

Použití externí mezipaměti umožňuje překonat několik omezení vestavěné mezipaměti. To je zvláště výhodné, pokud byste chtěli:

* Během aktualizací správy rozhraní API se vyhnete pravidelnému vymazání mezipaměti.
* Mít větší kontrolu nad konfigurací mezipaměti
* Ukládat do mezipaměti více dat, než umožňuje vaše úroveň správy rozhraní API
* Použití ukládání do mezipaměti s úrovní spotřeby správy rozhraní API

Podrobnější informace o ukládání do mezipaměti najdete v popisu [zásad ukládání do mezipaměti API Management](api-management-caching-policies.md) a [vlastního ukládání do mezipaměti Azure API Management](api-management-sample-cache-by-key.md).

![Přineste si vlastní cache apim](media/api-management-howto-cache-external/overview.png)

Naučíte se:

> [!div class="checklist"]
> * Přidání externí mezipaměti ve správě rozhraní API

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

+ [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)
+ Principy [ukládání do mezipaměti ve správě rozhraní Azure API](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Vytvoření mezipaměti Azure pro Redis

Tato část vysvětluje, jak vytvořit azure cache pro Redis v Azure. Pokud už máte Azure Cache pro Redis, v rámci nebo mimo Azure, můžete <a href="#add-external-cache">přeskočit</a> na další část.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Přidání externí mezipaměti

Podle následujících kroků přidejte externí mezipaměť Azure pro Redis ve správě rozhraní Azure API.

![Přineste si vlastní cache apim](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Nastavení **Use from** určuje, které regionální nasazení správy rozhraní API bude komunikovat s nakonfigurovanou mezipamětí v případě víceregionální konfigurace správy rozhraní API. Mezipaměti zadané jako **Výchozí** budou přepsány mezipamětí s místní hodnotou.
>
> Pokud je například správa rozhraní API hostována v oblastech Východní USA, Jihovýchodní Asie a Západní Evropa a jsou nakonfigurovány dvě mezipaměti, jedna pro **výchozí** a jedna pro **jihovýchodní Asii**, bude správa rozhraní API v **jihovýchodní Asii** používat vlastní mezipaměť, zatímco ostatní dvě oblasti budou používat **výchozí** položku mezipaměti.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Přidání azure cache pro Redis ze stejného předplatného

1. Přejděte na instanci správy rozhraní API na webu Azure Portal.
2. V nabídce vlevo vyberte kartu **Externí mezipaměť.**
3. Klikněte na tlačítko **+Přidat**.
4. Vyberte mezipaměť v rozevíracím poli **instance mezipaměti.**
5. V rozevíracím poli **Vyberte** **Výchozí** nebo určete požadovanou oblast.
6. Klikněte na **Uložit**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Přidání mezipaměti Azure pro Redis hostované mimo aktuální předplatné Azure nebo Azure obecně

1. Přejděte na instanci správy rozhraní API na webu Azure Portal.
2. V nabídce vlevo vyberte kartu **Externí mezipaměť.**
3. Klikněte na tlačítko **+Přidat**.
4. V rozevíracím poli **instance mezipaměti** vyberte **Vlastní.**
5. V rozevíracím poli **Vyberte** **Výchozí** nebo určete požadovanou oblast.
6. Zadejte v poli **Připojovací řetězec** připojovací řetězec azure cache pro redis.
7. Klikněte na **Uložit**.

## <a name="use-the-external-cache"></a>Použití externí mezipaměti

Jakmile je externí mezipaměť nakonfigurovaná ve správě rozhraní Azure API, dá se použít prostřednictvím zásad ukládání do mezipaměti. Podrobné kroky najdete [v tématu Přidání ukládání do mezipaměti pro zlepšení výkonu ve správě rozhraní Azure API.](api-management-howto-cache.md)

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky

* Další informace o zásadách ukládání do mezipaměti najdete v části [Zásady ukládání do mezipaměti][Caching policies] v článku [Zásady API Managementu][API Management policy reference].
* Informace o ukládání položek do mezipaměti podle klíče pomocí výrazů zásad najdete v článku [Vlastní ukládání do mezipaměti ve službě Azure API Management](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
