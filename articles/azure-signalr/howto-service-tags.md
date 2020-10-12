---
title: Použití značek služeb
titleSuffix: Azure SignalR Service
description: Použití značek služeb k povolení odchozího provozu do vaší služby Azure Signal
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 8810309fef5dbbb35465a2af15d42fa8a59d5401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84300173"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Použití značek služeb pro službu Azure Signal Service

Při konfiguraci [skupiny zabezpečení sítě](../virtual-network/security-overview.md#network-security-groups)můžete pro službu Azure Signal Service použít [značky služeb](../virtual-network/security-overview.md#service-tags) . Umožňuje definovat odchozí pravidlo zabezpečení sítě pro koncové body služby Azure Signal Service, aniž byste museli nekódujte pevně IP adresy.

Služba signalizace Azure spravuje tyto značky služeb. Nemůžete vytvořit vlastní značku služby ani upravit existující. Společnost Microsoft spravuje tyto předpony adres, které se shodují se značkou služby, a automaticky aktualizuje značku služby na změny adres.

## <a name="use-service-tag-on-portal"></a>Použití značky služby na portálu

Přidáním nového pravidla zabezpečení sítě do služby Azure Signal Service můžete povolení odchozího provozu:

1. Přejít do skupiny zabezpečení sítě.

1. Klikněte na nabídku nastavení s názvem **odchozí pravidla zabezpečení**.

1. Klikněte na tlačítko **+ Přidat** v horní části.

1. V části **cíl**vyberte **tag Service** .

1. V části **značka cílové služby**vyberte **AzureSignalR** .

1. Do pole **cílové rozsahy portů**zadejte **443** .

    ![Vytvořit odchozí pravidlo zabezpečení](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Upravte další pole podle vašich potřeb.

1. Klikněte na **Přidat**.


## <a name="next-steps"></a>Další kroky

- [Skupiny zabezpečení sítě: značky služeb](../virtual-network/security-overview.md#security-rules)
