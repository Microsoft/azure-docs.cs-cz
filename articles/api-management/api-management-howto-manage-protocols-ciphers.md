---
title: Správa protokolů a šifr v Azure API Management | Microsoft Docs
description: Naučte se spravovat protokoly (TLS, SSL) a šifry (DES) v Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f7c7fdd06480ce3da70c86d38ab0685b9b3aaaf2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072388"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Správa protokolů a šifr v Azure API Management

Azure API Management podporuje více verzí protokolu TLS pro klientské i back-endové strany i šifrování 3DES.

V této příručce se dozvíte, jak spravovat konfiguraci protokolů a šifrování pro instanci Azure API Management.

![Správa protokolů a šifr v APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle kroků v tomto článku, musíte mít:

* Instance API Management

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Jak spravovat protokoly TLS a šifru 3DES

1. V Azure Portal přejděte ke své **instanci API Management** .
2. V nabídce vyberte **nastavení protokolu** .  
3. Povolte nebo zakažte požadované protokoly nebo šifry.
4. Klikněte na **Uložit**. Změny budou provedeny během hodiny.  

## <a name="next-steps"></a>Další postup

* Přečtěte si další informace o [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o API Management.