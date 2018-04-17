---
title: Azure platebních zpracování plán, podle kterého – požadavky na heslo
description: PCI DSS požadavek 2
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 8fafc277d7410ab3f8f54b0defee127fd6ec2f4d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>Požadavky na heslo pro kompatibilní se standardem PCI DSS prostředí 
## <a name="pci-dss-requirement-2"></a>PCI DSS požadavek 2

**Nepoužívejte výchozí hodnoty poskytnuté dodavatelem systému hesel a dalších parametrů zabezpečení**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Jednotlivcům se zlými úmysly (externí i interní na entitu) často používají dodavatele výchozí hesla a další výchozí nastavení výrobce ohrozit systémy. Tato nastavení a hesla jsou dobře známé podle hacker komunit a jsou snadno určit prostřednictvím veřejné informace.

## <a name="pci-dss-requirement-21"></a>PCI DSS požadavek 2.1
 
**2.1** vždy změnit výchozí nastavení dodavatele a odebrání nebo zakázání nepotřebných výchozí účty **před** instalace systému v síti.
To platí pro všechna výchozí hesla, včetně, ale bez omezení, používaných verzí operačních systémů, softwaru, která poskytuje služby zabezpečení, aplikace a systému účty, terminály POS (POS), komunity síťový protokol SNMP (Simple Management) řetězců atd.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure Active Directory heslo požadavky zásad se vynutí pro nová hesla poskytl zákazníci v rámci portálu AADUX. Změny spouštěná zákazníka hesla pomocí samoobslužné služby vyžadují ověřování předchozí hesla. Resetování hesla správce je potřeba změnit při následné přihlášení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore vyžaduje, aby uživatelé používat sadu silná hesla pro všechny uživatele. Ukázku jsou povolené žádné účty ukázku nebo hostů.<br /><br />Bezdrátové sítě a SNMP nejsou implementované v řešení.|



### <a name="pci-dss-requirement-211"></a>PCI DSS požadavek 2.1.1

**2.1.1** bezdrátové připojení k prostředí dat držitele karty nebo přenosu dat držitele karty v prostředích, změňte všechny výchozí hodnoty bezdrátové dodavatele při instalaci, včetně, ale bez omezení, výchozí bezdrátové šifrovací klíče, hesla a Řetězce komunity protokolu SNMP.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Bezdrátové sítě a SNMP nejsou implementované v řešení.|



## <a name="pci-dss-requirement-22"></a>PCI DSS požadavek 2.2

**2.2** vyvíjet normami konfigurace pro všechny součásti systému. Zajistit, že těchto standardů vyřešit všechny chyby známé zabezpečení a jsou konzistentní s přijata oborových standardů posilování systému.
Zdroje přijata oborových standardů posilování systému může zahrnovat, ale nejsou omezeny na:
- Centrum pro zabezpečení Internetu (SNS)
- Mezinárodní organizace pro normalizaci (ISO)
- Správce auditu sítě zabezpečení (SAN) Institute
- National Institute of Standards technologie (NIST)

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Pro Microsoft Azure vyvíjí tým technické služeb zabezpečení OSSC normami konfigurace zabezpečení pro systémy v prostředí Microsoft Azure, které jsou konzistentní s přijata odvětví, posílení zabezpečení standardů. Tyto konfigurace jsou popsány v systému směrné plány a změny příslušné konfigurace se předávají ovlivněné týmy (např. nástroj týmu). Postupy jsou implementované k monitorování dodržování předpisů na základě standardů konfigurace zabezpečení. Konfigurace standardy zabezpečení pro systémy v prostředí Microsoft Azure jsou v souladu s normami přijata odvětví posílení zabezpečení a jsou kontrolovány nejméně jednou ročně. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje posílení zabezpečení všech služeb v oboru pro držitele karty data prostředí (CDE). <br /><br />Kromě toho nasadí Contoso Webstore [Azure Security Center](https://azure.microsoft.com/services/security-center/), který poskytuje centralizovanou zobrazení stavu zabezpečení všech vašich prostředků Azure. Na první pohled můžete ověřit příslušná bezpečnostní prvky jsou na místě a správně nakonfigurovaný, a můžete rychle zjistit všechny prostředky, které vyžadují pozornost.<br /><br />Contoso Webstore využívá Operations Management Suite do protokolu všechny změny systému. Log Analytics poskytuje rozsáhlé protokolování změn. Změny můžete zkontrolovat a ověřit přesnost. Podrobnější pokyny najdete v tématu [pokyny PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-221"></a>PCI DSS požadavek 2.2.1

**2.2.1** implementovat pouze jeden primární funkce na server, aby zabránil funkcí, které vyžadují různé úrovně zabezpečení z existujícího společně na stejném serveru. (Například webové servery, databázové servery a DNS by měla být implementována na samostatných serverech.) 

> [!NOTE]
> Kde technologie virtualizace, jsou používány, implementujte pouze jeden primární funkce za součást virtuálního systému.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore služby jsou nasazeny jako PaaS služby. Všechny služby jsou izolované a segmentovaným používání segmentace sítě.<br /><br />Contoso Webstore také používá [prostředí App Service (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) vynucovaného klíče postupy. Další informace najdete v tématu [pokyny PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-222"></a>PCI DSS požadavek 2.2.2

**2.2.2** povolit pouze nezbytné služby, protokoly, démoni, atd., podle potřeby pro funkci systému.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure softwarových a hardwarových konfiguracích jsou kontrolovány alespoň čtvrtletně identifikovat a odstranit všechny nepotřebné funkce, porty, protokoly a služby. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore služby jsou nasazeny jako PaaS služby. Všechny služby jsou izolované a segmentovaným používání segmentace sítě.<br /><br />Contoso Webstore také používá [prostředí App Service (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) vynucovaného klíče postupy. Další informace najdete v tématu [pokyny PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-223"></a>PCI DSS požadavek 2.2.3

**2.2.3** implementovat další funkce zabezpečení pro všechny požadované služby, protokoly nebo démoni, které se považují za nezabezpečené. 

> [!NOTE]
> Pokud se používá protokol SSL, předčasné TLS, musí být vyplněna požadavky uvedené v dodatku A2.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore služby jsou nasazeny jako PaaS služby. Všechny služby jsou izolované a segmentovaným používání segmentace sítě. Nasazení také poskytuje posílení zabezpečení všech služeb v oboru CDE. <br /><br />Kromě toho nasadí Contoso Webstore [Azure Security Center](https://azure.microsoft.com/services/security-center/), který poskytuje centralizovanou zobrazení stavu zabezpečení všech vašich prostředků Azure. Na první pohled můžete ověřit příslušná bezpečnostní prvky jsou na místě a správně nakonfigurovaný, a můžete rychle zjistit všechny prostředky, které vyžadují pozornost.<br /><br />Contoso Webstore také používá [prostředí App Service (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) vynucovaného klíče postupy. Další informace najdete v tématu [pokyny PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-224"></a>PCI DSS požadavek 2.2.4

**2.2.4** konfigurace parametrů zabezpečení systému, aby se zabránilo zneužití.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure zajistí, že jen oprávněný personál se můžou ke konfiguraci ovládacích prvků zabezpečení platformy Azure, pomocí vícefaktorového řízení přístupu a zdokumentovaných obchodních potřeb. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá AAD a RBAC AD ke správě parametrů zabezpečení jsou správně nasazena. Další informace najdete v tématu [pokyny PCI - Identity Management](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-225"></a>PCI DSS požadavek 2.2.5

**2.2.5** odeberte všechny nepotřebné funkce, jako jsou skripty, ovladače, funkce, subsystémy, systémy souborů a nepotřebné webové servery.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje dokumentaci na tom, jak jsou určeny hranice. Model hrozeb společnosti Contoso a diagram toku dat znázorňují všechny služby jsou používány a ovládací prvky, které jsou povolené.|



## <a name="pci-dss-requirement-23"></a>PCI DSS požadavek 2.3

**2.3** šifrování všechny mimo konzolu pro správu přístupu k používání silného šifrování. 

> [!NOTE]
> Pokud se používá protokol SSL, předčasné TLS, musí být vyplněna požadavky uvedené v dodatku A2.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zajistí, že používání silného šifrování v situacích, kdy přistupuje k infrastruktuře hypervisoru. Microsoft Azure také zajišťuje, aby byly zákazníkům, kteří používají Microsoft Azure Management Portal mít přístup k jejich konzoly služby nebo IaaS s silného šifrování. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukazuje, jak silného hesla, můžou se implementovat v řešení; všechny testy kromě toho lze provést ověření, zda že je implementováno šifrování v celé řešení.<br /><br />Contoso Webstore také používá [prostředí App Service (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) vynucovaného klíče postupy. Další informace najdete v tématu [pokyny PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



## <a name="pci-dss-requirement-24"></a>PCI DSS požadavek 2.4

**2.4** udržujte inventář systémových součástí, které jsou v oboru pro PCI DSS.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukázku PaaS řešení inventáře můžete zkontrolovat v zadané dokumentaci. Další informace najdete v tématu [pokyny PCI - řešení pro správu předinstalovaný](payment-processing-blueprint.md#management-solutions).|



## <a name="pci-dss-requirement-25"></a>PCI DSS požadavek 2.5

**2.5** zajistěte, aby zásady zabezpečení a provozní postupy pro správu od dodavatele výchozí hodnoty a dalších parametrů zabezpečení jsou popsané v použití a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje dokumentace, která poskytuje vhled do parametrů zabezpečení a dokumenty služby elementy. |



## <a name="pci-dss-requirement-26"></a>PCI DSS požadavek 2.6

**2.6** poskytovatelům sdíleného hostingu musí chránit data hostované prostředí a držitele karty každé entity. Tito zprostředkovatelé musí splňovat určité požadavky podle popisu v *příloha A: Další požadavky sběrnice PCI DSS pro sdílené hostování zprostředkovatele.*

**Odpovědnosti:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. Microsoft Azure není poskytovateli sdíleného hostingu. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici. Microsoft Azure není poskytovateli sdíleného hostingu.|




