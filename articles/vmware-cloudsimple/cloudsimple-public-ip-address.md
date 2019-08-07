---
title: Řešení VMware podle CloudSimple – veřejná IP adresa Azure
description: Přečtěte si o veřejných IP adresách a jejich přínosech na řešení VMware od CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 29d2bdb75a7f4409e6b3ffee2608cfe7adde6b1f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812518"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple – přehled veřejné IP adresy

Veřejná IP adresa umožňuje internetovým prostředkům komunikovat příchozí a privátní cloudové prostředky na privátní IP adrese. Soukromá IP adresa je buď virtuální počítač, nebo nástroj pro vyrovnávání zatížení softwaru. Privátní IP adresa se nachází ve vašem privátním cloudu vCenter. Veřejná IP adresa umožňuje vystavit služby, které běží na vašem privátním cloudu, na Internet.

Veřejná IP adresa je vyhrazená pro soukromou IP adresu, dokud ji nezrušíte. Veřejná IP adresa se dá přiřadit jenom k jedné privátní IP adrese.

Prostředek přidružený k veřejné IP adrese vždycky používá veřejnou IP adresu pro přístup k Internetu. Ve výchozím nastavení je na veřejné IP adrese povolený jenom odchozí přístup k Internetu.  Příchozí provoz na veřejné IP adrese je odepřený.  Chcete-li povolit příchozí provoz, vytvořte pravidlo brány firewall pro veřejnou IP adresu na konkrétní port.

## <a name="benefits"></a>Výhody

Použití veřejné IP adresy ke komunikaci příchozích adres poskytuje:

* Prevence útoků typu DDoS (Distributed Denial of Service). Tato ochrana je automaticky povolena pro veřejnou IP adresu.
* Nepřetržité monitorování provozu a zmírnění běžných útoků na úrovni sítě v reálném čase. Tyto obrany mají stejnou ochranu, kterou používá Microsoft online služby.
* Celá škála globální sítě Azure. Síť lze použít k distribuci a zmírnění provozu útoku napříč oblastmi.  

## <a name="next-steps"></a>Další postup

* Informace o [přidělení veřejné IP adresy](https://docs.azure.cloudsimple.com/public-ips/)
