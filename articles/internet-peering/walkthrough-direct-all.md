---
title: Podrobný průvodce peeringem typu Direct
titleSuffix: Azure
description: Podrobný průvodce peeringem typu Direct
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592377"
---
# <a name="direct-peering-walkthrough"></a>Podrobný průvodce peeringem typu Direct

V této části najdete popis kroků, které je třeba provést při nastavování a správě přímého partnerského vztahu.

## <a name="create-a-direct-peering"></a>Vytvoření přímého partnerského vztahu
> [!div class="mx-imgBorder"]
> ![Pracovní postup přímého partnerského vztahu a stavy připojení](./media/direct-peering.png)

Aby bylo možné zřídit přímý partnerský vztah, musí být dodrženy tyto kroky:
1. Projděte si [zásady partnerského vztahu](https://peering.azurewebsites.net/peering) Microsoftu, abyste pochopili požadavky na přímý partnerský vztah.
1. Postupujte podle pokynů v tématu [Vytvoření nebo úprava přímého partnerského vztahu](howto-direct-powershell.md) pro odeslání žádosti o partnerský vztah.
1. Po odeslání žádosti o partnerský vztah bude společnost Microsoft kontaktovat používání vaší registrované e-mailové adresy, aby provedla LOA (schválení), nebo další informace.
1. Po schválení žádosti o partnerský vztah se stav připojení změní na ProvisioningStarted.
1. Musíte:
    1. dokončení kabeláže podle LOA
    1. (volitelně) provést test propojení pomocí 169.254.0.0/16
    1. Nakonfigurujte relaci protokolu BGP a pak nás upozorněte.
1. Microsoft zřídí relaci protokolu BGP se ZAMÍTNUTím všech zásad a ověří od začátku do konce.
1. V případě úspěchu obdržíte oznámení, že stav připojení partnerského vztahu je aktivní.
1. Provoz se pak povolí prostřednictvím nového partnerského vztahu.

Všimněte si, že stavy připojení nejsou zaměňovány se standardními stavy relací [protokolu BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) .

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Převod starší verze peeringu typu Direct na prostředek Azure
Aby bylo možné převést starší verze přímého partnerského vztahu na prostředek Azure, musíte dodržovat tyto kroky:
1. Postupujte podle pokynů v tématu [Převod starší verze přímého partnerského vztahu na prostředek Azure](howto-legacy-direct-powershell.md) .
1. Po odeslání žádosti o převod Microsoft žádost zkontroluje a v případě potřeby vás bude kontaktovat.
1. Po schválení uvidíte přímé partnerské vztahy se stavem připojení jako aktivní.

## <a name="deprovision-direct-peering"></a>Zrušení zřízení přímého partnerského vztahu
Obraťte se na tým [partnerského vztahu Microsoftu](mailto:peering@microsoft.com) , aby zrušil zřízení přímého partnerského vztahu.

Když se pro zrušení zřízení nastaví přímý partnerský vztah, zobrazí se stav připojení jako **PendingRemove** .

> [!NOTE]
> Pokud spustíte rutinu prostředí PowerShell pro odstranění přímého partnerského vztahu, když je vlastnost ConnectionState ProvisioningStarted nebo ProvisioningCompleted, operace se nezdaří.

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o [požadavcích pro nastavení partnerského vztahu s Microsoftem](prerequisites.md).
