---
title: Přístup k rozhraní API služby Azure Media - 2.0 rozhraní příkazového řádku | Microsoft Docs
description: Postupujte podle tohoto postupy pro přístup k rozhraní API služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 9295c3f9dfabc8ef7749758e926df443843720a1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639771"
---
# <a name="access-azure-media-services-api-with-cli-20"></a>Přístup k Azure Media Services rozhraní API pomocí rozhraní příkazového řádku 2.0
 
Objekt zabezpečení ověřování služby Azure AD má použít pro přístup k rozhraní API služby Azure Media Services. Aplikace musí požádat o tokenu Azure AD, který má následující parametry:

* Koncový bod tenant Azure AD
* Identifikátor URI prostředku Media Services
* Identifikátor URI pro REST Media Services
* Hodnoty aplikace Azure AD: ID klienta a tajný klíč klienta

Tento článek ukazuje, jak vytvořit objekt služby Azure AD aplikace a služby a získat hodnoty, které jsou potřebné pro přístup k prostředkům Azure Media Services pomocí rozhraní příkazového řádku 2.0.

## <a name="prerequisites"></a>Požadavky 

Vytvořit nový účet Azure Media Services, jak je popsáno v [tento rychlý Start](create-account-cli-quickstart.md).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](http://portal.azure.com) a spusťte **CloudShell**, abyste mohli provést příkazy CLI, jak můžete vidět v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Další informace najdete v tématech

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
