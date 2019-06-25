---
title: Secure Azure Computing Architecture
description: Tato referenční architektura pro architekturu DMZ podnikové úrovni pomocí síťových virtuálních zařízení a další nástroje. Tato architektura je navržená pro splnění ministerstva obrany vaší zabezpečení Cloud computingu funkční požadavky na architekturu. Také může sloužit pro každou organizaci. Tato příručka obsahuje dvě automatické možnosti, které používají Citrix nebo F5 zařízení.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963230"
---
# <a name="secure-azure-computing-architecture"></a>Secure Azure Computing Architecture

USA Pokyny k nastavení zabezpečení virtuální sítě a konfigurace zabezpečení nástroje a služby, které stanovila DoD standardů a postupů žádali ministerstva obrany (DoD) Zákazníci, kteří nasazením úloh do Azure. 

Agentura systémové informace obrany (DISA) publikování [funkční požadavky rozhraní zabezpečit cloudové výpočetní architektura (SCCA) dokumentu (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017. SCCA popisuje funkční cíle pro zabezpečení ochrany před mobilními informace systému vaší sítě (DISN) a komerční cloudové poskytovatele spojovací body. SCCA také popisuje, jak mise vlastníky zabezpečené cloudové aplikace na hranici připojení. Každá entita amerického ministerstva obrany, připojující se ke komerčním cloudovém musí splňovat pokyny uvedené v SCCA FRD.
 
SCCA má čtyř komponent:
 
- Hranice cloudu přístupový bod (BCAP)
- Virtuální datové centrum zabezpečení zásobníku (VDSS)
- Virtuální datové centrum spravované služby (VDMS)
- Důvěryhodný Cloud správce přihlašovacích údajů (TCCM) 

Společnost Microsoft vyvinula řešení, které splňuje požadavky na SCCA IL4 a IL5 úlohy, které běží v Azure. Toto řešení týkající se Azure je volána zabezpečení Azure výpočetní architektura (SACA). Zákazníci, kteří nasazují SACA jsou v souladu s SCCA FRD. Umožňují zákazníkům ministerstva obrany USA na přesun úloh do Azure po připojení.

Architektury a SCCA pokyny jsou specifické pro zákazníky ministerstva obrany USA, ale nejnovější revize pro SACA civilních institucích zákazníkům v souladu s důvěryhodné internetové připojení (PIŠKVORKY) pokyny. Nejnovější revize také pomoci komerčními zákazníky, které chcete implementovat zabezpečené DMZ chránit jejich prostředí Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Zabezpečení součásti Cloudovou výpočetní architekturu

### <a name="bcap"></a>BCAP

Účelem BCAP je DISN chránit před útoky, které pocházejí v cloudovém prostředí. BCAP provádí zjišťování neoprávněných vniknutí a ochrany před únikem informací. odfiltruje také neoprávněné přenosy. Tuto součást lze umístit společně s ostatními součástmi SCCA. Doporučujeme nasadit tuto součást pomocí fyzického hardwaru. Požadavky na zabezpečení BCAP jsou uvedeny v následující tabulce.

#### <a name="bcap-security-requirements"></a>Požadavky na zabezpečení BCAP

![Matice BCAP požadavky](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Účelem VDSS je chránit ministerstva obrany USA mise vlastníka aplikace, které jsou hostované v Azure. VDSS vede SCCA hromadné operace zabezpečení. To se provádí kontroly přenosů dat k zabezpečení aplikace, které běží v Azure. Tuto součást lze zadat v rámci prostředí Azure.

#### <a name="vdss-security-requirements"></a>Požadavky na zabezpečení VDSS

![Matice VDSS požadavky](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Účelem VDMS je poskytnout hostiteli zabezpečení a sdílené služby System center data. Funkce VDMS buď spustit v centru vašeho SCCA nebo vlastník mise můžete nasadit kusů v jejich konkrétním předplatném Azure. Tuto součást lze zadat v rámci prostředí Azure.

#### <a name="vdms-security-requirements"></a>Požadavky na zabezpečení VDMS

![Matice VDMS požadavky](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM je organizační role. Tato osoba zodpovídá za správu SCCA. Jejich povinností je: 

- Vytvoření plánů a zásad pro přístup k účtu ke cloudovému prostředí. 
- Ujistěte se, správu identit a přístupu funguje správně. 
- Udržujte plán správy cloudu přihlašovacích údajů. 

Tato osoba je jmenovaný official je přínosné pro autorizaci. BCAP, VDSS a VDMS poskytují funkce, které TCCM potřebuje k provedení své práce.

#### <a name="tccm-security-requirements"></a>Požadavky na zabezpečení TCCM

![Matice TCCM požadavky](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA součásti a informace o plánování 

SACA referenční architektura je navržená k nasazení VDSS a VDMS součásti v Azure a umožňuje TCCM. Tato architektura je modulární. Všechny údaje VDSS a VDMS může existovat v centrální rozbočovač. Některé ovládací prvky mohou být splněny prostor mise vlastníka nebo dokonce místní. Společnost Microsoft doporučuje společně umísťovat VDSS a VDMS komponenty do centrální virtuální síť, která všichni vlastníci mise můžete připojit prostřednictvím. Následující diagram znázorňuje tuto architekturu: 


![Diagram referenční architektury SACA](media/sacav2generic.png)

Při plánování vaší strategie SCCA dodržování předpisů a technické architektury zvažte v následujících tématech od začátku, protože ovlivňují každý zákazník. Následující problémy se mají najít zákazníci ministerstva obrany USA a obvykle zpomalit plánování a provádění. 

#### <a name="which-bcap-will-your-organization-use"></a>Které BCAP bude vaše organizace používat?
   - DISA BCAP:
        - DISA má dvě provozní BCAPs v pětiúhelník a Camp Robertse, certifikační Autority. Třetí plánujeme přidat do režimu online brzy. 
        - DISA společnosti BCAPs mít všechny okruhy Azure ExpressRoute do Azure, což může využívat pro připojení k zákazníkům ministerstva obrany USA. 
        - DISA má relaci partnerského vztahu Microsoftu podnikové úrovni pro zákazníky ministerstva obrany USA, kteří chtějí se přihlásit k odběru Microsoft software jako služba (SaaS) nástroje, jako je Office 365. S použitím DISA BCAP, můžete povolit připojení a partnerský vztah k vaší instanci SACA. 
    - Vytváření vlastních BCAP:
        - Tato možnost vyžaduje, abyste zapůjčení místa ve společně umístěných datových center a nastavení okruhu ExpressRoute do Azure. 
        - Tato možnost vyžaduje další schválení. 
        - Z důvodu další schválení a fyzické mimo sestavení tato možnost trvá nejdéle. 
    - Doporučujeme použít DISA BCAP. Tato možnost je snadno k dispozici, má vestavěná redundance a jsou zákazníci, kteří už dnes pracovat s nimi v produkčním prostředí.
- DoD směrovatelné IP místo:
    - Na hranici, je nutné použít prostor směrovatelné IP ministerstva obrany USA. Možnost použití NAT pro připojení těchto prostorů k privátní adresní prostor IP v Azure je k dispozici.
    - Obraťte se ministerstva obrany USA informace Center NIC (Network) k získání adresní prostor IP. Musíte ho jako součást vašeho příspěvku systému/Network schvalovací proces (SNAP) s DISA. 
    - Pokud máte v plánu používat NAT pro připojení privátní adresní prostor v Azure, budete potřebovat minimálně/24 je podsíť přiřazené, z síťovou kartu pro každou oblast, kde plánujete nasadit SACA adresního prostoru.
- Redundance:
    - Nasaďte instanci SACA alespoň dvou oblastech. V cloudu ministerstva obrany USA můžete ji nasadit do obě oblasti k dispozici ministerstva obrany USA.
    - Připojení k aspoň dva BCAPs prostřednictvím samostatných okruhů ExpressRoute. Obě připojení ExpressRoute se pak dají propojit do každé oblasti SACA instance. 
- Požadavky na konkrétní součást ministerstva obrany USA:
    - Má vaše organizace žádné specifické požadavky mimo SCCA požadavky? Některé organizace mají požadavky na konkrétní IP adresy.
- SACA je modulární architektura:
    - Použijte pouze součásti, které potřebujete pro vaše prostředí. 
        - Nasazení síťových virtuálních zařízení v jedné vrstvě nebo více vrstvami.
        - Použití integrované IP adresy nebo přineste vlastní IP adresy.
- Úroveň dopadu ministerstva obrany USA vašich aplikací a dat:
    - Pokud existuje riziko aplikací běžících v oblastech Microsoft IL5, vytvořte instanci SACA v IL5. Instance je možné před IL4 aplikací a IL5. Instance IL4 SACA před IL5 aplikace pravděpodobně nebude dostávat schválení.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Kteří dodavatelé síťové virtuální zařízení bude používat VDSS?
Jak už bylo zmíněno dříve, můžete vytvořit tento odkaz SACA pomocí celé řady zařízení a služeb Azure. Microsoft má automatizované šablony řešení Chcete-li nasadit architekturu SACA pomocí F5 a Citrix. Tato řešení jsou popsány v následující části.

#### <a name="which-azure-services-will-you-use"></a>Které služby Azure bude používat?
- Existují služby Azure, které můžou splňovat požadavky pro log analytics, ochranu založenou na hostiteli a ID funkce. Je možné, že některé služby nejsou obecně dostupné v oblastech Microsoft IL5. V tomto případě může být potřeba používat nástroje třetích stran, pokud nemůže tyto služby Azure splňují vaše požadavky. Podívejte se na všechny nástroje, které už znáte a možnost využití Azure nativních nástrojů.
- Doporučujeme používat libovolný počet nativních nástrojů Azure nejvíce. Jsou už vytvořené bez kompromisů v zabezpečení cloudu a bez problémů integrují se zbytkem platformy Azure. Jak vyhovět různým nárokům SCCA použití nativních nástrojů Azure v následujícím seznamu:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Brány Firewall na Azure](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Skupiny zabezpečení Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Velikost
    - Je třeba provést určení velikosti cvičení. Podívejte se na počet souběžných připojení, může být prostřednictvím SACA instance a požadavkům na propustnost sítě. 
    - Tento krok je velmi důležité. To pomáhá k určení velikosti virtuálních počítačů a identifikovat licence, které jsou vyžadovány od různých dodavatelů, které používáte ve vaší instanci SACA. 
    - Analýza dobré nákladů se nedá provést bez tohoto cvičení velikosti. Nastavení správné velikosti také umožňuje pro zajištění nejlepšího výkonu. 


## <a name="most-common-deployment-scenario"></a>Nejběžnější scénář nasazení

 Několik zákazníků společnosti Microsoft přešly až po úplné nasazení, nebo na nejnižší fáze plánování jejich SACA prostředí. Možnosti zobrazení přehled nejběžnější scénáře nasazení. Následující diagram ukazuje nejběžnější architekturu: 


![Diagram referenční architektury SACA](media/sacav2commonscenario.png) 


Jak je vidět z diagramu DoD zákazníkům obvykle odběru do dvou DISA BCAPs. Jeden z nich se nachází na západním pobřeží a dalších život na východním pobřeží. Azure v každém umístění DISA BCAP je povoleno privátní partnerské ExpressRoute. Tyto partnerské uzly ExpressRoute jsou následně propojeny pro bránu virtuální sítě v oblastech centrální Azure ministerstva obrany USA a ministerstva obrany USA – východ. SACA instance je nasazena v oblastech centrální Azure ministerstva obrany USA a ministerstva obrany USA – východ. Všechny příchozí a odchozí provoz prochází přes něj připojení ExpressRoute k DISA BCAP.

Mise vlastníka aplikace zvolte oblastí Azure, ve kterých plánují nasazovat své aplikace. Uživatel použít k připojení jejich aplikace a virtuální sítě k virtuální síti SACA partnerský vztah virtuální sítě. Pak se vynucené tunelování všechny přenosy přes VDSS instance.

Vzhledem k tomu, že splňují požadavky SCCA doporučujeme tuto architekturu. Je vysoce dostupné a snadno škálovatelné. Se také zjednoduší nasazení a správu.

## <a name="automated-saca-deployment-options"></a>Možnosti nasazení automatizovaných SACA

 Jak už jsme zmínili, Microsoft ve spolupráci s dodavateli dvě vytvořit automatizované šablony SACA infrastruktury. Obě šablony nasazují následující komponenty Azure: 

- Virtuální síť SACA
    - Podsítě pro správu
        - Tato podsíť je ve které jsou nasazené virtuální počítače pro správu a službám, označované také jako pole odkazů.
        - VDMS podsítě
            - Tato podsíť je, ve které jsou nasazené virtuální počítače a služby používané pro VDMS.
        - Nedůvěryhodné a důvěryhodné podsítí
            - Tyto podsítě jsou, ve které jsou nasazené virtuální zařízení.
        - Podsíť brány
            - Tato podsíť je, které se nasadí bránu ExpressRoute.
- Virtuálních počítačů pro správu jump box
    - Slouží pro out-of-band management prostředí.
- Síťová virtuální zařízení
    - Použijte buď Citrix nebo F5 založené na šablonu, která je nasazená.
- Veřejné IP adresy
    - Slouží pro front-endu do ExpressRoute se přepne do online režimu. Tyto IP adresy překladu do back endu Azure privátní adresní prostor.
- Směrovací tabulky 
    - Platily automatizace, tyto trasy tabulky vynutit tunel veškerý provoz přes toto virtuální zařízení.
- Nástroje pro vyrovnávání zatížení Azure – standardní SKU
    - Slouží k vyrovnávat zatížení provozu napříč zařízení.
- Skupiny zabezpečení sítě
    - Slouží k řídit, které typy přenosů můžete procházet k určité koncových bodů.


### <a name="citrix-saca-deployment"></a>Nasazení Citrix SACA

Šablona nasazení Citrix nasadí s vysokou dostupností Citrix ADC zařízení pomocí dvou vrstev. Tato architektura splňuje požadavky VDSS. 

![Diagram Citrix SACA](media/citrixsaca.png)


Dokumentace ke službě Citrix a skript nasazení najdete v tématu [tento odkaz na GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>Nasazení F5 SACA

Dvě samostatné šablony nasazení F5 pokrývají dvě různé architektury. První šablona obsahuje pouze jednu vrstvu zařízení F5 v konfiguraci aktivní aktivní s vysokou dostupností. Tato architektura splňuje požadavky pro VDSS. Druhá šablona přidá druhou vrstvu F5s aktivní aktivní s vysokou dostupností. Tato druhá vrstva umožňuje zákazníkům přidat vlastní IP adresy oddělené od F5 mezi vrstvami F5. Ne všechny součásti ministerstva obrany USA mají konkrétní IP adresy, které jsou předepsány pro použití. Pokud je to tento případ, jedna vrstva F5 zařízení funguje pro většinu, protože tento architektura obsahuje IP adresy na zařízeních, F5.

![Diagram F5 SACA](media/f5saca.png)

Dokumentace ke službě F5 a skript nasazení najdete v tématu [tento odkaz na GitHub](https://github.com/f5devcentral/f5-azure-saca).












