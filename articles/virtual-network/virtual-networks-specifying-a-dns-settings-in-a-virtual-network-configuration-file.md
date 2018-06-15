---
title: Zadání nastavení DNS v konfiguračním souboru virtuální sítě | Microsoft Docs
description: Postup změny nastavení serveru DNS ve virtuální síti pomocí konfiguračního souboru virtuální síť v klasickém modelu nasazení
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: ed7f02d3e389db3bc772c4fcb00a7b3877d60173
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794520"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Zadání nastavení DNS v konfiguračním souboru virtuální sítě
Soubor konfigurace sítě má dva elementy, které můžete použít k určení nastavení systému DNS (Domain Name): **DnsServers** a **DnsServerRef**. Můžete přidat seznam serverů DNS zadáním jejich IP adresy a odkazovat na názvy **DnsServers** element. Pak můžete použít **DnsServerRef** elementu, který chcete určit, které položky serveru DNS z elementu DnsServers budou použity pro jiné síťové lokality v rámci virtuální sítě.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic.

Soubor konfigurace sítě může obsahovat následující prvky. Název každého prvku je propojený na stránku, která poskytuje další informace o nastavení hodnoty elementu.

> [!IMPORTANT]
> Informace o tom, jak nakonfigurovat konfiguračního souboru sítě najdete v tématu [konfigurace virtuální sítě pomocí konfiguračního souboru sítě](virtual-networks-using-network-configuration-file.md). Informace o jednotlivých prvků obsažené v souboru konfigurace sítě najdete v tématu [Azure schéma konfigurace virtuální sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS Element](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> **Název** atribut **Server_dns** element se používají pouze jako odkaz pro **DnsServerRef** element. Nereprezentuje název hostitele pro DNS server. Každý **Server_dns** hodnota atributu musí být jedinečný v rámci celé předplatné Microsoft Azure
> 
> 

[Element lokality virtuální sítě](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Chcete-li toto nastavení pro element virtuální sítě, se musí být dříve definovaný v elementu DNS. DnsServerRef *název* v virtuálních síťových webů element musí odkazovat na název hodnota zadaný v elementu DNS pro server DNS *název*.
> 
> 

## <a name="next-steps"></a>Další postup
* Pochopení [schéma konfigurace virtuální sítě Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
* Pochopení [schéma konfigurace Azure Service](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Konfigurace virtuální sítě pomocí konfiguračních souborů síť](virtual-networks-using-network-configuration-file.md).

