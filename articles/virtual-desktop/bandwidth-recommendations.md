---
title: Doporučení šířky pásma pro vzdálené relace – Azure
description: Dostupná doporučení šířky pásma pro vzdálené relace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: helohr
ms.openlocfilehash: 1a88cfb2fcd5f0b607e7a2b43ae833789a584e34
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695308"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Doporučení šířky pásma pro vzdálené relace

Při použití vzdálené relace Windows má dostupná šířka pásma vaší sítě výrazně dopad na kvalitu prostředí. Různé aplikace a rozlišení zobrazení vyžadují různé konfigurace sítě, proto je důležité zajistit, aby vaše síť byla nakonfigurovaná tak, aby vyhovovala vašim potřebám. Tento článek popisuje doporučené šířky pásma pro jednotlivé úlohy.

>[!NOTE]
>Následující doporučení platí pro sítě s méně než 0,1% ztrátou.

## <a name="applications"></a>Aplikace

V následující tabulce jsou uvedeny minimální požadavky pro hladké uživatelské prostředí. 

|Úloha        |Ukázkové aplikace                                                                                           |Doporučená šířka pásma|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Pracovníci zpracovávající úkoly     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1,5 @ no__t – 0Mbps             |
|Pracovní proces Office   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer                                        |3 @ no__t – 0Mbps               |
|Pracovní proces znalostí|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer, Java                                  |5 @ no__t – 0Mbps               |
|Power Worker    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer, Java, CAD/VAČKa, ilustrace/publikování|15 @ no__t – 0Mbps              |

Mějte na paměti, že zátěže, která je umístěna v síti, závisí na četnosti výstupních snímků úloh aplikace a na rozlišení obrazovky. Pokud se zvýší kmitočet snímků nebo rozlišení obrazovky, bude potřeba taky vyvolávat požadavek na šířku pásma. Například světlá úloha s vysokým rozlišením vyžaduje více dostupné šířky pásma než lehké úlohy s pravidelným nebo nízkým rozlišením.

V jiných scénářích může být požadavky na šířku pásma změny v závislosti na tom, jak je používáte:

- Hlasové nebo video konference
- Komunikace v reálném čase
- Video streamování 4K

Nezapomeňte načíst testy těchto scénářů v nasazení pomocí nástrojů simulace, jako je VSI přihlášení. Pro lepší pochopení požadavků vaší sítě se liší velikost zatížení, spouštění zátěžových testů a testování běžných uživatelských scénářů ve vzdálených relacích. 

## <a name="display-resolutions"></a>Rozlišení zobrazení

Různá rozlišení zobrazení vyžadují různé dostupné šířky pásma. V následující tabulce jsou uvedeny šířky pásma, které doporučujeme pro hladké uživatelské prostředí při typických rozlišeních obrazovky a snímková frekvence 30 snímků za sekundu (FPS). Pamatujte na to, že scénáře zahrnující kmitočet snímků za 30 fps, jako je například čtení statického textu, vyžadují menší dostupnou šířku pásma. 

|Typická rozlišení zobrazení při 30 fps    |Doporučená šířka pásma|
|-----------------------------------------|---------------------|
|Přibližně 1024 × 768 px                      |1,5 MB/s             |
|Přibližně 1280 × 720 px                      |3 MB/s               |
|Přibližně 1920 × 1080 px                     |5 MB/s               |
|Přibližně 3840 × 2160 px (4K)                |15 MB/s              |
