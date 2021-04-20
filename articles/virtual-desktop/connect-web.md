---
title: Připojení k virtuálnímu počítači s Windows pomocí webového klienta – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí webového klienta.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 80a090abee45f9cb3ec6ee5406aad6abf1d73a59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725003"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Připojení k virtuálnímu počítači s Windows pomocí webového klienta

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/connect-web-2019.md).

Webový klient umožňuje přístup k prostředkům virtuálního počítače s Windows z webového prohlížeče bez procesu instalace.

>[!NOTE]
>Webový klient nyní nepodporuje podporu pro mobilní operační systémy.

## <a name="supported-operating-systems-and-browsers"></a>Podporované operační systémy a prohlížeče

I když by měl prohlížeč podporující technologii HTML5 fungovat, oficiálně podporujeme následující operační systémy a prohlížeče.

| Prohlížeč           | Podporovaný operační systém                     | Poznámky               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Verze 11 nebo novější |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Verze 55 nebo novější |
| Google Chrome     | Windows, macOS, Linux, operační systém Chrome |                     |

## <a name="access-remote-resources-feed"></a>Přístup ke vzdáleným prostředkům kanálu

V prohlížeči přejděte na Azure Resource Manager integrovanou verzi webového klienta pro virtuální počítače s Windows <https://rdweb.wvd.microsoft.com/arm/webclient> a přihlaste se pomocí svého uživatelského účtu.

>[!NOTE]
>Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager integrace, připojte se <https://rdweb.wvd.microsoft.com/webclient> místo toho k prostředkům.
>
> Pokud používáte portál US Gov, použijte <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Pokud jste se už přihlásili pomocí jiného účtu Azure Active Directory, než který chcete použít pro virtuální plochu Windows, měli byste se buď odhlásit, nebo použít soukromé okno prohlížeče.

Po přihlášení by se teď měl zobrazit seznam prostředků. Prostředky můžete spustit tak, že je vyberete jako normální aplikaci na kartě **všechny prostředky** .

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat webového klienta, najdete v tématu [Začínáme s webovým klientem](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
