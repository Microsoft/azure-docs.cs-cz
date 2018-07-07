---
title: Ověřování pomocí Azure Container Registry ze služby Azure Kubernetes
description: Zjistěte, jak zajistit přístup k obrázkům v váš privátní registr kontejnerů ze služby Azure Kubernetes Service pomocí instančního objektu služby Azure Active Directory.
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: marsma
ms.openlocfilehash: 36b66fdcd157e4c44fcd451c8cc07ba68242b583
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888758"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Ověřování pomocí Azure Container Registry ze služby Azure Kubernetes

Pokud používáte Azure Container Registry (ACR) s Azure Kubernetes Service (AKS), je potřeba navázat mechanismus ověřování. Tento dokument podrobně popisuje doporučené konfigurace pro ověřování mezi těmito dvěma službami Azure.

## <a name="grant-aks-access-to-acr"></a>AKS udělit přístup do služby ACR

Když se vytvoří AKS cluster, hlavního názvu služby se vytvoří také spravovat požadavky na provoz clusteru s prostředky Azure. Tento instanční objekt služby je také použít pro ověřování pomocí registru ACR. Uděláte to tak, přiřazení role musí být vytvořen pro udělení přístupu služby instančního objektu pro čtení k prostředku služby ACR.

Následující příklad je možné k dokončení této operace.

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

## <a name="access-with-kubernetes-secret"></a>Přístup pomocí tajného klíče Kubernetes

V některých případech objektu, která je používána ve AKS služby nelze zařadit do oboru do registru ACR. Pro tyto případy můžete vytvořit objekt jedinečné služby a obor do registru ACR.

Tento skript slouží k vytvoření instančního objektu služby.

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

Můžete teď budou ukládat přihlašovací údaje instančního objektu služby v Kubernetes [tajný klíč o přijetí změn image] [ image-pull-secret] a odkazované při spouštění kontejnerů v clusteru AKS.

Následující příkaz vytvoří tajného kódu Kubernetes. Nahraďte název serveru pomocí přihlašovacího serveru ACR, uživatelské jméno id instančního objektu a hesla se heslo instančního objektu služby.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Tajného kódu Kubernetes můžete použít v nasazení pod `ImagePullSecrets` parametru.

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
