---
title: Nasazení serveru otevřete FHIR zdroje pro Azure pomocí prostředí PowerShell – rozhraní API od Microsoftu zdravotní péče
description: V tomto rychlém startu vysvětluje, jak nasadit server Microsoft otevřít zdroj FHIR pomocí Powershellu.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 6118c9371df7aa0af685390dae28bceb496145d4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823477"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Rychlý start: Nasazení serveru otevřený zdroj FHIR pomocí Powershellu

V tomto rychlém startu budete informace o nasazení serveru otevřete Microsoft FHIR zdroj pro Azure pomocí Powershellu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vyberte název skupiny prostředků, která bude obsahovat zřízené prostředky a vytvořte ho:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzureRmResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Nasazení šablony FHIR serveru

Microsoft Server FHIR pro Azure [úložiště GitHub](https://github.com/Microsoft/fhir-server) obsahuje šablonu, která nasadí všechny potřebné prostředky. Nasaďte je:

```azurepowershell-interactive
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName `
```

## <a name="verify-fhir-server-is-running"></a>Ověřte, zda je spuštěn FHIR server

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Bude trvat minutu nebo tak, aby server mohl reagovat prvním.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci používat, odstraňte skupinu prostředků pomocí následujících kroků:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili FHIR serveru otevřete zdroj Microsoft Azure do vašeho předplatného. Informace o přístup k rozhraní API FHIR pomocí nástroje Postman, pokračujte kurzem Postman.
 
>[!div class="nextstepaction"]
>[Používání rozhraní API FHIR pomocí nástroje Postman](access-fhir-postman-tutorial.md)