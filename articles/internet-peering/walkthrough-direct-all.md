---
title: Podrobný průvodce peeringem typu Direct
titleSuffix: Azure
description: Podrobný průvodce peeringem typu Direct
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775508"
---
# <a name="direct-peering-walkthrough"></a>Podrobný průvodce peeringem typu Direct

Tato část vysvětluje kroky, které je třeba provést při nastavování a správě přímého partnerského vztahu.

## <a name="create-a-direct-peering"></a>Vytvoření přímého partnerského vztahu
> [!div class="mx-imgBorder"]
> ![Pracovní postup přímého partnerského vztahu a stavy připojení](./media/direct-peering.png)

Pro zajištění přímého partnerského vztahu je třeba dodržovat následující kroky:
1. Projděte si [zásady partnerského vztahu](https://peering.azurewebsites.net/peering) Microsoftu, abyste pochopili požadavky na přímý partnerský vztah.
1. Podle pokynů v [části Vytvoření nebo úprava přímého partnerského vztahu](howto-direct-powershell.md) odešlete žádost o partnerský vztah.
1. Po odeslání žádosti o partnerský vztah bude společnost Microsoft kontaktovat pomocí vaší registrované e-mailové adresy, aby poskytla loa (Letter of Authorization) nebo pro jiné informace.
1. Po schválení požadavku partnerského vztahu se stav připojení změní na ProvisioningStarted.
1. Musíte:
    1. kompletní zapojení dle LOA
    1. (volitelně) provést test spojení pomocí 169.254.0.0/16
    1. nakonfigurujte relaci Protokolu BGP a poté nás o zpozorněte.
1. Společnost Microsoft zřazuje relaci protokolu BGP se zásadami DENY ALL a ověřuje konec do konce.
1. V případě úspěchu obdržíte oznámení, že stav připojení partnerského vztahu je aktivní.
1. Přenos y pak budou povoleny prostřednictvím nového partnerského vztahu.

Všimněte si, že stavy připojení nelze zaměňovat se standardními stavy relace [Protokolu BGP.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Převod starší verze peeringu typu Direct na prostředek Azure
Chcete-li převést starší přímý partnerský vztah na prostředek Azure, je nutné je dodržet následující kroky:
1. Postupujte podle pokynů v [části Převod staršího přímého partnerského vztahu na prostředek Azure](howto-legacy-direct-powershell.md)
1. Po odeslání žádosti o převod společnost Microsoft žádost zkontroluje a v případě potřeby vás bude kontaktovat.
1. Po schválení se váš přímý partnerský vztah se stavem připojení zobrazí jako Aktivní.

## <a name="deprovision-direct-peering"></a>Zrušení přímého partnerského vztahu
Obraťte se na tým [partnerského vztahu Microsoftu](mailto:peering@microsoft.com) a zrušení přímého partnerského vztahu.

Pokud je přímý partnerský vztah nastaven pro zrušení zřízení, zobrazí se stav připojení jako **PendingRemove**

> [!NOTE]
> Pokud spustíte rutinu prostředí PowerShell odstranit přímý partnerský vztah při ConnectionState je ProvisioningStarted nebo ProvisioningCompleted operace se nezdaří.

## <a name="next-steps"></a>Další kroky

* Informace o [požadavcích na nastavení partnerského vztahu se společností Microsoft](prerequisites.md).
