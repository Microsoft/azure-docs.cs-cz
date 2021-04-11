---
title: Předat ověřovací tokeny do Media Services V3 | Microsoft Docs
description: Naučte se odesílat ověřovací tokeny z klienta do služby pro doručování klíčů Media Services V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572425"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Předat tokeny službě doručování klíčů Azure Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Zákazníci se často dotazují, jak může hráč předat tokeny službě doručování klíčů Azure Media Services k ověření, aby tento klíč mohl získat hráč. Media Services podporuje formáty jednoduchých webových tokenů (SWT) a JSON Web Token (JWT). Ověřování tokenu se aplikuje na libovolný typ klíče bez ohledu na to, jestli v systému používáte společné šifrování nebo šifrování obálek s standard AES (Advanced Encryption Standard) (AES).

 V závislosti na přehrávači a platformě, na kterou cílíte, můžete token předat pomocí přehrávače následujícími způsoby:

## <a name="pass-a-token-through-the-http-authorization-header"></a>Předání tokenu prostřednictvím hlavičky autorizace protokolu HTTP

> [!NOTE]
> Na základě specifikací OAuth 2,0 se očekává prefix "nosič". Zdroj videa nastavíte tak, že vyberete **AES (token JWT)** nebo **AES (SWT token)**. Token se předává přes autorizační hlavičku.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Předání tokenu prostřednictvím přidání parametru dotazu URL s parametrem token = tokenvalue

> [!NOTE]
> Předpona "nosiče" není očekávána. Vzhledem k tomu, že se token odesílá prostřednictvím adresy URL, je nutné obraně řetězec tokenu. Zde je ukázkový kód C#, který ukazuje, jak to provést:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Předání tokenu prostřednictvím pole CustomData

Tato možnost se používá jenom pro získání licence PlayReady, a to prostřednictvím pole CustomData výzvy pro získání licence PlayReady. V takovém případě musí být token uvnitř dokumentu XML, jak je popsáno zde:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Do elementu tokenu vložte svůj ověřovací token.
