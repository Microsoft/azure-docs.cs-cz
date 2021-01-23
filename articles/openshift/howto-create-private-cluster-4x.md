---
title: Vytvoření privátního clusteru Azure Red Hat OpenShift 4
description: Zjistěte, jak vytvořit privátní cluster Azure Red Hat OpenShift se systémem OpenShift 4
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sakthi-vetrivel
ms.author: suvetriv
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6daeb617e7d1922427fe1889b41512a61c5067cf
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740920"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Vytvoření privátního clusteru Azure Red Hat OpenShift 4

V tomto článku připravíte své prostředí, aby se vytvořily privátní clustery Azure Red Hat OpenShift se systémem OpenShift 4. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nastavení požadavků a vytvoření požadované virtuální sítě a podsítí
> * Nasazení clusteru s privátním koncovým bodem serveru API a privátním adaptérem příchozího přenosu dat

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="before-you-begin"></a>Než začnete

### <a name="register-the-resource-providers"></a>Registrovat poskytovatele prostředků

1. Pokud máte více předplatných Azure, zadejte příslušné ID předplatného:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Zaregistrujte `Microsoft.RedHatOpenShift` poskytovatele prostředků:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```

1. Zaregistrujte `Microsoft.Compute` poskytovatele prostředků:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```

1. Zaregistrujte `Microsoft.Storage` poskytovatele prostředků:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Získání tajného kódu pro vyžádání Red Hat (volitelné)

Tajný kód pro stažení Red Hat umožňuje vašemu clusteru přístup k registrům kontejnerů Red Hat spolu s dalším obsahem. Tento krok je nepovinný, ale doporučuje se.

1. **[Navštivte portál Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) a přihlaste se.**

   Budete se muset přihlásit k účtu Red Hat nebo vytvořit nový účet Red Hat pomocí podnikového e-mailu a přijmout podmínky a ujednání.

2. **Klikněte na Stáhnout tajný klíč pro vyžádání.**

Uložte si uložený `pull-secret.txt` soubor na bezpečném místě, bude se používat při každém vytváření clusteru.

Při spuštění `az aro create` příkazu můžete na svůj tajný kód pro vyžádání obsahu odkazovat pomocí `--pull-secret @pull-secret.txt` parametru. Spusťte `az aro create` z adresáře, kam jste uložili `pull-secret.txt` soubor. V opačném případě nahraďte parametrem `@pull-secret.txt` `@<path-to-my-pull-secret-file` .

Pokud kopírujete tajný kód pro vyžádání obsahu nebo na něj odkazujete v jiných skriptech, měl by váš tajný klíč pro vyžádání formátu obsahovat platný řetězec JSON.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Vytvoření virtuální sítě obsahující dvě prázdné podsítě

V dalším kroku vytvoříte virtuální síť obsahující dvě prázdné podsítě.

1. **Nastavte následující proměnné.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
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

5. **[Zakažte zásady privátního koncového bodu podsítě](../private-link/disable-private-link-service-network-policy.md) v hlavní podsíti.** To je nutné, aby bylo možné připojit a spravovat cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Vytvoření clusteru

Spuštěním následujícího příkazu vytvořte cluster. Volitelně můžete [předat tajný klíč pro vyžádání Red Hat](#get-a-red-hat-pull-secret-optional) , který umožňuje vašemu clusteru přístup k registrům kontejnerů Red Hat spolu s dalším obsahem.

>[!NOTE]
> Pokud kopírujete a vkládáte příkazy a použijete některý z volitelných parametrů, nezapomeňte odstranit počáteční hashtagy a text na konci komentáře. Také uzavřete argument na předchozím řádku příkazu s koncovým zpětným lomítkem.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Po provedení `az aro create` příkazu bude normálně trvat přibližně 35 minut, než se cluster vytvoří.

>[!IMPORTANT]
> Pokud se rozhodnete zadat vlastní doménu, například **foo.example.com**, konzola OpenShift bude k dispozici na adrese URL `https://console-openshift-console.apps.foo.example.com` , jako je místo v předdefinované doméně `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .
>
> Ve výchozím nastavení používá OpenShift certifikáty podepsané svým držitelem pro všechny trasy vytvořené v `*.apps.<random>.<location>.aroapp.io` .  Pokud po připojení ke clusteru zvolíte vlastní DNS, budete se muset podle pokynů v dokumentaci k OpenShift [nakonfigurovat vlastní CA pro váš kontroler](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) příchozího přístupu a [vlastní CA pro váš Server API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

## <a name="connect-to-the-private-cluster"></a>Připojit k privátnímu clusteru

K tomuto clusteru se můžete přihlásit pomocí `kubeadmin` uživatele.  Spusťte následující příkaz a vyhledejte heslo pro `kubeadmin` uživatele.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

Následující příklad výstupu ukazuje, že heslo bude v `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Adresu URL konzoly clusteru můžete najít spuštěním následujícího příkazu, který bude vypadat nějak takto: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Abyste se mohli připojit k privátnímu clusteru Azure Red Hat OpenShift, budete muset provést následující krok z hostitele, který je buď ve Virtual Network, který jste vytvořili, nebo v Virtual Network s [partnerským vztahem](../virtual-network/virtual-network-peering-overview.md) Virtual Network byl nasazen cluster.

V prohlížeči spusťte adresu URL konzoly a přihlaste se pomocí `kubeadmin` přihlašovacích údajů.

![Snímek obrazovky se zobrazením přihlašovací obrazovky Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instalace rozhraní příkazového řádku OpenShift

Po přihlášení ke webové konzole OpenShift klikněte na **?** v pravém horním rohu a pak na **nástrojích příkazového řádku**. Stáhněte si verzi vhodnou pro váš počítač.

![Image zobrazuje přihlašovací obrazovku Azure Red Hat OpenShift](media/aro4-download-cli.png)

Můžete si také stáhnout nejnovější verzi rozhraní příkazového řádku, která je vhodná pro váš počítač <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

## <a name="connect-using-the-openshift-cli"></a>Připojení pomocí rozhraní příkazového řádku OpenShift

Načtěte adresu serveru rozhraní API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Abyste se mohli připojit k privátnímu clusteru Azure Red Hat OpenShift, budete muset provést následující krok z hostitele, který je buď ve Virtual Network, který jste vytvořili, nebo v Virtual Network s [partnerským vztahem](../virtual-network/virtual-network-peering-overview.md) Virtual Network byl nasazen cluster.

Přihlaste se k serveru rozhraní API OpenShift clusteru pomocí následujícího příkazu. Nahraďte **\<kubeadmin password>** heslem, které jste právě načetli.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Další kroky

V tomto článku byl nasazen cluster Azure Red Hat OpenShift se systémem OpenShift 4. Naučili jste se:

> [!div class="checklist"]
> * Nastavení požadavků a vytvoření požadované virtuální sítě a podsítí
> * Nasazení clusteru
> * Připojte se ke clusteru pomocí `kubeadmin` uživatele.

V dalším článku se dozvíte, jak nakonfigurovat cluster pro ověřování pomocí Azure Active Directory.


* [Konfigurace ověřování pomocí Azure Active Directory pomocí příkazového řádku](configure-azure-ad-cli.md)


* [Konfigurace ověřování pomocí Azure Active Directory pomocí webové konzole Azure Portal a OpenShift](configure-azure-ad-cli.md)
