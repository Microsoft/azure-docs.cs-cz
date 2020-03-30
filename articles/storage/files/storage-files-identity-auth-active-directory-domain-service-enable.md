---
title: Autorizace přístupu k datům souborů přes SMB pomocí služby Azure AD Domain Services
description: Zjistěte, jak povolit ověřování na základě identity přes blok zpráv serveru (SMB) pro soubory Azure prostřednictvím služby Azure Active Directory Domain Services. Virtuální počítače windows (VM) spojené s doménou pak můžou přistupovat ke sdíleným spodám Azure pomocí přihlašovacích údajů Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e2e3c7763a13c8850554b079a426ed4172b74d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599272"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Povolení ověřování služby Azure Active Directory Domain Services u souborů Azure

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Přehled ověřování Azure AD přes SMB pro sdílené složky Azure najdete v [tématu Přehled ověřování Azure Active Directory přes SMB pro soubory Azure](storage-files-active-directory-overview.md). Tento článek se zaměřuje na povolení ověřování pomocí služby Azure Active Directory Domain Services (Azure AD DS) v souborech Azure.

> [!NOTE]
> Azure Files podporuje ověřování kerberos pomocí Azure AD DS s rc4-HMAC šifrováním. Šifrování Protokolu AES Kerberos ještě není podporováno.

## <a name="prerequisites"></a>Požadavky

Než povolíte Azure AD přes SMB pro sdílené složky Azure, ujistěte se, že jste dokončili následující požadavky:

1.  **Vyberte nebo vytvořte klienta Azure AD.**

    Můžete použít nového nebo stávajícího klienta pro ověřování Azure AD přes SMB. Tenant a sdílené složky, ke které chcete získat přístup, musí být přidruženy ke stejnému předplatnému.

    Pokud chcete vytvořit nového klienta Azure AD, můžete [přidat klienta Azure AD a předplatné Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Pokud máte existujícího klienta Azure AD, ale chcete vytvořit nového klienta pro použití se sdílenými složkami Azure, přečtěte si informace [o vytvoření klienta Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Povolte služby Domény Azure AD v tenantovi Azure AD.**

    Chcete-li podporovat ověřování pomocí přihlašovacích údajů Azure AD, musíte povolit služby Azure AD Domain Services pro vašeho klienta Azure AD. Pokud nejste správcem klienta Azure AD, obraťte se na správce a postupujte podle podrobných pokynů k [povolení služby Azure Active Directory Domain Services pomocí portálu Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Obvykle trvá přibližně 15 minut pro nasazení Azure AD DS k dokončení. Před pokračováním dalšího kroku ověřte, zda stav služby Azure AD DS zobrazuje **spuštění**s povolenou synchronizací hodnot hash hesel.

1.  **Připojení k virtuálnímu počítači Azure pomocí Azure AD DS.**

    Chcete-li získat přístup ke sdílené složce pomocí přihlašovacích údajů Azure AD z virtuálního počítače, musí být váš virtuální počítač připojen k azure ad ds. Další informace o připojení k virtuálnímu počítači dodomén najdete v [tématu Připojení virtuálního počítače se systémem Windows Server ke spravované doméně](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure AD DS ověřování přes SMB s sdílenými složkami azure souborů je podporované jenom na virtuálních počítačích Azure běží na verzích operačního systému nad Windows 7 nebo Windows Server 2008 R2.

1.  **Vyberte nebo vytvořte sdílenou složku Azure.**

    Vyberte novou nebo existující sdílenou složku, která je přidružená ke stejnému předplatnému jako váš klient Azure AD. Informace o vytváření nové sdílené složky najdete [v tématu Vytvoření sdílené složky v Azure Files](storage-how-to-create-file-share.md).
    Pro optimální výkon doporučujeme, aby vaše sdílení souborů bylo ve stejné oblasti jako virtuální počítač, ze kterého plánujete přístup ke sdílené položce.

1.  **Ověřte připojení souborů Azure připojením sdílených složek Azure pomocí klíče účtu úložiště.**

    Chcete-li ověřit, zda je virtuální počítač a sdílená složka správně nakonfigurované, zkuste sdílet soubor pomocí klíče účtu úložiště. Další informace najdete [v tématu Připojení sdílené složky Azure a přístup ke sdílené složce ve Windows](storage-how-to-use-files-windows.md).

## <a name="overview-of-the-workflow"></a>Přehled pracovního postupu

Než povolíte ověřování azure ad ds přes SMB pro sdílené složky Azure, ověřte, že vaše prostředí Azure AD a Azure Storage jsou správně nakonfigurovaná. Doporučujeme, abyste si [prošli předpoklady](#prerequisites) a ujistili se, že jste dokončili všechny požadované kroky.

Dále udělte přístup k prostředkům Azure Files pomocí přihlašovacích údajů Azure AD následujícím postupem:

- Povolte ověřování Azure AD DS přes SMB pro váš účet úložiště k registraci účtu úložiště s přidruženým nasazením Azure AD DS.
- Přiřaďte přístupová oprávnění ke sdílené položce identitě Azure AD (uživateli, skupině nebo instančnímu objektu služby).
- Konfigurace oprávnění systému souborů NTFS přes Protokol SMB pro adresáře a soubory.
- Připojení sdílené složky Azure z virtuálního počítače přilehlého k doméně.

Následující diagram znázorňuje pracovní postup od konce pro povolení ověřování Azure AD DS přes SMB pro soubory Azure.

![Diagram znázorňující pracovní postup Azure AD přes SMB pro soubory Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Povolení ověřování Azure AD DS pro váš účet

Pokud chcete povolit ověřování Azure AD DS přes SMB pro soubory Azure, můžete nastavit vlastnost na účtech úložiště pomocí portálu Azure, Azure PowerShell nebo Azure CLI. Nastavení této vlastnosti implicitně "doména připojí" účet úložiště s přidružené nasazení Azure AD DS. Ověřování Azure AD DS přes SMB je pak povolena pro všechny nové a existující sdílené složky v účtu úložiště.

Nezapomeňte, že ověřování Azure AD DS přes SMB můžete povolit až po úspěšném nasazení služby Azure AD DS do klienta Azure AD. Další informace naleznete v [požadavcích](#prerequisites)na následující požadavky .

### <a name="azure-portal"></a>portál Azure

Pokud chcete povolit ověřování Azure AD DS přes SMB s [portálem Azure](https://portal.azure.com), postupujte takto:

1. Na webu Azure Portal přejděte na svůj stávající účet úložiště nebo [si vytvořte účet úložiště](../common/storage-account-create.md).
1. V části **Nastavení** vyberte **Konfigurace**.
1. V části **Přístup ke sdíleným položkám založený na identitě** přepněte přepínač pro **službu Azure Active Directory Domain Service (AAD DS)** na **Povoleno**.
1. Vyberte **Uložit**.

Následující obrázek ukazuje, jak povolit ověřování Azure AD DS přes SMB pro váš účet úložiště.

![Povolení ověřování Azure AD DS přes SMB na webu Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Pokud chcete povolit ověřování Azure AD DS přes SMB pomocí Azure PowerShellu, nainstalujte nejnovější modul Az (2.4 nebo novější) nebo modul Az.Storage (1.5 nebo novější). Další informace o instalaci PowerShellu najdete [v tématu Instalace Azure PowerShellu v systému Windows pomocí PowerShellu](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Chcete-li vytvořit nový účet úložiště, zavolejte [new-azstorageaccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)a nastavte parametr **EnableAzureActiveDirectoryDomainServicesForFile** na **hodnotu true**. V následujícím příkladu nezapomeňte nahradit zástupné hodnoty vlastními hodnotami. (Pokud jste používali předchozí modul náhledu, parametr pro povolení funkce je **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Chcete-li tuto funkci povolit na existujících účtech úložiště, použijte následující příkaz:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Pokud chcete povolit ověřování Azure AD přes SMB pomocí azure CLI, nainstalujte nejnovější verzi příkazového příkazového příkazu (verze 2.0.70 nebo novější). Další informace o instalaci azure cli najdete [v tématu instalace příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Chcete-li vytvořit nový účet úložiště, zavolejte[az úložiště vytvořit](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)a nastavte `--enable-files-aadds` vlastnost na **true**. V následujícím příkladu nezapomeňte nahradit zástupné hodnoty vlastními hodnotami. (Pokud jste používali předchozí modul náhledu, parametr pro povolení funkce je **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Chcete-li tuto funkci povolit na existujících účtech úložiště, použijte následující příkaz:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nyní jste úspěšně povolili ověřování Azure AD DS přes SMB a přiřadili vlastní roli, která poskytuje přístup ke sdílené složce Azure s identitou Azure AD. Chcete-li udělit přístup ke sdílené složce dalším uživatelům, postupujte podle pokynů v části [Přiřadit přístupová oprávnění](#assign-access-permissions-to-an-identity) k použití identity a [konfigurace oprávnění ntfs přes oddíly SMB](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Další kroky

Další informace o souborech Azure a o tom, jak používat Azure AD přes SMB, najdete v těchto prostředcích:

- [Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB](storage-files-active-directory-overview.md)
- [Nejčastější dotazy](storage-files-faq.md)
