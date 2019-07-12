---
title: Povolit ověřování Azure Active Directory přes protokol SMB pro soubory Azure (preview) – služby Azure Storage
description: Zjistěte, jak povolit ověřování pomocí identity přes protokol SMB (Server Message Block) (preview) pro soubory Azure přes Azure Active Directory (Azure AD) Domain Services. Připojené k doméně Windows virtuálních počítačů (VM) pak můžou sdílenými složkami Azure pomocí přihlašovacích údajů Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696127"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Povolit ověřování Azure Active Directory Domain Services přes protokol SMB pro soubory Azure (Preview)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Přehled ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [přehled o Azure Active Directory authentication přes protokol SMB pro soubory Azure (Preview)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Přehled pracovního postupu
Než povolíte ověřování Azure AD DS přes protokol SMB pro soubory Azure, ověřte, že služby Azure AD a prostředích Azure Storage jsou správně nakonfigurované. Doporučuje se, že si projdete [požadavky](#prerequisites) abyste měli jistotu, že jste provedli všechny požadované kroky. 

V dalším kroku udělte přístup k prostředkům soubory Azure pomocí přihlašovacích údajů Azure AD pomocí následujících kroků: 

1. Povolte ověřování Azure AD DS přes protokol SMB pro váš účet úložiště k registraci účtu úložiště přidruženého nasazení služby Azure AD Domain Services.
2. Přiřadíte přístupová oprávnění pro sdílenou složku do identity Azure AD (uživatele, skupiny nebo instanční objekt služby).
3. Nakonfigurujte oprávnění NTFS pro adresářů a souborů přes protokol SMB.
4. Připojení sdílené složky Azure z virtuálního počítače připojené k doméně.

Následující diagram znázorňuje – komplexní pracovní postup pro povolení ověřování Azure AD DS přes protokol SMB pro soubory Azure. 

![Diagram znázorňující Azure AD prostřednictvím protokolu SMB pro pracovní postup službě soubory Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Požadavky 

Před povolením služby Azure AD prostřednictvím protokolu SMB pro soubory Azure, ujistěte se, že jste dokončili následující požadavky:

1.  **Vyberte nebo vytvořte tenanta služby Azure AD.**

    Nové nebo stávající tenanty můžete použít pro ověřování Azure AD prostřednictvím protokolu SMB. Tenanta a sdílené složce, která chcete získat přístup, musí být přidruženy stejném předplatném.

    K vytvoření nového tenanta Azure AD, můžete [přidání tenanta služby Azure AD a předplatné služby Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Pokud máte existujícího tenanta Azure AD, ale chcete vytvořit nového tenanta pro použití s Azure Files, přečtěte si téma [vytvoření tenanta Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Povolení služby Azure AD Domain Services na tenanta Azure AD.**

    Pro podporu ověřování pomocí přihlašovacích údajů Azure AD, musíte povolit Azure AD Domain Services pro vašeho tenanta Azure AD. Pokud si nejste správce tenanta Azure AD, obraťte se na správce a postupujte podle podrobných pokynů k [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/create-instance.md).

    Obvykle trvá přibližně 15 minut na dokončení nasazení služby Azure AD Domain Services. Ověřte, že se zobrazuje stav vaší služby Azure AD Domain Services **systémem**, s povolena synchronizace hodnot hash hesel, než budete pokračovat k dalšímu kroku.

3.  **Připojení k doméně virtuálního počítače Azure s Azure Active Directory Domain Services.**

    Pro přístup ke sdílené složce pomocí přihlašovacích údajů Azure AD z virtuálního počítače, musí být váš virtuální počítač připojený k doméně do Azure AD Domain Services. Další informace o tom, jak připojení k doméně virtuálního počítače, naleznete v tématu [připojení virtuálního počítače s Windows serverem do spravované domény](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Ověřování Azure AD DS přes protokol SMB se soubory Azure je podporován pouze na virtuálních počítačích Azure, které běží na verze operačního systému Windows 7 nebo Windows Server 2008 R2.

4.  **Vyberte nebo vytvořte sdílenou složku Azure.**

    Vyberte nový nebo existující sdílenou složku, která má přidružené k stejnému předplatnému jako vašeho tenanta Azure AD. Informace o vytváření nové sdílené složky najdete v tématu [vytvoření sdílené složky ve službě soubory Azure](storage-how-to-create-file-share.md). 
    Pro zajištění optimálního výkonu společnost Microsoft doporučuje, vaše sdílená složka je ve stejné oblasti jako virtuální počítač, ze kterého plánujete přístup do sdílené složky.

5.  **Ověřte připojení k Azure Files připojení sdílených složek Azure pomocí klíče účtu úložiště.**

    Pokud chcete ověřit, zda jsou správně nakonfigurovány virtuální počítač a sdílenou složku, zkuste připojení sdílené složky pomocí klíče účtu úložiště. Další informace najdete v tématu [připojení sdílené složky Azure a přístup do sdílené složky ve Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Povolit ověřování Azure AD DS pro váš účet

Pokud chcete povolit ověřování Azure AD DS přes protokol SMB pro soubory Azure, můžete nastavit vlastnost na účty úložiště vytvořené po 24. září 2018, pomocí webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure. Nastavení této vlastnosti zaregistruje účet úložiště se přidružené nasazení služby Azure AD Domain Services. Ověřování Azure AD DS přes protokol SMB je pak povolená pro všechny nové a existující sdílené složky v účtu úložiště. 

Uvědomte si, že až poté, co jste úspěšně nasadili Azure AD Domain Services pro vašeho tenanta Azure AD, můžete povolit ověřování Azure AD DS přes protokol SMB. Další informace najdete [požadavky](#prerequisites).

### <a name="azure-portal"></a>portál Azure

Pokud chcete povolit ověřování Azure AD DS přes pomocí protokolu SMB [webu Azure portal](https://portal.azure.com), postupujte podle těchto kroků:

1. Na webu Azure Portal, přejděte na svůj existující účet úložiště, nebo [vytvořit účet úložiště](../common/storage-quickstart-create-account.md).
2. V **nastavení** vyberte **konfigurace**.
3. Povolit **ověřování Azure Active Directory pro soubory Azure (preview)** .

Následující obrázek ukazuje, jak povolit ověřování Azure AD prostřednictvím protokolu SMB pro váš účet úložiště.

![Povolit ověřování Azure AD prostřednictvím protokolu SMB na webu Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Povolení ověřování Azure AD DS přes protokol SMB pomocí Azure Powershellu: Nejprve nainstalujte nejnovější modul Az (2.4 nebo novější) nebo modulu Az.Storage (1.5 nebo novější). Další informace o instalaci Powershellu najdete v tématu [nainstalujte prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Dále vytvořte nové úložiště účtu a potom voláním [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) a nastavit **EnableAzureActiveDirectoryDomainServicesForFile** parametr **true**. V následujícím příkladu nezapomeňte nahradit zástupné hodnoty vlastními hodnotami. (Pokud jste používali předchozí modul ve verzi Preview, je parametr k povolení funkce **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Pokud chcete povolit tuto funkci na existujících účtů úložiště, použijte následující příkaz:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Pokud chcete povolit ověřování Azure AD prostřednictvím protokolu SMB pomocí Azure CLI 2.0, nejprve nainstalujte `storage-preview` rozšíření:

```cli-interactive
az extension add --name storage-preview
```
  
Dále vytvořte nové úložiště účtu a potom voláním [aktualizace účtu úložiště az](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) a nastavit `--file-aad` vlastnost **true**. V následujícím příkladu nezapomeňte nahradit zástupné hodnoty vlastními hodnotami.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Přiřadit přístupová oprávnění identitě 

Pro přístup k prostředkům soubory Azure pomocí přihlašovacích údajů Azure AD, identitu (uživatele, skupiny nebo instanční objekt služby), musí mít potřebná oprávnění na úrovni sdílené složky. Tento proces je podobný zadání oprávnění ke sdílení Windows, kde zadáte typ přístupu, která má určitý uživatel ke sdílené složce. Pokyny v této části ukazuje, jak přiřadit číst, zapsat nebo odstranit oprávnění pro sdílenou složku na identitu.

Zavedli jsme dvě Azure předdefinované role pro udělování oprávnění na úrovni sdílené složky pro uživatele: Úložiště souboru dat SMB sdílené složky Čtenář a Přispěvatel sdílené složky SMB dat souboru služby Storage. 

- **Čtenář sdílené složky SMB dat soubor Storage** umožňuje přístup pro čtení v Azure Storage sdílené složky přes protokol SMB
- **Přispěvatel sdílené složky SMB dat soubor Storage** umožňuje čtení, zápisu a odstranění přístupu v Azure Storage sdílené složky přes protokol SMB

> [!IMPORTANT]
> Úplné řízení pro správu sdílené složky, včetně možnosti přiřazení role na identitu, vyžaduje použití klíče účtu úložiště. Přihlašovací údaje služby Azure AD nepodporuje administrativní řízení. 

Webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure můžete přiřadit předdefinované role Azure AD identity uživatele pro udělování oprávnění na úrovni sdílené složky. 

#### <a name="azure-portal"></a>portál Azure
Přiřazení RBAC role do identity Azure AD, pomocí [webu Azure portal](https://portal.azure.com), postupujte podle těchto kroků:

1. Na webu Azure Portal, přejděte do sdílené složky nebo [vytvoření sdílené složky ve službě soubory Azure](storage-how-to-create-file-share.md).
2. Vyberte **řízení přístupu (IAM)** .
3. Vyberte **přidat přiřazení role**
4. V **přidat přiřazení role** okno, vyberte odpovídající předdefinovaná role (čtenář sdílené složky SMB dat souboru služby Storage, Přispěvatel sdílené složky SMB dat souboru služby Storage) ze **Role** rozevíracího seznamu. Zachovat **přiřadit přístup k** jako výchozí: "Azure AD uživatele, skupinu nebo instanční objekt služby" a vyberte cíl identity Azure AD podle jména nebo e-mailové adresy. 
5. Nakonec vyberte **Uložit** k dokončení operace přiřazení role.

#### <a name="powershell"></a>PowerShell

Následující příkaz prostředí PowerShell ukazuje, jak přiřadit roli RBAC identity Azure AD, na základě názvu přihlášení. Další informace o přiřazení role RBAC pomocí Powershellu najdete v tématu [správě přístupu pomocí RBAC a prostředí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Při spuštění následující ukázkový skript, nezapomeňte nahradit zástupné hodnoty, včetně závorkách a s vlastními hodnotami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Rozhraní příkazového řádku
  
Následující příkaz rozhraní příkazového řádku 2.0 ukazuje, jak přiřadit roli RBAC identity Azure AD, na základě názvu přihlášení. Další informace o přiřazení role RBAC pomocí rozhraní příkazového řádku Azure najdete v tématu [správě přístupu pomocí RBAC a rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md). 

Při spuštění následující ukázkový skript, nezapomeňte nahradit zástupné hodnoty, včetně závorkách a s vlastními hodnotami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurace oprávnění NTFS přes protokol SMB 
Po přiřazení oprávnění na úrovni sdílené složky pomocí RBAC, je nutné přiřadit správná oprávnění systému souborů NTFS na kořenové, adresáře nebo úrovni souboru. Představte si oprávnění na úrovni sdílené složky jako vrátný vysoké úrovně, která určuje, zda má uživatel přístup sdílenou složku, zatímco systém souborů NTFS oprávnění jednat na podrobnější úrovni, chcete-li zjistit, jaké operace uživatele můžete provést na úrovni adresář nebo soubor. 

Služba soubory Azure podporuje úplnou sadu základních a pokročilých oprávnění systému souborů NTFS. Můžete zobrazit a nakonfigurovat oprávnění systému souborů NTFS na adresáře a soubory sdílené složky Azure tak, že připojení sdílené složky a pak spustit Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) nebo [nastavení seznamu ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) příkazu. 

> [!NOTE]
> Verze preview podporuje zobrazení s Průzkumníkem souborů Windows pouze oprávnění. Oprávnění k úpravám se ještě nepodporuje.

Pokud chcete nakonfigurovat oprávnění systému souborů NTFS s oprávněními superuživatele, musí připojit sdílenou složku se klíč účtu úložiště z vašeho virtuálního počítače připojené k doméně. Postupujte podle pokynů v další části a připojte sdílenou složku Azure z příkazového řádku a odpovídajícím způsobem nakonfigurovat oprávnění systému souborů NTFS.

V kořenovém adresáři sdílené složky jsou podporovány následující sadu oprávnění:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(Rx)
- BUILTIN\Users:(OI)(CI)(IO)(GR,ge)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- TVŮRCE OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Připojit sdílenou složku z příkazového řádku

Použít Windows **použití** příkaz pro připojení sdílené složky Azure. Nezapomeňte nahradit zástupné hodnoty v příkladu s vlastními hodnotami. Další informace o připojení sdílených složek, najdete v části [připojení sdílené složky Azure a přístup do sdílené složky ve Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Nakonfigurujte oprávnění systému souborů NTFS s icacls
Použijte následující příkaz Windows k udělena úplná oprávnění k všech adresářů a souborů v rámci sdílené složky, včetně kořenový adresář. Nezapomeňte nahradit zástupné hodnoty uvedené v závorkách v příkladu nahraďte vlastními hodnotami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Další informace o tom, jak můžete nastavit oprávnění systému souborů NTFS a na jiný typ oprávnění podporovány, naleznete v tématu icacls [odkaz na příkazový řádek pro icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Připojit sdílenou složku z virtuálního počítače připojené k doméně 

Nyní jste připraveni na ověření, že jste dokončili postup uvedený výš úspěšně pomocí vašich přihlašovacích údajů Azure AD pro přístup k Azure file sdílet z virtuálního počítače připojené k doméně. Nejdřív přihlaste k virtuálnímu počítači pomocí identity Azure AD, ke kterému jste udělili oprávnění, jak je znázorněno na následujícím obrázku.

![Snímek obrazovky zobrazující Azure AD přihlašovací obrazovka pro ověřování uživatelů](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

V dalším kroku použijte následující příkaz pro připojení sdílené složky Azure. Nezapomeňte nahradit zástupné hodnoty vlastními hodnotami. Protože je již byly ověřeny, není potřeba zadat klíč účtu úložiště nebo Azure AD uživatelského jména a hesla. Azure AD prostřednictvím protokolu SMB podporuje jednotné přihlašování pomocí přihlašovacích údajů Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Teď úspěšně povolili jste ověřování Azure AD prostřednictvím protokolu SMB a přiřadit vlastní roli, která poskytuje přístup ke sdílené složce do identity Azure AD. Dalším uživatelům udělit přístup ke sdílené složce, postupujte podle pokynů uvedených v kroku 2 a 3.

## <a name="next-steps"></a>Další postup

Další informace o službě soubory Azure a použití služby Azure AD prostřednictvím protokolu SMB naleznete v následujících zdrojích:

- [Úvod do služby soubory Azure](storage-files-introduction.md)
- [Přehled ověřování pomocí Azure Active Directory přes protokol SMB pro soubory Azure (Preview)](storage-files-active-directory-overview.md)
- [Nejčastější dotazy](storage-files-faq.md)
