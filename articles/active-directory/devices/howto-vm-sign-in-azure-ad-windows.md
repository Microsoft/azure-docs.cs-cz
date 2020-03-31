---
title: Přihlášení k virtuálnímu počítači Windows v Azure pomocí Azure Active Directory (Preview)
description: Přihlášení azure a disponeb
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050062"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Přihlášení k virtuálnímu počítači Windows v Azure pomocí ověřování Azure AD (Preview)

Organizace teď můžou využívat ověřování Azure Active Directory (AD) pro své virtuální počítače Azure se systémem **Windows Server 2019 Datacenter Edition** nebo Windows **10 1809** a novějším. Použití Azure AD k ověření na virtuální počítače poskytuje způsob, jak centrálně řídit a vynucovat zásady. Nástroje, jako je řízení přístupu na základě rolí Azure (RBAC) a podmíněný přístup Azure AD umožňují řídit, kdo má přístup k virtuálnímu počítači. Tento článek ukazuje, jak vytvořit a nakonfigurovat virtuální počítač se systémem Windows Server 2019 tak, aby používal ověřování Azure AD.

|     |
| --- |
| Přihlášení azure ad pro virtuální počítače Azure Windows je veřejná funkce preview Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Existuje mnoho výhod používání ověřování Azure AD pro přihlášení k virtuálním počítačům s Windows v Azure, včetně:

- Využijte stejné federované nebo spravované přihlašovací údaje Azure AD, které běžně používáte.
- Již není třeba spravovat účty místních správců.
- Azure RBAC umožňuje udělit odpovídající přístup k virtuálním počítačům na základě potřeby a odebrat, když už není potřeba.
- Před povolením přístupu k virtuálnímu počítači může podmíněný přístup Azure AD vynutit další požadavky, jako jsou: 
   - Ověřování pomocí služby Multi-Factor Authentication
   - Kontrola rizika přihlášení
- Automatizujte a škálujte připojení Azure AD k virtuálním počítačům Azure windows, které jsou součástí vašich nasazení VDI.

> [!NOTE]
> Jakmile tuto možnost povolíte, vaše virtuální počítače s Windows v Azure se připojí k Azure AD. Nelze připojit k jiné doméně, jako je místní služby AD nebo Azure AD DS. Pokud to potřebujete udělat, budete muset odpojit virtuální počítač od vašeho klienta Azure AD odinstalováním rozšíření.

## <a name="requirements"></a>Požadavky

### <a name="supported-azure-regions-and-windows-distributions"></a>Podporované oblasti Azure a distribuce Windows

Během náhledu této funkce jsou aktuálně podporovány následující distribuce systému Windows:

- Windows Server 2019 Datacenter
- Windows 10 1809 a novější

> [!IMPORTANT]
> Vzdálené připojení k virtuálním počítačům připojení mů e Azure AD je povoleno jenom z počítačů s Windows 10, které jsou připojeny k Azure AD nebo hybridní Azure AD připojenke **stejnému** adresáři jako virtuální počítač. 

Během náhledu této funkce jsou aktuálně podporované následující oblasti Azure:

- Všechny globální oblasti Azure

> [!IMPORTANT]
> Chcete-li použít tuto funkci náhledu, nasadit pouze podporovanou distribuci systému Windows a v podporované oblasti Azure. Tato funkce momentálně není podporovaná v Azure Government nebo suverénních cloudech.

### <a name="network-requirements"></a>Síťové požadavky

Pokud chcete povolit ověřování Azure AD pro vaše virtuální počítače s Windows v Azure, musíte zajistit, aby konfigurace sítě virtuálních počítačů umožňovala odchozí přístup k následujícím koncovým bodům přes port TCP 443:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Povolení přihlášení k Azure AD pro virtuální počítač s Windows v Azure

Pokud chcete použít přihlášení azure ad pro virtuální počítač s Windows v Azure, musíte nejprve povolit možnost přihlášení Azure AD pro váš virtuální počítač s Windows a pak je potřeba nakonfigurovat přiřazení rolí RBAC pro uživatele, kteří mají oprávnění k přihlášení k virtuálnímu počítači.
Existuje několik způsobů, jak můžete povolit přihlášení Azure AD pro váš virtuální počítač s Windows:

- Použití prostředí portálu Azure při vytváření virtuálního počítače s Windows
- Použití prostředí Azure Cloud Shell při vytváření virtuálního počítače s Windows **nebo pro existující virtuální počítač s Windows**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Použití webu Azure Portal vytvořit prostředí virtuálních počítačů k povolení přihlášení azure ad

Přihlášení Azure AD pro Windows Server 2019 Datacenter nebo Windows 10 1809 a novější image virtuálních počítače. 

Vytvoření virtuálního počítače datového centra Windows Server 2019 v Azure pomocí přihlášení k Azure AD: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com), s účtem, který má přístup k vytváření virtuálních počítačů, a vyberte **+ Vytvořit prostředek**.
1. Zadejte **Windows Server** na panelu hledání na webu Marketplace.
   1. Klikněte na **Windows Server** a zvolte **Windows Server 2019 Datacenter** z rozbalovací nabídky Vybrat softwarový plán.
   1. Klikněte na **Vytvořit**.
1. Na kartě Správa povolte možnost **Přihlášení pomocí přihlašovacích údajů ad (preview)** v části Azure Active Directory z vypnuto na **Zapnuto**.
1. Ujistěte se, že **systém přiřazená spravovaná identita** v části Identita je nastavena **na zapnuto**. Tato akce by se měla uskutečnit automaticky, jakmile povolíte přihlášení s přihlašovacími údaji Azure AD.
1. Projděte si zbytek zkušeností s vytvářením virtuálního počítače. Během tohoto náhledu budete muset vytvořit uživatelské jméno správce a heslo pro virtuální počítače.

![Přihlášení pomocí přihlašovacích údajů Azure AD vytvořit virtuální počítač](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Chcete-li se přihlásit k virtuálnímu počítači pomocí pověření Azure AD, budete muset nejprve nakonfigurovat přiřazení rolí pro virtuální počítač, jak je popsáno v jedné z níže uvedených částí.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Povolení přihlášení azure a služby Azure AD pomocí prostředí Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Ve službě Cloud Shell jsou předinstalované obvyklé nástroje Azure a jsou nakonfigurované pro použití s vaším účtem. Stačí vybrat tlačítko Kopírovat a zkopírovat kód, vložit ho do služby Cloud Shell a pak ho spustit stisknutím klávesy Enter. Cloud Shell můžete otevřít několika způsoby:

Zvolte Vyzkoušet v pravém horním rohu bloku kódu.
Otevřete Cloud Shell ve vašem prohlížeči.
Zvolte Cloud Shell v nabídce v pravém horním rohu webu [Azure Portal](https://portal.azure.com).

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.31 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz az --version. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si článek [Instalace azure cli](/cli/azure/install-azure-cli).

1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 
1. Vytvořte virtuální ho s [vytvořením virtuálního virtuálního modulu as](/cli/azure/vm#az-vm-create) pomocí podporované distribuce v podporované oblasti. 
1. Nainstalujte rozšíření přihlašovacího virtuálního počítače Azure AD. 

Následující příklad nasazuje virtuální hod s názvem myVM, který používá Win2019Datacenter, do skupiny prostředků s názvem myResourceGroup v oblasti southcentralus. V následujících příkladech můžete podle potřeby zadat vlastní skupinu prostředků a názvy virtuálních počítačů.

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
> Je nutné povolit systém přiřazenou spravovanou identitu na vašem virtuálním počítači před instalací rozšíření přihlašovacího virtuálního počítače Azure AD.

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut.

Nakonec nainstalujte rozšíření přihlašovacího virtuálního počítače Azure AD, abyste povolili přihlášení Azure AD pro virtuální počítač s Windows. Rozšíření virtuálních počítačů jsou malé aplikace, které poskytují úlohy konfigurace a automatizace po nasazení na virtuálních počítačích Azure. Pomocí sady [rozšíření az vm](/cli/azure/vm/extension#az-vm-extension-set) nainstalujte rozšíření AADLoginForWindows na virtuální počítač s názvem myVM ve skupině prostředků myResourceGroup:

> [!NOTE]
> Rozšíření AADLoginForWindows můžete nainstalovat na existující Windows Server 2019 nebo Windows 10 1809 a novější virtuální počítač, abyste ho povolili pro ověřování Azure AD. Příklad AZ CLI je uveden níže.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

`Succeeded` Je `provisioningState` zobrazen, jakmile rozšíření je nainstalován na virtuálním počítači.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurace přiřazení rolí pro virtuální počítače

Teď, když jste vytvořili virtuální počítač, musíte nakonfigurovat zásady Azure RBAC k určení, kdo se může přihlásit k virtuálnímu počítači. Dvě role RBAC se používají k autorizaci přihlášení virtuálního mísy:

- **Přihlášení správce virtuálního počítače**: Uživatelé s přiřazenou touto rolí se můžou přihlásit k virtuálnímu počítači Azure s oprávněními správce.
- **Přihlášení uživatele virtuálního počítače**: Uživatelé s přiřazenou touto rolí se můžou přihlásit k virtuálnímu počítači Azure s běžnými uživatelskými oprávněními.

> [!NOTE]
> Chcete-li uživateli povolit přihlášení k virtuálnímu počítači přes protokol RDP, musíte přiřadit roli Přihlášení správce virtuálního počítače nebo Přihlášení uživatele virtuálního počítače. Uživatel Azure s rolemi vlastníka nebo přispěvatele přiřazené pro virtuální počítač nemají automaticky oprávnění k přihlášení k virtuálnímu počítači přes RDP. To to je poskytnout auditované oddělení mezi sadou lidí, kteří řídí virtuální počítače versus sadu lidí, kteří mají přístup k virtuálním počítačům.

Existuje několik způsobů, jak můžete nakonfigurovat přiřazení rolí pro virtuální počítač:

- Použití prostředí Portálu Azure AD
- Použití prostředí Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Používání prostředí Azure AD Portal

Konfigurace přiřazení rolí pro virtuální počítače datového centra Windows Server 2019 s podporou Azure AD:

1. Přechod na konkrétní stránku s přehledem virtuálních strojů
1. Vyberte **ovládací prvek přístupu (IAM)** z možností nabídky.
1. Vyberte **Přidat**, **Přidat přiřazení role** otevřete podokno Přidat přiřazení role.
1. V rozevíracím seznamu **Role** vyberte roli, jako je **přihlášení správce virtuálního počítače** nebo přihlášení uživatele **virtuálního počítače**.
1. V poli **Vybrat** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.
1. Chcete-li roli přiřadit, vyberte **uložit**.

Po několika okamžicích je objektu zabezpečení přiřazena role ve vybraném oboru.

![Přiřazení rolí uživatelům, kteří budou mít přístup k virtuálnímu virtuálnímu ms](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Použití prostředí Azure Cloud Shell

Následující příklad používá [vytvoření přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create) k přiřazení role Přihlášení správce virtuálního počítače virtuálnímu počítači pro aktuálního uživatele Azure. Uživatelské jméno vašeho aktivního účtu Azure se získá pomocí [az účtu show](/cli/azure/account#az-account-show)a obor je nastaven na virtuální počítač vytvořený v předchozím kroku s [az vm show](/cli/azure/vm#az-vm-show). Obor může být také přiřazen na úrovni skupiny prostředků nebo předplatného a platí normální oprávnění dědičnosti RBAC. Další informace naleznete [v tématu Ovládací prvky přístupu založené na rolích](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Pokud se vaše doména AAD a přihlašovací uživatelské jméno domény neshodují, je `--assignee-object-id`nutné zadat ID `--assignee`objektu vašeho uživatelského účtu s položkou , nikoli pouze s uživatelským jménem pro aplikaci . ID objektu můžete získat pro svůj uživatelský účet pomocí [seznamu uživatelů reklamy az](/cli/azure/ad/user#az-ad-user-list).

Další informace o tom, jak používat RBAC ke správě přístupu k prostředkům předplatného Azure, najdete v následujících článcích:

- [Správa přístupu k prostředkům Azure pomocí RBAC a Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure Portal](/azure/role-based-access-control/role-assignments-portal)
- [Spravujte přístup k prostředkům Azure pomocí RBAC a Azure PowerShellu](/azure/role-based-access-control/role-assignments-powershell).

## <a name="using-conditional-access"></a>Použití podmíněného přístupu

Před autorizací přístupu k virtuálním virtuálním ms s Windows v Azure, které jsou povolené přihlášením k Azure, můžete vynutit zásady podmíněného přístupu, jako je vícefaktorové ověřování nebo kontrola rizika přihlášení uživatele. Chcete-li použít zásady podmíněného přístupu, musíte vybrat aplikaci "Přihlášení virtuálních počítačích Azure Windows" z cloudových aplikací nebo možnosti přiřazení akcí a pak použít riziko přihlášení jako podmínku a/nebo vyžadovat vícefaktorové ověřování jako řízení přístupu k udělení. 

> [!NOTE]
> Pokud používáte "Vyžadovat vícefaktorové ověřování" jako řízení přístupu k udělení přístupu pro vyžádání přístupu k aplikaci Azure Windows VM Sign-In, musíte zadat deklaraci vícefaktorového ověřování jako součást klienta, který iniciuje relaci RDP do cílového systému Windows Virtuální počítač v Azure. Jediný způsob, jak toho dosáhnout na Windows 10 klienta je použití Windows Hello pro firmy PIN nebo biometrické authenication s klientem RDP. Podpora biometrického ověřování byla přidána do klienta RDP v systému Windows 10 verze 1809. Vzdálená plocha využívající ověřování Windows Hello for Business je k dispozici pouze pro nasazení, která používají model důvěryhodnosti certifikátu a v současné době nejsou k dispozici pro model důvěryhodnosti klíčů.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Přihlášení pomocí přihlašovacích údajů Azure AD k virtuálnímu počítači s Windows

> [!IMPORTANT]
> Vzdálené připojení k virtuálním počítačům připojení mů e Azure AD je povoleno jenom z počítačů s Windows 10, které jsou připojeny k Azure AD nebo hybridní Azure AD připojenke **stejnému** adresáři jako virtuální počítač. Kromě toho rdp pomocí přihlašovacích údajů Azure AD, uživatel musí patřit do jedné ze dvou rolí RBAC, přihlášení správce virtuálního počítače nebo přihlášení uživatele virtuálního počítače. V tuto chvíli nelze použít Azure Bastion k přihlášení pomocí ověřování Azure Active Directory s rozšířením AADLoginForWindows. Podporován je pouze přímý prv.

Přihlášení k virtuálnímu počítači Windows Serveru 2019 pomocí Azure AD: 

1. Přejděte na stránku s přehledem virtuálního počítače, který byl povolen s přihlášením služby Azure AD.
1. Výběrem **možnosti Připojit** otevřete okno Připojit k virtuálnímu stroji.
1. Vyberte **stáhnout soubor RDP**.
1. Výběrem **možnosti Otevřít** spusťte klienta Připojení ke vzdálené ploše.
1. Vyberte **Připojit,** chcete-li spustit přihlašovací dialogové okno systému Windows.
1. Přihlaste se pomocí přihlašovacích údajů azure ad.

Teď jste přihlášení k virtuálnímu počítači Azure se systémem Windows Server 2019 s přiřazenými oprávněními role, jako je například uživatel virtuálního počítače nebo správce virtuálního počítače. 

> [!NOTE]
> Můžete uložit . SOUBOR RDP místně v počítači pro spuštění budoucích připojení ke vzdálené ploše k virtuálnímu počítači namísto nutnosti přejít na stránku přehledu virtuálních počítačů na webu Azure portal a pomocí možnosti připojení.

## <a name="troubleshoot"></a>Řešení potíží

### <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

Rozšíření AADLoginForWindows musí úspěšně nainstalovat, aby virtuální počítač dokončit proces připojení Azure AD. Pokud se rozšíření virtuálního zařízení nenainstaluje správně, proveďte následující kroky.

1. Protokol RDP do virtuálního virtuálního modulu pomocí účtu místního správce a zkontrolujte příkaz CommandExecuti'n.log v části  
   
   C:\WindowsAzure\Protokoly\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Pokud se rozšíření po počáteční chybě restartuje, protokol s chybou nasazení bude uložen jako CommandExecution_YYYYMMDDHHMMSSSSS.log. "
1. Otevřete příkazový řádek na virtuálním počítači a ověřte tyto dotazy proti koncovému bodu služby Metadat instance (IMDS), který běží na výnosech hostitele Azure:

   | Příkaz ke spuštění | Očekávaný výstup |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Správné informace o virtuálním počítači Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Platné ID klienta přidružené k předplatnému Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Platný přístupový token vydaný službou Azure Active Directory pro spravovanou identitu přiřazenou k tomuto virtuálnímu počítači |

   > [!NOTE]
   > Přístupový token lze dekódovat pomocí [http://calebb.net/](http://calebb.net/)nástroje, jako je . Ověřte, zda "appid" v přístupovém tokenu odpovídá spravované identitě přiřazené virtuálnímu virtuálnímu soudu.

1. Ujistěte se, že požadované koncové body jsou přístupné z virtuálního virtuálního soudu pomocí příkazového řádku:
   
   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\/`<TenantID>`/login.microsoftonline.com/ / -D –

   > [!NOTE]
   > Nahraďte `<TenantID>` ID klienta Azure AD, které je přidružené k předplatnému Azure.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Stav zařízení lze zobrazit `dsregcmd /status`spuštěním . Cílem je, aby se `AzureAdJoined : YES`stav zařízení zobrazil jako .

   > [!NOTE]
   > Aktivita spojení Azure AD se zachytí v prohlížeči událostí v protokolu Registrace uživatelského zařízení\Správce.

Pokud se s určitým kódem chyby nezdaří rozšíření AADLoginForWindows, můžete provést následující kroky:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problém 1: AADLoginForWindows rozšíření se nezdaří nainstalovat s kódem chyby terminálu '1007' a ukončovací kód: -2145648574.

Tento ukončovací kód se překládá do DSREG_E_MSI_TENANTID_UNAVAILABLE, protože rozšíření nemůže dotazovat informace o tenantovi Azure AD.

1. Ověřte, že virtuální počítač Azure může načíst ID tenanta ze služby metadat instance.

   - RDP do virtuálního počítači jako místní správce a ověřit koncový bod vrátí platné ID klienta spuštěním tohoto příkazu z příkazového řádku se zvýšenými oprávněními na virtuálním počítači:
      
      - curl -H Metadata:truehttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. Správce virtuálního zařízení se pokusí nainstalovat rozšíření AADLoginForWindows, ale systém přiřazený spravovanou identitou nepovolil virtuální ho. Přejděte k okně identity virtuálního zařízení. Na kartě Systém přiřazený ověřte, zda je stav přepnuto na Zapnuto.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problém 2: AADLoginForWindows rozšíření se nepodaří nainstalovat s ukončovacíkód: -2145648607

Tento ukončovací kód se překládá do `https://enterpriseregistration.windows.net` DSREG_AUTOJOIN_DISC_FAILED protože rozšíření není možné dosáhnout koncového bodu.

1. Ověřte, zda jsou požadované koncové body přístupné z virtuálního virtuálního soudu pomocí příkazového řádku:

   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\/`<TenantID>`/login.microsoftonline.com/ / -D –
   
   > [!NOTE]
   > Nahraďte `<TenantID>` ID klienta Azure AD, které je přidružené k předplatnému Azure. Pokud potřebujete najít ID klienta, můžete najet na název účtu, abyste získali ID adresáře / klienta, nebo vyberte Azure Active Directory > Vlastnosti > ID adresáře na webu Azure Portal.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Pokud některý z příkazů selže s "Nelze vyřešit hostitele `<URL>`", zkuste spustit tento příkaz k určení serveru DNS, který je používán virtuálním serverem.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Nahraďte `<URL>` plně kvalifikovanými názvy domén používanými koncovými body, například "login.microsoftonline.com".

1. Dále zjistěte, zda zadání veřejného serveru DNS umožňuje příkaz uspěje:

   `nslookup <URL> 208.67.222.222`

1. V případě potřeby změňte server DNS, který je přiřazen ke skupině zabezpečení sítě, do které virtuální počítač Azure patří.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problém 3: AADLoginForWindows rozšíření se nepodaří nainstalovat s ukončovacíkód: 51

Ukončovací kód 51 překládá na "Toto rozšíření není podporováno v operačním systému virtuálního počítači".

Ve verzi Public Preview je rozšíření AADLoginForWindows určeno jenom k instalaci na Windows Server 2019 nebo Windows 10 (Build 1809 nebo novější). Ujistěte se, že je podporována verze systému Windows. Pokud sestavení systému Windows není podporováno, odinstalujte rozšíření virtuálního aplikace.

### <a name="troubleshoot-sign-in-issues"></a>Poradce při potížích s přihlášením

Některé běžné chyby při pokusu o RDP s přihlašovacími údaji Azure AD zahrnují žádné přiřazené role RBAC, neautorizovaného klienta nebo metodu přihlášení 2FA. K opravě těchto problémů použijte následující informace.

Zařízení a stav spřiho lze `dsregcmd /status`zobrazit spuštěním . Cílem je, aby se `AzureAdJoined : YES` stav `SSO State` zařízení `AzureAdPrt : YES`zobrazoval jako a zobrazoval .

Přihlášení rdp pomocí účtů Azure AD se také zachytí v prohlížeči událostí v protokolech událostí AAD\Operational.

#### <a name="rbac-role-not-assigned"></a>Role RBAC není přiřazena.

Pokud se při iniciaci připojení ke vzdálené ploše k virtuálnímu počítači zobrazí následující chybová zpráva: 

- Váš účet je nakonfigurován tak, aby vám zabránil v používání tohoto zařízení. Další informace získáte od správce systému.

![Váš účet je nakonfigurován tak, aby vám zabránil v používání tohoto zařízení.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Ověřte, zda jste [nakonfigurovali zásady RBAC](../../virtual-machines/linux/login-using-aad.md) pro virtuální počítač, které uživateli uděluje roli Přihlášení správce virtuálního počítače nebo Přihlášení uživatele virtuálního počítače:
 
#### <a name="unauthorized-client"></a>Neautorizovaný klient

Pokud se při iniciaci připojení ke vzdálené ploše k virtuálnímu počítači zobrazí následující chybová zpráva: 

- Vaše přihlašovací údaje nefungovaly

![Vaše přihlašovací údaje nefungovaly](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Ověřte, že počítač s Windows 10, který používáte k zahájení připojení ke vzdálené ploše, je ten, který je buď spojený s Azure AD, nebo hybridní Azure AD spojený se stejným adresářem Azure AD, ke kterému je připojen váš virtuální počítač. Další informace o identitě zařízení najdete v článku [Co je identita zařízení](/azure/active-directory/devices/overview).

> [!NOTE]
> Windows 10 20H1, přidá podporu pro Azure AD registrovaný počítač zahájit připojení ke vzdálené ploše k virtuálnímu počítači. Připojte se k programu Windows Insider a vyzkoušejte si to a prozkoumejte nové funkce Windows 10.

Také ověřte, ověřte, že rozšíření AADLoginForWindows nebylo odinstalováno po dokončení připojení služby Azure AD.
 
#### <a name="mfa-sign-in-method-required"></a>Metoda přihlášení vícefaktorové registrace je vyžadována.

Pokud se při iniciaci připojení ke vzdálené ploše k virtuálnímu počítači zobrazí následující chybová zpráva: 

- Metoda přihlášení, kterou se pokoušíte použít, není povolena. Vyzkoušejte jinou metodu přihlášení nebo se obraťte na správce systému.

![Metoda přihlášení, kterou se pokoušíte použít, není povolena.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Pokud jste nakonfigurovali zásadu podmíněného přístupu, která před přístupem k prostředku vyžaduje vícefaktorové ověřování (MFA), musíte zajistit, aby se počítač s Windows 10, který iniciuje připojení ke vzdálené ploše k vašemu virtuálnímu počítači, přihlašoval pomocí silného metoda ověřování, jako je Windows Hello. Pokud pro připojení ke vzdálené ploše nepoužíváte silnou metodu ověřování, zobrazí se předchozí chyba.

Pokud jste nenasadili Windows Hello pro firmy a pokud to zatím není možnost, můžete požadavek mfa vyloučit konfigurací zásad podmíněného přístupu, která vylučuje aplikaci "Azure Windows VM Sign-In" ze seznamu cloudových aplikací, které vyžadují vícefaktorové přístupy. Další informace o Windows Hello pro firmy najdete v [tématu Přehled Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Ověřování PIN kódů Windows Hello pro firmy pomocí protokolu RDP podporuje systém Windows 10 pro několik verzí, ale podpora biometrického ověřování pomocí protokolu RDP byla přidána v systému Windows 10 verze 1809. Použití windows hello pro obchodní auth během RDP je k dispozici pouze pro nasazení, které používají certifikát důvěryhodný model a aktuálně není k dispozici pro model důvěryhodnosti klíče.
 
## <a name="preview-feedback"></a>Zpětná vazba na verzi Preview

Podělte se o své názory týkající se této funkce náhledu nebo sestavy problémy pomocí na [fóru pro zpětnou vazbu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Active Directory najdete v tématu [Co je Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
