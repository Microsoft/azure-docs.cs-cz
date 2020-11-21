---
title: Registrace aplikace prostředků v Azure AD – Azure API pro FHIR
description: Zaregistrujte aplikaci prostředků (nebo rozhraní API) v Azure Active Directory, aby klientské aplikace mohly při ověřování vyžadovat přístup k prostředku.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024478"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrace aplikace prostředků v Azure Active Directory

V tomto článku se dozvíte, jak v Azure Active Directory zaregistrovat aplikaci prostředků (nebo rozhraní API). Aplikace prostředků je Azure Active Directory reprezentace samotného rozhraní API serveru FHIR a klientské aplikace můžou požádat o přístup k prostředku při ověřování. Aplikace prostředků je také známá jako *cílová skupina* v agilním OAuth.

## <a name="azure-api-for-fhir"></a>Azure API for FHIR

Pokud používáte rozhraní API Azure pro FHIR, při nasazení služby se automaticky vytvoří aplikace prostředků. Pokud používáte rozhraní API Azure pro FHIR ve stejném tenantovi Azure Active Directory jako při nasazování vaší aplikace, můžete tento návod přeskočit a místo toho nasadit rozhraní API Azure pro FHIR, abyste mohli začít.

Pokud používáte jiného klienta Azure Active Directory (není přidruženo k vašemu předplatnému), můžete do svého tenanta pomocí PowerShellu importovat Azure API pro aplikaci prostředků FHIR:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

nebo můžete použít rozhraní příkazového řádku Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Server FHIR pro Azure

Pokud používáte Open Source Server FHIR pro Azure, použijte k registraci aplikace prostředků postup v [úložišti GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) . 

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak zaregistrovat aplikaci prostředků v Azure Active Directory. Pak zaregistrujte vaši důvěrnou klientskou aplikaci.
 
>[!div class="nextstepaction"]
>[Registrace důvěrné klientské aplikace](register-confidential-azure-ad-client-app.md)