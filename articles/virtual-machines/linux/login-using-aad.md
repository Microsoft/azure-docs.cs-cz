---
title: Přihlaste se k virtuálnímu počítači s Linuxem pomocí přihlašovacích údajů Azure Active Directory | Dokumentace Microsoftu
description: V tomto postupy se dozvíte, jak vytvořit a nakonfigurovat virtuální počítač s Linuxem pro přihlášení uživatelů používat ověřování služby Azure Active Directory
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/17/2018
ms.author: cynthn
ms.openlocfilehash: 2ec712dcce1295a91f552176ddcf6572d3f23ecc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993557"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Přihlaste se k virtuální počítač s Linuxem v Azure pomocí ověřování Azure Active Directory (Preview)

Tím se zvyšuje zabezpečení Linuxových virtual machines (VM) v Azure, můžete integrovat s ověřováním Azure Active Directory (AD). Při použití ověřování Azure AD pro virtuální počítače s Linuxem můžete centrálně řídit a vynucovat zásady, které povolují nebo odepírají přístup k virtuálním počítačům. V tomto článku se dozvíte, jak vytvořit a nakonfigurovat virtuální počítač s Linuxem pro použití ověřování Azure AD.

> [!NOTE]
> Tato funkce je ve verzi preview a nedoporučujeme používat s úloh nebo produkčních virtuálních počítačů. Pomocí této funkce na testovací virtuální počítač, který očekáváte, že se zahodit po testování.

Existuje mnoho výhod používání ověřování Azure AD k přihlášení do virtuálních počítačů s Linuxem v Azure, včetně:

- **Vylepšené zabezpečení:**
  - K přihlášení do Azure s Linuxem můžete použít podnikové přihlašovací údaje služby AD. Není nutné vytvořit místní účty správců a správa životního cyklu přihlašovacích údajů.
  - Díky snížení vaše umožňující místní účty správců, není potřeba starat o ztrátu/metodám krádeže přihlašovacích údajů, uživatelé konfigurace slabé pověření atd.
  - Složitost hesla a doby života zásady hesel nakonfigurované pro váš adresář Azure AD pomáhají zabezpečené virtuální počítače s Linuxem i.
  - Na další zabezpečení přihlášení pro virtuální počítače Azure můžete nakonfigurovat ověřování Multi-Factor Authentication.
  - Schopnost přihlášení do virtuálních počítačů s Linuxem pomocí Azure Active Directory funguje i pro zákazníky používající [Federation Services](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Bezproblémová spolupráce:** řízení přístupu With Role-Based (RBAC), můžete určit, kdo může přihlásit k daného virtuálního počítače jako běžný uživatel nebo s oprávněním správce. Když uživatelé připojit nebo opustit váš tým, můžete aktualizovat zásady RBAC pro virtuální počítač k udělení přístupu podle potřeby. Toto prostředí je mnohem jednodušší než by bylo nutné procházení virtuálních počítačů odebrat nepotřebné veřejné klíče SSH. Když zaměstnanci opustí organizaci a jeho uživatelský účet je zakázán nebo odebrány ze služby Azure AD, že už nebude mít přístup k vašim prostředkům.

### <a name="supported-azure-regions-and-linux-distributions"></a>Podporované oblasti Azure a distribucí systému Linux

Následující distribucí systému Linux jsou aktuálně podporované ve verzi preview tato funkce:

| Distribuce | Verze |
| --- | --- |
| CentOS | CentOS 6.9 a CentOS 7.4 |
| Debian | Debian 9 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04, Ubuntu Server 17.10 a Ubuntu Server 18.04 |

Následující oblasti Azure jsou aktuálně podporované ve verzi preview tato funkce:

- Všechny globálními oblastmi Azure

>[!IMPORTANT]
> Pomocí této funkce ve verzi preview, pouze nasaďte podporované distribuce Linuxu a v podporované oblasti Azure. Tato funkce není podporovaná v Azure Government nebo suverénních cloudech.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

Vytvořte skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az-group-create), pak vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az-vm-create) pomocí podporované distribuce a v podporované oblasti. Tento příklad nasadí virtuální počítač s názvem *myVM* , která používá *Ubuntu 16.04 LTS* do skupiny prostředků s názvem *myResourceGroup* v *southcentralus*  oblasti. V následujících příkladech můžete zadat vlastní skupiny prostředků a názvy virtuálních počítačů podle potřeby.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut.

## <a name="install-the-azure-ad-login-vm-extension"></a>Nainstalovat rozšíření virtuálního počítače přihlášení k Azure AD

Přihlásit se k virtuálnímu počítači s Linuxem pomocí přihlašovacích údajů Azure AD, nainstalujte protokolu Azure Active Directory v rozšíření virtuálního počítače. Rozšíření virtuálních počítačů jsou malých aplikací, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure. Použití [sada rozšíření az vm](/cli/azure/vm/extension#az-vm-extension-set) k instalaci *AADLoginForLinux* rozšíření na virtuální počítač s názvem *myVM* v *myResourceGroup* prostředků Skupina:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ProvisioningState* z *Succeeded* se zobrazí po instalaci rozšíření na virtuálním počítači.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurace přiřazení rolí virtuálního počítače

Zásady řízení přístupu na základě rolí (RBAC) Azure určí, kdo může přihlásit k virtuálnímu počítači. Dvě role RBAC se používají k autorizaci přihlášení virtuálního počítače:

- **Přihlášení správce virtuálního počítače**: uživatelé s touto rolí přiřazené přihlásit virtuálního počítače Azure s správce Windows nebo Linuxem kořenovými oprávněními.
- **Přihlášení uživatele virtuálního počítače**: uživatelé s touto rolí přiřazené přihlásit virtuální počítač Azure s běžná uživatelská oprávnění.

> [!NOTE]
> Pokud chcete, aby uživatel pro přihlášení k virtuálnímu počítači přes SSH, je nutné přiřadit buď *přihlášení správce virtuálního počítače* nebo *přihlášení uživatele virtuálního počítače* role. Uživatele s Azure *vlastníka* nebo *Přispěvatel* role přiřazené pro virtuální počítač automaticky nemají oprávnění k přihlášení k virtuálnímu počítači přes SSH.

Následující příklad používá [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create) přiřadit *přihlášení správce virtuálního počítače* role virtuálního počítače Azure aktuálního uživatele. Uživatelské jméno účtu Azure active se získá pomocí [zobrazit účet az](/cli/azure/account#az-account-show)a *oboru* je nastavena na virtuální počítač vytvořený v předchozím kroku s [az vm show](/cli/azure/vm#az-vm-show). Můžou také mít přiřazené oboru na úrovni skupiny nebo předplatného resource a použít normální dědičnost oprávnění RBAC. Další informace najdete v tématu [řízení přístupu na základě rolí](../../azure-resource-manager/resource-group-overview.md#access-control)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Pokud doména AAD a přihlašovací uživatelské jméno domény se neshodují, je nutné zadat ID objektu váš uživatelský účet s *– id objektu pověřené osoby*, ne jenom uživatelské jméno pro *– pověřené osoby*. Můžete získat ID objektu pro váš uživatelský účet s [seznamu uživatelů služby ad az](/cli/azure/ad/user#az-ad-user-list).

Další informace o tom, jak používat ke správě přístupu k prostředkům předplatného Azure RBAC najdete v tématu pomocí [rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md), [webu Azure portal](../../role-based-access-control/role-assignments-portal.md), nebo [prostředí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Azure AD tak, aby vyžadovala vícefaktorové ověřování pro konkrétního uživatele pro přihlášení k virtuálním počítači s Linuxem můžete také nakonfigurovat. Další informace najdete v tématu [Začínáme s Azure Multi-Factor Authentication v cloudu](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Přihlaste se k virtuálním počítači s Linuxem

Nejprve zobrazte veřejnou IP adresu vašeho virtuálního počítače s [az vm show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Přihlaste se k virtuálnímu počítači Azure s Linuxem pomocí svých přihlašovacích údajů Azure AD. `-l` Parametr umožňuje určit vlastní adresu účtu služby Azure AD. Zadejte veřejnou IP adresu vašeho virtuálního počítače jako výstup v předchozím příkazu:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Zobrazí se výzva k přihlášení k Azure AD s jednorázový kód na [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Zkopírujte a vložte jednorázový kód do přihlašovací stránky zařízení, jak je znázorněno v následujícím příkladu:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

Po zobrazení výzvy zadejte své přihlašovací údaje Azure AD na přihlašovací stránku. Následující zpráva je zobrazena ve webovém prohlížeči, když jste úspěšně ověření:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Zavřete okno prohlížeče, vraťte se do SSH výzvy a stiskněte klávesu **Enter** klíč. Jste nyní přihlášeni k virtuálnímu počítači Azure s Linuxem pomocí oprávnění role jako přiřazená, jako například *uživatele virtuálního počítače* nebo *správce virtuálního počítače*. Pokud je váš uživatelský účet přiřazený *přihlášení správce virtuálního počítače* roli, můžete použít `sudo` ke spuštění příkazů, které vyžadují oprávnění root.

## <a name="troubleshoot-sign-in-issues"></a>Řešení potíží s problémy s přihlašováním

Některé běžné chyby při pokusu o SSH pomocí přihlašovacích údajů Azure AD. obsahovat žádné role RBAC přiřazen a opakovat pokynů se přihlaste. Pomocí následujících částí k odstranění těchto problémů.

### <a name="access-denied-rbac-role-not-assigned"></a>Přístup byl odepřen: není přiřazená role RBAC

Pokud se zobrazí následující chyba na příkazovém řádku vašeho SSH, ověřte, že máte [nakonfigurovali zásady RBAC](#configure-rbac-policy-for-the-virtual-machine) pro virtuální počítač, který uděluje uživateli buď *přihlášení správce virtuálního počítače* nebo *virtuální Počítač přihlášení uživatele* role:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Trvalé SSH výzvám k přihlášení

Pokud úspěšně dokončit krok ověřování ve webovém prohlížeči, budete okamžitě vyzváni znovu přihlásil nový kód. Tato chyba je obvykle způsobeno neshodou mezi přihlašovací jméno, které jste zadali do příkazového řádku SSH a účet, který jste přihlášení k Azure AD se službou. K vyřešení tohoto problému:

- Ověřte správnost přihlašovací jméno, které jste zadali do příkazového řádku SSH. Máte překlep v názvu přihlášení může způsobit nesoulad mezi přihlašovací jméno, které jste zadali do příkazového řádku SSH a účet, který jste přihlášení k Azure AD se službou. Například jste zadali *azuresuer@contoso.onmicrosoft.com* místo *azureuser@contoso.onmicrosoft.com*.
- Pokud máte víc uživatelských účtů, ujistěte se, že neposkytnete jiného uživatelského účtu v okně prohlížeče při přihlašování k Azure AD.
- Linux je velká a malá písmena operačního systému. Existuje rozdíl mezi "Azureuser@contoso.onmicrosoft.com"a"azureuser@contoso.onmicrosoft.com", což vedlo k neshodě. Ujistěte se, že je zadat hlavní název uživatele s správné rozlišování řádku SSH.

## <a name="preview-feedback"></a>Zpětnou vazbu ve verzi Preview

Podělte se o této verzi preview funkce nebo sestavu problémy ji váš názor [fóru názorů na Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Další postup

Další informace o Azure Active Directory najdete v tématu [co je Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
