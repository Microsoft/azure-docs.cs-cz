---
title: Povolit vícekanálový protokol SMB
description: Přečtěte si, jak povolit službu SMB vícekanálový ve sdílených složkách Azure Premium.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732530"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Povolení funkce SMB vícekanálový na účtu úložiště účtů (Preview) 

Účty úložiště Azure podporují službu SMB vícekanálový (Preview), která zvyšuje výkon od klienta SMB 3. x tím, že vytváří více síťových připojení ke sdíleným složkám souborů Premium. Tento článek poskytuje podrobné pokyny, jak povolit vícekanálový protokol SMB v existujícím účtu úložiště. Podrobné informace o službě Azure Files SMB vícekanálový najdete v tématu výkon ve vícekanálovém protokolu SMB.

## <a name="limitations"></a>Omezení

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet úložiště](./storage-how-to-create-file-share.md).
- Pokud máte v úmyslu použít modul Azure PowerShell, [nainstalujte si verzi 3.0.1-Preview modulu](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Začínáme

Otevřete okno PowerShellu a přihlaste se k předplatnému Azure. Odtud se můžete zaregistrovat pro protokol SMB vícekanálový ve verzi Preview pomocí následujících příkazů.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> Registrace může trvat až hodinu.

### <a name="verify-that-feature-registration-is-complete"></a>Ověřte, že se dokončila registrace funkcí.

Vzhledem k tomu, že může trvat až hodinu, než se funkce v účtu úložiště povolí, možná budete chtít použít následující příkaz, který ověří, jestli je pro vaše předplatné zaregistrovaný:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Povolení funkce SMB Multichannel 
Jakmile vytvoříte účet úložiště, můžete postupovat podle pokynů k aktualizaci nastavení protokolu SMB vícekanálový pro váš účet úložiště.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Přihlaste se k Azure Portal a přejděte k účtu úložiště úložiště, ve kterém chcete konfigurovat vícekanálový protokol SMB.
1. V části **Souborová služba** vyberte **sdílené složky** a pak vyberte **nastavení sdílení souborů**.
1. Přepněte položku **SMB** na **zapnutou** (nebo **vypnutou** ) a vyberte **Uložit**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Snímek obrazovky s účtem úložiště, na kterém je zapnutá funkce SMB vícekanálový.":::

Pokud možnost vícekanálový protokolem SMB není v **Nastavení sdílené složky** viditelná nebo při aktualizaci konfigurace se nezdařilo aktualizovat chybu nastavení, ujistěte se prosím, že je vaše předplatné zaregistrované a že váš účet je v některé z [podporovaných oblastí](#regional-availability) s podporovaným typem účtu a replikací.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete povolit vícekanálový protokol SMB pomocí modulu Azure PowerShell, musíte [nainstalovat verzi modulu 3.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) .

`$resourceGroupName` `$storageAccountName` Před spuštěním těchto příkazů PowerShellu nastavte proměnné a skupinu prostředků a účet úložiště.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Rozhraní příkazového řádku Azure ještě nepodporuje konfiguraci vícekanálového protokolu SMB. V pokynech na portálu můžete nakonfigurovat protokol SMB vícekanálový v účtu úložiště.

---

> [!NOTE]
> Všechny změny nastavení konfigurace vícekanálového protokolu SMB budou platit pro všechny sdílené složky v účtu úložiště. Aby se změny projevily, budete muset znovu připojit sdílenou složku v klientovi.


## <a name="next-steps"></a>Další kroky 

- [Opětovně připojte sdílenou složku](storage-how-to-use-files-windows.md) , abyste mohli využívat výhod protokolu SMB vícekanálový.
- [Vyřešte všechny problémy, které souvisí s protokolem SMB vícekanálový](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Další informace o vylepšeních najdete v tématu Co je to [vícekanálový výkon protokolu SMB](storage-files-smb-multichannel-performance.md) .
 - Další informace o funkci Windows SMB vícekanálový najdete v tématu [Správa SMB karty](/azure-stack/hci/manage/manage-smb-multichannel).