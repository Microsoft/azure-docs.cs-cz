---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361528"
---
### <a name="database-tier"></a>Databázová vrstva

Databázová vrstva obsahuje databázové instance aplikace. Databáze může být buď Oracle DB, Oracle RAC nebo Oracle Exadata Database System. 

Pokud se rozhodnete použít Oracle DB, může být instance databáze nasazená v Azure prostřednictvím Oracle DBch imagí, které jsou k dispozici na Azure Marketplace. Alternativně můžete použít propojení mezi Azure a OCI k nasazení Oracle DB v modelu PaaS na OCI.

Pro Oracle RAC můžete nasadit Oracle RAC pro Azure CloudSimple v modelu IaaS nebo v OCI v modelu PaaS. Doporučuje se použít systém RAC se dvěma uzly. 

Nakonec pro systémy Exadata použijte rozhraní OCI Interconnect a nasaďte systém Exadata v rozhraní OCI. Předchozí diagram architektury výše ukazuje systém Exadata nasazený v rozhraní OCI napříč dvěma podsítěmi.

V produkčních scénářích nasaďte více instancí databáze ve dvou zónách dostupnosti (při nasazení v Azure) nebo ve dvou doménách dostupnosti (v OCI). Použijte Oracle Active Data Guard k synchronizaci primárních a pohotovostních databází.

Databázová vrstva přijímá pouze požadavky ze střední vrstvy. Doporučuje se nastavit skupinu zabezpečení sítě (seznam zabezpečení při nasazení databáze v rozhraní OCI), aby bylo možné pouze požadavky na portu 1521 od střední vrstvy a port 22 z bastionu serveru z důvodů správy.

V případě databází nasazených v OCI musí být samostatná virtuální cloudová síť nastavená pomocí brány DRG (Dynamic Routing Gateway), která je připojená k vašemu okruhu FastConnect.