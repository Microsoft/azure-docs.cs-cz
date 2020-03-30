---
title: Vytvoření clusteru Azure Red Hat OpenShift 4.3 | Dokumenty společnosti Microsoft
description: Vytvoření clusteru pomocí Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, červený klobouk, cli
ms.openlocfilehash: 423f09c135da51b8401c1933a4a271d0becd2c8f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349432"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Vytvoření, přístup a správa clusteru Azure Red Hat OpenShift 4.3

> [!IMPORTANT]
> Upozorňujeme, že Azure Red Hat OpenShift 4.3 je momentálně k dispozici jenom v privátní verzi Preview v USA – východ. Přijetí soukromé verze preview je pouze na pozvání. Než se pokusíte povolit tuto funkci: [Azure Red Hat OpenShift Private Preview Registration,](https://aka.ms/aro-preview-register) nezapomeňte si zaregistrovat předplatné.

> [!NOTE]
> Funkce náhledu jsou samoobslužné a jsou poskytovány tak, jak jsou k dispozici a jsou vyloučeny ze smlouvy o úrovni služeb (SLA) a omezené záruky. Proto funkce nejsou určeny pro produkční použití.

## <a name="prerequisites"></a>Požadavky

K vytvoření clusteru Azure Red Hat OpenShift 4.3 budete potřebovat následující:

- Azure CLI verze 2.0.72 nebo vyšší
  
- Rozšíření "az aro"

- Virtuální síť obsahující dvě prázdné podsítě, z nichž každá nemá připojenou žádnou skupinu zabezpečení sítě.  Váš cluster bude nasazen do těchto podsítí.

- Aplikace AAD clusteru (ID klienta a tajný klíč) a `az aro create` instanční objekt služby nebo dostatečná oprávnění AAD pro automatické vytvoření objektu a instančního objektu AAD.

- Instanční objekt RP a objekt instancí clusteru musí mít roli přispěvatele ve virtuální síti clusteru.  Pokud máte roli "Správce uživatelského přístupu" `az aro create` ve virtuální síti, nastaví přiřazení rolí automaticky.

### <a name="install-the-az-aro-extension"></a>Nainstalujte rozšíření 'az aro'
Rozšíření `az aro` umožňuje vytvářet, přistupovat a odstraňovat clustery Azure Red Hat OpenShift přímo z příkazového řádku pomocí azure cli.

> [!Note] 
> Rozšíření `az aro` je aktuální v náhledu. Může být změněnnebo odebrán v budoucí verzi.
> Chcete-li se `az aro` přihlásit k náhledu `Microsoft.RedHatOpenShift` rozšíření, musíte zaregistrovat poskytovatele prostředků.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Přihlásí se k Azure.

   ```console
   az login
   ```

2. Chcete-li nainstalovat rozšíření, spusťte `az aro` následující příkaz:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Ověřte, zda je registrované rozšíření ARO.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Vytvoření virtuální sítě obsahující dvě prázdné podsítě

Následujícím postupem vytvořte virtuální síť obsahující dvě prázdné podsítě.

1. Nastavte následující proměnné.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   PULL_SECRET="<optional-pull-secret>"
   ```
   >[!NOTE]
   > Volitelný tajný klíč pro vyžádat umožňuje clusteru přístup k registrům kontejnerů Red Hat spolu s dalším obsahem.
   >
   > Přístup k tajnému klíči pro vyžádat přejdete na https://cloud.redhat.com/openshift/install/azure/installer-provisioned položku Kopírovat tajný *klíč*a klepnete na něj .
   >
   > Budete se muset přihlásit ke svému účtu Red Hat nebo si vytvořit nový účet Red Hat pomocí firemního e-mailu a přijmout smluvní podmínky.
 

2. Vytvořte skupinu prostředků pro váš cluster.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Vytvořte virtuální síť.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Přidejte do virtuální sítě dvě prázdné podsítě.

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Zakažte zásady sítě pro službu Private Link Service ve virtuální síti a podsítích. Toto je požadavek, aby služba ARO přistupovala ke clusteru a spravovala ho.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Vytvoření clusteru

Chcete-li vytvořit cluster, spusťte následující příkaz.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --pull-secret "$PULL_SECRET"
```

>[!NOTE]
> Vytvoření clusteru obvykle trvá přibližně 35 minut.

## <a name="access-the-cluster-console"></a>Přístup ke konzole clusteru

Adresu URL konzoly clusteru (formuláře) `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`najdete v prostředku clusteru Azure Red Hat OpenShift 4.3. Chcete-li zobrazit prostředek, spusťte následující příkaz:

```console
az aro list -o table
```

Ke clusteru se můžete `kubeadmin` přihlásit pomocí uživatele.  Chcete-li najít heslo pro `kubeadmin` uživatele, spusťte následující příkaz:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Odstranění clusteru

Chcete-li odstranit cluster, spusťte následující příkaz.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
