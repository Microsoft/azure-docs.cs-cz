---
title: Přehled App Service Environment
description: Přehled App Service Environment
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fbc498fcd654d16936c2548528e2600be68a2ad9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663620"
---
# <a name="app-service-environment-overview"></a>Přehled App Service Environment 

> [!NOTE]
> Tento článek se týká App Service Environment V3 (Preview).
> 

Azure App Service Environment je funkce služby Azure App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění aplikací Azure App Service ve velkém měřítku. Tato funkce umožňuje hostovat vaše:

- Webové aplikace pro Windows
- Webové aplikace pro Linux
- Kontejnery Dockeru
- Funkce

Služby App Service Environment (ASE) jsou vhodné pro úlohy aplikací, které mají tyto požadavky:

- Vysoké měřítko.
- Izolace a bezpečný přístup k síti
- Využití velkého množství paměti
- Vysoké požadavky za sekundu (RPS). V jedné oblasti Azure nebo v několika oblastech Azure můžete udělat víc služby ase. Díky této flexibilitě je služby ASE ideální pro horizontální škálování bezstavových aplikací s vysokým požadavkem na RPS.

Hostitelské aplikace pro pomocného uživatele jenom od jednoho zákazníka a tak učinit v jednom z jeho virtuální sítě. Zákazníci mají podrobnou kontrolu nad příchozími i odchozími přenosy v síti aplikací. Aplikace můžou prostřednictvím sítí VPN vytvářet vysokorychlostní zabezpečená připojení k místním firemním prostředkům.

ASEv3 obsahuje svou vlastní cenovou úroveň izolovanou v2.
App Service prostředí V3 poskytují okolí k ochraně vašich aplikací v podsíti vaší sítě a poskytuje vlastní privátní nasazení Azure App Service.
K horizontálnímu škálování se dá použít víc služeb ASE. U aplikací využívajících služby ASE mají je přístup chráněný branami v podobě upstreamovými zařízeními, jako jsou brány firewall webových aplikací (WAF). Další informace najdete v článku Firewall webových aplikací (WAF).

## <a name="usage-scenarios"></a>Scénáře použití

App Service Environment má mnoho případů použití, včetně:

- Interní obchodní aplikace
- Aplikace, které vyžadují více než 30 instancí ASP
- Jeden tenantový systém pro splnění požadavků na vnitřní dodržování předpisů nebo zabezpečení
- Hostování aplikace izolované od sítě
- Vícevrstvé aplikace

K dispozici je řada funkcí sítě, které umožňují aplikacím ve více tenantůch App Service získat přístup k síťovým izolovaným prostředkům nebo se může stát izolací sítě. Tyto funkce jsou povolené na úrovni aplikace.  Pomocí pomocného mechanismu se v aplikacích žádná další konfigurace, které by měly být ve virtuální síti. Aplikace se nasadí do izolovaného prostředí sítě, které už je ve virtuální síti. Nad aplikacemi hostujícími izolované síťové služby se jedná i o jediný klientský systém. Pomocí pomocného mechanismu pro řízení neexistují žádní další zákazníci. Pokud opravdu potřebujete kompletní příběh pro izolaci, můžete taky získat svůj přístup k vašemu mechanismu pro nasazování na vyhrazený hardware. Mezi hostováním aplikace izolované od sítě, s jednou tenantů a možností 

## <a name="dedicated-environment"></a>Vyhrazené prostředí
Pomocného mechanismu je vyhrazené výhradně pro jedno předplatné a může hostovat 200 App Service instancí plánu. Tento rozsah může zahrnovat 100 instancí v rámci jednoho plánu služby App Service, ale i 100 plánů služby App Service s jednou instancí a veškeré další kombinace.

Služba ASE se skládá z front-endů a pracovních procesů. Front-endy zodpovídají za ukončení protokolu HTTP/HTTPS a automatické vyrovnávání zatížení požadavků aplikací ve službě ASE. Front-endy se přidávají automaticky při horizontálním škálování plánů služby App Service ve službě ASE.

Pracovní procesy jsou role, které jsou hostiteli zákaznických aplikací. Pracovní procesy jsou dostupné ve třech pevných velikostech:

- 2 vCPU/8 GB paměti RAM
- Čtyři vCPU/16 GB paměti RAM
- 8 vCPU/32 GB RAM

Zákazníci nemusí spravovat front-endy a pracovní procesy. Veškerá infrastruktura je automaticky. Při vytváření nebo škálování plánů služby App Service ve službě ASE se podle potřeby přidává nebo odebírá nutná infrastruktura.

Za izolované instance App Service plánu v2 se účtují poplatky. Pokud ve vašem pomocném mechanismu pro pořízení nemáte žádné plány App Service, účtují se vám poplatky za to, že jste naplánovali jeden App Service s jednou instancí obou základních pracovních procesů.

## <a name="virtual-network-support"></a>Podpora virtuální sítě
Funkce pomocného mechanismu je nasazení Azure App Service přímo do Azure Resource Manager virtuální sítě zákazníka. V podsíti virtuální sítě vždy existuje pomocného mechanismu služby. Pomocí funkcí zabezpečení virtuálních sítí můžete řídit příchozí a odchozí síťovou komunikaci svých aplikací.

Skupiny zabezpečení sítě omezují příchozí síťovou komunikaci na podsíť, ve které se nachází služba ASE. Pomocí skupin zabezpečení sítě můžete spouštět aplikace za upstreamovými zařízeními a službami, jako jsou brány WAF a síťoví poskytovatelé SaaS.

Aplikace také často potřebují přístup k firemním prostředkům, jako jsou třeba interní databáze a webové služby. Pokud nasadíte službu ASE ve virtuální síti, která má připojení VPN k místní síti, aplikace v této službě ASE můžou získat přístup k místním prostředkům. Tato možnost platí bez ohledu na to, jestli se jedná o síť VPN typu site-to-site, nebo o síť VPN Azure ExpressRoute.

## <a name="preview"></a>Preview
App Service Environment v3 je ve verzi Public Preview.  Během průběhu verze Preview se přidávají některé funkce. Aktuální omezení ASEv3 zahrnují:

- Neschopnost škálovat plán App Service přesahující pět instancí
- Neschopnost získat kontejner z privátního registru
- Neschopnost aktuálně nepodporovaných App Service funkcí přejít přes virtuální síť zákazníka
- Žádný model externího nasazení s dostupným koncovým bodem Internetu
- Žádná podpora příkazového řádku (AZ CLI a PowerShell)
- Žádná možnost upgradu z ASEv2 na ASEv3
- Žádná podpora FTP
- Žádná podpora pro některé funkce App Service procházejí prostřednictvím virtuální sítě zákazníka. Zálohování, obnovení, Key Vault odkazů v nastavení aplikace, použití privátního registru kontejnerů a diagnostické protokolování do úložiště nebude fungovat s koncovými body služby nebo soukromými body.
    
### <a name="asev3-preview-architecture"></a>Architektura ASEv3 ve verzi Preview
V ASEv3 Preview bude přihlášený pomocí privátních koncových bodů podporovat příchozí provoz. Privátní koncový bod bude nahrazen nástroji pro vyrovnávání zatížení od GA. Ve verzi Preview nebude přihlášený modul pro přístup k Internetu integrován v podpoře pro koncový bod dostupný v Internetu. Pro takový účel můžete přidat Application Gateway. Pomocného mechanismu potřebuje prostředky ve dvou podsítích.  Příchozí přenosy budou procházet soukromým koncovým bodem. Soukromý koncový bod může být umístěn v jakékoli podsíti, pokud má dostupnou adresu, kterou mohou používat privátní koncové body.  Odchozí podsíť musí být prázdná a musí být delegovaná na Microsoft. Web/hostingEnvironments. I když ho služba pomocného mechanismu používá, nejde odchozí podsíť použít pro cokoli jiného.

V případě ASEv3 neexistují žádné příchozí ani odchozí požadavky na síť v podsíti pomocného mechanismu služby. Provoz můžete řídit pomocí skupin zabezpečení sítě a směrovacích tabulek, a to pouze v případě, že bude ovlivněn provoz vaší aplikace. Neodstraňujte privátní koncový bod přidružený k vašemu pomocnému programu, protože tuto akci nejde vrátit zpět. Pro všechny aplikace v pomocném mechanismu služby se používá privátní koncový bod používaný pro pomocného bodu služby. 
