---
title: Řešení VMware podle CloudSimple – veřejné IP adrese Azure
description: Další informace o veřejné IP adresy a jejich výhody na řešení VMware podle CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a366ec821e5e9663908f790ab3a88424fa956075
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576967"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Přehled CloudSimple veřejných IP adres

Veřejná IP adresa umožňuje internetové prostředky ke komunikaci příchozí prostředky privátního cloudu na privátní IP adresu. Privátní IP adresa je virtuální počítač nebo nástroj pro vyrovnávání zatížení softwaru. Privátní IP adresa je na vCenter privátního cloudu. Veřejná IP adresa umožňuje vystavit služby spuštěné v privátním cloudu k Internetu.

Veřejná IP adresa je vyhrazen pro privátní IP adresu, dokud je její přiřazení zrušit. Veřejnou IP adresu můžete přiřadit pouze na jednu privátní IP adresu.

Prostředek vždy přidružený k veřejné IP adresy používá veřejné IP adresy pro přístup k Internetu. Ve výchozím nastavení je povolená jenom odchozí internetový přístup na veřejnou IP adresu.  Příchozí provoz na veřejnou IP adresu byl odepřen.  Povolit příchozí přenosy, vytvořte pravidlo brány firewall pro veřejnou IP adresu na konkrétní port.

## <a name="benefits"></a>Výhody

Použitím veřejné IP adresy pro příchozí komunikaci poskytuje:

* Distribuované s cílem odepření služeb (DDoS) útoku ochrany před únikem informací. Tato ochrana je automaticky povolen pro veřejnou IP adresu.
* Provoz vždy na monitorování a v reálném čase ke zmírnění běžných útoků na úrovni sítě. Tato ochrana se stejné ochrany používané služeb Microsoft online services.
* Celé škále globální sítě Azure. Síť je možné distribuovat a zmírnit útok provoz napříč oblastmi.  

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [přidělení veřejné IP adresy](https://docs.azure.cloudsimple.com/publicips/)
