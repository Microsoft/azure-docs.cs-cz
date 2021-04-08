---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560221"
---
## <a name="assign-access-permissions-to-an-identity"></a>Přiřazení přístupových oprávnění k identitě

Aby bylo možné získat přístup k prostředkům Azure Files s ověřováním na základě identity, musí mít identita (uživatel, skupina nebo instanční objekt) potřebná oprávnění na úrovni sdílené složky. Tento postup je podobný určení oprávnění pro sdílenou složku systému Windows, kde zadáte typ přístupu, který má určitý uživatel ke sdílené složce. Návod v této části ukazuje, jak přiřadit oprávnění ke čtení, zápisu a odstranění sdílené složky k identitě. 

Zavedli jsme tři předdefinované role Azure pro udělení oprávnění na úrovni sdílení pro uživatele:

- **Čtečka sdílené složky SMB souborů úložiště** umožňuje přístup pro čtení v Azure Storage sdílené složky přes protokol SMB.
- **Přispěvatel sdílené složky SMB soubor úložiště** umožňuje přístup ke čtení, zápisu a odstraňování v Azure Storage sdílených složkách přes SMB.
- **Soubor úložiště data sdílené složky SMB se zvýšenými oprávněními** umožňuje oprávnění ke čtení, zápisu, odstraňování a úpravám souborů NTFS v Azure Storage sdílených složkách přes SMB.

> [!IMPORTANT]
> Úplná Správa sdílené složky, včetně možnosti převzít vlastnictví souboru, vyžaduje použití klíče účtu úložiště. Pro přihlašovací údaje Azure AD se nepodporuje administrativní řízení.

Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete přiřadit předdefinované role k identitě uživatele Azure AD pro udělení oprávnění na úrovni sdílené složky. Mějte na paměti, že přiřazení role Azure na úrovni sdílené složky může nějakou dobu trvat. 

> [!NOTE]
> Pokud plánujete používat místní služba AD DS k ověřování, nezapomeňte [synchronizovat přihlašovací údaje služba AD DS do služby Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) . Synchronizace hodnot hash hesel z služba AD DS do služby Azure AD je volitelná. Identitě služby Azure AD, která je synchronizovaná z vašich místních služba AD DS, se udělí oprávnění na úrovni sdílené složky.

Obecně doporučujeme použít oprávnění na úrovni sdílené složky pro správu přístupu na vysokou úroveň do skupiny AD reprezentující skupinu uživatelů a identit a pak využít oprávnění NTFS pro detailní řízení přístupu na úrovni adresářů a souborů. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Přiřazení role Azure k identitě AD

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete přiřadit roli Azure k identitě Azure AD, použijte [Azure Portal](https://portal.azure.com)podle těchto kroků:

1. V Azure Portal přejdete do sdílené složky nebo [vytvoříte sdílenou složku](../articles/storage/files/storage-how-to-create-file-share.md).
2. Vyberte **Access Control (IAM)**.
3. Vyberte **Přidat přiřazení role** .
4. V okně **Přidat přiřazení role** vyberte příslušnou integrovanou roli (soubor úložiště, sdílenou složku SMB pro sdílení souborů úložiště, přispěvatel sdílené složky SMB) ze seznamu **rolí** . Nechte **přiřadit přístup k** výchozímu nastavení: **uživatel, skupina nebo instanční objekt služby Azure AD**. Vyberte cílovou identitu Azure AD podle jména nebo e-mailové adresy.
5. Výběrem **Uložit** dokončete operaci přiřazení role.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Následující ukázka prostředí PowerShell ukazuje, jak přiřadit k identitě Azure AD roli Azure na základě přihlašovacího jména. Další informace o přiřazování rolí Azure pomocí PowerShellu najdete v tématu [Správa přístupu pomocí RBAC a Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Před spuštěním následujícího ukázkového skriptu Nezapomeňte nahradit hodnoty zástupných symbolů, včetně závorek, vlastními hodnotami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
Následující příkaz CLI 2,0 ukazuje, jak přiřadit roli Azure k identitě Azure AD na základě přihlašovacího jména. Další informace o přiřazování rolí Azure pomocí rozhraní příkazového řádku Azure najdete v tématu [Správa přístupu pomocí RBAC a Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Před spuštěním následujícího ukázkového skriptu Nezapomeňte nahradit hodnoty zástupných symbolů, včetně závorek, vlastními hodnotami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurace oprávnění NTFS přes protokol SMB

Po přiřazení oprávnění na úrovni sdílení s RBAC musíte přiřadit správná oprávnění NTFS na úrovni kořenového adresáře, adresáře nebo souboru. Oprávnění na úrovni sdílené složky si můžete představit jako gatekeeper vysoké úrovně, které určuje, jestli uživatel může ke sdílené složce přistupovat. Vzhledem k tomu, že oprávnění NTFS fungují na podrobnější úrovni, aby bylo možné určit, které operace může uživatel provádět na úrovni adresáře nebo souboru.

Soubory Azure podporují úplnou sadu základních a rozšířených oprávnění systému souborů NTFS. Oprávnění NTFS můžete zobrazit a nakonfigurovat u adresářů a souborů ve sdílené složce Azure připojením sdílené složky a pak pomocí Průzkumníka souborů Windows nebo spuštěním příkazu Windows [Icacls](/windows-server/administration/windows-commands/icacls) nebo [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

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

Sdílenou složku Azure připojíte pomocí příkazu Windows **net use** . Nezapomeňte nahradit zástupné hodnoty v následujícím příkladu vlastními hodnotami. Další informace o připojení sdílených složek najdete v tématu [použití sdílené složky Azure v systému Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Pokud dochází k problémům s připojením k souborům Azure, přečtěte si prosím [Nástroj pro řešení potíží, který jsme publikovali pro chyby připojení k souborům Azure ve Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Poskytujeme také [pokyny](../articles/storage/files/storage-files-faq.md#on-premises-access) pro řešení scénářů při zablokování portu 445. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurace oprávnění NTFS pomocí Průzkumníka souborů Windows

Pomocí Průzkumníka souborů Windows udělte úplným oprávněním všem adresářům a souborům ve sdílené složce, včetně kořenového adresáře.

1. Otevřete Průzkumníka souborů Windows, klikněte pravým tlačítkem na soubor nebo adresář a vyberte **vlastnosti**.
2. Vyberte kartu **zabezpečení** .
3. Vyberte **Upravit...** ke změně oprávnění.
4. Můžete změnit oprávnění stávajících uživatelů nebo vybrat **Přidat...** a udělit jim oprávnění novým uživatelům.
5. V okně příkazového řádku pro přidání nových uživatelů zadejte cílové uživatelské jméno, kterému chcete udělit oprávnění, do pole **Zadejte názvy objektů k výběru** a vyberte možnost **kontrolovat názvy** a vyhledejte úplný název UPN cílového uživatele.
7.    Vyberte **OK**.
8.    Na kartě **zabezpečení** vyberte všechna oprávnění, která chcete novému uživateli udělit.
9.    Vyberte **Použít**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurace oprávnění systému souborů NTFS pomocí icacls

K udělení úplných oprávnění všem adresářům a souborům ve sdílené složce, včetně kořenového adresáře, použijte následující příkaz Windows. Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Další informace o tom, jak pomocí icacls nastavit oprávnění NTFS a v různých typech podporovaných oprávnění, najdete v tématu [Reference k příkazovému řádku pro icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Připojení sdílené složky z virtuálního počítače připojeného k doméně

Následující proces ověří, že se správně nastavila vaše sdílená složka a přístupová oprávnění a že máte přístup ke sdílené složce Azure z virtuálního počítače připojeného k doméně. Mějte na paměti, že přiřazení role Azure na úrovni sdílené složky může nějakou dobu trvat. 

Přihlaste se k virtuálnímu počítači pomocí identity Azure AD, ke které jste udělili oprávnění, jak je znázorněno na následujícím obrázku. Pokud jste povolili místní ověřování služba AD DS pro soubory Azure, použijte přihlašovací údaje pro služba AD DS. Pro ověřování Azure služba AD DS Přihlaste se pomocí přihlašovacích údajů Azure AD.

![Snímek obrazovky zobrazující přihlašovací obrazovku Azure AD pro ověřování uživatelů](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Sdílenou složku Azure připojíte pomocí následujícího příkazu. Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami. Vzhledem k tomu, že jste ověření, nemusíte zadávat klíč účtu úložiště, místní služba AD DS přihlašovací údaje ani přihlašovací údaje Azure služba AD DS. Možnosti jednotného přihlašování se podporují pro ověřování pomocí místních služba AD DS nebo Azure služba AD DS. Pokud narazíte na problémy s připojením k služba AD DS přihlašovací údaje, přečtěte si téma řešení potíží se [soubory Azure v systému Windows](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) , kde najdete pokyny.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```