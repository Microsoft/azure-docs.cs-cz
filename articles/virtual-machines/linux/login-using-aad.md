---
title: Přihlášení k virtuálnímu počítači se systémem Linux s přihlašovacími údaji Azure Active Directory
description: Naučte se vytvořit a nakonfigurovat virtuální počítač Linux pro přihlášení pomocí Azure Active Directory ověřování.
author: SanDeo-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: sandeo
ms.openlocfilehash: 882df9d0dcb01d6321455b845fed087a5e14ccc6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016161"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Verze Preview: přihlášení k virtuálnímu počítači se systémem Linux v Azure pomocí ověřování Azure Active Directory

Pokud chcete zlepšit zabezpečení virtuálních počítačů se systémem Linux v Azure, můžete je integrovat s ověřováním Azure Active Directory (AD). Když použijete ověřování Azure AD pro virtuální počítače se systémem Linux, centrálně ovládáte a vynutili zásady, které povolují nebo odmítnou přístup k virtuálním počítačům. V tomto článku se dozvíte, jak vytvořit a nakonfigurovat virtuální počítač Linux pro použití ověřování Azure AD.


> [!IMPORTANT]
> Ověřování Azure Active Directory je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Tuto funkci můžete použít na testovacím virtuálním počítači, který po testování očekáváte zrušit.
>


K přihlášení k virtuálním počítačům se systémem Linux v Azure využíváme mnoho výhod, včetně:

- **Vylepšené zabezpečení:**
  - K přihlášení k virtuálním počítačům Azure Linux můžete použít přihlašovací údaje podnikového AD. Není nutné vytvářet účty místních správců a spravovat dobu života přihlašovacích údajů.
  - Omezením závislosti na účtech místních správců nemusíte dělat starosti se ztrátou a krádeží přihlašovacích údajů, uživatelé konfigurují slabé přihlašovací údaje atd.
  - Zásady složitosti hesla a životního cyklu hesla nakonfigurované pro váš adresář služby Azure AD pomůžou zabezpečit také virtuální počítače se systémem Linux.
  - K dalšímu zabezpečení přihlášení k virtuálním počítačům Azure můžete nakonfigurovat službu Multi-Factor Authentication.
  - Možnost přihlášení k virtuálním počítačům Linux pomocí Azure Active Directory funguje taky pro zákazníky, kteří používají [federační služby](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Bezproblémová spolupráce:** Díky řízení přístupu na základě role Azure (Azure RBAC) můžete určit, kdo se může přihlásit k danému virtuálnímu počítači jako běžný uživatel nebo s oprávněními správce. Když se uživatelé připojí nebo odejdou z týmu, můžete podle potřeby aktualizovat zásady Azure RBAC pro virtuální počítač a udělit tak přístup. Toto prostředí je mnohem jednodušší než nutnosti odstraňovat virtuální počítače, aby nedošlo k odebrání nepotřebných veřejných klíčů SSH. Když zaměstnanci odejdou z vaší organizace a jejich uživatelský účet je v Azure AD zakázaný nebo odebraný, už nebudou mít přístup k vašim prostředkům.

## <a name="supported-azure-regions-and-linux-distributions"></a>Podporované oblasti Azure a distribuce Linux

Během verze Preview této funkce se aktuálně podporují následující distribuce systému Linux:

| Distribuce | Verze |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE, přestupné 42,3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14,04 LTS, Ubuntu Server 16,04 a Ubuntu Server 18,04 |


Ve verzi Preview této funkce se aktuálně podporují tyto oblasti Azure:

- Všechny globální oblasti Azure

>[!IMPORTANT]
> Pokud chcete tuto funkci ve verzi Preview používat, nasaďte jenom podporované distribuce Linux a v podporované oblasti Azure. Tato funkce není podporována v cloudech Azure Government nebo svrchovan.
>
> Používání tohoto rozšíření v clusterech Azure Kubernetes Service (AKS) se nepodporuje. Další informace najdete v tématu [zásady podpory pro AKS](../../aks/support-policies.md).


Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.31 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Požadavky sítě

Pokud chcete povolit ověřování Azure AD pro virtuální počítače se systémem Linux v Azure, musíte zajistit, aby konfigurace sítě virtuálních počítačů povolovala odchozí přístup k následujícím koncovým bodům přes port TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /Device.Login.microsoftonline.com
* https: \/ /pas.Windows.NET
* https:\//management.azure.com
* https: \/ /Packages.Microsoft.com

> [!NOTE]
> V současné době není možné nakonfigurovat skupiny zabezpečení sítě Azure pro virtuální počítače, které jsou povolené pomocí ověřování Azure AD.

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create)a pak vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az-vm-create) pomocí podporovaného distribuce a v podporované oblasti. Následující příklad nasadí virtuální počítač s názvem *myVM* , který používá *Ubuntu 16,04 LTS* do skupiny prostředků s názvem *myResourceGroup* v oblasti *southcentralus* . V následujících příkladech můžete podle potřeby zadat vlastní skupinu prostředků a názvy virtuálních počítačů.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalace rozšíření pro přihlášení k virtuálnímu počítači Azure AD

> [!NOTE]
> Pokud toto rozšíření nasadíte do dříve vytvořeného virtuálního počítače, zajistěte, aby měl počítač alespoň 1 GB přidělené paměti, jinak se rozšíření nepodaří nainstalovat.

Pokud se chcete přihlásit k virtuálnímu počítači se systémem Linux s přihlašovacími údaji služby Azure AD, nainstalujte Azure Active Directory rozšíření VM pro přihlášení. Rozšíření virtuálních počítačů jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace na virtuálních počítačích Azure. Pomocí [AZ VM Extension set](/cli/azure/vm/extension#az-vm-extension-set) nainstalujete rozšíření *AADLoginForLinux* na virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* :

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ProvisioningState* *se po* úspěšné instalaci rozšíření na virtuálním počítači zobrazí. Pro instalaci rozšíření potřebuje virtuální počítač spuštěného agenta virtuálního počítače. Další informace najdete v tématu [Přehled agenta virtuálního počítače](../extensions/agent-windows.md).

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurace přiřazení rolí pro virtuální počítač

Zásady řízení přístupu na základě role Azure (Azure RBAC) určují, kdo se může přihlásit k virtuálnímu počítači. K autorizaci přihlášení k virtuálnímu počítači se používají dvě role Azure:

- **Přihlášení správce virtuálního počítače**: uživatelé s touto rolí se můžou přihlašovat k virtuálnímu počítači Azure pomocí oprávnění správce Windows nebo kořenového uživatele Linux.
- **Přihlášení uživatele k virtuálnímu počítači**: uživatelé s touto rolí se můžou přihlašovat k virtuálnímu počítači Azure s pravidelnými uživatelskými oprávněními.

> [!NOTE]
> Pokud chcete uživateli dovolit, aby se přihlásil k VIRTUÁLNÍmu počítači přes SSH, musíte přiřadit buď roli *přihlášení správce virtuálního počítače* , nebo *přihlašovací údaje uživatele virtuálního počítače* . Uživatel Azure s rolemi *vlastník* nebo *Přispěvatel* přiřazený k virtuálnímu počítači nemá automaticky oprávnění k přihlášení k virtuálnímu počítači přes SSH.

V následujícím příkladu se pomocí funkce [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) přiřadí k virtuálnímu počítači role *přihlášení správce virtuálního počítače* pro aktuálního uživatele Azure. Uživatelské jméno vašeho aktivního účtu Azure se získá pomocí [AZ Account show](/cli/azure/account#az-account-show)a *obor* se nastaví na virtuální počítač vytvořený v předchozím kroku pomocí [AZ VM show](/cli/azure/vm#az-vm-show). Obor se taky dá přiřadit na úrovni skupiny prostředků nebo předplatného a platí normální oprávnění dědičnosti Azure RBAC. Další informace najdete v tématu [Azure RBAC](../../role-based-access-control/overview.md) .

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Pokud se vaše doména AAD a doména přihlašovacího jména uživatele neshodují, je nutné zadat ID objektu vašeho uživatelského účtu pomocí *--zmocněnce-Object-ID*, nikoli jenom pomocí uživatelského jména pro- *-nabyvatele*. ID objektu pro svůj uživatelský účet můžete získat pomocí [seznamu AZ AD User list](/cli/azure/ad/user#az-ad-user-list).

Další informace o tom, jak pomocí Azure RBAC spravovat přístup k prostředkům předplatného Azure, najdete v tématu použití rozhraní příkazového [řádku Azure](../../role-based-access-control/role-assignments-cli.md), [Azure Portal](../../role-based-access-control/role-assignments-portal.md)nebo [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Službu Azure AD můžete také nakonfigurovat tak, aby vyžadovala službu Multi-Factor Authentication pro konkrétního uživatele, aby se přihlásil k virtuálnímu počítači se systémem Linux. Další informace najdete v tématu [Začínáme se službou Azure AD Multi-Factor Authentication v cloudu](../../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Přihlášení k virtuálnímu počítači se systémem Linux

Nejprve zobrazte veřejnou IP adresu vašeho virtuálního počítače pomocí [AZ VM show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Přihlaste se k virtuálnímu počítači Azure Linux pomocí svých přihlašovacích údajů Azure AD. `-l`Parametr umožňuje zadat vlastní adresu účtu služby Azure AD. Nahraďte vzorový účet vlastním. Adresy účtu by měly být zadány pouze malými písmeny. Z předchozího příkazu nahraďte ukázkovou IP adresu veřejnou IP adresou vašeho virtuálního počítače.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Budete vyzváni k přihlášení ke službě Azure AD s jednorázovým kódem použití na adrese [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Zkopírujte a vložte kód jednorázového použití do přihlašovací stránky zařízení.

Po zobrazení výzvy zadejte přihlašovací údaje služby Azure AD přihlašovací údaje na přihlašovací stránce. 

Ve webovém prohlížeči se po úspěšném ověření zobrazí následující zpráva: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Zavřete okno prohlížeče, vraťte se do výzvy SSH a stiskněte klávesu **ENTER** . 

Nyní jste přihlášeni k virtuálnímu počítači Azure Linux s oprávněními role přiřazenou, jako je například *uživatel virtuálního počítače* nebo *Správce virtuálního počítače*. Pokud je vašemu uživatelskému účtu přiřazená role *přihlášení správce virtuálního počítače* , můžete použít `sudo` ke spuštění příkazů, které vyžadují oprávnění root.

## <a name="sudo-and-aad-login"></a>Přihlášení sudo a AAD

Při prvním spuštění sudo se zobrazí výzva k ověření druhého času. Pokud se pro spuštění sudo nechcete znovu přihlásit, můžete upravit soubor sudoers `/etc/sudoers.d/aad_admins` a nahradit tento řádek:

```bash
%aad_admins ALL=(ALL) ALL
```

S tímto řádkem:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Řešení potíží s přihlašováním

Některé běžné chyby při pokusu o přihlášení SSH s přihlašovacími údaji služby Azure AD zahrnují žádné přiřazené role Azure a opakované výzvy k přihlášení. Tyto problémy opravíte pomocí následujících částí.

### <a name="access-denied-azure-role-not-assigned"></a>Přístup byl odepřen: role Azure nebyla přiřazena.

Pokud se v příkazovém řádku SSH zobrazí následující chyba, ověřte, že jste pro virtuální počítač nakonfigurovali zásady Azure RBAC, které udělí uživateli buď *přihlašovací jméno správce virtuálního počítače* , nebo roli *přihlášení uživatele virtuálního počítače* :

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Pokračování přihlašovacích výzev protokolu SSH

Pokud jste úspěšně dokončili krok ověřování ve webovém prohlížeči, můžete se okamžitě zobrazit výzva, abyste se znovu přihlásili pomocí nového kódu. Tato chyba je obvykle způsobena neshodou mezi přihlašovacím jménem, které jste zadali v příkazovém řádku SSH, a účtem, ke kterému jste se přihlásili do Azure AD. Chcete-li tento problém vyřešit:

- Ověřte, jestli je přihlašovací jméno, které jste zadali na příkazovém řádku SSH, správné. Překlep v přihlašovacím jméně může způsobit neshodu mezi přihlašovacím jménem, které jste zadali na příkazovém řádku SSH, a účtem, který jste se přihlásili k Azure AD pomocí. Například jste zadali *azuresuer \@ contoso.onmicrosoft.com* namísto *azureuser \@ contoso.onmicrosoft.com*.
- Pokud máte víc uživatelských účtů, ujistěte se, že při přihlašování do Azure AD neposkytnete v okně prohlížeče jiný uživatelský účet.
- Linux je operační systém s rozlišováním velkých a malých písmen. Existuje rozdíl mezi ' Azureuser@contoso.onmicrosoft.com ' a ' azureuser@contoso.onmicrosoft.com ', což může způsobit neshodu. Ujistěte se, že v příkazovém řádku SSH určíte hlavní název uživatele se správným rozlišováním velkých a malých písmen.

### <a name="other-limitations"></a>Další omezení

Uživatelé, kteří dědí přístupová práva prostřednictvím vnořených skupin nebo přiřazení rolí, se v tuto chvíli nepodporují. Uživatel nebo skupina musí přímo přiřadit [požadovaná přiřazení rolí](#configure-role-assignments-for-the-vm). Například použití skupin pro správu nebo přiřazení role vnořené skupiny neudělí správná oprávnění, aby se uživatel mohl přihlásit.

## <a name="preview-feedback"></a>Zpětná vazba na verzi Preview

Nasdílejte svůj názor na tuto funkci ve verzi Preview nebo nahlaste problémy pomocí svého [fóra s názory na Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) .

## <a name="next-steps"></a>Další kroky

Další informace o Azure Active Directory najdete v tématu [co je Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) .
