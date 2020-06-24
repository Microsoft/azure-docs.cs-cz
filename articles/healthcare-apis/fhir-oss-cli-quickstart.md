---
title: 'Azure CLI: nasazení Open Source serveru FHIR pro Azure – Azure API pro Azure'
description: V tomto rychlém startu se dozvíte, jak nasadit Open Source Server Microsoft FHIR pro Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "84820226"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Rychlý Start: nasazení Open Source serveru FHIR pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak nasadit otevřený zdrojový &reg; Server FHIR v Azure pomocí rozhraní příkazového řádku Azure CLI.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vyberte název skupiny prostředků, která bude obsahovat zřízené prostředky, a vytvořte ji:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Nasazení šablony

[Úložiště GitHub](https://github.com/Microsoft/fhir-server) pro Microsoft FHIR Server pro Azure obsahuje šablonu, která bude nasazovat všechny nezbytné prostředky. Nasadit pomocí:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Ověřte, že je spuštěný Server FHIR.

Získat příkaz funkce ze serveru FHIR pomocí:

```console
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Může to chvíli trvat, než se server poprvé odpoví.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte skupinu prostředků pomocí následujících kroků:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili server Microsoft Open Source FHIR pro Azure do svého předplatného. Pokud se chcete dozvědět, jak získat přístup k rozhraní FHIR API pomocí post, přejděte do kurzu post.
 
>[!div class="nextstepaction"]
>[Přístup k rozhraní FHIR API pomocí post](access-fhir-postman-tutorial.md)