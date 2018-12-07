---
title: Použít externí mezipamětí ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat a používat externí mezipamětí ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06052bd0cba6d119d07e86ed6aed833dec9f1f92
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014561"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Účely externí mezipamětí Azure Redis ve službě Azure API Management

Kromě využívá integrovanou mezipaměť, Azure API Management umožňuje také pro ukládání do mezipaměti odpovědi v externí mezipamětí Azure Redis.

Použití externích mezipaměti umožňuje překonat určitá omezení integrovanou mezipaměť na. Je obzvláště užitečné, pokud chcete:

* Nepoužívejte mezipaměť pravidelně vymazána při aktualizacích API Management
* Máte větší kontrolu nad konfigurací mezipaměti
* Více dat, než úroveň API Management umožňuje ukládat do mezipaměti
* Použití ukládání do mezipaměti služby API Management na úrovni Consumption

Podrobnější informace o ukládání do mezipaměti najdete v popisu [zásad ukládání do mezipaměti API Management](api-management-caching-policies.md) a [vlastního ukládání do mezipaměti Azure API Management](api-management-sample-cache-by-key.md).

![Přineste vlastní mezipaměti služby APIM](media/api-management-howto-cache-external/overview.png)

Naučíte se:

> [!div class="checklist"]
> * Přidat externí mezipamětí ve službě API Management

## <a name="availability"></a>Dostupnost

> [!NOTE]
> Tato funkce je momentálně dostupná jenom **spotřeby** úroveň služby Azure API Management.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

+ [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)
+ Vysvětlení [ukládání do mezipaměti ve službě Azure API Management](api-management-howto-cache.md)

## <a name="create-cache"> </a> Vytvoření Azure Cache pro Redis

Tato část vysvětluje postup při vytvoření Azure Cache pro Redis v Azure. Pokud už máte Azure Cache pro Redis, uvnitř nebo mimo Azure, můžete <a href="#add-external-cache">přeskočit</a> k další části.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Přidat externí mezipamětí

Postupujte podle následujících kroků a přidejte externí mezipamětí Azure Redis ve službě Azure API Management.

![Přineste vlastní mezipaměti služby APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> **Použití z** nastavení určuje, které API Management, místní nasazení bude komunikovat s nakonfigurovanou mezipamětí v případě více oblastí konfigurace služby API Management. Mezipaměti zadaný jako **výchozí** mezipaměti s místní hodnota se přepíše.
>
> Například pokud API Management je hostována v oblastech východní USA, jihovýchodní Asie a západní Evropa a existují dvě mezipaměti nakonfigurován, jeden pro **výchozí** a jeden pro **jihovýchodní Asie**, API Management v  **Jihovýchodní Asie** používat svůj vlastní mezipaměti, přestože tyto dvě oblasti budou používat **výchozí** položky mezipaměti.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Přidat Azure Cache pro Redis ze stejného předplatného

1. Přejděte k vaší instanci API Management na webu Azure Portal.
2. Vyberte **externí mezipamětí** kartu z nabídky na levé straně.
3. Klikněte na tlačítko **+ přidat** tlačítko.
4. Vyberte příslušnou mezipaměť **instance mezipaměti** pole rozevíracího seznamu.
5. Vyberte **výchozí** nebo zadejte požadovaná oblast v **použití z** pole rozevíracího seznamu.
6. Klikněte na **Uložit**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Přidat pro Redis hostované mimo aktuální předplatné Azure nebo Azure obecně Azure Cache

1. Přejděte k vaší instanci API Management na webu Azure Portal.
2. Vyberte **externí mezipamětí** kartu z nabídky na levé straně.
3. Klikněte na tlačítko **+ přidat** tlačítko.
4. Vyberte **vlastní** v **instance mezipaměti** pole rozevíracího seznamu.
5. Vyberte **výchozí** nebo zadejte požadovaná oblast v **použití z** pole rozevíracího seznamu.
6. Zadání připojovacího řetězce Redis v Azure Cache **připojovací řetězec** pole.
7. Klikněte na **Uložit**.

## <a name="use-the-external-cache"></a>Použití externích mezipaměti

Jakmile externí mezipaměť je nakonfigurovaná ve službě Azure API Management, můžete použít prostřednictvím zásad ukládání do mezipaměti. Zobrazit [ukládání do mezipaměti ke zlepšení výkonu ve službě Azure API Management přidat](api-management-howto-cache.md) podrobné pokyny.

## <a name="next-steps"></a>Další kroky
* Další informace o zásadách ukládání do mezipaměti najdete v části [Zásady ukládání do mezipaměti][Caching policies] v článku [Zásady API managementu][API Management policy reference].
* Informace o ukládání položek do mezipaměti podle klíče pomocí výrazů zásad najdete v článku [Vlastní ukládání do mezipaměti ve službě Azure API Management](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx