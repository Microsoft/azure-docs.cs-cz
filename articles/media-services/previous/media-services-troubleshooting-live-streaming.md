---
title: Průvodce odstraňováním potíží pro živé streamování | Microsoft Docs
description: Tento článek obsahuje návrhy, jak řešit potíže s Azure Media Servicesm při živém streamování.
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
ms.openlocfilehash: 97ad3048fccc4f8ed6baf0f1d5304f1c8d81c3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269229"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Průvodce řešením potíží s živým streamováním

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Tento článek obsahuje návrhy na řešení problémů s živým streamování.

## <a name="issues-related-to-on-premises-encoders"></a>Problémy související s místními kodéry
V této části najdete návrhy, jak řešit problémy související s místními kodéry, které jsou nakonfigurované k odesílání datových proudů s jednou přenosovou rychlostí do kanálů AMS, které mají povolené kódování v reálném čase.

### <a name="problem-would-like-to-see-logs"></a>Problém: Chci zobrazit protokoly
* **Možný problém**: nepovedlo se najít protokoly kodéru, které by mohly pomáhat při ladění problémů.
  
  * **Wirecast proudu**: obvykle můžete najít protokoly v části C:\Users \{ username} \AppData\Roaming\Wirecast\ 
  * **Element-Live**: můžete najít odkazy na protokoly na portálu pro správu. Klikněte na **Statistika**a pak na **protokoly**. Na stránce **soubory protokolu** se zobrazí seznam protokolů pro všechny položky Livestream. Vyberte jednu vyhovující vaší aktuální relaci. 
  * **Live Encoder Flash Media**: **adresář protokolu** můžete najít tak, že přejdete na kartu **protokol kódování** .

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problém: neexistuje žádná možnost pro výstup progresivního datového proudu.
* **Potenciální problém**: používaný kodér neprovádí automatické odkládání. 
  
    **Postup řešení potíží**: vyhledejte možnost zrušení prokládání v rámci rozhraní kodéru. Po povolení rušení prokládání znovu ověřte nastavení progresivního výstupu. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problém: došlo k pokusu o několik nastavení výstupů kodéru a stále se nemůže připojit.
* **Možný problém**: kanál kódování Azure nebyl správně resetován. 
  
    **Postup řešení potíží**: Zajistěte, aby kodér přestal předávat do AMS, kanál zastaví a resetuje. Po opětovném spuštění zkuste kodér připojit k novému nastavení. Pokud se tím problém nevyřeší, zkuste vytvořit nový kanál úplně, někdy se kanály můžou poškodit po několika neúspěšných pokusech.  
* **Možný problém**: nastavení velikosti skupinu GOP nebo klíčových snímků nejsou optimální. 
  
    **Postup řešení potíží**: Doporučená velikost skupinu GOP nebo interval klíčového snímku jsou dvě sekundy. Některé kodéry počítají toto nastavení v počtu snímků, zatímco jiné používají sekundy. Příklad: při uvádění 30 snímků za sekundu by velikost skupinu GOP představovala 60 snímků, což je ekvivalent 2 sekund.  
* **Potenciální problém**: uzavřené porty blokují datový proud. 
  
    **Postup řešení potíží**: při streamování pomocí RTMP zkontrolujte nastavení brány firewall nebo proxy serveru a potvrďte, že jsou otevřené odchozí porty 1935 a 1936. 

> [!NOTE]
> Pokud se po provedení kroků pro řešení potíží stále nemůžete úspěšně streamovat, odešlete lístek podpory pomocí Azure Portal.
> 
> 

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

