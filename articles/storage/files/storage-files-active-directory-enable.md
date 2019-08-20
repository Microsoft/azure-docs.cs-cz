---
title: Povolení Azure Active Directory ověřování pomocí protokolu SMB pro soubory Azure – Azure Storage
description: Naučte se povolit ověřování na základě identity přes protokol SMB (Server Message Block) pro soubory Azure prostřednictvím Azure Active Directory Domain Services. Virtuální počítače s Windows připojené k doméně potom můžou přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: rogarana
ms.openlocfilehash: 23e8c82bad60675338a0482155ed8a92cdad3d3d
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617675"
---
# <a name="enable-azure-active-directory-domain-services-authentication-over-smb-for-azure-files"></a>Povolení Azure Active Directory Domain Services ověřování pomocí protokolu SMB pro soubory Azure
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Přehled ověřování Azure AD pomocí protokolu SMB pro soubory Azure najdete v tématu [přehled Azure Active Directory ověřování pomocí protokolu SMB pro soubory Azure](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Přehled pracovního postupu
Než povolíte službu Azure služba AD DS Authentication přes SMB pro soubory Azure, ověřte, že jsou správně nakonfigurovaná vaše prostředí Azure AD a Azure Storage. Doporučujeme projít si [požadavky](#prerequisites) a ujistit se, že jste dokončili všechny požadované kroky.

V dalším kroku udělte přístup k prostředkům Azure Files pomocí přihlašovacích údajů Azure AD pomocí následujících kroků: 

1. Povolte Azure služba AD DS ověřování přes SMB pro váš účet úložiště pro registraci účtu úložiště s přidruženým nasazením Azure služba AD DS.
2. Přiřazení oprávnění k přístupu ke sdílené složce identitě služby Azure AD (uživatel, skupina nebo instanční objekt).
3. Konfigurace oprávnění NTFS pro adresáře a soubory protokolu SMB.
4. Připojte sdílenou složku Azure z virtuálního počítače připojeného k doméně.

Následující diagram znázorňuje kompletní pracovní postup pro povolení ověřování Azure služba AD DS přes protokol SMB pro soubory Azure. 

![Diagram znázorňující pracovní postup Azure AD pomocí protokolu SMB pro Azure Files](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Požadavky

Než povolíte službu Azure AD přes SMB pro soubory Azure, ujistěte se, že jste dokončili následující požadavky:

1.  **Vyberte nebo vytvořte tenanta Azure AD.**

    Pro ověřování Azure AD prostřednictvím protokolu SMB můžete použít nového nebo stávajícího tenanta. Tenant a sdílená složka, ke kterým chcete získat přístup, musí být přidruženy ke stejnému předplatnému.

    Pokud chcete vytvořit nového tenanta Azure AD, můžete [Přidat tenanta Azure AD a předplatné služby Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Pokud máte existujícího tenanta Azure AD, ale chcete vytvořit nového tenanta pro použití se službou Azure Files, přečtěte si téma [Vytvoření klienta Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Povolí Azure AD Domain Services v tenantovi Azure AD.**

< < < < < < < VEDOUCÍm k podpoře ověřování pomocí přihlašovacích údajů Azure AD musíte povolit Azure AD Domain Services pro vašeho tenanta Azure AD. Pokud nejste správcem tenanta Azure AD, obraťte se na správce a postupujte podle podrobných pokynů, které vám [umožní Azure Active Directory Domain Services používání Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).
= = = = = = = = Pokud chcete podporovat ověřování pomocí přihlašovacích údajů Azure AD, musíte povolit Azure služba AD DS pro vašeho tenanta Azure AD. Pokud nejste správcem tenanta Azure AD, obraťte se na správce a postupujte podle podrobných pokynů k [povolení Azure Active Directory Domain Services pomocí Azure Portal](../../active-directory-domain-services/create-instance.md).
>>>>>>> 5f049740fef320adb172d59f68bd161bdc4318e1

    It typically takes about 15 minutes for an Azure AD DS deployment to complete. Verify that the health status of Azure AD DS shows **Running**, with password hash synchronization enabled, before proceeding to the next step.

3.  **Doména: připojení virtuálního počítače Azure s Azure služba AD DS.**

    Pro přístup ke sdílené složce pomocí přihlašovacích údajů Azure AD z virtuálního počítače musí být váš virtuální počítač připojený k doméně Azure služba AD DS. Další informace o tom, jak připojit virtuální počítač k doméně, najdete v tématu [připojení virtuálního počítače s Windows serverem ke spravované doméně](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Ověřování pomocí služby Azure služba AD DS přes protokol SMB se soubory Azure podporuje jenom na virtuálních počítačích Azure, na kterých běží verze operačního systému, na kterých je Windows 7 nebo Windows Server 2008 R2.

4.  **Vyberte nebo vytvořte sdílenou složku Azure.**

    Vyberte novou nebo existující sdílenou složku, která je přidružená ke stejnému předplatnému jako tenant služby Azure AD. Informace o vytvoření nové sdílené složky najdete v tématu [Vytvoření sdílené složky ve službě soubory Azure](storage-how-to-create-file-share.md). 
    Pro zajištění optimálního výkonu doporučujeme, aby byla sdílená složka ve stejné oblasti jako virtuální počítač, ze kterého plánujete přístup ke sdílené složce.

5.  **Pomocí klíče účtu úložiště ověřte připojení ke službě soubory Azure pomocí připojení sdílených složek Azure.**

    Pokud chcete ověřit, jestli je virtuální počítač a sdílená složka správně nakonfigurované, zkuste připojit sdílenou složku pomocí klíče účtu úložiště. Další informace najdete v tématu [připojení sdílené složky Azure a přístup ke sdílené složce v systému Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Povolit Azure služba AD DS ověřování pro váš účet

Pokud chcete povolit službu Azure služba AD DS Authentication přes protokol SMB pro soubory Azure, můžete nastavit vlastnost na účtech úložiště vytvořených po 24. září 2018 pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. Nastavením této vlastnosti se zaregistruje účet úložiště s přidruženým nasazením služby Azure služba AD DS. Pro všechny nové a existující sdílené složky v účtu úložiště se pak povolí ověřování Azure služba AD DS přes protokol SMB.

Mějte na paměti, že můžete povolit ověřování Azure služba AD DS přes protokol SMB až po úspěšném nasazení služba AD DS Azure do tenanta Azure AD. Další informace najdete v části [požadavky](#prerequisites).

### <a name="azure-portal"></a>portál Azure

Pokud chcete povolit ověřování pomocí služby Azure služba AD DS přes protokol SMB s [Azure Portal](https://portal.azure.com), postupujte podle následujících kroků:

1. V Azure Portal navštivte svůj existující účet úložiště nebo [vytvořte účet úložiště](../common/storage-quickstart-create-account.md).
2. V části **Nastavení** vyberte **Konfigurace**.
3. V rozevíracím seznamu **adresářová služba založená na identitě pro Azure File Authentication** vyberte **Azure Active Directory Domain Services (Azure služba AD DS)** .

Následující obrázek ukazuje, jak povolit Azure služba AD DS ověřování pomocí protokolu SMB pro váš účet úložiště.

![Povolit ověřování Azure AD přes SMB v Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Pokud chcete povolit ověřování Azure služba AD DS přes protokol SMB s Azure PowerShell, nainstalujte nejnovější modul AZ Module (2,4 nebo novější) nebo modul AZ. Storage (1,5 nebo novější). Další informace o instalaci PowerShellu najdete v tématu [instalace Azure PowerShell ve Windows pomocí PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Pokud chcete vytvořit nový účet úložiště, zavolejte [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)a pak nastavte parametr **EnableAzureActiveDirectoryDomainServicesForFile** na **true**. V následujícím příkladu Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami. (Pokud jste používali předchozí modul Preview, je parametr pro povolení funkcí **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Pokud chcete tuto funkci povolit u stávajících účtů úložiště, použijte následující příkaz:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Pokud chcete povolit ověřování Azure AD přes protokol SMB pomocí Azure CLI, nainstalujte nejnovější verzi rozhraní příkazového řádku (verze 2.0.70 nebo novější). Další informace o instalaci rozhraní příkazového řádku Azure najdete v tématu [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Chcete-li vytvořit nový účet úložiště, zavolejte příkaz[AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)a nastavte `--enable-files-aadds` vlastnost na **hodnotu true**. V následujícím příkladu Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami. (Pokud jste používali předchozí modul Preview, je parametr pro povolení funkce **File-AAD**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Pokud chcete tuto funkci povolit u stávajících účtů úložiště, použijte následující příkaz:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>Přiřazení přístupových oprávnění k identitě

Aby bylo možné získat přístup k prostředkům Azure Files s přihlašovacími údaji služby Azure AD, musí mít identita (uživatel, skupina nebo instanční objekt) potřebná oprávnění na úrovni sdílené složky. Tento postup je podobný určení oprávnění pro sdílenou složku systému Windows, kde zadáte typ přístupu, který má určitý uživatel ke sdílené složce. Návod v této části ukazuje, jak přiřadit oprávnění ke čtení, zápisu a odstranění sdílené složky k identitě.

Zavedli jsme dvě předdefinované role Azure pro udělení oprávnění na úrovni sdílení pro uživatele:

- **Čtečka sdílené složky SMB souborů úložiště** umožňuje přístup pro čtení v Azure Storage sdílené složky přes protokol SMB.
- **Přispěvatel sdílené složky SMB soubor úložiště** umožňuje přístup ke čtení, zápisu a odstraňování v Azure Storage sdílených složkách přes SMB.
- **Soubor úložiště data sdílené složky SMB se zvýšenými oprávněními** umožňuje oprávnění ke čtení, zápisu, odstraňování a úpravám souborů NTFS v Azure Storage sdílených složkách přes SMB.

> [!IMPORTANT]
> Úplná Správa sdílené složky, včetně možnosti přiřazení role k identitě, vyžaduje použití klíče účtu úložiště. Pro přihlašovací údaje Azure AD se nepodporuje administrativní řízení.

Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete přiřadit předdefinované role k identitě uživatele Azure AD pro udělení oprávnění na úrovni sdílené složky.

#### <a name="azure-portal"></a>portál Azure
K přiřazení role RBAC k identitě Azure AD použijte [Azure Portal](https://portal.azure.com)použijte následující postup:

1. V Azure Portal přejdete do sdílené složky nebo [vytvoříte sdílenou složku ve službě soubory Azure](storage-how-to-create-file-share.md).
2. Vyberte **řízení přístupu (IAM)** .
3. Vyberte **Přidat přiřazení role** .
4. V okně **Přidat přiřazení role** vyberte příslušnou integrovanou roli (soubor úložiště, sdílenou složku SMB pro sdílení souborů úložiště, přispěvatel sdílené složky SMB) ze seznamu **rolí** . Ponechejte možnost **přiřadit přístup k** výchozímu nastavení: **Uživatel, skupina nebo instanční objekt služby Azure AD**. Vyberte cílovou identitu Azure AD podle jména nebo e-mailové adresy.
5. Výběrem **Uložit** dokončete operaci přiřazení role.

#### <a name="powershell"></a>PowerShell

Následující příkaz prostředí PowerShell ukazuje, jak přiřadit roli RBAC k identitě Azure AD na základě přihlašovacího jména. Další informace o přiřazování rolí RBAC pomocí PowerShellu najdete v tématu [Správa přístupu pomocí RBAC a Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Před spuštěním následujícího ukázkového skriptu Nezapomeňte nahradit hodnoty zástupných symbolů, včetně závorek, vlastními hodnotami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Rozhraní příkazového řádku
  
Následující příkaz CLI 2,0 ukazuje, jak přiřadit roli RBAC k identitě Azure AD na základě přihlašovacího jména. Další informace o přiřazování rolí RBAC pomocí rozhraní příkazového řádku Azure najdete v tématu [Správa přístupu pomocí RBAC a Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Před spuštěním následujícího ukázkového skriptu Nezapomeňte nahradit hodnoty zástupných symbolů, včetně závorek, vlastními hodnotami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurace oprávnění NTFS přes protokol SMB 
Po přiřazení oprávnění na úrovni sdílení s RBAC musíte přiřadit správná oprávnění NTFS na úrovni kořenového adresáře, adresáře nebo souboru. Oprávnění na úrovni sdílené složky si můžete představit jako gatekeeper vysoké úrovně, které určuje, jestli uživatel může ke sdílené složce přistupovat. Vzhledem k tomu, že oprávnění NTFS fungují na podrobnější úrovni, aby bylo možné určit, které operace může uživatel provádět na úrovni adresáře nebo souboru.

Soubory Azure podporují úplnou sadu základních a rozšířených oprávnění systému souborů NTFS. Oprávnění NTFS můžete zobrazit a nakonfigurovat u adresářů a souborů ve sdílené složce Azure připojením sdílené složky a pak pomocí Průzkumníka souborů Windows nebo spuštěním příkazu Windows [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) nebo [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

Pokud chcete nakonfigurovat systém souborů NTFS pomocí uživatelských oprávnění, musíte sdílenou složku připojit pomocí klíče účtu úložiště na VIRTUÁLNÍm počítači připojeném k doméně. Podle pokynů v následující části připojte sdílenou složku Azure z příkazového řádku a patřičně nakonfigurujte oprávnění NTFS.

V kořenovém adresáři sdílené složky jsou podporovány následující sady oprávnění:

- BUILTIN\Administrators: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (v/v) (GR, GE)
- Uživatelé NT Authority\authenticated Users: (OI) – CI (M)
- NT AUTHORITY\SYSTEM: (F)
- CREATOR OWNER: (OI) (CI) (V/V) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Připojení sdílení souborů z příkazového řádku

Sdílenou složku Azure připojíte pomocí příkazu Windows **net use** . Nezapomeňte nahradit zástupné hodnoty v následujícím příkladu vlastními hodnotami. Další informace o připojování sdílených souborů najdete v tématu [připojení sdílené složky Azure a přístup ke sdílené složce ve Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurace oprávnění NTFS pomocí Průzkumníka souborů Windows
Pomocí Průzkumníka souborů Windows udělte úplným oprávněním všem adresářům a souborům ve sdílené složce, včetně kořenového adresáře.

1. Otevřete Průzkumníka souborů Windows, klikněte pravým tlačítkem na soubor nebo adresář a vyberte **vlastnosti** .
2. Klikněte na kartu **zabezpečení** .
3. Klikněte na **Upravit..** . tlačítko pro změnu oprávnění
4. Můžete změnit oprávnění stávajících uživatelů nebo kliknout na **Přidat...** a udělit jim oprávnění novým uživatelům.
5. V okně příkazového řádku pro přidání nových uživatelů zadejte cílové uživatelské jméno, kterému chcete udělit oprávnění, do pole **Zadejte názvy objektů k výběru** a kliknutím na **zaškrtávací políčko kontrolovat jména** vyhledejte úplný název UPN cílového uživatele.
7.  Klikněte na **OK** .
8.  Na kartě zabezpečení vyberte všechna oprávnění, která chcete nově přidaným uživatelům udělit.
9.  Klikněte na **použít** .

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurace oprávnění systému souborů NTFS pomocí icacls
K udělení úplných oprávnění všem adresářům a souborům ve sdílené složce, včetně kořenového adresáře, použijte následující příkaz Windows. Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Další informace o tom, jak pomocí icacls nastavit oprávnění NTFS a v různých typech podporovaných oprávnění, najdete v tématu [Reference k příkazovému řádku pro icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Připojení sdílené složky z virtuálního počítače připojeného k doméně

Následující proces ověří, že vaše přihlašovací údaje služby Azure AD byly správně nastaveny a že máte přístup ke sdílené složce Azure z virtuálního počítače připojeného k doméně: 

Přihlaste se k virtuálnímu počítači pomocí identity Azure AD, ke které jste udělili oprávnění, jak je znázorněno na následujícím obrázku.

![Snímek obrazovky zobrazující přihlašovací obrazovku Azure AD pro ověřování uživatelů](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Sdílenou složku Azure připojíte pomocí následujícího příkazu. Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami. Vzhledem k tomu, že jste už ověření, nemusíte zadávat klíč účtu úložiště ani uživatelské jméno a heslo pro Azure AD. Azure AD přes SMB podporuje jednotné přihlašování s přihlašovacími údaji Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Nyní jste úspěšně povolili ověřování Azure AD přes protokol SMB a přiřadili jste vlastní roli, která poskytuje přístup ke sdílené složce Azure pomocí identity Azure AD. Pokud chcete ostatním uživatelům udělit přístup ke sdílené složce, postupujte podle pokynů v části [přiřazení oprávnění k přístupu k identitě](#assign-access-permissions-to-an-identity) a [Konfigurace oprávnění NTFS](#configure-ntfs-permissions-over-smb) v oddílech SMB.

## <a name="next-steps"></a>Další postup

Další informace o službě soubory Azure a o tom, jak používat Azure AD přes SMB, najdete v těchto zdrojích:

- [Seznámení se soubory Azure](storage-files-introduction.md)
- [Přehled ověřování Azure Active Directory přes protokol SMB pro soubory Azure](storage-files-active-directory-overview.md)
- [Nejčastější dotazy](storage-files-faq.md)
