---
title: Připojení k virtuálnímu počítači s Windows (Classic) z iOS – Azure
description: Jak se připojit k virtuálnímu počítači s Windows (Classic) pomocí klienta iOS.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7efedadbba196223db2ad5700035e27d37fb414
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008453"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-ios-client"></a>Připojení k virtuálnímu počítači s Windows (Classic) pomocí klienta iOS

> Platí pro: iOS 13,0 nebo novější. Kompatibilní s iPhone, iPad a iPod touch.

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../connect-ios.md).

Pomocí našeho klienta ke stažení můžete získat přístup k prostředkům virtuálních klientů Windows ze zařízení s iOS. V této příručce se dozvíte, jak nastavit klienta iOS.

## <a name="install-the-ios-client"></a>Instalace klienta iOS

Začněte tím, že [si stáhnete](https://aka.ms/rdios) a nainstalujete klienta na zařízení s iOS.

## <a name="subscribe-to-a-feed"></a>Přihlášení k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu, který poskytuje správce, aby získal seznam spravovaných prostředků, ke kterým máte přístup v zařízení s iOS.

Přihlášení k odběru informačního kanálu:

1. V centru připojení klepněte na **+** a potom klepněte na **Přidat pracovní prostor**.
2. Do pole **Adresa URL informačního kanálu** zadejte adresu URL informačního kanálu. Adresa URL informačního kanálu může být buď adresa URL, nebo e-mailová adresa.
   - Pokud použijete adresu URL, použijte tu, kterou vám správce poskytl. Obvykle je adresa URL <https://rdweb.wvd.microsoft.com> .
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

Další informace o tom, jak používat klienta iOS, najdete v dokumentaci [Začínáme s klientem iOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
