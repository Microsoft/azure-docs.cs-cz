---
title: Vytvoření třídy Azure Files Storage v Azure Red Hat OpenShiftu 4
description: Zjistěte, jak vytvořit Azure Files StorageClass v Azure Red Hat OpenShift
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI, Azure File
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: daaf6d3042b3b8d050173a87d94f0ff6a4ccb2c7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633253"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Vytvoření třídy Azure Files Storage v Azure Red Hat OpenShiftu 4

V tomto článku vytvoříte StorageClass pro Azure Red Hat OpenShift 4, který dynamicky zřídí úložiště ReadWriteMany (RWX) pomocí služby soubory Azure. Co se naučíte:

> [!div class="checklist"]
> * Nastavte požadované součásti a nainstalujte potřebné nástroje.
> * Vytvoření Azure Red Hat OpenShift 4 StorageClass se službou Azure File provision

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Než začnete

Nasazení clusteru Azure Red Hat OpenShift 4 do předplatného najdete v tématu [Vytvoření clusteru Azure Red Hat OpenShift 4](tutorial-create-cluster.md) .


### <a name="set-up-azure-storage-account"></a>Nastavení účtu Azure Storage

V tomto kroku se vytvoří skupina prostředků mimo skupinu prostředků clusteru Azure Red Hat OpenShift (ARO). Tato skupina prostředků bude obsahovat sdílené složky Azure Files, které vytvořil dynamický zřizovací modul Azure Red Hat OpenShift.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Nastavení oprávnění
### <a name="set-resource-group-permissions"></a>Nastavení oprávnění skupiny prostředků

Instanční objekt služby ARO vyžaduje oprávnění ' klíče listkey ' pro novou skupinu prostředků účtu úložiště Azure. Přiřaďte k tomuto účelu roli Přispěvatel.

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor --assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>Nastavení oprávnění clusteru ARO

Účet služby pořadače trvalého svazku OpenShift bude vyžadovat možnost čtení tajných kódů. K tomuto účelu vytvořte a přiřaďte roli clusteru OpenShift.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Vytváření StorageClass pomocí služby Azure Files zřídí

V tomto kroku se vytvoří StorageClass se službou Azure Files zřídí. V manifestu StorageClass jsou vyžadovány podrobnosti účtu úložiště, aby cluster ARO věděl, že se podívá na účet úložiště mimo aktuální skupinu prostředků.

Při zřizování úložiště se pro přihlašovací údaje pro připojení vytvoří tajný kód s názvem Secret. V kontextu s více architekturami se důrazně doporučuje nastavit hodnotu pro secretNamespace explicitně, jinak přihlašovací údaje účtu úložiště můžou přečíst jiní uživatelé.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  secretNamespace: kube-system
  skuName: Standard_LRS
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>Změna výchozího StorageClass (volitelné)

Výchozí StorageClass v ARO společnosti se nazývá Managed-Premium a používá službu Azure-disk Provisioning. Toto změňte vyvoláním příkazů patch na manifesty StorageClass.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Ověření služby Azure File StorageClass (volitelné)

Vytvořte novou aplikaci a přiřaďte k ní úložiště.

```bash
oc new-project azfiletest
oc new-app -template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
Soubor test.txt bude také viditelný prostřednictvím Průzkumník služby Storage v Azure Portal.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili dynamické trvalé úložiště pomocí souborů Microsoft Azure a Azure Red Hat OpenShift 4. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření účtu úložiště
> * Konfigurace StorageClass v clusteru Azure Red Hat OpenShift 4 pomocí služby Azure Files zřídí

V dalším článku se dozvíte víc o podporovaných prostředcích Azure Red Hat OpenShift 4.

* [Zásady podpory Azure Red Hat OpenShift](support-policies-v4.md)
