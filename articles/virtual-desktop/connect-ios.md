---
title: Připojení k virtuální ploše Windows z iOS – Azure
description: Jak se připojit k virtuální ploše windows pomocí klienta iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128249"
---
# <a name="connect-with-the-ios-client"></a>Připojení s klientem iOS

> Platí pro: iOS 13.0 nebo novější. Kompatibilní s iPhonem, iPadem a iPodem touch.

K prostředkům Windows Virtual Desktop můžete přistupovat ze svého iOS zařízení s naším klientem ke stažení. Tato příručka vám řekne, jak nastavit klienta iOS.

## <a name="install-the-ios-client"></a>Instalace klienta iOS

Chcete-li začít, [stáhněte](https://aka.ms/rdios) a nainstalujte klienta do iOS zařízení.

## <a name="subscribe-to-a-feed"></a>Přihlásit se k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu poskytnutého správcem a získejte seznam spravovaných prostředků, ke kterým máte přístup na iOS zařízení.

Přihlášení k odběru informačního kanálu:

1. V Centru připojení **+** klepněte na tlačítko A potom klepněte na **Přidat pracovní prostor**.
2. Do pole URL zdroje zadejte adresu URL informačního **kanálu.** Adresa URL zdroje může být adresa URL nebo e-mailová adresa.
   - Pokud používáte adresu URL, použijte adresu, kterou vám dal správce. Za normálních <https://rdweb.wvd.microsoft.com>okolností je adresa URL .
   - Chcete-li používat e-mail, zadejte svou e-mailovou adresu. To říká klientovi, aby vyhledal adresu URL přidruženou k vaší e-mailové adrese, pokud váš správce nakonfiguroval server tímto způsobem.
3. Klepněte na **Další**.
4. Po zobrazení výzvy zadejte pověření.
   - V **části Uživatelské jméno**zadejte uživatelské jméno s oprávněním k přístupu k prostředkům.
   - V **části Heslo**zadejte heslo přidružené k uživatelskému jménu.
   - Pokud správce nakonfiguroval ověřování tímto způsobem, můžete být také vyzváni k zadání dalších faktorů.
5. Klepněte na **Uložit**.

Poté by centrum připojení mělo zobrazit vzdálené prostředky.

Jakmile se obsah zdroje přihlásí k odběru, bude se obsah zdroje pravidelně automaticky aktualizovat. Prostředky mohou být přidány, změněny nebo odebrány na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat klienta iOS, najdete v dokumentaci [ke klientovi pro iOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/)
