---
title: Správa koncových bodů streamování pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak spravovat koncové body streamování pomocí portálu Azure.
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
ms.openlocfilehash: 57ec22cb02512577f8737718cae9175403fb5603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900886"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Správa koncových bodů streamování s využitím webu Azure Portal 

Tento článek ukazuje, jak používat portál Azure ke správě koncových bodů streamování. 

>[!NOTE]
>Nezapomeňte si prohlédnout článek s [přehledem.](media-services-streaming-endpoints-overview.md) 

Informace o tom, jak škálovat koncový bod streamování, naleznete v [tomto](media-services-portal-scale-streaming-endpoints.md) článku.

## <a name="start-managing-streaming-endpoints"></a>Zahájení správy koncových bodů streamování 

Chcete-li začít spravovat koncové body streamování pro váš účet, postupujte takto.

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** vyberte **Položky koncových bodů streamování**.
   
    ![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Bude se vám účtovat pouze v případě, že je koncový bod streamování ve spuštěném stavu.

## <a name="adddelete-a-streaming-endpoint"></a>Přidání nebo odstranění koncového bodu streamování

>[!NOTE]
>Výchozí koncový bod streamování nelze odstranit.

Pokud chcete přidat nebo odstranit koncový bod streamování pomocí webu Azure Portal, postupujte takto:

1. Chcete-li přidat koncový bod streamování, klikněte na **+ Koncový bod** v horní části stránky. 

    Můžete chtít více koncových bodů streamování, pokud máte v plánu mít různé cdn nebo CDN a přímý přístup.

2. Pokud chcete odstranit koncový bod streamování, stiskněte tlačítko **Odstranit.**      
3. Kliknutím na tlačítko **Start** spusťte koncový bod streamování.
   
    ![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Konfigurace koncového bodu streamování
Koncový datový proud umožňuje konfigurovat následující vlastnosti:

* Řízení přístupu
* Řízení mezipaměti
* Zásady přístupu napříč weby

Podrobné informace o těchto vlastnostech naleznete v [tématu StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Je-li povolena síť CDN, nelze získat přístup k přístupu k protokolu IP. Přístup k IP adresě je použitelný pouze v případě, že nemáte CDN.

Koncový bod streamování můžete nakonfigurovat následujícím způsobem:

1. Vyberte koncový bod streamování, který chcete konfigurovat.
2. Klikněte na příkaz **Nastavení**.

Následuje stručný popis polí.

![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximální zásady mezipaměti: slouží ke konfiguraci životnosti mezipaměti pro datové zdroje obsluhované prostřednictvím tohoto koncového bodu streamování. Pokud není nastavena žádná hodnota, použije se výchozí nastavení. Výchozí hodnoty lze definovat také přímo v úložišti Azure. Pokud Azure CDN je povolena pro koncový bod streamování, neměli byste nastavit hodnotu zásad mezipaměti na méně než 600 sekund.  
2. Povolené IP adresy: používá se k určení IP adres, které by bylo povoleno připojení k publikovanému koncovému bodu streamování. Pokud nejsou zadány žádné adresy IP, bude se moci připojit jakákoli adresa IP. IP adresy lze zadat buď jako jednu IP adresu (například "10.0.0.1"), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například "10.0.0.1/22"), nebo jako rozsah IP adres pomocí IP adresy a tečkované desetinné masky podsítě (například "10.0.0.1(255.255.255.0)").
3. Konfigurace pro ověřování záhlaví podpisu Akamai: slouží k určení způsobu konfigurace požadavku na ověření záhlaví podpisu ze serverů Akamai. Vypršení platnosti je v UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Škálování koncového bodu streamování Premium

Další informace naleznete v [tomto](media-services-portal-scale-streaming-endpoints.md) článku.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Povolení integrace Azure CDN

Při vytvoření nového účtu je ve výchozím nastavení povolena výchozí integrace CDN koncového bodu streamování Azure.

Pokud později chcete zakázat nebo povolit CDN, koncový bod streamování musí být v **zastaveném** stavu. Může trvat až dvě hodiny, než se integrace Azure CDN aktivuje a změny budou aktivní ve všech přístupových psách CDN. Můžete však spustit koncový bod streamování a datový proud bez přerušení z koncového bodu streamování a po dokončení integrace je datový proud doručován z CDN. Během období zřizování koncový bod streamování bude ve **stavu spuštění** a můžete sledovat snížený výkon.

Integrace CDN je povolená ve všech datových centrech Azure s výjimkou oblastí Číny a federální vlády.

Jakmile je povolena, **řízení přístupu**, ** Vlastní název hostitele a **akamai podpis ověřování** konfigurace se deaktivuje.
 
> [!IMPORTANT]
> Integrace Azure Media Services s Azure CDN se implementuje na **Azure CDN od Verizonu** pro standardní koncové body streamování. Koncové body streamování Premium lze nakonfigurovat pomocí všech **cenových úrovní Azure CDN a poskytovatelů**. Další informace o funkcích Azure CDN najdete v přehledu [CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Další aspekty

* Pokud je pro koncový bod streamování povolena síť CDN, klienti nemohou požadovat obsah přímo z počátku. Pokud potřebujete možnost otestovat obsah s nebo bez CDN, můžete vytvořit jiný koncový bod streamování, který není povolen CDN.
* Název hostitele koncového bodu streamování zůstane po povolení sítě CDN stejný. Po povolení sítě CDN není nutné provádět žádné změny pracovního postupu mediálních služeb. Například pokud je název hostitele koncového bodu streamování strasbourg.streaming.mediaservices.windows.net po povolení CDN, použije se přesně stejný název hostitele.
* U nových koncových bodů streamování můžete povolit CDN jednoduše vytvořením nového koncového bodu; pro existující koncové body streamování je třeba nejprve zastavit koncový bod a potom povolit nebo zakázat CDN.
* Standardní koncový bod streamování lze nakonfigurovat jenom pomocí **verizonstandardu poskytovatele CDN** pomocí klasického portálu Azure. Můžete však povolit další zprostředkovatele Azure CDN pomocí rest api.

## <a name="configure-cdn-profile"></a>Konfigurace profilu CDN

Profil CDN můžete nakonfigurovat tak, že shora vyberete tlačítko **Spravovat CDN.**

![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Další kroky
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

