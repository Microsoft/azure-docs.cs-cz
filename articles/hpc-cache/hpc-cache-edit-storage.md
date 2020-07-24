---
title: Aktualizace cílů úložiště mezipaměti HPC Azure
description: Úprava cílů úložiště mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092452"
---
# <a name="edit-storage-targets"></a>Úprava cílů úložiště

Cíl úložiště můžete odebrat nebo upravit na stránce portálu cílového **úložiště** mezipaměti nebo pomocí Azure CLI.

> [!TIP]
> [Správa videa Azure HPC cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) ukazuje, jak upravit cíl úložiště v Azure Portal.

## <a name="remove-a-storage-target"></a>Odebrání cíle úložiště

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete cíl úložiště odebrat, vyberte ho v seznamu a klikněte na tlačítko **Odstranit** .

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

Tato akce odebere přidružení cíle úložiště k tomuto systému mezipaměti prostředí Azure HPC, ale nemění back-end systém úložiště. Pokud jste například použili kontejner úložiště objektů BLOB v Azure, kontejner a jeho obsah stále existují i po jeho odstranění z mezipaměti. Kontejner můžete přidat do jiné mezipaměti prostředí Azure HPC, znovu ho přidat do této mezipaměti nebo ho odstranit pomocí Azure Portal.

Všechny změny souborů uložené v mezipaměti se zapisují do back-endového systému úložiště před odebráním cíle úložiště. Pokud se velké množství změněných dat nachází v mezipaměti, může tento proces trvat hodinu nebo déle.

## <a name="update-storage-targets"></a>Aktualizace cílů úložiště

Změnou cílů úložiště můžete upravit některé z jejich vlastností. Různé vlastnosti jsou editovatelné pro různé typy úložiště:

* V případě cílů služby Blob Storage můžete změnit cestu k oboru názvů.

* V případě cílů úložiště NFS můžete tyto vlastnosti změnit:

  * Cesta oboru názvů
  * Model využití
  * Export
  * Exportovat podadresář

Nemůžete upravit název, typ nebo back-end úložiště cíle úložiště (kontejner objektů BLOB nebo název hostitele nebo IP adresa systému souborů NFS). Pokud potřebujete tyto vlastnosti změnit, odstraňte cíl úložiště a vytvořte náhradu s novou hodnotou.

V Azure Portal můžete zobrazit, která pole jsou upravitelná, a to tak, že kliknete na název cíle úložiště a otevřete jeho stránku s podrobnostmi. Můžete také upravit cíle úložiště pomocí Azure CLI.

![snímek obrazovky se stránkou pro úpravy cíle úložiště NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aktualizace cíle úložiště NFS

V případě cíle úložiště NFS můžete aktualizovat několik vlastností. (Příklad stránky pro úpravy najdete na snímku obrazovky výše.)

* **Model použití** – model použití ovlivňuje, jak mezipaměť uchovává data. Další informace najdete [v tématu Výběr modelu využití](hpc-cache-add-storage.md#choose-a-usage-model) .
* **Cesta k virtuálnímu oboru názvů** – cesta, kterou klienti používají k připojení tohoto cíle úložiště. Podrobnosti najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .
* **Cesta exportu NFS** – export systému úložiště, který se má použít pro tuto cestu oboru názvů.
* **Cesta k podadresáři** – podadresář (v rámci exportu), který má být přidružen k této cestě k oboru názvů. Pokud nepotřebujete zadat podadresář, ponechte toto pole prázdné.

Každá cesta k oboru názvů vyžaduje jedinečnou kombinaci exportu a podadresáře. To znamená, že nemůžete vytvořit dvě různé cesty směřující na klienta do stejného adresáře v systému back-endu.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Po provedení změn aktualizujte cíl úložiště kliknutím na tlačítko **OK** . Kliknutím na tlačítko **Storno** zahodíte změny.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Pomocí příkazu [AZ NFS-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) můžete změnit model použití, cestu k virtuálnímu oboru názvů a export nebo podadresáře NFS pro cíl úložiště.

* Chcete-li změnit model použití, použijte ``--nfs3-usage-model`` možnost. Příklad: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Chcete-li změnit cestu k oboru názvů, exportovat nebo exportovat podadresář, použijte ``--junction`` možnost.

  ``--junction``Parametr používá tyto hodnoty:

  * ``namespace-path``– Cesta k virtuálnímu souboru s přístupem klienta
  * ``nfs-export``– Export systému úložiště, který se má přidružit k cestě s přístupem klienta
  * ``target-path``(volitelné) – podadresář exportu (v případě potřeby)

  Příklad: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Ve všech příkazech Update se vyžadují název mezipaměti, název cíle úložiště a skupina prostředků.

Příklad příkazu: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Pokud je mezipaměť zastavená nebo není v dobrém stavu, bude se tato aktualizace vztahovat i po dobrém stavu mezipaměti.

---

## <a name="update-an-azure-blob-storage-target"></a>Aktualizace cíle Azure Blob Storage

V případě cíle služby Blob Storage můžete upravit cestu k virtuálnímu oboru názvů.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Stránka s podrobnostmi pro cíl úložiště objektů BLOB umožňuje upravit cestu k virtuálnímu oboru názvů.

![snímek obrazovky se stránkou pro úpravy pro cíl úložiště objektů BLOB](media/hpc-cache-edit-storage-blob.png)

Až budete hotovi, kliknutím na **OK** aktualizujte cíl úložiště, nebo klikněte na **Zrušit** , aby se změny zahodit.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Pomocí [AZ HPC-cache BLOB-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) aktualizujte cestu k oboru názvů cíle.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Pokud je mezipaměť zastavená nebo není v dobrém stavu, bude aktualizace platit až po dobrém stavu mezipaměti.

---

## <a name="next-steps"></a>Další kroky

* Další informace o těchto možnostech najdete v tématu [Přidání cílů úložiště](hpc-cache-add-storage.md) .
* Další tipy k používání virtuálních cest najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .
