---
title: Příručka pro řešení potíží pro živé streamování | Microsoft Docs
description: Toto téma nabízí návrhy na řešení problémů živé streamování.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 84e3e9fc18671d7199eeaf638377a6681cf09fb4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33940920"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Průvodce řešením potíží s živým streamováním
Tento článek poskytuje návrhy na řešení problémů některé živé streamování.

## <a name="issues-related-to-on-premises-encoders"></a>Problémy související s místními kodéry
Tato část poskytuje návrhy na řešení potíží s problémy související s místními kodéry, které jsou nakonfigurované k odeslání datový proud s jednou přenosovou rychlostí do AMS kanály, které jsou povolené kódování v reálném čase.

### <a name="problem-would-like-to-see-logs"></a>Problém: Rádi viděli protokoly
* **Potenciální problém**: Nelze najít protokoly kodér, který vám může pomoci při ladění problémů.
  
  * **Telestream Wirecast**: obvykle najdete v části C:\Users protokoly\{uživatelské jméno} \AppData\Roaming\Wirecast\ 
  * **Elementární živé**: můžete najít na portálu pro správu obsahuje odkazy na protokoly. Klikněte na **statistiky**, pak **protokoly**. Na **soubory protokolu** stránky, uvidíte seznam protokolů pro všechny položky LiveEvent, vyberte příslušnou možnost, odpovídající aktuální relace. 
  * **Flash Live kodér médií**: můžete najít **adresář protokolu...**  přechodem na **kódování protokolu** kartě.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problém: Není žádná možnost pro výstup vytvořeného progresivní datového proudu
* **Potenciální problém**: používá kodér nepodporuje automaticky zrušit prokládání. 
  
    **Řešení potíží**: Vyhledejte odstraňování prokládání možnost v rámci rozhraní kodér. Po povolení algoritmy pro odstranění prokládání znovu zkontrolujte nastavení progresivní výstup. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problém: Pokusili několik nastavení výstupní kodér a stále se nedaří připojit.
* **Potenciální problém**: Azure kódování kanál nebyl správně resetován. 
  
    **Řešení potíží**: Ujistěte se, že kodér je už nabízení do AMS, zastavte a znovu nastavit kanál. Jednou spustit znovu, zkuste se připojit kodér s novým nastavením. Pokud tento problém pořád nevyřeší, zkuste vytvořit nový kanál zcela, někdy kanály se může stát poškozená po několika nezdařených pokusů o zadání.  
* **Potenciální problém**: nejsou optimální velikost The GOP nebo nastavení klíčových snímků. 
  
    **Řešení potíží**: GOP doporučená velikost nebo @keyframe, které určuje interval je dvou sekund. Některé kodéry vypočítat toto nastavení v počet snímků, jiné zase sekund. Příklad: při výstupu 30 fps, velikost GOP by být 60 rámce, který je ekvivalentní na 2 sekundy.  
* **Potenciální problém**: uzavřené porty blokují datového proudu. 
  
    **Řešení potíží**: při streamování prostřednictvím RTMP, zkontrolujte nastavení brány firewall nebo proxy, potvrďte, že jsou otevřené odchozí porty 1935 a 1936. 

> [!NOTE]
> Pokud po provedení kroků řešení potíží, které stále nemůžete použít datový proud úspěšně, odešlete lístek podpory pomocí portálu Azure.
> 
> 

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

