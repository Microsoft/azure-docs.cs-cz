---
title: Vytvoření účtu Azure Media Services pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Postup tohoto rychlého startu použijte k vytvoření účtu služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 988b56569c2537f9f0c74b1bcc2bf5e2a9400a80
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378801"
---
# <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

Pokud chcete začít, šifrování, kódování, analýza, Správa a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. V době, vytvořit účet Media Services, můžete také vytvořit přidruženého úložiště účtu (nebo použijte již existující) ve stejné zeměpisné oblasti jako účet Media Services.

Tento článek popisuje postup vytvoření nového účtu Azure Media Services pomocí rozhraní příkazového řádku Azure.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [webu Azure portal](http://portal.azure.com) a spusťte **cloud Shell** provádět příkazy rozhraní příkazového řádku, jak je znázorněno v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu uveďte ID předplatného Azure, které chcete pro účet Media Services použít. Když přejdete na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zobrazí se seznam předplatných, ke kterým máte přístup.

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)
