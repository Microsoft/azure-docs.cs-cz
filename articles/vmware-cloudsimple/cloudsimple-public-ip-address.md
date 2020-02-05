---
title: Azure VMware Solutions (AVS) – Veřejná IP adresa
description: Seznamte se s veřejnými IP adresami a jejich výhodami v řešeních Azure VMware (AVS).
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cb9d0e33da4447760ae0be216c1dd9868c498bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024972"
---
# <a name="avs-public-ip-address-overview"></a>Přehled veřejné IP adresy pro funkci AVS

Veřejná IP adresa umožňuje, aby internetové prostředky komunikovaly s příchozími prostředky privátního cloudu ze sítě na privátní IP adrese. Soukromá IP adresa je virtuální počítač nebo nástroj pro vyrovnávání zatížení softwaru v rámci vašeho privátního cloudu služby AVS. Veřejná IP adresa umožňuje vystavit služby běžící na vašem privátním cloudu služby AVS na internetu.

Veřejná IP adresa je vyhrazená pro soukromou IP adresu, dokud ji nezrušíte. Veřejná IP adresa se dá přiřadit jenom k jedné privátní IP adrese.

Prostředek přidružený k veřejné IP adrese vždycky používá veřejnou IP adresu pro přístup k Internetu. Ve výchozím nastavení je na veřejné IP adrese povolený jenom odchozí přístup k Internetu.  Příchozí provoz na veřejné IP adrese je odepřený.  Chcete-li povolit příchozí provoz, vytvořte pravidlo brány firewall pro veřejnou IP adresu na konkrétní port.

## <a name="benefits"></a>Výhody

Použití veřejné IP adresy ke komunikaci příchozích adres poskytuje:

* Prevence útoků typu DDoS (Distributed Denial of Service). Tato ochrana je automaticky povolena pro veřejnou IP adresu.
* Nepřetržité monitorování provozu a zmírnění běžných útoků na úrovni sítě v reálném čase. Tyto obrany mají stejnou ochranu, kterou používá Microsoft online služby.
* Celá škála globální sítě Azure. Síť lze použít k distribuci a zmírnění provozu útoku napříč oblastmi.  

## <a name="next-steps"></a>Další kroky

* Informace o [přidělení veřejné IP adresy](public-ips.md)