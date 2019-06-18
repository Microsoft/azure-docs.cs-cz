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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 5dbcf446a609adcd0f1902fcca2ac19ad87f17b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65779671"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Přístup k Azure Media Services rozhraní API pomocí Azure CLI
 
Chcete-li se připojit k rozhraní API služby Azure Media Services pomocí ověřování instančního objektu služby Azure AD, vaše aplikace potřebuje k vyžádání tokenu Azure AD, který má následující parametry:

* Koncový bod tenant Azure AD
* Identifikátor URI prostředku služby Media Services
* Identifikátor URI pro REST Media Services
* Hodnoty aplikace Azure AD: ID klienta a tajný kód klienta

Podrobné vysvětlení najdete v tématu [rozhraní API pro přístup k Media Services v3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Tento článek popisuje, jak používat rozhraní příkazového řádku Azure k vytvoření instančního objektu aplikace Azure AD a služby a získat hodnoty, které jsou potřeba pro přístup k prostředkům Azure Media Services.

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](create-account-cli-how-to.md).

Ujistěte se, že hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services mějte na paměti.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Další informace najdete v tématech

- [Rezervované pro média škálovací jednotky – rozhraní příkazového řádku](media-reserved-units-cli-how-to.md)
- [Vytvoření účtu Azure Media Services – rozhraní příkazového řádku](create-account-cli-how-to.md) 
- [Resetovat přihlašovací údaje účtu – rozhraní příkazového řádku](cli-reset-account-credentials.md)
- [Vytvoření prostředků – rozhraní příkazového řádku](cli-create-asset.md)
- [Nahrát soubor – rozhraní příkazového řádku](cli-upload-file-asset.md)
- [Vytvoření transformace – rozhraní příkazového řádku](cli-create-transform.md)
- [Kódování pomocí vlastní transformace – rozhraní příkazového řádku](custom-preset-cli-howto.md)
- [Vytvoření úlohy – CLI](cli-create-jobs.md)
- [Vytvoření EventGrid – rozhraní příkazového řádku](job-state-events-cli-how-to.md)
- [Publikování assetu – rozhraní příkazového řádku](cli-publish-asset.md)
- [Filtr – rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Další postup

Koncového bodu streamování ze kterého chcete Streamovat obsah, musí být ve spuštěném stavu. Následující příkaz rozhraní příkazového řádku spustí výchozí koncový bod streamování:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

