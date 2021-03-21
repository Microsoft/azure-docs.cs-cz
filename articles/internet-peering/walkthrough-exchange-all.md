---
title: Podrobný průvodce peeringem typu Exchange
titleSuffix: Azure
description: Podrobný průvodce peeringem typu Exchange
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586818"
---
# <a name="exchange-peering-walkthrough"></a>Podrobný průvodce peeringem typu Exchange

V této části najdete popis kroků, které je třeba provést při nastavování a správě partnerského vztahu serveru Exchange.

## <a name="create-an-exchange-peering"></a>Vytvoření partnerského vztahu Exchange
> [!div class="mx-imgBorder"]
> ![Pracovní postup partnerských vztahů Exchange a stavy připojení](./media/exchange-peering.png)

Aby bylo možné zřídit partnerský vztah serveru Exchange, je nutné dodržovat následující kroky:
1. Projděte si [zásady partnerského vztahu](https://peering.azurewebsites.net/peering) Microsoftu, abyste pochopili požadavky na partnerský vztah Exchange.
1. Vyhledání umístění partnerského vztahu Microsoftu a ID zařízení partnerského vztahu v [PeeringDB](https://www.peeringdb.com/net/694)
1. Požádejte o partnerský vztah Exchange pro umístění partnerského vztahu podle pokynů v článku [Vytvoření a úprava partnerského vztahu Exchange pomocí PowerShellu](howto-exchange-powershell.md) , kde najdete další podrobnosti.
1. Po odeslání žádosti o vytvoření partnerského vztahu Microsoft žádost zkontroluje a v případě potřeby se vás pokusí kontaktovat.
1. Po schválení se stav připojení změní na schváleno.
1. Konfigurace relace protokolu BGP na konci a informování Microsoftu
1. Vytvoříme relaci protokolu BGP s ODEPŘENím všech zásad a ověří se od začátku do konce.
1. V případě úspěchu obdržíte oznámení, že stav připojení partnerského vztahu je aktivní.
1. Provoz se pak povolí prostřednictvím nového partnerského vztahu.

Všimněte si, že stavy připojení nejsou zaměňovány se standardními stavy relací [protokolu BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) .

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Převod starší verze peeringu typu Exchange na prostředek Azure
Před převodem staršího partnerského vztahu systému Exchange na prostředek Azure musí být dodrženy tyto kroky:
1. Postupujte podle pokynů v tématu [převedení staršího partnerského vztahu Exchange na prostředek Azure](howto-legacy-exchange-powershell.md) .
1. Po odeslání žádosti o převod Microsoft žádost zkontroluje a v případě potřeby vás bude kontaktovat.
1. Po schválení se váš partnerský vztah Exchange se stavem připojení zobrazí jako aktivní.

## <a name="deprovision-exchange-peering"></a>Zrušit zřízení partnerského vztahu Exchange
Obraťte se na [partnerský vztah Microsoftu](mailto:peering@microsoft.com) , aby se zrušilo zřízení partnerského vztahu Exchange.

Pokud je pro zrušení zřízení nastavený partnerský vztah Exchange, zobrazí se stav připojení jako **PendingRemove**

> [!NOTE]
> Pokud spustíte rutinu PowerShellu k odstranění partnerského vztahu Exchange, když je stav připojení ProvisioningStarted nebo ProvisioningCompleted, operace se nezdaří.

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o [požadavcích pro nastavení partnerského vztahu s Microsoftem](prerequisites.md).
