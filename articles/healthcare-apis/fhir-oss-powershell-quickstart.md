---
title: 'PowerShell: nasazení serveru FHIR pro Azure – Azure API pro FHIR'
description: V tomto rychlém startu se dozvíte, jak nasadit server Microsoft Open Source FHIR pomocí prostředí PowerShell.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f959b884f67f354599b99bb6dd24918b28d13382
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "84820178"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Rychlý Start: nasazení Open Source serveru FHIR pomocí prostředí PowerShell

V tomto rychlém startu se dozvíte, jak nasadit Open Source Server Microsoft FHIR pro Azure pomocí PowerShellu.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vyberte název skupiny prostředků, která bude obsahovat zřízené prostředky, a vytvořte ji:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Nasazení šablony serveru FHIR

[Úložiště GitHub](https://github.com/Microsoft/fhir-server) pro Microsoft FHIR Server pro Azure obsahuje šablonu, která bude nasazovat všechny nezbytné prostředky. Nasadit pomocí:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Ověřte, že je spuštěný Server FHIR.

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Může to chvíli trvat, než se server poprvé odpoví.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte skupinu prostředků pomocí následujících kroků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili server Microsoft Open Source FHIR pro Azure do svého předplatného. Pokud se chcete dozvědět, jak získat přístup k rozhraní FHIR API pomocí post, přejděte do kurzu post.
 
>[!div class="nextstepaction"]
>[Přístup k rozhraní FHIR API pomocí post](access-fhir-postman-tutorial.md)
