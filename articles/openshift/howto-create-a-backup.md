---
title: Vytvoření zálohování clusterové aplikace Azure Red Hat OpenShift 4 pomocí Velero
description: Naučte se vytvořit zálohu aplikací clusteru Azure Red Hat OpenShift pomocí Velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 49ffc33310564299131e2831b74154719b7cf7c7
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078574"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Vytvoření zálohování clusterové aplikace Azure Red Hat OpenShift 4

V tomto článku připravíte své prostředí, aby se vytvořila záloha aplikace clusteru Azure Red Hat OpenShift 4. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nastavte požadované součásti a nainstalujte potřebné nástroje.
> * Vytvoření zálohy aplikace Azure Red Hat OpenShift 4

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Než začnete

### <a name="install-velero"></a>Nainstalovat Velero

Pokud chcete [nainstalovat](https://velero.io/docs/main/basic-install/) Velero do svého systému, postupujte podle doporučeného postupu pro váš operační systém.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Nastavení účtu úložiště Azure a kontejneru objektů BLOB

V tomto kroku se vytvoří skupina prostředků mimo skupinu prostředků clusteru ARO.  Tato skupina prostředků umožní zálohování zachovat a může obnovit aplikace do nových clusterů.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Nastavení oprávnění pro Velero

### <a name="create-service-principal"></a>Vytvoření instančního objektu

Velero potřebuje oprávnění k zálohování a obnovení. Při vytváření instančního objektu udělíte Velero oprávnění pro přístup ke skupině prostředků, kterou jste definovali v předchozím kroku. Tento krok zobrazí skupinu prostředků clusteru:

```bash
export AZURE_RESOURCE_GROUP=aro-$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r '.clusterProfile.domain')
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Instalace Velero do clusteru Azure Red Hat OpenShift 4

Tento krok nainstaluje Velero do vlastního projektu a [vlastní definice prostředků](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) potřebné k zálohování a obnovení pomocí Velero. Ujistěte se, že jste úspěšně přihlášení ke clusteru Azure Red Hat OpenShift v4.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Vytvoření zálohy pomocí Velero

Pokud chcete vytvořit zálohu aplikace pomocí Velero, budete muset zahrnout obor názvů, ve kterém je tato aplikace.  Pokud máte `nginx-example` obor názvů a chcete zahrnout všechny prostředky v tomto oboru názvů do zálohy, spusťte v terminálu následující příkaz:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
Stav zálohování můžete zjistit spuštěním:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Úspěšná záloha bude mít výstup `phase:Completed` a objekty budou v kontejneru v účtu úložiště aktivní.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Vytvoření zálohy s Velero pro zahrnutí snímků

Pokud chcete vytvořit zálohu aplikace pomocí Velero, aby zahrnovala trvalé svazky vaší aplikace, budete muset zahrnout obor názvů, ve kterém je aplikace, a `snapshot-volumes=true` při vytváření zálohy přidat příznak.

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

Stav zálohování můžete zjistit spuštěním:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Úspěšná záloha s výstupem `phase:Completed` a objekty budou v kontejneru v účtu úložiště aktivní.

Další informace o tom, jak vytvářet zálohy a obnovování pomocí Velero, najdete v tématu [zálohování prostředků záložních OpenShift nativním způsobem](https://www.openshift.com/blog/backup-openshift-resources-the-native-way) .

## <a name="next-steps"></a>Další kroky

V tomto článku se zazálohovali Clusterová aplikace Azure Red Hat OpenShift 4. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření zálohy clusterové aplikace OpenShift v4 pomocí Velero
> * Vytvoření zálohy clusterové aplikace OpenShift v4 pomocí snímků pomocí Velero


V dalším článku se dozvíte, jak vytvořit obnovení clusterové aplikace Azure Red Hat OpenShift 4.

* [Vytvoření clusteru Azure Red Hat OpenShift 4 – obnovení clusterové aplikace](howto-create-a-restore.md)
* [Vytvoření clusterové aplikace Azure Red Hat OpenShift 4 obnovení zahrnující snímky](howto-create-a-restore.md)
