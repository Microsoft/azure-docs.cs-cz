---
title: Přístup k rozhraní Azure Media Services API – Azure CLI | Microsoft Docs
description: Při přístupu k rozhraní Azure Media Services API postupujte podle pokynů v tomto článku.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896156"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Přístup k rozhraní API Azure Media Services pomocí Azure CLI
 
Pokud chcete pro připojení k Azure Media Services rozhraní API použít ověřování instančního objektu služby Azure AD, musí vaše aplikace požádat o token Azure AD s následujícími parametry:

* Koncový bod tenanta Azure AD
* Identifikátor URI Media Services prostředku
* Identifikátor URI prostředku pro Media Services REST
* Hodnoty aplikace Azure AD: ID klienta a tajný klíč klienta

Podrobné vysvětlení najdete v tématu [přístup k rozhraním api Media Services V3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

V tomto článku se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit aplikaci a instanční objekt služby Azure AD a získat hodnoty potřebné pro přístup k Azure Media Services prostředkům.

## <a name="prerequisites"></a>Předpoklady 

[Vytvoření účtu Media Services](create-account-cli-how-to.md).

Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Další informace najdete v tématech

- [Škálování rezervovaných jednotek médií – CLI](media-reserved-units-cli-how-to.md)
- [Vytvoření účtu Media Services – rozhraní příkazového řádku](create-account-cli-how-to.md) 
- [Resetování přihlašovacích údajů k účtu – CLI](cli-reset-account-credentials.md)
- [Vytváření assetů – CLI](cli-create-asset.md)
- [Nahrání souboru – CLI](cli-upload-file-asset.md)
- [Vytvoření transformací – rozhraní příkazového řádku](cli-create-transform.md)
- [Kódování pomocí vlastního transformačního rozhraní příkazového řádku](custom-preset-cli-howto.md)
- [Vytváření úloh – CLI](cli-create-jobs.md)
- [Vytvoření EventGrid-CLI](job-state-events-cli-how-to.md)
- [Publikování assetu – CLI](cli-publish-asset.md)
- [Filtr – rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Další kroky

Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu spuštěno. Následující příkaz rozhraní příkazového řádku spustí výchozí koncový bod streamování:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

