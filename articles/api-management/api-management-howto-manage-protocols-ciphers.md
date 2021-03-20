---
title: Správa protokolů a šifr v Azure API Management | Microsoft Docs
description: Naučte se spravovat protokoly (TLS) a šifry (DES) v Azure API Management.
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
ms.openlocfilehash: 043a3d0b63dfc74f587b58b3c2ac42f1a084cc4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86250307"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Správa protokolů a šifer ve službě Azure API Management

Azure API Management podporuje více verzí protokolu TLS pro klientské i back-endové strany i šifrování 3DES.

V této příručce se dozvíte, jak spravovat konfiguraci protokolů a šifrování pro instanci Azure API Management.

![Správa protokolů a šifr v APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Předpoklady

Pokud chcete postupovat podle kroků v tomto článku, musíte mít:

* Instance API Management

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Jak spravovat protokoly TLS a šifru 3DES

1. V Azure Portal přejděte ke své **instanci API Management** .
2. V nabídce vyberte **nastavení protokolu** .  
3. Povolte nebo zakažte požadované protokoly nebo šifry.
4. Klikněte na **Uložit**. Změny budou provedeny během hodiny.  

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [TLS (Transport Layer Security)](/dotnet/framework/network-programming/tls).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o API Management.
