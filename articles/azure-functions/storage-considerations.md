---
title: Požadavky na úložiště pro Azure Functions
description: Seznamte se s požadavky na úložiště Azure Functions a o šifrování uložených dat.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276579"
---
# <a name="storage-considerations-for-azure-functions"></a>Požadavky na úložiště pro Azure Functions

Azure Functions vyžaduje účet Azure Storage při vytváření instance aplikace Function App. Vaše aplikace Function App může používat následující služby úložiště:


|Služba úložiště  | Využití funkcí  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Zachovat stav vazeb a klíče funkcí  <br/>Používá se také pro [centra úloh v Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Sdílená složka používaná k ukládání a spouštění kódu aplikace Function App v [plánu spotřeby](functions-scale.md#consumption-plan). |
| [Úložiště front Azure](../storage/queues/storage-queues-introduction.md)     | Používá [se pro centra úloh v Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  Používá [se pro centra úloh v Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Pokud použijete plán hostování Consumption, kód vaší funkce a vázané konfigurační soubory se ukládají ve službě Azure File Storage v hlavním účtu úložiště. Když odstraníte hlavní účet úložiště, tento obsah se odstraní a není možné ho obnovit.

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

Při vytváření aplikace Function App musíte vytvořit nebo propojit s Azure Storage účet pro obecné účely, který podporuje úložiště objektů blob, front a tabulek. Důvodem je to, že funkce spoléhají na Azure Storage pro operace, jako je Správa triggerů a spouštění funkcí protokolování. Některé účty úložiště nepodporují fronty a tabulky. Tyto účty zahrnují účty úložiště jen pro objekty blob, Azure Premium Storage a účty úložiště pro obecné účely s replikací ZRS. Tyto nepodporované účty se při vytváření aplikace Function App odfiltrují z okna účtu úložiště.

Další informace o typech účtů úložiště najdete v tématu [Seznámení se službami Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

I když pomocí aplikace Function App můžete použít existující účet úložiště, musíte se ujistit, že splňuje tyto požadavky. Účty úložiště vytvořené jako součást služby Function App vytvořit tok jsou zaručené splňovat tyto požadavky na účet úložiště.  

## <a name="storage-account-guidance"></a>Doprovodné materiály k účtu úložiště

Každá aplikace Function App vyžaduje, aby účet úložiště fungoval. Pokud se tento účet odstraní, aplikace Function App se nespustí. Pokud chcete řešit problémy související s úložištěm, přečtěte si téma řešení [potíží souvisejících s úložištěm](functions-recover-storage-account.md). Následující další požadavky platí pro účet úložiště používaný aplikacemi Function App.

### <a name="storage-account-connection-setting"></a>Nastavení připojení účtu úložiště

Připojení k účtu úložiště se udržuje v [nastavení aplikace AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Připojovací řetězec účtu úložiště se musí aktualizovat při opětovném generování klíčů úložiště. [Přečtěte si další informace o správě klíčů úložiště tady](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Sdílené účty úložiště

Je možné, že více aplikací Function App sdílí stejný účet úložiště bez problémů. Například v aplikaci Visual Studio můžete vyvíjet více aplikací pomocí emulátoru Azure Storage. V takovém případě emulátor funguje jako jeden účet úložiště. K ukládání dat aplikací se dá použít taky stejný účet úložiště, který používá vaše aplikace Function App. Tento přístup ale není vždycky dobrý nápad v produkčním prostředí.

### <a name="optimize-storage-performance"></a>Optimalizace výkonu úložiště

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Šifrování dat úložiště

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Další informace najdete v tématu [Azure Storage šifrování pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které se použijí k šifrování dat objektů BLOB a souborů. Aby funkce mohly získat přístup k účtu úložiště, musí se tyto klíče vyskytovat v Azure Key Vault. Další informace najdete v tématu [konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pomocí Azure Portal](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Připojit sdílené soubory (Linux)

Existující sdílené složky Azure můžete připojit k aplikacím funkcí pro Linux. Připojením sdílené složky k aplikaci Functions pro Linux můžete využít stávající modely strojového učení nebo jiná data ve vašich funkcích. Pomocí příkazu [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) můžete připojit existující sdílenou složku k aplikaci Functions pro Linux. 

V tomto příkazu je `share-name` název existující sdílené složky služby soubory Azure a `custom-id` může být libovolný řetězec, který jedinečně definuje sdílenou složku, když je připojená k aplikaci Function App. `mount-path` je také cesta, ze které je sdílená složka dostupná ve vaší aplikaci Function App. `mount-path` musí být ve formátu `/dir-name`a nemůže začínat `/home`.

Úplný příklad najdete ve skriptech v tématu [Vytvoření aplikace funkcí Pythonu a připojení sdílené složky služby soubory Azure](scripts/functions-cli-mount-files-storage-linux.md). 

V současné době je podporována pouze `storage-type` `AzureFiles`. Do dané aplikace Function App můžete připojit jenom pět sdílených složek. Připojení sdílené složky může prodloužit čas spuštění alespoň 200-300ms nebo ještě více, pokud je účet úložiště v jiné oblasti.

Připojená sdílená složka je k dispozici pro váš kód funkce v zadaném `mount-path`. Například pokud je `mount-path` `/path/to/mount`, můžete k cílovému adresáři přistupovat pomocí rozhraní API systému souborů, jako v následujícím příkladu Pythonu:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o možnostech hostování Azure Functions.

> [!div class="nextstepaction"]
> [Hostování a škálování Azure Functions](functions-scale.md)


