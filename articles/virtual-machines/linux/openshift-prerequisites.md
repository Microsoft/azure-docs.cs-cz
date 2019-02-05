---
title: OpenShift v Azure požadavků | Dokumentace Microsoftu
description: Požadavky na nasazení OpenShift v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 25ec82c923ebe322194d868159332ef145727999
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692986"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Běžné požadavky pro nasazení OpenShift v Azure

Tento článek popisuje běžné požadavky pro nasazení OpenShift Container Platform nebo OKD v Azure.

Instalace OpenShift používá runbooků Ansible. Ansible používá Secure Shell (SSH) pro připojení na všechny hostitele clusterů, dokončete kroky instalace.

Když ansible zahájí připojení SSH ke vzdáleným hostitelům, nelze zadat heslo. Z tohoto důvodu se privátní klíč nemůže mít heslo (heslo), s ním spojená nebo nasazení se nezdaří.

Protože virtuální počítače (VM) nasazovat pomocí šablon Azure Resource Manageru, stejný veřejný klíč se používá pro přístup ke všem virtuálním počítačům. Potřebujete připojení k virtuálnímu počítači, který se spustí i všechny playbooky vkládat odpovídající privátní klíč. K tomuto účelu bezpečně použít trezor klíčů Azure k předávání privátní klíč k virtuálnímu počítači.

Pokud je potřeba jako trvalé úložiště pro kontejnery, trvalé svazky jsou povinné. OpenShift podporuje Azure virtuálních pevných disků (VHD) pro tuto funkci, ale Azure je nutné nejprve konfigurovat jako poskytovatele cloudu.

V tomto modelu OpenShift:

- Vytvoří objekt VHD v účtu služby Azure Storage nebo spravovaný disk.
- Připojí VHD k virtuálnímu počítači a naformátuje svazek.
- Připojí svazek pod.

Pro tuto konfiguraci pro práci OpenShift potřebuje oprávnění k provedení těchto úloh v Azure. Můžete toho dosáhnout pomocí instančního objektu. Instanční objekt je účet zabezpečení v Azure Active Directory, která jsou udělena oprávnění k prostředkům.

Instanční objekt musí mít přístup k účtům úložiště a virtuální počítače, které tvoří cluster. Pokud všechny prostředky clusteru OpenShift nasadit do jedné skupiny prostředků, instanční objekt můžete udělit oprávnění této skupiny prostředků.

Tato příručka popisuje, jak vytvořit artefakty spojené s požadavky.

> [!div class="checklist"]
> * Vytvoření trezoru klíčů pro správu klíčů SSH pro OpenShift cluster.
> * Vytvoření instančního objektu pro použití poskytovatelem cloudu Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure 
Přihlaste se k předplatnému Azure pomocí [az login](/cli/azure/reference-index) příkaz a postupujte podle pokynů na obrazovce pokynů nebo klikněte na tlačítko **vyzkoušet** použití Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Doporučujeme použít skupiny vyhrazených prostředků pro hostování služby key vault. Tato skupina je oddělené od skupiny prostředků, do které prostředky clusteru Openshiftu nasazovat.

Následující příklad vytvoří skupinu prostředků s názvem *keyvaultrg* v *eastus* umístění:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů
Vytvoření trezoru klíčů pro ukládání klíčů SSH pro cluster s [az keyvault vytvořit](/cli/azure/keyvault) příkazu. Název trezoru klíčů musí být globálně jedinečný.

Následující příklad vytvoří trezor klíčů s názvem *keyvault* v *keyvaultrg* skupina prostředků:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Vytvoření klíče SSH 
Klíč SSH je potřeba k zabezpečení přístupu ke clusteru OpenShift. Vytvoření páru klíčů SSH s použitím `ssh-keygen` příkaz (v systému Linux nebo macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Páru klíčů SSH nemůže mít heslo nebo přístupové heslo.

Další informace o klíče SSH ve Windows najdete v tématu [vytvoření SSH klíčů ve Windows](/azure/virtual-machines/linux/ssh-from-windows). Je nutné exportovat privátní klíč ve formátu.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Store privátní klíč SSH ve službě Azure Key Vault
Nasazení Openshiftu používá klíč SSH, které jste vytvořili pro zabezpečený přístup k hlavnímu serveru OpenShift. Pokud chcete povolit nasazení bezpečně načítat klíč SSH, uložení klíče ve službě Key Vault pomocí následujícího příkazu:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 
OpenShift komunikuje s Azure pomocí uživatelského jména a hesla nebo instanční objekt služby. Instanční objekt Azure je identita zabezpečení, který vám pomůže s aplikací, služeb a nástrojů automatizace, jako je OpenShift. Kontrolou a můžete definovat oprávnění, operace, které můžete provádět instanční objekt služby v Azure. Doporučujeme rozsah oprávnění pro instanční objekt na určitých skupinách prostředků a místo celé předplatné.

Vytvořit instanční objekt s [az ad sp create-for-rbac](/cli/azure/ad/sp) tak za výstupní přihlašovací údaje, které potřebuje OpenShift.

Následující příklad vytvoří službu objektu zabezpečení a přiřadí ji do skupiny prostředků s názvem openshiftrg oprávnění přispěvatele.

Nejprve vytvořte skupinu prostředků s názvem openshiftrg:

```azurecli
az group create -l eastus -n openshiftrg
```

Vytvoření instančního objektu:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Pokud používáte Windows, spusťte ```az group show --name openshiftrg --query id``` a použít výstup místo $scope.

Poznamenejte si vlastnost appId vrácenou příkazem:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Nezapomeňte vytvořit zabezpečené heslo. Postupujte podle pokynů v tématu [Pravidla a omezení pro hesla Azure AD](/azure/active-directory/active-directory-passwords-policy).

Další informace o instančních objektů najdete v tématu [vytvoření instančního objektu Azure pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Další postup

Tento článek popisuje v následujících tématech:
> [!div class="checklist"]
> * Vytvoření trezoru klíčů pro správu klíčů SSH pro OpenShift cluster.
> * Vytvoření instančního objektu pro použití Azure Cloud Solution Provider.

V dalším kroku nasaďte OpenShift cluster:

- [Nasazení OpenShift Container Platform](./openshift-container-platform.md)
- [Nasazení OKD](./openshift-okd.md)
