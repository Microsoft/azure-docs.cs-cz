---
title: Aktualizace cílů úložiště mezipaměti HPC Azure
description: Úprava cílů úložiště mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 1/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0fa8be58db9754c652d6e1ee5349c950a1c19109
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513845"
---
# <a name="edit-storage-targets"></a>Úprava cílů úložiště

Cíl úložiště můžete odebrat nebo upravit ze stránky **cíle úložiště** mezipaměti.

> [!TIP]
> [Správa videa Azure HPC cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) ukazuje, jak upravit cíl úložiště.

## <a name="remove-a-storage-target"></a>Odebrání cíle úložiště

Pokud chcete cíl úložiště odebrat, vyberte ho v seznamu a klikněte na tlačítko **Odstranit** .

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
