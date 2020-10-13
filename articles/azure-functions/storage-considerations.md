---
title: Požadavky na úložiště pro Azure Functions
description: Seznamte se s požadavky na úložiště Azure Functions a o šifrování uložených dat.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: aefd9a35235a09d94973f383603349f6862bbdd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318177"
---
# <a name="storage-considerations-for-azure-functions"></a>Požadavky na úložiště pro Azure Functions

Azure Functions vyžaduje účet Azure Storage při vytváření instance aplikace Function App. Vaše aplikace Function App může používat následující služby úložiště:


|Služba úložiště  | Využití funkcí  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Zachovat stav vazeb a klíče funkcí  <br/>Používá se také pro [centra úloh v Durable Functions](durable/durable-functions-task-hubs.md). |
| [Soubory Azure](../storage/files/storage-files-introduction.md)  | Sdílená složka používaná k ukládání a spouštění kódu aplikace Function App v [plánu spotřeby](functions-scale.md#consumption-plan) a [plánu Premium](functions-scale.md#premium-plan). |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | Používá [se pro centra úloh v Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  Používá [se pro centra úloh v Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Při použití plánu hostování v rámci spotřeby/Premium se kód funkce a konfigurační soubory vazby ukládají do služby Azure File Storage v hlavním účtu úložiště. Když odstraníte hlavní účet úložiště, tento obsah se odstraní a není možné ho obnovit.

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

Při vytváření aplikace Function App musíte vytvořit nebo propojit s Azure Storage účet pro obecné účely, který podporuje úložiště objektů blob, front a tabulek. Důvodem je to, že funkce spoléhají na Azure Storage pro operace, jako je Správa triggerů a spouštění funkcí protokolování. Některé účty úložiště nepodporují fronty a tabulky. Tyto účty zahrnují účty úložiště jen pro objekty blob, Azure Premium Storage a účty úložiště pro obecné účely s replikací ZRS. Tyto nepodporované účty se při vytváření aplikace Function App odfiltrují z okna účtu úložiště.

Další informace o typech účtů úložiště najdete v tématu [Seznámení se službami Azure Storage](../storage/common/storage-introduction.md#core-storage-services). 

I když pomocí aplikace Function App můžete použít existující účet úložiště, musíte se ujistit, že splňuje tyto požadavky. Účty úložiště vytvořené jako součást služby Function App vytvořit tok jsou zaručené splňovat tyto požadavky na účet úložiště.  

## <a name="storage-account-guidance"></a>Doprovodné materiály k účtu úložiště

Každá aplikace Function App vyžaduje, aby účet úložiště fungoval. Pokud se tento účet odstraní, aplikace Function App se nespustí. Pokud chcete řešit problémy související s úložištěm, přečtěte si téma řešení [potíží souvisejících s úložištěm](functions-recover-storage-account.md). Následující další požadavky platí pro účet úložiště používaný aplikacemi Function App.

### <a name="storage-account-connection-setting"></a>Nastavení připojení účtu úložiště

Připojení k účtu úložiště se udržuje v [nastavení aplikace AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Připojovací řetězec účtu úložiště se musí aktualizovat při opětovném generování klíčů úložiště. [Přečtěte si další informace o správě klíčů úložiště tady](../storage/common/storage-account-create.md).

### <a name="shared-storage-accounts"></a>Sdílené účty úložiště

Je možné, že více aplikací Function App sdílí stejný účet úložiště bez problémů. Například v aplikaci Visual Studio můžete vyvíjet více aplikací pomocí emulátoru Azure Storage. V takovém případě emulátor funguje jako jeden účet úložiště. K ukládání dat aplikací se dá použít taky stejný účet úložiště, který používá vaše aplikace Function App. Tento přístup ale není vždycky dobrý nápad v produkčním prostředí.

### <a name="optimize-storage-performance"></a>Optimalizace výkonu úložiště

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Šifrování dat úložiště

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="mount-file-shares-linux"></a>Připojit sdílené soubory (Linux)

Existující sdílené složky Azure můžete připojit k aplikacím funkcí pro Linux. Připojením sdílené složky k aplikaci Functions pro Linux můžete využít stávající modely strojového učení nebo jiná data ve vašich funkcích. Pomocí [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) příkazu můžete připojit existující sdílenou složku k aplikaci Functions pro Linux. 

V tomto příkazu `share-name` je název existující sdílené složky Azure Files a `custom-id` může to být libovolný řetězec, který jedinečně definuje sdílenou složku, když je připojená k aplikaci Function App. Také `mount-path` je cesta, ze které je sdílená složka k dispozici ve vaší aplikaci Function App. `mount-path` musí být ve formátu `/dir-name` a nemůže začínat na `/home` .

Úplný příklad najdete ve skriptech v tématu [Vytvoření aplikace funkcí Pythonu a připojení sdílené složky služby soubory Azure](scripts/functions-cli-mount-files-storage-linux.md). 

V současné době `storage-type` je podporována pouze část z `AzureFiles` . Do dané aplikace Function App můžete připojit jenom pět sdílených složek. Připojení sdílené složky může prodloužit čas spuštění alespoň 200-300ms nebo ještě více, pokud je účet úložiště v jiné oblasti.

Připojená sdílená složka je k dispozici pro váš kód funkce na `mount-path` zadaném. Například když `mount-path` je `/path/to/mount` , můžete získat přístup k cílovému adresáři pomocí rozhraní API systému souborů, jak je uvedeno v následujícím příkladu Pythonu:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o možnostech hostování Azure Functions.

> [!div class="nextstepaction"]
> [Hostování a škálování Azure Functions](functions-scale.md)
