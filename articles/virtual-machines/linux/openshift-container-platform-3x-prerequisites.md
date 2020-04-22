---
title: OpenShift Container Platform 3.11 v Azure předpoklady
description: Předpoklady pro nasazení platformy OpenShift Container Platform 3.11 v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 26b190515819378309c2b0705efdbc349ecccbe2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759505"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Běžné předpoklady pro nasazení platformy OpenShift Container Platform 3.11 v Azure

Tento článek popisuje běžné předpoklady pro nasazení OpenShift Container Platform nebo OKD v Azure.

Instalace OpenShift používá Ansible playbooky. Ansible používá Secure Shell (SSH) pro připojení ke všem hostitelům clusteru k dokončení kroků instalace.

Když ansible vytvoří připojení SSH ke vzdáleným hostitelům, nemůže zadat heslo. Z tohoto důvodu soukromý klíč nemůže mít heslo (přístupové heslo) s ním spojené nebo nasazení se nezdaří.

Vzhledem k tomu, že virtuální počítače (VM) nasadit prostřednictvím šablon Azure Resource Manager, stejný veřejný klíč se používá pro přístup ke všem virtuálním počítačům. Odpovídající soukromý klíč musí být na virtuálním počítači, který také provádí všechny playbooky. Chcete-li provést tuto akci bezpečně, trezor klíčů Azure se používá k předání privátního klíče do virtuálního počítače.

Pokud je potřeba trvalé úložiště pro kontejnery, jsou vyžadovány trvalé svazky. OpenShift podporuje virtuální pevné disky Azure (VD) pro trvalé svazky, ale Azure musí být nejprve nakonfigurován jako poskytovatel cloudu.

V tomto modelu OpenShift:

- Vytvoří objekt Virtuálního pevného disku v účtu úložiště Azure nebo na spravovaném disku.
- Připojí virtuální pevný disk k virtuálnímu virtuálnímu virtuálnímu disku a naformátuje svazek.
- Připojí hlasitost k modulu.

Aby tato konfigurace fungovala, openshift potřebuje oprávnění k provádění těchto úloh v Azure. K tomuto účelu se používá instanční objekt. Instanční objekt je účet zabezpečení ve službě Azure Active Directory, který je udělena oprávnění k prostředkům.

Instanční objekt musí mít přístup k účtům úložiště a virtuálním počítačům, které tvoří cluster. Pokud se všechny prostředky clusteru OpenShift nasazují do jedné skupiny prostředků, může být instančnímu objektu udělena oprávnění pro tuto skupinu prostředků.

Tato příručka popisuje, jak vytvořit artefakty spojené s požadavky.

> [!div class="checklist"]
> * Vytvořte trezor klíčů pro správu klíčů SSH pro cluster OpenShift.
> * Vytvořte instanční objekt pro použití poskytovatelem Cloud Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 
Přihlaste se ke svému předplatnému Azure pomocí [příkazu az login](/cli/azure/reference-index) a postupujte podle pokynů na obrazovce, nebo klikněte na **Vyzkoušet,** abyste použili Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. K hostování trezoru klíčů byste měli použít vyhrazenou skupinu prostředků. Tato skupina je oddělená od skupiny prostředků, do které se nasazují prostředky clusteru OpenShift.

Následující příklad vytvoří skupinu prostředků s názvem *keyvaultrg* v umístění *eastus:*

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů
Vytvořte trezor klíčů pro uložení klíčů SSH pro cluster pomocí příkazu [az keyvault create.](/cli/azure/keyvault) Název trezoru klíčů musí být globálně jedinečný a musí být povolen pro nasazení šablony, jinak se nasazení nezdaří s chybou "KeyVaultParameterReferenceSecretRetrieveFailed".

Následující příklad vytvoří trezor klíčů s názvem *keyvault* ve skupině prostředků *keyvaultrg:*

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Vytvoření klíče SSH 
K zabezpečení přístupu ke clusteru OpenShift je nutný klíč SSH. Vytvořte dvojici klíčů SSH pomocí příkazu `ssh-keygen` (v Linuxu nebo macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Váš pár klíčů SSH nemůže mít heslo / přístupové heslo.

Další informace o klíčích SSH v systému Windows naleznete v tématu [Vytvoření klíčů SSH v systému Windows](/azure/virtual-machines/linux/ssh-from-windows). Nezapomeňte exportovat soukromý klíč ve formátu OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Uložení soukromého klíče SSH do azure key vaultu
Nasazení OpenShift používá klíč SSH, který jste vytvořili k zabezpečení přístupu k předloze OpenShift. Chcete-li povolit nasazení bezpečně načíst klíč SSH, uložte klíč v trezoru klíčů pomocí následujícího příkazu:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 
OpenShift komunikuje s Azure pomocí uživatelského jména a hesla nebo instančního objektu služby. Instanční objekt Azure je identita zabezpečení, kterou můžete používat s aplikacemi, službami a automatizačními nástroji, jako je OpenShift. Řídíte a definujete oprávnění, která operace může instanční objekt v Azure provádět. Je vhodné obor oprávnění instanční ho pro konkrétní skupiny prostředků, nikoli celé předplatné.

Vytvořte instanční objekt s [az az ad sp create-for-rbac](/cli/azure/ad/sp) a vyjádřete pověření, která OpenShift potřebuje.

Následující příklad vytvoří instanční objekt a přiřadí mu oprávnění přispěvatele skupině prostředků s názvem *openshiftrg*.

Nejprve vytvořte skupinu prostředků s názvem *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Vytvořit instanční objekt:

```azurecli
az group show --name openshiftrg --query id
```

Uložení výstupu příkazu a použití místo $scope v dalším příkazu

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Poznamenejte si vlastnost appId a heslo vrácené z příkazu:

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
 > Nezapomeňte si zapsat zabezpečené heslo, protože nebude možné toto heslo znovu načíst.

Další informace o instančních objektech najdete [v tématu Vytvoření instančního objektu Azure pomocí azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Požadavky použitelné pouze pro šablonu Správce prostředků

Tajné klíče bude nutné vytvořit pro soukromý klíč SSH (**sshPrivateKey**), tajný klíč klienta Azure AD (**aadClientSecret),** OpenShift admin heslo **(openshiftPassword)** a Red Hat Subscription Manager heslo nebo aktivační klíč (**rhsmPasswordOrActivationKey**).  Navíc pokud jsou použity vlastní certifikáty TLS/SSL, bude nutné vytvořit šest dalších tajných kódů – **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**a **masterkeyfile**.  Tyto parametry budou podrobněji vysvětleny.

Šablona odkazuje na konkrétní tajné názvy, takže **je nutné** použít výše uvedené tučné názvy (malá a velká písmena).

### <a name="custom-certificates"></a>Vlastní certifikáty

Ve výchozím nastavení bude šablona nasazovat cluster OpenShift pomocí certifikátů podepsaných svým držitelem pro webovou konzolu OpenShift a směrovací doménu. Pokud chcete použít vlastní certifikáty TLS/SSL, nastavte "routingCertType" na "custom" a "masterCertType" na "custom".  Pro certifikáty budete potřebovat soubory certifikačníautority, certifikátu a klíče ve formátu .pem.  Je možné použít vlastní certifikáty pro jeden, ale ne druhý.

Tyto soubory budete muset uložit do tajných kódů trezoru klíčů.  Použijte stejný trezor klíčů, jako je ten, který se používá pro soukromý klíč.  Místo toho, aby vyžadovala 6 dalších vstupů pro tajné názvy, je šablona pevně zakódována tak, aby používala konkrétní tajné názvy pro každý soubor certifikátu TLS/SSL.  Data certifikátu uložte pomocí informací z následující tabulky.

| Tajný název      | Soubor certifikátu   |
|------------------|--------------------|
| soubor mastercafile     | hlavní soubor certifikační autority     |
| mastercertfile   | hlavní soubor CERT   |
| soubor masterkey    | hlavní soubor klíče    |
| soubor routingcafile    | směrování souboru certifikační autority    |
| soubor směrovacího postupu  | směrování souboru CERT  |
| routingkeyfile   | soubor směrovacího klíče   |

Vytvořte tajné klíče pomocí azure cli. Níže je uveden příklad.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Další kroky

Tento článek se zabýval následujícími tématy:
> [!div class="checklist"]
> * Vytvořte trezor klíčů pro správu klíčů SSH pro cluster OpenShift.
> * Vytvořte instanční objekt pro použití poskytovatelem cloudových řešení Azure.

Dále nasaďte cluster OpenShift:

- [Nasazení kontejnerové platformy OpenShift](./openshift-container-platform-3x.md)
- [Nasazení nabídky samoobslužné tržiště platformy OpenShift Container Platform](./openshift-container-platform-3x-marketplace-self-managed.md)
