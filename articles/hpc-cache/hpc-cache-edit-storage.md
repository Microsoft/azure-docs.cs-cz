---
title: Aktualizace cílů úložiště mezipaměti HPC Azure
description: Úprava cílů úložiště mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 70f350204796099e02f7afe829a6e2e1fdf653c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613089"
---
# <a name="edit-storage-targets"></a>Úprava cílů úložiště

Můžete odebrat nebo upravit cíle úložiště pomocí Azure Portal nebo pomocí Azure CLI.

V závislosti na typu úložiště můžete tyto hodnoty cíle úložiště změnit:

* V případě cílů služby Blob Storage můžete změnit cestu k oboru názvů.

* V případě cílů úložiště NFS můžete tyto hodnoty změnit:

  * Cesty oboru názvů
  * Podadresář exportu nebo exportu úložiště přidruženého k cestě oboru názvů
  * Model využití

Nemůžete upravit název, typ nebo back-end úložiště cíle úložiště (kontejner objektů BLOB nebo název hostitele nebo IP adresa systému souborů NFS). Pokud potřebujete tyto vlastnosti změnit, odstraňte cíl úložiště a vytvořte náhradu s novou hodnotou.

> [!TIP]
> [Správa videa Azure HPC cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) ukazuje, jak upravit cíl úložiště v Azure Portal.

## <a name="remove-a-storage-target"></a>Odebrání cíle úložiště

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete cíl úložiště odebrat, otevřete stránku **cíle úložiště** . V seznamu vyberte cíl úložiště a klikněte na tlačítko **Odstranit** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Pomocí [AZ HPC-cache Storage-Target Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) odstraňte cíl úložiště z mezipaměti.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Odstraněním cíle úložiště se odebere přidružení systému úložiště k tomuto systému mezipaměti prostředí Azure HPC, ale nemění se back-end systém úložiště. Pokud jste například použili kontejner úložiště objektů BLOB v Azure, kontejner a jeho obsah stále existují i po jeho odstranění z mezipaměti. Kontejner můžete přidat do jiné mezipaměti prostředí Azure HPC, znovu ho přidat do této mezipaměti nebo ho odstranit pomocí Azure Portal.

Všechny změny souborů uložené v mezipaměti se zapisují do back-endového systému úložiště před odebráním cíle úložiště. Pokud se velké množství změněných dat nachází v mezipaměti, může tento proces trvat hodinu nebo déle.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Změna cesty oboru názvů cíle služby Blob Storage

Cesty oboru názvů jsou cesty, které klienti používají k připojení tohoto cíle úložiště. (Další informace najdete v článku [Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) a [Nastavení agregovaného oboru názvů](add-namespace-paths.md)).

Jediná aktualizace, kterou můžete provést v cíli úložiště objektů BLOB v Azure. K jeho změně použijte Azure Portal nebo rozhraní příkazového řádku Azure.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Použijte stránku **oboru názvů** pro mezipaměť prostředí Azure HPC. Na stránce obor názvů je podrobněji popsáno v článku [Nastavení agregovaného oboru názvů](add-namespace-paths.md).

Klikněte na název cesty, kterou chcete změnit, a vytvořte novou cestu v zobrazeném okně pro úpravy.

![Snímek obrazovky se stránkou s oborem názvů po kliknutí na cestu k oboru názvů objektů BLOB – pole pro úpravy se zobrazí v podokně vpravo](media/edit-namespace-blob.png)

Po provedení změn aktualizujte cíl úložiště kliknutím na tlačítko **OK** . Kliknutím na tlačítko **Storno** zahodíte změny.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Pokud chcete změnit obor názvů cíle služby Blob Storage pomocí Azure CLI, použijte příkaz [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). `--virtual-namespace-path`Lze změnit pouze hodnotu.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aktualizace cíle úložiště NFS

V případě cílů úložiště NFS můžete měnit nebo přidávat cesty k virtuálnímu oboru názvů, měnit hodnoty exportu nebo podadresáře NFS, na které cesta oboru názvů odkazuje, a změnit model využití.

Podrobnosti jsou uvedené níže:

* [Změna agregovaných hodnot oboru názvů](#change-aggregated-namespace-values) (cesta k virtuálnímu oboru názvů, export a export podadresáře)
* [Změna modelu použití](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Změna agregovaných hodnot oboru názvů

Pomocí Azure Portal nebo rozhraní příkazového řádku Azure můžete změnit cestu k oboru názvů směřující na klienta, export úložiště a podadresář export (Pokud se používá).

Pokud potřebujete připomenutí, jak vytvořit několik platných cest na jednom cíli úložiště, přečtěte si pokyny v tématu [přidání cest k oboru názvů systému souborů NFS](add-namespace-paths.md#nfs-namespace-paths) .

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pro aktualizaci hodnot oboru názvů použijte stránku **oboru názvů** pro mezipaměť prostředí Azure HPC. Tato stránka je podrobněji popsána v článku [Nastavení agregovaného oboru názvů](add-namespace-paths.md).

![snímek obrazovky se stránkou s oborem názvů portálu se stránkou aktualizace NFS otevřená na pravé straně](media/update-namespace-nfs.png)

1. Klikněte na název cesty, kterou chcete změnit.
1. Použijte okno Upravit k zadání nové virtuální cesty, exportu nebo hodnot podadresářů.
1. Po provedení změn aktualizujte cíl úložiště kliknutím na tlačítko **OK** . Chcete-li změny zahodit, klikněte na tlačítko **Storno** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Použijte ``--junction`` možnost v příkazu [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) a změňte cestu k oboru názvů, export NFS nebo export podadresáře.

``--junction``Parametr používá tyto hodnoty:

* ``namespace-path`` – Cesta k virtuálnímu souboru s přístupem klienta
* ``nfs-export`` – Export systému úložiště, který se má přidružit k cestě s přístupem klienta
* ``target-path`` (volitelné) – podadresář exportu (v případě potřeby)

Příklad: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Pro každou cestu v příkazu je nutné dodat všechny tři hodnoty ``--junction`` . Pro všechny hodnoty, které nechcete měnit, použijte existující hodnoty.

Název mezipaměti, název cíle úložiště a skupina prostředků jsou také požadovány ve všech příkazech Update.

Příklad příkazu:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Změna modelu použití

Model použití má vliv na to, jak mezipaměť uchovává data. Další informace najdete [v tématu Výběr modelu využití](hpc-cache-add-storage.md#choose-a-usage-model) .

Pokud chcete změnit model použití pro cíl úložiště NFS, použijte jednu z těchto metod.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Změňte model využití ze stránky **cíle úložiště** v Azure Portal. Klikněte na název cíle úložiště, který chcete změnit.

![snímek obrazovky se stránkou pro úpravy cíle úložiště NFS](media/edit-storage-nfs.png)

Pomocí rozevíracího selektoru vyberte nový model využití. Kliknutím na tlačítko **OK** aktualizujte cíl úložiště, nebo kliknutím na tlačítko **Storno** zahoďte změny.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Použijte příkaz [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target?view=azure-cli-latest#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) .

Příkaz Update je skoro stejný jako příkaz, který používáte k přidání cíle úložiště systému souborů NFS. Podrobnosti a příklady najdete v tématu [Vytvoření cíle úložiště NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target) .

Chcete-li změnit model použití, aktualizujte ``--nfs3-usage-model`` možnost. Příklad: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Také jsou požadovány názvy mezipaměti, název cíle úložiště a skupiny prostředků.

Pokud chcete ověřit názvy modelů použití, použijte příkaz [AZ HPC-cache Usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Pokud je mezipaměť zastavená nebo není v dobrém stavu, bude aktualizace platit až po dobrém stavu mezipaměti.

---

## <a name="next-steps"></a>Další kroky

* Další informace o těchto možnostech najdete v tématu [Přidání cílů úložiště](hpc-cache-add-storage.md) .
* Další tipy k používání virtuálních cest najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .
