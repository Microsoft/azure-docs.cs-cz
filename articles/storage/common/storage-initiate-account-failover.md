---
title: Zahájit úložiště účtu převzetí služeb při selhání (preview) – služby Azure Storage
description: Informace o zahájení účtu převzetí služeb při selhání v případě, že primární koncový bod vašeho účtu úložiště k dispozici. Aktualizuje převzetí služeb při selhání sekundární oblasti stane primární oblast pro účet úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.component: common
ms.openlocfilehash: d1fb921f72af58eacde5f1ef35ee3aec80f767c8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55994673"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Zahájení převzetí služeb účtu storage (preview)

Pokud z nějakého důvodu nedostupný primární koncový bod pro váš účet geograficky redundantní úložiště, můžete spustit účtu převzetí služeb při selhání (preview). Účtu převzetí služeb při selhání aktualizace sekundárního koncového bodu se stát primární koncový bod vašeho účtu úložiště. Po dokončení převzetí služeb klientům můžete začít psát na novou primární oblasti. Vynucené převzetí služeb při selhání umožňuje udržet vysokou dostupnost pro vaše aplikace.

Tento článek ukazuje, jak inicializovat účtu převzetí služeb při selhání u vašeho účtu úložiště pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure. Další informace o účtu převzetí služeb při selhání najdete v tématu [po havárii pro obnovení a účet převzetí služeb při selhání (preview) ve službě Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Převzetí služeb při selhání účet obvykle způsobí ztrátu některých dat. Při nepochopení účtu převzetí služeb při selhání a připravit na ztrátu dat, zkontrolujte [procesu převzetí služeb při selhání účet](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

## <a name="prerequisites"></a>Požadavky

Na vašem účtu úložiště, abyste mohli provádět účtu převzetí služeb při selhání, ujistěte se, že jste provedli následující kroky:

- Zaregistrovat verzi preview účet převzetí služeb při selhání. Informace o tom, jak zaregistrovat, najdete v části [o verzi preview](storage-disaster-recovery-guidance.md#about-the-preview).
- Ujistěte se, že je nakonfigurovaný účet úložiště pomocí geograficky redundantního úložiště (GRS) nebo geograficky redundantní úložiště jen pro čtení (RA-GRS). Další informace o geograficky redundantním úložišti najdete v tématu [geograficky redundantní úložiště (GRS): Replikace mezi zónami pro službu Azure Storage](storage-redundancy-grs.md). 

## <a name="important-implications-of-account-failover"></a>Důležité důsledcích účet převzetí služeb při selhání

Při zahájení převzetí služeb při selhání účtu pro vašeho účtu úložiště, se aktualizují záznamy DNS pro sekundární koncový bod tak, aby sekundární koncový bod primární koncový bod. Ujistěte se, že pochopit potenciální dopad do vašeho účtu úložiště, před spuštěním převzetí služeb.

K odhadu rozsahu pravděpodobnost úniku před spuštěním převzetí služeb, zkontrolujte **čas poslední synchronizace** pomocí vlastnosti `Get-AzureRmStorageAccount` rutiny Powershellu a zahrnout `-IncludeGeoReplicationStats` parametr. Zkontrolujte `GeoReplicationStats` vlastnost pro váš účet. 

Po převzetí služeb při selhání typ účtu úložiště automaticky převést na místně redundantní úložiště (LRS) v nové primární oblasti. Geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště jen pro čtení (RA-GRS) pro účet můžete znovu povolit. Všimněte si, že převod z LRS na GRS nebo RA-GRS s sebou nese náklady další poplatky. Další informace najdete v tématu [podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/). 

Po GRS znovu povolili pro váš účet úložiště, Microsoft zahájí replikaci dat ve vašem účtu na nový sekundární oblasti. Replikace doba je závislá na množství dat se replikuje.  

## <a name="azure-portal"></a>portál Azure

K zahájení účtu převzetí služeb při selhání z portálu Azure portal, postupujte takto:

1. Přejděte na svůj účet úložiště.
2. V části **nastavení**vyberte **geografickou replikaci**. Následující obrázek ukazuje stav geografickou replikaci a převzetí služeb při selhání u účtu úložiště.

    ![Snímek obrazovky ukazující stav geografickou replikaci a převzetí služeb při selhání](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Ověřte, že je nakonfigurovaný účet úložiště pro geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště jen pro čtení (RA-GRS). Pokud není, vyberte **konfigurace** pod **nastavení** a aktualizujete účet geograficky redundantní. 
4. **Čas poslední synchronizace** vlastnost určuje, jak daleko sekundární je za z primárního serveru. **Poslední čas synchronizace** poskytuje odhad rozsahu ztráty dat, ke které dojde po dokončení převzetí služeb při selhání.
5. Vyberte **připravit pro převzetí služeb při selhání (preview)**. 
6. Zkontrolujte dialogové okno pro potvrzení. Až budete připravení, zadejte **Ano** potvrzení a zahájit převzetí služeb při selhání.

    ![Snímek obrazovky znázorňující potvrzovací dialogové okno účtem převzetí služeb při selhání](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

Prostředí PowerShell použít k zahájení účtu převzetí služeb při selhání, je třeba nejprve nainstalovat 6.0.1 modul ve verzi preview. Postupujte podle těchto kroků nainstalujte modul:

1. Odinstalujte všechny předchozí instalace Azure Powershellu:

    - Odebrat všechny předchozí instalace Azure Powershellu z Windows pomocí **aplikace a funkce** v nabídce **nastavení**.
    - Odeberte všechny **Azure*** moduly z `%Program Files%\WindowsPowerShell\Modules`.
    
1. Ujistěte se, že máte nejnovější verzi modulu PowerShellGet nainstalovaný. Otevřete okno Windows Powershellu a spusťte následující příkaz k instalaci nejnovější verze:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Zavřete a znovu otevřete okno Powershellu po instalaci Správce balíčků PowerShellGet. 

1. Nainstalujte nejnovější verzi Azure Powershellu:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Nainstalujte modul ve verzi preview služby Azure Storage, který podporuje Azure AD:
   
    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```
1. Zavřete a znovu otevřete okno Powershellu.
 

K zahájení účtu převzetí služeb při selhání z prostředí PowerShell, spusťte následující příkaz:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zahájit účtu převzetí služeb při selhání pomocí Azure CLI, spusťte následující příkazy:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>Další postup

- [Po havárii pro obnovení a účet převzetí služeb při selhání (preview) ve službě Azure Storage](storage-disaster-recovery-guidance.md)
- [Návrh aplikací s vysokou dostupností pomocí RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Kurz: Sestavení aplikace s vysokou dostupností s úložištěm objektů Blob](../blobs/storage-create-geo-redundant-storage.md) 
