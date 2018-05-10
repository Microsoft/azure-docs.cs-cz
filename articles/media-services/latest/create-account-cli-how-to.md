---
title: Vytvoření účtu Azure Media Services pomocí rozhraní příkazového řádku 2.0 | Microsoft Docs
description: Postupujte podle tento rychlý start k vytvoření účtu Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: a9660ac61bab9f8b9eb9563aab4cc584786b25ae
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

Pokud chcete spustit šifrování, kódování, analýza, správy a vysílání datového proudu mediální obsah v Azure, musíte vytvořit účet Media Services. V okamžiku vytvoření účtu Media Services si současně vytvoříte i přidružený účet úložiště (nebo použijete existující) ve stejné zeměpisné oblasti jako účet Media Services.

Toto téma popisuje kroky pro vytvoření nového účtu Azure Media Services pomocí rozhraní příkazového řádku 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portál Azure](http://portal.azure.com) a spusťte **CloudShell** provést příkazy rozhraní příkazového řádku, jak je znázorněno v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Nastavte předplatné Azure

V následujícím příkazu zadejte ID předplatného Azure, který chcete použít pro účet Media Services. Zobrazí se seznam odběry, které mají přístup k přechodem na [odběry](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Datový proud souboru](stream-files-dotnet-quickstart.md)
