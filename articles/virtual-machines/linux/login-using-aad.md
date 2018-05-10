---
title: Přihlaste se k virtuálního počítače s Linuxem pomocí přihlašovacích údajů Azure Active Directory | Microsoft Docs
description: V této postupy zjistíte, jak vytvořit a nakonfigurovat virtuální počítač s Linuxem do Azure Active Directory ověřování používejte pro přihlášení uživatelů
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: iainfou
ms.openlocfilehash: 652f9867b7423ce4307dba1c77e8f38fcd596c67
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Přihlaste se k virtuální počítač s Linuxem v Azure pomocí ověřování Azure Active Directory (Preview)

Pokud chcete zvýšit zabezpečení Linux virtuálních počítačů (VM) v Azure, můžete integrovat s ověřováním Azure Active Directory (AD). Při použití ověřování Azure AD pro virtuální počítače s Linuxem, můžete centrálně řídit a vynucovat zásady, které povolují nebo odepírají přístup k virtuálním počítačům. Tento článek ukazuje, jak vytvořit a nakonfigurovat virtuální počítač s Linuxem na použití ověřování Azure AD.

> [!NOTE]
> Tato funkce je ve verzi preview a nedoporučuje se používat pro použití s provozním virtuálních počítačů nebo úlohy. Pomocí této funkce na testovacím virtuálním počítači, který chcete zahodit po testování.

Existuje mnoho výhod používání ověřování Azure AD zapisovat do protokolu virtuální počítače s Linuxem v Azure, včetně:

- **Vylepšené zabezpečení:**
  - K přihlášení do virtuálních počítačů Linux Azure můžete svoje podnikové přihlašovací údaje AD. Není nutné vytvořit místní účty správců a správa životního cyklu přihlašovacích údajů.
  - Snížením vaší spoléhat na místní účty správců není nutné se obávat přihlašovacích údajů ke ztrátě nebo odcizení, uživatelé konfigurace slabé pověření atd.
  - Složitost hesla a doba platnosti zásady hesel nakonfigurované pro váš adresář Azure AD pomáhají zabezpečené virtuální počítače s Linuxem také.
  - Další zabezpečené přihlášení virtuální počítače Azure můžete nakonfigurovat službu Multi-Factor authentication.

- **Bezproblémová spolupráce:** řízení přístupu With Role-Based (RBAC), můžete určit, kdo může přihlásit ke daný virtuální počítač jako běžný uživatel nebo s oprávněním správce. Při připojení nebo opustit váš tým uživatelů, můžete aktualizovat zásady RBAC pro virtuální počítač k udělení přístupu podle potřeby. Toto prostředí je mnohem jednodušší než museli procházet nástroje virtuálních počítačů k odebrání nepotřebných veřejné klíče SSH. Když zaměstnanci nechte vaší organizace a jejich uživatelský účet je vypnuto nebo odstraněno z Azure AD, mají už přístupu k prostředkům.

### <a name="supported-azure-regions-and-linux-distributions"></a>Podporované distribuce systému Linux a oblastech Azure

Následující Linuxových distribucích jsou aktuálně podporovány ve verzi Preview této funkce:

| Distribuce | Verze |
| --- | --- |
| CentOS | CentOS 6.9 a CentOS 7.4 |
| Red Hat Enterprise Linux | RHEL 7 | 
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 a Ubuntu Server 17.10 |

Ve verzi Preview této funkce jsou aktuálně podporovány následující oblasti:

- Všechny veřejné oblasti Azure

>[!IMPORTANT]
> Chcete-li použít tuto funkci verze preview, se nasadit jenom podporované distro Linux a v podporované oblasti Azure. Funkce není podporována v Azure Government nebo svrchovaných cloudy.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, tento kurz vyžaduje, že používáte Azure CLI verze 2.0.31 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

Vytvořte skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az-group-create), pak vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az-vm-create) pomocí podporovaných distro a v podporované oblasti. Následující příklad nasadí virtuální počítač s názvem *Můjvp* používající *Ubuntu 16.04 LTS* do skupiny prostředků s názvem *myResourceGroup* v *southcentralus*  oblast. V následujících příkladech můžete zadat vlastní skupiny prostředků a názvy virtuálních počítačů podle potřeby.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Jak dlouho trvá několik minut pro vytvoření virtuálního počítače a doprovodné materiály.

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalace Azure AD přihlášení rozšíření virtuálního počítače

K přihlášení do virtuálního počítače s Linuxem pomocí přihlašovacích údajů Azure AD, nainstalujte rozšíření virtuálního počítače v Azure Active Directory protokolu. Rozšíření virtuálního počítače jsou malých aplikacích, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure. Použití [nastavení rozšíření virtuálního az](/cli/azure/vm/extension#az-vm-extension-set) k instalaci *AADLoginForLinux* rozšíření ve virtuálním počítači s názvem *Můjvp* v *myResourceGroup* prostředků Skupina:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ProvisioningState* z *úspěšné* se zobrazí po instalaci rozšíření ve virtuálním počítači.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurace přiřazení rolí pro virtuální počítač.

Azure zásad řízení přístupu na základě rolí (RBAC) určuje, který umožní přihlásit se k virtuálnímu počítači. Dvě role RBAC se používají k autorizaci přihlášení virtuálních počítačů:

- **Přihlášení správce virtuálních počítačů**: uživatelé k této roli přiřadit se můžete přihlásit na virtuální počítač Azure s Správce služby Windows nebo Linux oprávnění uživatele root.
- **Přihlášení uživatele virtuálního počítače**: uživatelé k této roli přiřadit se můžete přihlásit na virtuální počítač Azure s běžná uživatelská oprávnění.

> [!NOTE]
> Chcete-li umožnit uživateli přihlášení k virtuálnímu počítači přes protokol SSH, musíte přiřadit buď *přihlášení správce virtuálních počítačů* nebo *přihlášení uživatele virtuálního počítače* role. Azure uživatele s *vlastníka* nebo *Přispěvatel* role přiřazené pro virtuální počítač automaticky nemají oprávnění k přihlášení k virtuálnímu počítači přes protokol SSH.

Následující příklad používá [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create) přiřadit *přihlášení správce virtuálních počítačů* role virtuálního počítače Azure aktuálního uživatele. Uživatelské jméno účtu Azure active se získávají pomocí [az účet zobrazit](/cli/azure/account#az-account-show)a *oboru* je nastaven na virtuální počítač vytvořený v předchozím kroku s [zobrazit az virtuálních počítačů](/cli/azure/vm#az-vm-show). Oboru může být také přiřazený na úrovni prostředků skupiny nebo předplatného, a použít normální dědičnosti oprávnění RBAC. Další informace najdete v tématu [řízení přístupu na základě rolí](../../azure-resource-manager/resource-group-overview.md#access-control)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

Další informace o tom, jak používat funkci RBAC můžete spravovat přístup k prostředkům na předplatné Azure, najdete v části pomocí [Azure CLI 2.0](../../role-based-access-control/role-assignments-cli.md), [portál Azure](../../role-based-access-control/role-assignments-portal.md), nebo [prostředí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Můžete také nakonfigurovat vyžadovat vícefaktorové ověřování pro určitého uživatele k přihlášení k virtuálnímu počítači Linux Azure AD. Další informace najdete v tématu [Začínáme s Azure Multi-Factor Authentication v cloudu](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Přihlaste se k virtuálnímu počítači Linux

Nejprve zobrazit veřejnou IP adresu vašeho virtuálního počítače s [az virtuálních počítačů zobrazit](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Přihlaste se k virtuálnímu počítači Azure Linux pomocí svých přihlašovacích údajů Azure AD. `-l` Parametr umožňuje určit vlastní adresu účtu služby Azure AD. Zadejte veřejnou IP adresu vašeho virtuálního počítače jako výstup v předchozí příkaz:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Zobrazí se výzva k přihlášení k Azure AD s jednorázové použít kód v [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Zkopírujte a vložte kód jednorázové použití do přihlašovací stránky zařízení, jak je znázorněno v následujícím příkladu:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

Po zobrazení výzvy zadejte své přihlašovací údaje Azure AD na přihlašovací stránku. Tato zpráva se zobrazí ve webovém prohlížeči, když úspěšně jste se ověřili:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Zavřete okno prohlížeče, vraťte se na SSH výzvy a stiskněte klávesu **Enter** klíč. Jste nyní přihlášeni k virtuálnímu počítači Azure Linux pomocí oprávnění role přiřazené, jako například *uživatele virtuálního počítače* nebo *Správce virtuálních počítačů*. Pokud je váš uživatelský účet přiřazený *přihlášení správce virtuálních počítačů* role, můžete použít `sudo` ke spuštění příkazů, které vyžadují oprávnění root.

## <a name="troubleshoot-sign-in-issues"></a>Řešení problémů

Některé běžné chyby při pokusu SSH pomocí přihlašovacích údajů Azure AD patří žádné role RBAC přiřazen a opakuje výzev k přihlášení. Pomocí následujících částí k odstranění těchto problémů.

### <a name="access-denied-rbac-role-not-assigned"></a>Přístup byl odepřen: role RBAC není přiřazen

Pokud se zobrazí následující chyba na řádku vaší SSH, ověřte, zda máte [nakonfigurovali zásady RBAC](#configure-rbac-policy-for-the-virtual-machine) pro virtuální počítač, který uděluje uživatel buď *přihlášení správce virtuálních počítačů* nebo *virtuální Počítač přihlášení uživatele* role:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Trvalá SSH přihlášení výzvy

Pokud úspěšně dokončit krok ověřování ve webovém prohlížeči, může se okamžitě vás se znovu přihlásit s nový kód. Tato chyba je obvykle způsobena neshodu mezi název přihlášení, který jste zadali na řádku SSH a účet, který jste přihlášení ke službě Azure AD s. Chcete-li opravit tento problém:

- Ověřte, zda je správný název přihlášení, který jste zadali na řádku SSH. Máte překlep v názvu přihlášení může způsobit neshodu mezi název přihlášení, který jste zadali na řádku SSH a účet, který jste přihlášení ke službě Azure AD s. Například jste zadali *azuresuer@contoso.onmicrosoft.com* místo *azureuser@contoso.onmicrosoft.com*.
- Pokud máte víc uživatelských účtů, ujistěte se, že neposkytnete jiného uživatelského účtu v okně prohlížeče se při přihlášení do služby Azure AD.
- Linux je malá a velká písmena operační systém. Existuje rozdíl mezi 'Azureuser@contoso.onmicrosoft.com'a'azureuser@contoso.onmicrosoft.com", což může způsobit neshodu. Zajistěte, aby zadejte hlavní název uživatele s správné rozlišování řádku SSH.

## <a name="next-steps"></a>Další postup

Další informace o službě Azure Active Directory najdete v tématu [co je Azure Active Directory](../../active-directory/active-directory-whatis.md) a [jak začít pracovat s Azure Active Directory](../../active-directory/get-started-azure-ad.md)
