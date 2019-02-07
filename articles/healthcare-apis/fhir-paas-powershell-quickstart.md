---
title: Nasazení rozhraní API služby Azure pro FHIR pomocí Powershellu
description: Nasazení rozhraní API služby Azure pro FHIR pomocí Powershellu.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 0cea06da1815fa1128b16f1427690141823e82d8
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823482"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Rychlý start: Nasazení rozhraní API služby Azure pro FHIR pomocí Powershellu

V tomto rychlém startu budete informace o nasazení rozhraní API služby Azure pro FHIR pomocí Powershellu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Zaregistrovat poskytovatele prostředků FHIR do rozhraní API Azure

Pokud `Microsoft.HealthcareApis` poskytovatele prostředků dosud zaregistrovaný není pro vaše předplatné, můžete ho zaregistrovat:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Vytvoření šablony Azure Resource Manageru s následujícím obsahem:

[!code-json[](samples/azuredeploy.json)]

Uložte s názvem `azuredeploy.json`

## <a name="create-azure-resource-manager-parameter-file"></a>Vytvořte soubor parametrů Azure Resource Manageru

Vytvořte soubor parametrů šablony Azure Resource Manageru s následujícím obsahem:

[!code-json[](samples/azuredeploy.parameters.json)]

Uložte s názvem `azuredeploy.parameters.json`

Hodnoty ID objektu `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` a `yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy` odpovídají objektu ID jednotlivých uživatelů Azure Active Directory nebo služby objekty zabezpečení v adresáři přidružené k předplatnému. Pokud chcete znát ID objektu konkrétního uživatele, najdete ho pomocí příkazu jako:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

Přečtěte si průvodce na [vyhledání ID objektů identity](find-identity-object-ids.md) další podrobnosti.

## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure

```azurepowershell-interactive
$rg = New-AzureRmResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-template"></a>Nasazení šablony

```azurepowershell-interactive
New-AzureRmResourceGroupDeployment -ResourceGroup $rg.ResourceGroupName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

## <a name="fetch-capability-statement"></a>Načtení funkce – příkaz

Budete moct ověřit, že běží, rozhraní API služby Azure pro účet FHIR pomocí načítání FHIR možnosti příkazu:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri $metadataUrl "https://nameOfFhirAccount.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci používat, odstraňte skupinu prostředků pomocí následujících kroků:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili rozhraní API služby Azure pro FHIR do vašeho předplatného. Informace o přístup k rozhraní API FHIR pomocí nástroje Postman, pokračujte kurzem Postman.

>[!div class="nextstepaction"]
>[Používání rozhraní API FHIR pomocí nástroje Postman](access-fhir-postman-tutorial.md)