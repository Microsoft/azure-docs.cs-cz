---
title: Zadání nastavení DNS v konfiguračním souboru virtuální sítě | Dokumentace Microsoftu
description: Změna nastavení serveru DNS ve virtuální síti pomocí souboru konfigurace virtuální sítě v modelu nasazení classic
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
ms.openlocfilehash: 36f7ed9b02b66718327c1a05a6cf29eedf39e7a5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248381"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Zadání nastavení DNS v konfiguračním souboru virtuální sítě
Soubor konfigurace sítě má dva prvky, které můžete použít k určení nastavení systému DNS (Domain Name): **DnsServers** a **DnsServerRef**. Můžete přidat seznam serverů DNS zadáním jejich adresy IP a názvů, které se odkazují **DnsServers** elementu. Pak můžete použít **DnsServerRef** elementu k určení, které položky serveru DNS z elementu DnsServers se používají pro jiné síťové lokality ve vaší virtuální síti.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic.

Soubor konfigurace sítě může obsahovat následující prvky. Název každého prvku je propojen s stránku, která poskytuje další informace o nastavení hodnoty prvku.

> [!IMPORTANT]
> Informace o tom, jak nakonfigurovat soubor konfigurace sítě najdete v tématu [konfigurace virtuální sítě pomocí souboru konfigurace sítě](virtual-networks-using-network-configuration-file.md). Informace o každého prvku obsažených v konfiguračním souboru sítě, naleznete v tématu [Azure schéma konfigurace virtuální sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS Element](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> **Název** atribut **serveru DNS** prvek slouží pouze jako reference pro **DnsServerRef** elementu. To nepředstavuje název hostitele pro DNS server. Každý **serveru DNS** hodnota atributu musí být jedinečný mezi celé předplatné Microsoft Azure
> 
> 

[Element Sites se virtuální sítě](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Pokud chcete toto nastavení použijte pro element virtuální síťové lokality, se musí být dříve definován v elementu DNS. DnsServerRef *název* ve virtuální síťové lokality elementu musí odkazovat na název hodnota zadaná pro serveru DNS v DNS elementu *název*.
> 
> 

## <a name="next-steps"></a>Další postup
* Vysvětlení [schéma konfigurace Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093).
* Vysvětlení [schématu konfigurace služby Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Konfigurace virtuální sítě pomocí konfiguračních souborech sítě](virtual-networks-using-network-configuration-file.md).

