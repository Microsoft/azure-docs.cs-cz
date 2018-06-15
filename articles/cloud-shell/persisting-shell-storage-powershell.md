---
title: Zachovat soubory v prostředí PowerShell v prostředí cloudu Azure (Preview) | Microsoft Docs
description: Návod, jak Azure Cloud prostředí potrvají soubory.
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154394"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Jak funguje prostředí PowerShell v prostředí cloudu Azure (Preview)
Prostředí PowerShell v prostředí cloudu (Preview) potrvá soubory prostřednictvím následující metody: 
* Připojení vaší zadanou sdílenou složku Azure jako `clouddrive` ve vaší `$Home` adresář pro přímé sdílení souborů interakce.

## <a name="list-clouddrive-azure-file-shares"></a>Seznam `clouddrive` sdílené složky Azure
`Get-CloudDrive` Příkaz načte informace o sdílené složky Azure file aktuálně připojené pomocí `clouddrive` v prostředí cloudu. <br>
![Get-CloudDrive systémem](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>Odpojení `clouddrive`
Sdílenou složku Azure připojeném do prostředí cloudu kdykoli můžete odpojit. Pokud byl odebrán sdílenou složkou Azure, budete vyzváni k vytvořit a připojit novou sdílenou složku Azure file na další relace.

`Dismount-CloudDrive` Příkaz odpojí sdílenou složku Azure z aktuální účet úložiště. Odpojení `clouddrive` ukončí aktuální relaci. Uživatel se vyzve k vytvořit a připojit novou sdílenou složku Azure file během další relace.
![Spuštění CloudDrive odpojení](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Další postup
[Rychlý start pro PowerShell](quickstart-powershell.md) <br>
[Další informace o Azure soubory](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Další informace o značkách úložiště](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>