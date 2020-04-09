---
title: Důležité informace o úložišti pro funkce Azure
description: Přečtěte si o požadavcích na úložiště v Azure Functions a o šifrování uložených dat.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 48ff2dedd997cccb76b13acdadc895504f656ea3
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984159"
---
# <a name="storage-considerations-for-azure-functions"></a>Důležité informace o úložišti pro funkce Azure

Funkce Azure vyžadují účet Azure Storage při vytváření instance aplikace funkce. Aplikace funkce může používat následující služby úložiště:


|Služba úložiště  | Využití funkcí  |
|---------|---------|
| [Úložiště objektů blob Azure](../storage/blobs/storage-blobs-introduction.md)     | Udržovat vazby stavu a funkční klíče.  <br/>Používá se také [v rozbočovačích úloh v aplikaci Durable Functions](durable/durable-functions-task-hubs.md). |
| [Soubory Azure](../storage/files/storage-files-introduction.md)  | Sdílená složka používaná k uložení a spuštění kódu aplikace funkce v [plánu spotřeby](functions-scale.md#consumption-plan). |
| [Úložiště fronty Azure](../storage/queues/storage-queues-introduction.md)     | Používá se [rozbočovači úloh v trvanlivé funkce](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  Používá se [rozbočovači úloh v trvanlivé funkce](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Pokud použijete plán hostování Consumption, kód vaší funkce a vázané konfigurační soubory se ukládají ve službě Azure File Storage v hlavním účtu úložiště. Když odstraníte hlavní účet úložiště, tento obsah se odstraní a není možné ho obnovit.

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

Při vytváření aplikace funkce musíte vytvořit nebo propojit účet úložiště Azure pro obecné účely, který podporuje úložiště objektů Blob, Fronta a Tabulka. Důvodem je, že funkce závisí na Azure Storage pro operace, jako je správa aktivačních událostí a spuštění funkcí protokolování. Některé účty úložiště nepodporují fronty a tabulky. Tyto účty zahrnují účty úložiště pouze pro objekty blob, Azure Premium Storage a účty úložiště pro obecné účely s replikací ZRS. Tyto nepodporované účty jsou odfiltrovány z okna účtu úložiště při vytváření aplikace funkce.

Další informace o typech účtů úložiště najdete v tématu [Seznámení se službami Azure Storage](../storage/common/storage-introduction.md#core-storage-services). 

Zatímco můžete použít existující účet úložiště s vaší aplikace funkce, musíte se ujistit, že splňuje tyto požadavky. Účty úložiště vytvořené jako součást toku vytvoření aplikace funkce je zaručeno, že splňují tyto požadavky na účet úložiště.  

## <a name="storage-account-guidance"></a>Pokyny k účtu úložiště

Každá aplikace funkce vyžaduje účet úložiště pro provoz. Pokud je tento účet odstraněn, vaše funkční aplikace se nespustí. Informace o řešení problémů souvisejících s úložištěm najdete v [tématu Řešení potíží souvisejících s úložištěm](functions-recover-storage-account.md). Následující další aspekty platí pro účet úložiště používané funkce aplikací.

### <a name="storage-account-connection-setting"></a>Nastavení připojení účtu úložiště

Připojení účtu úložiště se udržuje v [nastavení aplikace AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Při opětovném generování klíčů úložiště je nutné aktualizovat připojovací řetězec účtu úložiště. [Další informace o správě klíčů úložiště načteme zde](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Účty sdíleného úložiště

Je možné, že více funkčních aplikací sdílí stejný účet úložiště bez problémů. Například v Sadě Visual Studio můžete vyvíjet více aplikací pomocí emulátoru úložiště Azure. V tomto případě emulátor funguje jako jeden účet úložiště. Stejný účet úložiště, který používá vaše aplikace funkce, lze také použít k ukládání dat aplikace. Tento přístup však není vždy dobrý nápad v produkčním prostředí.

### <a name="optimize-storage-performance"></a>Optimalizace výkonu úložiště

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Šifrování dat úložiště

Azure Storage šifruje všechna data v účtu úložiště v klidovém stavu. Další informace najdete v tématu [šifrování Azure Storage pro data v klidovém stavu](../storage/common/storage-service-encryption.md).

Ve výchozím nastavení jsou data šifrována pomocí klíčů spravovaných společností Microsoft. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které se použijí pro šifrování dat objektů blob a souborů. Tyto klíče musí být k dispozici v trezoru klíčů Azure pro funkce, aby bylo možné získat přístup k účtu úložiště. Další informace najdete [v tématu Konfigurace klíčů spravovaných zákazníky pomocí Azure Key Vault pomocí webu Azure Portal](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Připojení sdílených složek (Linux)

Existující sdílené složky Azure Files můžete připojit do aplikací pro funkce Linuxu. Připojením sdílené položky do aplikace pro funkce Linuxu můžete ve svých funkcích využít stávající modely strojového učení nebo jiná data. Pomocí příkazu [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) můžete připojit existující sdílenou složku do aplikace pro funkce Linuxu. 

V tomto `share-name` příkazu je název existující sdílené `custom-id` složky Azure Files a může být libovolný řetězec, který jedinečně definuje sdílenou složku při připojení k aplikaci funkce. Také `mount-path` je cesta, ze které je sdílená skupina přístupná ve vaší aplikaci funkce. `mount-path`musí být ve `/dir-name`formátu a nemůže začínat na . `/home`

Úplný příklad najdete v tématu skripty v [aplikaci Vytvoření funkce Pythonu a připojení sdílené složky Azure Files](scripts/functions-cli-mount-files-storage-linux.md). 

V současné době `storage-type` `AzureFiles` je podporovánpouze z je podporována. Do aplikace pro danou funkci můžete připojit pouze pět sdílených složek. Připojení sdílené složky může zvýšit čas studeného startu nejméně o 200 až 300 ms nebo dokonce více, když je účet úložiště v jiné oblasti.

Připojená sdílená složky je `mount-path` k dispozici pro kód funkce na zadaný. Například když `mount-path` `/path/to/mount`je , můžete přistupovat k cílovému adresáři pomocí rozhraní API systému souborů, jako v následujícím příkladu Pythonu:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o možnostech hostování Azure Functions.

> [!div class="nextstepaction"]
> [Hostování a škálování Azure Functions](functions-scale.md)


