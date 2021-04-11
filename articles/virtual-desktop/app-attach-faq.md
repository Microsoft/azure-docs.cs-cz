---
title: Připojení aplikace MSIX k virtuálnímu počítači s Windows – nejčastější dotazy – Azure
description: Nejčastější dotazy týkající se připojení aplikace MSIX k virtuálnímu klientovi Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a1a2464735ff8039bb31c43d6956cbf03e694c63
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448402"
---
# <a name="msix-app-attach-faq"></a>DOTAZY k připojení aplikace MSIX

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se připojení aplikace MSIX k virtuálnímu počítači s Windows.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Jaký je rozdíl mezi připojením aplikace MSIX a MSIX?

MSIX je formát balení pro aplikace, zatímco připojení aplikace MSIX je funkce, která poskytuje balíčky MSIX do nasazení.

## <a name="does-msix-app-attach-use-fslogix"></a>Připojí aplikace MSIX použít FSLogix?

Připojení aplikace MSIX nepoužívá FSLogix. Připojení k aplikaci MSIX a FSLogix jsou však navržená tak, aby poskytovaly bezproblémové uživatelské prostředí.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Můžu použít aplikaci MSIX připojit mimo virtuální plochu Windows?

K dispozici jsou rozhraní API pro Windows 10 Enterprise, která se připojují k aplikaci Power MSIX. Tato rozhraní API se dají používat mimo virtuální počítač s Windows. Není však k dispozici žádná rovina správy pro připojení aplikace MSIX mimo virtuální plochu Windows.

## <a name="how-do-i-get-an-msix-package"></a>Návody získat balíček MSIX?

Dodavatel softwaru vám poskytne balíček MSIX. Balíčky, které nejsou MSIX, můžete také převést na MSIX. Přečtěte si další informace [o tom, jak přesunout existující instalační programy do MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Které operační systémy podporují připojení aplikace MSIX?

Windows 10 Enterprise a Windows 10 Enterprise multi-session verze 2004 nebo novější.

## <a name="is-msix-app-attach-currently-generally-available"></a>Je připojení aplikace MSIX aktuálně všeobecně dostupné?

Připojení aplikace MSIX je součástí Windows 10 Enterprise a Windows 10 Enterprise multi-session verze 2004 nebo novější. Oba operační systémy jsou v současnosti všeobecně dostupné. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Můžu použít připojení aplikace MSIX mimo virtuální plochu Windows?

Rozhraní API pro připojení aplikací MSIX a MSIX jsou součástí více relací Windows 10 Enterprise a Windows 10 Enterprise, verze 2004 a novější. V tuto chvíli neposkytujeme software pro správu pro připojení aplikace MSIX mimo virtuální plochu Windows.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Můžu ve stejnou chvíli spustit dvě verze stejné aplikace?

Aby dvě verze stejných aplikací MSIX běžely současně, musí být řada balíčků MSIX definovaná v souboru appxmanifest.xml pro každou aplikaci odlišná.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Mám při použití aplikace MSIX připojit automatickou aktualizaci?

Ano. Připojení aplikace MSIX nepodporuje automatické aktualizace pro aplikace MSIX.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Jak fungují oprávnění s připojením aplikace MSIX?

Všechny virtuální počítače v hostitelském fondu, který používá připojení aplikace MSIX, musí mít oprávnění ke čtení sdílené složky, kde jsou uložené image MSIX. Pokud se používá také soubory Azure, bude jim potřeba udělit oprávnění řízení přístupu na základě role (RBAC) i nové oprávnění systému souborů NTFS (Technology File System).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Můžu použít připojení aplikace MSIX pro HTTP nebo HTTPs?

Používání aplikace MSIX připojit přes protokol HTTP nebo HTTPs není aktuálně podporováno.

## <a name="can-i-restage-the-same-msix-application"></a>Můžu znovu připravit stejnou aplikaci MSIX?

Ano. Můžete znovu připravit aplikace, které jste již znovu připravili, a nemělo by dojít k chybám.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Podporuje aplikace MSIX připojení certifikátů podepsaných svým držitelem?

Ano. Certifikát podepsaný svým držitelem je potřeba nainstalovat na všechny virtuální počítače hostitele relací, kde se pro hostování aplikace podepsané svým držitelem používá připojení aplikace MSIX.


## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o připojení aplikace MSIX, podívejte se na náš [Přehled](what-is-app-attach.md) a [Glosář](app-attach-glossary.md). V opačném případě začněte s [nastavením připojit k aplikaci](app-attach.md).
