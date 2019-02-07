---
title: Nasazení rozhraní API služby Azure pro FHIR pomocí Azure CLI
description: Nasazení rozhraní API služby Azure pro FHIR pomocí Azure CLI.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 880f9d67f87d32fbc03bc04877267b5b26022381
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823517"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Rychlý start: Nasazení rozhraní API služby Azure pro FHIR pomocí Azure CLI

V tomto rychlém startu budete informace o nasazení rozhraní API služby Azure pro FHIR v Azure pomocí Azure CLI.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Vytvoření šablony Azure Resource Manageru s následujícím obsahem:

[!code-json[](samples/azuredeploy.json)]

Uložte s názvem `azuredeploy.json`

## <a name="create-azure-resource-manager-parameter-file"></a>Vytvořte soubor parametrů Azure Resource Manageru

Vytvořte soubor parametrů šablony Azure Resource Manageru s následujícím obsahem:

[!code-json[](samples/azuredeploy.parameters.json)]

Uložte s názvem `azuredeploy.parameters.json`

Hodnoty ID objektu `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` a `yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy` odpovídají objektu ID jednotlivých uživatelů Azure Active Directory nebo služby objekty zabezpečení v adresáři přidružené k předplatnému. Pokud chcete znát ID objektu konkrétního uživatele, najdete ho pomocí příkazu jako:

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

Přečtěte si průvodce na [vyhledání ID objektů identity](find-identity-object-ids.md) další podrobnosti.

# <a name="create-azure-resource-group"></a>Vytvořte skupinu prostředků Azure

Vyberte název skupiny prostředků, která bude obsahovat rozhraní API služby Azure pro FHIR a vytvořte ho:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir-account"></a>Nasazení rozhraní API služby Azure pro účet FHIR

Použití šablony (`azuredeploy.json`) a soubor parametrů šablony (`azuredeploy.parameters.json`) k nasazení rozhraní API služby Azure pro FHIR:

```azurecli-interactive
az group deployment create -g "myResourceGroup" --template-file azuredeploy.json --parameters @{azuredeploy.parameters.json}
```

## <a name="fetch-fhir-api-capability-statement"></a>Načíst FHIR API funkce – příkaz

Získejte možnost příkazu z rozhraní API FHIR pomocí:

```azurecli-interactive
curl --url "https://nameOfFhirAccount.azurehealthcareapis.com/fhir/metadata"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci používat, odstraňte skupinu prostředků pomocí následujících kroků:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili rozhraní API služby Azure pro FHIR do vašeho předplatného. Informace o přístup k rozhraní API FHIR pomocí nástroje Postman, pokračujte kurzem Postman.

>[!div class="nextstepaction"]
>[Používání rozhraní API FHIR pomocí nástroje Postman](access-fhir-postman-tutorial.md)