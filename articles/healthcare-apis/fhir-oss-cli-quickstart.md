---
title: Nasazení Open FHIR zdrojového serveru pro Azure pomocí Azure CLI
description: V tomto rychlém startu vysvětluje, jak nasadit server otevřete Microsoft FHIR zdroj pro Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 6bb2806aa1ba830f1ef3215a61db4d40f531bdd8
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823462"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Rychlý start: Nasazení serveru otevřený zdroj FHIR pomocí rozhraní příkazového řádku Azure

V tomto rychlém startu se dozvíte jak nasadit otevřít FHIR zdroje&reg; server v Azure pomocí Azure CLI.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vyberte název skupiny prostředků, která bude obsahovat zřízené prostředky a vytvořte ho:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Nasazení šablony

Microsoft Server FHIR pro Azure [úložiště GitHub](https://github.com/Microsoft/fhir-server) obsahuje šablonu, která nasadí všechny potřebné prostředky. Nasaďte je:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Ověřte, zda je spuštěn FHIR server

Možnosti příkazu získáte ze serveru FHIR pomocí:

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Bude trvat minutu nebo tak, aby server mohl reagovat prvním.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci používat, odstraňte skupinu prostředků pomocí následujících kroků:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili FHIR serveru otevřete zdroj Microsoft Azure do vašeho předplatného. Informace o přístup k rozhraní API FHIR pomocí nástroje Postman, pokračujte kurzem Postman.
 
>[!div class="nextstepaction"]
>[Používání rozhraní API FHIR pomocí nástroje Postman](access-fhir-postman-tutorial.md)