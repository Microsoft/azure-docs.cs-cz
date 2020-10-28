---
title: Přihlášení k virtuálnímu počítači s Windows v Azure pomocí Azure Active Directory (Preview)
description: Přihlášení do Azure AD s virtuálním počítačem Azure s Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85bbdff2f7e67434a3e21aaf51af96c1e851eb0d
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740184"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Přihlášení k virtuálnímu počítači s Windows v Azure pomocí ověřování Azure Active Directory (Preview)

Organizace teď můžou používat ověřování Azure Active Directory (AD) pro své virtuální počítače Azure s **Windows serverem 2019 Datacenter Edition** nebo **Windows 10 1809** a novějším. Použití Azure AD k ověřování na virtuálních počítačích vám poskytne způsob, jak centrálně řídit a vysazovat zásady. Nástroje, jako je řízení přístupu na základě role Azure (Azure RBAC) a podmíněný přístup Azure AD, umožňují řídit, kdo má přístup k virtuálnímu počítači. V tomto článku se dozvíte, jak vytvořit a nakonfigurovat virtuální počítač s Windows serverem 2019 pro použití ověřování Azure AD.

> [!NOTE]
> Přihlášení Azure AD pro virtuální počítače Azure s Windows je funkce veřejné verze Preview Azure Active Directory. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

K přihlášení k virtuálním počítačům s Windows v Azure přinášíme spoustu výhod, včetně těchto:

- Využijte stejné federované nebo spravované přihlašovací údaje služby Azure AD, které běžně používáte.
- Už nemusíte spravovat účty místních správců.
- Azure RBAC vám umožňuje udělit odpovídající přístup k virtuálním počítačům podle potřeby a odebrat je, když už nepotřebujete.
- Než povolíte přístup k virtuálnímu počítači, podmíněný přístup Azure AD může vynutil další požadavky, jako třeba: 
   - Ověřování pomocí služby Multi-Factor Authentication
   - Kontroly rizika přihlašování
- Automatizujte a škálujte připojení Azure AD k virtuálním počítačům Azure s Windows, které jsou součástí nasazení infrastruktury virtuálních klientských počítačů.

> [!NOTE]
> Jakmile tuto možnost povolíte, vaše virtuální počítače s Windows v Azure budou připojené ke službě Azure AD. Nemůžete ho připojit k jiné doméně, jako je místní služba AD nebo Azure služba AD DS. Pokud to budete potřebovat, budete muset odpojit virtuální počítač od svého tenanta Azure AD odinstalováním rozšíření.

## <a name="requirements"></a>Požadavky

### <a name="supported-azure-regions-and-windows-distributions"></a>Podporované oblasti Azure a distribuce oken

Během verze Preview této funkce se aktuálně podporují následující distribuce systému Windows:

- Windows Server 2019 Datacenter
- Windows 10 1809 a novější

> [!IMPORTANT]
> Vzdálené připojení k virtuálním počítačům připojeným ke službě Azure AD je povolené jenom z počítačů s Windows 10, které jsou zaregistrované v Azure AD (od Windows 10 20H1), připojené k Azure AD nebo ke stejnému adresáři, který je připojený ke **stejnému** adresáři jako virtuální počítač. 

Ve verzi Preview této funkce se aktuálně podporují tyto oblasti Azure:

- Všechny globální oblasti Azure

> [!IMPORTANT]
> Chcete-li použít tuto funkci verze Preview, nasaďte pouze podporované distribuce oken a v podporované oblasti Azure. Tato funkce se v cloudech Azure Government nebo svrchovaného v tuto chvíli nepodporuje.

### <a name="network-requirements"></a>Požadavky sítě

Pokud chcete povolit ověřování Azure AD pro virtuální počítače s Windows v Azure, musíte zajistit, aby konfigurace sítě virtuálních počítačů povolovala odchozí přístup k následujícím koncovým bodům přes port TCP 443:

- https: \/ /enterpriseregistration.Windows.NET
- https:\//login.microsoftonline.com
- https: \/ /Device.Login.microsoftonline.com
- https: \/ /pas.Windows.NET

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Povolení přihlášení Azure AD pro virtuální počítač s Windows v Azure

Pokud chcete používat přihlášení Azure AD pro virtuální počítače s Windows v Azure, musíte nejdřív povolit možnost přihlášení Azure AD pro virtuální počítač s Windows a pak musíte nakonfigurovat přiřazení rolí Azure pro uživatele, kteří mají oprávnění k přihlášení k virtuálnímu počítači.
Přihlášení Azure AD pro virtuální počítač s Windows můžete povolit několika způsoby:

- Použití prostředí Azure Portal při vytváření virtuálního počítače s Windows
- Použití prostředí Azure Cloud Shell při vytváření virtuálního počítače s Windows **nebo pro existující virtuální počítač s Windows**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Povolení přihlášení Azure AD pomocí Azure Portal vytvoření prostředí pro virtuální počítače

Přihlašovací údaje služby Azure AD můžete povolit pro image virtuálních počítačů s Windows serverem 2019 Datacenter nebo Windows 10 1809 a novějším. 

Vytvoření virtuálního počítače s Windows serverem 2019 Datacenter v Azure s přihlášením pomocí Azure AD: 

1. Přihlaste se k [Azure Portal](https://portal.azure.com)s účtem, který má přístup k vytváření virtuálních počítačů, a vyberte **+ vytvořit prostředek** .
1. Do vyhledávacího panelu webu Marketplace zadejte **Windows Server** .
   1. Klikněte na **Windows Server** a zvolte **Windows Server 2019 Datacenter** v rozevíracím seznamu vybrat plán softwaru.
   1. Klikněte na **vytvořit** .
1. Na kartě Správa povolte možnost **Přihlásit se pomocí přihlašovacích údajů AAD (Preview)** v části Azure Active Directory z možností vypnuto na **zapnuto** .
1. Ujistěte se, že je v části Identita nastavená **spravovaná identita systému** **na zapnuto** . Tato akce by se měla provést automaticky po povolení přihlášení s přihlašovacími údaji Azure AD.
1. Projděte si zbytek zkušeností při vytváření virtuálního počítače. V této verzi Preview budete muset vytvořit uživatelské jméno a heslo správce pro virtuální počítač.

![Přihlášení pomocí přihlašovacích údajů Azure AD vytvoření virtuálního počítače](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Abyste se mohli přihlásit k VIRTUÁLNÍmu počítači pomocí přihlašovacích údajů Azure AD, musíte nejdřív nakonfigurovat přiřazení rolí pro virtuální počítač, jak je popsáno v některém z následujících částí.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Povolení přihlášení Azure AD pomocí Azure Cloud Shellho prostředí

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Ve službě Cloud Shell jsou předinstalované obvyklé nástroje Azure a jsou nakonfigurované pro použití s vaším účtem. Stačí vybrat tlačítko Kopírovat a zkopírovat kód, vložit ho do služby Cloud Shell a pak ho spustit stisknutím klávesy Enter. Cloud Shell můžete otevřít několika způsoby:

Zvolte Vyzkoušet v pravém horním rohu bloku kódu.
Otevřete Cloud Shell ve vašem prohlížeči.
Zvolte Cloud Shell v nabídce v pravém horním rohu webu [Azure Portal](https://portal.azure.com).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.31 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz az --version. Pokud potřebujete instalaci nebo upgrade, přečtěte si článek instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 
1. Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az-vm-create) pomocí podporované distribuce v podporované oblasti. 
1. Nainstalujte rozšíření pro přihlášení k virtuálnímu počítači Azure AD. 

Následující příklad nasadí virtuální počítač s názvem myVM, který používá Win2019Datacenter, do skupiny prostředků s názvem myResourceGroup v oblasti southcentralus. V následujících příkladech můžete podle potřeby zadat vlastní skupinu prostředků a názvy virtuálních počítačů.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Než nainstalujete rozšíření přihlašovacích virtuálních počítačů Azure AD, musíte na svém virtuálním počítači povolit spravovanou identitu přiřazenou systémem.

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut.

Nakonec nainstalujte rozšíření Azure AD Login VM pro povolení přihlášení Azure AD pro virtuální počítač s Windows. Rozšíření virtuálních počítačů jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace na virtuálních počítačích Azure. Pomocí [AZ VM Extension](/cli/azure/vm/extension#az-vm-extension-set) set nainstalujete rozšíření AADLoginForWindows na virtuální počítač s názvem myVM ve skupině prostředků myResourceGroup:

> [!NOTE]
> Rozšíření AADLoginForWindows můžete nainstalovat na stávající virtuální počítač s Windows serverem 2019 nebo Windows 10 1809 a novějším, abyste ho mohli povolit pro ověřování Azure AD. Příklad AZ CLI je uveden níže.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

`provisioningState` `Succeeded` Po instalaci rozšíření na virtuálním počítači se zobrazí část z.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurace přiřazení rolí pro virtuální počítač

Teď, když jste vytvořili virtuální počítač, musíte nakonfigurovat zásadu Azure RBAC, abyste zjistili, kdo se může přihlásit k virtuálnímu počítači. K autorizaci přihlášení k virtuálnímu počítači se používají dvě role Azure:

- **Přihlášení správce virtuálního počítače** : uživatelé s touto rolí se můžou přihlašovat k virtuálnímu počítači Azure s oprávněními správce.
- **Přihlášení uživatele k virtuálnímu počítači** : uživatelé s touto rolí se můžou přihlašovat k virtuálnímu počítači Azure s pravidelnými uživatelskými oprávněními.

> [!NOTE]
> Pokud chcete uživateli dovolit, aby se přihlásil k VIRTUÁLNÍmu počítači přes RDP, musíte přiřadit buď roli přihlášení správce virtuálního počítače, nebo přihlašovací údaje uživatele virtuálního počítače. Uživatel Azure s rolemi vlastník nebo přispěvatel přiřazený k virtuálnímu počítači nemá automaticky oprávnění k přihlášení k virtuálnímu počítači přes RDP. Slouží k zajištění prověřeného oddělení mezi sadou osob, které ovládají virtuální počítače a sadou lidí, kteří mají přístup k virtuálním počítačům.

Přiřazení rolí pro virtuální počítač můžete nakonfigurovat několika způsoby:

- Použití prostředí portálu Azure AD
- Použití prostředí Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Použití prostředí portálu Azure AD

Konfigurace přiřazení rolí pro virtuální počítače Windows serveru 2019 Datacenter s povolenou službou Azure AD:

1. Přejít na stránku Přehled konkrétního virtuálního počítače
1. Z možností nabídky vyberte **řízení přístupu (IAM)** .
1. Výběrem **Přidat** , **Přidat přiřazení role** otevřete podokno přidat přiřazení role.
1. V rozevíracím seznamu **role** vyberte roli, například **přihlašovací jméno správce virtuálního počítače** nebo **přihlášení uživatele k virtuálnímu počítači** .
1. V poli **Vybrat** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.
1. Vyberte **Uložit** a přiřaďte roli.

Po chvíli se objektu zabezpečení přiřadí role ve vybraném oboru.

![Přiřaďte role uživatelům, kteří budou mít přístup k virtuálnímu počítači.](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Použití prostředí Azure Cloud Shell

V následujícím příkladu se pomocí funkce [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) přiřadí k virtuálnímu počítači role přihlášení správce virtuálního počítače pro aktuálního uživatele Azure. Uživatelské jméno vašeho aktivního účtu Azure se získá pomocí [AZ Account show](/cli/azure/account#az-account-show)a obor se nastaví na virtuální počítač vytvořený v předchozím kroku pomocí [AZ VM show](/cli/azure/vm#az-vm-show). Obor se taky dá přiřadit na úrovni skupiny prostředků nebo předplatného a platí normální oprávnění dědičnosti Azure RBAC. Další informace najdete v tématu [přihlášení k virtuálnímu počítači se systémem Linux v Azure pomocí ověřování Azure Active Directory](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Pokud se vaše doména AAD a doména přihlášení k uživatelskému jménu neshodují, je nutné zadat ID objektu vašeho uživatelského účtu s `--assignee-object-id` příponou, nikoli jenom s uživatelským jménem pro `--assignee` . ID objektu pro svůj uživatelský účet můžete získat pomocí [seznamu AZ AD User list](/cli/azure/ad/user#az-ad-user-list).

Další informace o tom, jak pomocí Azure RBAC spravovat přístup k prostředkům předplatného Azure, najdete v následujících článcích:

- [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Přidejte nebo odeberte přiřazení rolí Azure pomocí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Použití podmíněného přístupu

Před autorizací přístupu k virtuálním počítačům s Windows v Azure, které jsou povolené při přihlášení ke službě Azure AD, můžete vyhovět zásadám podmíněného přístupu, jako je vícefaktorové ověřování nebo ověření rizik přihlašování uživatelů. Pokud chcete použít zásady podmíněného přístupu, musíte v rámci možnosti přiřazení cloudových aplikací nebo akcí vybrat aplikaci přihlášení k virtuálnímu počítači Azure Azure a pak použít pro podmínku přihlášení podmínky přihlášení nebo vyžadovat vícefaktorové ověřování jako řízení přístupu pro udělení. 

> [!NOTE]
> Pokud použijete "vyžadovat vícefaktorové ověřování" jako udělení řízení přístupu pro vyžádání přístupu k aplikaci pro přihlášení k virtuálnímu počítači Azure s Windows, musíte jako součást klienta zadat službu Multi-Factor Authentication, která inicializuje relaci RDP k cílovému virtuálnímu počítači s Windows v Azure. Jediným způsobem, jak toho dosáhnout na klientovi s Windows 10, je použít PIN kód Windows Hello pro firmy nebo biometrické ověřování s klientem RDP. Do klienta RDP v systému Windows 10 verze 1809 byla přidána podpora biometrického ověřování. Ověřování pomocí Windows Hello pro firmy na vzdálené ploše je dostupné jenom pro nasazení, která používají model důvěryhodnosti certifikátů a aktuálně nejsou k dispozici pro model vztahu důvěryhodnosti klíče.

> [!WARNING]
> Pro přihlášení k VIRTUÁLNÍm počítačům se nepodporují Azure Multi-Factor Authentication s povoleným/vydaným uživatelem.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Přihlášení pomocí přihlašovacích údajů Azure AD k virtuálnímu počítači s Windows

> [!IMPORTANT]
> Vzdálené připojení k virtuálním počítačům připojeným ke službě Azure AD je povolené jenom z počítačů s Windows 10, které jsou buď registrované v Azure AD (vyžaduje se minimální 20H1 sestavení), nebo jste připojeni ke službě Azure AD nebo ke **stejnému** adresáři jako virtuální počítač připojili službu Azure AD. Navíc k protokolu RDP pomocí přihlašovacích údajů Azure AD musí uživatel patřit do jedné ze dvou rolí Azure, přihlášení správce virtuálních počítačů nebo přihlášení uživatele virtuálního počítače. Pokud používáte počítač s Windows 10 registrovaný v Azure AD, musíte zadat přihlašovací údaje ve formátu AzureAD\UPN (např. AzureAD\john@contoso.com ). V tuto chvíli se služba Azure bastionu nedá použít k přihlášení pomocí Azure Active Directory ověřování s rozšířením AADLoginForWindows. podporován je pouze přímý protokol RDP.

Přihlášení k virtuálnímu počítači s Windows serverem 2019 pomocí Azure AD: 

1. Přejděte na stránku Přehled virtuálního počítače, který je povolený s přihlášením pomocí Azure AD.
1. Kliknutím na **připojit** otevřete okno připojit k virtuálnímu počítači.
1. Vyberte **Stáhnout soubor RDP** .
1. Vyberte **otevřít** a spusťte klienta připojení ke vzdálené ploše.
1. Vyberte **připojit** a spusťte přihlašovací dialog Windows.
1. Přihlaste se pomocí přihlašovacích údajů Azure AD.

Nyní jste přihlášeni k virtuálnímu počítači s Windows serverem 2019 Azure pomocí oprávnění role, jako je například uživatel virtuálního počítače nebo správce virtuálního počítače. 

> [!NOTE]
> Můžete uložit. Soubor RDP místně ve vašem počítači, aby se spouštěla budoucí připojení vzdálené plochy k virtuálnímu počítači, nemusíte v Azure Portal přejít na stránku Přehled virtuálního počítače a použít možnost připojit.

## <a name="troubleshoot"></a>Řešení potíží

### <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

Aby virtuální počítač dokončil proces připojení k Azure AD, musí se úspěšně nainstalovat rozšíření AADLoginForWindows. Pokud se nepovede správně nainstalovat rozšíření virtuálního počítače, proveďte následující kroky.

1. RDP k virtuálnímu počítači pomocí účtu místního správce a prověřte protokol CommandExecuti'n. log pod  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Pokud se rozšíření po počátečním selhání restartuje, protokol s chybou nasazení se uloží jako CommandExecution_YYYYMMDDHHMMSSSSS. log. "
1. Na virtuálním počítači otevřete příkazový řádek prostředí PowerShell a ověřte tyto dotazy proti koncovému bodu Instance Metadata Service (IMDS) běžícímu na hostiteli Azure:

   | Příkaz, který se má spustit | Očekávaný výstup |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Správné informace o virtuálním počítači Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Platné ID tenanta přidružené k předplatnému Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Platný přístupový token vydaný Azure Active Directory pro spravovanou identitu, která je přiřazená k tomuto virtuálnímu počítači |

   > [!NOTE]
   > Přístupový token se dá dekódovat pomocí nástroje, jako je [http://calebb.net/](http://calebb.net/) . Ověřte, že "AppID" v přístupovém tokenu odpovídá spravované identitě přiřazené k virtuálnímu počítači.

1. Ujistěte se, že požadované koncové body jsou dostupné z virtuálního počítače pomocí příkazového řádku:
   
   - oblé https: \/ /Login.microsoftonline.com/-D –
   - oblé https: \/ /Login.microsoftonline.com/ `<TenantID>` /-D –

   > [!NOTE]
   > Nahraďte `<TenantID>` ID tenanta Azure AD, které je přidružené k předplatnému Azure.

   - oblé https: \/ /enterpriseregistration.Windows.NET/-D –
   - oblé https: \/ /Device.Login.microsoftonline.com/-D –
   - oblé https: \/ /pas.Windows.NET/-D –

1. Stav zařízení lze zobrazit spuštěním `dsregcmd /status` . Cílem je stav zařízení, který se má zobrazit jako `AzureAdJoined : YES` .

   > [!NOTE]
   > Aktivita připojení Azure AD se zachycuje v prohlížeči událostí v protokolu Registration\Admin uživatelského zařízení.

Pokud rozšíření AADLoginForWindows selhává s určitým kódem chyby, můžete provést následující kroky:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problém 1: rozšíření AADLoginForWindows se nemůže nainstalovat s kódem chyby terminálu 1007 a ukončovacím kódem:-2145648574.

Tento ukončovací kód se přeloží na DSREG_E_MSI_TENANTID_UNAVAILABLE, protože rozšíření se nemůže dotazovat na informace o Tenantovi Azure AD.

1. Ověřte, že virtuální počítač Azure může načíst TenantID z Instance Metadata Service.

   - RDP na virtuální počítač jako místní správce a ověření, že koncový bod vrátí platné ID tenanta spuštěním tohoto příkazu z příkazového řádku se zvýšenými oprávněními na virtuálním počítači:
      
      - Metadata složeného H: true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. Správce virtuálních počítačů se pokusí nainstalovat rozšíření AADLoginForWindows, ale spravovaná identita přiřazená systémem nepovolila virtuální počítač jako první. Přejděte do okna identita virtuálního počítače. Na kartě přiřazené systémem ověřte, zda je stav Zapnuto.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problém 2: rozšíření AADLoginForWindows se nemůže nainstalovat s ukončovacím kódem:-2145648607

Tento ukončovací kód se přeloží na DSREG_AUTOJOIN_DISC_FAILED, protože rozšíření není schopné spojit s `https://enterpriseregistration.windows.net` koncovým bodem.

1. Ověřte dostupnost požadovaných koncových bodů z virtuálního počítače pomocí příkazového řádku:

   - oblé https: \/ /Login.microsoftonline.com/-D –
   - oblé https: \/ /Login.microsoftonline.com/ `<TenantID>` /-D –
   
   > [!NOTE]
   > Nahraďte `<TenantID>` ID tenanta Azure AD, které je přidružené k předplatnému Azure. Pokud potřebujete najít ID tenanta, můžete ukazatel myši umístit na název účtu a získat tak ID adresáře nebo tenanta, nebo v Azure Portal vybrat Azure Active Directory > vlastností > ID adresáře.

   - oblé https: \/ /enterpriseregistration.Windows.NET/-D –
   - oblé https: \/ /Device.Login.microsoftonline.com/-D –
   - oblé https: \/ /pas.Windows.NET/-D –

1. Pokud některý z příkazů selhává s názvem "nelze přeložit hostitele `<URL>` ", zkuste spustit tento příkaz, který určí server DNS, který je používán virtuálním počítačem.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Nahraďte `<URL>` plně kvalifikovanými názvy domén používanými koncovými body, jako je například "login.microsoftonline.com".

1. Další informace najdete v tématu určení veřejného serveru DNS, který umožňuje úspěšné provedení příkazu:

   `nslookup <URL> 208.67.222.222`

1. V případě potřeby změňte server DNS, který je přiřazený ke skupině zabezpečení sítě, do které virtuální počítač Azure patří.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problém 3: rozšíření AADLoginForWindows se nemůže nainstalovat s ukončovacím kódem: 51

Ukončovací kód 51 se překládá na toto rozšíření není v operačním systému virtuálního počítače podporováno.

V Public Preview je rozšíření AADLoginForWindows určeno pouze pro instalaci v systému Windows Server 2019 nebo Windows 10 (Build 1809 nebo novější). Ujistěte se, že je podporovaná verze Windows. Pokud sestavení Windows není podporované, odinstalujte rozšíření virtuálního počítače.

### <a name="troubleshoot-sign-in-issues"></a>Řešení potíží s přihlašováním

Některé běžné chyby při pokusu o připojení RDP s přihlašovacími údaji Azure AD zahrnují žádné přiřazené role Azure, neautorizovaný klient nebo 2FA Metoda přihlašování, která je povinná. Tyto problémy opravíte pomocí následujících informací.

Stav zařízení a jednotného přihlašování se dá zobrazit tak, že se spustí `dsregcmd /status` . Cílem je stav zařízení, který se má zobrazit jako `AzureAdJoined : YES` a `SSO State` Zobrazit `AzureAdPrt : YES` .

Přihlášení RDP pomocí účtů Azure AD se taky zachycuje v prohlížeči událostí v protokolech událostí AAD\Operational.

#### <a name="azure-role-not-assigned"></a>Role Azure není přiřazená.

Pokud se při inicializaci připojení ke vzdálené ploše na virtuální počítač zobrazí následující chybová zpráva: 

- Váš účet je nakonfigurovaný tak, aby vám zabránil v používání tohoto zařízení. Pokud chcete získat další informace, obraťte se na správce systému.

![Váš účet je nakonfigurovaný tak, aby vám zabránil v používání tohoto zařízení.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Ověřte, že jste pro virtuální počítač [nakonfigurovali zásady Azure RBAC](../../virtual-machines/linux/login-using-aad.md) , které udělí uživateli buď přihlašovací jméno správce virtuálního počítače, nebo roli přihlášení uživatele virtuálního počítače:
 
#### <a name="unauthorized-client"></a>Neautorizovaný klient

Pokud se při inicializaci připojení ke vzdálené ploše na virtuální počítač zobrazí následující chybová zpráva: 

- Vaše přihlašovací údaje nefungovaly.

![Vaše přihlašovací údaje nefungovaly.](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Ověřte, že počítač s Windows 10, který používáte k inicializaci připojení ke vzdálené ploše, je ten, který je připojený k Azure AD, nebo jestli je hybridní služba Azure AD připojená ke stejnému adresáři Azure AD, ke kterému je připojený váš virtuální počítač. Další informace o identitě zařízení najdete v článku [co je identita zařízení](./overview.md).

> [!NOTE]
> Windows 10 Build 20H1 přidal podporu pro počítač se systémem Azure AD, který iniciuje připojení RDP k vašemu VIRTUÁLNÍmu počítači. Při použití registrovaného počítače Azure AD (ne připojeného k Azure AD nebo k hybridnímu Azure AD) jako klienta RDP pro inicializaci připojení k vašemu VIRTUÁLNÍmu počítači musíte zadat přihlašovací údaje ve formátu AzureAD\UPn (např. AzureAD\john@contoso.com ).

Ověřte také, že rozšíření AADLoginForWindows nebylo po dokončení připojení služby Azure AD odinstalováno.
 
#### <a name="mfa-sign-in-method-required"></a>Vyžaduje se metoda přihlášení MFA.

Pokud se při inicializaci připojení ke vzdálené ploše na virtuální počítač zobrazí následující chybová zpráva: 

- Metoda přihlašování, kterou se pokoušíte použít, není povolená. Vyzkoušejte jinou metodu přihlašování nebo se obraťte na správce systému.

![Metoda přihlašování, kterou se pokoušíte použít, není povolená.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Pokud jste nakonfigurovali zásadu podmíněného přístupu, která vyžaduje vícefaktorové ověřování (MFA) před tím, než budete mít přístup k prostředku, musíte zajistit, aby se počítač s Windows 10, který iniciuje připojení ke vzdálené ploše k vašemu VIRTUÁLNÍmu počítači, přihlásí pomocí metody silného ověřování, jako je Windows Hello. Pokud pro připojení ke vzdálené ploše nepoužíváte metodu silného ověřování, zobrazí se předchozí chyba.

Pokud jste nenainstalovali Windows Hello pro firmy a pokud to není možnost pro teď, můžete tento požadavek vyloučit z důvodu nastavení zásad podmíněného přístupu, které vyloučí aplikaci Azure Azure VM Signing ze seznamu cloudových aplikací, které vyžadují MFA. Další informace o Windows Hello pro firmy najdete v tématu [Přehled Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Ověřování PIN kódu ve Windows Hello pro firmy se systémem Windows 10 podporuje pro několik verzí, ale v systému Windows 10 verze 1809 byla přidána podpora biometrického ověřování pomocí protokolu RDP. Použití ověřování ve Windows Hello pro firmy během protokolu RDP je dostupné jenom pro nasazení, která používají model důvěryhodnosti certifikátu a v současnosti není k dispozici pro model vztahu důvěryhodnosti klíče.
 
## <a name="preview-feedback"></a>Zpětná vazba na verzi Preview

Nasdílejte svůj názor na tuto funkci ve verzi Preview nebo nahlaste problémy s jejich použitím ve [fóru pro názory na Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Další kroky

Další informace o Azure Active Directory najdete v tématu [co je Azure Active Directory](../fundamentals/active-directory-whatis.md) .
