---
title: Vytvoření účtu Azure Media Services pomocí webu Azure Portal
description: Tento kurz vás provede kroky pro vytvoření účtu služby Azure Media Services pomocí webu Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 5b05cd31a1747da0170556003e7a8534752e2fde
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137190"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Vytvoření účtu Media Services pomocí Azure Portal

Azure Portal poskytuje způsob, jak rychle vytvořit Azure Media Services účet. Účet můžete použít pro přístup ke službě Media Services, která vám umožní ukládat, šifrovat, kódovat, spravovat a streamovat mediální obsah v Azure.

V současné době můžete použít [Azure Portal](https://portal.azure.com/) k těmto akcím:

* Správa událostí Media Services V3 [Live](live-events-outputs-concept.md) 
* Zobrazit (Nespravovat) 3 [prostředky](assets-concept.md)V3 
* [Získejte informace o přístupu k rozhraním API](access-api-portal.md). 

Pro všechny ostatní úlohy správy (například [transformace a úlohy](transforms-jobs-concept.md) a [Ochrana obsahu](content-protection-overview.md)) použijte [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

V tomto článku se dozvíte, jak vytvořit účet Media Services pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Klikněte na **+ vytvořit prostředek** > **Media** > **Media Services**.
1. V části **Vytvoření účtu Media Services** zadejte požadované hodnoty.
    
    | Název | Popis |
    | ---|---|
    |**Název účtu**|Zadejte název nového účtu Media Services. Název účtu Media Services musí obsahovat jenom malá písmena a číslice, nesmí obsahovat mezery a musí mít délku 3 až 24 znaků.|
    |**Předplatné**|Pokud máte více než jedno předplatné, vyberte ho ze seznamu předplatných Azure, ke kterým máte přístup.|
    |**Skupina prostředků**|Vyberte nový nebo existující prostředek. Skupina prostředků je kolekce prostředků, které sdílejí životní cyklus, oprávnění a zásady. Další informace najdete [tady](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Umístění**|Vyberte zeměpisnou oblast, která se bude používat k ukládání médií a záznamů metadat pro váš Media Services účet. Tato oblast se bude používat ke zpracování a streamování vašeho média. V rozevíracím seznamu se vám zobrazí pouze ty oblasti Media Services, které jsou dostupné. |
    |**Účet úložiště**|Vyberte účet úložiště, který bude poskytovat blobové úložiště mediálního obsahu z vašeho účtu Media Services. Můžete vybrat existující účet úložiště ve stejné zeměpisné oblasti jako váš účet Media Services, nebo můžete vytvořit nový účet úložiště. Nový účet úložiště bude vytvořen ve stejné oblasti. Pro názvy účtů úložiště platí stejná pravidla jako pro názvy účtů Media Services.<br/><br/>Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a libovolný počet **sekundárních** účtů úložiště. Pomocí Azure Portal můžete přidat sekundární účty úložiště. Další informace najdete v tématu [účty Azure Storage s účty Azure Media Services](storage-account-concept.md).<br/><br/>Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli další latenci a nákladům na výchozí přenos dat.|
    
1. Zaškrtněte **Připnout na řídicí panel**, abyste viděli průběh nasazení účtu.
1. Klikněte na tlačítko **Vytvořit** dole na formuláři.

    Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod [dynamického balení](dynamic-packaging-overview.md) a [dynamického šifrování](content-protection-overview.md), musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **spuštěno** . 

## <a name="next-steps"></a>Další kroky

Pokud plánujete přístup k rozhraní API Media Services programově, přečtěte si téma [přístup k rozhraní api Azure Media Services pomocí ověřování Azure AD](access-api-portal.md).

## <a name="provide-feedback"></a>Poskytnout zpětnou vazbu
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

