---
title: App Service Environment sítě
description: Podrobnosti o App Service Environment sítě
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 680b1f3b6af186eba27a4dd926016a04cd863760
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013480"
---
# <a name="app-service-environment-networking"></a>App Service Environment sítě

> [!NOTE]
> Tento článek se týká App Service Environment V3 (Preview).
> 

App Service Environment (pomocného mechanismu) je nasazení Azure App Service jednoho tenanta, které hostuje webové aplikace, aplikace API a aplikace Function App. Při instalaci pomocného mechanismu vyberte službu Azure Virtual Network (VNet), do které chcete nasadit službu. Všechny příchozí a odchozí přenosy aplikace budou uvnitř virtuální sítě, kterou zadáte.  

ASEv3 používá dvě podsítě.  Pro soukromý koncový bod, který zpracovává příchozí provoz, se používá jedna podsíť. Může se jednat o již existující podsíť nebo o novou.  Příchozí podsíť se dá použít pro jiné účely, než je privátní koncový bod. Odchozí podsíť se dá použít jenom pro odchozí přenosy z pomocného mechanismu služby. V odchozí podsíti pomocného mechanismu řízení nelze nic dalšího.

## <a name="addresses"></a>Adresy 
Při vytváření pomocného nástroje má následující adresy:

| Typ adresy | Popis |
|--------------|-------------|
| Příchozí adresa | Příchozí adresa je adresa privátního koncového bodu, kterou používá váš pomocným mechanismem řízení. |
| Odchozí podsíť | Odchozí podsíť je taky podsíť pomocného mechanismu. Ve verzi Preview se tato podsíť používá jenom pro odchozí přenosy. |
| Odchozí adresa pro Windows | Aplikace pro Windows v tomto pomocném formuláři budou ve výchozím nastavení používat tuto adresu při odchozím volání na Internet. |
| Odchozí adresa pro Linux | Aplikace pro Linux v této službě pomocného mechanismu budou ve výchozím nastavení používat tuto adresu při odchozích voláních na Internet. |

ASEv3 obsahuje podrobnosti o adresách využívaných pomocným mechanismem v části **IP adresy** na portálu pomocného programu.

![Uživatelské rozhraní adres pomocného uživatelského rozhraní](./media/networking/networking-ip-addresses.png)

Pokud odstraníte privátní koncový bod používaný pomocným mechanismem, nebudete se moct spojit s aplikacemi ve vašem přihlašování.  

Pomocného nástroje používá adresy v odchozí podsíti k podpoře infrastruktury používané pomocným mechanismem řízení. Při škálování App Service plánů v pomocném mechanismu služby budete používat víc adres. Aplikace v pomocném modulu pro přihlášení nemají ve výstupní podsíti vyhrazené adresy. Adresy používané aplikací v odchozí podsíti aplikací se v průběhu času mění.

## <a name="ports"></a>Porty

Pomocného modulu pro příjem dat přijímá aplikace na portech 80 a 443.  Pro tento pomocného mechanismu neexistují žádné požadavky na příchozí nebo odchozí port. 

## <a name="extra-configurations"></a>Dodatečné konfigurace

Na rozdíl od ASEv2 s ASEv3 můžete nastavit skupiny zabezpečení sítě (skupin zabezpečení sítě) a směrovací tabulky (udr) podle toho, co se hodí bez omezení. Pokud chcete vynutit tunelování všech odchozích přenosů z vašeho pomocného programu do zařízení síťové virtuální zařízení. Zařízení WAF můžete umístit před veškerý příchozí provoz do svého pomocného programu. 

## <a name="dns"></a>DNS

Aplikace ve vašem pomocném formuláři budou používat DNS, se kterými je vaše virtuální síť nakonfigurovaná. Postupujte podle pokynů v části [použití App Service Environment](https://docs.microsoft.com/azure/app-service/environment/using#dns-configuration) ke konfiguraci serveru DNS tak, aby odkazoval na váš pokyn pro přihlášení k systému. Pokud chcete, aby některé aplikace používaly jiný server DNS, než je vaše virtuální síť nakonfigurovaná pomocí, můžete ji ručně nastavit na základě aplikace s nastavením aplikace WEBSITE_DNS_SERVER a WEBSITE_DNS_ALT_SERVER. Nastavení aplikace WEBSITE_DNS_ALT_SERVER nakonfiguruje sekundární server DNS. Sekundární server DNS se používá jenom v případě, že není k dispozici žádná odpověď z primárního serveru DNS. 

## <a name="preview-limitation"></a>Omezení verze Preview

Existuje několik funkcí sítě, které nejsou k dispozici v ASEv3.  Mezi věci, které nejsou dostupné v ASEv3, patří:

• FTP • vzdálené ladění • nasazení externího nástroje pro vyrovnávání zatížení • schopnost získat přístup k privátnímu registru kontejneru pro nasazení kontejnerů • schopnost volat globálně partnerských virtuální sítě • schopnost zálohovat/obnovit pomocí koncového bodu služby nebo privátního koncového bodu zabezpečeného účet úložiště • schopnost mít odkazy na Trezor klíčů klíčů pro koncové body služby nebo privátního koncového bodu. • schopnost používat BYOS pro koncový bod služby nebo privátní koncový bod zabezpečeného úložiště • použití Network Watcher nebo NSG toku na odchozí provoz
    
    

