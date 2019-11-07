---
title: Připojení k virtuálnímu počítači s Windows z iOS – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí klienta iOS
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bfc7efa6e8ead3b53704e3c9bd189b18cb787618
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605838"
---
# <a name="connect-with-the-ios-client"></a>Připojení s klientem iOS

> Platí pro: iOS 8,0 nebo novější. Kompatibilní s iPhone, iPad a iPod touch.

>[!NOTE]
> Klient iOS je aktuálně stále ve verzi Preview.

Pomocí našeho klienta ke stažení můžete získat přístup k prostředkům virtuálních klientů Windows ze zařízení s iOS. V této příručce se dozvíte, jak nastavit klienta iOS.

## <a name="install-the-ios-beta-client"></a>Nainstalovat klienta iOS beta
Instalace klienta iOS beta:

1. Nainstalujte aplikaci [Apple testovacího prostředí](https://apps.apple.com/us/app/testflight/id899247664) na zařízení s iOS.
2. Na zařízení s iOS otevřete prohlížeč a přejděte na [aka.MS/rdiosbeta](https://aka.ms/rdiosbeta).
3. V části popisek **Krok 2: Připojte se k beta verzi**vyberte **zahájit testování**.
4. Až budete přesměrováni do aplikace testovacího prostředí, vyberte **přijmout**a pak vyberte **nainstalovat**.

## <a name="subscribe-to-a-feed"></a>Přihlášení k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu, který poskytuje správce, aby získal seznam spravovaných prostředků, ke kterým máte přístup v zařízení s iOS.

Přihlášení k odběru informačního kanálu:

1. V centru připojení klepněte na **+** a potom klepněte na **Přidat pracovní prostor**.
2. Do pole **Adresa URL informačního kanálu** zadejte adresu URL informačního kanálu. Adresa URL informačního kanálu může být buď adresa URL, nebo e-mailová adresa.
   - Pokud použijete adresu URL, použijte tu, kterou vám správce poskytl. Adresa URL je obvykle <https://rdweb.wvd.microsoft.com>.
   - Pokud chcete používat e-mail, zadejte svou e-mailovou adresu. To klientovi oznamuje, aby vyhledal adresu URL přidruženou k vaší e-mailové adrese, pokud váš správce nakonfiguroval server tímto způsobem.
3. Klepněte na **Další**.
4. Po zobrazení výzvy zadejte své přihlašovací údaje.
   - V poli **uživatelské jméno**zadejte uživatelské jméno s oprávněním pro přístup k prostředkům.
   - Pro **heslo**zadejte heslo přidružené k uživatelskému jménu.
   - Může se také zobrazit výzva k poskytnutí dalších faktorů, pokud váš správce nakonfiguroval ověřování tímto způsobem.
5. Klepněte na **Uložit**.

V tomto případě by měl centrum připojení zobrazit vzdálené prostředky.

Po přihlášení k odběru informačního kanálu se obsah informačního kanálu pravidelně aktualizuje automaticky. Prostředky je možné přidat, změnit nebo odebrat na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat klienta iOS beta, najdete v dokumentaci [Začínáme s klientem iOS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) .
