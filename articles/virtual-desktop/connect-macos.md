---
title: Připojení k virtuálnímu počítači s Windows z macOS – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí klienta macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bcd7bf46a3def963a4f9fe8913c4f8afcdce743f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075645"
---
# <a name="connect-to-windows-virtual-desktop-with-the-macos-client"></a>Připojení k virtuálnímu počítači s Windows pomocí klienta macOS

> Platí pro: macOS 10,12 nebo novější

>[!IMPORTANT]
>Tento obsah se vztahuje na jarní 2020 aktualizaci s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows na verzi 2019 bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/connect-macos-2019.md).
>
> V současnosti je ve verzi Public Preview na jaře 2020 aktualizace virtuálních počítačů s Windows. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

K prostředkům virtuálních klientů s Windows můžete přistupovat ze svých zařízení macOS pomocí našeho klienta ke stažení. V této příručce se dozvíte, jak nastavit klienta.

## <a name="install-the-client"></a>Instalace klienta

Začněte tím, že [si stáhnete](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)   a nainstalujete klienta na zařízení MacOS.

## <a name="subscribe-to-a-feed"></a>Přihlášení k odběru informačního kanálu

Přihlaste se k odběru informačního kanálu, který vám správce umožnil získat seznam spravovaných prostředků, které máte k dispozici na vašem zařízení macOS.

Přihlášení k odběru informačního kanálu:

1. Na hlavní stránce vyberte **Přidat pracovní prostor** pro připojení ke službě a načtení prostředků.
2. Zadejte adresu URL informačního kanálu. Může se jednat o adresu URL nebo e-mailovou adresu:
   - Pokud použijete adresu URL, použijte tu, kterou vám správce poskytl. Obvykle je adresa URL <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Pokud chcete používat e-mail, zadejte svou e-mailovou adresu. To klientovi oznamuje, aby vyhledal adresu URL přidruženou k vaší e-mailové adrese, pokud váš správce nakonfiguroval server tímto způsobem.
3. Vyberte **Přidat**.
4. Po zobrazení výzvy se přihlaste pomocí svého uživatelského účtu.

Po přihlášení by se měl zobrazit seznam dostupných prostředků.

Jakmile se přihlásíte k odběru informačního kanálu, obsah informačního kanálu se pravidelně aktualizuje v pravidelných intervalech. Prostředky je možné přidat, změnit nebo odebrat na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o klientovi macOS najdete v dokumentaci ke [klientskému MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) v části Začínáme.
