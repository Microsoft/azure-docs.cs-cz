---
title: Konfigurace agregovaného oboru názvů mezipaměti HPC Azure
description: Postup vytvoření cest k back-endu pro klientské úložiště pomocí Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2cb8db4e73a8f4fa299031070bffc15a2b754d7e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870368"
---
# <a name="set-up-the-aggregated-namespace"></a>Nastavení agregovaného oboru názvů

Po vytvoření cílů úložiště je nutné pro ně také vytvořit cesty oboru názvů. Klientské počítače používají tyto virtuální cesty k přístupu k souborům přes mezipaměť místo přímého připojení k úložišti back-endu. Tento systém umožňuje správcům mezipaměti změnit back-endové úložné systémy bez nutnosti přepsat pokyny klienta.

Další informace o této funkci najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .

Stránka **obor názvů** v Azure Portal zobrazuje cesty, které klienti používají pro přístup k datům prostřednictvím mezipaměti. Tato stránka slouží k vytvoření, odebrání nebo změně cest oboru názvů. Cesty oboru názvů můžete nakonfigurovat také pomocí rozhraní příkazového řádku Azure CLI.

Všechny cesty pro klienta, které byly definovány pro tuto mezipaměť, jsou uvedeny na stránce **obor názvů** . Cíle úložiště, které nemají definované žádné cesty oboru názvů, se v tabulce zatím neobjevují.

Můžete seřadit sloupce tabulky a lépe porozumět agregovanému oboru názvů vaší mezipaměti. Kliknutím na šipky v záhlaví sloupců seřadíte cesty.

[![snímek stránky s oborem názvů portálu se dvěma cestami v tabulce Záhlaví sloupců: cesta oboru názvů, cíl úložiště, cesta exportu a podadresář exportu a zásady přístupu klienta. Názvy cest v prvním sloupci jsou kliknutí na odkazy. Horní tlačítka: přidat cestu k oboru názvů, aktualizovat, odstranit ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Přidat nebo upravit cesty oboru názvů

Než budou moci klienti získat přístup k cíli úložiště, je nutné vytvořit alespoň jednu cestu k oboru názvů. (Pokud chcete získat další informace o přístupu klientů, přečtěte si téma [připojení mezipaměti prostředí Azure HPC](hpc-cache-mount.md) .)

Pokud jste nedávno přidali cíl úložiště nebo přizpůsobili zásady přístupu, může trvat několik minut, než můžete vytvořit cestu k oboru názvů.

### <a name="blob-namespace-paths"></a>Cesty k oboru názvů objektů BLOB

Cíl úložiště objektů BLOB v Azure může mít jenom jednu cestu k oboru názvů.

Podle následujících pokynů nastavte nebo změňte cestu pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z Azure Portal načtěte stránku nastavení **oboru názvů** . Z této stránky můžete přidávat, měnit nebo odstraňovat cesty oboru názvů.

* **Přidat novou cestu:** Klikněte na tlačítko **+ Přidat** v horní části a vyplňte informace na panelu úprav.

  ![Snímek obrazovky s vybranou oblastí pro úpravy oboru názvů s vybraným cílem úložiště objektů BLOB Cesty pro export a podadresář jsou nastavené na/a nelze je upravovat.](media/namespace-add-blob.png)

  * Zadejte cestu, kterou klienti použijí pro přístup k tomuto cíli úložiště.

  * Vyberte, které zásady přístupu se mají použít pro tuto cestu. Přečtěte si další informace o přizpůsobení přístupu klienta v portálu [použití zásad přístupu klienta](access-policies.md).

  * V rozevíracím seznamu vyberte cíl úložiště. Pokud cíl úložiště objektů BLOB již obsahuje cestu k oboru názvů, nelze jej vybrat.

  * Pro cíl úložiště objektů BLOB v Azure se cesty pro export a podadresář automaticky nastaví na ``/`` .

* **Změnit existující cestu:** Klikněte na cestu k oboru názvů. Otevře se panel úprav. Můžete upravit cestu a zásady přístupu, ale nemůžete změnit na jiný cíl úložiště.

* **Odstranit cestu k oboru názvů:** Zaškrtněte políčko vlevo od cesty a klikněte na tlačítko **Odstranit** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Při použití rozhraní příkazového řádku Azure je při vytváření cíle úložiště nutné přidat cestu k oboru názvů. Podrobnosti najdete v tématu [Přidání nového cíle služby Azure Blob Storage](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) .

Chcete-li aktualizovat cestu k oboru názvů cíle, použijte příkaz [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) . Argumenty pro příkaz Update jsou podobné argumentům příkazu CREATE s tím rozdílem, že nepředáte název kontejneru nebo účet úložiště.

Cestu k oboru názvů nelze odstranit z cíle úložiště objektů BLOB pomocí rozhraní příkazového řádku Azure CLI, ale můžete tuto cestu přepsat jinou hodnotou.

---

### <a name="nfs-namespace-paths"></a>Cesty oboru názvů NFS

Cíl úložiště NFS může mít několik virtuálních cest, pokud každá cesta představuje jiný export nebo podadresář ve stejném systému úložiště.

Při plánování oboru názvů pro cíl úložiště NFS Pamatujte na to, že každá cesta musí být jedinečná a nemůže být podadresářem jiné cesty k oboru názvů. Například pokud máte cestu oboru názvů, která je volána ``/parent-a`` , nemůžete také vytvořit cesty oboru názvů, jako jsou ``/parent-a/user1`` a ``/parent-a/user2`` . Tyto cesty k adresářům jsou již v oboru názvů k dispozici jako podadresáře ``/parent-a`` .

Všechny cesty oboru názvů pro systém úložiště NFS se vytvářejí na jednom cíli úložiště. Většina konfigurací mezipaměti podporuje až deset cest oboru názvů na cíl úložiště, ale větší konfigurace může podporovat až 20.

Tento seznam uvádí maximální počet cest oboru názvů na jednu konfiguraci.

* Propustnost až 2 GB/s:

  * 3 TB cache – 10 cest oboru názvů
  * 6 TB cache – 10 cest oboru názvů
  * mezipaměť z 12 TB-20 cest oboru názvů

* Propustnost až 4 GB/s:

  * 6 TB cache – 10 cest oboru názvů
  * 12 TB cache – 10 cest oboru názvů
  * 24 TB mezipaměti – 20 cest oboru názvů

* Propustnost až 8 GB/s:

  * 12 TB cache – 10 cest oboru názvů
  * 24 TB mezipaměti – 10 cest oboru názvů
  * mezipaměť 48 TB-20 cest oboru názvů

Pro každou cestu k oboru názvů systému souborů NFS zadejte cestu klienta, export systému úložiště a volitelně podadresář export.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z Azure Portal načtěte stránku nastavení **oboru názvů** . Z této stránky můžete přidat, upravit nebo odstranit cesty oboru názvů.

* **Přidání nové cesty:** Klikněte na tlačítko **+ Přidat** v horní části a vyplňte informace na panelu úprav.
* **Postup změny existující cesty:** Klikněte na cestu k oboru názvů. Otevře se panel úprav a můžete upravit cestu.
* **Postup odstranění cesty k oboru názvů:** Zaškrtněte políčko vlevo od cesty a klikněte na tlačítko **Odstranit** .

Zadejte tyto hodnoty pro každou cestu oboru názvů:

* **Cesta k oboru názvů** – cesta k souboru, na který směřuje klient.

* **Zásady přístupu klienta** – vyberte, které zásady přístupu se mají použít pro tuto cestu. Přečtěte si další informace o přizpůsobení přístupu klienta v portálu [použití zásad přístupu klienta](access-policies.md).

* **Cíl úložiště** – Pokud vytváříte novou cestu k oboru názvů, vyberte cíl úložiště z rozevírací nabídky.

* **Cesta pro export** – zadejte cestu k exportu NFS. Nezapomeňte zadat název exportu správně – portál ověřuje syntaxi pro toto pole, ale nekontroluje export, dokud tuto změnu neodešlete.

* **Exportovat podadresář** – Pokud chcete, aby tato cesta připojená ke konkrétnímu podadresáři exportu, zadejte ji sem. Pokud ne, ponechte toto pole prázdné.

![snímek obrazovky se stránkou s oborem názvů portálu se stránkou pro úpravy otevřenou vpravo. Formulář pro úpravy zobrazuje nastavení pro cestu cílového oboru názvů úložiště NFS.](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Při použití rozhraní příkazového řádku Azure je při vytváření cíle úložiště nutné přidat alespoň jednu cestu k oboru názvů. Podrobnosti najdete v tématu [Přidání nového cíle úložiště NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) .

Pokud chcete aktualizovat cestu oboru názvů cíle nebo přidat další cesty, použijte příkaz [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) . Použijte ``--junction`` možnost k zadání všech cest oboru názvů, které chcete.

Možnosti použité pro příkaz Update jsou podobné příkazu "vytvořit", s tím rozdílem, že nepředáte informace o systému úložiště (IP adresa nebo název hostitele) a model použití je nepovinný. Další podrobnosti o syntaxi této možnosti najdete v tématu [Přidání nového cíle úložiště NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) ``--junction`` .

---

### <a name="adls-nfs-namespace-paths-preview"></a>ADLS – cesty oboru názvů systému souborů NFS (PREVIEW)

Podobně jako u běžného cíle služby Blob Storage má cíl úložiště ADLS-NFS jenom jeden export, takže může mít jenom jednu cestu k oboru názvů.

Podle následujících pokynů nastavte nebo změňte cestu pomocí Azure Portal.

Načtěte stránku nastavení **oboru názvů** .

* **Přidat novou cestu:** Klikněte na tlačítko **+ Přidat** v horní části a vyplňte informace na panelu úprav.

  ![Snímek obrazovky s vybranou oblastí pro úpravy oboru názvů s vybraným cílem úložiště ADLS-NFS Cesty pro export a podadresář jsou nastavené na/a nelze je upravovat.](media/namespace-add-adls.png)

  * Zadejte cestu, kterou klienti použijí pro přístup k tomuto cíli úložiště.

  * Vyberte, které zásady přístupu se mají použít pro tuto cestu. Přečtěte si další informace o přizpůsobení přístupu klienta v portálu [použití zásad přístupu klienta](access-policies.md).

  * V rozevíracím seznamu vyberte cíl úložiště. Pokud cíl úložiště ADLS se systémem souborů NFS už má cestu oboru názvů, nedá se vybrat.

  * V případě cíle úložiště ADLS-NFS jsou cesty export a podadresář automaticky nastavené na ``/`` .

* **Změnit existující cestu:** Klikněte na cestu k oboru názvů. Otevře se panel úprav. Můžete upravit cestu a zásady přístupu, ale nemůžete změnit na jiný cíl úložiště.

* **Odstranit cestu k oboru názvů:** Zaškrtněte políčko vlevo od cesty a klikněte na tlačítko **Odstranit** .

## <a name="next-steps"></a>Další kroky

Po vytvoření agregovaného oboru názvů pro vaše cíle úložiště můžete připojit klienty do mezipaměti. Další informace najdete v těchto článcích.

* [Připojení služby Azure HPC Cache](hpc-cache-mount.md)
* [Přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md)
