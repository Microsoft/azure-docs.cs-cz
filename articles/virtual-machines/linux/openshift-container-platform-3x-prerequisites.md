---
title: OpenShift kontejnerová platforma 3,11 v požadavcích Azure
description: Požadavky na nasazení OpenShift kontejneru platformy 3,11 v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 51f6a2ac4f524ac2a504fb8e0c3dd90ec25c9f93
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734726"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Běžné požadavky pro nasazení OpenShift kontejneru Platform 3,11 v Azure

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

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.
Vytvořte Trezor klíčů pro uložení klíčů SSH pro cluster pomocí příkazu [AZ Key trezor Create](/cli/azure/keyvault) . Název trezoru klíčů musí být globálně jedinečný a musí být povolen pro nasazení šablony, jinak se nasazení nezdaří a dojde k chybě "KeyVaultParameterReferenceSecretRetrieveFailed".

Následující příklad vytvoří Trezor klíčů s názvem *trezor* klíčů ve skupině prostředků *keyvaultrg* :

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Vytvoření klíče SSH 
K zabezpečení přístupu ke clusteru OpenShift je nutný klíč SSH. Vytvoření páru klíčů SSH pomocí `ssh-keygen` příkazu (v systému Linux nebo MacOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Váš pár klíčů SSH nemůže mít heslo nebo přístupové heslo.

Další informace o klíčích SSH ve Windows najdete v tématu [vytvoření klíčů ssh ve Windows](./ssh-from-windows.md). Nezapomeňte exportovat privátní klíč ve formátu OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Uložení privátního klíče SSH do Azure Key Vault
Nasazení OpenShift používá klíč SSH, který jste vytvořili, abyste zabezpečili přístup k hlavnímu serveru OpenShift. Chcete-li povolit nasazení bezpečně načíst klíč SSH, uložte klíč do Key Vault pomocí následujícího příkazu:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 
OpenShift komunikuje s Azure pomocí uživatelského jména a hesla nebo instančního objektu. Instanční objekt Azure je identita zabezpečení, kterou můžete používat s aplikacemi, službami a nástroji pro automatizaci, jako je OpenShift. Můžete řídit a definovat oprávnění, která může instanční objekt provádět v Azure. Doporučujeme nastavit obor oprávnění instančního objektu na konkrétní skupiny prostředků, a ne na celé předplatné.

Vytvořte instanční objekt pomocí [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp) a zajistěte výstup přihlašovacích údajů, které OpenShift potřebuje.

Následující příklad vytvoří instanční objekt a přiřadí mu oprávnění Přispěvatel pro skupinu prostředků s názvem *openshiftrg*.

Nejdřív vytvořte skupinu prostředků s názvem *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Vytvořit instanční objekt:

```azurecli
az group show --name openshiftrg --query id
```

Uložit výstup příkazu a použít místo $scope v příkazu Next

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Poznamenejte si vlastnost appId a heslo vrácené příkazem:

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
 > Nezapomeňte si zapsat zabezpečené heslo, protože ho nebude možné znovu načíst.

Další informace o instančních objektech najdete v tématu [Vytvoření instančního objektu Azure pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Požadavky, které se vztahují pouze na šablonu Správce prostředků

Pro privátní klíč SSH (**sshPrivateKey**) se musí vytvořit tajné kódy, tajný klíč klienta Azure AD (**aadClientSecret**), heslo správce OpenShift (**openshiftPassword**) a heslo správce předplatného Red Hat (**rhsmPasswordOrActivationKey**).  Pokud se navíc použijí vlastní certifikáty TLS/SSL, musí se vytvořit šest dalších tajných klíčů – **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile** a **masterkeyfile**.  Tyto parametry budou podrobněji vysvětleny.

Šablona odkazuje na konkrétní tajné názvy, takže je **nutné** použít tučné názvy uvedené výše (rozlišuje velká a malá písmena).

### <a name="custom-certificates"></a>Vlastní certifikáty

Ve výchozím nastavení Šablona nasadí cluster OpenShift pomocí certifikátů podepsaných svým držitelem pro webovou konzolu OpenShift a doménu směrování. Pokud chcete použít vlastní certifikáty TLS/SSL, nastavte ' routingCertType ' na ' Custom ' a ' masterCertType ' na ' Custom '.  Pro certifikáty budete potřebovat certifikační autoritu, certifikát a soubory klíčů ve formátu. pem.  Je možné použít vlastní certifikáty pro jednu, ale ne druhou.

Tyto soubory budete muset ukládat do Key Vault tajných klíčů.  Použijte stejný Key Vault jako ten, který se používá pro privátní klíč.  Místo toho, aby pro názvy tajných kódů vyžadoval 6 dalších vstupů, je šablona pevně zakódována pro použití určitých tajných názvů pro každý soubor certifikátu TLS/SSL.  Data certifikátu uložte pomocí informací z následující tabulky.

| Název tajného kódu      | Soubor certifikátu   |
|------------------|--------------------|
| mastercafile     | soubor hlavní certifikační autority     |
| mastercertfile   | hlavní soubor certifikátu   |
| masterkeyfile    | soubor hlavního klíče    |
| routingcafile    | soubor certifikační autority směrování    |
| routingcertfile  | soubor certifikátu směrování  |
| routingkeyfile   | soubor klíče směrování   |

Vytvoření tajných kódů pomocí Azure CLI. Níže je uveden příklad.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Další kroky

Tento článek je popsaný v následujících tématech:
> [!div class="checklist"]
> * Vytvořte Trezor klíčů pro správu klíčů SSH pro cluster OpenShift.
> * Vytvoření instančního objektu pro použití Azure Cloud Solution Provider.

Dále nasaďte cluster OpenShift:

- [Nasazení OpenShift kontejnerové platformy](./openshift-container-platform-3x.md)
- [Nasazení OpenShift kontejnerové platformy Self-Managed nabídky Marketplace](./openshift-container-platform-3x-marketplace-self-managed.md)
