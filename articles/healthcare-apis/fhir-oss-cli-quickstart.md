---
title: 'Azure CLI: nasazení Open Source serveru FHIR pro Azure – Azure API pro Azure'
description: V tomto rychlém startu se dozvíte, jak nasadit Open Source Server Microsoft FHIR pro Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10af71afd8843e75d5df3be57c909c56a7abca01
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843568"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Rychlý Start: nasazení Open Source serveru FHIR pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak nasadit otevřený zdrojový &reg; Server FHIR v Azure pomocí rozhraní příkazového řádku Azure CLI.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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

```azurecli-interactive
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
