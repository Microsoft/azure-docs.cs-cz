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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536474"
---
Ve výchozím nastavení mohou standardní sdílené složky span pouze 5 TiB, i když limit sdílení lze zvýšit na 100 TiB. Chcete-li to provést, musí být na úrovni účtu úložiště povolena funkce *sdílení velkých souborů.* Účty úložiště Premium (Účty úložiště*úložiště úložiště filestorage)* nemají příznak funkce velké sdílené složky, protože všechny sdílené složky premium jsou již povoleny pro zřizování až do plné kapacity 100 TiB.

Velké sdílené složky můžete povolit pouze na místně redundantních nebo zónově redundantních účtech standardního úložiště. Jakmile aktivujete příznak funkce velké sdílené složky, nemůžete změnit úroveň redundance na geograficky redundantní úložiště nebo úložiště s geografickou zónou.

Chcete-li povolit velké sdílené složky na existujícím účtu úložiště, přejděte do zobrazení **Konfigurace** v obsahu účtu úložiště a přepněte přepínač rocker sdílené velké soubory na povolený:

![Snímek obrazovky s přepínačem povolit přechod pro sdílení velkých souborů na webu Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Můžete také povolit 100 sdílených [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) složek TiB [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) prostřednictvím rutiny prostředí PowerShell a příkazu Azure CLI. Podrobné pokyny k povolení sdílených složek velkých souborů naleznete v tématu [povolení a vytvoření velkých sdílených složek souborů](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Další informace o tom, jak vytvářet sdílené složky na nových účtech úložiště, najdete [v tématu vytvoření sdílené složky Azure](../articles/storage/files/storage-how-to-create-file-share.md).
