---
title: Aktualizace cílů úložiště mezipaměti Azure HPC
description: Jak upravit cíle úložiště Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867005"
---
# <a name="edit-storage-targets"></a>Úprava cílů úložiště

Cíl úložiště můžete odebrat nebo upravit ze stránky **cílů úložiště mezipaměti.**

## <a name="remove-a-storage-target"></a>Odebrání cíle úložiště

Chcete-li odebrat cíl úložiště, vyberte ho v seznamu a klepněte na tlačítko **Odstranit.**

Tato akce odebere přidružení cíle úložiště s tímto systémem Azure HPC Cache, ale nezmění systém úložiště back-end. Například pokud jste použili kontejner úložiště objektů blob Azure, kontejner a jeho obsah stále existují po odstranění z mezipaměti. Kontejner můžete přidat do jiné mezipaměti Azure HPC, znovu ho přidat do této mezipaměti nebo ho odstranit pomocí portálu Azure.

Všechny změny souborů uložené v mezipaměti jsou zapsány do systému úložiště back-end před odebráním cíle úložiště. Tento proces může trvat hodinu nebo více, pokud je v mezipaměti mnoho změněných dat.

## <a name="update-storage-targets"></a>Aktualizace cílů úložiště

Můžete upravit cíle úložiště a upravit některé jejich vlastnosti. Různé vlastnosti lze upravovat pro různé typy úložišť:

* U cílů úložiště objektů Blob můžete změnit cestu oboru názvů.

* U cílů úložiště služby NFS můžete změnit tyto vlastnosti:

  * Cesta oboru názvů
  * Model použití
  * Export
  * Exportovat podadresář

Nelze upravit název úložiště cíl, typ nebo back-end úložný systém (kontejner objektů blob nebo název_hostitele NFS/IP adresa). Pokud potřebujete změnit tyto vlastnosti, odstraňte cíl úložiště a vytvořte náhradu za novou hodnotu.

Chcete-li upravit cíl úložiště, kliknutím na cílový název úložiště otevřete jeho stránku podrobností. Některá pole na stránce lze upravovat.

![Snímek obrazovky s editační stránkou pro cíl úložiště systému Sdílení neotesaných dat](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aktualizace cíle úložiště systému nfs

Pro cíl úložiště systému windows můžete aktualizovat několik vlastností. (Příklad stránky pro úpravy naleznete na obrázku výše.)

* **Model použití** – model použití ovlivňuje způsob, jakým mezipaměť uchovává data. Přečtěte si článek Další informace [najdete v článek Výběr modelu použití.](hpc-cache-add-storage.md#choose-a-usage-model)
* **Cesta virtuálního oboru názvů** – cesta, kterou klienti používají k připojení tohoto cíle úložiště. Přečtěte [si naplánujte agregovaný obor názvů](hpc-cache-namespace.md) podrobnosti.
* **Cesta exportu systému souborů NFS** – export systému úložiště pro tuto cestu oboru názvů.
* **Cesta podadresáře** - Podadresář (pod exportem), který chcete přidružit k této cestě oboru názvů. Pokud nepotřebujete zadat podadresář, ponechejte toto pole prázdné.

Každá cesta oboru názvů potřebuje jedinečnou kombinaci exportu a podadresáře. To znamená, že nelze vytvořit dvě různé cesty směřující ke klientovi do přesně stejného adresáře v systému úložiště back-end.

Po provedení změn kliknutím na **OK** aktualizujte cíl úložiště nebo klepnutím na tlačítko **Storno** změny zamítnete.

## <a name="update-an-azure-blob-storage-target"></a>Aktualizace cíle úložiště objektů blob Azure

Stránka podrobností pro cíl úložiště objektů Blob umožňuje upravit cestu virtuálního oboru názvů.

![Snímek obrazovky s editační stránkou pro cíl úložiště objektů blob](media/hpc-cache-edit-storage-blob.png)

Po dokončení klikněte na **OK,** abyste aktualizovali cíl úložiště, nebo klepnutím na **tlačítko Storno** změny zamítnete.

## <a name="next-steps"></a>Další kroky

* Další informace o těchto možnostech najdete v [části Přidání cílů úložiště.](hpc-cache-add-storage.md)
* Přečtěte [si naplánujte agregovaný obor názvů,](hpc-cache-namespace.md) kde najdete další tipy k používání virtuálních cest.
