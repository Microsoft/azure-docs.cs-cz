---
title: Časté otázky – VMware řešení podle CloudSimple
description: Nejčastější dotazy k řešení VMware Azure podle CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8cc6cf834c54ca25c12a6d66675e4290fd66136
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165820"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Nejčastější dotazy ohledně řešení VMware podle CloudSimple

Nejčastější dotazy a odpovědi týkající se řešení VMware Azure podle CloudSimple, které vám pomůžou pochopit, služby a jak ji používat. Otázky a odpovědi jsou uspořádány do následujících kategorií:

* CloudSimple služby
* Připojení
* Sítě
* Zabezpečení
* Compute
* Úložiště
* VMware
* Integrace s Azure
 
## <a name="cloudsimple-service"></a>CloudSimple služby

**Co je Azure řešení VMware podle CloudSimple?**

Řešení Azure VMware podle CloudSimple transformuje a rozšiřuje úloh VMware do privátních, vyhrazených cloudů v Azure během několika minut. Řešení zřizuje, spravuje infrastrukturu a orchestruje úloh mezi místními a Azure. Vzhledem k tomu, že vaše aplikace běží přesně stejnou místní i v Azure, můžete těžit z pružnost a služeb v cloudu bez složitosti změna architektury aplikace. CloudSimple snižuje celkové náklady na vlastnictví pomocí cloudového využití modelu, který poskytuje na vyžádání zřizování, platit jako růstu a optimalizace kapacity. Funkce, výhody a scénáře, naleznete v tématu [co je Azure řešení VMware podle CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Co je CloudSimple privátního cloudu?**

Zřizování privátních, vyhrazených cloudu, který se skládá z vysoce výkonné výpočetní prostředky, úložiště a síťové prostředí, které jsou nasazené na infrastruktury Microsoft Azure (místo hardwaru a datového centra) v umístění Azure. Privátní cloud poskytuje nativní VMware platformu jako službu. Každý z privátních cloudů v podmínkách VMware, obsahuje přesně jednu instanci serveru vcenter. VCenter Server spravuje více uzlů ESXi obsažené v jedné nebo více clusterech vSphere, společně s odpovídající úložiště sítě vSAN. CloudSimple služby může obsahovat více privátních cloudů ve vašem předplatném Azure. Další informace o privátních cloudech, naleznete v tématu [přehled privátního cloudu](cloudsimple-private-cloud.md).

**Kde je služba CloudSimple dostupná?**

CloudSimple je dostupná v oblastech USA – východ a USA – západ.

**Jak povolím předplatného CloudSimple?**

Obraťte se na obchodního zástupce společnosti Microsoft na [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) povolit předplatné pro službu CloudSimple. Zadejte své ID předplatného v e-mailu, pro které chcete CloudSimple služby povolena. 

**Jak získám přístup k portálu CloudSimple?**

Portálu má CloudSimple přístup z portálu Azure portal. Informace o tom, jak přístup k portálu CloudSimple najdete v tématu [portálem CloudSimple z portálu Azure portal přístup k řešení VMware](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Jak můžu zvýšit kapacitu privátního cloudu?**

Zřízení uzlů z portálu Azure portal a rozbalte vašeho privátního cloudu z portálu CloudSimple. Privátní cloud můžete rozšířit přidáním uzlů k existujícímu clusteru vSphere nebo vytvořením nového clusteru vSphere. Informace o postupu najdete v tématu [rozbalte privátního cloudu CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Co se stane Moje privátního cloudu při údržbě?**

CloudSimple poskytuje pravidelná oznámení dní před plánovanou údržbu. Údržba se provádí v nenarušující způsob, jak zajistit dostupnost vašeho privátního cloudu. Údržba může být z následujících typů:

- **Infrastruktura CloudSimple**: Infrastruktura CloudSimple byla navržena jako vysoce dostupný. Během údržby připojení a dostupnosti vašeho privátního cloudu je, že jsou splněné aktualizací redundantní komponenty jeden po druhém žádný vliv. Máte přístup k vCenter privátního cloudu, všechny virtuální počítače, připojení k Internetu z privátního cloudu a připojení k místní nebo v Azure.
- **Portál CloudSimple**: Během údržby některé funkce na portálu CloudSimple nemusí být dostupné nebo můžou být zakázané. Oznámení o údržbě obsahuje informace o co se dá dělat na portálu.

## <a name="connectivity"></a>Připojení

**Jaké jsou možnosti připojení k síti CloudSimple oblasti?**

CloudSimple poskytuje tři různé možnosti připojení pro připojení k síti CloudSimple oblasti. Současně lze použít všechny tři možnosti:

- Azure ExpressRoute připojení z místního datacentra k síti CloudSimple oblasti: Vysokorychlostní s nízkou latencí zabezpečených privátních připojení propojující vaše místní okruh ExpressRoute s váš okruh CloudSimple ExpressRoute pomocí globální dosah. Abyste mohli připojení nastavit, najdete v článku [CloudSimple pomocí ExpressRoute připojit v místním](https://docs.azure.cloudsimple.com/on-premises-connection).
- Připojení ExpressRoute z vaší virtuální sítí Azure k síti CloudSimple oblasti: S nízkou latencí, vysokorychlostní zabezpečené privátní připojení propojující vaše virtuální síť v Azure s váš okruh CloudSimple ExpressRoute pomocí brány virtuální sítě. Abyste mohli připojení nastavit, najdete v článku [svoje prostředí privátního cloudu CloudSimple připojit k virtuální síti Azure pomocí ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Site-to-site VPN připojení z vašeho místního datového centra k síti CloudSimple oblasti: Zabezpečené virtuální privátní sítě z vašeho místního zařízení VPN pro vaši oblast CloudSimple privátního cloudu. Abyste mohli připojení nastavit, najdete v článku [nastavit připojení VPN mezi vaší místní sítí a CloudSimple privátního cloudu](https://docs.azure.cloudsimple.com/set-up-vpn).

**Jak připojit do privátního cloudu**

Na portálu CloudSimple můžete zobrazit podrobnosti o vašem privátním cloudu. Pro připojení k serveru vCenter, který odpovídá do privátního cloudu, ujistěte se, že připojení k síti se vytvoří pomocí site-to-site, point-to-site nebo ExpressRoute. Potom spusťte portál CloudSimple z portálu Azure portal. Vyberte **spuštění vSphere klienta** na domovské stránce nebo na stránce s podrobnostmi privátního cloudu.

**Co je výhoda okruhu ExpressRoute?**

Okruh Azure ExpressRoute poskytuje zabezpečené připojení vysokorychlostní, s nízkou latencí. CloudSimple poskytuje vyhrazeného okruhu ExpressRoute v jedné oblasti na zákazníka. Pomocí tohoto okruhu, můžou vytvořit zabezpečené připojení z místního a vaše předplatné Azure.

**Jaké jsou náklady na síť pro připojení do a z CloudSimple? Existují žádné poplatky za výchozí přenos dat do a z CloudSimple do Azure? Existují žádné poplatky za výchozí přenos dat mezi oblastmi?**

Neplatí žádné poplatky za odchozí síťovou komunikaci. Standardní sazby Azure platí pro veškerý odchozí provoz z vaší virtuální sítě nebo z okruhu ExpressRoute v místním prostředí.

## <a name="networking"></a>Sítě

**Jaké síťové funkce jsou k dispozici pro tento privátní cloud?**

Můžete zřídit sítě VLAN a podsítě a brány firewall tabulky. Můžete přiřadit veřejné IP adresy a mapování na virtuální počítač, na kterém běží ve vašem privátním cloudu. Další informace najdete v tématu [přehled sítě VLAN a podsítě](cloudsimple-vlans-subnets.md), [Přehled brány Firewall tabulky](cloudsimple-firewall-tables.md), a [přehled veřejných IP adres](cloudsimple-public-ip-address.md).

**Jak můžu nastavit různé podsítě pro Moje aplikace v privátním cloudu.?**

Sítě VLAN v privátním cloudu můžete vytvořit ze svého portálu CloudSimple. Po vytvoření sítě VLAN, můžete vytvořit skupinu portů distribuované na vCenter privátního cloudu pomocí sítě VLAN a vytvořit virtuální počítače připojené ke skupině distribuované portu. Můžete povolit tabulku brány firewall pro sítě VLAN a podsítě a definovat pravidla brány firewall k zabezpečení síťových přenosů.

**Jaká nastavení brány firewall jsou k dispozici pro můj privátní cloudy?**

Můžete nakonfigurovat pravidla pro provoz sever jih a východozápadním směrem. Pravidla jsou definovaná v tabulce brány firewall. Tabulky brány firewall lze připojit k sítím VLAN v privátním cloudu. Postup instalace najdete v části [nastavení brány firewall na tabulky a pravidel pro privátní cloudy](https://docs.azure.cloudsimple.com/firewall).

**Mohu přiřadit veřejné IP adresy pro virtuální počítače v Moje prostředí privátního cloudu?**

Na portálu CloudSimple můžete snadno přidělit novou veřejnou IP adresu a přidružte jej k privátní IP adresu vašeho virtuálního počítače nebo zařízení. Také můžete vytvořit nová pravidla brány firewall nebo existující pravidla brány firewall umožňující provoz z konkrétní porty a konkrétní sady IP adres na portálu. Postup instalace najdete v části [přidělení veřejné IP adresy pro prostředí privátních cloudů](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Zabezpečení

**Jaké jsou možnosti zabezpečení na CloudSimple?**

Privátní cloud CloudSimple poskytuje následující funkce zabezpečení pro zabezpečení vašeho prostředí privátního cloudu:

- **Data šifrování neaktivních dat:** Můžete k šifrování dat v klidovém stavu, který se nachází ve virtuální síti SAN úložiště ve vašem privátním cloudu. síť vSAN podporuje externí key management server, který je možné nasadit v Azure virtuální síti nebo v místním prostředí. Další informace najdete v tématu [konfigurace sítě vSAN šifrování pro svůj privátní cloud CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Zabezpečení sítě:** Ovládací prvek tokem provozu sítě od a do privátního cloudu z Internetu, v místním prostředí a v rámci podsítí ve vašem privátním cloudu s využitím pravidel brány firewall.
- **Zabezpečené a privátní připojení:** Zabezpečené a privátní připojení mezi vaší místní sítí a vaše předplatné Azure.

## <a name="compute"></a>Compute

**Jaký druh hostitelé jsou k dispozici?**

CloudSimple nabízí dva typy hostitelů:

* **Uzel CS28**: CPU:2 x 2,2 GHz, 28. Celkový počet jader, 48 HT. Paměť RAM: 256 GB. Storage: 1 600 GB mezipaměti NVMe, 5760 GB dat (typu Flash). Síť: 2x25Gbe NIC.
* **Uzel CS36**: Procesor 2 x 2.3 GHz, celkový počet jader 36, 72 HT. Paměť RAM: 512 GB. Storage: 3200 GB mezipaměti NVMe 11,520 GB dat (typu Flash). Síť: 2x25Gbe NIC.

**Jak se zpracovává selhání hardwaru?**

Veškerá infrastruktura CloudSimple průběžně monitorovat CloudSimple platformu a jeho služby provozní týmy. Pokud se zjistí selhání hardwaru, přidání nového uzlu do privátního cloudu. Uzel je odebrán k zajištění vysoké dostupnosti vašeho privátního cloudu.

## <a name="storage"></a>Úložiště

**Jaký typ úložiště je podporována v privátním cloudu?**

Nabízí CloudSimple **úložiště typu flash VMware sítě vSAN** s každou privátního cloudu. Každý vSphere se vytvoří s vlastní úložiště dat sítě vSAN. Další informace najdete v tématu [VMware součásti privátního cloudu – sítě vSAN úložiště](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Je šifrování uložených dat, které jsou podporovány?**
Ano. Nastavením sítě vSAN úložiště v privátním cloudu k používá server pro správu klíčů (KMS), která je nasazená místně nebo v Azure pro šifrování dat uložených v síti vSAN.

**Jak se zpracovává selhávajících disků?**

Monitorování CloudSimple nepřetržitě monitoruje všechny hardwarové součásti privátního cloudu. Pokud se zjistí selhání disku nebo disku se identifikuje jako služeb při selhání podle heuristickými metodami, je automaticky přidán nový uzel pro daný privátní cloud. Uzel s diskem selhání je odebrán z privátního cloudu.

## <a name="vmware"></a>VMware

**Jak provést odeslání velkého rozsahu a migraci aplikací a dat z místních?**

CloudSimple poskytuje nativní řešení VMware vSphere. Libovolný nástroj používaný pro migrace hromadných dat jde použít s CloudSimple privátního cloudu. Zde jsou některé z dostupných možností:

- VMware HCX migrace hromadných dat
- Studená migraci dat pomocí řešení Storage vMotion z místního na CloudSimple.

**Můžete nainstalovat všechny nástroje VMware?**

CloudSimple poskytuje nativní řešení VMware vSphere. Libovolný nástroj používaný ke správě prostředí vSphere, na CloudSimple se dá použít místní. CloudSimple podporuje model bring your-používání vlastní licence (BYOL) pro instalaci nástroje VMware tools.

**Jak se spravují aktualizace a upgrady?**

CloudSimple spravuje a aktualizuje všechny součásti infrastruktury privátního cloudu bezproblémové nenarušující způsobem. Žádné aktualizace nebo opravy zabezpečení vydané společností VMware nebo infrastrukturu dodavatelů, kteří je naplánovaná aktualizace ihned poté, co je kvalifikována CloudSimple.

CloudSimple neprovádí upgradů nebo aktualizací aplikací instalovaných do privátního cloudu.

## <a name="azure-integration"></a>Integrace s Azure

**Jaké služby Azure jsou podporovány?**

CloudSimple představují připojení Azure ExpressRoute k vašemu předplatnému Azure. Všechny služby, které běží ve vašem předplatném máte připojení k síti do privátního cloudu a se může připojit k privátním cloudu. Příklady:

- **Azure Active Directory**: Použití Azure Active Directory jako identitu zdroje pro CloudSimple vCenter.
- **Azure Storage**: Použití úložiště pro ukládání záloh, obrázky a další data z vašeho privátního cloudu.
- **Hybridní aplikace**: Můžete vytvořit aplikace architektury, která zahrnuje veřejných a privátních cloudů. Například můžete vytvoříte webových serverů v Azure, které aplikace access a databázové servery CloudSimple privátního cloudu.
- **Azure Monitor** a **Azure Security Center**: Úloha, která běží na VMware můžete použít Centrum zabezpečení a monitorování pro protokolování, metriky výkonu a správu zabezpečení.

**Jak se mapují klienti VMware do Azure?**

CloudSimple nabízí jedinečnou možnost spravovat virtuální počítače VMware na privátní cloud z webu Azure portal. Fond zdrojů serveru vCenter nakonfigurovanou omezení prostředků, které chcete, aby je možné mapovat na vaše předplatné globálního správce. 

**Jaké výhod licencování začít s Azure?**

S CloudSimple můžete využít zvýhodněné hybridní využití Azure a ušetřit až 90 % na licence pro zachování vašich investic do licencí Microsoftu a snížit celkové náklady na vlastnictví ve srovnání s jinými cloudy. Také získat rozšířené aktualizace zabezpečení pro Windows Server 2008 a Microsoft SQL Server 2008. Zachovat nízké s BYOL náklady na cloud pro běžné aplikace, jako je Veeam, řešení Zerto a dalších. 
