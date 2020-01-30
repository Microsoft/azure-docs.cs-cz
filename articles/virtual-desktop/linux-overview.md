---
title: Linux Support virtuálních počítačů s Windows – Azure
description: Stručné informace o podpoře pro Linux pro virtuální počítače s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
ms.openlocfilehash: f7d62487605c26a68e7c71b6d95d7814da0a07c9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776388"
---
# <a name="linux-support"></a>Podpora Linuxu

Můžete použít sadu Linux SDK pro virtuální plochu Windows k sestavení samostatného klienta virtuální plochy Windows. Můžete ji také použít k povolení podpory virtuálních počítačů s Windows v klientské aplikaci. V této stručné příručce se dozvíte, co je sada Linux SDK a jak ji začít používat.

## <a name="what-is-the-linux-sdk"></a>Co je sada Linux SDK?

Pomocí rozhraní API sady SDK můžete načíst kanály prostředků, připojit se k relaci počítače nebo vzdálené aplikace a použít spoustu přesměrování, která naši klienti podporují.

### <a name="supported-linux-distributions"></a>Podporované linuxové distribuce

Sada SDK je kompatibilní s největším operačním systémem, který je založený na Ubuntu 18,04 nebo novějším. Pokud máte jinou distribuci pro Linux, můžeme s vámi spolupracovat a zjistit, jak co nejlépe vyhovuje vašim potřebám.

### <a name="feature-support"></a>Podpora funkce

Sada SDK podporuje více připojení k relacím na plochu a vzdálené aplikace. Jsou podporovány následující přesměrování:

| Přesměrování       | Podporováno |
| :---------------- | :-------: |
| Klávesnice          | &#10004;  |
| Stisknut             | &#10004;  |
| Zvuk v          | &#10004;  |
| Zvukový výstup         | &#10004;  |
| Schránka (text)  | &#10004;  |
| Schránka (obrázek) | &#10004;  |
| Schránka (soubor)  | &#10004;  |
| Pomocí         | &#10004;  |
| Jednotka nebo složka      | &#10004;  |

Sada SDK také podporuje více konfigurací zobrazení monitorování, pokud jsou monitory, které vyberete pro vaši relaci, souvislé.

Tento dokument aktualizujeme, protože přidáváme podporu pro nové funkce a přesměrování. Pokud chcete navrhnout nové funkce a další vylepšení, navštivte [stránku UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Začínáme se sadou SDK pro Linux

Než budete moct vyvíjet klienta pro Linux pro virtuální počítače s Windows, musíte provést následující akce:

1. Sestavte a nasaďte prostředí virtuálních ploch Windows pro účely testování nebo produkčního použití.
2. Otestujte dostupné klienty první strany a seznamte se s uživatelským prostředím pro virtuální počítače s Windows.

## <a name="next-steps"></a>Další kroky

Můžete požádat o přístup k sadě Linux SDK na našem [fóru pro technickou komunitu](https://go.microsoft.com/fwlink/?linkid=2116541).

Projděte si naši dokumentaci pro následující klienty:

- [Klient pro stolní počítače Windows](connect-windows-7-and-10.md)
- [Webový klient](connect-web.md)
- [Klient pro Android](connect-android.md)
- [klient macOS](connect-macos.md)
- [klient iOS](connect-ios.md)
