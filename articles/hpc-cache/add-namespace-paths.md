---
title: Konfigurace agregovaného oboru názvů mezipaměti HPC Azure
description: Postup vytvoření cest k back-endu pro klientské úložiště pomocí Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 090e3f93d025fe87ad5b89a98193574595f3d632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614578"
---
# <a name="set-up-the-aggregated-namespace"></a>Nastavení agregovaného oboru názvů

Po vytvoření cílů úložiště je nutné pro ně také vytvořit cesty oboru názvů. Klientské počítače používají tyto virtuální cesty k přístupu k souborům přes mezipaměť místo přímého připojení k úložišti back-endu. Tento systém umožňuje správcům mezipaměti změnit back-endové úložné systémy bez nutnosti přepsat pokyny klienta.

Další informace o této funkci najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .

Stránka **obor názvů** v Azure Portal zobrazuje cesty, které klienti používají pro přístup k datům prostřednictvím mezipaměti. Tato stránka slouží k vytvoření, odebrání nebo změně cest oboru názvů. Cesty oboru názvů můžete nakonfigurovat také pomocí rozhraní příkazového řádku Azure CLI.

Všechny existující cesty na straně klienta jsou uvedené na stránce **obor názvů** . Pokud cíl úložiště neobsahuje žádné cesty, nezobrazí se v tabulce.

Sloupce tabulky můžete seřadit kliknutím na šipky a lépe porozumět agregovanému oboru názvů vaší mezipaměti.

![snímek stránky s oborem názvů portálu se dvěma cestami v tabulce Záhlaví sloupců: cesta oboru názvů, cíl úložiště, cesta exportu a export podadresáře. Položky v prvním sloupci jsou kliknutí na odkazy. Horní tlačítka: přidat cestu k oboru názvů, aktualizovat, odstranit](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>Přidat nebo upravit cesty k oboru názvů směřujících na klienta

Než budou moci klienti získat přístup k cíli úložiště, je nutné vytvořit alespoň jednu cestu k oboru názvů. (Pokud chcete získat další informace o přístupu klientů, přečtěte si téma [připojení mezipaměti prostředí Azure HPC](hpc-cache-mount.md) .)

### <a name="blob-namespace-paths"></a>Cesty k oboru názvů objektů BLOB

Cíl úložiště objektů BLOB v Azure může mít jenom jednu cestu k oboru názvů.

Podle následujících pokynů nastavte nebo změňte cestu pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z Azure Portal načtěte stránku nastavení **oboru názvů** . Z této stránky můžete přidávat, měnit nebo odstraňovat cesty oboru názvů.

* **Přidat novou cestu:** Klikněte na tlačítko **+ Přidat** v horní části a vyplňte informace na panelu úprav.

  * V rozevíracím seznamu vyberte cíl úložiště. (Na tomto snímku obrazovky nelze vybrat cíl úložiště objektů blob, protože již má cestu k oboru názvů.)

    ![Snímek obrazovky s upravenými poli pro úpravy oboru názvů s zveřejněným selektorem cíle úložiště](media/namespace-select-storage-target.png)

  * Pro cíl úložiště objektů BLOB v Azure se cesty pro export a podadresář automaticky nastaví na ``/`` .

* **Změnit existující cestu:** Klikněte na cestu k oboru názvů. Otevře se panel úprav a můžete upravit cestu.

  ![Snímek obrazovky se stránkou s oborem názvů po kliknutí na cestu k oboru názvů objektů BLOB – pole pro úpravy se zobrazí v podokně vpravo](media/edit-namespace-blob.png)

* **Odstranit cestu k oboru názvů:** Zaškrtněte políčko vlevo od cesty a klikněte na tlačítko **Odstranit** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Při použití rozhraní příkazového řádku Azure je při vytváření cíle úložiště nutné přidat cestu k oboru názvů. Podrobnosti najdete v tématu [Přidání nového cíle služby Azure Blob Storage](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) .

Chcete-li aktualizovat cestu k oboru názvů cíle, použijte příkaz [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) . Argumenty pro příkaz Update jsou podobné argumentům příkazu CREATE s tím rozdílem, že nepředáte název kontejneru nebo účet úložiště.

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
  * 23 TB cache – 20 cest oboru názvů

* Propustnost až 5 GB/s:

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

* **Cíl úložiště** – Pokud vytváříte novou cestu k oboru názvů, vyberte cíl úložiště z rozevírací nabídky.

* **Cesta pro export** – zadejte cestu k exportu NFS. Nezapomeňte zadat název exportu správně – portál ověřuje syntaxi pro toto pole, ale nekontroluje export, dokud tuto změnu neodešlete.

* **Exportovat podadresář** – Pokud chcete, aby tato cesta připojená ke konkrétnímu podadresáři exportu, zadejte ji sem. Pokud ne, ponechte toto pole prázdné.

![snímek obrazovky se stránkou s oborem názvů portálu se stránkou aktualizace otevřenou na pravé straně](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Při použití rozhraní příkazového řádku Azure je při vytváření cíle úložiště nutné přidat alespoň jednu cestu k oboru názvů. Podrobnosti najdete v tématu [Přidání nového cíle úložiště NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) .

Pokud chcete aktualizovat cestu oboru názvů cíle nebo přidat další cesty, použijte příkaz [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) . Použijte ``--junction`` možnost k zadání všech cest oboru názvů, které chcete.

Možnosti použité pro příkaz Update jsou podobné příkazu "vytvořit", s tím rozdílem, že nepředáte informace o systému úložiště (IP adresa nebo název hostitele) a model použití je nepovinný. Další podrobnosti o syntaxi této možnosti najdete v tématu [Přidání nového cíle úložiště NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) ``--junction`` .

---

## <a name="next-steps"></a>Další kroky

Po vytvoření agregovaného oboru názvů pro vaše cíle úložiště můžete připojit klienty do mezipaměti. Další informace najdete v těchto článcích.

* [Připojení služby Azure HPC Cache](hpc-cache-mount.md)
* [Přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md)
