---
title: Připojení k virtuální ploše Windows z Androidu – Azure
description: Jak se připojit k virtuální ploše systému Windows pomocí klienta Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295366"
---
# <a name="connect-with-the-android-client"></a>Práce s klientem Android

> Platí pro: Android 4.1 a novější, Chromebooky s ChromeOS 53 a novějším.

>[!NOTE]
> Možnost přístupu k prostředkům virtuální plochy systému Windows z klienta Android je v současné době k dispozici ve verzi Preview.

K prostředkům Windows Virtual Desktop můžete přistupovat ze zařízení Android pomocí našeho klienta ke stažení. Klient android můžete také použít na zařízeních Chromebook, která podporují Obchod Google Play. Tato příručka vám řekne, jak nastavit klienta Android.

## <a name="install-the-android-client"></a>Instalace klienta Android

Chcete-li začít, [stáhněte](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) a nainstalujte klienta do zařízení Android.

## <a name="subscribe-to-a-feed"></a>Přihlásit se k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu poskytnutého správcem a získejte seznam spravovaných prostředků, ke kterým máte přístup na svém zařízení Android.

Přihlášení k odběru informačního kanálu:

1. V Centru připojení **+** klepněte na tlačítko a potom klepněte na **Kanál vzdáleného zdrojů**.
2. Do pole URL zdroje zadejte adresu URL informačního **kanálu.** Adresa URL zdroje může být adresa URL nebo e-mailová adresa.
   - Pokud používáte adresu URL, použijte adresu, <https://rdweb.wvd.microsoft.com>kterou vám dal správce, obvykle .
   - Chcete-li používat e-mail, zadejte svou e-mailovou adresu. Pokud správce server tímto způsobem nakonfiguroval, bude klient hledat adresu URL přidruženou k vaší e-mailové adrese.
3. Klepněte na **DALŠÍ**.
4. Po zobrazení výzvy zadejte pověření.
   - V **části Uživatelské jméno**zadejte uživatelské jméno s oprávněním k přístupu k prostředkům.
   - V **části Heslo**zadejte heslo přidružené k uživatelskému jménu.
   - Pokud správce nakonfiguroval ověřování tímto způsobem, můžete být také vyzváni k zadání dalších faktorů.

Po přihlášení k odběru by centrum připojení mělo zobrazit vzdálené prostředky.

Jakmile se obsah zdroje přihlásí k odběru, bude se obsah zdroje pravidelně automaticky aktualizovat. Prostředky mohou být přidány, změněny nebo odebrány na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat klienta Android, najdete v části [Začínáme s klientem Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
