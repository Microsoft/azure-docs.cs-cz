---
title: Aktualizace cílů úložiště mezipaměti HPC Azure
description: Úprava cílů úložiště mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: ebf68c1eb06984e2de8114c53e1bb55d52aed70a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862628"
---
# <a name="edit-storage-targets"></a>Úprava cílů úložiště

Můžete odebrat nebo upravit cíle úložiště pomocí Azure Portal nebo pomocí Azure CLI.

V závislosti na typu úložiště můžete tyto hodnoty cíle úložiště změnit:

* V případě cílů služby Blob Storage můžete změnit cestu k oboru názvů a zásady přístupu.

* V případě cílů úložiště NFS můžete tyto hodnoty změnit:

  * Cesty oboru názvů
  * Zásady přístupu
  * Podadresář exportu nebo exportu úložiště přidruženého k cestě oboru názvů
  * Model využití

* V případě cílů úložiště ADLS-NFS můžete změnit cestu k oboru názvů, zásadu přístupu a model využití.

Nemůžete upravit název, typ nebo back-end úložiště cíle úložiště (kontejner objektů BLOB nebo název hostitele nebo IP adresa systému souborů NFS). Pokud potřebujete tyto vlastnosti změnit, odstraňte cíl úložiště a vytvořte náhradu s novou hodnotou.

> [!TIP]
> [Správa videa Azure HPC cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) ukazuje, jak upravit cíl úložiště v Azure Portal.

## <a name="remove-a-storage-target"></a>Odebrání cíle úložiště

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete cíl úložiště odebrat, otevřete stránku **cíle úložiště** . V seznamu vyberte cíl úložiště a klikněte na tlačítko **Odstranit** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Pomocí [AZ HPC-cache Storage-Target Remove](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) odstraňte cíl úložiště z mezipaměti.

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

![Snímek obrazovky se stránkou s oborem názvů po kliknutí na cestu k oboru názvů objektů BLOB – pole pro úpravy se zobrazí v podokně vpravo](media/update-namespace-blob.png)

Po provedení změn aktualizujte cíl úložiště kliknutím na tlačítko **OK** . Kliknutím na tlačítko **Storno** zahodíte změny.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Pokud chcete změnit obor názvů cíle služby Blob Storage pomocí Azure CLI, použijte příkaz [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update). `--virtual-namespace-path`Lze změnit pouze hodnotu.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aktualizace cíle úložiště NFS

V případě cílů úložiště NFS můžete měnit nebo přidávat cesty k virtuálnímu oboru názvů, měnit hodnoty exportu nebo podadresáře NFS, na které cesta oboru názvů odkazuje, a změnit model využití.

Cíle úložiště v mezipamětech s některými typy vlastních nastavení DNS mají také ovládací prvek pro obnovení IP adres. (Tento druh konfigurace je zřídka.)

Podrobnosti jsou uvedené níže:

* [Změna agregovaných hodnot oboru názvů](#change-aggregated-namespace-values) (cesta k virtuálnímu oboru názvů, zásada přístupu, export a export podadresáře)
* [Změna modelu použití](#change-the-usage-model)
* [Aktualizovat DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Změna agregovaných hodnot oboru názvů

Pomocí Azure Portal nebo rozhraní příkazového řádku Azure můžete změnit cestu k oboru názvů směřující na klienta, export úložiště a podadresář export (Pokud se používá).

Pokud potřebujete připomenutí, jak vytvořit několik platných cest na jednom cíli úložiště, přečtěte si pokyny v tématu [přidání cest k oboru názvů systému souborů NFS](add-namespace-paths.md#nfs-namespace-paths) .

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pro aktualizaci hodnot oboru názvů použijte stránku **oboru názvů** pro mezipaměť prostředí Azure HPC. Tato stránka je podrobněji popsána v článku [Nastavení agregovaného oboru názvů](add-namespace-paths.md).

![snímek obrazovky se stránkou s oborem názvů portálu se stránkou aktualizace NFS otevřená na pravé straně](media/update-namespace-nfs.png)

1. Klikněte na název cesty, kterou chcete změnit.
1. Pomocí okna Upravit můžete zadat nové virtuální cesty, exportovat nebo podadresáře a vybrat jiné zásady přístupu.
1. Po provedení změn aktualizujte cíl úložiště kliknutím na tlačítko **OK** . Chcete-li změny zahodit, klikněte na tlačítko **Storno** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Použijte ``--junction`` možnost v příkazu [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/hpc-cache/nfs-storage-target) a změňte cestu k oboru názvů, export NFS nebo export podadresáře.

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

Model použití má vliv na to, jak mezipaměť uchovává data. Další informace najdete v tématu [Principy modelů používání mezipaměti](cache-usage-models.md) .

> [!NOTE]
> Pokud změníte modely použití, bude pravděpodobně nutné znovu připojit klienty, aby nedocházelo k chybám NLM. Přečtěte si informace o [tom, kdy se mají klienti znovu připojit](cache-usage-models.md#know-when-to-remount-clients-for-nlm) .

Pokud chcete změnit model použití pro cíl úložiště NFS, použijte jednu z těchto metod.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Změňte model využití ze stránky **cíle úložiště** v Azure Portal. Klikněte na název cíle úložiště, který chcete změnit.

![snímek obrazovky se stránkou pro úpravy cíle úložiště NFS](media/edit-storage-nfs.png)

Pomocí rozevíracího selektoru vyberte nový model využití. Kliknutím na tlačítko **OK** aktualizujte cíl úložiště, nebo kliknutím na tlačítko **Storno** zahoďte změny.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Použijte příkaz [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) .

Příkaz Update je skoro stejný jako příkaz, který používáte k přidání cíle úložiště systému souborů NFS. Podrobnosti a příklady najdete v tématu [Vytvoření cíle úložiště NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target) .

Chcete-li změnit model použití, aktualizujte ``--nfs3-usage-model`` možnost. Příklad: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Také jsou požadovány názvy mezipaměti, název cíle úložiště a skupiny prostředků.

Pokud chcete ověřit názvy modelů použití, použijte příkaz [AZ HPC-cache Usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list).

Pokud je mezipaměť zastavená nebo není v dobrém stavu, bude aktualizace platit až po dobrém stavu mezipaměti.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>IP adresa aktualizace (jenom vlastní konfigurace DNS)

Pokud vaše mezipaměť používá jinou než výchozí konfiguraci DNS, je možné, že se IP adresa cíle úložiště NFS změní kvůli změnám back-endu DNS. Pokud váš server DNS změní IP adresu systému back-end úložiště, mezipaměť prostředí Azure HPC může ztratit přístup k systému úložiště.

V ideálním případě byste měli pracovat se správcem vlastního systému DNS vaší mezipaměti, aby bylo možné naplánovat jakékoli aktualizace, protože u těchto změn není úložiště k dispozici.

Pokud potřebujete aktualizovat IP adresu poskytnutou službou DNS cíle úložiště, v seznamu cílů úložiště je tlačítko. Kliknutím na **Aktualizovat DNS** se můžete dotazovat na vlastní server DNS pro novou IP adresu.

![Snímek obrazovky s cílovým seznamem úložiště Pro jeden cíl úložiště, "..." nabídka ve sloupci úplně vpravo je otevřená a zobrazí se dvě možnosti: odstranit a aktualizovat DNS.](media/refresh-dns.png)

V případě úspěchu by tato aktualizace měla trvat déle než dvě minuty. Najednou můžete aktualizovat jenom jeden cíl úložiště. před dalším pokusem o dokončení operace počkejte, než se předchozí operace dokončí.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Aktualizace cíle úložiště ADLS-NFS (PREVIEW)

Podobně jako cíle NFS můžete změnit cestu k oboru názvů a model použití pro cíle úložiště ADLS-NFS.

### <a name="change-an-adls-nfs-namespace-path"></a>Změna cesty k oboru názvů ADLS-NFS

Pro aktualizaci hodnot oboru názvů použijte stránku **oboru názvů** pro mezipaměť prostředí Azure HPC. Tato stránka je podrobněji popsána v článku [Nastavení agregovaného oboru názvů](add-namespace-paths.md).

![snímek obrazovky se stránkou s oborem názvů portálu se stránkou aktualizace služby ADS – NFS otevřená na pravé straně](media/update-namespace-adls.png)

1. Klikněte na název cesty, kterou chcete změnit.
1. Pomocí okna Upravit zadejte novou virtuální cestu nebo aktualizujte zásady přístupu.
1. Po provedení změn aktualizujte cíl úložiště kliknutím na tlačítko **OK** . Chcete-li změny zahodit, klikněte na tlačítko **Storno** .

### <a name="change-adls-nfs-usage-models"></a>Změna modelů využití ADLS-NFS

Konfigurace pro modely využití ADLS-NFS je shodná s volbou modelu použití systému souborů NFS. Přečtěte si pokyny na portálu v části [Změna modelu použití](#change-the-usage-model) v oddílu NFS výše. Další nástroje pro aktualizaci cílů úložiště ADLS-NFS jsou ve vývoji.


## <a name="next-steps"></a>Další kroky

* Další informace o těchto možnostech najdete v tématu [Přidání cílů úložiště](hpc-cache-add-storage.md) .
* Další tipy k používání virtuálních cest najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .
