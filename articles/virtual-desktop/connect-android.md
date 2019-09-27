---
title: Připojení k virtuálnímu počítači s Windows z Androidu – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí klienta pro Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: helohr
ms.openlocfilehash: c19aa6e0acc936c5b03afdab99ce0b9230838ce2
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71339007"
---
# <a name="connect-with-the-android-client"></a>Připojení pomocí klienta Android

> To platí pro: Android 4,1 a novější, Chromebooks s ChromeOS 53 a novějším.

>[!NOTE]
> Možnost přístupu k prostředkům virtuálního počítače s Windows z klienta pro Android je aktuálně dostupná ve verzi Preview.

Pomocí našeho klienta ke stažení můžete získat přístup k prostředkům virtuálních klientů Windows ze zařízení s Androidem. Můžete také použít klienta Android na zařízeních Chromebook, která podporují Obchod Google Play. V této příručce se dozvíte, jak nastavit klienta pro Android.

## <a name="install-the-android-client"></a>Instalace klienta pro Android

Začněte tím, že [si stáhnete](https://play.google.com/store/apps/details?id=com.microsoft.rdc.android) a nainstalujete klienta na zařízení s Androidem.

## <a name="subscribe-to-a-feed"></a>Přihlášení k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu, který poskytuje správce, aby získal seznam spravovaných prostředků, ke kterým máte přístup na zařízení s Androidem.

Přihlášení k odběru informačního kanálu:

1. V centru připojení klepněte na **+** a potom klepněte na **kanál vzdáleného prostředku**.
2. Do pole **Adresa URL informačního kanálu** zadejte adresu URL informačního kanálu. Adresa URL informačního kanálu může být buď adresa URL, nebo e-mailová adresa.
   - Pokud použijete adresu URL, použijte toho, kterého vám správce poskytl, obvykle <https://rdweb.wvd.microsoft.com>.
   - Pokud chcete používat e-mail, zadejte svou e-mailovou adresu. Pokud váš správce nakonfiguroval server tímto způsobem, bude klient hledat adresu URL přidruženou k vaší e-mailové adrese.
3. Klepněte na **Další**.
4. Po zobrazení výzvy zadejte své přihlašovací údaje.
   - V poli **uživatelské jméno**zadejte uživatelské jméno s oprávněním pro přístup k prostředkům.
   - Pro **heslo**zadejte heslo přidružené k uživatelskému jménu.
   - Může se také zobrazit výzva k poskytnutí dalších faktorů, pokud váš správce nakonfiguroval ověřování tímto způsobem.

Po přihlášení k odběru by centrum připojení mělo zobrazovat vzdálené prostředky.

Po přihlášení k odběru informačního kanálu se obsah informačního kanálu pravidelně aktualizuje automaticky. Prostředky je možné přidat, změnit nebo odebrat na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat klienta pro Android, najdete v tématu [Začínáme s klientem pro Android](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-android).
