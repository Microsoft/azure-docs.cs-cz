---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: c88f5a4dd4f2997ce01b1f6a3ae192c62f530e76
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011412"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. Přiřazení přístupových oprávnění k identitě

Chcete-li získat přístup k prostředkům Souborů Azure pomocí ověřování na základě identity, musí mít identita (uživatel, skupina nebo instanční objekt) potřebná oprávnění na úrovni sdílené složky. Tento proces je podobný určení oprávnění ke sdílení systému Windows, kde zadáte typ přístupu, který má konkrétní uživatel ke sdílené složce. Pokyny v této části ukazují, jak přiřadit oprávnění pro čtení, zápis nebo odstranění sdílené složky identitě. 

Zavedli jsme tři předdefinované role Azure pro udělování oprávnění na úrovni sdílení uživatelům:

- **Data úložiště Dat SMB Share Reader** umožňuje přístup pro čtení ve sdílených složek úložiště Azure přes SMB.
- **Data úložiště SMB Share Contributor** umožňuje čtení, zápis a odstranění přístupu ve sdílených složek úložiště Azure přes SMB.
- **Data úložiště dat SMB Share Elevated Contributor** umožňuje čtení, zápis, odstranění a úpravu oprávnění NTFS ve sdílených složkách úložiště Azure přes SMB.

> [!IMPORTANT]
> Úplná administrativní kontrola sdílené složky, včetně možnosti převzít vlastnictví souboru, vyžaduje použití klíče účtu úložiště. Správa není podporována s přihlašovacími údaji Azure AD.

Pomocí portálu Azure, PowerShellu nebo rozhraní api Azure můžete přiřadit předdefinované role k identitě Azure AD uživatele pro udělování oprávnění na úrovni sdílení.

> [!NOTE]
> Nezapomeňte synchronizovat přihlašovací údaje služby AD do služby Azure AD, pokud chcete službu AD používat k ověřování. Synchronizace hash hesla ze služby AD do služby Azure AD je volitelná. Oprávnění na úrovni sdílení bude uděleno identitě Azure AD, která se synchronizuje ze služby AD.

Obecným doporučením je použít oprávnění na úrovni sdílení pro správu přístupu na vysoké úrovni ke skupině služby AD představující skupinu uživatelů a identit a potom využít oprávnění ntfs pro podrobné řízení přístupu na úrovni adresáře nebo souboru. 

#### <a name="azure-portal"></a>portál Azure
Pokud chcete přiřadit roli RBAC k identitě Azure AD pomocí [portálu Azure](https://portal.azure.com), postupujte takto:

1. Na webu Azure Portal přejděte do sdílené složky nebo [vytvořte sdílenou složku](../articles/storage/files/storage-how-to-create-file-share.md).
2. Vyberte **řízení přístupu (IAM).**
3. Vyberte **Přidat přiřazení role.**
4. V okně **Přidat přiřazení role** vyberte příslušnou předdefinovanou roli (čtečka dat souboru úložiště SMB, přispěvatel sdílené složky souboru úložiště SMB) ze seznamu **rolí.** Ponechat **Přiřadit přístup na** výchozí nastavení: **Uživatel, skupina nebo instanční objekt Služby Azure AD**. Vyberte cílovou identitu Azure AD podle názvu nebo e-mailové adresy.
5. Chcete-li dokončit operaci přiřazení role, vyberte **uložit.**

#### <a name="powershell"></a>PowerShell

Následující ukázka prostředí PowerShell ukazuje, jak přiřadit roli RBAC k identitě Azure AD na základě přihlašovacího jména. Další informace o přiřazování rolí RBAC pomocí PowerShellu najdete v [tématu Správa přístupu pomocí RBAC a Azure PowerShellu](../articles/role-based-access-control/role-assignments-powershell.md).

Před spuštěním následujícíukázkový skript, nezapomeňte nahradit zástupné hodnoty, včetně závorek, s vlastními hodnotami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Rozhraní příkazového řádku
  
Následující příkaz CLI 2.0 ukazuje, jak přiřadit roli RBAC k identitě Azure AD na základě přihlašovacího jména. Další informace o přiřazování rolí RBAC pomocí rozhraní příkazového příkazu k řešení Azure najdete [v tématu Správa přístupu pomocí RBAC a Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Před spuštěním následujícíukázkový skript, nezapomeňte nahradit zástupné hodnoty, včetně závorek, s vlastními hodnotami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. Konfigurace oprávnění NTFS přes SMB 
Po přiřazení oprávnění na úrovni sdílené složky pomocí nástroje RBAC je nutné přiřadit správná oprávnění systému souborů NTFS na úrovni kořenového adresáře, adresáře nebo souboru. Představte si oprávnění na úrovni sdílené složky jako správce brány na vysoké úrovni, který určuje, zda má uživatel přístup ke sdílené položce. Zatímco oprávnění systému souborů NTFS jednají na podrobnější úrovni, aby určila, jaké operace může uživatel provést na úrovni adresáře nebo souboru.

Soubory Azure podporuje úplnou sadu základních a pokročilých oprávnění ntfs. Oprávnění systému souborů NTFS pro adresáře a soubory ve sdílené složce Azure můžete zobrazit a nakonfigurovat tak, že se sdílenou složku a potom pomocí Průzkumníka souborů Windows nebo spusťte příkaz Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) nebo [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 

Chcete-li nakonfigurovat systém souborů NTFS s oprávněními superuživatele, je nutné připojit sdílenou složku pomocí klíče účtu úložiště z virtuálního počítače připojovaného k doméně. Podle pokynů v další části připojte sdílenou složku Azure z příkazového řádku a odpovídajícím způsobem nakonfigurujte oprávnění ntfs.

V kořenovém adresáři sdílené složky jsou podporovány následující sady oprávnění:

- BUILTIN\Správci:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Uživatelé:(RX)
- BUILTIN\Uživatelé:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT Autorita\SYSTÉM:(F)
- TVŮRCE VLASTNÍK:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurace oprávnění ntfs pomocí icacls
Pomocí následujícího příkazu systému Windows udělte úplná oprávnění všem adresářům a souborům ve sdílené složce, včetně kořenového adresáře. Nezapomeňte nahradit zástupné hodnoty v příkladu vlastními hodnotami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Další informace o použití icacls k nastavení oprávnění NTFS a o různých typech podporovaných oprávnění naleznete [v odkazu na příkazový řádek pro icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Připojení sdílené složky z příkazového řádku

Pomocí příkazu **Windows net use** můžete připojit sdílenou složku Azure. Nezapomeňte nahradit zástupné hodnoty v následujícím příkladu vlastními hodnotami. Další informace o připojujících se sdílených složek najdete [v tématu Použití sdílené složky Azure se systémem Windows](../articles/storage/files/storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurace oprávnění ntfs pomocí Průzkumníka souborů systému Windows
Pomocí Průzkumníka souborů systému Windows udělte úplná oprávnění všem adresářům a souborům ve sdílené složce, včetně kořenového adresáře.

1. Otevřete Průzkumníka souborů systému Windows a klikněte pravým tlačítkem myši na soubor/adresář a vyberte **vlastnosti**
2. Klikněte na kartu **Zabezpečení.**
3. Klikněte na **Edit..**. tlačítko pro změnu oprávnění
4. Můžete změnit oprávnění stávajících uživatelů nebo kliknout na **přidat...** a udělit oprávnění novým uživatelům.
5. V okně s výzvou pro přidání nových uživatelů zadejte cílové uživatelské jméno, ke kterým chcete udělit oprávnění, do pole **Zadejte názvy objektů, které chcete vybrat,** a kliknutím na **Zkontrolovat jména** vyhledejte úplný název hlavního názvu cílového uživatele.
7.  Klikněte na **OK**
8.  Na kartě Zabezpečení vyberte všechna oprávnění, která chcete nově přidanému uživateli udělit.
9.  Klikněte na **Použít**

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. Připojení sdílené složky z virtuálního zařízení přilehlého k doméně

Následující proces ověří, že vaše sdílení souborů a přístupová oprávnění byla nastavena správně a že můžete přistupovat ke sdílené složce Azure ze virtuálního počítače připojovaného k doméně. Uvědomte si, že přiřazení role RBAC na úrovni sdílení může nějakou dobu trvat, než bude v platnosti. 

Přihlaste se k virtuálnímu počítači pomocí identity Azure AD, které jste udělili oprávnění, jak je znázorněno na následujícím obrázku. Pokud jste povolili ověřování služby AD pro soubory Azure, použijte pověření služby AD. Pro ověřování Azure AD DS se přihlaste pomocí přihlašovacích údajů Služby Azure AD.

![Snímek obrazovky s přihlašovací obrazovkou Azure AD pro ověřování uživatelů](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Pomocí následujícího příkazu připojte sdílenou složku Azure. Nezapomeňte nahradit zástupné hodnoty vlastními hodnotami. Vzhledem k tomu, že jste byli ověřeni, nemusíte zadejte klíč účtu úložiště, přihlašovací údaje služby AD nebo přihlašovací údaje Azure AD. Jednotné přihlašování je podporováno pro ověřování pomocí služby AD nebo Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
