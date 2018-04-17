---
title: Scénáře a příklady pro řízení předplatné | Microsoft Docs
description: Obsahuje příklady, jak implementovat zásady správného řízení předplatné Azure pro běžné scénáře.
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: e8fbeeb8-d7a1-48af-804f-6fe1a6024bcb
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 6bd4e9f6bbc5bba73b2c169b7f3c5931f30029e6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Příklady implementace Azure enterprise vygenerované uživatelské rozhraní
Toto téma obsahuje příklady, jak organizace může implementovat doporučení pro [vygenerované uživatelské rozhraní Azure enterprise](resource-manager-subscription-governance.md). Pro ilustraci osvědčené postupy pro běžné scénáře používá fiktivní společnost s názvem Contoso.

## <a name="background"></a>Pozadí
Contoso je, že po celém světě společnost, která poskytuje zásobovací řetězec řešení pro zákazníky v části všechno z modelu "Softwaru jako službu" zabalené modelu nasadit místně.  Po celém světě s centrech významné vývoj v Indie, USA a Kanada vyvíjejí softwaru.

Část ISV společnosti je rozdělena na několik nezávislých organizační jednotky, které spravovat produkty ve významné podniku. Jednotlivé obchodní jednotky má svou vlastní vývojáře, produktu správci a architekti.

Organizační jednotka služby technologie Enterprise (ETS) poskytuje funkce centralizované IT a spravuje několik datových center, kterých obchodních jednotek hostovat svých aplikací. Společně s správy datových center, organizace ETS obsahuje a spravuje centralizované spolupráce (například e-mailu a weby) a sítě nebo telefonní služby. Pro menší organizační jednotky, kteří nemají provozní pracovníci také spravují zákazníkem úlohy.

V tomto tématu se používají následující osoby:

* Dave je správce ETS Azure.
* Alice je společnosti Contoso ředitel vývoj v organizační jednotce, zadejte řetězec.

Contoso je potřeba vytvářet-obchodní aplikace a aplikace na straně zákazníka. Rozhodla se ke spuštění aplikace v Azure. Přečte Dave [zásad správného řízení doporučený předplatné](resource-manager-subscription-governance.md) tématu a je nyní připravena k implementaci doporučení.

## <a name="scenario-1-line-of-business-application"></a>Scénář 1:-obchodní aplikace
Contoso je vytváření systém správy zdrojového kódu (BitBucket), který se má použít vývojáři po celém světě.  Aplikace používá infrastrukturu jako službu (IaaS) pro hostování a skládá se z webových serverů a databázový server. Vývojáři přístup k serverům ve svých prostředích vývoj, ale nepotřebují přístup k serverům v Azure. Contoso ETS, které chcete povolit vlastníka aplikace a týmu ke správě aplikace. Aplikace je dostupná pouze při na podnikové síti společnosti Contoso. Dave je potřeba nastavit odběr pro tuto aplikaci. Předplatné bude v budoucnu také hostitelem jiných vývojáře softwaru.  

### <a name="naming-standards--resource-groups"></a>Standardy pro vytváření názvů & skupiny prostředků
Dave vytvoří odběr pro podporu nástroje pro vývojáře, které jsou společné pro všechny organizační jednotky. Zadá musí vytvořit smysluplný názvy pro skupiny, předplatné a prostředků (pro aplikace a sítě). Vytvoří následujících skupin pro předplatné a prostředků:

| Položka | Název | Popis |
| --- | --- | --- |
| Předplatné |Produkční DeveloperTools contoso ETS |Podporuje běžné nástroje pro vývojáře |
| Skupina prostředků |bitbucket produkčnímu rg |Obsahuje aplikace webového serveru a databázového serveru |
| Skupina prostředků |corenetworks produkčnímu rg |Obsahuje virtuální sítě a připojení brány site-to-site |

### <a name="role-based-access-control"></a>Řízení přístupu na základě role
Po vytvoření svého předplatného, Dave chce zajistit, aby příslušné týmy a vlastníci aplikace můžete přístup k jejich prostředkům. Dave rozpozná, že každý tým má jiné požadavky. Zadá využívá skupiny, které byly synchronizovány ze společnosti Contoso místní služby Active Directory (AD) do Azure Active Directory a poskytuje správnou úroveň přístupu k týmy.

Dave přiřadí následující role pro předplatné:

| Role | Přiřazeno | Popis |
| --- | --- | --- |
| [Vlastník](../role-based-access-control/built-in-roles.md#owner) |Spravované ID ze společnosti Contoso AD |Toto ID je řízena s těsně přístupu čas (JIT) pomocí nástroje Správa identit společnosti Contoso a zajistí, že je plně auditován přístup vlastníka předplatného |
| [Správce zabezpečení](../role-based-access-control/built-in-roles.md#security-manager) |Zabezpečení a rizika oddělení správy |Tato role umožňuje uživatelům prohlížet Azure Security Center a stav prostředků |
| [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) |Tým síťových |Tato role umožňuje společnosti Contoso tým síťových ke správě Site to Site VPN a virtuální sítě |
| *Vlastní role* |Majitel aplikace |Dave vytvoří roli, která umožňuje upravit prostředky ve skupině prostředků. Další informace najdete v tématu [vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md) |

### <a name="policies"></a>Zásady
Dave má tyto požadavky pro správu prostředků v předplatném:

* Nástroje pro vývoj nepodporují vývojáři po celém světě, mohl nechce zabránění uživatelům ve vytváření prostředků v libovolné oblasti. Však mohl musí vědět, kde jsou vytvářeny prostředky.
* Se zajímají náklady. Proto chce vlastníci aplikace zabránit ve vytváření zbytečně nákladné virtuálních počítačů.  
* Protože tato aplikace obsluhuje vývojáři v mnoha obchodních jednotek, chce označení každého prostředku s majitel firmy jednotky a aplikace. Pomocí těchto značek můžete ETS účtovat příslušné týmy.

Vytvoří následující [Azure zásady](../azure-policy/azure-policy-introduction.md):

| Pole | Efekt | Popis |
| --- | --- | --- |
| location |Audit |Audit vytváření prostředků v libovolné oblasti |
| type |odmítnout |Odepřít vytváření virtuálních počítačů G-Series |
| tags |odmítnout |Vyžadují aplikace vlastníka značky |
| tags |odmítnout |Vyžadovat náklady center značky |
| tags |Připojit |Append – název značky **organizační jednotce** a hodnota značky **ETS** ke všem prostředkům |

### <a name="resource-tags"></a>Značky prostředku
Dave plně chápe, že mu musí mít konkrétní informace na faktuře k identifikaci nákladového střediska pro implementaci BitBucket. Kromě toho Dave chce vědět, všechny prostředky, které vlastní ETS.

Přidá následující [značky](resource-group-using-tags.md) skupiny prostředků a prostředky.

| Název značky | Hodnota značky |
| --- | --- |
| ApplicationOwner |Jméno osoby, který spravuje tuto aplikaci |
| Nákladové středisko |Nákladové středisko skupiny, která platí za využití platformy Azure |
| Organizační jednotku |**ETS** (organizační jednotky přidruženou k odběru) |

### <a name="core-network"></a>Základní sítě
Contoso ETS informace o zabezpečení a rizika vedení zkontroluje společnosti navrhované plán přesunout aplikaci do Azure. Chtějí Ujistěte se, že aplikace není přístup k Internetu.  Dave má také vývojáře aplikací, které bude v budoucnu přesunuta do Azure. Tyto aplikace vyžadují veřejné rozhraní.  Pro splnění těchto požadavků, poskytuje interní a externí virtuální sítě a skupinu zabezpečení sítě pro omezení přístupu.

Vytvoří v následujících zdrojích informací:

| Typ prostředku | Název | Popis |
| --- | --- | --- |
| Virtual Network |interní virtuální sítě |Použít s aplikací BitBucket a je připojená přes ExpressRoute k podnikové síti společnosti Contoso.  Podsíť (`bitbucket`) poskytuje aplikaci s konkrétní prostor IP adres |
| Virtual Network |externí vnet |K dispozici pro budoucí aplikace, které vyžadují veřejné koncové body |
| Skupina zabezpečení sítě |bitbucket nsg |Zajišťuje, že prostor pro útoky této úlohy je minimalizován tím, že se připojení pouze na portu 443 pro podsíť tam, kde aplikace platný (`bitbucket`) |

### <a name="resource-locks"></a>Zámky prostředků
Dave rozpozná, že připojení z podnikové sítě společnosti Contoso na interní virtuální síť musí být chráněny v jakémkoli wayward skriptu nebo náhodného odstranění.

Vytvoří následující [prostředek zámku](resource-group-lock-resources.md):

| Typ zámku | Prostředek | Popis |
| --- | --- | --- |
| **CanNotDelete** |interní virtuální sítě |Zabrání uživatelům odstranit virtuální sítě nebo podsítě, ale nezabrání přidání nových podsítí |

### <a name="azure-automation"></a>Azure Automation
Dave má nic k automatizaci pro tuto aplikaci. I když mu vytvořili účet Azure Automation, mu zpočátku je používat.

### <a name="azure-security-center"></a>Azure Security Center
Správy služeb contoso IT musí rychle identifikovat a zpracování hrozeb. Chtějí také pochopit, jaké problémy můžou existovat.  

Ke splnění těchto požadavků, Dave umožňuje [Azure Security Center](../security-center/security-center-intro.md)a poskytuje přístup k roli správce zabezpečení.

## <a name="scenario-2-customer-facing-app"></a>Scénář 2: zákazníka směřujících aplikací
Vedení firmy v organizační jednotce, zadejte řetězec označila k většímu zapojení uživatelů zákazníků společnosti Contoso pomocí karty věrného různých možností. Alice tým musí vytvořit tuto aplikaci a rozhodne, že Azure zvyšuje jejich schopnost splňovat obchodních potřeb. Alice pracuje s Dave z ETS konfigurace obě předplatná pro vývoj a provoz této aplikace.

### <a name="azure-subscriptions"></a>Předplatná Azure
Dave přihlásí k portálu Azure Enterprise a uvidí, že oddělení řetězu napájení již existuje.  Ale tento projekt je první projekt vývoj tým řetězu napájení v Azure, Dave rozpozná potřebu nového účtu pro vývojový tým od Alice.  Vytvoří účet "R & D" pro svůj tým a přiřadí přístup k Alice. Alice přihlásí prostřednictvím portálu Azure a vytvoří dvě předplatná: jeden pro uložení vývoj servery a jeden pro uložení provozních serverů.  Jana řídí administrate standardy pro vytváření názvů, při vytváření k dispozici následující odběry:

| Použití předplatného | Název |
| --- | --- |
| Vývoj |Vývoj ResearchDevelopment LoyaltyCard contoso SupplyChain |
| Výroba |Produkční LoyaltyCard Operations contoso SupplyChain |

### <a name="policies"></a>Zásady
Dave a Alice popisují aplikace a identifikaci této aplikace jenom slouží zákazníků v oblasti Severní Ameriky.  Alice a její tým v úmyslu používat prostředí aplikace služby Azure a Azure SQL k vytvoření aplikace. Může potřebují k vytvoření virtuálních počítačů během vývoje.  Alice chce mít jistotu, že jeho vývojáři mají prostředky, které potřebují k prozkoumávání a prozkoumat problémy bez vyžádání v ETS.

Pro **vývoj předplatné**, vytvoří tyto zásady:

| Pole | Efekt | Popis |
| --- | --- | --- |
| location |Audit |Audit vytváření prostředků v libovolné oblasti |

Neomezovat typ sku, které uživatel může vytvořit při vývoji a nevyžaduje žádné značky pro všechny skupiny prostředků nebo prostředky.

Pro **produkční předplatné**, vytvoří následující zásady:

| Pole | Efekt | Popis |
| --- | --- | --- |
| location |odmítnout |Odepřít vytvoření všechny prostředky mimo datových centrech USA |
| tags |odmítnout |Vyžadují aplikace vlastníka značky |
| tags |odmítnout |Vyžadovat oddělení značky |
| tags |Připojit |Značka připojit ke každé skupině prostředků, který určuje produkční prostředí |

Neomezovat typ sku, které uživatel může vytvořit v produkčním prostředí.

### <a name="resource-tags"></a>Značky prostředku
Dave plně chápe, že mu musí mít specifické informace k identifikaci správné obchodních skupin pro fakturaci a vlastnictví. Definuje značky prostředku pro skupiny prostředků a prostředky.

| Název značky | Hodnota značky |
| --- | --- |
| ApplicationOwner |Jméno osoby, který spravuje tuto aplikaci |
| Oddělení |Nákladové středisko skupiny, která platí za využití platformy Azure |
| EnvironmentType |**Produkční** (i když předplatné zahrnuje **produkční** v názvu, včetně tato značka umožňuje snadnou identifikaci při vyhledávání na prostředky na portálu nebo na kusovníku) |

### <a name="core-networks"></a>Základní sítě
Contoso ETS informace o zabezpečení a rizika vedení zkontroluje společnosti navrhované plán přesunout aplikaci do Azure. Chtějí, aby se zajistilo správně izolované a chráněné v síti DMZ aplikaci věrného karta.  Ke splnění tohoto požadavku, vytvořte Dave a Alice externí virtuální síť a skupinu zabezpečení sítě izolovat aplikaci věrného karta z podnikové sítě Contoso.  

Pro **vývoj předplatné**, vytvoří:

| Typ prostředku | Název | Popis |
| --- | --- | --- |
| Virtual Network |interní virtuální sítě |Slouží Contoso věrného karty vývojového prostředí a je připojená přes ExpressRoute k podnikové síti společnosti Contoso |

Pro **produkční předplatné**, vytvoří:

| Typ prostředku | Název | Popis |
| --- | --- | --- |
| Virtual Network |externí vnet |Hostuje aplikaci věrného karta a není připojené přímo k ExpressRoute společnosti Contoso. Kód se instaluje prostřednictvím jejich systému zdrojového kódu přímo do služby PaaS |
| Skupina zabezpečení sítě |loyaltycard nsg |Zajišťuje, že prostor pro útoky této úlohy je minimalizován povolením jenom v vázané na komunikaci na portu TCP 443.  Contoso je také prošetřování pomocí brány Firewall webových aplikací pro zvýšení ochrany |

### <a name="resource-locks"></a>Zámky prostředků
Dave a Alice udělit a pokud se rozhodnete přidat zámky prostředků na některé z klíčových prostředků v prostředí, aby se zabránilo nechtěnému odstranění během vyvolání chybové kód nabízená.

Uživatel vytvořit následující uzamčení:

| Typ zámku | Prostředek | Popis |
| --- | --- | --- |
| **CanNotDelete** |externí vnet |Chcete-li zabránit neoprávněným osobám v odstranění virtuální sítě nebo podsítě. Zámek nezabrání přidání nových podsítí |

### <a name="azure-automation"></a>Azure Automation
Alice a její tým vývoj mají rozsáhlé runbooky ke správě prostředí pro tuto aplikaci. Sady runbook povolit pro přidávání a odstraňování uzlů pro aplikace a další úlohy DevOps.

Pokud chcete použít tyto sady runbook, umožňují [automatizace](../automation/automation-intro.md).

### <a name="azure-security-center"></a>Azure Security Center
Správy služeb contoso IT musí rychle identifikovat a zpracování hrozeb. Chtějí také pochopit, jaké problémy můžou existovat.  

Ke splnění těchto požadavků, umožňuje Dave Azure Security Center. Mu zajišťuje, že je monitorování prostředků Azure Security Center a poskytuje přístup k týmy DevOps a zabezpečení.

## <a name="next-steps"></a>Další postup
* Další informace o vytváření šablon Resource Manageru, najdete v části [osvědčené postupy pro vytváření šablon Azure Resource Manager](resource-manager-template-best-practices.md).
