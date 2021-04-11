---
title: Předat ověřovací tokeny do Azure Media Services | Microsoft Docs
description: Přečtěte si, jak odesílat ověřovací tokeny z klienta do služby pro doručování klíčů Azure Media Services
services: media-services
keywords: Ochrana obsahu, DRM, ověřování tokenů
author: IngridAtMicrosoft
manager: femila
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe692e1eb20956f339c9b861f50163cee9c5063
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564649"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Přečtěte si, jak klienti přecházejí tokeny službě doručování klíčů Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Zákazníci se často dotazují, jak může hráč předat tokeny službě doručování klíčů Azure Media Services k ověření, aby tento klíč mohl získat hráč. Media Services podporuje formáty jednoduchých webových tokenů (SWT) a JSON Web Token (JWT). Ověřování tokenu se aplikuje na libovolný typ klíče bez ohledu na to, jestli v systému používáte společné šifrování nebo šifrování obálek s standard AES (Advanced Encryption Standard) (AES).

 V závislosti na přehrávači a platformě, na kterou cílíte, můžete token předat pomocí přehrávače následujícími způsoby:

- Prostřednictvím hlavičky autorizace protokolu HTTP.
    > [!NOTE]
    > Na základě specifikací OAuth 2,0 se očekává prefix "nosič". Zdroj videa nastavíte tak, že vyberete **AES (token JWT)** nebo **AES (SWT token)**. Token se předává přes autorizační hlavičku.

- Prostřednictvím přidání parametru dotazu URL s parametrem token = tokenvalue.  
    > [!NOTE]
    > Předpona "nosiče" není očekávána. Vzhledem k tomu, že se token odesílá prostřednictvím adresy URL, je nutné obraně řetězec tokenu. Zde je ukázkový kód C#, který ukazuje, jak to provést:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Prostřednictvím pole CustomData.
Tato možnost se používá jenom pro získání licence PlayReady, a to prostřednictvím pole CustomData výzvy pro získání licence PlayReady. V takovém případě musí být token uvnitř dokumentu XML, jak je popsáno zde:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Do elementu tokenu vložte svůj ověřovací token.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
