---
title: Zadání nastavení DNS v konfiguračním souboru služby | Dokumentace Microsoftu
description: Zadání vlastní nastavení DNS pro virtuální síť je používán konfigurační soubor služby
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 0ac488a67d8b9debf6539d199395997cf44cf1e4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247173"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Zadání nastavení DNS v konfiguračním souboru služby
## <a name="dns-elements"></a>Prvky DNS
Konfigurační soubor služby může obsahovat prvek DnsServers se seznamem adres IPv4 pro servery systému DNS (Domain Name), které bude služba používat. Nastavení v konfiguračním souboru služby mají přednost před nastavením v konfiguračním souboru sítě. Další informace najdete v tématu [schématu konfigurace služby Azure (.cscfg souboru)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration element**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> **Název** atribut **serveru DNS** element slouží jen jako referenční název. To nepředstavuje název hostitele pro DNS server. Každý **serveru DNS** hodnota atributu musí být jedinečný mezi celé předplatné Microsoft Azure.
> 
> 

## <a name="see-also"></a>Viz také
[Schéma konfigurace služby Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schéma konfigurace Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurace virtuální sítě pomocí konfiguračních souborech sítě](https://go.microsoft.com/fwlink/?LinkId=248094)

[Informace o nastavení virtuální sítě v portálu pro správu](https://go.microsoft.com/fwlink/?LinkId=248092)

