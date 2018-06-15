---
title: Zabezpečení rozhraní API klienta pomocí certifikátu ověřování ve službě API Management - Azure API Management | Microsoft Docs
description: Zjistěte, jak zabezpečit přístup k rozhraní API pomocí klientských certifikátů
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
ms.openlocfilehash: 841825923819bdb257e5b5983071d999cca805e9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2017
ms.locfileid: "26406739"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Zabezpečení rozhraní API pomocí klienta pro ověřování pomocí certifikátu ve službě API Management

API Management poskytuje schopnost zabezpečit přístup k rozhraní API (tj. klienta k API Management) pomocí klientských certifikátů. V současné době můžete zkontrolovat kryptografický otisk certifikátu klienta pro požadovanou hodnotu. Můžete také zkontrolovat kryptografický otisk pro existující certifikátů odeslaných do rozhraní API Management.  

Informace o zabezpečení přístupu ke službě back endové rozhraní API pomocí klientských certifikátů (tj, rozhraní API správy na back-end) najdete v tématu [jak zabezpečit back endové služby pomocí klienta pro ověřování pomocí certifikátu](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-the-expiration-date"></a>Kontrola datum vypršení platnosti

Níže zásady mohou být konfigurovány ke kontrole, pokud vypršela platnost certifikátu:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Kontrola vystavitele a předmět

Ke kontrole vystavitele a předmět certifikátu klienta lze nakonfigurovat následující zásady:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Kontrola kryptografický otisk

Níže zásady můžete nakonfigurovat zkontrolujte kryptografický otisk certifikátu klienta:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrola kryptografický otisk před certifikáty nahrán do rozhraní API Management

Následující příklad ukazuje, jak zkontrolujte kryptografický otisk certifikátu klienta vůči certifikátů odeslaných do rozhraní API správy: 

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

*  [Jak zabezpečit back endové služby pomocí klienta pro ověřování pomocí certifikátu](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Postup nahrání certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

