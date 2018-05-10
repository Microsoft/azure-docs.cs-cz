---
title: Ověření pomocí Azure kontejneru registru ze služby Azure Kubernetes
description: Zjistěte, jak poskytnout přístup k bitové kopie v registru vaší privátní kontejneru z Kubernetes služby Azure pomocí Azure Active Directory instanční objekt.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.openlocfilehash: 0888afbb9087251e2c9219e2eb32fbf0d5600304
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Ověření pomocí Azure kontejneru registru ze služby Azure Kubernetes

Pokud používáte Azure kontejneru registru (ACR) s Azure Kubernetes služby (AKS), je potřeba navázat mechanismus ověřování. Tento dokument podrobně popisuje doporučené konfigurace pro ověřování mezi těmito dvěma službami Azure.

## <a name="grant-aks-access-to-acr"></a>AKS udělit přístup k ACR

Při vytváření clusteru služby AKS instanční objekt se vytvoří také ke správě clusteru požadavky na provoz s prostředky Azure. Tento objekt zabezpečení služby mohou sloužit také pro ověřování pomocí ACR registru. K tomu je potřeba vytvořit k udělení přístupu služby hlavní pro čtení k prostředku ACR přiřazení role.

Následující příklad slouží k dokončení této operace.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Přístup s Kubernetes tajný klíč

V některých případech nelze vymezovat službu hlavní používá AKS ACR registru. Pro tyto případy můžete vytvořit objekt jedinečné služby a obor pouze ACR registru.

Následující skript slouží k vytvoření objektu služby.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Nyní se uloží pověření hlavní služby Kubernetes [tajný klíč pro vyžádání obsahu k image] [ image-pull-secret] a při spuštění kontejnery v clusteru služby AKS na něj odkazovat.

Následující příkaz vytvoří Kubernetes tajný. Nahraďte název serveru se serverem ACR přihlášení, uživatelské jméno s id objektu zabezpečení služby a heslo, hlavní heslo služby.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Tajný klíč Kubernetes mohou být používány pod nasazení pomocí `ImagePullSecrets` parametr.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
