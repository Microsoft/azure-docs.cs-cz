---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361528"
---
### <a name="database-tier"></a>Databázová vrstva

Úroveň Database obsahuje instance databáze pro aplikaci. Databáze může být buď Oracle DB, Oracle RAC nebo Oracle Exadata Database system. 

Pokud je možné použít oracle DB, může být instance databáze nasazena v Azure prostřednictvím iniciál Oracle DB dostupných na Azure Marketplace. Případně můžete použít propojení mezi Azure a OCI k nasazení Oracle DB v modelu PaaS na OCI.

V případě oracle RAC můžete nasadit Oracle RAC na Azure CloudSimple v modelu IaaS nebo v OCI v modelu PaaS. Doporučujeme používat dvounosý systém RAC. 

A konečně, pro systémy Exadata použijte propojení OCI a nasaďte systém Exadata v OCI. Předchozí diagram architektury výše ukazuje exadata systém usazené v OCI ve dvou podsítích.

Pro produkční scénáře nasaďte více instancí databáze ve dvou zónách dostupnosti (pokud se nasazujete v Azure) nebo ve dvou doménách dostupnosti (v OCI). Pomocí služby Oracle Active Data Guard synchronizujte primární a pohotovostní databáze.

Úroveň databáze přijímá pouze požadavky ze střední vrstvy. Doporučujeme nastavit skupinu zabezpečení sítě (seznam zabezpečení při nasazení databáze v oci) tak, aby z administrativních důvodů povolovala pouze požadavky na portu 1521 ze střední vrstvy a portu 22 z bastionového serveru.

Pro databáze nasazené v OCI musí být samostatná virtuální cloudová síť nastavena s dynamickou směrovací bránou (DRG), která je připojena k okruhu FastConnect.