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
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 8374e4c49012a2c49de41001be0fdb30f9151332
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733500"
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
- [Vytvoření účtu Azure Media Services – rozhraní příkazového řádku](./scripts/cli-create-account.md) 
- [Resetovat přihlašovací údaje účtu – rozhraní příkazového řádku](./scripts/cli-reset-account-credentials.md)
- [Vytvoření prostředků – rozhraní příkazového řádku](./scripts/cli-create-asset.md)
- [Nahrát soubor – rozhraní příkazového řádku](./scripts/cli-upload-file-asset.md)
- [Vytvoření transformace – rozhraní příkazového řádku](./scripts/cli-create-transform.md)
- [Vytvoření úlohy – CLI](./scripts/cli-create-jobs.md)
- [Vytvoření EventGrid – rozhraní příkazového řádku](./scripts/cli-create-event-grid.md)
- [Publikování assetu – rozhraní příkazového řádku](./scripts/cli-publish-asset.md)
- [Filtr – rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Další postup

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
