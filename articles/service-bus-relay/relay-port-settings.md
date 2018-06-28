---
title: Nastavení portu Azure předávání | Microsoft Docs
description: Údaje o předávání přes Azure hodnoty portů.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: sethm
ms.openlocfilehash: 56688b6941d58c0ecc8d0ff4ba3c8a8392e71496
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029369"
---
# <a name="azure-relay-port-settings"></a>Nastavení portu Azure předávání

Následující tabulka popisuje požadované konfigurace pro hodnoty portů pro předávání přes Azure.

## <a name="hybrid-connections"></a>Hybridní připojení

Hybridní připojení používá na portu 443 s protokolem SSL jako podkladový přenosový mechanismus, který používá **HTTPS** pouze. 

## <a name="wcf-relays"></a>Přenosy WCF
  
|Vazba|Zabezpečení přenosu|Port|  
|-------------|------------------------|----------|  
|[Třída BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Ano|HTTPS| 
|" |Ne|HTTP|  
|[Třída BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (služba)|Buď|9351/HTTP|  
|[Třída NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ano|9351/HTTPS|  
|" |Ne|9350/HTTP|  
|[Třída NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (služba)|Buď|9351/HTTP|  
|[Třída NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klienta nebo služba)|Buď|5671/9352/HTTP (9352/9353 Pokud používáte hybridní)|  
|[Třída NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ano|9351/HTTPS|  
|" |Ne|9350/HTTP|  
|[Třída NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (služba)|Buď|9351/HTTP|  
|[Třída WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ano|HTTPS|  
|" |Ne|HTTP|  
|[Třída WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (služba)|Buď|9351/HTTP|  
|[Třída WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Ano|HTTPS|  
|" |Ne|HTTP|  
|[Třída WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (služba)|Buď|9351/HTTP|

## <a name="next-steps"></a>Další postup
Další informace o předávání přes Azure, najdete pomocí těchto odkazů:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Přenos – nejčastější dotazy](relay-faq.md)