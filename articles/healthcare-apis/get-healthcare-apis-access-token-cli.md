---
title: Získání přístupového tokenu pomocí Azure CLI – Azure API pro FHIR
description: Tento článek vysvětluje, jak získat přístupový token pro Azure API pro FHIR pomocí Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: 7528f9d4e3b3043af1e4790c063eb6ddc6d9a828
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87849008"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Získání přístupového tokenu pro Azure API pro FHIR pomocí Azure CLI

V tomto článku se dozvíte, jak získat přístupový token pro Azure API pro FHIR pomocí Azure CLI. Když [zřizujete rozhraní API Azure pro FHIR](fhir-paas-portal-quickstart.md), nakonfigurujete skupinu uživatelů nebo instančních objektů, které mají přístup ke službě. Pokud je ID objektu uživatele v seznamu povolených ID objektů, můžete ke službě přistupovat pomocí tokenu získaného pomocí Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>Přihlášení pomocí Azure CLI

Než budete moct získat token, musíte se přihlásit s uživatelem, pro který chcete získat token:

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>Získání tokenu

Rozhraní Azure API pro FHIR používá `resource`  nebo `Audience` s identifikátorem URI se rovná identifikátoru URI serveru FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Token můžete získat a Uložit do proměnné (s názvem `$token` ) pomocí následujícího příkazu:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Použití s rozhraním Azure API pro FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem získání přístupového tokenu pro rozhraní Azure API pro FHIR pomocí Azure CLI. Pokud se chcete dozvědět, jak získat přístup k rozhraní FHIR API pomocí post, přejděte do kurzu post.

>[!div class="nextstepaction"]
>[Přístup k rozhraní FHIR API pomocí post](access-fhir-postman-tutorial.md)
