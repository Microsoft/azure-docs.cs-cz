---
title: Azure platebních zpracování plán, podle kterého – požadavky na bránu Firewall
description: PCI DSS požadavek 1
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6f82c828b7cd878cc9ed9f42a4bf63d0d7aedb16
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Požadavky na bránu firewall pro kompatibilní se standardem PCI DSS prostředí 
## <a name="pci-dss-requirement-1"></a>PCI DSS požadavek 1

**Instalaci a údržbě konfiguraci brány firewall k ochraně dat držitele karty**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Brány firewall jsou zařízení, která řídí provoz počítače povolená mezi sítěmi entity (interní) a nedůvěryhodným sítím (external), jakož i provoz do a z více citlivých prostor v rámci entity interní důvěryhodné sítě. Prostředí dat držitele karty je příkladem citlivější oblasti v rámci entity důvěryhodné síti.
Brána firewall zkoumá veškerý přenos v síti a blokuje tyto přenosy, které nesplňují kritéria zadaná zabezpečení.
Všechny systémy musí být chráněny před neoprávněným přístupem z nedůvěryhodné sítě, jestli zadávání systému přes Internet jako elektronické obchodování, zaměstnanec přístup k Internetu prostřednictvím stolních počítačů a e-mailového přístupu zaměstnanců, vyhrazené připojení jako Business-to-business připojení přes bezdrátové sítě nebo prostřednictvím jiné zdroje. Často zdánlivě zanedbatelný cesty do a z nedůvěryhodných sítích můžete zadat počet nechráněné cest do klíče systémů. Brány firewall jsou klíče ochranný mechanismus pro všechny síťové počítače.
Dalších komponent systému poskytnout funkci brány firewall, tak dlouho, dokud splňují minimální požadavky na brány firewall, jak jsou definovány v požadavku 1. Pokud dalších komponent systému se používají v prostředí datového držitele karty zajistit funkci brány firewall, tato zařízení musí být zahrnut v rámci oboru a hodnocení požadavek 1.

## <a name="pci-dss-requirement-11"></a>PCI DSS požadavek 1.1

**1.1** navázání a implementujte směrovače a brány firewall konfigurace standardů, které patří (viz 1.1.1 prostřednictvím 1.1.7).


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje fungující brána firewall může z CDE pomocí izolace PaaS a implementaci služby App Service Environment zajistíte chráněný CDE příchozí a odchozí data.<br /><br />[Prostředí App Service (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) je plán služeb Premium použít kvůli dodržování předpisů. Další informace o ovládacích prvcích App Service Environment a konfigurace najdete v tématu [pokyny PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-111"></a>PCI DSS požadavek 1.1.1

**1.1.1** formální proces pro schválení a testování všech síťových připojení a změny konfigurace směrovače a brány firewall


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Instance Contoso Webstore vytvoří model DevOps CI nebo CD pro zajištění, že všechny změny jsou správně spravovány. Log Analytics poskytuje rozsáhlé protokolování změn. Změny můžete zkontrolovat a ověřit přesnost. Podrobnější pokyny najdete v tématu [pokyny PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).<br /><br />[Azure Security Center](https://azure.microsoft.com/services/security-center/) poskytuje centralizovanou zobrazení stavu zabezpečení všech vašich prostředků Azure. Na první pohled můžete ověřit příslušná bezpečnostní prvky jsou na místě a správně nakonfigurovaný, a můžete rychle zjistit všechny prostředky, které vyžadují pozornost.|



### <a name="pci-dss-requirement-112"></a>PCI DSS požadavek 1.1.2

**1.1.2** aktuální síťový diagram, který identifikuje všechna připojení mezi prostředím data držitele karty a další sítě, včetně žádné bezdrátové sítě.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Naleznete Contoso Webstore referenční architektuře a designu dokumentaci k dispozici jako součást instalace vzoru řešení.|



### <a name="pci-dss-requirement-113"></a>PCI DSS požadavek 1.1.3

**1.1.3** aktuální diagram, který zobrazuje všechny držitele karty data proudí mezi systémy a sítě

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Viz Contoso Webstore diagramu toku dat a Model hrozeb.|



### <a name="pci-dss-requirement-114"></a>PCI DSS požadavek 1.1.4

**1.1.4** požadavky na brány firewall na každém připojení k Internetu a mezi všechny demilitarizovaná zóna (DMZ) a interní síti zóny

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Aktivuje se hranic ochranu zařízení, jako jsou brány, seznamy ACL sítě a brány firewall aplikace pro řízení komunikace na externí i interní hranice na úrovni platformy Microsoft Azure. Zákazník nakonfiguruje pak tyto požadavky a specifikace. Microsoft Azure filtruje komunikace, když přicházející do platformy. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje hraniční sítě pomocí izolace PaaS a implementaci služby App Service Environment zajistíte chráněný CDE příchozí a odchozí data.<br /><br />[Prostředí App Service (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) je plán služeb Premium použít kvůli dodržování předpisů. Další informace o ovládacích prvcích App Service Environment a konfigurace najdete v tématu [pokyny PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-115"></a>PCI DSS požadavek 1.1.5

**1.1.5** popis skupiny, role a odpovědnosti pro správu síťových součástí

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá [řízení řízení přístupu (RBAC)](/azure/role-based-access-control/role-assignments-portal) izolovat uživatelské role. RBAC umožňuje přesněji správu cílených přístupu pro Azure. Existují konkrétní konfigurace pro předplatné přístup a přístup Azure Key Vault.|



### <a name="pci-dss-requirement-116"></a>PCI DSS požadavek 1.1.6

**1.1.6** dokumentaci obchodního oprávnění a schválení pro použití služeb, protokoly a porty povolena, včetně dokumentace implementována pro tyto protokoly považuje za nezabezpečené funkce zabezpečení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore otevře pouze požadované porty a protokoly v rámci RA návrhu. Podrobnosti o tok dat si můžete prohlédnout ve model diagramu toku dat a hrozeb.|



### <a name="pci-dss-requirement-117"></a>PCI DSS požadavek 1.1.7

**1.1.7** požadavek na zkontrolujte pravidlo brány firewall a směrovač nastaví alespoň jednou za šest měsíců

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Ve společnosti Contoso Webstore jsou kontrolovány sady pravidel brány firewall zajistit, že žádná pravidla nepotřebné a nepoužívané jsou zahrnuty. Standardně se ukázku nasazuje s nejnižší oprávnění, nejmenší nároky cestu.|



## <a name="pci-dss-requirement-12"></a>PCI DSS požadavek 1.2

**1.2** konfigurace směrovače a brány firewall, které omezují připojení mezi nedůvěryhodnými sítěmi a všechny součásti systému v prostředí datového držitele karty sestavení. 

> [!NOTE]
> "Nedůvěryhodné sítě" je žádné síti, která je mimo sítě patřící do entity v části kontrolní nebo což je mimo entity možnost řídit nebo spravovat.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | CDE Contoso Webstore je definována v dokumentaci k nasazení a Vzdálená pomoc. Návrh je odepřen nedůvěryhodným sítím.|



### <a name="pci-dss-requirement-121"></a>PCI DSS požadavek 1.2.1

**1.2.1** omezit příchozí a odchozí přenosy, které jsou nezbytné pro držitele karty data prostředí a konkrétně Odepřít všechny ostatní přenosy.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | CDE Contoso Webstore je definována v dokumentaci k nasazení a Vzdálená pomoc. Návrh je odepřen nedůvěryhodným sítím. Ukázkové společnosti Contoso Webstore nakonfiguruje bránu firewall aplikace Microsoft Azure umožňuje pouze zadaný rozsah IP adres pro přístup ke službám Microsoft Azure. Contoso Webstore poskytuje brána firewall Odepřít všechny vůbec CDE hranice. Všechny konfigurace se provádí při počátečním nastavení nasazení.

> [!NOTE]
> Prostředí App Service (App Service Environment) se používá v tomto řešení izolovat CDE ale je nezbytné, aby vaše kvalifikovaný zabezpečení posuzovatel (QSA) vyhodnotí toto řešení, protože App Service Environment implementuje DMZ izolace, která umožňuje odchozí připojení k být provedené App Service Environment. PCI-DSS vyžaduje, je nutné blokovat všechny příchozí a odchozí připojení, které nejsou potřeba. Pro App Service Environment pracovat správně, App Service Environment bude navázat odchozí připojení podle potřeby definovaným v ["Sítě důležité informace týkající se služby App Service Environment"](/azure/app-service/app-service-environment/network-info). Zákazníci by měly vyhodnotit odchozí připojení s vaší QSA před nasazením řešení do provozního prostředí k zajištění, že se bude splňovat požadavky. |



### <a name="pci-dss-requirement-122"></a>PCI DSS požadavek 1.2.2

**1.2.2** zabezpečeného a synchronizaci směrovač konfigurační soubory.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje konfigurace synchronizace pro řízení nativní sítě Microsoft Azure.|



### <a name="pci-dss-requirement-123"></a>PCI DSS požadavek 1.2.3

**1.2.3** hraniční brány firewall mezi všechny bezdrátové sítě a prostředí, data držitele karty instalaci a konfiguraci těchto bránu firewall, aby odepřít nebo, pokud provoz je nutné pro obchodní účely povolit pouze provoz mezi bezdrátovou oprávnění prostředí a prostředí, data držitele karty.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore nemá žádné povolené funkce nebo bezdrátové řešení.|



## <a name="pci-dss-requirement-13"></a>PCI DSS požadavek 1.3

**1.3** zakázat přímé veřejný přístup mezi Internetu a jakékoli součásti systému v prostředí datového držitele karty.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure aktivuje zařízení ochrany založené na síti a založená na hostiteli hranic, jako jsou brány firewall, nástroje pro vyrovnávání zatížení a seznamy ACL. Tato zařízení používají mechanismy, například izolace sítě VLAN, NAT a filtrování samostatné zákazníka provoz z Internetu a přenosy správy paketů. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje v době nasazení, konfigurace brány firewall Azure aplikací povolit pouze zadaný rozsah IP adres pro přístup k webu, zahrnout do své CDE u chráněných virtuálních počítačích Azure.|



### <a name="pci-dss-requirement-131"></a>PCI DSS požadavek 1.3.1

**1.3.1** implementovat DMZ omezit příchozí provoz jenom součásti systému, které poskytují ověřených služeb veřejně přístupný, protokoly a porty.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Implementace Contoso Webstore jeho DMZ zajistí, že CDE se může připojit pouze ověřených služeb.|



### <a name="pci-dss-requirement-132"></a>PCI DSS požadavek 1.3.2

**1.3.2** limit příchozí přenosy z Internetu pro IP adresy v hraniční síti.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Implementace Contoso Webstore jeho DMZ zajistí, že CDE se může připojit pouze ověřených služeb.|



### <a name="pci-dss-requirement-133"></a>PCI DSS požadavek 1.3.3

**1.3.3** implementace ochranu proti falšování opatření ke zjištění a blokování forged zdrojové IP adresy z vstupem do sítě. (Například blokovat přenosy pocházející z Internetu adresu vnitřního zdroje.)

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementuje filtrování Pokud chcete zabránit podvodná provoz a omezit příchozí a odchozí provoz na komponenty platformy důvěryhodné sítě. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-134"></a>PCI DSS požadavek 1.3.4

**1.3.4** neumožňují neoprávněným odchozí provoz z prostředí dat držitele karty k Internetu.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Architektura Contoso Webstore brání neoprávněným odchozí provoz z prostředí v oboru k Internetu. To se provádí konfigurace odchozí přenosy seznamy ACL pro schválené porty a protokoly v Microsoft Azure. Tyto ovládací prvky zahrnovat přístup do CDE v databázi systému SQL Server. <br /><br />Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-135"></a>PCI DSS požadavek 1.3.5

**1.3.5** povolení pouze "navázat" připojení do sítě.


**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementuje filtrování Pokud chcete zabránit podvodná provoz a omezit příchozí a odchozí provoz na komponenty platformy důvěryhodné sítě. K oddělení provozu zákazníka z provozu správy je rozdělen sítě Microsoft Azure. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-136"></a>PCI DSS požadavek 1.3.6

**1.3.6** místní systému součásti, které ukládají data držitele karty (například databáze) v zóně interní síti, oddělené od hraniční síti a dalších nedůvěryhodným sítím.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure používá síť oddělení a NAT pro oddělení provozu zákazníka z provozu správy. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Architektura Contoso Webstore brání neoprávněným odchozí provoz z prostředí v oboru k Internetu. To se provádí konfigurace odchozí přenosy seznamy ACL pro schválené porty a protokoly v Microsoft Azure. Tyto ovládací prvky zahrnovat přístup do CDE v databázi systému SQL Server. <br /><br />Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-137"></a>PCI DSS požadavek 1.3.7

**1.3.7** neprozradili privátních IP adres a informace o směrování neoprávněným osobám. 

> [!NOTE]
> Metody pro skrývat IP adresování může zahrnovat, ale nejsou omezeny na:
> - Překlad síťových adres (NAT).
> - Umístění serverů obsahující data držitele karty za servery proxy nebo brány firewall.
> - Odebrání nebo filtrování inzerování trasy pro privátní sítě, které využívají zaregistrován adresování.
> - Interní použití RFC1918 adresního prostoru místo registrované adresy.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure používá překládání adres (NAT) a sítě oddělení pro oddělení provozu zákazníka z provozu správy. Azure zařízení je jednoznačně identifikováno jejich UUID a ověření pomocí protokolu Kerberos. Azure spravované sítě, kterou zařízení se identifikují podle RFC 1918 IP řešit. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore umístí všechna data držitele karty za servery proxy nebo brány firewall a interně používá RFC1918 adresní prostor.|



## <a name="pci-dss-requirement-14"></a>PCI DSS požadavek 1.4

**1.4** instalace softwaru nebo ekvivalentní funkci osobní brány firewall na všechny přenosné výpočetní zařízení (včetně společnosti nebo zaměstnancům), připojení k Internetu při mimo síť (například přenosné počítače používají zaměstnanci), a které se také používají pro přístup k CDE. Konfigurace brány firewall (nebo podobnou) zahrnují:-jsou definovány specifické nastavení.
- Běží aktivně osobní brány firewall (nebo ekvivalentní funkce).
- Osobní brány firewall (nebo ekvivalentní funkce) není měnit uživatelé přenosných výpočetní zařízení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore neposkytuje ochranu koncového uživatele zařízení. [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) lze použít ke správě mobilních zařízení pracovníky používá pro přístup k firemním datům.|



## <a name="pci-dss-requirement-15"></a>PCI DSS požadavek 1.5

**1.5** zajistěte, aby zásady zabezpečení a provozní postupy pro správu brány firewall jsou popsané v použití a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje v době nasazení, konfigurace brány firewall Azure aplikací povolit pouze zadaný rozsah IP adres pro přístup k webu, zahrnout do své CDE u chráněných virtuálních počítačích Azure.|




