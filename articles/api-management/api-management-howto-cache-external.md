---
title: Použití externí mezipaměti v Azure API Management | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat a používat externí mezipaměť v Azure API Management.
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
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072491"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Použití externí mezipaměti Azure pro Redis v Azure API Management

Kromě používání integrované mezipaměti umožňuje Azure API Management také ukládání odpovědí do mezipaměti v externí mezipaměti Azure pro Redis.

Použití externí mezipaměti umožňuje překonat několik omezení integrované mezipaměti. Je to obzvláště užitečné v případě, že chcete:

* Vyhněte se pravidelnému vymazání mezipaměti během API Management aktualizací
* Větší kontrola nad konfigurací mezipaměti
* Ukládání více dat do mezipaměti, než jakou vaše API Management úrovně umožňuje
* Použití mezipaměti s úrovní spotřeby API Management

Podrobnější informace o ukládání do mezipaměti najdete v popisu [zásad ukládání do mezipaměti API Management](api-management-caching-policies.md) a [vlastního ukládání do mezipaměti Azure API Management](api-management-sample-cache-by-key.md).

![Přineste si vlastní mezipaměť do APIM](media/api-management-howto-cache-external/overview.png)

Naučíte se:

> [!div class="checklist"]
> * Přidat externí mezipaměť v API Management

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

+ [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)
+ Vysvětlení [ukládání do mezipaměti v Azure API Management](api-management-howto-cache.md)

## <a name="create-cache"></a> Vytvoření mezipaměti Azure pro Redis

V této části se dozvíte, jak vytvořit mezipaměť Azure pro Redis v Azure. Pokud už máte mezipaměť Azure pro Redis, v rámci nebo mimo Azure můžete <a href="#add-external-cache">Přejít</a> k další části.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Přidat externí mezipaměť

Pomocí následujících kroků přidejte externí mezipaměť Azure pro Redis do Azure API Management.

![Přineste si vlastní mezipaměť do APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Nastavení **použít z** určuje, které API Management regionální nasazení bude komunikovat s nakonfigurovanou mezipamětí v případě vícenásobné regionální konfigurace API Management. Mezipaměti zadané jako **výchozí** budou přepsány mezipamětí s regionální hodnotou.
>
> Pokud se například API Management hostuje v oblastech Východní USA, jihovýchodní Asie a Západní Evropa a jsou nakonfigurované dvě mezipaměti, jeden pro **výchozí** a druhý pro **jihovýchodní Asie**, API Management v **jihovýchodní Asie** bude používat vlastní mezipaměť, zatímco ostatní dvě oblasti použijí **výchozí** položku mezipaměti.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Přidání mezipaměti Azure pro Redis ze stejného předplatného

1. V Azure Portal přejděte k instanci API Management.
2. V nabídce na levé straně vyberte kartu **externí mezipaměť** .
3. Klikněte na tlačítko **+ Přidat** .
4. V rozevíracím poli **instance mezipaměti** vyberte mezipaměť.
5. Vyberte **výchozí** nebo zadejte požadovanou oblast do rozevíracího pole **použít z** .
6. Klikněte na **Uložit**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Přidání mezipaměti Azure pro Redis hostované mimo aktuální předplatné Azure nebo obecně Azure

1. V Azure Portal přejděte k instanci API Management.
2. V nabídce na levé straně vyberte kartu **externí mezipaměť** .
3. Klikněte na tlačítko **+ Přidat** .
4. V rozevíracím poli **instance mezipaměti** vyberte **vlastní** .
5. Vyberte **výchozí** nebo zadejte požadovanou oblast do rozevíracího pole **použít z** .
6. Zadejte do pole **připojovací řetězec** svou mezipaměť Azure pro připojovací řetězec Redis.
7. Klikněte na **Uložit**.

## <a name="use-the-external-cache"></a>Použít externí mezipaměť

Jakmile je externí mezipaměť nakonfigurovaná v Azure API Management, dá se použít prostřednictvím zásad ukládání do mezipaměti. Podrobné pokyny najdete v tématu [Přidání ukládání do mezipaměti pro zlepšení výkonu v Azure API Management](api-management-howto-cache.md) .

## <a name="next-steps"></a>Další kroky

* Další informace o zásadách ukládání do mezipaměti najdete v části [Zásady ukládání do mezipaměti][Caching policies] v článku [Zásady API Managementu][API Management policy reference].
* Informace o ukládání položek do mezipaměti podle klíče pomocí výrazů zásad najdete v článku [Vlastní ukládání do mezipaměti ve službě Azure API Management](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
