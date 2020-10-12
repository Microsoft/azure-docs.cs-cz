---
title: Konfigurace služby – QnA Maker
description: Zjistěte, jak a kde nakonfigurovat prostředky.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 1d54b912d2177a3ccd0cf34d57fc0358af653199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776710"
---
# <a name="service-configuration"></a>Konfigurace služeb

QnA Maker využívá několik prostředků Azure (služby) včetně Kognitivní hledání, App Service, App Service plánů a Application Insights.

Všechna přizpůsobení pro tato nastavení podporovaná QnA Maker jsou uvedená níže.

## <a name="app-service"></a>App Service

QnA Maker používá App Service k poskytnutí modulu runtime dotazu používaného [rozhraním API generateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Tato nastavení jsou k dispozici v Azure Portal pro App Service. Nastavení jsou dostupná, když vyberete **Nastavení**a pak na **Konfigurace**.

Jednotlivá nastavení můžete nastavit buď prostřednictvím seznamu nastavení aplikace, nebo můžete upravit několik nastavení tak, že vyberete **Rozšířené úpravy**.

|Prostředek|Nastavení|
|--|--|
|AzureSearchAdminKey|Kognitivní hledání – používá se pro párové úložiště a #1 pro QnA.|
|AzureSearchName|Kognitivní hledání – používá se pro párové úložiště a #1 pro QnA.|
|DefaultAnswer|Text odpovědi, pokud se nenajde žádná shoda|
|UserAppInsightsAppId|Protokol a telemetrie chatu|
|UserAppInsightsKey|Protokol a telemetrie chatu|
|UserAppInsightsName|Protokol a telemetrie chatu|

Naučte [se, jak přidat službu kognitivní hledání](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) do vaší služby.

Až provedete změny, budete muset službu **restartovat** ze stránky **Přehled** Azure Portal.

## <a name="qna-maker-service"></a>Služba QnA Maker

Služba QnA Maker poskytuje konfiguraci pro tyto uživatele, aby spolupracovali na jediné službě QnA Maker a všech svých základech znalostní báze.

Naučte se, jak do služby [Přidat spolupracovníky](./how-to/collaborate-knowledge-base.md) .

## <a name="application-insights"></a>Application Insights

Application Insights nemá žádná konfigurační nastavení specifická pro QnA Maker.

## <a name="app-service-plan"></a>Plán služby App Service

Plán App Service nemá žádná nastavení konfigurace specifická pro QnA Maker.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [formátech](reference-document-format-guidelines.md) dokumentů a adres URL, které chcete importovat do znalostní báze.