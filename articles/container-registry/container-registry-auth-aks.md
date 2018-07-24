---
title: Ověřování pomocí Azure Container Registry ze služby Azure Kubernetes
description: Zjistěte, jak zajistit přístup k obrázkům v váš privátní registr kontejnerů ze služby Azure Kubernetes Service pomocí instančního objektu služby Azure Active Directory.
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/11/2018
ms.author: marsma
ms.openlocfilehash: b56f2a8b2ae8cf04b8c27ab657be3f4d77ee7402
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205387"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Ověřování pomocí Azure Container Registry ze služby Azure Kubernetes

Pokud používáte Azure Container Registry (ACR) s Azure Kubernetes Service (AKS), je potřeba navázat mechanismus ověřování. Tento článek podrobně popisuje doporučené konfigurace pro ověřování mezi těmito dvěma službami Azure.

## <a name="grant-aks-access-to-acr"></a>AKS udělit přístup do služby ACR

Při vytváření clusteru AKS Azure také vytvoří službu objektu zabezpečení pro podporu funkčnost clusteru v jiných prostředcích Azure. Použít tento instanční objekt služby pro automaticky generované pro ověřování pomocí registru služby ACR. K tomu je potřeba vytvořit i službu Azure AD [přiřazení role](../role-based-access-control/overview.md#role-assignment) , který uděluje do registru kontejneru clusteru přístup k hlavní službě.

Pomocí následujícího skriptu pro udělení přístupu instančního objektu služby AKS generované do služby Azure container registry. Upravit `AKS_*` a `ACR_*` proměnné pro vaše prostředí před spuštěním skriptu.

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

V některých případech nemusí být možné přiřadit požadované role automaticky generované AKS instančnímu objektu služby se uděluje přístup do služby ACR. Díky modelu zabezpečení vaší organizace například nemusí mít dostatečná oprávnění v adresáři služby Azure AD k přiřazení role ve službě AKS generovány instančního objektu. V takovém případě můžete vytvořit nový instanční objekt a pak mu udělit přístup k registru kontejnerů pomocí tajného klíče Kubernetes obrázek o přijetí změn.

Pomocí následujícího skriptu vytvořte nový instanční objekt (budete používat svoje přihlašovací údaje pro tajného kódu Kubernetes obrázek o přijetí změn). Upravit `ACR_NAME` proměnné pro vaše prostředí před spuštěním skriptu.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a 'Reader' role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Nyní můžete ukládat přihlašovací údaje instančního objektu v Kubernetes [tajný klíč o přijetí změn image][image-pull-secret], který bude odkazovat na clusteru AKS při spuštěné kontejnery.

Pomocí následujících **kubectl** příkaz pro vytvoření tajného kódu Kubernetes. Nahraďte `<acr-login-server>` s plně kvalifikovaným názvem vašeho registru kontejneru Azure (to je ve formátu "acrname.azurecr.io"). Nahraďte `<service-principal-ID>` a `<service-principal-password>` hodnotami, které jste získali pomocí skriptu pro předchozí.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Nyní můžete tajného kódu Kubernetes v podu nasazení tak, že zadáte jeho název (v tomto případě "acr vícefaktorového ověřování") v `imagePullSecrets` parametr:

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
