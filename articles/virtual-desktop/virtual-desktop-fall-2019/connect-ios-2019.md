---
title: Připojení k virtuálnímu počítači s Windows z iOS – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí klienta iOS
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 80bbb90c46376d29c1f4f8b0701cf4059db32246
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615003"
---
# <a name="connect-with-the-ios-client"></a>Připojení s klientem iOS

> Platí pro: iOS 13,0 nebo novější. Kompatibilní s iPhone, iPad a iPod touch.

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objekty virtuálních klientů Windows, které byly zavedeny v aktualizaci jarní 2020, přečtěte si [Tento článek](../connect-ios.md).

Pomocí našeho klienta ke stažení můžete získat přístup k prostředkům virtuálních klientů Windows ze zařízení s iOS. V této příručce se dozvíte, jak nastavit klienta iOS.

## <a name="install-the-ios-client"></a>Instalace klienta iOS

Začněte tím, že [si stáhnete](https://aka.ms/rdios) a nainstalujete klienta na zařízení s iOS.

## <a name="subscribe-to-a-feed"></a>Přihlášení k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu, který poskytuje správce, aby získal seznam spravovaných prostředků, ke kterým máte přístup v zařízení s iOS.

Přihlášení k odběru informačního kanálu:

1. V centru připojení klepněte **+** na a potom klepněte na **Přidat pracovní prostor**.
2. Do pole **Adresa URL informačního kanálu** zadejte adresu URL informačního kanálu. Adresa URL informačního kanálu může být buď adresa URL, nebo e-mailová adresa.
   - Pokud použijete adresu URL, použijte tu, kterou vám správce poskytl. Obvykle je <https://rdweb.wvd.microsoft.com>adresa URL.
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
