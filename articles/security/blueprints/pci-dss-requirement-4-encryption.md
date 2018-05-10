---
title: Azure platebních zpracování plán, podle kterého – požadavky na šifrování
description: PCI DSS požadavek 4
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6de3290fc2147e3c8ed63642b6e8470093898ef6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Požadavky na šifrování pro kompatibilní se standardem PCI DSS prostředí 
## <a name="pci-dss-requirement-4"></a>PCI DSS požadavek 4

**Šifrování přenosu dat držitele karty v otevřený, veřejných sítích**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

V sítích, které jsou snadno přístupné pomocí jednotlivcům se zlými úmysly musí být během přenosu šifrována citlivé informace. Nesprávně nakonfigurované bezdrátových sítí a ohrožení zabezpečení v ověřovací protokoly a starší verze šifrování nadále cíle škodlivý jednotlivcům, kteří tyto chyby zabezpečení získat oprávnění k přístupu do prostředí dat držitele karty zneužít.

## <a name="pci-dss-requirement-41"></a>PCI DSS požadavek 4.1

**4.1** používat silné šifrování a zabezpečení protokoly (například TLS, IPSEC, SSH atd.) k ochraně citlivých držitele karty dat během přenosu přes otevřené, veřejné sítě, včetně následujících:
- Přijímají se jenom důvěryhodné klíčů a certifikátů.
- Protokol v použití podporuje pouze zabezpečené verze nebo konfigurace.
- Síla šifrování je vhodné pro použití metody šifrování. 

> [!NOTE]
> Pokud se používá protokol SSL, předčasné TLS, musí být vyplněna požadavky uvedené v dodatku A2.
>
> Příklady otevřené, veřejné sítě patří, ale nejsou omezeny na:
> - Internet
> - Bezdrátové technologie, včetně 802.11 a Bluetooth
> - Mobilní technologií, například Global System for Mobile communications (GSM), Code dělení násobný přístup (CDMA)
> - Přepínač paketu obecné služby (GPRS)
> - Satelitní komunikace


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore je řešení PaaS, které implementuje silné šifrování pro nasazení následujícím způsobem:<br /><br />Aby splňoval požadavky šifrovaná data na rest, [Azure Storage](https://azure.microsoft.com/services/storage/) používá následující:<br /><br /><ul><li>[Šifrování služby úložiště Azure (SSE) pro Data v klidovém stavu](/azure/storage/storage-service-encryption)</li><li>SQL Database: Instanci SQL databáze PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).</li><li>[Azure Disk Encryption (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>Použití Azure Key Vault zarovnaná s Azure Government, PCI DSS a HIPAA požadavky.|



### <a name="pci-dss-requirement-411"></a>PCI DSS požadavek 4.1.1

**4.1.1** zajistěte bezdrátové sítě přenosu dat držitele karty, nebo připojené k prostředí dat držitele karty, použijte osvědčené postupy oboru (například IEEE 802.11i) k implementaci silné šifrování pro ověřování a přenos.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Bezdrátové sítě a SNMP nejsou implementované v řešení.|



## <a name="pci-dss-requirement-42"></a>PCI DSS požadavek 4.2

**4.2** nikdy odeslat nechráněný zvětšení pomocí technologie zasílání zpráv koncového uživatele (například e-mailu, rychlé zasílání zpráv SMS, konverzace, atd.).

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore nemá žádné zasílání zpráv řešení implementována, která může odesílat data nechráněné primární účet číslo (PANORAMOVÁNÍ).|



## <a name="pci-dss-requirement-43"></a>PCI DSS požadavek 4.3

**4.3** zajistěte, aby zásady zabezpečení a provozních postupů pro šifrování přenosů dat držitele karty jsou popsané v použití a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědný za dokumentaci a šifrování přenosů obsahující data držitele karty zákazníků.|




