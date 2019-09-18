---
title: Zadání nastavení DNS v konfiguračním souboru služby | Microsoft Docs
description: určení vlastních nastavení DNS pomocí konfiguračního souboru služby pro virtuální síť
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: f27802d76a8b94a0d5f1eb0c35fd55c93712e557
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059069"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Zadání nastavení DNS v konfiguračním souboru služby
## <a name="dns-elements"></a>Prvky DNS
Konfigurační soubor služby může obsahovat element DnsServers se seznamem IPv4 adres pro servery DNS (Domain Name System), které bude služba používat. Nastavení v konfiguračním souboru služby mají přednost před nastavením v souboru konfigurace sítě. Další informace najdete v tématu [schéma konfigurace služby Azure (soubor. cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Element NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> Atribut **Name** v elementu **serveru DNS** je použit pouze jako název odkazu. Nepředstavuje název hostitele pro server DNS. Každá hodnota atributu **serveru DNS** musí být v rámci celého předplatného Microsoft Azure jedinečná.
> 
> 

## <a name="see-also"></a>Viz také
[Schéma konfigurace služby Azure (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schéma konfigurace Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurace Virtual Network pomocí konfiguračních souborů sítě](https://go.microsoft.com/fwlink/?LinkId=248094)

[Informace o nastaveních Virtual Network v Portál pro správu](https://go.microsoft.com/fwlink/?LinkId=248092)

