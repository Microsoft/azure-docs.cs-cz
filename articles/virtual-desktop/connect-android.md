---
title: Připojení k virtuálnímu počítači s Windows z Androidu – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí klienta pro Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ebf91f68e45148d9a609ff671ffa4683bd74c82c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89226110"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>Připojení k virtuálnímu počítači s Windows pomocí klienta pro Android

> Platí pro: Android 4,1 a novější, Chromebooks s ChromeOS 53 a novějším.

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/connect-android-2019.md).

Pomocí našeho klienta ke stažení můžete získat přístup k prostředkům virtuálních klientů Windows ze zařízení s Androidem. Můžete také použít klienta Android na zařízeních Chromebook, která podporují Obchod Google Play. V této příručce se dozvíte, jak nastavit klienta pro Android.

## <a name="install-the-android-client"></a>Instalace klienta pro Android

Začněte tím, že [si stáhnete](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) a nainstalujete klienta na zařízení s Androidem.

## <a name="subscribe-to-a-feed"></a>Přihlášení k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu, který poskytuje správce, aby získal seznam spravovaných prostředků, ke kterým máte přístup na zařízení s Androidem.

Přihlášení k odběru informačního kanálu:

1. V centru připojení klepněte na **+** a pak klepněte na **kanál vzdáleného prostředku**.
2. Do pole **Adresa URL informačního kanálu** zadejte adresu URL informačního kanálu. Adresa URL informačního kanálu může být buď adresa URL, nebo e-mailová adresa.
   - Pokud použijete adresu URL, použijte tu, kterou vám správce poskytl, normálně <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Pokud chcete používat e-mail, zadejte svou e-mailovou adresu. Pokud váš správce nakonfiguroval server tímto způsobem, bude klient hledat adresu URL přidruženou k vaší e-mailové adrese.
   - Pokud se chcete připojit prostřednictvím portálu US Gov, použijte <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Klepněte na **Další**.
4. Po zobrazení výzvy zadejte své přihlašovací údaje.
   - V poli **uživatelské jméno** zadejte uživatelské jméno s oprávněním pro přístup k prostředkům.
   - Pro **heslo** zadejte heslo přidružené k uživatelskému jménu.
   - Může se také zobrazit výzva k poskytnutí dalších faktorů, pokud váš správce nakonfiguroval ověřování tímto způsobem.

Po přihlášení k odběru by centrum připojení mělo zobrazovat vzdálené prostředky.

Po přihlášení k odběru informačního kanálu se obsah informačního kanálu pravidelně aktualizuje automaticky. Prostředky je možné přidat, změnit nebo odebrat na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat klienta pro Android, najdete v tématu [Začínáme s klientem pro Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
