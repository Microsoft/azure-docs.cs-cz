---
title: Doručení událostí pomocí služby privátního propojení
description: Tento článek popisuje, jak obejít omezení neschopnýho doručovat události pomocí služby privátního propojení.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 75ae8ff2c341048c39156e43da3615788e9d0f0a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309427"
---
# <a name="deliver-events-using-private-link-service"></a>Doručení událostí pomocí služby privátního propojení
V současné době není možné doručovat události pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md). To znamená, že pokud máte přísné požadavky na izolaci sítě, u kterých vaše doručováné události nesmí opustit privátní IP adresu, neexistuje žádná podpora. 

## <a name="use-managed-identity"></a>Použití spravované identity
Pokud ale vaše požadavky zavolají zabezpečený způsob, jak odesílat události pomocí šifrovaného kanálu a známé identity odesilatele (v tomto případě Event Grid) pomocí veřejného prostoru IP adres, mohli byste události doručovat do Event Hubs, Service Bus nebo Azure Storage pomocí vlastního tématu v Azure Event gridu nebo domény s identitou spravovanou systémem. Podrobnosti o doručování událostí pomocí spravované identity najdete v tématu [doručování událostí pomocí spravované identity](managed-service-identity.md). 

Pak můžete pomocí privátního odkazu nakonfigurovaného v Azure Functions nebo Webhooku nasazeného ve virtuální síti vyžádat události. Viz Ukázka: [připojení k privátním koncovým bodům pomocí Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Doručení prostřednictvím služby privátního propojení":::


V rámci této konfigurace přechází přenos přes veřejnou IP adresu nebo Internet z Event Grid na Event Hubs, Service Bus nebo Azure Storage, kanál ale může být zašifrovaný a používá se spravovaná identita Event Grid. Pokud nakonfigurujete Azure Functions nebo Webhook nasazený do virtuální sítě tak, aby používal Event Hubs, Service Bus nebo Azure Storage prostřednictvím privátního propojení, Tato část provozu bude v Azure zůstat v provozu.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Doručovat události do Event Hubs pomocí spravované identity
Pokud chcete doručovat události do Center událostí v oboru názvů Event Hubs pomocí spravované identity, postupujte takto:

1. Povolit identitu přiřazenou systémem: [Systémová témata](enable-identity-system-topics.md), [vlastní témata a domény](enable-identity-custom-topics-domains.md).  
1. [Přidejte identitu do role **odesilatele dat Azure Event Hubs** v oboru názvů Event Hubs](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Povolit **důvěryhodným službám Microsoftu obejít toto nastavení brány firewall** ve vašem oboru názvů Event Hubs](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. [Nakonfigurujte odběr událostí](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) , který používá centrum událostí jako koncový bod pro použití identity přiřazené systémem.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Doručovat události do Service Bus pomocí spravované identity
Pokud chcete doručovat události do Service Bus front nebo témat v oboru názvů Service Bus pomocí spravované identity, postupujte takto:

1. Povolit identitu přiřazenou systémem: [Systémová témata](enable-identity-system-topics.md), [vlastní témata a domény](enable-identity-custom-topics-domains.md). 
1. [Přidejte identitu do role **Azure Service Bus data Sender**](../service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) v oboru názvů Service Bus
1. [Povolit **důvěryhodným službám Microsoftu obejít toto nastavení brány firewall** ve vašem oboru názvů Service Bus](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. [Nakonfigurujte odběr událostí](managed-service-identity.md) , který používá Service Busou frontu nebo téma jako koncový bod pro použití identity přiřazené systémem.

## <a name="deliver-events-to-storage"></a>Doručovat události do úložiště 
Pokud chcete doručovat události do front úložiště pomocí spravované identity, postupujte podle těchto kroků:

1. Povolit identitu přiřazenou systémem: [Systémová témata](enable-identity-system-topics.md), [vlastní témata a domény](enable-identity-custom-topics-domains.md). 
1. [Přidejte identitu do role **odesílatel zprávy data fronty úložiště**](../storage/common/storage-auth-aad-rbac-portal.md) ve frontě Azure Storage.
1. [Nakonfigurujte odběr událostí](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) , který používá Service Busou frontu nebo téma jako koncový bod pro použití identity přiřazené systémem.


## <a name="next-steps"></a>Další kroky
Další informace o doručování událostí pomocí spravované identity najdete v tématu [doručování událostí pomocí spravované identity](managed-service-identity.md). 
