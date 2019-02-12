---
title: Předání ověřovacích tokenů do služby Azure Media Services | Dokumentace Microsoftu
description: Zjistěte, jak odeslat ověřovací tokeny z klienta ke službě Azure Media Services doručení klíče
services: media-services
keywords: Ochrana obsahu, DRM, ověřování pomocí tokenu
documentationcenter: ''
author: dbgeorge
manager: jasonsue
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: dwgeo
ms.openlocfilehash: b4bcc1bbed732e09f4c4d915342a422ce6c36901
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002067"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Zjistěte, jak klienti předání tokenů ve službě Azure Media Services doručení klíče
Zákazníci se často ptají, jak přehrávač můžete předat tokeny ke službě Azure Media Services doručení klíče pro ověření, přehrávače můžete získat klíč. Služba Media Services podporuje jednoduchý webový token (SWT) a formátů JSON Web Token (JWT). Ověření tokenu se použije na libovolný typ klíče, bez ohledu na to, jestli používat standard common encryption nebo Obálka šifrování Advanced Encryption (Standard AES) v systému.

 V závislosti na player a platformy, na kterou cílíte můžete předat token s přehrávače následujícími způsoby:

- Prostřednictvím protokolu HTTP autorizační hlavičky.
    > [!NOTE]
    > Předpona "Nosiče" očekává podle specifikace OAuth 2.0. Ukázka player se konfigurace tokenu je hostované na Azure Media Player [ukázkové stránky](http://ampdemo.azureedge.net/). Chcete-li nastavit zdroj videa, zvolte **AES (tokenů JWT)** nebo **AES (SWT Token)**. Token, který se předá prostřednictvím autorizační hlavičky.

- Prostřednictvím přidání adresy URL parametr pomocí dotazu "token = tokenvalue."  
    > [!NOTE]
    > Předpona "Nosiče" není očekáván. Vzhledem k tomu, že token, který se odesílá prostřednictvím adresy URL, budete muset obraně řetězec tokenu. Tady je C# ukázkový kód, který ukazuje, jak to udělat:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Prostřednictvím CustomData pole.
Tato možnost se používá pro získání licence PlayReady jenom přes pole CustomData výzvy získání licence PlayReady. V takovém případě musí být token uvnitř dokumentu XML, podle postupu popsaného tady:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Vložte ověřovací token do prvku Token.

- Prostřednictvím alternativní stop HTTP Live Streaming (HLS). Pokud je nutné nakonfigurovat ověřování pomocí tokenu pro AES + HLS přehrávání na iOS/Safari, není k dispozici způsob, jak můžete odeslat přímo v tokenu. Další informace o tom, jak alternativní seznam stop, chcete-li povolit tento scénář, najdete v tomto [blogový příspěvek](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Další postup

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]