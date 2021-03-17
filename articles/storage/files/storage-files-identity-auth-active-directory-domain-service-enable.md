---
title: Použití Azure AD Domain Services k autorizaci přístupu k souborovým datům přes protokol SMB
description: Naučte se povolit ověřování na základě identity přes protokol SMB (Server Message Block) pro soubory Azure prostřednictvím Azure Active Directory Domain Services. Virtuální počítače s Windows připojené k doméně potom můžou přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů Azure AD.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 3abca397186572cabb4f7ae99edae8688ea4d9a6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499505"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Povolení ověřování Azure Active Directory Domain Services u souborů Azure

[Soubory Azure](storage-files-introduction.md)   podporuje ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím dvou typů doménových služeb: místní Active Directory Domain Services (služba AD DS) a Azure Active Directory Domain Services (Azure služba AD DS). Důrazně doporučujeme, abyste si v [části jak to funguje](./storage-files-active-directory-overview.md#how-it-works) , abyste si vybrali správnou doménovou službu pro ověřování. Nastavení se liší v závislosti na zvolené doménové službě. Tento článek se zaměřuje na povolení a konfiguraci služby Azure služba AD DS pro ověřování pomocí sdílených složek Azure.

Pokud se sdílenými složkami Azure teprve začínáte, doporučujeme si přečíst si náš [Průvodce plánováním](storage-files-planning.md) , abyste si přečetli následující řadu článků.

> [!NOTE]
> Soubory Azure podporují ověřování pomocí protokolu Kerberos s Azure služba AD DS jenom pomocí RC4-HMAC. Šifrování AES Kerberos ještě není podporované.
> Služba soubory Azure podporuje ověřování pro Azure služba AD DS s plnou synchronizací s Azure AD. Pokud jste povolili synchronizaci s vymezeným oborem v Azure služba AD DS, že se jenom synchronizuje omezená sada identit z Azure AD, ověřování a autorizace se nepodporuje.

## <a name="prerequisites"></a>Požadavky

Než povolíte Azure AD přes SMB pro sdílené složky Azure, ujistěte se, že jste dokončili následující požadavky:

1.  **Vyberte nebo vytvořte tenanta Azure AD.**

    Pro ověřování Azure AD prostřednictvím protokolu SMB můžete použít nového nebo stávajícího tenanta. Tenant a sdílená složka, ke kterým chcete získat přístup, musí být přidruženy ke stejnému předplatnému.

    Pokud chcete vytvořit nového tenanta Azure AD, můžete [Přidat tenanta Azure AD a předplatné služby Azure AD](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Pokud máte existujícího tenanta Azure AD, ale chcete vytvořit nového tenanta pro použití se sdílenými složkami Azure, přečtěte si téma [Vytvoření klienta Azure Active Directory](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Povolí Azure AD Domain Services v tenantovi Azure AD.**

    Aby bylo možné podporovat ověřování pomocí přihlašovacích údajů Azure AD, musíte Azure AD Domain Services pro vašeho tenanta Azure AD povolit. Pokud nejste správcem tenanta Azure AD, obraťte se na správce a postupujte podle podrobných pokynů, které vám [umožní Azure Active Directory Domain Services používání Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Dokončení nasazení Azure služba AD DS obvykle trvá přibližně 15 minut. Než přejdete k dalšímu kroku, ověřte, že se stav služby Azure služba AD DS zobrazuje jako **spuštěný** a povolená synchronizace hodnot hash hesel.

1.  **Doména: připojení virtuálního počítače Azure s Azure služba AD DS.**

    Pro přístup ke sdílené složce pomocí přihlašovacích údajů Azure AD z virtuálního počítače musí být váš virtuální počítač připojený k doméně Azure služba AD DS. Další informace o tom, jak připojit virtuální počítač k doméně, najdete v tématu [připojení virtuálního počítače s Windows serverem ke spravované doméně](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Ověřování pomocí Azure služba AD DS přes protokol SMB se sdílenými složkami Azure je podporované jenom na virtuálních počítačích Azure, na kterých běží verze operačního systému Windows 7 nebo Windows Server 2008 R2.

1.  **Vyberte nebo vytvořte sdílenou složku Azure.**

    Vyberte novou nebo existující sdílenou složku, která je přidružená ke stejnému předplatnému jako tenant služby Azure AD. Informace o vytvoření nové sdílené složky najdete v tématu [Vytvoření sdílené složky ve službě soubory Azure](storage-how-to-create-file-share.md).
    Pro zajištění optimálního výkonu doporučujeme, aby byla sdílená složka ve stejné oblasti jako virtuální počítač, ze kterého plánujete přístup ke sdílené složce.

1.  **Pomocí klíče účtu úložiště ověřte připojení ke službě soubory Azure pomocí připojení sdílených složek Azure.**

    Pokud chcete ověřit, jestli je virtuální počítač a sdílená složka správně nakonfigurované, zkuste připojit sdílenou složku pomocí klíče účtu úložiště. Další informace najdete v tématu [připojení sdílené složky Azure a přístup ke sdílené složce v systému Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regionální dostupnost

Ověřování souborů Azure pomocí Azure služba AD DS je k dispozici ve [všech oblastech Azure Public, gov a Čína](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview-of-the-workflow"></a>Přehled pracovního postupu

Než povolíte Azure služba AD DS Authentication přes SMB pro sdílené složky Azure, ověřte, že jsou správně nakonfigurovaná vaše prostředí Azure AD a Azure Storage. Doporučujeme projít si [požadavky](#prerequisites) a ujistit se, že jste dokončili všechny požadované kroky.

V dalším kroku udělte přístup k prostředkům Azure Files pomocí přihlašovacích údajů Azure AD následující věci:

1. Povolte Azure služba AD DS ověřování přes SMB pro váš účet úložiště pro registraci účtu úložiště s přidruženým nasazením Azure služba AD DS.
2. Přiřazení oprávnění k přístupu ke sdílené složce identitě služby Azure AD (uživatel, skupina nebo instanční objekt).
3. Konfigurace oprávnění NTFS pro adresáře a soubory protokolu SMB.
4. Připojte sdílenou složku Azure z virtuálního počítače připojeného k doméně.

Následující diagram znázorňuje kompletní pracovní postup pro povolení ověřování Azure služba AD DS přes protokol SMB pro soubory Azure.

![Diagram znázorňující pracovní postup Azure AD pomocí protokolu SMB pro Azure Files](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Povolit Azure služba AD DS ověřování pro váš účet

Pokud chcete povolit službu Azure služba AD DS Authentication přes protokol SMB pro soubory Azure, můžete nastavit vlastnost na účtech úložiště pomocí rozhraní příkazového řádku Azure Portal, Azure PowerShell nebo Azure CLI. Nastavení této vlastnosti implicitně připojí k doméně účet úložiště s přidruženým nasazením služby Azure služba AD DS. Pro všechny nové a existující sdílené složky v účtu úložiště se pak povolí ověřování Azure služba AD DS přes protokol SMB.

Mějte na paměti, že můžete povolit ověřování Azure služba AD DS přes protokol SMB až po úspěšném nasazení služba AD DS Azure do tenanta Azure AD. Další informace najdete v části [požadavky](#prerequisites).

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete povolit ověřování pomocí služby Azure služba AD DS přes protokol SMB s [Azure Portal](https://portal.azure.com), postupujte podle následujících kroků:

1. V Azure Portal navštivte svůj existující účet úložiště nebo [vytvořte účet úložiště](../common/storage-account-create.md).
1. V části **Nastavení** vyberte **Konfigurace**.
1. V části **přístup založený na identitě pro sdílené složky** přepněte přepínač pro **službu Azure Active Directory Domain Service (AAD DS)** na **povolenou**.
1. Vyberte **Uložit**.

Následující obrázek ukazuje, jak povolit Azure služba AD DS ověřování pomocí protokolu SMB pro váš účet úložiště.

![Povolit ověřování Azure služba AD DS přes SMB v Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete povolit ověřování Azure služba AD DS přes protokol SMB s Azure PowerShell, nainstalujte nejnovější modul AZ Module (2,4 nebo novější) nebo modul AZ. Storage (1,5 nebo novější). Další informace o instalaci PowerShellu najdete v tématu [instalace Azure PowerShell ve Windows pomocí PowerShellGet](/powershell/azure/install-Az-ps).

Pokud chcete vytvořit nový účet úložiště, zavolejte [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)a pak nastavte parametr **EnableAzureActiveDirectoryDomainServicesForFile** na **true**. V následujícím příkladu Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami. (Pokud jste používali předchozí modul Preview, parametr pro povolení funkce je **EnableAzureFilesAadIntegrationForSMB**.)

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


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete povolit ověřování Azure AD přes protokol SMB pomocí Azure CLI, nainstalujte nejnovější verzi rozhraní příkazového řádku (verze 2.0.70 nebo novější). Další informace o instalaci rozhraní příkazového řádku Azure najdete v tématu [instalace Azure CLI](/cli/azure/install-azure-cli).

Chcete-li vytvořit nový účet úložiště, zavolejte příkaz [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create)a nastavte `--enable-files-aadds` vlastnost na **hodnotu true**. V následujícím příkladu Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami. (Pokud jste používali předchozí modul Preview, je parametr pro povolení funkce **File-AAD**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Pokud chcete tuto funkci povolit u stávajících účtů úložiště, použijte následující příkaz:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nyní jste úspěšně povolili ověřování Azure služba AD DS přes protokol SMB a přiřadili jste vlastní roli, která poskytuje přístup ke sdílené složce Azure pomocí identity Azure AD. Pokud chcete ostatním uživatelům udělit přístup ke sdílené složce, postupujte podle pokynů v tématu [Přiřazení přístupových oprávnění](#assign-access-permissions-to-an-identity) k používání identity a [Konfigurace oprávnění NTFS v oddílech SMB](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Další kroky

Další informace o službě soubory Azure a o tom, jak používat Azure AD přes SMB, najdete v těchto zdrojích:

- [Přehled podpory přístupu prostřednictvím protokolu SMB v rámci ověřování na základě identity služby Azure Files](storage-files-active-directory-overview.md)
- [Nejčastější dotazy](storage-files-faq.md)
