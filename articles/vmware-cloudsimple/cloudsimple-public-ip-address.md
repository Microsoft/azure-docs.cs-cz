---
title: Řešení Azure VMware podle CloudSimple – veřejná IP adresa
description: Další informace o veřejných IP adresách a jejich výhodách v řešení Azure VMware od CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024972"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Přehled veřejné IP adresy CloudSimple

Veřejná IP adresa umožňuje internetovým prostředkům komunikovat příchozí prostředky privátního cloudu na privátní IP adrese. Privátní IP adresa je virtuální počítač nebo nástroj pro vyrovnávání zatížení softwaru v programu Private Cloud vCenter. Veřejná IP adresa umožňuje zpřístupnit služby spuštěné ve vašem privátním cloudu internetu.

Veřejná IP adresa je vyhrazena privátní IP adrese, dokud ji neodřadíte. Veřejnou IP adresu lze přiřadit pouze k jedné privátní IP adrese.

Prostředek přidružený k veřejné IP adrese vždy používá veřejnou IP adresu pro přístup k Internetu. Ve výchozím nastavení je na veřejné IP adrese povolen pouze odchozí přístup k Internetu.  Příchozí provoz na veřejné IP adrese je odepřen.  Chcete-li povolit příchozí přenosy, vytvořte pravidlo brány firewall pro veřejnou IP adresu na konkrétní port.

## <a name="benefits"></a>Výhody

Použití veřejné IP adresy ke komunikaci příchozích poskytuje:

* Prevence útoků distribuovaného odmítnutí služby (DDoS). Tato ochrana je automaticky povolena pro veřejnou IP adresu.
* Nepřetržité sledování provozu a zmírňování běžných útoků na úrovni sítě v reálném čase. Tyto obrany jsou stejné obrany používané online službami společnosti Microsoft.
* Celé škálování globální sítě Azure. Síť lze použít k distribuci a zmírnění provozu útoku napříč oblastmi.  

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [přidělit veřejnou IP adresu](public-ips.md)