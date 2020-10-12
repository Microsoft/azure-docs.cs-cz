---
title: Připojení aplikace MSIX k virtuálnímu počítači s Windows – nejčastější dotazy – Azure
description: Nejčastější dotazy týkající se připojení aplikace MSIX k virtuálnímu klientovi Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556235"
---
# <a name="msix-app-attach-faq"></a>DOTAZY k připojení aplikace MSIX

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se připojení aplikace MSIX k virtuálnímu počítači s Windows.

## <a name="does-msix-app-attach-use-fslogix"></a>Připojí aplikace MSIX použít FSLogix?

Připojení aplikace MSIX nepoužívá FSLogix. Připojení aplikace a FSLogix jsou ale navržené tak, aby poskytovaly bezproblémové uživatelské prostředí.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Můžu použít připojení aplikace MSIX mimo virtuální plochu Windows?

Ano, MSIX App Attach je funkce, která je součástí Windows 10 Enterprise a dá se používat mimo virtuální plochu Windows. Není však k dispozici žádná rovina správy pro připojení aplikace MSIX mimo virtuální plochu Windows.

## <a name="how-do-i-get-an-msix-package"></a>Návody získat balíček MSIX?

Dodavatel softwaru vám poskytne balíček MSIX. Balíčky, které nejsou MSIX, můžete také převést na MSIX. Přečtěte si další informace [o tom, jak přesunout existující instalační programy do MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Které operační systémy podporují připojení aplikace MSIX?

Více relací pro Windows 10 Enterprise a Windows 10 Enterprise.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o připojení aplikace MSIX, podívejte se na náš [Přehled](what-is-app-attach.md) [Glosář](app-attach-glossary.md). V opačném případě začněte s [nastavením připojit k aplikaci](app-attach.md).