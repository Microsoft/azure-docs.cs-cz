---
title: Podrobný průvodce peeringem typu Exchange
titleSuffix: Azure
description: Podrobný průvodce peeringem typu Exchange
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775170"
---
# <a name="exchange-peering-walkthrough"></a>Podrobný průvodce peeringem typu Exchange

Tato část vysvětluje kroky, které je třeba provést při nastavování a správě partnerského vztahu serveru Exchange.

## <a name="create-an-exchange-peering"></a>Vytvoření partnerského vztahu serveru Exchange
> [!div class="mx-imgBorder"]
> ![Pracovní postup partnerského vztahu exchange a stavy připojení](./media/exchange-peering.png)

Pro zřízení partnerského vztahu exchange je nutné dodržet následující kroky:
1. Zkontrolujte [zásady partnerského vztahu](https://peering.azurewebsites.net/peering) Microsoftu, abyste pochopili požadavky na partnerský vztah exchange.
1. Najít umístění partnerského vztahu Microsoftu a id služby partnerského vztahu v [PeeringDB](https://www.peeringdb.com/net/694)
1. Požádejte partnerský vztah exchange pro umístění partnerského vztahu pomocí pokynů v části Vytvořit a upravit partnerský vztah exchange pomocí článku [prostředí PowerShell](howto-exchange-powershell.md) pro další podrobnosti.
1. Po odeslání žádosti o partnerský vztah společnost Microsoft žádost zkontroluje a v případě potřeby vás bude kontaktovat.
1. Po schválení se stav připojení změní na Schváleno
1. Konfigurace relace protokolu BGP na konci a upozornění společnosti Microsoft
1. Zřídíme relaci Protokolu BGP se zásadami DENY ALL a ověříme konec do konce.
1. V případě úspěchu obdržíte oznámení, že stav připojení partnerského vztahu je aktivní.
1. Přenos y pak budou povoleny prostřednictvím nového partnerského vztahu.

Všimněte si, že stavy připojení nelze zaměňovat se standardními stavy relace [Protokolu BGP.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Převod starší verze peeringu typu Exchange na prostředek Azure
Chcete-li převést starší partnerský vztah Exchange na prostředek Azure, je nutné je dodržet následující kroky:
1. Postupujte podle pokynů v [části Převod staršího partnerského vztahu Exchange na prostředek Azure](howto-legacy-exchange-powershell.md)
1. Po odeslání žádosti o převod společnost Microsoft žádost zkontroluje a v případě potřeby vás bude kontaktovat.
1. Po schválení se váš partnerský vztah exchange se stavem připojení zobrazí jako Aktivní.

## <a name="deprovision-exchange-peering"></a>Zrušení zřízení partnerského vztahu exchange
Obraťte se na [partnerský vztah Microsoftu](mailto:peering@microsoft.com) k odstranění zřízení partnerského vztahu exchange.

Pokud je partnerský vztah serveru Exchange nastaven pro zrušení zřízení, zobrazí se stav připojení jako **PendingRemove**

> [!NOTE]
> Pokud spustíte rutinu prostředí PowerShell odstranit partnerský vztah exchange při stavu připojení provisioningStarted nebo provisioningCompleted operace se nezdaří.

## <a name="next-steps"></a>Další kroky

* Informace o [požadavcích na nastavení partnerského vztahu se společností Microsoft](prerequisites.md).
