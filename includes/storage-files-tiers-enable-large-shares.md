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
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597780"
---
Ve výchozím nastavení mohou standardní sdílené složky zahrnovat pouze až 5 TiB, i když limit sdílení lze zvýšit na 100 TiB. K tomu je potřeba, aby byla na úrovni účtu úložiště zapnutá funkce *Velká sdílená složka* . Účty úložiště úrovně Premium *(účty úložiště úložiště* ) nemají příznak funkce Velká sdílená složka, protože všechny soubory Premium jsou už povolené pro zřizování až pro celou 100 TIB kapacitu.

Můžete povolit jenom velké sdílené složky na místně redundantních nebo v neredundantních účtech úložiště zóny Standard. Jakmile povolíte příznak funkce pro velký sdílený soubor, nemůžete změnit úroveň redundance na geograficky redundantní nebo geograficky redundantní úložiště.

Pokud chcete povolit velké sdílené složky v existujícím účtu úložiště, přejděte do zobrazení **Konfigurace** v obsahu účtu úložiště a přepněte přepínač Rock Shared Shared File na Enabled:

![Snímek obrazovky s přepínačem Povolit u velkého sdíleného souboru sdílené složky v Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Pomocí rutiny [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShellu a příkazu [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) rozhraní příkazového řádku Azure CLI můžete taky povolit 100 TIB sdílené složky.

Další informace o tom, jak povolit velké sdílené složky pro nové účty úložiště, najdete v tématu [Vytvoření sdílené složky Azure](../articles/storage/files/storage-how-to-create-file-share.md).