---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81536474"
---
Ve výchozím nastavení mohou standardní sdílené složky zahrnovat pouze až 5 TiB, i když limit sdílení lze zvýšit na 100 TiB. K tomu je potřeba, aby byla na úrovni účtu úložiště zapnutá funkce *Velká sdílená složka* . Účty úložiště úrovně Premium *(účty úložiště úložiště* ) nemají příznak funkce Velká sdílená složka, protože všechny soubory Premium jsou už povolené pro zřizování až pro celou 100 TIB kapacitu.

Můžete povolit jenom velké sdílené složky na místně redundantních nebo v neredundantních účtech úložiště zóny Standard. Jakmile povolíte příznak funkce pro velký sdílený soubor, nemůžete změnit úroveň redundance na geograficky redundantní nebo geograficky redundantní úložiště.

Pokud chcete povolit velké sdílené složky v existujícím účtu úložiště, přejděte do zobrazení **Konfigurace** v obsahu účtu úložiště a přepněte přepínač Rock Shared Shared File na Enabled:

![Snímek obrazovky s přepínačem Povolit u velkého sdíleného souboru sdílené složky v Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Pomocí [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) rutiny PowerShellu a [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) příkazu rozhraní příkazového řádku Azure můžete taky povolit 100 TIB sdílené složky. Podrobné pokyny týkající se povolení sdílených složek ve velkých souborech najdete v tématu [povolení a vytváření velkých sdílených složek](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Další informace o tom, jak vytvořit sdílené složky pro nové účty úložiště, najdete v tématu [Vytvoření sdílené složky Azure](../articles/storage/files/storage-how-to-create-file-share.md).
