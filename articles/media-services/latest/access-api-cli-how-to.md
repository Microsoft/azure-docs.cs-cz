---
title: Přístup k rozhraní API Azure Media Services – Azure CLI | Dokumentace Microsoftu
description: Postupujte podle kroků tento návod pro přístup k rozhraní API služby Azure Media Services.
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
ms.openlocfilehash: e20cac5f1063589bdbfee0f384ac6af5a39811ed
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096787"
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

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](http://portal.azure.com) a spusťte **CloudShell**, abyste mohli provést příkazy CLI, jak můžete vidět v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
