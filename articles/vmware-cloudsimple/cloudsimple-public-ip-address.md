---
title: Řešení Azure VMware podle CloudSimple – veřejná IP adresa
description: Další informace o veřejných IP adresách a jejich přínosech k řešení Azure VMware od CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "77024972"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple – přehled veřejné IP adresy

Veřejná IP adresa umožňuje, aby internetové prostředky komunikovaly s prostředky privátního cloudu na privátní IP adrese. Soukromá IP adresa je virtuální počítač nebo nástroj pro vyrovnávání zatížení softwaru ve vašem privátním cloudu vCenter. Veřejná IP adresa umožňuje vystavit služby, které běží na vašem privátním cloudu, na Internet.

Veřejná IP adresa je vyhrazená pro soukromou IP adresu, dokud ji nezrušíte. Veřejná IP adresa se dá přiřadit jenom k jedné privátní IP adrese.

Prostředek přidružený k veřejné IP adrese vždycky používá veřejnou IP adresu pro přístup k Internetu. Ve výchozím nastavení je na veřejné IP adrese povolený jenom odchozí přístup k Internetu.  Příchozí provoz na veřejné IP adrese je odepřený.  Chcete-li povolit příchozí provoz, vytvořte pravidlo brány firewall pro veřejnou IP adresu na konkrétní port.

## <a name="benefits"></a>Výhody

Použití veřejné IP adresy ke komunikaci příchozích adres poskytuje:

* Prevence útoků typu DDoS (Distributed Denial of Service). Tato ochrana je automaticky povolena pro veřejnou IP adresu.
* Nepřetržité monitorování provozu a zmírnění běžných útoků na úrovni sítě v reálném čase. Tyto obrany mají stejnou ochranu, kterou používá Microsoft online služby.
* Celá škála globální sítě Azure. Síť lze použít k distribuci a zmírnění provozu útoku napříč oblastmi.  

## <a name="next-steps"></a>Další kroky

* Informace o [přidělení veřejné IP adresy](public-ips.md)