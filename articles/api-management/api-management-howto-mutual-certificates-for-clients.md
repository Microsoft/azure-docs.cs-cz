---
title: Zabezpečte rozhraní API pomocí ověřování klientského certifikátu v API Management
titleSuffix: Azure API Management
description: Naučte se zabezpečit přístup k rozhraním API pomocí klientských certifikátů. K ověření příchozích certifikátů můžete použít výrazy zásad.
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
ms.openlocfilehash: 19e0d741d959eba704f26e7e8f7b5d311aa77775
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87904854"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Postup zabezpečení rozhraní API s využitím ověřování pomocí klientských certifikátů ve službě API Management

API Management poskytuje možnost zabezpečení přístupu k rozhraním API (například klienta k API Management) pomocí klientských certifikátů. Příchozí certifikát a kontrolu vlastností certifikátu můžete ověřit podle požadovaných hodnot pomocí výrazů zásad.

Informace o zabezpečení přístupu k back-endové službě rozhraní API pomocí klientských certifikátů (tj. API Management do back-endu) najdete v tématu [zabezpečení back-endové služby pomocí ověřování klientských certifikátů](./api-management-howto-mutual-certificates.md) .

> [!IMPORTANT]
> Pokud chcete přijímat a ověřovat klientské certifikáty přes HTTP/2 na úrovních pro vývojáře, Basic, Standard nebo Premium, musíte v okně vlastní domény zapnout nastavení vyjednávat klientský certifikát, jak je znázorněno níže.

![Vyjednat klientský certifikát](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Pokud chcete přijímat a ověřovat klientské certifikáty ve vrstvě spotřeby, musíte zapnout nastavení "požadovat certifikát klienta" v okně vlastní domény, jak je znázorněno níže.

![Požádat o certifikát klienta](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Kontroluje se Vydavatel a předmět.

Níže uvedené zásady je možné nakonfigurovat tak, aby kontrolovaly vystavitele a předmět klientského certifikátu:

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
> Chcete-li zakázat kontrolu použití seznamu odvolaných certifikátů `context.Request.Certificate.VerifyNoRevocation()` namísto `context.Request.Certificate.Verify()` .
> Pokud je klientský certifikát podepsaný svým držitelem, musí být do API Management a pro práci [odesílány](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` .

## <a name="checking-the-thumbprint"></a>Kontrola kryptografického otisku

Níže uvedené zásady se dají nakonfigurovat tak, aby kontrolovaly kryptografický otisk klientského certifikátu:

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
> Chcete-li zakázat kontrolu použití seznamu odvolaných certifikátů `context.Request.Certificate.VerifyNoRevocation()` namísto `context.Request.Certificate.Verify()` .
> Pokud je klientský certifikát podepsaný svým držitelem, musí být do API Management a pro práci [odesílány](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` .

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrola kryptografického otisku proti certifikátům odeslaným do API Management

Následující příklad ukazuje, jak kontrolovat kryptografický otisk certifikátu klienta proti certifikátům odeslaným do API Management:

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
> Chcete-li zakázat kontrolu použití seznamu odvolaných certifikátů `context.Request.Certificate.VerifyNoRevocation()` namísto `context.Request.Certificate.Verify()` .
> Pokud je klientský certifikát podepsaný svým držitelem, musí být do API Management a pro práci [odesílány](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` .

> [!TIP]
> Problém zablokování klientského certifikátu popsaný v tomto [článku](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) se může projevit v několika ohledech, například když se požadavky zablokují, požadavky `403 Forbidden` jsou po vypršení časového limitu na stavový kód `context.Request.Certificate` `null` . Tento problém obvykle ovlivňuje `POST` a `PUT` požadavky s délkou obsahu přibližně 60KB nebo větší.
> Aby nedošlo k tomuto problému, zapněte nastavení vyjednávat klientský certifikát pro požadované názvy hostitelů v okně vlastní domény, jak je znázorněno níže. Tato funkce není k dispozici na úrovni spotřeby.

![Vyjednat klientský certifikát](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Další kroky

-   [Postup zabezpečení back-endové služby pomocí ověřování klientských certifikátů](./api-management-howto-mutual-certificates.md)
-   [Postup nahrání certifikátů](./api-management-howto-mutual-certificates.md)
