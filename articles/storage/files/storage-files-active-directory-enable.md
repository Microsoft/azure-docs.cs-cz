---
title: Povolit ověřování Azure Active Directory přes protokol SMB pro soubory Azure (preview) – služby Azure Storage
description: Zjistěte, jak povolit ověřování pomocí identity přes protokol SMB (Server Message Block) (preview) pro soubory Azure přes Azure Active Directory (Azure AD) Domain Services. Připojené k doméně Windows virtuálních počítačů (VM) pak můžou sdílenými složkami Azure pomocí přihlašovacích údajů Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: tamram
ms.openlocfilehash: 6ee80aa7b7a58e2f02ed36d3c0c4b1a0889a906f
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831462"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Povolit ověřování Azure Active Directory přes protokol SMB pro soubory Azure (preview)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Přehled ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [přehled o Azure Active Directory authentication přes protokol SMB pro soubory Azure (Preview)](storage-files-active-directory-overview.md).

## <a name="workflow-overview"></a>Přehled pracovního postupu
Před povolením služby Azure AD prostřednictvím protokolu SMB pro soubory Azure, ověřte, že služby Azure AD a prostředích Azure Storage jsou správně nakonfigurované. Doporučuje se, že si projdete [požadavky](#prerequisites) abyste měli jistotu, že jste provedli všechny požadované kroky. 

V dalším kroku udělte přístup k prostředkům soubory Azure pomocí přihlašovacích údajů Azure AD pomocí následujících kroků: 

1. Povolte ověřování Azure AD prostřednictvím protokolu SMB pro váš účet úložiště k registraci účtu úložiště přidruženého nasazení služby Azure AD Domain Services.
2. Přiřadíte přístupová oprávnění pro sdílenou složku do identity Azure AD (uživatele, skupiny nebo instanční objekt služby).
3. Nakonfigurujte oprávnění NTFS pro adresářů a souborů přes protokol SMB.
4. Připojení sdílené složky Azure z virtuálního počítače připojené k doméně.

Následující diagram znázorňuje – komplexní pracovní postup pro povolení ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure. 

![Diagram znázorňující Azure AD prostřednictvím protokolu SMB pro pracovní postup službě soubory Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Požadavky 
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

    Tenant Azure AD se musí nasadit do oblasti, podporovaná ve verzi Preview služby Azure AD prostřednictvím protokolu SMB. Verzi preview je k dispozici ve všech veřejných oblastech s výjimkou: USA – Západ, USA – západ 2, střed USA – Jih, USA – východ, USA – východ 2, střed USA, střed USA – sever, Austrálie – východ, západní Evropa, Severní Evropa.

    Pro zajištění optimálního výkonu společnost Microsoft doporučuje, vaše sdílená složka je ve stejné oblasti jako virtuální počítač, ze kterého plánujete přístup do sdílené složky.

5.  **Ověřte připojení k Azure Files připojení sdílených složek Azure pomocí klíče účtu úložiště.**

    Pokud chcete ověřit, zda jsou správně nakonfigurovány virtuální počítač a sdílenou složku, zkuste připojení sdílené složky pomocí klíče účtu úložiště. Další informace najdete v tématu [připojení sdílené složky Azure a přístup do sdílené složky ve Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication"></a>Povolit ověřování Azure AD
Po dokončení [požadavky](#prerequisites), můžete povolit ověřování Azure AD prostřednictvím protokolu SMB.

### <a name="step-1-enable-azure-ad-authentication-over-smb-for-your-storage-account"></a>Krok 1: Povolení ověřování Azure AD prostřednictvím protokolu SMB pro váš účet úložiště
Pokud chcete povolit ověřování Azure AD pro soubory Azure přes protokol SMB, můžete nastavit vlastnost na účty úložiště vytvořené po 29. srpna 2018 se pomocí poskytovatele prostředků úložiště Azure z Powershellu nebo rozhraní příkazového řádku Azure. Pro verzi preview nepodporuje nastavení vlastnosti na webu Azure Portal. 

Nastavení této vlastnosti zaregistruje účet úložiště se přidružené nasazení služby Azure AD Domain Services. Ověřování Azure AD prostřednictvím protokolu SMB potom zapnutá pro všechny nové a existující sdílené složky v účtu úložiště. 

Uvědomte si, že až poté, co jste úspěšně nasadili Azure AD Domain Services pro vašeho tenanta Azure AD, můžete povolit ověřování Azure AD prostřednictvím protokolu SMB. Další informace najdete [požadavky](#prerequisites).

**Powershell**  
Pokud chcete povolit ověřování Azure AD prostřednictvím protokolu SMB, nainstalujte `AzureRM.Storage 6.0.0-preview` modul prostředí PowerShell. Informace o instalaci Powershellu najdete v tématu [nainstalujte prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Pak zavolejte [Set-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount) a nastavit **EnableAzureFilesAadIntegrationForSMB** parametr **true**. V následujícím příkladu nezapomeňte nahradit zástupné hodnoty vlastními hodnotami.

```powershell
# Create a new storage account
New-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
Set-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
```

**Rozhraní příkazového řádku**  
Pokud chcete povolit ověřování Azure AD prostřednictvím protokolu SMB pomocí Azure CLI 2.0, nejprve nainstalujte *úložiště ve verzi preview* rozšíření:

```azurecli-interactive
az extension add --name storage-preview
```

Pak zavolejte [aktualizace účtu úložiště az](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) a nastavit `--file-aad` vlastnost **true**. V následujícím příkladu nezapomeňte nahradit zástupné hodnoty vlastními hodnotami.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

### <a name="step-2-assign-access-permissions-to-an-identity"></a>Krok 2: Přiřazení oprávnění k přístupu k identitě 
Pro přístup k prostředkům soubory Azure pomocí přihlašovacích údajů Azure AD, identitu (uživatele, skupiny nebo instanční objekt služby), musí mít potřebná oprávnění na úrovni sdílené složky. Podrobné pokyny níže ukazuje, jak přiřadit číst, zapsat nebo odstranit oprávnění pro sdílenou složku na identitu.

> [!IMPORTANT]
> Úplné řízení pro správu sdílené složky, včetně možnosti přiřazení role na identitu, vyžaduje použití klíče účtu úložiště. Správu ovládací prvek není podporován pomocí přihlašovacích údajů Azure AD. 

#### <a name="step-21-define-a-custom-role"></a>Krok 2.1: Definování vlastní roli
Udělení oprávnění na úrovni sdílené složky, definovat vlastní roli RBAC a přiřaďte ho do identity, oborů do sdílené složky konkrétní. Tento proces je podobný zadání oprávnění ke sdílení Windows, kde zadáte typ přístupu, která má určitý uživatel ke sdílené složce.  

Šablony je znázorněno v následující části poskytují číst nebo změnit oprávnění pro sdílenou složku. Pokud chcete definovat vlastní roli, vytvořte soubor JSON a zkopírujte příslušnou šablonu do tohoto souboru. Další informace o definování vlastní role RBAC najdete v tématu [vlastní role v Azure](../../role-based-access-control/custom-roles.md).

**Definice role pro oprávnění na úrovni sdílení změn**  
Následující šablony vlastní role poskytuje oprávnění změnit na úrovni sdílené složky, poskytování identity čtení, zápisu a odstranění přístup ke sdílené složce.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share",
  "Actions": [
    "*"
  ],
  "NotActions": [
      "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
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
  "Description": "Allows for read access to Azure File Share",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

#### <a name="step-22-create-the-custom-role-and-assign-it-to-the-target-identity"></a>Krok 2.2: Vytvoření vlastní role a přiřaďte ho k cílové
Pak pomocí Powershellu nebo rozhraní příkazového řádku Azure vytvořte roli a přiřaďte ho do identity Azure AD. 

**Powershell**  
Pokud chcete povolit ověřování Azure AD prostřednictvím protokolu SMB, nainstalujte `AzureRM.Storage 6.0.0-preview` modul prostředí PowerShell. Informace o instalaci Powershellu najdete v tématu [nainstalujte prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Následující příkaz Powershellu vytvoří vlastní roli a přiřadí roli identity Azure AD, na základě názvu přihlášení. Další informace o přiřazení role RBAC pomocí Powershellu najdete v tématu [správě přístupu pomocí RBAC a prostředí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Při spuštění následující ukázkový skript, nezapomeňte nahradit zástupné hodnoty vlastními hodnotami.

```powershell
#Create a custom role based on the sample template above
New-AzureRmRoleDefinition -InputFile "<custom-role-def-json-path>"
#Get the name of the custom role
$FileShareContributorRole = Get-AzureRmRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzureRmRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

**Rozhraní příkazového řádku**  
Následující příkaz rozhraní příkazového řádku 2.0 vytvoří vlastní roli a přiřadí roli identity Azure AD, na základě názvu přihlášení. Další informace o přiřazení role RBAC pomocí rozhraní příkazového řádku Azure najdete v tématu [správě přístupu pomocí RBAC a rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md). 

Při spuštění následující ukázkový skript, nezapomeňte nahradit zástupné hodnoty vlastními hodnotami.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

### <a name="step-3-configure-ntfs-permissions-over-smb"></a>Krok 3: Konfigurace oprávnění NTFS přes protokol SMB 
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

#### <a name="step-31-mount-an-azure-file-share-from-the-command-prompt"></a>Krok 3.1 připojení sdílené složky Azure z příkazového řádku
Použít Windows **použití** příkaz pro připojení sdílené složky Azure. Nezapomeňte nahradit zástupné hodnoty v příkladu s vlastními hodnotami. Další informace o připojení sdílených složek, najdete v části [připojení sdílené složky Azure a přístup do sdílené složky ve Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

#### <a name="step-32-configure-ntfs-permissions-with-icacls"></a>Krok 3.2 konfiguraci systému souborů NTFS oprávnění s icacls
Použijte následující příkaz Windows k udělena úplná oprávnění k všech adresářů a souborů v rámci sdílené složky, včetně kořenový adresář. Nezapomeňte nahradit zástupné hodnoty v příkladu s vlastními hodnotami.

```
icacls <mounted-drive-letter> /grant <user-email>:(f)
```

Další informace o tom, jak můžete nastavit oprávnění systému souborů NTFS a na jiný typ oprávnění podporovány, naleznete v tématu icacls [odkaz na příkazový řádek pro icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="step-4-mount-an-azure-file-share-from-a-domain-joined-vm"></a>Krok 4: Připojení sdílené složky Azure z virtuálního počítače připojené k doméně 
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