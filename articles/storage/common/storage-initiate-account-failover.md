---
title: Iniciovat převzetí služeb při selhání účtu úložiště (Preview) – Azure Storage
description: Přečtěte si, jak iniciovat převzetí služeb při selhání v případě, že primární koncový bod vašeho účtu úložiště nebude k dispozici. Převzetí služeb při selhání aktualizuje sekundární oblast jako primární oblast pro váš účet úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0c619224201d6225d5e5c127b342f71f2f7fced9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79535348"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Iniciovat převzetí služeb při selhání účtu úložiště (Preview)

Pokud z nějakého důvodu nebude primární koncový bod účtu geograficky redundantního úložiště dostupný, můžete iniciovat převzetí služeb při selhání (Preview) účtu. Převzetí služeb při selhání účtu aktualizuje sekundární koncový bod tak, aby se stal primárním koncovým bodem pro váš účet úložiště. Až se převzetí služeb při selhání dokončí, můžou klienti začít zapisovat do nové primární oblasti. Vynucené převzetí služeb při selhání umožňuje udržovat vysokou dostupnost aplikací.

Tento článek ukazuje, jak iniciovat převzetí služeb při selhání účtu úložiště pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI. Další informace o převzetí služeb při selhání účtů najdete [v tématu zotavení po havárii a převzetí služeb při selhání účtů (Preview) v Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Převzetí služeb při selhání účtu obvykle vede ke ztrátě dat. Pokud chcete pochopit důsledky převzetí služeb při selhání účtu a připravit se na ztrátu dat, přečtěte si [Vysvětlení procesu převzetí služeb při selhání účtu](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Před převzetím služeb při selhání v účtu úložiště ověřte, že jste provedli následující krok:

- Ujistěte se, že je váš účet úložiště nakonfigurovaný tak, aby používal buď geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). Další informace o geograficky redundantním úložišti najdete v tématu [Azure Storage redundance](storage-redundancy.md).

## <a name="important-implications-of-account-failover"></a>Důležité důsledky převzetí služeb při selhání účtu

Když zahájíte převzetí služeb při selhání účtu úložiště, aktualizují se záznamy DNS pro sekundární koncový bod tak, aby se sekundární koncový bod stal primárním koncovým bodem. Než zahájíte převzetí služeb při selhání, ujistěte se, že rozumíte možnému dopadu účtu úložiště.

Pokud chcete odhadnout rozsah pravděpodobných ztrát dat před tím, než zahájíte převzetí služeb při selhání, podívejte se na vlastnost **čas poslední synchronizace** pomocí rutiny `Get-AzStorageAccount` prostředí PowerShell a zadejte `-IncludeGeoReplicationStats` parametr. Pak zkontrolujte `GeoReplicationStats` vlastnost svého účtu. \

Po převzetí služeb při selhání se Váš typ účtu úložiště automaticky převede na místně redundantní úložiště (LRS) v nové primární oblasti. Pro tento účet můžete znovu povolit geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). Všimněte si, že při převodu z LRS na GRS nebo RA-GRS se vyskytnou další náklady. Další informace najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Po opětovném povolení GRS pro váš účet úložiště začne Microsoft replikovat data ve vašem účtu do nové sekundární oblasti. Doba replikace závisí na množství replikovaných dat.  

## <a name="portal"></a>[Portál](#tab/azure-portal)

Pokud chcete iniciovat převzetí služeb při selhání účtu z Azure Portal, postupujte takto:

1. Přejděte na svůj účet úložiště.
2. V části **Nastavení**vyberte **geografickou replikaci**. Následující obrázek ukazuje stav geografické replikace a převzetí služeb při selhání v účtu úložiště.

    ![Snímek obrazovky znázorňující geografickou replikaci a stav převzetí služeb při selhání](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Ověřte, že je váš účet úložiště nakonfigurovaný pro geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). Pokud není, vyberte v části **Nastavení** možnost **Konfigurace** a aktualizujte svůj účet na geograficky redundantní. 
4. Vlastnost **čas poslední synchronizace** určuje, jak daleko je sekundární sekundární z primární třídy. **Čas poslední synchronizace** poskytuje odhad rozsahu ztráty dat, ke které dojde po dokončení převzetí služeb při selhání.
5. Vyberte **Příprava na převzetí služeb při selhání (Preview)**. 
6. Přečtěte si potvrzovací dialog. Až budete připraveni, potvrďte a zahajte převzetí služeb při selhání zadáním **Ano** .

    ![Snímek obrazovky s potvrzovacím dialogem pro převzetí služeb při selhání účtu](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[Prostředí](#tab/azure-powershell)

Pokud chcete pomocí PowerShellu iniciovat převzetí služeb při selhání, musíte nejdřív nainstalovat modul 6.0.1 Preview. Pomocí těchto kroků nainstalujte modul:

1. Odinstalujte všechny předchozí instalace Azure PowerShell:

    - Odeberte všechny předchozí instalace Azure PowerShell z Windows pomocí nastavení **funkce & aplikace** v části **Nastavení**.
    - Odeberte všechny moduly **Azure** z `%Program Files%\WindowsPowerShell\Modules`.

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

1. Zavřete a znovu otevřete okno PowerShellu.
 
Pokud chcete iniciovat převzetí služeb při selhání z PowerShellu, spusťte následující příkaz:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete pomocí Azure CLI iniciovat převzetí služeb při selhání, spusťte následující příkazy:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Další kroky

- [Zotavení po havárii a převzetí služeb při selhání účtu (Preview) v Azure Storage](storage-disaster-recovery-guidance.md)
- [Návrh aplikací s vysokou dostupností pomocí RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Kurz: vytvoření vysoce dostupné aplikace s úložištěm BLOB](../blobs/storage-create-geo-redundant-storage.md) 
