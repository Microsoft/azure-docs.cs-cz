---
title: Podpora Windows Virtual Desktop Linux – Azure
description: Stručný přehled linuxové podpory pro Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 967fd1fa182b7c8e581fd74cc287c5a6ba0e4038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127823"
---
# <a name="linux-support"></a>Podpora Linuxu

Pomocí sady Linux SDK pro virtuální plochu windows můžete vytvořit samostatný klient virtuální plochy windows. Můžete ji také použít k povolení podpory virtuální plochy systému Windows v klientské aplikaci. Tento rychlý průvodce vám vysvětlí, co je linuxová sada SDK a jak ji začít používat.

## <a name="what-is-the-linux-sdk"></a>Co je linuxová sada SDK?

Pomocí sad SDK API můžete načítat informační kanály prostředků, připojit se k relacím aplikací plochy nebo vzdálené aplikace a použít mnoho přesměrování, která naši klienti první strany podporují.

> [!NOTE]
> Sada SDK je v současné době ve vývoji. Tento dokument aktualizujeme s pokyny pro přístup k sdk, jakmile bude k dispozici.

### <a name="supported-linux-distributions"></a>Podporované distribuce Linuxu

Sada SDK je kompatibilní s většinou operačních systémů založených na Ubuntu 18.04 nebo novějším. Pokud máte jinou distribuci Linuxu, můžeme s vámi spolupracovat, abychom zjistili, jak nejlépe podporovat vaše potřeby.

### <a name="feature-support"></a>Podpora funkcí

Sada SDK podporuje více připojení k relacím plochy a vzdálených aplikací. Jsou podporována následující přesměrování:

| Přesměrování       | Podporuje se |
| :---------------- | :-------: |
| Klávesnice          | &#10004;  |
| Myš             | &#10004;  |
| Zvuk v          | &#10004;  |
| Výstup zvuku         | &#10004;  |
| Schránka (text)  | &#10004;  |
| Schránka (obrázek) | &#10004;  |
| Schránka (soubor)  | &#10004;  |
| Čipová karta         | &#10004;  |
| Jednotka/složka      | &#10004;  |

Sada SDK také podporuje více konfigurací monitoru, pokud jsou monitory vybrané pro relaci souvislé.

Tento dokument aktualizujeme, když přidáme podporu pro nové funkce a přesměrování. Chcete-li navrhnout nové funkce a další vylepšení, navštivte naši [stránku UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Začínáme s linuxovou sadou SDK

Než budete moci vyvinout klienta Linux pro Windows Virtual Desktop, musíte udělat následující věci:

1. Vytvořte a nasaďte prostředí Virtuální plochy Windows pro testování nebo produkční využití.
2. Otestujte dostupné klienty první strany, abyste se seznámili s uživatelským prostředím Windows Virtual Desktop.

## <a name="next-steps"></a>Další kroky

Prohlédněte si naši dokumentaci pro následující klienty:

- [Klient klasické pracovní plochy systému Windows](connect-windows-7-and-10.md)
- [Webový klient](connect-web.md)
- [Klient androida](connect-android.md)
- [klient macOS](connect-macos.md)
- [klient iOS](connect-ios.md)
