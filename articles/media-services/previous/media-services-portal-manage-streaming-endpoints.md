---
title: Správa koncových bodů streamování pomocí webu Azure portal | Dokumentace Microsoftu
description: Toto téma ukazuje, jak spravovat koncové body streamování pomocí webu Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: cfowler
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: a023cb2ea1cf74c8029cc207c9b899631facb929
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018260"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Správa koncových bodů streamování pomocí webu Azure portal

Tento článek ukazuje, jak spravovat koncové body streamování pomocí webu Azure portal. 

>[!NOTE]
>Přečtěte si [přehled](media-services-streaming-endpoints-overview.md) článku. 

Informace o tom, jak škálovat koncový bod streamování najdete v tématu [to](media-services-portal-scale-streaming-endpoints.md) článku.

## <a name="start-managing-streaming-endpoints"></a>Začněte spravovat koncové body streamování 

Pokud chcete začít, správě koncových bodů streamování pro váš účet, postupujte takto.

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V **nastavení** okně vyberte **koncové body streamování**.
   
    ![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Nebudete dostávat faktury, pokud je váš koncový bod streamování ve spuštěném stavu.

## <a name="adddelete-a-streaming-endpoint"></a>Přidat/odstranit koncový bod streamování

>[!NOTE]
>Nelze odstranit výchozí koncový bod streamování.

K přidání/odstranění koncového bodu streamování pomocí webu Azure portal, postupujte takto:

1. Chcete-li přidat koncový bod streamování, klikněte na tlačítko **+ koncový bod** v horní části stránky. 

    Více koncových bodů streamování může být vhodné, pokud budete chtít mít různé CDN nebo CDN a přímý přístup.

2. Pokud chcete odstranit koncový bod streamování, stiskněte klávesu **odstranit** tlačítko.      
3. Klikněte na tlačítko **Start** tlačítko Spustit koncový bod streamování.
   
    ![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Konfigurace koncového bodu streamování
Koncový bod streamování, můžete nakonfigurovat následující vlastnosti:

* Řízení přístupu
* Řízení mezipaměti
* Různé zásady přístupu k webu

Podrobné informace o těchto vlastnostech najdete v tématu [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Když je povolené CDN, nebude možné získat přístup IP. Přístup IP je použitelná, pouze když nemáte k dispozici CDN.

Konfigurace koncového bodu streamování následujícím způsobem:

1. Vyberte koncový bod streamování, kterou chcete konfigurovat.
2. Klikněte na tlačítko **nastavení**.

Následuje stručný popis pole.

![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Zásady mezipaměti: slouží ke konfiguraci životnosti mezipaměti pro assety poskytované přes tento koncový bod streamování. Pokud není nastavena žádná hodnota, použije se výchozí hodnota. Výchozí hodnoty lze definovat také přímo ve službě Azure storage. Pokud je Azure CDN povolené pro koncový bod streamování, by neměla nastavte hodnotu zásady mezipaměti na menší než 600 sekund.  
2. Povolené IP adresy: používá se k určení IP adresy, které by bylo možné se připojit k publikované koncový bod streamování. Pokud není zadané žádné IP adresy, jakékoli IP adresy bude moct připojit. IP adresy můžete zadat jako jedna IP adresa (např ' 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například ' 10.0.0.1/22') nebo rozsah IP adres pomocí IP adresy a masky podsítě s tečkou (třeba 10.0.0.1 ' () 255.255.255.0) ").
3. Konfigurace ověřování hlaviček podpisů Akamai: používá se k určení, jak nakonfigurovat požadavek na ověření hlaviček podpis ze serverů Akamai. Vypršení platnosti je ve formátu UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Škálujte vaše Premium koncový bod streamování

Další informace najdete v [tomto](media-services-portal-scale-streaming-endpoints.md) článku.

## <a id="enable_cdn"></a>Povolení integrace Azure CDN

Když vytvoříte nový účet, integrace výchozí datový proud koncového bodu Azure CDN ve výchozím nastavení zapnutá.

Pokud budete později chtít zakázat nebo povolit CDN, koncový bod streamování musí být v **zastavena** stavu. Může to trvat až dvě hodiny, aby povoleno integrace Azure CDN a změn jako aktivní ve všech POP v síti CDN. Však můžete spustit koncový bod streamování a datový proud bez přerušení z koncového bodu streamování a po dokončení integrace datový proud doručen z CDN. Během zřizování období bude mít koncový bod streamování **od** stavu a můžete všimnout snížení výkonu.

Zavedené integraci CDN je povolena ve všech datových center Azure s výjimkou Číny a federální vlády oblastech.

Jakmile ho povolíte, **řízení přístupu**, ** vlastní název hostitele, a **ověřování podpisů Akamai** konfigurace získá zakázána.
 
> [!IMPORTANT]
> Integrace Azure Media Services s Azure CDN se implementuje na **Azure CDN od Verizonu** koncové body streamování standard. Koncové body streamování Premium lze konfigurovat pomocí všechny **Azure CDN cenové úrovně a poskytovatelé**. Další informace o funkcích Azure CDN, najdete v článku [přehled sítě CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Další aspekty

* Pokud je CDN povolené pro koncový bod streamování, nemůže klient vyžádá obsah přímo ze zdroje. Pokud chcete mít možnost otestovat váš obsah s nebo bez něj CDN, můžete vytvořit jiný koncový bod streamování, který se povolila se síť CDN.
* Streamování název hostitele koncového bodu zůstává po povolení CDN. Nemusíte dělat žádné změny pracovního postupu media services po povolení CDN. Pokud streamování název hostitele koncového bodu po strasbourg.streaming.mediaservices.windows.net, povoluje se CDN, je třeba použít přesně stejný název hostitele.
* Pro nové koncové body streamování můžete jednoduše tak, že vytvoříte nový koncový bod; povolit CDN pro stávající koncové body streamování musíte nejprve zastavit koncový bod a potom povolit nebo zakázat CDN.
* Koncový bod streamování standard se dá nakonfigurovat jenom pomocí **poskytovatel CDN společnosti Verizon úrovně Standard** pomocí portálu Azure classic. Můžete ale povolit jiných poskytovatelů Azure CDN pomocí rozhraní REST API.

## <a name="configure-cdn-profile"></a>Konfigurace profilu CDN

Profil CDN můžete nakonfigurovat tak, že vyberete **spravovat CDN** tlačítko v horní části.

![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Další postup
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

