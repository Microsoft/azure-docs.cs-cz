---
title: Zahájení převzetí služeb při selhání účtu úložiště (preview) – Azure Storage
description: Přečtěte si, jak spustit převzetí služeb při selhání účtu v případě, že primární koncový bod pro váš účet úložiště přestane být k dispozici. Převzetí služeb při selhání aktualizuje sekundární oblast, aby se stala primární oblastí pro váš účet úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0c619224201d6225d5e5c127b342f71f2f7fced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535348"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Zahájení převzetí služeb při selhání účtu úložiště (náhled)

Pokud primární koncový bod pro váš účet geograficky redundantníúložiště přestane být z nějakého důvodu k dispozici, můžete zahájit převzetí služeb při selhání účtu (náhled). Převzetí služeb při selhání účtu aktualizuje sekundární koncový bod, aby se stal primárním koncovým bodem pro váš účet úložiště. Po dokončení převzetí služeb při selhání mohou klienti začít zapisovat do nové primární oblasti. Vynucené převzetí služeb při selhání umožňuje udržovat vysokou dostupnost pro vaše aplikace.

Tento článek ukazuje, jak zahájit převzetí služeb při selhání účtu pro váš účet úložiště pomocí portálu Azure, PowerShellu nebo Azure CLI. Další informace o převzetí služeb při selhání účtu najdete [v tématu Zotavení po havárii a převzetí služeb při selhání účtu (preview) ve službě Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Převzetí služeb při selhání účtu obvykle vede ke ztrátě některých dat. Chcete-li pochopit důsledky převzetí služeb při selhání účtu a připravit se na ztrátu dat, přečtěte [si seznamte se s principy procesu převzetí služeb při selhání účtu](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než budete moci provést převzetí služeb při selhání účtu na vašem účtu úložiště, ujistěte se, že jste provedli následující krok:

- Ujistěte se, že váš účet úložiště je nakonfigurovaný tak, aby používal geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště pro čtení (RA-GRS). Další informace o geograficky redundantním úložišti najdete [v tématu Redundance azure storage](storage-redundancy.md).

## <a name="important-implications-of-account-failover"></a>Důležité důsledky převzetí služeb při selhání účtu

Při zahájení převzetí služeb při selhání účtu pro váš účet úložiště, záznamy DNS pro sekundární koncový bod jsou aktualizovány tak, aby sekundární koncový bod se stane primární koncový bod. Ujistěte se, že jste pochopili potenciální dopad na váš účet úložiště před zahájením převzetí služeb při selhání.

Chcete-li odhadnout rozsah pravděpodobné ztráty dat před zahájením převzetí služeb `Get-AzStorageAccount` při selhání, zkontrolujte `-IncludeGeoReplicationStats` vlastnost Poslední **doba synchronizace** pomocí rutiny prostředí PowerShell a zahrňte parametr. Pak `GeoReplicationStats` zkontrolujte, zda vlastnost pro váš účet. \

Po převzetí služeb při selhání se typ účtu úložiště automaticky převede na místně redundantní úložiště (LRS) v nové primární oblasti. Můžete znovu povolit geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště pro čtení (RA-GRS) pro účet. Všimněte si, že převod z LRS na GRS nebo RA-GRS za následek dodatečné náklady. Další informace naleznete v [tématu Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Po opětovném povolení grs pro váš účet úložiště, Microsoft začne replikovat data ve vašem účtu do nové sekundární oblasti. Doba replikace závisí na množství replikovaných dat.  

## <a name="portal"></a>[Portál](#tab/azure-portal)

Pokud chcete nawebu s microsoftem zahájit převzetí služeb při selhání, postupujte takto:

1. Přejděte na svůj účet úložiště.
2. V části **Nastavení**vyberte **možnost Geografická replikace**. Následující obrázek znázorňuje geografickou replikaci a stav převzetí služeb při selhání účtu úložiště.

    ![Snímek obrazovky se stavem geografické replikace a převzetí služeb při selhání](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Ověřte, zda je váš účet úložiště nakonfigurovaný pro geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště pro čtení (RA-GRS). Pokud tomu tak není, pak v části **Nastavení** vyberte **Konfigurace** a aktualizujte svůj účet tak, aby byl geograficky redundantní. 
4. Vlastnost **Doba poslední synchronizace** označuje, jak daleko je sekundární od primární. **Poslední čas synchronizace** poskytuje odhad rozsahu ztráty dat, ke které dojde po dokončení převzetí služeb při selhání.
5. Vyberte **Připravit na převzetí služeb při selhání (náhled).** 
6. Zkontrolujte potvrzovací dialog. Až budete připraveni, zadejte **Ano** pro potvrzení a zahájení převzetí služeb při selhání.

    ![Snímek obrazovky s potvrzovacím dialogem pro převzetí služeb při selhání účtu](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li použít prostředí PowerShell k zahájení převzetí služeb při selhání účtu, musíte nejprve nainstalovat modul preview 6.0.1. Chcete-li modul nainstalovat, postupujte takto:

1. Odinstalujte všechny předchozí instalace Azure PowerShellu:

    - Odebrání všech předchozích instalací Prostředí Azure PowerShell z Windows pomocí nastavení **funkce aplikace &** v části **Nastavení**.
    - Odeberte všechny `%Program Files%\WindowsPowerShell\Modules`moduly **Azure** z aplikace .

1. Ujistěte se, že máte nainstalovanou nejnovější verzi PowerShellGet. Otevřete okno prostředí Windows PowerShell a pro instalaci nejnovější verze nainstalujte následující příkaz:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zavřete a znovu otevřete okno PowerShellu po instalaci PowerShellGet. 

1. Nainstalujte nejnovější verzi Azure PowerShellu:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Nainstalujte modul Preview služby Azure Storage, který podporuje převzetí služeb při selhání účtu:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Zavřete a znovu otevřete okno PowerShellu.
 
Chcete-li zahájit převzetí služeb při selhání účtu z prostředí PowerShell, proveďte následující příkaz:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li použít azure cli k zahájení převzetí služeb při selhání účtu, spusťte následující příkazy:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Další kroky

- [Zotavení po havárii a převzetí služeb při selhání účtu (preview) ve službě Azure Storage](storage-disaster-recovery-guidance.md)
- [Návrh aplikací s vysokou dostupností pomocí RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Kurz: Vytvoření vysoce dostupné aplikace s úložištěm objektů blob](../blobs/storage-create-geo-redundant-storage.md) 
