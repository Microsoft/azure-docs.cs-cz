---
title: Ověření pomocí Azure Container Registry služby Azure Kubernetes
description: Naučte se, jak poskytnout přístup k imagím v privátním registru kontejneru ze služby Azure Kubernetes pomocí instančního objektu služby Azure Active Directory.
services: container-service
author: dlepow
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/27/2019
ms.author: danlep
ms.openlocfilehash: dc5276227913d2da6e52ee3c0fb493b98e86688a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827769"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Ověření pomocí Azure Container Registry služby Azure Kubernetes

Pokud používáte Azure Container Registry (ACR) se službou Azure Kubernetes Service (AKS), je nutné vytvořit ověřovací mechanismus. Tento článek podrobně popisuje doporučené konfigurace pro ověřování mezi těmito dvěma službami Azure.

Musíte nakonfigurovat jenom jednu z těchto metod ověřování. Nejběžnějším přístupem je [udělení přístupu pomocí instančního objektu služby AKS](#grant-aks-access-to-acr). Pokud máte konkrétní potřeby, můžete volitelně [udělit přístup pomocí tajných klíčů Kubernetes](#access-with-kubernetes-secret).

V tomto článku se předpokládá, že jste už vytvořili cluster AKS a máte přístup ke clusteru pomocí klienta příkazového řádku `kubectl`. Pokud místo toho chcete vytvořit cluster a nakonfigurovat přístup k registru kontejneru v době vytváření clusteru, přečtěte si článek [kurz: nasazení clusteru AKS](../aks/tutorial-kubernetes-deploy-cluster.md) nebo [ověření pomocí Azure Container Registry ze služby Azure Kubernetes](../aks/cluster-container-registry-integration.md).

## <a name="grant-aks-access-to-acr"></a>Udělení přístupu AKS k ACR

Když vytvoříte cluster AKS, Azure taky vytvoří instanční objekt, který bude podporovat cluster operability s dalšími prostředky Azure. Tento automaticky generovaný instanční objekt můžete použít k ověřování pomocí registru ACR. K tomu je potřeba vytvořit [přiřazení role](../role-based-access-control/overview.md#role-assignments) Azure AD, které udělí instančnímu objektu clusteru přístup k registru kontejneru.

Pomocí následujícího skriptu udělte instančnímu objektu služby, který je vygenerovaný AKS, přístup k službě Azure Container Registry. Před spuštěním skriptu upravte proměnné `AKS_*` a `ACR_*` pro vaše prostředí.

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
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Přístup s tajným klíčem Kubernetes

V některých případech možná nebudete moci přiřadit požadovanou roli k automaticky generovanému instančnímu objektu AKS, který uděluje přístup k ACR. Například vzhledem k modelu zabezpečení vaší organizace, možná nemáte dostatečná oprávnění ve vašem tenantovi Azure Active Directory pro přiřazení role k instančnímu objektu generovanému službou AKS. Přiřazení role k instančnímu objektu vyžaduje, aby měl váš účet Azure AD oprávnění k zápisu do vašeho tenanta služby Azure AD. Pokud nemáte oprávnění, můžete vytvořit nový instanční objekt a pak mu udělit přístup k registru kontejneru pomocí tajného klíče pro vyžádání obsahu Kubernetes image.

Pomocí následujícího skriptu vytvořte nový instanční objekt (použijete jeho přihlašovací údaje pro získání tajného klíče pro stažení Image Kubernetes). Před spuštěním skriptu upravte proměnnou `ACR_NAME` pro vaše prostředí.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Přihlašovací údaje instančního objektu teď můžete ukládat v [tajném klíči pro vyžádání][image-pull-secret]obsahu Kubernetes image, který váš cluster AKS bude odkazovat na spuštění kontejnerů.

K vytvoření tajného klíče Kubernetes použijte následující příkaz **kubectl** . Nahraďte `<acr-login-server>` plně kvalifikovaným názvem vašeho registru služby Azure Container Registry (ve formátu "acrname.azurecr.io"). Nahraďte `<service-principal-ID>` a `<service-principal-password>` hodnotami, které jste získali spuštěním předchozího skriptu. Nahraďte `<email-address>` libovolnou správně vytvořenou e-mailovou adresou.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

V rámci nasazení teď můžete použít tajný klíč Kubernetes zadáním jeho názvu (v tomto případě "ACR-auth") v parametru `imagePullSecrets`:

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
