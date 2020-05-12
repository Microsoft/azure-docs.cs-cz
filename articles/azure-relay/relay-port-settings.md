---
title: Nastavení portu Azure Relay | Microsoft Docs
description: Tento článek obsahuje tabulku, která popisuje požadovanou konfiguraci pro hodnoty portů pro Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211759"
---
# <a name="azure-relay-port-settings"></a>Nastavení portu Azure Relay

Následující tabulka popisuje požadovanou konfiguraci pro hodnoty portů pro Azure Relay.

## <a name="hybrid-connections"></a>Hybridní připojení

Hybrid Connections používá objekty WebSockets na portu 443 s protokolem TLS jako podkladový přenosový mechanismus, který používá pouze **protokol HTTPS** . 

## <a name="wcf-relays"></a>Přenosy WCF
  
|Vazba|Zabezpečení přenosu|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Ano|HTTPS| 
|" |Ne|HTTP|  
|[BasicHttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (služba)|Buď|9351/HTTP|  
|[NetEventRelayBinding – třída](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ano|9351/HTTPS|  
|" |Ne|9350/HTTP|  
|[NetEventRelayBinding – třída](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (služba)|Buď|9351/HTTP|  
|[NetTcpRelayBinding – třída](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/služba)|Buď|5671/9352/HTTP (9352/9353 při použití Hybrid)|  
|[NetOnewayRelayBinding – třída](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ano|9351/HTTPS|  
|" |Ne|9350/HTTP|  
|[NetOnewayRelayBinding – třída](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (služba)|Buď|9351/HTTP|  
|[WebHttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ano|HTTPS|  
|" |Ne|HTTP|  
|[WebHttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (služba)|Buď|9351/HTTP|  
|[WS2007HttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Ano|HTTPS|  
|" |Ne|HTTP|  
|[WS2007HttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (služba)|Buď|9351/HTTP|

## <a name="next-steps"></a>Další kroky
Další informace o Azure Relay najdete na těchto odkazech:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Přenos – nejčastější dotazy](relay-faq.md)