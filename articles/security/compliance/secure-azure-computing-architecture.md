---
title: Zabezpečení Azure výpočetní architekturu
description: Toto je referenční architekturu pro architekturu DMZ podnikové úrovni, využívání síťových virtuálních zařízení a další nástroje. Tato architektura je navržená pro splnění ministerstva obrany vaší zabezpečení Cloud computingu funkční požadavky na architekturu. Nicméně ji můžete využít pro každou organizaci. Tato příručka obsahuje dvě automatizované možnosti použití zařízení Citrix nebo F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917587"
---
# <a name="secure-azure-computing-architecture"></a>Zabezpečení Azure výpočetní architekturu

Pokyny k nastavení zabezpečení virtuální sítě a konfiguraci zabezpečení nástroje a služby, které stanovila DoD standardů a postupů už dlouho žádali rychle rostoucí počet DoD zákazníci, kteří nasazují úlohy do Azure. DISA publikovaná [rozhraní zabezpečit cloudové výpočetní architektura (SCCA) funkční požadavky dokumentu](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017. SCCA popisuje funkční cíle pro zabezpečení Defense Information System sítě (DISN) a připojení k poskytovateli komerční cloudové odkazuje a jak stěžejní vlastníky zabezpečené cloudové aplikace na hranici připojení. Je vyžadováno, aby každá entita amerického ministerstva obrany, připojující se ke komerčním cloudovém následuje pokyny SCCA FRD.
 
Existují čtyři součásti SCCA. Hranice cloudu přístupový bod (BCAP), virtuální datové centrum zabezpečení zásobníku (VDSS), virtuální datové centrum spravované služby (VDMS) a důvěryhodný správce přihlašovacích údajů cloudu (TCCM). Společnost Microsoft vyvinula řešení, které se bude splňovat požadavky na SCCA IL4 a IL5 úloh spuštěných v Azure. Konkrétní řešení Azure je názvem rozhraní zabezpečení Azure výpočetní architektura (SACA). Zákazníci, kteří nasazují SACA bude v souladu s SCCA FRD a umožní zákazníkům ministerstva obrany USA na přesun úloh do Azure po připojení. 

Architektury a SCCA pokyny jsou specifické pro zákazníky amerického ministerstva obrany, nejnovější revize pro SACA vám také pomůže civilních institucích zákazníkům pomohli s důvěryhodné internetové připojení (PIŠKVORKY) pokyny, stejně jako komerčními zákazníky, které chcete implementovat zabezpečené DMZ do Chraňte jejich prostředí azure.


## <a name="secure-cloud-computing-architecture-components"></a>Zabezpečení součásti Cloudovou výpočetní architekturu

**BCAP**

Účelem BCAP je DISN chránit před útoky v cloudovém prostředí. BCAP bude provádět zjišťování neoprávněných vniknutí a ochrany před únikem informací a také vyfiltrovat neoprávněné přenosy. Tuto součást lze umístit společně s ostatními součástmi SCCA. Důrazně doporučujeme, aby tato součást se nasadí pomocí fyzického hardwaru. Níže najdete seznam požadavků na zabezpečení BCAP.

***Požadavky na zabezpečení BCAP***

![Matice BCAP požadavky](media/bcapreqs.png)


**VDSS**

Účelem VDSS je chránit DoD mise vlastníka aplikace, které jsou hostované v Azure. VDSS vede SCCA hromadné operace zabezpečení. To provede kontroly přenosů dat myslet při zabezpečování aplikace běžící v Azure. Tuto součást lze zadat v rámci prostředí Azure.

***Požadavky na zabezpečení VDSS***

![Matice VDSS požadavky](media/vdssreqs.png)

**VDMS**

Účelem VDMS je poskytnout hostiteli zabezpečení, stejně jako sdílené služby System center data. Funkce VDMS buď spustit v centru vašeho SCCA nebo vlastník mise můžete nasadit kusů v jejich konkrétním předplatném Azure. Tuto součást lze zadat v rámci prostředí Azure.

***Požadavky na zabezpečení VDMS***

![Matice VDMS požadavky](media/vdmsreqs.png)


**TCCM**

TCCM je organizační role. Tato osoba bude zodpovědného za správu SCCA. Jejich povinnosti patří vytváření plánů a zásad pro přístup k účtu ke cloudovému prostředí, zajištění identit a řízení přístupu funguje správně a udržovat plánování pro správu cloudu přihlašovacích údajů. Tato osoba je jmenovaný oficiální autorizace. BCAP, VDSS a VDMS bude poskytovat funkce potřebné pro TCCM provádět jejich pracovní funkci.

***Požadavky na zabezpečení TCCM***

![Matice TCCM požadavky](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA součásti a informace o plánování 

Referenční architektura SACA slouží k nasazení VDSS a VDMS komponent v azure, stejně jako TCCM povolit. Tato architektura je modulární, což znamená, že všechny údaje VDSS a VDMS může existovat v centrální rozbočovač nebo některé ovládací prvky mohou být splněny mise vlastníka prostor nebo dokonce místní. Našeho týmu Microsoft doporučuje se společně umísťovat VDSS a VDMS komponenty do centrální virtuální Net spojující všichni vlastníci mise prostřednictvím. Následující diagram znázorňuje naše doporučená architektura. 


![Diagram referenční architektury SACA](media/sacav2generic.png)

Při plánování vaší strategie SCCA dodržování předpisů a technické architektury, existuje mnoho věcí, které byste měli zvážit. Je důležité, že v následujících tématech se berou v úvahu od začátku, protože každý zákazník s budete potřebovat pro tyto. Následující témata se problémy, které mají Navrhněte skutečných zákazníků ministerstva obrany USA a obvykle zpomalit plánování a provádění. 

- Které BCAP bude vaše organizace používat?
    - DISA BCAP
        - DISA má dvě provozní BCAPs pětiúhelník a Camp Robertse Certifikační autoritě se třetí Připravujeme online. 
        - DISA společnosti BCAPs mít všechny okruhy ExpressRoute do Azure, které můžete využít zákazníci ministerstva obrany USA pro připojení. 
        - DISA už má relaci Microsoft Peering podnikové úrovni pro zákazníky ministerstva obrany USA, kteří mají k odběru v rámci Microsoft SaaS nástrojů, jako je Office 365. S použitím DISA BCAP, můžete povolit připojení a partnerský vztah k vaší instanci SACA. 
    - Vytváření vlastních BCAP
        - To by vyžadovalo zapůjčení místa ve společně umístěných datových center a nastavení okruhu ExpressRoute do Azure. 
        - Tato možnost vyžaduje další schválení 
        - Tuto možnost z důvodu další schválení a fyzické sestavení na nejdelší dobu trvá. 
    - Microsoft doporučuje využívat DISA BCAP. Tato možnost je snadno k dispozici, má integrované redundance a už zákazníci provoz na něm ještě dnes v produkčním prostředí.
- Prostor směrovatelné IP ministerstva obrany USA
    - Budete muset použít DoD směrovatelné IP místo na hranici. Možnost NAT, aby privátní adresní prostor IP v Azure je k dispozici.  
    - Obraťte se na DoD NIC k získání prostor IP adres je potřeba jako součást vašeho příspěvku SNAP s DISA. 
    - Pokud budete chtít překladu adres pro privátní adresní prostor v Azure, budete potřebovat minimálně/24 je podsíť přiřazené, z síťovou kartu pro každou oblast, máte v úmyslu nasadit SACA adresního prostoru. 
- Redundance 
    - Microsoft navrhuje, nasaďte instanci SACA alespoň dvou oblastech. V cloudu amerického ministerstva obrany bude to znamenat, že nasazení pro obě oblasti k dispozici ministerstva obrany USA. 
    - Doporučujeme také, že se připojíte k aspoň dva BCAPs prostřednictvím samostatných okruhů ExpressRoute. Obě tras Express se pak dají propojit do každé oblasti SACA instance. 
- Požadavky na konkrétní součást ministerstva obrany USA
    - Má vaše organizace žádné specifické požadavky mimo SCCA požadavky? (Některé organizace mají požadavky na konkrétní IP adresy)
- SACA je modulární architektura  
    - Použijte pouze součásti, které budete potřebovat pro vaše prostředí. 
        - Nasazení síťových virtuálních zařízení v jedné vrstvě i vícevrstvými
        - Integrované IP adresy, nebo použijte vlastní IP adresy
- Úroveň dopadu ministerstva obrany USA vašich aplikací a dat
    - Pokud existuje riziko aplikací běžících v našich regionech IL5, doporučuje se, že vytvoříte instanci SACA v IL5. Instance je možné před IL4 aplikací, jakož i IL5. Ale IL4 SACA instance před IL5 aplikace pravděpodobně neobdrží schválení. 
- Kteří dodavatelé síťové virtuální zařízení bude používat VDSS?
    - Jak už bylo zmíněno dříve, můžete tento odkaz SACA vytvořených pomocí různých zařízení a služeb Azure. Máme ale automatizované řešení šablony mají být nasazeny SACA architektury s využitím F5 a Citrix. Tato řešení se budeme podrobněji níže. 
- Které služby Azure bude používat?
    - Existují služby Azure, které můžete splnit požadavky týkající se log analytics, ochranu založenou na hostiteli a ID funkce. Nicméně je možné, že některé služby nejsou obecně dostupné v našich regionech IL5. Může to vést k nutnosti použít některé nástroje 3. stran, pokud tyto služby Azure nesplňuje vaše požadavky. Musíte se podívat na kolik nástrojů jsou zvyklí a možnost využití Azure nativních nástrojů. 
    - Je doporučením společnosti Microsoft, že používáte tolik nativních nástrojů Azure co nejlépe, jako jsou vytvořeny bez kompromisů v zabezpečení cloudu a bez problémů integrují se zbytkem platformy Azure. Níže je uveden přehled Azure nativních nástrojů, které můžete využít jak vyhovět různým nárokům SCCA. 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Brány Firewall na Azure](https://docs.microsoft.com/azure/firewall/overview) 
        - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [Skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Velikosti
    - Nastavení velikosti cvičení bude nutné dokončit. Musíte se podívat na počet souběžných připojení, které jste uzavřeli prostřednictvím SACA instance, jakož i požadavky na propustnost sítě. 
    - To je důležitý krok, protože ho budou Nápověda pro nastavení velikosti virtuálních počítačů a také pomoct s identifikací licence, které se bude vyžadovat od různých dodavatelů, které používáte ve vaší instanci SACA. 
    - Nelze provést analýzu dobré nákladů bez tohoto cvičení velikosti je také důležité pro zaručení všechno, co se správnou velikost povolit pro zajištění nejlepšího výkonu. 


## <a name="most-common-deployment-scenario"></a>Nejběžnější scénář nasazení

Microsoft má několik zákazníků, kteří už prošly přes celý nasazení nebo alespoň plánování fázích jejich SACA prostředí. To je povoleno námi a získejte přehled o nejběžnější scénáře nasazení. Následující diagram znázorňuje nejběžnější architektury. 


![Diagram referenční architektury SACA](media/sacav2commonscenario.png) 


Jak je vidět z diagramu DoD zákazníkům obvykle odběru do dvou BCAPs DISA, jeden z těchto životy na západním pobřeží a život na východním pobřeží. S ExpressRoute privátní peer funkce od do Azure v každém umístění DISA BCAP povolený. Tyto partnerské uzly ExpressRoute jsou následně propojeny pro bránu virtuální sítě v ministerstva obrany USA – východ a ministerstva obrany USA ve středových oblastech Azure. SACA instance je nasazená v oblasti ministerstva obrany USA – východ a ministerstva obrany USA střed Azure a všechny příchozí a odchozí provoz prochází přes něj připojení Expressroute k DISA BCAP. 

Středisko vlastníka aplikace zvolte, které oblasti Azure, bude nasazení svých aplikací v partnerské vztahy virtuálních sítí pomocí připojení a jejich aplikace vaší virtuální sítě k virtuální síti SACA. Vynucené tunelování všechny přenosy přes VDSS instance. 

Tato architektura důrazně doporučujeme od Microsoftu, jak se bude splňovat požadavky SCCA, je vysoce dostupná, snadno škálovatelné a zjednodušuje nasazení a správu.

## <a name="automated-saca-deployment-options"></a>Možnosti nasazení automatizovaných SACA

 Dříve už jsme zmínili, že Microsoft ve spolupráci s dodavateli dvě vytvořit automatizované šablony SACA infrastruktury. Obě šablony nasadí následující komponenty Azure. 

- SACA Virtual Network
    - Podsítě pro správu
        - Kde jsou nasazené virtuální počítače pro správu a služby (jump polí)
        - VDMS podsítě
            - Ve které jsou nasazené virtuální počítače a služby používané pro VDMS
        - Nedůvěryhodné a důvěryhodné podsítí 
            - Ve které jsou nasazené virtuální zařízení
        - Podsíť brány
            - Kam se nasadí bránu ExpressRoute
- Virtuálních počítačů pro správu Jump Box
    - Používá pro vzdálenou správu prostředí.
- Síťová virtuální zařízení
    - Citrix nebo F5, v závislosti na šablonu, kterou nasadíte.
- Veřejné IP adresy
    - Použít pro front-endu, dokud ExpressRoute se přepne do online režimu. Tyto IP adresy se přeloží do back-endu Azure privátní adresní prostor
- Směrovací tabulky 
    - Použité během automatizace, tyto směrovací tabulky vynuceného tunelování veškerý provoz přes toto virtuální zařízení
- Nástroje pro vyrovnávání zatížení Azure – standardní SKU
    - Ty se používají k vyrovnávat zatížení provozu napříč zařízení
- Network Security Groups (Skupiny zabezpečení sítě)
    - Pro ovládání, jaké typy provozu může přechod k určité koncových bodů


**Nasazení Citrix SACA**

Citrix vytvořil šablonu nasazení, který se nasazuje s vysokou dostupností Citrix ADC zařízení pomocí dvou vrstev. Tato architektura splňuje požadavky VDSS. 

![Diagram Citrix SACA](media/citrixsaca.png)


Dokumentace ke službě Citrix a nasazení skriptu lze nalézt [tady.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **Nasazení F5 SACA**

F5 vytvořil dvě samostatné nasazení šablony, které pokrývají dvě různé architektury. První z nich má pouze jednu vrstvu zařízení F5 v konfiguraci aktivní aktivní s vysokou dostupností. Tato architektura splňuje požadavky pro VDSS. Druhá přidává druhou úroveň F5s aktivní aktivní s vysokou dostupností. Účelem této druhé vrstvy je umožnit pro zákazníky, chcete-li přidat vlastní IP adresy oddělené od F5 mezi vrstvami F5. Ne všechny součásti ministerstva obrany USA mají konkrétní IP adresy, které jsou předepsány pro použití. Pokud je to tento případ, jedna vrstva F5 zařízení budou fungovat v nejvíce od, že architektura obsahuje IP adresy na zařízeních, F5.  

![Diagram Citrix SACA](media/f5saca.png)

Skript nasazení a F5 dokumentaci najdete [tady.](https://github.com/f5devcentral/f5-azure-saca) 












