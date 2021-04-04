---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99569490"
---
Ve výchozím nastavení mohou standardní sdílené složky zahrnovat pouze až 5 TiB, ale můžete zvýšit limit počtu sdílení na 100 TiB. Pokud chcete zvýšit limit vaší sdílené složky, povolte na svém účtu úložiště **velkou sdílenou složku** . Účty úložiště úrovně Premium *(účty úložiště úložiště* ) nemají příznak funkce Velká sdílená složka, protože všechny soubory Premium jsou už povolené pro zřizování až pro celou 100 TIB kapacitu.

Můžete povolit jenom velké sdílené složky na místně redundantních nebo v neredundantních účtech úložiště zóny Standard. Jakmile povolíte příznak funkce pro velký sdílený soubor, nemůžete změnit úroveň redundance na geograficky redundantní nebo geograficky redundantní úložiště.

Pokud chcete povolit velké sdílené složky v existujícím účtu úložiště, přejděte v obsahu účtu úložiště na **sdílené složky** .
V tomto okně vyberte **sdílet kapacitu**, změňte kapacitu sdílené složky na **100 TIB** a vyberte **Uložit**.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Snímek obrazovky s nastavením možnosti Povolit velkou sdílenou složku pro sdílení souborů v Azure Portal." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Pomocí [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) rutiny prostředí PowerShell a [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) příkazu rozhraní příkazového řádku Azure můžete také povolit sdílené složky 100-TIB. Podrobné pokyny týkající se povolení sdílených složek ve velkých souborech najdete v tématu [povolení a vytváření velkých sdílených složek](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Další informace o tom, jak vytvořit sdílené složky pro nové účty úložiště, najdete v tématu [Vytvoření sdílené složky Azure](../articles/storage/files/storage-how-to-create-file-share.md).