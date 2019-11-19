---
title: Aktualizace cílů úložiště mezipaměti HPC Azure
description: Úprava cílů úložiště mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 115e75c0149a35104d9c3696710bf8231a98743d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168514"
---
# <a name="edit-storage-targets"></a>Úprava cílů úložiště

Změnou cílů úložiště můžete upravit některé z jejich vlastností. Různé vlastnosti lze upravovat pro různé typy úložišť:

* V případě cílů služby Blob Storage můžete změnit cestu k oboru názvů.

* V případě cílů úložiště NFS můžete tyto vlastnosti změnit:

  * Cesta oboru názvů
  * Model využití
  * Export
  * Exportovat podadresář

Nemůžete upravit název, typ nebo back-end úložiště cíle úložiště (kontejner objektů BLOB nebo název hostitele nebo IP adresa systému souborů NFS). Pokud potřebujete tyto vlastnosti změnit, odstraňte cíl úložiště a vytvořte náhradu s novou hodnotou.

Chcete-li upravit cíl úložiště, klikněte na název cíle úložiště a otevřete jeho stránku s podrobnostmi. Některá pole na stránce lze upravovat.

![snímek obrazovky se stránkou pro úpravy cíle úložiště NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aktualizace cíle úložiště NFS

V případě cíle úložiště NFS můžete aktualizovat několik vlastností. (Příklad stránky pro úpravy najdete na snímku obrazovky výše.)

* **Model použití** – model použití ovlivňuje, jak mezipaměť uchovává data. Další informace najdete [v tématu Výběr modelu využití](hpc-cache-add-storage.md#choose-a-usage-model) .
* **Cesta k virtuálnímu oboru názvů** – cesta, kterou klienti používají k připojení tohoto cíle úložiště. Podrobnosti najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .
* **Cesta exportu NFS** – export systému úložiště, který se má použít pro tuto cestu oboru názvů.
* **Cesta k podadresáři** – podadresář (v rámci exportu), který má být přidružen k této cestě k oboru názvů. Pokud nepotřebujete zadat podadresář, ponechte toto pole prázdné.

Každá cesta k oboru názvů vyžaduje jedinečnou kombinaci exportu a podadresáře. To znamená, že nemůžete vytvořit dvě různé cesty směřující na klienta do stejného adresáře v systému back-endu.

Po provedení změn aktualizujte cíl úložiště kliknutím na tlačítko **OK** . Kliknutím na tlačítko **Storno** zahodíte změny.

## <a name="update-an-azure-blob-storage-target"></a>Aktualizace cíle Azure Blob Storage

Stránka s podrobnostmi pro cíl úložiště objektů BLOB umožňuje upravit cestu k virtuálnímu oboru názvů.

![snímek obrazovky se stránkou pro úpravy pro cíl úložiště objektů BLOB](media/hpc-cache-edit-storage-blob.png)

Až budete hotovi, kliknutím na **OK** aktualizujte cíl úložiště, nebo klikněte na **Zrušit** , aby se změny zahodit.

## <a name="next-steps"></a>Další kroky

* Další informace o těchto možnostech najdete v tématu [Přidání cílů úložiště](hpc-cache-add-storage.md) .
* Další tipy k používání virtuálních cest najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .
