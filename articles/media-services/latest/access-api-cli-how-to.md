---
title: Přístup k rozhraní API Azure Media Services – Azure CLI | Dokumentace Microsoftu
description: Postupujte podle kroků tento návod pro přístup k rozhraní API služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 5d7c338dc9d5028552d9b33802982ffda97a56f0
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389838"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Přístup k Azure Media Services rozhraní API pomocí Azure CLI
 
Ověřování instančního objektu služby Azure AD by měl používat pro připojení k rozhraní API služby Azure Media Services. Vaše aplikace potřebuje k vyžádání tokenu Azure AD, který má následující parametry:

* Koncový bod tenant Azure AD
* Identifikátor URI prostředku služby Media Services
* Identifikátor URI pro REST Media Services
* Hodnoty aplikace Azure AD: ID klienta a tajný kód klienta

Tento článek popisuje, jak používat rozhraní příkazového řádku Azure k vytvoření instančního objektu aplikace Azure AD a služby a získat hodnoty, které jsou potřeba pro přístup k prostředkům Azure Media Services.

## <a name="prerequisites"></a>Požadavky 

Vytvořit si nový účet služby Azure Media Services podle popisu [v tomto rychlém startu](create-account-cli-quickstart.md)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [webu Azure portal](http://portal.azure.com) a spusťte **cloud Shell** provádět příkazy rozhraní příkazového řádku, jak je znázorněno v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
