---
title: Zadání nastavení DNS v konfiguračním souboru virtuální sítě Azure
description: Postup změny nastavení serveru DNS ve virtuální síti pomocí konfiguračního souboru virtuální sítě v modelu nasazení Classic
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: db8b1817bb14d293632d16fe02792dbb85766559
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196642"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Zadání nastavení DNS v konfiguračním souboru virtuální sítě
Konfigurační soubor sítě má dva prvky, které můžete použít k zadání nastavení DNS (Domain Name System): **DnsServers** a **DnsServerRef**. Seznam serverů DNS můžete přidat zadáním jejich IP adres a referenčních názvů **DnsServers** elementu. Pak můžete pomocí elementu **DnsServerRef** určit, které položky serveru DNS z prvku DnsServers se používají pro různé síťové lokality v rámci vaší virtuální sítě.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic.

Konfigurační soubor sítě může obsahovat následující prvky. Název každého prvku je propojen se stránkou, která poskytuje další informace o nastavení hodnoty prvku.

> [!IMPORTANT]
> Informace o konfiguraci konfiguračního souboru sítě najdete v tématu [konfigurace Virtual Network pomocí konfiguračního souboru sítě](virtual-networks-using-network-configuration-file.md). Informace o jednotlivých prvcích obsažených v konfiguračním souboru sítě najdete v tématu věnovaném [schématu konfigurace pro Azure Virtual Network](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Element DNS](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> Atribut **Name** v elementu **serveru DNS** je použit pouze jako odkaz pro element **DnsServerRef** . Nepředstavuje název hostitele pro server DNS. Každá hodnota atributu **serveru DNS** musí být v rámci celého předplatného Microsoft Azure jedinečná.
> 
> 

[Element Virtual Networkch lokalit](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Chcete-li zadat toto nastavení pro prvek Virtual Network lokality, musí být dříve definováno v elementu DNS. *Název* DnsServerRef v elementu Virtual Network lokality musí odkazovat na hodnotu názvu zadanou v elementu DNS pro *název*serveru DNS.
> 
> 

## <a name="next-steps"></a>Další kroky
* Pochopení [schématu konfigurace Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093).
* Pochopení [schématu konfigurace služby Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Nakonfigurujte virtuální síť pomocí konfiguračních souborů sítě](virtual-networks-using-network-configuration-file.md).

