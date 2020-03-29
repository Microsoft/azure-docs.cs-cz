---
title: Zabezpečená rozhraní API pomocí ověřování klientského certifikátu ve správě rozhraní API
titleSuffix: Azure API Management
description: Zjistěte, jak zabezpečit přístup k možnostem API pomocí klientských certifikátů
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713146"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Postup zabezpečení rozhraní API s využitím ověřování pomocí klientských certifikátů ve službě API Management

Správa rozhraní API umožňuje zabezpečit přístup k rozhraním API (tj. klientke správě rozhraní API) pomocí klientských certifikátů. Můžete ověřit příchozí certifikát a zkontrolovat vlastnosti certifikátu proti požadovaným hodnotám pomocí výrazů zásad.

Informace o zabezpečení přístupu k back-endové službě rozhraní API pomocí klientských certifikátů (tj. správa rozhraní API pro back-end) naleznete v [tématu Zabezpečení back-endových služeb pomocí ověřování klientských certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Chcete-li přijímat a ověřovat klientské certifikáty přes HTTP/2 na úrovních Vývojář, Basic, Standard nebo Premium, musíte zapnout nastavení "Vyjednat klientský certifikát" v okně "Vlastní domény", jak je znázorněno níže.

![Vyjednat klientský certifikát](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Chcete-li přijímat a ověřovat klientské certifikáty na úrovni Spotřeba, musíte zapnout nastavení "Požádat o klientský certifikát" v okně "Vlastní domény", jak je znázorněno níže.

![Požádat o klientský certifikát](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Kontrola vystavitho a předmětu

Níže uvedené zásady lze nakonfigurovat tak, aby kontrolovaly vystavitele a předmět klientského certifikátu:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Chcete-li zakázat kontrolu `context.Request.Certificate.VerifyNoRevocation()` seznamu `context.Request.Certificate.Verify()`odvolaných certifikátů, použijte místo .
> Pokud je klientský certifikát podepsaný svým držitelem, je nutné do správy rozhraní `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` API [odeslat](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority.

## <a name="checking-the-thumbprint"></a>Kontrola kryptografického otisku

Níže uvedené zásady lze nakonfigurovat tak, aby kontrolovaly kryptografický otisk klientského certifikátu:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Chcete-li zakázat kontrolu `context.Request.Certificate.VerifyNoRevocation()` seznamu `context.Request.Certificate.Verify()`odvolaných certifikátů, použijte místo .
> Pokud je klientský certifikát podepsaný svým držitelem, je nutné do správy rozhraní `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` API [odeslat](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrola kryptografických otisků certifikátů nahraných do správy rozhraní API

Následující příklad ukazuje, jak zkontrolovat kryptografický otisk klientského certifikátu proti certifikátům odeslaným do správy rozhraní API:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Chcete-li zakázat kontrolu `context.Request.Certificate.VerifyNoRevocation()` seznamu `context.Request.Certificate.Verify()`odvolaných certifikátů, použijte místo .
> Pokud je klientský certifikát podepsaný svým držitelem, je nutné do správy rozhraní `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` API [odeslat](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority.

> [!TIP]
> Problém zablokování klientského certifikátu popsaný v tomto [článku](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) se může projevit několika způsoby, například požadavky zmrazit, požadavky za následek `403 Forbidden` stavový kód po vypršení časového limitu, `context.Request.Certificate` je `null`. Tento problém `POST` obvykle `PUT` ovlivňuje a požadavky s délkou obsahu přibližně 60 kB nebo větší.
> Chcete-li zabránit tomuto problému dochází, zapněte nastavení "Vyjednat klientský certifikát" pro požadované názvy hostitelů v okně "Vlastní domény", jak je znázorněno níže. Tato funkce není k dispozici ve vrstvě Spotřeba.

![Vyjednat klientský certifikát](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Další kroky

-   [Zabezpečení back-endových služeb pomocí ověřování klientských certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Jak nahrát certifikáty](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
