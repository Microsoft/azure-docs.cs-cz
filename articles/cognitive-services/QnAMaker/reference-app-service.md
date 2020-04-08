---
title: Konfigurace služby - QnA Maker
description: Zjistěte, jak a kde konfigurovat prostředky.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804256"
---
# <a name="service-configuration"></a>Konfigurace služeb

QnA Maker používá několik prostředků Azure (služeb), včetně kognitivního vyhledávání, služby App Service, plánu služby App Service a přehledů aplikací.

Všechna nastavení těchto nastavení podporovaná qnA makerem jsou uvedena níže.

## <a name="app-service"></a>App Service

QnA Maker používá službu App Service k poskytování za běhu dotazu používaného [rozhraním GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Tato nastavení jsou k dispozici na webu Azure Portal pro službu App Service. Nastavení jsou k dispozici výběrem **nastavení**a potom **konfigurace**.

Jednotlivá nastavení můžete nastavit buď prostřednictvím seznamu Nastavení aplikace, nebo můžete změnit několik nastavení výběrem **možnosti Upřesnit úpravy**.

|Prostředek|Nastavení|
|--|--|
|Klíč AzureSearchAdmin|Kognitivní vyhledávání - používá se pro ukládání dvojice QnA a Ranker #1|
|AzureSearchName|Kognitivní vyhledávání - používá se pro ukládání dvojice QnA a Ranker #1|
|Výchozí odpověď|Text odpovědi, pokud není nalezena žádná shoda|
|UživatelAppInsightsId|Protokol chatu a telemetrie|
|Klíč UserAppInsights|Protokol chatu a telemetrie|
|Jméno UserAppInsightsName|Protokol chatu a telemetrie|

Přečtěte [si, jak do služby přidat změnu služby Kognitivní vyhledávání.](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)

Po dokončení provádění změn je potřeba **službu restartovat** na stránce **Přehled** na webu Azure Portal.

## <a name="qna-maker-service"></a>Služba QnA Maker

Služba QnA Maker poskytuje konfiguraci pro následující uživatele ke spolupráci na jedné službě QnA Maker a všech jejích znalostních bází.

Přečtěte si, jak ke službě [přidat spolupracovníky.](./how-to/collaborate-knowledge-base.md)

## <a name="application-insights"></a>Application Insights

Application Insights nemá žádné nastavení konfigurace specifické pro QnA Maker.

## <a name="app-service-plan"></a>Plán služby App Service

Plán služby App Service nemá žádné nastavení konfigurace specifické pro QnA Maker.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [formátech](reference-document-format-guidelines.md) dokumentů a adres URL, které chcete importovat do znalostní báze.