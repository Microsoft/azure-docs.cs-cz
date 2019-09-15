---
title: Secure Azure Computing Architecture
description: Tato referenční architektura pro architekturu DMZ na podnikové úrovni používá síťová virtuální zařízení a další nástroje. Tato architektura byla navržena tak, aby splňovala funkční požadavky architektury zabezpečeného cloud computingu v rámci obrany. Dá se taky použít pro libovolnou organizaci. Tento odkaz obsahuje dvě automatizované možnosti, které používají zařízení Citrix nebo F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: a12f5643c96b855d07bd038fcc96100a87f1252d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001865"
---
# <a name="secure-azure-computing-architecture"></a>Secure Azure Computing Architecture

USA Zákazníci oddělení obrany, kteří nasazují úlohy do Azure, požádali o pokyny k nastavení zabezpečených virtuálních sítí a konfiguraci nástrojů a služeb zabezpečení, které jsou uvedené ve standardech DoD a zvyklostí. 

Systém DISA (obrany Information System Agency) publikoval v 2017 [(Secure Cloud Computing Architecture) dokument s požadavky (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) . SCCA popisuje funkční cíle pro zabezpečení síťových bodů DISN (obrany Information System) a komerčních přípojných bodů poskytovatele cloudu. SCCA také popisuje, jak vlastníci zabezpečují cloudové aplikace na hranici připojení. Každá entita DoD, která se připojuje k komerčnímu cloudu, musí splňovat pokyny uvedené v SCCA FRD.
 
SCCA má čtyři součásti:
 
- Cloudový přístupový bod hranice (BCAP)
- Zásobník zabezpečení virtuálního datacentra (VDSS)
- Spravovaná služba Virtual Datacenter (VDMS)
- Správce pověření důvěryhodného cloudu (TCCM) 

Společnost Microsoft vyvinula řešení, které splňuje požadavky na SCCA pro úlohy IL4 i IL5 spuštěné v Azure. Toto řešení specifické pro Azure se nazývá architektura Secure Azure Computing (SACA). Zákazníci, kteří nasazují SACA, jsou v souladu s SCCA FRD. Můžou zákazníkům DoD povolit přesunout úlohy do Azure až po jejich připojení.

Doprovodné materiály a architektury SCCA jsou specifické pro zákazníky ze strany DoD, ale nejnovější revize, které SACA, mohou pomoci civilním zákazníkům v rozporu s pokyny k důvěryhodnému připojení k Internetu (TIC). Nejnovější revize také pomůžou komerčním zákazníkům, kteří chtějí implementovat zabezpečený DMZ pro ochranu prostředí Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Součásti architektury zabezpečeného cloud computingu

### <a name="bcap"></a>BCAP

Účelem BCAP je chránit DISN proti útokům, které pocházejí z cloudového prostředí. BCAP provádí zjišťování a prevenci vniknutí. odfiltruje také neautorizovaný provoz. Tato součást může být umístěná společně s dalšími komponentami SCCA. Tuto součást doporučujeme nasadit pomocí fyzického hardwaru. Požadavky na zabezpečení BCAP jsou uvedeny v následující tabulce.

#### <a name="bcap-security-requirements"></a>Požadavky na zabezpečení BCAP

![BCAP – matice požadavků](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Účelem VDSS je chránit aplikace pro majitele IT, které jsou hostované v Azure. VDSS provádí hromadné operace zabezpečení v SCCA. Provádí kontrolu provozu za účelem zabezpečení aplikací, které běží v Azure. Tato součást se dá poskytnout v prostředí Azure.

#### <a name="vdss-security-requirements"></a>Požadavky na zabezpečení VDSS

![VDSS – matice požadavků](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Účelem VDMS je poskytnout zabezpečení hostitelů a sdílené služby datového centra. Funkce VDMS můžou běžet buď v centru vaší SCCA, nebo vlastník mise ho nasadí do svého konkrétního předplatného Azure. Tato součást se dá poskytnout v prostředí Azure.

#### <a name="vdms-security-requirements"></a>Požadavky na zabezpečení VDMS

![VDMS – matice požadavků](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM je obchodní role. Tento jednotlivec zodpovídá za správu SCCA. Jejich povinností je: 

- Stanovte plány a zásady pro přístup k cloudovým prostředím. 
- Zajistěte, aby Správa identit a přístupu fungovala správně. 
- Udržujte plán správy cloudových přihlašovacích údajů. 

Tuto osobu jmenuje oprávnění úředník pro autorizaci. BCAP, VDSS a VDMS poskytují možnosti, které TCCM potřebuje k provedení svých úloh.

#### <a name="tccm-security-requirements"></a>Požadavky na zabezpečení TCCM

![TCCM – matice požadavků](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA součásti a požadavky plánování 

Referenční architektura SACA je navržena k nasazení komponent VDSS a VDMS v Azure a k povolení TCCM. Tato architektura je modulární. Všechny části VDSS a VDMS můžou být živé v centralizovaném centru. Některé ovládací prvky můžou být splněné v prostoru vlastníka mise nebo dokonce v místním prostředí. Microsoft doporučuje, abyste společně našli komponenty VDSS a VDMS do centrální virtuální sítě, ke kterým se můžou připojovat všichni vlastníci mise. Následující diagram znázorňuje tuto architekturu: 


![Diagram referenční architektury SACA](media/sacav2generic.png)

Při plánování strategie SCCA dodržování předpisů a technické architektury Vezměte v úvahu následující témata od začátku, protože mají vliv na každého zákazníka. Následující problémy se zabývají zákazníky s DoD a mají v úmyslu zpomalit plánování a provádění. 

#### <a name="which-bcap-will-your-organization-use"></a>Které BCAP bude vaše organizace používat?
   - DISA BCAP:
        - DISA má dvě provozní BCAPs na pětiúhelník a v Camp Roberts, CA. Třetí se plánuje přejít online brzo. 
        - DISA má BCAPs okruhy Azure ExpressRoute do Azure, které můžou použít zákazníci z DoD na připojení. 
        - DISA má relaci partnerských vztahů Microsoftu na podnikové úrovni pro zákazníky DoD, kteří se chtějí přihlásit k odběru nástrojů Microsoft software jako služba (SaaS), jako je například Office 365. Pomocí BCAP DISA můžete povolit připojení a partnerský vztah ke své instanci SACA. 
    - Sestavte si vlastní BCAP:
        - Tato možnost vyžaduje, abyste zapůjčili místo v rámci společně umístěného datového centra a nastavili okruh ExpressRoute na Azure. 
        - Tato možnost vyžaduje dodatečné schválení. 
        - Z důvodu dalšího schválení a fyzického sestavení je tato možnost trvat nejvíce času. 
    - Doporučujeme, abyste používali DISA BCAP. Tato možnost je snadno dostupná, má integrovanou redundanci a má zákazníky, kteří se v současnosti v produkčním prostředí pracují.
- IP místo pro směrování DoD:
    - K dispozici je nutné použít místo směrování DoD na Edge. K dispozici je možnost použít NAT k propojení těchto prostorů s privátním IP místem v Azure.
    - Pokud chcete získat místo IP adres, kontaktujte Network Information Center (NIC). Budete ji potřebovat jako součást odeslání procesu schvalování systému nebo sítě (SNAP) pomocí DISA. 
    - Pokud máte v úmyslu k propojení privátního adresního prostoru v Azure používat překlad adres (NAT), potřebujete pro každou oblast, kde plánujete nasadit SACA, minimálně podsíť s adresním prostorem/24 přiřazenou od síťového rozhraní.
- Redundance:
    - Nasaďte instanci SACA do alespoň dvou oblastí. V cloudu DoD ji nasadíte do dostupných oblastí DoD.
    - Připojte se k aspoň dvěma BCAPs prostřednictvím oddělených okruhů ExpressRoute. Obě připojení ExpressRoute je pak možné propojit s instancí SACA každé oblasti. 
- Splnění požadavků specifických pro komponentu:
    - Má vaše organizace nějaké zvláštní požadavky mimo požadavky na SCCA? Některé organizace mají specifické požadavky na IP adresy.
- SACA je modulární architektura:
    - Používejte pouze součásti, které pro vaše prostředí potřebujete. 
        - Nasaďte síťová virtuální zařízení v jedné nebo vícevrstvé vrstvě.
        - Používejte integrované IP adresy nebo přinášející vlastní IP adresy.
- Úroveň dopadu vašich aplikací a dat DoD:
    - Pokud existuje žádná možnost aplikací spuštěných v oblastech Microsoft IL5, sestavte instanci SACA v IL5. Instance se dá použít před IL4 aplikacemi a IL5. Instance IL4 SACA před aplikací IL5 pravděpodobně neobdrží akreditaci.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Který dodavatel síťového virtuálního zařízení budete používat pro VDSS?
Jak už bylo zmíněno dříve, můžete tento odkaz na SACA sestavit pomocí nejrůznějších zařízení a služeb Azure. Společnost Microsoft má automatizované šablony řešení pro nasazení architektury SACA pomocí kláves F5 i Citrix. Tato řešení jsou zahrnuta v následující části.

#### <a name="which-azure-services-will-you-use"></a>Které služby Azure budete používat?
- K dispozici jsou služby Azure, které mohou splňovat požadavky pro Log Analytics, ochranu na základě hostitelů a identifikátory. Je možné, že některé služby nejsou všeobecně dostupné v oblastech Microsoft IL5e. V takovém případě může být nutné použít nástroje třetích stran, pokud tyto služby Azure nevyhovují vašim požadavkům. Podívejte se na nástroje, které máte v pohodlí, a proveditelnost použití nativních nástrojů Azure.
- Doporučujeme, abyste používali co nejvíce nativních nástrojů Azure. Jsou sestavené s ohledem na zabezpečení cloudu a bezproblémově se integrují se zbytkem platformy Azure. Pro splnění různých požadavků SCCA použijte nativní nástroje Azure v následujícím seznamu:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Přední dvířka Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Skupiny zabezpečení Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Velikost
    - Vykonání změny velikosti je dokončeno. Podívejte se na počet souběžných připojení, která může mít instance SACA a požadavky na propustnost sítě. 
    - Tento krok je kritický. Pomáhá určit velikost virtuálních počítačů a identifikovat licence, které jsou vyžadovány od různých dodavatelů používaných ve vaší instanci SACA. 
    - Dobrá analýza nákladů se nedá provést bez tohoto uplatnění změny velikosti. Správná velikost také umožňuje dosáhnout nejlepšího výkonu. 


## <a name="most-common-deployment-scenario"></a>Nejběžnější scénář nasazení

 Několik zákazníků Microsoftu prošlo úplným nasazením nebo alespoň fázemi plánování jejich SACA prostředí. Jejich zkušenosti ukázaly, jaký je scénář nasazení nejběžnější. Následující diagram znázorňuje nejběžnější architekturu: 


![Diagram referenční architektury SACA](media/sacav2commonscenario.png) 


Jak vidíte z diagramu, zákazníci DoD se obvykle můžou přihlásit k odběru dvou z DISA BCAPs. Jedna z těchto žije na západním pobřeží a druhá žije na východním pobřeží. Privátní partner ExpressRoute je povolený pro Azure v každém umístění BCAP DISA. Tito vzdálení partneři ExpressRoute se pak propojí s bránou virtuální sítě v oblastech DoD – východ a DoD – střed Azure. Instance SACA je nasazená v oblastech DoD – východ a DoD Azure. Všechny příchozí a odchozí přenosy dat procházejí do a z připojení ExpressRoute k DISA BCAP.

Aplikace vlastnící vlastníkem pak zvolí oblasti Azure, ve kterých plánuje nasadit své aplikace. Používají partnerský vztah virtuálních sítí k připojení virtuální sítě své aplikace k virtuální síti SACA. Pak vynutí tunelování všech jejich přenosů prostřednictvím instance VDSS.

Doporučujeme tuto architekturu, protože splňuje požadavky SCCA. Je vysoce dostupná a snadno škálovatelná. Také usnadňuje nasazení a správu.

## <a name="automated-saca-deployment-options"></a>Možnosti nasazení automatizovaného SACA

 Jak už jsme uvedli, společnost Microsoft spolupracuje se dvěma dodavateli a vytvořila automatizovanou šablonu infrastruktury SACA. Obě šablony nasazují tyto komponenty Azure: 

- Virtuální síť SACA
    - Podsíť pro správu
        - V této podsíti jsou nasazené virtuální počítače a služby pro správu, označované také jako pole s odkazy.
        - VDMS podsíť
            - Tato podsíť je místo, kde se nasazují virtuální počítače a služby používané pro VDMS.
        - Nedůvěryhodné a důvěryhodné podsítě
            - V těchto podsítích jsou virtuální zařízení nasazená.
        - Podsíť brány
            - Tato podsíť je tam, kde je nasazená brána ExpressRoute.
- Virtuální počítače v poli pro skok správy
    - Používají se pro vzdálenou správu prostředí.
- Síťová virtuální zařízení
    - V závislosti na šabloně, kterou nasazujete, použijete buď Citrix, nebo F5.
- Veřejné IP adresy
    - Používají se pro front-end, dokud se ExpressRoute nepřepne do online režimu. Tyto IP adresy se přeloží na soukromý adresní prostor Azure v back-endu.
- Tabulky směrování 
    - Tyto směrovací tabulky při automatizaci vynutí tunelování všech přenosů prostřednictvím virtuálního zařízení.
- Nástroje pro vyrovnávání zatížení Azure – standardní SKU
    - Používají se k vyrovnávání zatížení provozu napříč zařízeními.
- Skupiny zabezpečení sítě
    - Používají se k určení, které typy provozu se můžou procházet s určitými koncovými body.


### <a name="citrix-saca-deployment"></a>Nasazení Citrix SACA

Šablona nasazení Citrix nasadí dvě vrstvy vysoce dostupných zařízení Citrix ADC. Tato architektura splňuje požadavky VDSS. 

![Diagram Citrix SACA](media/citrixsaca.png)


Dokumentaci pro Citrix a skript nasazení najdete v [tomto odkazu na GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>SACA nasazení F5

Dva samostatné šablony nasazení F5 pokrývá dvě různé architektury. První šablona obsahuje pouze jednu vrstvu zařízení F5 v konfiguraci aktivní-aktivní s vysokou dostupností. Tato architektura splňuje požadavky pro VDSS. Druhá šablona přidá druhou vrstvu typu aktivní-aktivní s vysokou dostupností F5s. Tato druhá vrstva umožňuje zákazníkům přidat své vlastní IP adresy odděleně od klávesy F5 mezi vrstvami F5. Ne všechny komponenty DoD mají pro použití předepsané konkrétní IP adresy. V takovém případě funguje jediná vrstva zařízení F5, protože tato architektura zahrnuje IP adresy na zařízeních F5.

![Obrázek F5 SACA](media/f5saca.png)

Dokumentaci F5 a skript nasazení najdete v [tomto odkazu na GitHub](https://github.com/f5devcentral/f5-azure-saca).












