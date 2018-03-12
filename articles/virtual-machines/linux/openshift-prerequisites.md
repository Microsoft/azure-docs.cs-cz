---
title: "OpenShift v Azure požadavky | Microsoft Docs"
description: "Požadavky na nasazení OpenShift v Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 178f4d6dab5c87c9c53ab87c4a3b1edcfa61c221
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Běžné požadavky pro nasazení OpenShift v Azure

Tento článek popisuje běžné požadavky pro nasazení OpenShift původ nebo OpenShift kontejneru platformy v Azure.

Instalace OpenShift používá Ansible playbooks. Ansible Secure Shell (SSH) používá k připojení na všech hostitelích clusteru k dokončení kroky instalace.

Při zahájení připojení SSH na vzdáleného hostitele, nelze zadat heslo. Z tohoto důvodu privátní klíč nemůže mít heslo s ním spojená nebo nasazení se nezdaří.

Protože virtuální počítače (VM) nasadit prostřednictvím šablon Azure Resource Manageru, stejný veřejný klíč se používá pro přístup ke všem virtuálním počítačům. Je třeba vložit do virtuálních počítačů, které provádí také všechny playbooks odpovídajícího privátního klíče. K tomuto účelu bezpečně pomocí klíče trezoru služby Azure lze předat privátní klíč do virtuálního počítače.

Pokud je třeba pro trvalé úložiště pro kontejnery, trvalé svazky jsou vyžadovány. OpenShift podporuje Azure virtuální pevné disky (VHD) pro tuto funkci, ale Azure musí být nejprve nakonfigurován jako zprostředkovatele cloudu. 

V tomto modelu OpenShift:

- Vytvoří objekt virtuálního pevného disku v účtu Azure Storage.
- Připojí VHD do virtuálního počítače a naformátování svazku.
- Připojí svazek pod.

Tato konfigurace fungovat musí OpenShift oprávnění k provedení předchozí úlohy v Azure. Můžete dosáhnout s hlavní službou. Instanční objekt je účet zabezpečení v Azure Active Directory, který má uděleno oprávnění k prostředkům.

Instanční objekt musí mít přístup k účtům úložiště a virtuálních počítačů, které tvoří clusteru. Pokud všechny prostředky clusteru OpenShift nasadíte do jedna skupina prostředků, můžete objekt služby udělit oprávnění k této skupině zdrojů.

Tato příručka popisuje, jak vytvořit artefakty související s požadavky.

> [!div class="checklist"]
> * Vytvoření trezoru klíčů pro správu klíčů SSH OpenShift clusteru.
> * Vytvořte objekt služby pro použití v Azure Cloud Solution Provider.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 
Přihlaste se k předplatnému Azure s [az přihlášení](/cli/azure/reference-index#az_login) příkazů a postupujte podle na obrazovce pokynů nebo klikněte na **vyzkoušet** používat cloudové prostředí.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Použijte skupinu prostředků vyhrazené pro hostování trezoru klíčů. Tato skupina je oddělené od skupiny prostředků, do kterého prostředků clusteru OpenShift nasazení. 

Následující příklad vytvoří skupinu prostředků s názvem *keyvaultrg* v *eastus* umístění:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů
Vytvoření trezoru klíčů pro ukládání klíčů SSH pro cluster s [vytvořit az keyvault](/cli/azure/keyvault#az_keyvault_create) příkaz. Název trezoru klíčů musí být globálně jedinečný.

Následující příklad vytvoří trezoru klíčů s názvem *keyvault* v *keyvaultrg* skupiny prostředků:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Vytvoření klíče SSH 
Klíč SSH je potřeba zabezpečit přístup k počátku OpenShift clusteru. Vytvořte dvojici klíčů SSH pomocí `ssh-keygen` (v systému macOS nebo Linux):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Heslo nemůže mít dvojici klíčů SSH.

Další informace o klíče SSH v systému Windows najdete v tématu [vytvoření SSH klíčů v systému Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Uložit privátní klíč SSH v Azure Key Vault
Nasazení OpenShift používá klíč SSH, které jste vytvořili pro zabezpečený přístup k hlavnímu serveru OpenShift. Pokud chcete povolit nasazení tak, aby bezpečně načítat klíč SSH, uložení klíče v Key Vault pomocí následujícího příkazu:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 
OpenShift komunikuje s Azure pomocí uživatelského jména a hesla nebo hlavní název služby. Objektu zabezpečení služby Azure je identita zabezpečení, která můžete použít s aplikací, služeb a automatizace nástroje, například OpenShift. Můžete řídit a definovat oprávnění, které operace můžete provádět objektu služby v Azure. Pokud chcete zvýšit zabezpečení nad rámec právě poskytnutí uživatelského jména a hesla, tento příklad vytvoří základní služby hlavní.

Vytvoření služby hlavní s [az ad sp vytvořit pro rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) a přihlašovací údaje, které potřebuje OpenShift výstup.

Následující příklad vytvoří službu objektu zabezpečení a přiřadí ji k skupinu prostředků s názvem myResourceGroup oprávnění přispěvatele. Pokud používáte Windows, spouštění ```az group show --name myResourceGroup --query id``` samostatně a použít výstup ke kanálu možnost--obory.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Poznamenejte si vlastnost appId vrácená z tohoto příkazu:
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
 > Ujistěte se, že vytvoření zabezpečeného hesla. Postupujte podle pokynů v tématu [Pravidla a omezení pro hesla Azure AD](/azure/active-directory/active-directory-passwords-policy).

Další informace o objekty služby najdete v tématu [vytvořit objekt služby Azure pomocí Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Další postup

Tento článek popsané v následujících tématech:
> [!div class="checklist"]
> * Vytvoření trezoru klíčů pro správu klíčů SSH OpenShift clusteru.
> * Vytvořte objekt služby pro použití v Azure Cloud Solution Provider.

V dalším kroku nasazení clusteru OpenShift:

- [Nasazení OpenShift původu](./openshift-origin.md)
- [Nasazení OpenShift kontejneru platformy](./openshift-container-platform.md)

