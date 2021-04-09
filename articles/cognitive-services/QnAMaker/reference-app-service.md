---
title: Konfigurace služby – QnA Maker
description: Zjistěte, jak a kde nakonfigurovat prostředky.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: da46084c8c2616284c31ef155927e8dbcbd19e0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209355"
---
# <a name="service-configuration"></a>Konfigurace služeb

Každá verze QnA Maker používá jinou sadu prostředků Azure (služeb). Tento článek popisuje podporovaná přizpůsobení pro tyto služby. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

QnA Maker používá App Service k poskytnutí modulu runtime dotazu používaného [rozhraním API generateAnswer](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer).

Tato nastavení jsou k dispozici v Azure Portal pro App Service. Nastavení jsou dostupná, když vyberete **Nastavení** a pak na **Konfigurace**.

Jednotlivá nastavení můžete nastavit buď prostřednictvím seznamu nastavení aplikace, nebo můžete upravit několik nastavení tak, že vyberete **Rozšířené úpravy**.

|Prostředek|Nastavení|
|--|--|
|AzureSearchAdminKey|Kognitivní hledání – používá se pro párové úložiště a #1 pro QnA.|
|AzureSearchName|Kognitivní hledání – používá se pro párové úložiště a #1 pro QnA.|
|DefaultAnswer|Text odpovědi, pokud se nenajde žádná shoda|
|UserAppInsightsAppId|Protokol a telemetrie chatu|
|UserAppInsightsKey|Protokol a telemetrie chatu|
|UserAppInsightsName|Protokol a telemetrie chatu|
|QNAMAKER_EXTENSION_VERSION|Vždycky nastavte na _nejnovější_. Toto nastavení inicializuje rozšíření webu Qnamakerem v App Service.|

Až provedete změny, budete muset službu **restartovat** ze stránky **Přehled** Azure Portal.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

App Service přizpůsobení neplatí pro QnA Maker spravovaná (Preview).

---

## <a name="qna-maker-service"></a>Služba QnA Maker

Služba QnA Maker poskytuje konfiguraci pro tyto uživatele, aby spolupracovali na jediné službě QnA Maker a všech svých základech znalostní báze.

Naučte se, jak do služby [Přidat spolupracovníky](./reference-role-based-access-control.md) .

## <a name="change-azure-cognitive-search"></a>Změnit Kognitivní hledání Azure

Naučte [se, jak změnit službu kognitivní hledání](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) propojenou s vaší službou QnA maker.

## <a name="change-default-answer"></a>Změna výchozí odpovědi

Přečtěte si, [Jak změnit text vašich výchozích odpovědí](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Telemetrie

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Application Insights se používá pro monitorování telemetrie s QnA Maker GA. Neexistují žádná konfigurační nastavení specifická pro QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Naučte [se, jak přidat telemetrii do služby QnA maker Managed (Preview)](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>Plán služby App Service

# <a name="qnamaker-ga-stable-release"></a>[Qnamakerem GA (stabilní verze)](#tab/v1)

Plán App Service nemá žádná nastavení konfigurace specifická pro QnA Maker.

# <a name="qnamaker-managed-preview-release"></a>[Spravované Qnamakerem (verze Preview)](#tab/v2)

Plán App Service se nepoužívá se správou QnA Maker (Preview).

---

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [formátech](reference-document-format-guidelines.md) dokumentů a adres URL, které chcete importovat do znalostní báze.
