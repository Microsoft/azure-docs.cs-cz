---
title: Najít ID objektů identity pro ověřování – rozhraní API služby Azure pro FHIR
description: Tento článek vysvětluje, jak vyhledat objekt identity ID potřebné ke konfiguraci ověřování pro rozhraní API služby Azure pro FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: b120b21a89a7105a25ba610402da99f9dce4b7e1
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824026"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Najít ID objektů identity pro konfiguraci ověřování

V tomto článku se dozvíte, jak najít ID objektů, které jsou potřebné ke konfiguraci seznamu povolených identity ID objektů pro rozhraní API služby Azure pro FHIR identity.

Plně spravovaná rozhraní API Azure FHIR&reg; služba je nakonfigurovaná k povolení přístupu jenom na předem definované seznam identit ID objektů. Když aplikace nebo uživatel se pokouší o přístup k rozhraní API FHIR, musíte jim nosný token. Tento token nosiče, bude mít určitých deklarací identity (pole). Aby bylo možné udělit přístup k rozhraní API FHIR, musí obsahovat token správné vystavitele (`iss`), cílová skupina (`aud`) a ID objektu (`oid`) ze seznamu ID povolených objektů. ID identity objektu je buď ID objektu uživatele nebo instančního objektu v Azure Active Directory.

## <a name="configure-list-of-allowed-object-ids"></a>Nakonfigurovat seznam povolených objektů ID

Když vytvoříte nové rozhraní API Azure FHIR instance, můžete nakonfigurovat seznam ID povolených objektů:

![Nakonfigurovat ID povolených objektů](media/quickstart-paas-portal/configure-allowed-oids.png)

Tyto identifikátory objektu může být buď ID pro konkrétní uživatele nebo instanční objekty v Azure Active Directory.

## <a name="find-user-object-id-using-powershell"></a>Najít ID objektu uživatele pomocí Powershellu

Pokud máte uživatele s uživatelským jménem `myuser@consoso.com`, najdou uživatelé `ObjectId` pomocí následujícího příkazu Powershellu:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

nebo můžete použít rozhraní příkazového řádku Azure:

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id-using-powershell"></a>Najít ID instančního objektu pomocí Powershellu

Předpokládejme, že jste se zaregistrovali [klientská aplikace služby](register-service-azure-ad-client-app.md) a chcete povolit tohoto klienta služby pro přístup k rozhraní API služby Azure pro FHIR, můžete najít ID objektu pro klienta instančního objektu pomocí následujícího příkazu Powershellu:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

kde `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` je ID klienta aplikace služby. Alternativně můžete použít `DisplayName` klienta služby:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Pokud používáte rozhraní příkazového řádku Azure, můžete použít:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak najít ID, které jsou potřebné ke konfiguraci identity, které můžou přistupovat k rozhraní API Azure pro instanci FHIR objektů identity. Vedle nasazení plně spravovaná rozhraní API Azure pro FHIR:
 
>[!div class="nextstepaction"]
>[Nasazení Open FHIR zdrojového serveru](fhir-paas-portal-quickstart.md)