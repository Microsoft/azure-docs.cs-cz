---
title: Přihlášení k virtuálnímu počítači s Linuxem pomocí přihlašovacích údajů služby Azure Active Directory
description: Přečtěte si, jak vytvořit a nakonfigurovat virtuální počítač s Linuxem pro přihlášení pomocí ověřování Azure Active Directory.
author: iainfoulds
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: 2731693667d2129a72da72455c6bbdd74c277697
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366494"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Náhled: Přihlášení k virtuálnímu počítači Linuxu v Azure pomocí ověřování Azure a služby Azure

Chcete-li zlepšit zabezpečení virtuálních počítačů Linuxu (VM) v Azure, můžete integrovat s ověřováním Azure Active Directory (AD). Když používáte ověřování Azure AD pro virtuální počítače s Linuxem, centrálně řídíte a vynucujete zásady, které umožňují nebo odepírají přístup k virtuálním počítačům. Tento článek ukazuje, jak vytvořit a nakonfigurovat virtuální počítač s Linuxem používat ověřování Azure AD.


> [!IMPORTANT]
> Ověřování azure active directory je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Tuto funkci použijte na testovacím virtuálním počítači, který očekáváte, že po testování zahodíte.
>


Existuje mnoho výhod používání ověřování Azure AD pro přihlášení k virtuálním počítačům SIO v Azure, včetně:

- **Lepší zabezpečení:**
  - K přihlášení k virtuálním počítačům Azure Linux u dál můžete použít svá podniková pověření služby AD. Není nutné vytvářet účty místního správce a spravovat životnost pověření.
  - Snížením vaší závislosti na účtech místních správců se nemusíte obávat ztráty/krádeže přihlašovacích údajů, konfigurace slabých přihlašovacích údajů apod.
  - Zásady složitosti hesla a doby platnosti hesla nakonfigurované pro váš adresář Azure AD pomáhají zabezpečit virtuální počítače s Linuxem.
  - Chcete-li dále zabezpečit přihlášení k virtuálním počítačům Azure, můžete nakonfigurovat vícefaktorové ověřování.
  - Možnost přihlásit se k virtuálním mům Linux pomocí služby Azure Active Directory funguje také pro zákazníky, kteří používají [službu Federation Services](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Bezproblémová spolupráce:** Pomocí řízení přístupu na základě rolí (RBAC) můžete určit, kdo se může přihlásit k danému virtuálnímu virtuálnímu ms jako běžný uživatel nebo s oprávněními správce. Když se uživatelé připojí nebo opustí váš tým, můžete aktualizovat zásady RBAC pro virtuální počítač udělit přístup podle potřeby. Toto prostředí je mnohem jednodušší než nutnost kodstranění virtuálních počítače odebrat zbytečné ssh veřejné klíče. Když zaměstnanci opustí vaši organizaci a jejich uživatelský účet je zakázán nebo odebrán z Azure AD, už nemají přístup k vašim prostředkům.

## <a name="supported-azure-regions-and-linux-distributions"></a>Podporované oblasti Azure a distribuce Linuxu

Následující distribuce Linuxu jsou v současné době podporovány během náhledu této funkce:

| Distribuce | Version |
| --- | --- |
| CentOS | Centos 6, Centos 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Skok 42,3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 a Ubuntu Server 18.04 |


Během náhledu této funkce jsou aktuálně podporované následující oblasti Azure:

- Všechny globální oblasti Azure

>[!IMPORTANT]
> Chcete-li použít tuto funkci náhledu, nasadit pouze podporované distribuce Linuxu a v podporované oblasti Azure. Tato funkce není podporovaná v Azure Government nebo suverénní cloudy.
>
> Není podporováno používat toto rozšíření v clusterech služby Azure Kubernetes Service (AKS). Další informace naleznete v [tématu Zásady podpory pro AKS](../../aks/support-policies.md).


Pokud se rozhodnete nainstalovat a používat příkazcli místně, tento kurz vyžaduje, abyste spouštěli Azure CLI verze 2.0.31 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Síťové požadavky

Pokud chcete povolit ověřování Azure AD pro vaše virtuální počítače s Linuxem v Azure, musíte zajistit, aby konfigurace sítě virtuálních počítačů umožňovala odchozí přístup k následujícím koncovým bodům přes port TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> V současné době nelze nakonfigurovat skupiny zabezpečení sítě Azure pro virtuální počítače povolené ověřováním Azure AD.

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

Vytvořte skupinu prostředků s [vytvořením skupiny az](/cli/azure/group#az-group-create), pak vytvořte virtuální ho s [vytvořením virtuálního virtuálního virtuálního modulu apomocí](/cli/azure/vm#az-vm-create) podporované ho distro a v podporované oblasti. Následující příklad nasazuje virtuální hod s názvem *myVM,* který používá *Ubuntu 16.04 LTS* do skupiny prostředků s názvem *myResourceGroup* v oblasti *southcentralus.* V následujících příkladech můžete podle potřeby zadat vlastní skupinu prostředků a názvy virtuálních počítačů.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalace rozšíření virtuálního počítače pro přihlášení služby Azure AD

> [!NOTE]
> Pokud nasazení tohoto rozšíření na dříve vytvořený virtuální počítač ujistěte se, že počítač má alespoň 1 GB paměti přidělené jinak rozšíření se nezdaří nainstalovat

Pokud se chcete přihlásit k virtuálnímu počítači SYP s přihlašovacími údaji Azure AD, nainstalujte rozšíření přihlašovacího virtuálního počítače Služby Azure Active Directory. Rozšíření virtuálních počítačů jsou malé aplikace, které poskytují úlohy konfigurace a automatizace po nasazení na virtuálních počítačích Azure. Pomocí [sady rozšíření az vm](/cli/azure/vm/extension#az-vm-extension-set) nainstalujte rozšíření *AADLoginForLinux* na virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ZřizováníState* *Succeeded* se zobrazí po úspěšné instalaci rozšíření na virtuální ms. Virtuální ho virtuálního provozu potřebuje spuštěného agenta virtuálního aplikace k instalaci rozšíření. Další informace najdete v tématu [Přehled agenta virtuálního zařízení](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurace přiřazení rolí pro virtuální počítače

Zásady řízení přístupu na základě rolí Azure (RBAC) určuje, kdo se může přihlásit k virtuálnímu počítači. Dvě role RBAC se používají k autorizaci přihlášení virtuálního mísy:

- **Přihlášení správce virtuálního počítače**: Uživatelé s přiřazenou touto rolí se můžou přihlásit k virtuálnímu počítači Azure s oprávněními správce Windows nebo Root root.
- **Přihlášení uživatele virtuálního počítače**: Uživatelé s přiřazenou touto rolí se můžou přihlásit k virtuálnímu počítači Azure s běžnými uživatelskými oprávněními.

> [!NOTE]
> Chcete-li uživateli povolit přihlášení k virtuálnímu počítači přes SSH, musíte přiřadit roli *Přihlášení správce virtuálního počítače* nebo Přihlášení uživatele *virtuálního počítače.* Uživatel Azure s rolemi *vlastníka* nebo *přispěvatele* přiřazené pro virtuální počítač nemají automaticky oprávnění k přihlášení k virtuálnímu počítači přes SSH.

Následující příklad používá [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create) k přiřazení role *Přihlášení správce virtuálního počítače* virtuálnímu počítači pro aktuálního uživatele Azure. Uživatelské jméno vašeho aktivního účtu Azure se získá pomocí [az účtu show](/cli/azure/account#az-account-show)a *obor* je nastaven na virtuální počítač vytvořený v předchozím kroku s [az vm show](/cli/azure/vm#az-vm-show). Obor může být také přiřazen na úrovni skupiny prostředků nebo předplatného a platí normální oprávnění dědičnosti RBAC. Další informace naleznete [v tématu Ovládací prvky přístupu založené na rolích](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Pokud se vaše doména AAD a přihlašovací uživatelské jméno domény neshodují, musíte zadat ID objektu vašeho uživatelského účtu s *id --assignee-object-id*, nikoli pouze uživatelské jméno pro *--assignee*. ID objektu můžete získat pro svůj uživatelský účet pomocí [seznamu uživatelů reklamy az](/cli/azure/ad/user#az-ad-user-list).

Další informace o tom, jak používat RBAC ke správě přístupu k prostředkům předplatného Azure, najdete v tématu pomocí [Azure CLI](../../role-based-access-control/role-assignments-cli.md), [Portál Azure](../../role-based-access-control/role-assignments-portal.md)nebo [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Azure AD můžete také nakonfigurovat tak, aby vyžadovalo vícefaktorové ověřování pro konkrétního uživatele, aby se mohl přihlásit k virtuálnímu počítači SIP. Další informace najdete [v tématu Začínáme s Azure Multi-Factor Authentication v cloudu](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Přihlášení k virtuálnímu počítači S Linuxem

Nejprve zobrazte veřejnou IP adresu virtuálního počítače s [az vm show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Přihlaste se k virtuálnímu počítači Azure Linux pomocí přihlašovacích údajů Azure AD. Parametr `-l` umožňuje zadat vlastní adresu účtu Azure AD. Nahraďte ukázkový účet svým vlastním. Adresy účtů by měly být zadány ve všech složených písmenech. Nahraďte ukázkovou IP adresu veřejnou IP adresou virtuálního počítače z předchozího příkazu.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Budete vyzváni k přihlášení k Azure AD s jednorázovým kódem použití na . [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) Zkopírujte a vložte kód jednorázového použití na přihlašovací stránku zařízení.

Po zobrazení výzvy zadejte přihlašovací údaje Azure AD na přihlašovací stránce. 

Po úspěšném ověření se ve webovém prohlížeči zobrazí ve webovém prohlížeči následující zpráva:`You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Zavřete okno prohlížeče, vraťte se na výzvu SSH a stiskněte klávesu **Enter.** 

Teď jste přihlášení k virtuálnímu počítači Azure Linux s přiřazenými oprávněními role, jako je *například uživatel virtuálního počítače* nebo *správce virtuálního počítače*. Pokud je vašemu uživatelskému účtu přiřazena role `sudo` Přihlášení správce *virtuálního počítače,* můžete použít ke spuštění příkazů, které vyžadují oprávnění root.

## <a name="sudo-and-aad-login"></a>Přihlášení Sudo a AAD

Při prvním spuštění sudo, budete vyzváni k ověření podruhé. Pokud nechcete, aby se znovu ověřit spustit sudo, můžete upravit soubor `/etc/sudoers.d/aad_admins` sudoers a nahradit tento řádek:

```bash
%aad_admins ALL=(ALL) ALL
```

S tímto řádkem:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Poradce při potížích s přihlášením

Některé běžné chyby při pokusu o SSH s přihlašovacími údaji Azure AD zahrnují žádné přiřazené role RBAC a opakované výzvy k přihlášení. Tyto problémy opravte v následujících částech.

### <a name="access-denied-rbac-role-not-assigned"></a>Přístup byl odepřen: Role RBAC nebyla přiřazena.

Pokud se na vaší výzvě SSH zobrazí následující chyba, ověřte, že jste nakonfigurovali zásady RBAC pro virtuální počítač, které uživateli udělují roli *Přihlášení správce virtuálního počítače* nebo Přihlášení uživatele *virtuálního počítače:*

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Continued SSH přihlašovací výzvy

Pokud úspěšně dokončíte krok ověřování ve webovém prohlížeči, můžete být okamžitě vyzváni k opětovnému přihlášení pomocí nového kódu. Tato chyba je obvykle způsobena neshodou mezi přihlašovacím jménem, které jste zadali na výzvu SSH a účtem, se kterým jste se přihlásili k Azure AD. Chcete-li tento problém opravit:

- Ověřte, zda je přihlašovací jméno zadané na výzvu SSH správné. Překlep v přihlašovacím názvu může způsobit neshodu mezi přihlašovacím jménem, které jste zadali na výzvu SSH a účtem, se kterým jste se přihlásili k Azure AD. Například jste zadali *azuresuer\@contoso.onmicrosoft.com* namísto *azureuser\@contoso.onmicrosoft.com*.
- Pokud máte více uživatelských účtů, ujistěte se, že při přihlašování do Azure AD neposkytujete jiný uživatelský účet v okně prohlížeče.
- Linux je operační systém rozlišující malá a velká písmena. Mezi "Azureuser@contoso.onmicrosoft.coma "azureuser@contoso.onmicrosoft.comje rozdíl, který může způsobit neshodu. Ujistěte se, že zadáte hlavní název onn se správnou rozlišenou malá a velká písmena na ssh řádku.

### <a name="other-limitations"></a>Další omezení

Uživatelé, kteří dědí přístupová práva prostřednictvím vnořených skupin nebo přiřazení rolí, nejsou aktuálně podporováni. Uživateli nebo skupině musí být přímo [přiřazena požadovaná přiřazení rolí](#configure-role-assignments-for-the-vm). Například použití skupin pro správu nebo vnořených přiřazení rolí skupiny neudělí správná oprávnění, která by uživateli umožnila přihlášení.

## <a name="preview-feedback"></a>Zpětná vazba na verzi Preview

Podělte se o své názory týkající se této funkce náhledu nebo sestavy problémy pomocí na [fóru pro zpětnou vazbu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Active Directory najdete v tématu [Co je Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
