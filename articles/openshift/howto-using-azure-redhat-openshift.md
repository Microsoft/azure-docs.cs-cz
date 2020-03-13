---
title: Vytvoření clusteru Azure Red Hat OpenShift 4,3 | Microsoft Docs
description: Vytvoření clusteru pomocí Azure Red Hat OpenShift 4,3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.openlocfilehash: 23d7c950396c36925ce50d746195916292d360ad
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79201038"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Vytvoření, přístup a Správa clusteru Azure Red Hat OpenShift 4,3

> [!IMPORTANT]
> Upozorňujeme, že Azure Red Hat OpenShift 4,3 je teď k dispozici jenom v privátní verzi Preview v Východní USA. Přijetí privátní verze Preview je jenom na pozvání. Před pokusem o povolení této funkce nezapomeňte zaregistrovat své předplatné: [registrace Azure Red Hat OpenShift Private Preview](https://aka.ms/aro-preview-register)

> [!NOTE]
> Funkce ve verzi Preview jsou samoobslužné a jsou k dispozici, jak jsou dostupné a jsou vyloučené z smlouvy o úrovni služeb (SLA) a omezené záruky. Proto funkce nejsou určeny pro použití v produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

K vytvoření clusteru Azure Red Hat OpenShift 4,3 budete potřebovat následující:

- Azure CLI verze 2.0.72 nebo vyšší
  
- Přípona AZ ARO

- Virtuální síť obsahující dvě prázdné podsítě, z nichž každá nemá připojenou skupinu zabezpečení sítě.  Cluster se nasadí do těchto podsítí.

- Aplikace AAD clusteru (ID klienta a tajný klíč) a instanční objekt nebo dostatečná oprávnění služby AAD pro `az aro create` pro automatické vytvoření aplikace AAD a instančního objektu.

- Instanční objekt a instanční objekt služby RP musí mít roli Přispěvatel ve virtuální síti clusteru.  Pokud máte ve virtuální síti roli správce přístupu uživatele, `az aro create` nastaví automatické přiřazení rolí.

### <a name="install-the-az-aro-extension"></a>Instalace rozšíření AZ ARO
Rozšíření `az aro` umožňuje vytvořit, otevřít a odstranit clustery Azure Red Hat OpenShift přímo z příkazového řádku pomocí Azure CLI.

> [!Note] 
> Rozšíření `az aro` je aktuální ve verzi Preview. Může být v budoucí verzi změněno nebo odebráno.
> Pokud se chcete přihlásit ke službě `az aro` Extension Preview, musíte zaregistrovat `Microsoft.RedHatOpenShift` poskytovatele prostředků.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Přihlásí se k Azure.

   ```console
   az login
   ```

2. Spusťte následující příkaz pro instalaci rozšíření `az aro`:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Ověřte, zda je rozšíření ARO společnosti registrováno.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Vytvoření virtuální sítě obsahující dvě prázdné podsítě

Pomocí těchto kroků vytvoříte virtuální síť obsahující dvě prázdné podsítě.

1. Nastavte následující proměnné.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Vytvořte skupinu prostředků pro svůj cluster.

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

5. Zakažte zásady sítě pro službu privátního propojení ve vaší virtuální síti a podsítích. To je požadavek, aby služba ARO měla přístup ke clusteru a jeho správě.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Vytvoření clusteru

Spuštěním následujícího příkazu vytvořte cluster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> Vytvoření clusteru obvykle trvá přibližně 35 minut.

## <a name="access-the-cluster-console"></a>Přístup ke konzole clusteru

Adresu URL konzoly clusteru (z formuláře `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) najdete v prostředku clusteru Azure Red Hat OpenShift 4,3. Spusťte následující příkaz pro zobrazení prostředku:

```console
az aro list -o table
```

Ke clusteru se můžete přihlásit pomocí `kubeadmin`ho uživatele.  Spusťte následující příkaz, který zjistí heslo pro `kubeadmin`ho uživatele:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Odstranění clusteru

Spuštěním následujícího příkazu odstraňte cluster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```