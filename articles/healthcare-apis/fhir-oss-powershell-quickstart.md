---
title: 'PowerShell: nasazení serveru FHIR pro Azure – Azure API pro FHIR'
description: V tomto rychlém startu se dozvíte, jak nasadit server Microsoft Open Source FHIR pomocí prostředí PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c6c1a7e21f0a1554c67c7f1860a2bd3382c941f5
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817955"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Rychlý Start: nasazení Open Source serveru FHIR pomocí prostředí PowerShell

V tomto rychlém startu se dozvíte, jak nasadit Open Source Server Microsoft FHIR pro Azure pomocí PowerShellu.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vyberte název skupiny prostředků, která bude obsahovat zřízené prostředky, a vytvořte ji:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Nasazení šablony serveru FHIR

[Úložiště GitHub](https://github.com/Microsoft/fhir-server) pro Microsoft FHIR Server pro Azure obsahuje šablonu, která bude nasazovat všechny nezbytné prostředky. Proces nasazení trvá několik minut, než se vyžadují vytváření a konfigurace prostředků Azure. Nasadit pomocí:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> Pokud jste ještě přihlášeni, spusťte nejprve následující příkazy.

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

Pokud chcete použít existující skupinu prostředků, změňte $rg hodnoty v řádku definice $rg proměnné a v příkazovém řádku nasazení šablony Azure ARM, jak je znázorněno v kódu.

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
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
