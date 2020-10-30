---
title: Správa koncových bodů streamování pomocí Azure Portal | Microsoft Docs
description: Tento článek ukazuje, jak spravovat koncové body streamování pomocí Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 496c5de8f9ead0773dfef7e65051344645ffa9de
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041975"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Správa koncových bodů streamování s využitím webu Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Tento článek ukazuje, jak použít Azure Portal ke správě koncových bodů streamování. 

>[!NOTE]
>Nezapomeňte si projít článek s [přehledem](media-services-streaming-endpoints-overview.md) . 

Informace o tom, jak škálovat koncový bod streamování, najdete v [tomto](media-services-portal-scale-streaming-endpoints.md) článku.

## <a name="start-managing-streaming-endpoints"></a>Zahájení správy koncových bodů streamování 

Pokud chcete začít spravovat koncové body streamování pro svůj účet, udělejte toto:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** vyberte **koncové body streamování** .
   
    ![Snímek obrazovky, který zobrazuje stránku Media Services se zvolenými možnostmi "koncové body streamování" v okně nastavení.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Fakturuje se vám jenom v případě, že je koncový bod streamování ve stavu spuštěno.

## <a name="adddelete-a-streaming-endpoint"></a>Přidat/odstranit koncový bod streamování

>[!NOTE]
>Výchozí koncový bod streamování nelze odstranit.

Pokud chcete přidat nebo odstranit koncový bod streamování pomocí Azure Portal, udělejte toto:

1. Chcete-li přidat koncový bod streamování, klikněte v horní části stránky na **koncový bod +** . 

    Pokud máte v plánu různé sítě CDN nebo CDN a přímý přístup, můžete chtít několik koncových bodů streamování.

2. Pokud chcete odstranit koncový bod streamování, stiskněte tlačítko **Odstranit** .      
3. Kliknutím na tlačítko **Spustit** spusťte koncový bod streamování.
   
    ![Snímek obrazovky s vybraným názvem "Endpoint" a zobrazeným podoknem "Podrobnosti o koncovém bodu streamování".](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Konfigurace koncového bodu streamování
Koncový bod streamování umožňuje konfigurovat následující vlastnosti:

* Řízení přístupu
* Řízení mezipaměti
* Zásady přístupu přes web

Podrobné informace o těchto vlastnostech naleznete v tématu [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Když je CDN povolené, nebudete mít přístup k IP adrese. Přístup k IP adresám se vztahuje jenom v případě, že nemáte síť CDN.

Koncový bod streamování můžete nakonfigurovat následujícím způsobem:

1. Vyberte koncový bod streamování, který chcete nakonfigurovat.
2. Klikněte na **Nastavení** .

Následuje stručný popis polí.

![Snímek obrazovky, který zobrazuje akci nastavení vybranou pro koncový bod streamování.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximální zásada mezipaměti: slouží ke konfiguraci životního cyklu mezipaměti pro prostředky poskytované prostřednictvím tohoto koncového bodu streamování. Pokud není nastavená žádná hodnota, použije se výchozí hodnota. Výchozí hodnoty lze také definovat přímo ve službě Azure Storage. Pokud je pro koncový bod streamování povolené Azure CDN, neměli byste nastavit hodnotu zásad mezipaměti na míň než 600 sekund.  
2. Povolené IP adresy: slouží k zadání IP adres, které se můžou připojit k publikovanému koncovému bodu streamování. Pokud nejsou zadané žádné IP adresy, může se připojit jakákoli IP adresa. IP adresy je možné zadat buď jako jednu IP adresu (například 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například 10.0.0.1/22), nebo rozsah IP adres pomocí IP adresy a masky podsítě s tečkami (například 10.0.0.1 (255.255.255.0)).
3. Konfigurace pro ověřování hlaviček podpisů Akamai: používá se k určení toho, jak se konfigurují žádosti o ověření hlaviček podpisů ze serverů Akamai. Vypršení platnosti je v UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Škálování koncového bodu streamování Premium

Další informace najdete v [tomto](media-services-portal-scale-streaming-endpoints.md) článku.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Povolit integraci Azure CDN

Když vytváříte nový účet, ve výchozím nastavení je výchozí nastavení služby streamování Azure CDN pro streamování povolené.

Pokud budete chtít síť CDN později zakázat nebo povolit, musí být koncový bod streamování ve stavu **Zastaveno** . Může trvat až dvě hodiny, než se aktivuje Integrace Azure CDN a změny, které se mají aktivní napříč všemi body POP CDN. Váš koncový bod streamování a streamování ale můžete spustit bez přerušení z koncového bodu streamování a po dokončení integrace se datový proud doručí ze sítě CDN. Během období zřizování bude koncový bod streamování ve **výchozím** stavu a může se stát, že dojde ke snížení výkonu.

Integrace CDN je povolená ve všech datových centrech Azure s výjimkou Číny a federálních vládních oblastí.

Jakmile je tato možnost povolená, **Access Control** , * * vlastní název hostitele a konfigurace **ověřování signatur Akamai** budou zakázané.
 
> [!IMPORTANT]
> Azure Media Services integrace s Azure CDN je implementovaná na **Azure CDN z Verizon** pro koncové body streamování Standard. Koncové body streamování Premium se dají nakonfigurovat pomocí všech **Azure CDN cenové úrovně a zprostředkovatelů** . Další informace o funkcích Azure CDN najdete v [přehledu CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Další aspekty

* Pokud je CDN pro koncový bod streamování povolené, klienti nemůžou požadovat obsah přímo od počátku. Pokud potřebujete mít možnost testovat obsah s CDN nebo bez něj, můžete vytvořit další koncový bod streamování, který není CDN povolený.
* Název hostitele koncového bodu streamování zůstane po povolení CDN stejný. Po povolení CDN nemusíte dělat žádné změny pracovního postupu pro Media Services. Pokud je například název hostitele koncového bodu streamování strasbourg.streaming.mediaservices.windows.net, po povolení CDN se použije přesný stejný název hostitele.
* Pro nové koncové body streamování můžete síť CDN povolit jednoduše tak, že vytvoříte nový koncový bod. u stávajících koncových bodů streamování musíte nejdřív ukončit koncový bod a pak povolit nebo zakázat CDN.
* Koncový bod streamování Standard se dá nakonfigurovat jenom pomocí **Verizon standardního poskytovatele CDN** pomocí portálu Azure Classic. Můžete ale povolit jiné poskytovatele Azure CDN pomocí rozhraní REST API.

## <a name="configure-cdn-profile"></a>Konfigurace profilu CDN

Profil CDN můžete nakonfigurovat tak, že v horní části vyberete tlačítko **Spravovat CDN** .

![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Další kroky
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
