---
title: Připojení k virtuálnímu počítači s Windows pomocí webového klienta – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí webového klienta.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400632"
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

## <a name="using-an-input-method-editor"></a>Použití editoru IME

Webový klient podporuje použití editoru IME (Input Method Editor) ve vzdálené relaci ve verzi **1.0.21.16 nebo novější**. V hostitelském virtuálním počítači musí být nainstalována jazyková sada klávesnice, kterou chcete použít ve vzdálené relaci. Další informace o nastavení jazykových sad ve vzdálené relaci najdete v části [přidání jazykových sad do bitové kopie Windows 10 s více relacemi](language-packs.md).

Povolení vstupu v editoru IME pomocí webového klienta:

1. Před připojením ke vzdálené relaci přejdete na panel **Nastavení** webového klienta.

2. Přepněte nastavení **Povolit editor IME** na **zapnuto**.

3. V rozevírací nabídce vyberte klávesnici, kterou budete používat ve vzdálené relaci.

4. Připojte se ke vzdálené relaci.

Webový klient bude potlačit místní okno editoru IME, pokud se zaměřujete na vzdálenou relaci. Změna nastavení editoru IME poté, co jste už připojení ke vzdálené relaci, nebude mít žádný vliv.

>[!NOTE]
>Pokud není jazyková sada nainstalována na hostitelském virtuálním počítači, bude Vzdálená relace ve výchozím nastavení pro anglickou (USA) klávesnici.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat webového klienta, najdete v tématu [Začínáme s webovým klientem](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
