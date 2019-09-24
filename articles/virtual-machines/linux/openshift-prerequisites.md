---
title: OpenShift v Azure – požadavky | Microsoft Docs
description: Požadavky na nasazení OpenShift v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2019
ms.author: haroldw
ms.openlocfilehash: 8dae521902d0568f2d79725bad792d4df64daa1c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204003"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Běžné předpoklady pro nasazení OpenShift v Azure

Tento článek popisuje běžné předpoklady pro nasazení OpenShift kontejnerové platformy nebo OKD v Azure.

Instalace OpenShift používá Ansible playbooky. Ansible používá pro připojení ke všem hostitelům clusteru Secure Shell (SSH) k dokončení kroků instalace.

Když Ansible provede připojení SSH ke vzdáleným hostitelům, nemůže zadat heslo. Z tohoto důvodu privátní klíč nemůže mít přidružené heslo (přístupové heslo), jinak se nasazení nepodaří.

Vzhledem k tomu, že se virtuální počítače nasazují prostřednictvím šablon Azure Resource Manager, používá se pro přístup ke všem virtuálním počítačům stejný veřejný klíč. Odpovídající privátní klíč musí být na virtuálním počítači, který provádí všechny playbooky. K tomu, aby se tato akce provedla bezpečně, se k předání privátního klíče do virtuálního počítače použije Trezor klíčů Azure.

Pokud je pro kontejnery potřeba trvalé úložiště, vyžadují se trvalé svazky. OpenShift podporuje virtuální pevné disky (VHD) Azure pro trvalé svazky, ale Azure musí být nejdřív nakonfigurovaný jako poskytovatel cloudu.

V tomto modelu OpenShift:

- Vytvoří objekt VHD v účtu služby Azure Storage nebo spravovaném disku.
- Připojí VHD k virtuálnímu počítači a naformátuje svazek.
- Připojí svazek k poli pod.

Aby tato konfigurace fungovala, OpenShift potřebuje oprávnění k provádění těchto úloh v Azure. K tomuto účelu se používá objekt služby. Instanční objekt je účet zabezpečení v Azure Active Directory, kterému je uděleno oprávnění k prostředkům.

Instanční objekt musí mít přístup k účtům úložiště a virtuálním počítačům, které tvoří cluster. Pokud se všechny prostředky clusteru OpenShift nasazují do jedné skupiny prostředků, instančnímu objektu se dá udělit oprávnění k této skupině prostředků.

Tato příručka popisuje, jak vytvořit artefakty spojené s požadavky.

> [!div class="checklist"]
> * Vytvořte Trezor klíčů pro správu klíčů SSH pro cluster OpenShift.
> * Vytvoření instančního objektu pro použití poskytovatelem cloudu Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 
Přihlaste se ke svému předplatnému Azure pomocí příkazu [AZ Login](/cli/azure/reference-index) a postupujte podle pokynů na obrazovce, nebo klikněte na tlačítko **vyzkoušet** a použijte Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. K hostování trezoru klíčů byste měli použít vyhrazenou skupinu prostředků. Tato skupina je oddělená od skupiny prostředků, do které se nasazují prostředky clusteru OpenShift.

Následující příklad vytvoří skupinu prostředků s názvem *keyvaultrg* v umístění *eastus* :

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů
Vytvořte Trezor klíčů pro uložení klíčů SSH pro cluster pomocí příkazu [AZ Key trezor Create](/cli/azure/keyvault) . Název trezoru klíčů musí být globálně jedinečný a musí být povolen pro nasazení šablony, jinak se nasazení nezdaří a dojde k chybě "KeyVaultParameterReferenceSecretRetrieveFailed".

Následující příklad vytvoří Trezor klíčů s názvem *trezor* klíčů ve skupině prostředků *keyvaultrg* :

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Vytvoření klíče SSH 
K zabezpečení přístupu ke clusteru OpenShift je nutný klíč SSH. Vytvoření páru klíčů ssh pomocí `ssh-keygen` příkazu (v systému Linux nebo MacOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Váš pár klíčů SSH nemůže mít heslo nebo přístupové heslo.

Další informace o klíčích SSH ve Windows najdete v tématu [vytvoření klíčů ssh ve Windows](/azure/virtual-machines/linux/ssh-from-windows). Nezapomeňte exportovat privátní klíč ve formátu OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Uložení privátního klíče SSH do Azure Key Vault
Nasazení OpenShift používá klíč SSH, který jste vytvořili, abyste zabezpečili přístup k hlavnímu serveru OpenShift. Chcete-li povolit nasazení bezpečně načíst klíč SSH, uložte klíč do Key Vault pomocí následujícího příkazu:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 
OpenShift komunikuje s Azure pomocí uživatelského jména a hesla nebo instančního objektu. Instanční objekt Azure je identita zabezpečení, kterou můžete používat s aplikacemi, službami a nástroji pro automatizaci, jako je OpenShift. Můžete řídit a definovat oprávnění, která může instanční objekt provádět v Azure. Doporučujeme nastavit obor oprávnění instančního objektu na konkrétní skupiny prostředků, a ne na celé předplatné.

Vytvořte instanční objekt pomocí [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp) a zajistěte výstup přihlašovacích údajů, které OpenShift potřebuje.

Následující příklad vytvoří instanční objekt a přiřadí mu oprávnění Přispěvatel pro skupinu prostředků s názvem openshiftrg.

Nejdřív vytvořte skupinu prostředků s názvem openshiftrg:

```azurecli
az group create -l eastus -n openshiftrg
```

Vytvořit instanční objekt:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope \
```
Pokud používáte systém Windows, spusťte ```az group show --name openshiftrg --query id``` a použijte výstup místo $Scope.

Poznamenejte si vlastnost appId vrácenou z příkazu:
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

Další informace o instančních objektech najdete v tématu [Vytvoření instančního objektu Azure pomocí Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Požadavky, které se vztahují pouze na šablonu Správce prostředků

Pro privátní klíč SSH (**sshPrivateKey**) se musí vytvořit tajná hesla, tajný klíč klienta Azure AD (**aadClientSecret**), heslo správce OpenShift (**openshiftPassword**) a heslo správce předplatného Red Hat nebo aktivační klíč ( **rhsmPasswordOrActivationKey**).  Pokud se navíc použijí vlastní certifikáty SSL, musí se vytvořit šest dalších tajných klíčů – **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**a  **masterkeyfile**.  Tyto parametry budou podrobněji vysvětleny.

Šablona odkazuje na konkrétní tajné názvy, takže je **nutné** použít tučné názvy uvedené výše (rozlišuje velká a malá písmena).

### <a name="custom-certificates"></a>Vlastní certifikáty

Ve výchozím nastavení Šablona nasadí cluster OpenShift pomocí certifikátů podepsaných svým držitelem pro webovou konzolu OpenShift a doménu směrování. Pokud chcete používat vlastní certifikáty SSL, nastavte ' routingCertType ' na ' Custom ' a ' masterCertType ' na ' Custom '.  Pro certifikáty budete potřebovat certifikační autoritu, certifikát a soubory klíčů ve formátu. pem.  Je možné použít vlastní certifikáty pro jednu, ale ne druhou.

Tyto soubory budete muset ukládat do Key Vault tajných klíčů.  Použijte stejný Key Vault jako ten, který se používá pro privátní klíč.  Místo toho, aby pro názvy tajných kódů vyžadoval 6 dalších vstupů, je šablona pevně zakódovaná, aby pro každý soubor certifikátu SSL používala konkrétní tajné názvy.  Data certifikátu uložte pomocí informací z následující tabulky.

| Název tajného kódu      | Soubor certifikátu   |
|------------------|--------------------|
| mastercafile     | soubor hlavní certifikační autority     |
| mastercertfile   | hlavní soubor certifikátu   |
| masterkeyfile    | soubor hlavního klíče    |
| routingcafile    | soubor certifikační autority směrování    |
| routingcertfile  | soubor certifikátu směrování  |
| routingkeyfile   | soubor klíče směrování   |

Vytvoření tajných kódů pomocí Azure CLI. Níže je uveden příklad.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Další kroky

Tento článek je popsaný v následujících tématech:
> [!div class="checklist"]
> * Vytvořte Trezor klíčů pro správu klíčů SSH pro cluster OpenShift.
> * Vytvoření instančního objektu pro použití Azure Cloud Solution Provider.

Dále nasaďte cluster OpenShift:

- [Nasazení OpenShift kontejnerové platformy](./openshift-container-platform.md)
- [Nasazení nabídky samoobslužného tržiště spravovaného kontejnerem OpenShift](./openshift-marketplace-self-managed.md)
