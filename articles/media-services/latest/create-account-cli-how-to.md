---
title: Vytvoření účtu Azure Media Services pomocí Azure CLI – Azure | Dokumentace Microsoftu
description: Postup tohoto rychlého startu použijte k vytvoření účtu služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: b01958dc320ff6c81bb27c85ff4a5e508eabb01b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209940"
---
# <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

Pokud chcete začít, šifrování, kódování, analýza, Správa a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. V době, vytvořit účet Media Services, můžete také vytvořit přidruženého úložiště účtu (nebo použijte již existující).  

> [!NOTE]
> Účet Media Services a všechny přidružené úložiště účty musí být ve stejném předplatném Azure. Doporučujeme použít účty úložiště ve stejném umístění jako účet Media Services.

Tento článek popisuje postup vytvoření nového účtu Azure Media Services pomocí rozhraní příkazového řádku Azure.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu uveďte ID předplatného Azure, které chcete pro účet Media Services použít. Když přejdete na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zobrazí se seznam předplatných, ke kterým máte přístup.

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Další postup

[Streamování souboru](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

