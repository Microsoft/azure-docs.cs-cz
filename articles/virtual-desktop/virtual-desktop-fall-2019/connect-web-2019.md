---
title: Připojení webového klienta Windows Virtual Desktop (Classic) – Azure
description: Jak se připojit k virtuálnímu počítači s Windows (Classic) pomocí webového klienta.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0ea095a8ed902b9636b0cb8026f86eb3a0882460
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445189"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Připojení k virtuální ploše Windows (Classic) pomocí webového klienta

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../connect-web.md).

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
>Pokud používáte virtuální plochu Windows s Azure Resource Manager integrací, připojte se místo toho k prostředkům <https://rdweb.wvd.microsoft.com/arm/webclient> .

>[!NOTE]
>Pokud jste se už přihlásili pomocí jiného účtu Azure Active Directory, než který chcete použít pro virtuální plochu Windows, měli byste se buď odhlásit, nebo použít soukromé okno prohlížeče.

Po přihlášení by se teď měl zobrazit seznam prostředků. Prostředky můžete spustit tak, že je vyberete jako normální aplikaci na kartě **všechny prostředky** .

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat webového klienta, najdete v tématu [Začínáme s webovým klientem](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
