---
title: Připojení webového klienta pro virtuální počítače s Windows – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí webového klienta.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b73a6bf2c8b853d9e3e939b6c8ff6fc05ac7021
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657255"
---
# <a name="connect-with-the-web-client"></a>Připojení k webovému klientovi

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objekty virtuálních klientů Windows, které byly zavedeny v aktualizaci jarní 2020, přečtěte si [Tento článek](../connect-web.md).

Webový klient umožňuje přístup k prostředkům virtuálního počítače s Windows z webového prohlížeče bez procesu instalace.

>[!NOTE]
>Webový klient nyní nepodporuje podporu pro mobilní operační systémy.

## <a name="supported-operating-systems-and-browsers"></a>Podporované operační systémy a prohlížeče

I když by měl prohlížeč podporující technologii HTML5 fungovat, oficiálně podporujeme následující operační systémy a prohlížeče.

| Prohlížeč           | Podporovaný operační systém                     | Poznámky               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Verze 55 nebo novější |
| Google Chrome     | Windows, macOS, Linux, operační systém Chrome |                     |

## <a name="access-remote-resources-feed"></a>Přístup ke vzdáleným prostředkům kanálu

V prohlížeči přejděte na webový klient pro virtuální počítače <https://rdweb.wvd.microsoft.com/webclient> s Windows a přihlaste se pomocí svého uživatelského účtu.

>[!NOTE]
>Pokud používáte Azure Resource Managerovou integraci virtuálních 2020 počítačů s Windows pomocí služby Integration, připojte se <https://rdweb.wvd.microsoft.com/arm/webclient> místo toho k prostředkům.

>[!NOTE]
>Pokud jste se už přihlásili pomocí jiného účtu Azure Active Directory, než který chcete použít pro virtuální plochu Windows, měli byste se buď odhlásit, nebo použít soukromé okno prohlížeče.

Po přihlášení by se teď měl zobrazit seznam prostředků. Prostředky můžete spustit tak, že je vyberete jako normální aplikaci na kartě **všechny prostředky** .

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat webového klienta, najdete v tématu [Začínáme s webovým klientem](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
