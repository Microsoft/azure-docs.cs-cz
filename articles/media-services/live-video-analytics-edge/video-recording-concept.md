---
title: Záznam videa – Azure
description: V souvislosti se systémem pro správu videa pro kamery CCTV se záznam videa označuje procesu zachycení videa z fotoaparátů a jeho nahrávání pro zobrazení prostřednictvím mobilních a prohlížečových aplikací. Záznam videa je možné kategorizovat jako průběžné nahrávání videa a nahrávání videa založeného na událostech.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260943"
---
# <a name="video-recording"></a>Záznam videa

V souvislosti se systémem pro správu videa pro kamery CCTV se záznam videa označuje procesu zachycení videa z fotoaparátů a jeho nahrávání pro zobrazení prostřednictvím mobilních a prohlížečových aplikací. Záznam videa je možné kategorizovat jako průběžné nahrávání videa a nahrávání videa založeného na událostech. 

## <a name="continuous-video-recording"></a>Nepřetržité nahrávání videa  

Záznam nepřetržitého videa (CVR) označuje proces průběžného zaznamenávání veškerého videa zachyceného ze zdroje videa. CVR zajišťuje, že všechna videa budou k dispozici (vydaná [zásadami záznamu](#recording-policy)) k analýze a/nebo auditování v pozdějším časovém okamžiku.

## <a name="event-based-video-recording"></a>Nahrávání videa na základě událostí  

Nahrávání videa založeného na událostech (EVR) se týká procesu nahrávání videa aktivovaného událostí. Důvodem může být to, že v důsledku zpracování samotného videosignálu nebo z nezávislého zdroje (například ze senzoru dveří) by mohlo dojít k nějaké události. Nahrávání videa založené na událostech (EVR) může mít za následek úspory úložiště, ale vyžaduje další komponenty, které generují události a spouštějí záznam videa (na předem definované zásady). Jinými slovy, EVR vyžaduje další rozhodnutí týkající se událostí, které by měly aktivovat záznam videa a zásadu přidruženou k záznamu videa (označuje se také jako zásady záznamu). Příkladem zásad může být: nahrání videa na 2 minuty od 30 sekund před časem události Tyto události by mohly vzcházet kvůli zpracování videa na samotné kameře. Příklad: několik fotoaparátů podporuje generování signálu detekce pohybu, když se v předem nakonfigurované zóně zajímá pohyb v rámci v zobrazení kamery. Události je také možné vygenerovat zpracováním videa na jiném zařízení, které zachycuje video z kamery a analyzuje ho pomocí jednoduchých technik zpracování obrazu nebo propracovaných modelů strojového učení. 

## <a name="choosing-recording-modes"></a>Výběr režimů záznamu  

Volba, zda používat CVR nebo EVR, závisí na obchodních cílech. Live video Analytics na IoT Edge poskytuje možnosti platformy pro CVR i EVR. Další informace najdete v článcích scénářů [CVR](continuous-video-recording-concept.md) a [EVR](event-based-video-recording-concept.md) .

## <a name="recording-policy"></a>Zásady záznamu  

Zásady záznamu odkazují na zásady, které určují, jak dlouho se zaznamená doba zahájení nebo zastavení nahrávání (v případě EVR), doba nahrávání a odstranění záznamu. Zaznamenávání zásad vám umožní vyrovnávat náklady na úložiště pomocí podnikových požadavků. Zásady záznamu se mezi CVR a EVR liší. V případě CVR určuje zásada záznamu, kolik dnů se má uložit (například posledních 7 dnů). V případě EVR se zásady zápisu definují, kdy by měl záznam začínat a končit, a to i po dobu trvání videa. Další informace najdete v článcích scénářů [CVR](continuous-video-recording-concept.md) a [EVR](event-based-video-recording-concept.md) .

## <a name="next-steps"></a>Další kroky

* [Detekce pohybu, nahrávání videoklipů do Azure Media Services](detect-motion-record-video-clips-media-services-quickstart.md)
* [Detekce pohybu, nahrávání videoklipů do hraničních zařízení](detect-motion-record-video-clips-edge-devices-quickstart.md)

