---
title: Průvodce odstraňováním potíží pro živé vysílání | Dokumenty společnosti Microsoft
description: Tento článek obsahuje návrhy, jak řešit problémy s živým streamováním služby Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: b00df5e8176aaad86be5cf3ae4e04c736f36cf5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885598"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Průvodce řešením potíží s živým streamováním  

Tento článek obsahuje návrhy, jak řešit některé problémy s živým přenosem.

## <a name="issues-related-to-on-premises-encoders"></a>Problémy související s místními kodéry
Tato část obsahuje návrhy, jak řešit problémy související s místními kodéry, které jsou nakonfigurovány tak, aby odesílaly jeden datový proud datového toku do kanálů AMS, které jsou povoleny pro živé kódování.

### <a name="problem-would-like-to-see-logs"></a>Problém: Chtěl by vidět protokoly
* **Potenciální problém**: Nelze najít protokoly kodéru, které by mohly pomoci při ladění problémy.
  
  * **Telestream Wirecast**: Protokoly obvykle najdete\{pod c:\Users username}\AppData\Roaming\Wirecast\ 
  * **Elemental Live**: Můžete najít odkazy na protokoly na portálu pro správu. Klikněte na **statistiky**, pak **protokoly**. Na stránce **Soubory protokolu** se zobrazí seznam protokolů pro všechny položky LiveEvent; vyberte tu, která odpovídá vaší aktuální relaci. 
  * **Flash Media Live Encoder**: Můžete najít **Log Directory ...** přechodem na záložku Protokol **kódování.**

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problém: Neexistuje žádná možnost pro odchozí progresivní datový proud
* **Potenciální problém**: Použitý kodér automaticky neodpojí. 
  
    **Kroky řešení potíží**: Vyhledejte možnost odstranění prokládání v rozhraní kodéru. Po zrušení prokládání zkontrolujte znovu progresivní nastavení výstupu. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problém: Vyzkoušeli několik nastavení výstupu kodéru a stále se nemohou připojit.
* **Potenciální problém**: Kanál kódování Azure nebyl správně resetován. 
  
    **Kroky řešení potíží**: Ujistěte se, že kodér již netlačí na AMS, zastavte a obnovte kanál. Po spuštění znovu zkuste připojit kodér s novým nastavením. Pokud se tím problém stále netýká, zkuste vytvořit nový kanál úplně, někdy se kanály mohou poškodit po několika neúspěšných pokusech.  
* **Potenciální problém**: Nastavení velikosti GOP nebo klíčový snímek nejsou optimální. 
  
    **Kroky řešení potíží**: Doporučená velikost gop nebo interval klíčových snímků je dvě sekundy. Některé kodéry počítají toto nastavení v počtu snímků, zatímco jiné používají sekundy. Příklad: Při vývodi 30 fps by velikost gop byla 60 snímků, což odpovídá 2 sekundám.  
* **Potenciální problém**: Uzavřené porty blokují datový proud. 
  
    **Kroky řešení potíží**: Při streamování přes RTMP zkontrolujte nastavení brány firewall nebo proxy, abyste potvrdili, že jsou otevřené odchozí porty 1935 a 1936. 

> [!NOTE]
> Pokud po provedení kroků řešení potíží stále nelze úspěšně streamovat, odešlete lístek podpory pomocí portálu Azure.
> 
> 

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

