---
title: Iniciovat převzetí služeb při selhání účtu úložiště
titleSuffix: Azure Storage
description: Přečtěte si, jak iniciovat převzetí služeb při selhání v případě, že primární koncový bod vašeho účtu úložiště nebude k dispozici. Převzetí služeb při selhání aktualizuje sekundární oblast jako primární oblast pro váš účet úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e39548a923e76fc118dec4158398d02577ec20c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91610054"
---
# <a name="initiate-a-storage-account-failover"></a>Iniciovat převzetí služeb při selhání účtu úložiště

Pokud z nějakého důvodu nebude primární koncový bod účtu geograficky redundantního úložiště dostupný, můžete iniciovat převzetí služeb při selhání účtu. Převzetí služeb při selhání účtu aktualizuje sekundární koncový bod tak, aby se stal primárním koncovým bodem pro váš účet úložiště. Až se převzetí služeb při selhání dokončí, můžou klienti začít zapisovat do nové primární oblasti. Vynucené převzetí služeb při selhání umožňuje udržovat vysokou dostupnost aplikací.

Tento článek ukazuje, jak iniciovat převzetí služeb při selhání účtu úložiště pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI. Další informace o převzetí služeb při selhání najdete v tématu [obnovení po havárii a převzetí služeb při selhání účtu úložiště](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Převzetí služeb při selhání účtu obvykle vede ke ztrátě dat. Pokud chcete pochopit důsledky převzetí služeb při selhání účtu a připravit se na ztrátu dat, přečtěte si [Vysvětlení procesu převzetí služeb při selhání účtu](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než budete moct v účtu úložiště provést převzetí služeb při selhání, ujistěte se, že je váš účet úložiště nakonfigurovaný pro geografickou replikaci. Váš účet úložiště může používat kteroukoli z následujících možností redundance:

- Geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)
- Geograficky redundantní úložiště (GZRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS)

Další informace o Azure Storage redundanci najdete v tématu [Azure Storage redundance](storage-redundancy.md).

## <a name="initiate-the-failover"></a>Iniciovat převzetí služeb při selhání

## <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete iniciovat převzetí služeb při selhání účtu z Azure Portal, postupujte takto:

1. Přejděte na svůj účet úložiště.
1. V části **Nastavení**vyberte **geografickou replikaci**. Následující obrázek ukazuje stav geografické replikace a převzetí služeb při selhání v účtu úložiště.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="Snímek obrazovky znázorňující geografickou replikaci a stav převzetí služeb při selhání":::

1. Ověřte, že je váš účet úložiště nakonfigurovaný pro geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). Pokud není, vyberte v části **Nastavení** možnost **Konfigurace** a aktualizujte svůj účet na geograficky redundantní.
1. Vlastnost **čas poslední synchronizace** určuje, jak daleko je sekundární sekundární z primární třídy. **Čas poslední synchronizace** poskytuje odhad rozsahu ztráty dat, ke které dojde po dokončení převzetí služeb při selhání. Další informace o kontrole vlastnosti **Poslední čas synchronizace** najdete v tématu [Kontrola vlastnosti času poslední synchronizace pro účet úložiště](last-sync-time-get.md).
1. Vyberte **Příprava pro převzetí služeb při selhání**.
1. Přečtěte si potvrzovací dialog. Až budete připraveni, potvrďte a zahajte převzetí služeb při selhání zadáním **Ano** .

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="Snímek obrazovky znázorňující geografickou replikaci a stav převzetí služeb při selhání":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Funkce převzetí služeb při selhání účtu je všeobecně dostupná, ale pořád se spoléhá na modul verze Preview pro PowerShell. Pokud chcete k zahájení převzetí služeb při selhání účtu použít PowerShell, musíte nejdřív nainstalovat modul AZ. Storage [1.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) . Pomocí těchto kroků nainstalujte modul:

1. Odinstalujte všechny předchozí instalace Azure PowerShell:

    - Odeberte všechny předchozí instalace Azure PowerShell z Windows pomocí nastavení **funkce & aplikace** v části **Nastavení**.
    - Odeberte všechny moduly **Azure** z `%Program Files%\WindowsPowerShell\Modules` .

1. Ujistěte se, že máte nainstalovanou nejnovější verzi PowerShellGet. Otevřete okno prostředí Windows PowerShell a spuštěním následujícího příkazu nainstalujte nejnovější verzi:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Po instalaci PowerShellGet zavřete a znovu otevřete okno PowerShellu.

1. Nainstalujte nejnovější verzi Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Nainstalujte modul Azure Storage Preview, který podporuje převzetí služeb při selhání účtu:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Pokud chcete iniciovat převzetí služeb při selhání z PowerShellu, spusťte následující příkaz:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete pomocí Azure CLI iniciovat převzetí služeb při selhání, spusťte následující příkazy:

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Důležité důsledky převzetí služeb při selhání účtu

Když zahájíte převzetí služeb při selhání účtu úložiště, aktualizují se záznamy DNS pro sekundární koncový bod tak, aby se sekundární koncový bod stal primárním koncovým bodem. Než zahájíte převzetí služeb při selhání, ujistěte se, že rozumíte možnému dopadu účtu úložiště.

Pokud chcete odhadnout rozsah pravděpodobných ztrát dat před tím, než zahájíte převzetí služeb při selhání, podívejte se na vlastnost **čas poslední synchronizace** . Další informace o kontrole vlastnosti **Poslední čas synchronizace** najdete v tématu [Kontrola vlastnosti času poslední synchronizace pro účet úložiště](last-sync-time-get.md).

Po převzetí služeb při selhání se Váš typ účtu úložiště automaticky převede na místně redundantní úložiště (LRS) v nové primární oblasti. Pro tento účet můžete znovu povolit geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). Všimněte si, že při převodu z LRS na GRS nebo RA-GRS se vyskytnou další náklady. Další informace najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Po opětovném povolení GRS pro váš účet úložiště začne Microsoft replikovat data ve vašem účtu do nové sekundární oblasti. Doba replikace závisí na množství replikovaných dat.  

## <a name="next-steps"></a>Další kroky

- [Zotavení po havárii a převzetí služeb při selhání účtu úložiště](storage-disaster-recovery-guidance.md)
- [Podívejte se na vlastnost čas poslední synchronizace pro účet úložiště.](last-sync-time-get.md)
- [Použití geografické redundance k návrhu vysoce dostupných aplikací](geo-redundant-design.md)
- [Kurz: vytvoření vysoce dostupné aplikace s úložištěm BLOB](../blobs/storage-create-geo-redundant-storage.md)
