---
title: Zabezpečení rozhraní API klienta pomocí certifikátu ověřování ve službě API Management – Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak zabezpečit přístup k rozhraním API pomocí klientských certifikátů
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 3307ea391734828cb83c927e8df8aca79685279a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441532"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Jak zabezpečit rozhraní API klienta pomocí certifikátu ověřování ve službě API Management

API Management umožňuje zabezpečený přístup k rozhraní API (například klienta do API managementu) pomocí klientských certifikátů. V současné době můžete zkontrolovat kryptografický otisk klientského certifikátu na požadovanou hodnotu. Můžete také zkontrolovat kryptografický otisk proti existujících certifikátů odeslaných do API managementu.  

Informace o zabezpečení přístupu ke službě back endové rozhraní API s použitím klientské certifikáty (tj, API Management do back endu) najdete v tématu [jak ověřování pomocí certifikátů zabezpečit back endovým službám pomocí klienta](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="checking-the-expiration-date"></a>Kontroluje se datum vypršení platnosti

Ke kontrole, pokud vypršela platnost certifikátu, lze nakonfigurovat následující zásady:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Kontroluje se vydavatel a předmět

Ke kontrole vydavatel a předmět certifikátu klienta lze nakonfigurovat následující zásady:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Kontroluje se kryptografický otisk

Zkontrolujte kryptografický otisk klientského certifikátu lze nakonfigurovat následující zásady:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontroluje se kryptografický otisk před certifikáty nahráli do API managementu

Následující příklad ukazuje, jak zkontrolujte kryptografický otisk klientského certifikátu proti certifikátů odeslaných do rozhraní API Management: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Další krok

*  [Jak zabezpečit back endovým službám pomocí klienta ověření certifikátu](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Jak nahrát certifikáty](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

