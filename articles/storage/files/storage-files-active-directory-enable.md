---
title: Povolit ověřování Azure Active Directory přes protokol SMB pro soubory Azure (preview) – služby Azure Storage
description: Zjistěte, jak povolit ověřování pomocí identity přes protokol SMB (Server Message Block) (preview) pro soubory Azure přes Azure Active Directory (Azure AD) Domain Services. Připojené k doméně Windows virtuálních počítačů (VM) pak můžou sdílenými složkami Azure pomocí přihlašovacích údajů Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.openlocfilehash: 03344cf989e1381f97b108e82b8d63e9c4653404
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53809794"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Povolit ověřování Azure Active Directory přes protokol SMB pro soubory Azure (preview)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Přehled ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [přehled o Azure Active Directory authentication přes protokol SMB pro soubory Azure (Preview)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Přehled pracovního postupu
Před povolením služby Azure AD prostřednictvím protokolu SMB pro soubory Azure, ověřte, že služby Azure AD a prostředích Azure Storage jsou správně nakonfigurované. Doporučuje se, že si projdete [požadavky](#prerequisites) abyste měli jistotu, že jste provedli všechny požadované kroky. 

V dalším kroku udělte přístup k prostředkům soubory Azure pomocí přihlašovacích údajů Azure AD pomocí následujících kroků: 

1. Povolte ověřování Azure AD prostřednictvím protokolu SMB pro váš účet úložiště k registraci účtu úložiště přidruženého nasazení služby Azure AD Domain Services.
2. Přiřadíte přístupová oprávnění pro sdílenou složku do identity Azure AD (uživatele, skupiny nebo instanční objekt služby).
3. Nakonfigurujte oprávnění NTFS pro adresářů a souborů přes protokol SMB.
4. Připojení sdílené složky Azure z virtuálního počítače připojené k doméně.

Následující diagram znázorňuje – komplexní pracovní postup pro povolení ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure. 

![Diagram znázorňující Azure AD prostřednictvím protokolu SMB pro pracovní postup službě soubory Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Požadavky 

Před povolením služby Azure AD prostřednictvím protokolu SMB pro soubory Azure, ujistěte se, že jste dokončili následující požadavky:

1.  **Vyberte nebo vytvořte tenanta služby Azure AD.**

    Nové nebo stávající tenanty můžete použít pro ověřování Azure AD prostřednictvím protokolu SMB. Tenanta a sdílené složce, která chcete získat přístup, musí být přidruženy stejném předplatném.

    K vytvoření nového tenanta Azure AD, můžete [přidání tenanta služby Azure AD a předplatné služby Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Pokud máte existujícího tenanta Azure AD, ale chcete vytvořit nového tenanta pro použití s Azure Files, přečtěte si téma [vytvoření tenanta Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Povolení služby Azure AD Domain Services na tenanta Azure AD.**

    Pro podporu ověřování pomocí přihlašovacích údajů Azure AD, musíte povolit Azure AD Domain Services pro vašeho tenanta Azure AD. Pokud si nejste správce tenanta Azure AD, obraťte se na správce a postupujte podle podrobných pokynů k [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md).

    Obvykle trvá přibližně 15 minut na dokončení nasazení služby Azure AD Domain Services. Ověřte, že se zobrazuje stav vaší služby Azure AD Domain Services **systémem**, s povolena synchronizace hodnot hash hesel, než budete pokračovat k dalšímu kroku.

3.  **Připojení k doméně virtuálního počítače Azure s Azure Active Directory Domain Services.**

    Pro přístup ke sdílené složce pomocí přihlašovacích údajů Azure AD z virtuálního počítače, musí být váš virtuální počítač připojený k doméně do Azure AD Domain Services. Další informace o tom, jak připojení k doméně virtuálního počítače, naleznete v tématu [připojení virtuálního počítače s Windows serverem do spravované domény](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md).

    > [!NOTE]
    > Ověřování Azure AD prostřednictvím protokolu SMB se soubory Azure je podporován pouze na virtuálních počítačích Azure, které běží na verze operačního systému Windows 7 nebo Windows Server 2008 R2.

4.  **Vyberte nebo vytvořte sdílenou složku Azure.**

    Vyberte nový nebo existující sdílenou složku, která má přidružené k stejnému předplatnému jako vašeho tenanta Azure AD. Informace o vytváření nové sdílené složky najdete v tématu [vytvoření sdílené složky ve službě soubory Azure](storage-how-to-create-file-share.md). 

    Tenant Azure AD se musí nasadit do oblasti, podporovaná ve verzi Preview služby Azure AD prostřednictvím protokolu SMB. Verzi preview je k dispozici ve všech veřejných oblastech s výjimkou: USA – Západ, USA – západ 2, střední část jihu USA, USA – východ, USA – východ 2, střed USA, USA (střed) – sever, Austrálie – východ, západní Evropa, Severní Evropa.

    Pro zajištění optimálního výkonu společnost Microsoft doporučuje, vaše sdílená složka je ve stejné oblasti jako virtuální počítač, ze kterého plánujete přístup do sdílené složky.

5.  **Ověřte připojení k Azure Files připojení sdílených složek Azure pomocí klíče účtu úložiště.**

    Pokud chcete ověřit, zda jsou správně nakonfigurovány virtuální počítač a sdílenou složku, zkuste připojení sdílené složky pomocí klíče účtu úložiště. Další informace najdete v tématu [připojení sdílené složky Azure a přístup do sdílené složky ve Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication-for-your-account"></a>Povolit ověřování Azure AD pro váš účet

Pokud chcete povolit ověřování Azure AD pro soubory Azure přes protokol SMB, můžete nastavit vlastnost na účty úložiště vytvořené po 24. září 2018, pomocí webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure. Nastavení této vlastnosti zaregistruje účet úložiště se přidružené nasazení služby Azure AD Domain Services. Ověřování Azure AD prostřednictvím protokolu SMB potom zapnutá pro všechny nové a existující sdílené složky v účtu úložiště. 

Uvědomte si, že až poté, co jste úspěšně nasadili Azure AD Domain Services pro vašeho tenanta Azure AD, můžete povolit ověřování Azure AD prostřednictvím protokolu SMB. Další informace najdete [požadavky](#prerequisites).

### <a name="azure-portal"></a>portál Azure

Chcete-li povolit ověřování Azure AD pomocí protokolu SMB [webu Azure portal](https://portal.azure.com), postupujte podle těchto kroků:

1. Na webu Azure Portal, přejděte na svůj existující účet úložiště, nebo [vytvořit účet úložiště](../common/storage-quickstart-create-account.md).
2. V **nastavení** vyberte **konfigurace**.
3. Povolit **ověřování Azure Active Directory pro soubory Azure (preview)**.

Následující obrázek ukazuje, jak povolit ověřování Azure AD prostřednictvím protokolu SMB pro váš účet úložiště.

![Povolit ověřování Azure AD prostřednictvím protokolu SMB na webu Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Pokud chcete povolit ověřování Azure AD prostřednictvím protokolu SMB v Azure Powershellu, nejprve nainstalovat sestavení ve verzi preview `Az.Storage` modulu s podporou služby Azure AD. Další informace o instalaci Powershellu najdete v tématu [nainstalujte prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Dále vytvořte nové úložiště účtu a potom voláním [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) a nastavit **EnableAzureFilesAadIntegrationForSMB** parametr **true**. V následujícím příkladu nezapomeňte nahradit zástupné hodnoty vlastními hodnotami.

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
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

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Přiřadit přístupová oprávnění identitě 

Pro přístup k prostředkům soubory Azure pomocí přihlašovacích údajů Azure AD, identitu (uživatele, skupiny nebo instanční objekt služby), musí mít potřebná oprávnění na úrovni sdílené složky. Pokyny v této části ukazuje, jak přiřadit číst, zapsat nebo odstranit oprávnění pro sdílenou složku na identitu.

> [!IMPORTANT]
> Úplné řízení pro správu sdílené složky, včetně možnosti přiřazení role na identitu, vyžaduje použití klíče účtu úložiště. Správu ovládací prvek není podporován pomocí přihlašovacích údajů Azure AD. 

### <a name="define-a-custom-role"></a>Definovat vlastní roli

Udělení oprávnění na úrovni sdílené složky, definovat vlastní roli RBAC a přiřaďte ho do identity, oborů do sdílené složky konkrétní. Tento proces je podobný zadání oprávnění ke sdílení Windows, kde zadáte typ přístupu, která má určitý uživatel ke sdílené složce.  

Šablony je znázorněno v následující části poskytují číst nebo změnit oprávnění pro sdílenou složku. Pokud chcete definovat vlastní roli, vytvořte soubor JSON a zkopírujte příslušnou šablonu do tohoto souboru. Další informace o definování vlastní role RBAC najdete v tématu [vlastní role v Azure](../../role-based-access-control/custom-roles.md).

**Definice role pro oprávnění na úrovni sdílení změn**  
Následující šablony vlastní role poskytuje oprávnění změnit na úrovni sdílené složky, poskytování identity čtení, zápisu a odstranění přístup ke sdílené složce.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share over SMB",
  "Actions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshare/*"
  ],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/*"
  ],
  "NotDataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermission",
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actasadmin"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**Definice role pro oprávnění ke čtení úrovni sdílené složky**  
Následující šablony vlastní role poskytuje oprávnění ke čtení úrovni sdílené složky, udělení přístupu pro čtení identity ke sdílené složce.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share over SMB",
  "Actions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshare/read"
  ],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

### <a name="create-the-custom-role"></a>Vytvořit vlastní roli

Chcete-li vytvořit vlastní roli, pomocí Powershellu nebo rozhraní příkazového řádku Azure. 

#### <a name="powershell"></a>PowerShell

Následující příkaz Powershellu vytvoří vlastní roli na základě jedné z ukázkových šablon.

```powershell
#Create a custom role based on the sample template above
New-AzRoleDefinition -InputFile "<custom-role-def-json-path>"
```

#### <a name="cli"></a>Rozhraní příkazového řádku 

Následující příkaz Azure CLI vytvoří vlastní roli na základě jedné z ukázkových šablon.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
```

### <a name="assign-the-custom-role-to-the-target-identity"></a>Přiřazení vlastní role cílové

V dalším kroku pomocí Powershellu nebo rozhraní příkazového řádku Azure přiřazení vlastní role do identity Azure AD. 

#### <a name="powershell"></a>PowerShell

Následující příkaz prostředí PowerShell ukazuje, jak seznamu k dispozici vlastní role a potom přiřadit vlastní roli identity Azure AD, na základě názvu přihlášení. Další informace o přiřazení role RBAC pomocí Powershellu najdete v tématu [správě přístupu pomocí RBAC a prostředí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Při spuštění následující ukázkový skript, nezapomeňte nahradit zástupné hodnoty, včetně závorkách a s vlastními hodnotami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Rozhraní příkazového řádku
  
Následující příkaz rozhraní příkazového řádku 2.0 ukazuje, jak seznamu k dispozici vlastní role a potom přiřadit vlastní roli identity Azure AD, na základě názvu přihlášení. Další informace o přiřazení role RBAC pomocí rozhraní příkazového řádku Azure najdete v tématu [správě přístupu pomocí RBAC a rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md). 

Při spuštění následující ukázkový skript, nezapomeňte nahradit zástupné hodnoty, včetně závorkách a s vlastními hodnotami.

```azurecli-interactive
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
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
