---
title: Připojení k virtuálnímu počítači s Windows z Androidu – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí klienta pro Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 636a31cdb657ca95ea9ea0da6bb85caa61425e5a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614990"
---
# <a name="connect-with-the-android-client"></a>Práce s klientem Android

> Platí pro: Android 4,1 a novější, Chromebooks s ChromeOS 53 a novějším.

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objekty virtuálních klientů Windows, které byly zavedeny v aktualizaci jarní 2020, přečtěte si [Tento článek](../connect-android.md).

>[!NOTE]
> Možnost přístupu k prostředkům virtuálního počítače s Windows z klienta pro Android je aktuálně dostupná ve verzi Preview.

Pomocí našeho klienta ke stažení můžete získat přístup k prostředkům virtuálních klientů Windows ze zařízení s Androidem. Můžete také použít klienta Android na zařízeních Chromebook, která podporují Obchod Google Play. V této příručce se dozvíte, jak nastavit klienta pro Android.

## <a name="install-the-android-client"></a>Instalace klienta pro Android

Začněte tím, že [si stáhnete](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) a nainstalujete klienta na zařízení s Androidem.

## <a name="subscribe-to-a-feed"></a>Přihlášení k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu, který poskytuje správce, aby získal seznam spravovaných prostředků, ke kterým máte přístup na zařízení s Androidem.

Přihlášení k odběru informačního kanálu:

1. V centru připojení klepněte **+** na a pak klepněte na **kanál vzdáleného prostředku**.
2. Do pole **Adresa URL informačního kanálu** zadejte adresu URL informačního kanálu. Adresa URL informačního kanálu může být buď adresa URL, nebo e-mailová adresa.
   - Pokud použijete adresu URL, použijte tu, kterou vám správce poskytl, normálně <https://rdweb.wvd.microsoft.com>.
   - Pokud chcete používat e-mail, zadejte svou e-mailovou adresu. Pokud váš správce nakonfiguroval server tímto způsobem, bude klient hledat adresu URL přidruženou k vaší e-mailové adrese.
3. Klepněte na **Další**.
4. Po zobrazení výzvy zadejte své přihlašovací údaje.
   - V poli **uživatelské jméno**zadejte uživatelské jméno s oprávněním pro přístup k prostředkům.
   - Pro **heslo**zadejte heslo přidružené k uživatelskému jménu.
   - Může se také zobrazit výzva k poskytnutí dalších faktorů, pokud váš správce nakonfiguroval ověřování tímto způsobem.

Po přihlášení k odběru by centrum připojení mělo zobrazovat vzdálené prostředky.

Po přihlášení k odběru informačního kanálu se obsah informačního kanálu pravidelně aktualizuje automaticky. Prostředky je možné přidat, změnit nebo odebrat na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat klienta pro Android, najdete v tématu [Začínáme s klientem pro Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).