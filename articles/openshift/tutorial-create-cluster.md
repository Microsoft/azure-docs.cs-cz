---
title: Kurz – vytvoření clusteru Azure Red Hat OpenShift 4
description: Zjistěte, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí Azure CLI.
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d9b02c11c055b4b072c5f8a1ff47e44001ec4580
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509716"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Kurz: Vytvoření clusteru Azure Red Hat OpenShift 4

V tomto kurzu, který je první částí tři, připravíte své prostředí, aby se vytvořil cluster Azure Red Hat OpenShift se systémem OpenShift 4, a vytvořte cluster. V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení požadavků a vytvoření požadované virtuální sítě a podsítí
> * Nasazení clusteru

## <a name="before-you-begin"></a>Před zahájením

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.75 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="install-the-az-aro-extension"></a>Nainstalovat `az aro` rozšíření
`az aro` Rozšíření umožňuje vytvořit, otevřít a odstranit clustery Azure Red Hat OpenShift přímo z příkazového řádku pomocí Azure CLI.

Spusťte následující příkaz pro instalaci `az aro` rozšíření.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Pokud už máte rozšíření nainstalované, můžete ho aktualizovat spuštěním následujícího příkazu.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Dál je potřeba zaregistrovat poskytovatele `Microsoft.RedHatOpenShift` prostředků ve vašem předplatném.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Ověřte, zda je rozšíření registrováno.

```azurecli-interactive
az -v
```

  Měl by se zobrazit výstup podobný následujícímu.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Získání tajného kódu pro vyžádání Red Hat (volitelné)

Tajný kód pro stažení Red Hat umožňuje vašemu clusteru přístup k registrům kontejnerů Red Hat spolu s dalším obsahem. Tento krok je nepovinný, ale doporučuje se.

Získání tajného kódu pro vyžádání obsahu získáte https://cloud.redhat.com/openshift/install/azure/aro-provisioned tak, že přejdete na *Stáhnout tajný kód pro vyžádání*obsahu.

Budete se muset přihlásit k účtu Red Hat nebo vytvořit nový účet Red Hat pomocí podnikového e-mailu a přijmout podmínky a ujednání.

Uložte si uložený `pull-secret.txt` soubor na bezpečném místě, bude se používat při každém vytváření clusteru.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Vytvoření virtuální sítě obsahující dvě prázdné podsítě

V dalším kroku vytvoříte virtuální síť obsahující dvě prázdné podsítě.

1. **Nastavte následující proměnné.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Vytvoření skupiny prostředků**

    Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. V tomto umístění se ukládají metadata skupin prostředků, a to i v případě, že se vaše prostředky spouštějí v Azure, pokud při vytváření prostředků nezadáte jinou oblast. Vytvořte skupinu prostředků pomocí příkazu [az Group Create] [az-Group-Create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    Následující příklad výstupu ukazuje, že skupina prostředků byla úspěšně vytvořena:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Vytvořte virtuální síť.**

    Clustery Azure Red Hat OpenShift se systémem OpenShift 4 vyžadují pro hlavní a pracovní uzly virtuální síť se dvěma prázdnými podsítěmi.

    Vytvořte novou virtuální síť ve stejné skupině prostředků, kterou jste vytvořili dříve.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    Následující příklad výstupu ukazuje, že virtuální síť byla úspěšně vytvořena:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Přidejte prázdnou podsíť pro hlavní uzly.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Přidejte prázdnou podsíť pro pracovní uzly.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Zakažte zásady privátního koncového bodu podsítě](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) v hlavní podsíti.** To je nutné, aby bylo možné připojit a spravovat cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Vytvoření clusteru

Spuštěním následujícího příkazu vytvořte cluster. Volitelně můžete předat tajný klíč pro vyžádání obsahu, který umožňuje vašemu clusteru přístup k registrům kontejnerů Red Hat spolu s dalším obsahem. Přejděte do [Správce clusteru Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) a kliknutím na **Kopírovat tajný kód pro vyžádání**přístupu na svůj tajný klíč.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> Vytvoření clusteru obvykle trvá přibližně 35 minut.

>[!IMPORTANT]
> Pokud se rozhodnete zadat vlastní doménu, například **foo.example.com**, konzola OpenShift bude k dispozici na adrese URL `https://console-openshift-console.apps.foo.example.com`, jako je místo v předdefinované doméně. `https://console-openshift-console.apps.<random>.<location>.aroapp.io`
>
> Ve výchozím nastavení používá OpenShift certifikáty podepsané svým držitelem pro všechny trasy vytvořené v `*.apps.<random>.<location>.aroapp.io`nástroji.  Pokud se po připojení ke clusteru rozhodnete použít vlastní DNS, budete muset postupovat podle dokumentace OpenShift a [nakonfigurovat vlastní CA pro váš kontroler](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) příchozího přístupu a [vlastní CA pro váš Server API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Nastavení požadavků a vytvoření požadované virtuální sítě a podsítí
> * Nasazení clusteru

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Připojení ke clusteru Azure Red Hat OpenShift](tutorial-connect-cluster.md)
