---
title: Nejčastější dotazy – řešení Azure VMware (AVS)
description: Nejčastější dotazy týkající se řešení Azure VMware (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025057"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>Nejčastější dotazy týkající se řešení VMware pomocí služba AVS

## <a name="avs-service"></a>Služba AVS

**Co je řešení Azure VMware (AVS)?**

Azure VMware Solutions (AVS) transformuje a rozšiřuje úlohy VMware do privátních a vyhrazených cloudů v Azure během několika minut. Služby AVS se postará o zřizování, správu infrastruktury a orchestraci úloh mezi místními a Azure. Vzhledem k tomu, že vaše aplikace běží přesně v místním prostředí a v Azure, můžete využít flexibilitu a služby v cloudu bez nutnosti složitě měnit architekturu vašich aplikací. Služba AVS snižuje celkové náklady na vlastnictví pomocí modelu cloudové spotřeby, který poskytuje zřizování na vyžádání, průběžné platby a optimalizace kapacity. V tématu [co je řešení VMware v Azure pomocí funkce AVS](cloudsimple-vmware-solutions-overview.md) pro funkce, výhody a scénáře.

**Co je privátní cloud služby AVS?**

Privátní cloud služby AVS je privátní vyhrazený Cloud, který se skládá z vysoce výkonného výpočetního prostředí, úložiště a síťového prostředí nasazeného v infrastruktuře Microsoft Azure (hardware a prostor Datacenter) v umístěních Azure. Privátní cloud služby AVS poskytuje nativní platformu VMware as a Service. Ve výrazech VMware obsahuje každý privátní cloud AVS přesně jednu instanci vCenter Server. VCenter Server spravuje více uzlů ESXi obsažených v jednom nebo několika clusterech vSphere spolu s odpovídajícím úložištěm virtuální sítě SAN (síti vSAN). Služba AVS může obsahovat několik privátních cloudů služby AVS v rámci vašeho předplatného Azure. Další podrobnosti najdete v tématu [Přehled služby AVS Private Cloud](cloudsimple-private-cloud.md).

**Kde je služba AVS Service k dispozici?**

AVS je k dispozici v oblastech Východní USA, Západní USA a Západní Evropa s dalšími oblastmi, které už brzy připravujeme.

**Návody povolit předplatné pro funkci AVS?**

Můžete se obrátit na zástupce účet Microsoft na [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) a povolit tak předplatné služby AVS. Zadejte ID předplatného v e-mailu, pro který chcete povolit službu AVS. 

**Návody přístup k portálu pro funkci AVS?**

Přístup k portálu služby AVS z Azure Portal. Podrobnosti najdete v tématu [přístup k portálu VMware Solutions (AVS) z Azure Portal](access-cloudsimple-portal.md).

**Návody zvýšit kapacitu privátního cloudu služby AVS?**

Pokud chcete zvýšit kapacitu, kupte si další uzly z Azure Portal a potom pomocí uzlů Rozšiřte svůj privátní cloud služby AVS z portálu AVS. Do existujícího clusteru vSphere můžete přidat další uzly nebo je přidat do nového clusteru vSphere. Podrobnosti najdete v tématu věnovaném [rozšíření privátního cloudu služby AVS](expand-private-cloud.md).

**Co se stane s privátním cloudem pro funkci AVS během údržby?**

AVS poskytuje oznámení několik dní před plánovaným intervalem údržby. Údržba se provádí nerušivým způsobem, aby se zajistila dostupnost vašeho privátního cloudu služby AVS. Údržba může být z následujících typů:

* **Infrastruktura AVS**. Infrastruktura AVS je navržená tak, aby byla vysoce dostupná. Během tohoto typu intervalu údržby se redundantní komponenty aktualizují po jednom, aby se předešlo jakémukoli přerušení služby. Udržujete přístup k vaší službě AVS Private Cloud vCenter, všem virtuálním počítačům, připojení k Internetu z vašeho privátního cloudu služby AVS a připojení k místnímu nebo Azure.
* **Portál**pro funkci AVS Během tohoto typu intervalu údržby můžou být některé funkce na portálu AVS zakázané nebo nedostupné. Oznámení před intervalem údržby zahrnuje podrobnosti o omezeních funkcí během údržby.

## <a name="connectivity"></a>Připojení

**Jaké mám možnosti připojení k síti oblastí služby AVS?**

Funkce AVS nabízí následující možnosti připojení pro připojení k síti vaší sítě služby AVS. Současně lze použít více možností.

* **ExpressRoute připojení z místního datacentra k síti oblasti služby AVS**. Jedná se o vysokorychlostní a nízkou latenci, zabezpečené privátní připojení, které používá Global Reach k přemostění místního okruhu ExpressRoute do vašeho okruhu služby AVS ExpressRoute. Pokyny k nastavení připojení najdete v tématu [připojení z místního prostředí ke službě AVS pomocí ExpressRoute](on-premises-connection.md).
* **ExpressRoute připojení z vaší virtuální sítě Azure do vaší místní sítě**. Jedná se o vysokorychlostní a nízkou latenci, zabezpečené privátní připojení, které používá brány virtuální sítě k přemostění vaší virtuální sítě v Azure do vašeho okruhu služby AVS ExpressRoute. Pokyny k nastavení připojení najdete v tématu [připojení vašeho prostředí privátního cloudu služby AVS ke službě Azure Virtual Network pomocí ExpressRoute](azure-expressroute-connection.md).
* **Připojení VPN typu Site-to-Site z místního datacentra do vaší místní sítě**. Jedná se o zabezpečenou virtuální privátní síť z místního zařízení VPN do vaší oblasti privátního cloudu služby AVS. Podrobnosti najdete v tématu [Nastavení bran sítě VPN v síti služby AVS](vpn-gateway.md).

**Návody se připojit k privátnímu cloudu služby AVS?**

Podrobnosti o vašem privátním cloudu služby AVS můžete zobrazit na portálu služby AVS. Pokud se chcete připojit k serveru vCenter, který odpovídá privátnímu cloudu služby AVS, nejdřív ověřte, že se naváže připojení k síti pomocí sítě VPN typu Site-to-site, VPN typu Point-to-site nebo ExpressRoute. Pak z Azure Portal spusťte portál AVS a klikněte na tlačítko **Spustit klienta vSphere** na domovské stránce nebo na stránce s podrobnostmi privátního cloudu služby AVS.

**Jaká je výhoda okruhů ExpressRoute?**

Okruh Azure ExpressRoute je vysokorychlostní a s nízkou latencí a zabezpečeným připojením. AVS poskytuje vyhrazený okruh ExpressRoute pro jednotlivé oblasti na zákazníka. Pomocí tohoto okruhu můžete vytvořit zabezpečené připojení z místního prostředí nebo předplatného Azure.

**Jaké jsou náklady na síť pro připojení ke službě AVS? Platí jakékoliv poplatky za odchozí přenosy mezi službou AVS a Azure nebo napříč různými oblastmi?**

Pro odchozí přenosy z sítě není poplatek za funkci AVS. Standardní sazby za Azure se vztahují na jakýkoliv výstupní provoz z vaší virtuální sítě nebo z místního okruhu ExpressRoute.

## <a name="networking"></a>Sítě

**Jaké síťové funkce jsou k dispozici pro privátní cloud služby AVS?**

Můžete zřídit sítě VLAN (a jejich podsítě) a tabulky brány firewall a přiřadit veřejné IP adresy, které se mapují k virtuálnímu počítači spuštěnému v privátním cloudu služby AVS. Podrobnosti o funkcích sítě najdete v tématech [Přehled sítí VLAN a podsítí](cloudsimple-vlans-subnets.md), [Přehled tabulek brány firewall](cloudsimple-firewall-tables.md)a [Přehled veřejných IP adres](cloudsimple-public-ip-address.md).

**Návody pro moje aplikace nastavit různé podsítě v mém privátním cloudu služby AVS?**

SÍTĚ VLAN vytvoříte na privátním cloudu služby AVS na portálu služby AVS. Po vytvoření sítě VLAN můžete vytvořit distribuovanou skupinu portů na svém privátním cloudu služby AVS pomocí sítě VLAN a vytvořit virtuální počítače, které jsou připojené ke skupině distribuovaných portů. Můžete povolit tabulky brány firewall pro síť VLAN nebo podsíť a definovat pravidla brány firewall pro zabezpečení síťového provozu.

**Jaká nastavení brány firewall jsou pro privátní cloudy služby AVS k dispozici?**

Můžete nakonfigurovat pravidla pro provoz sever-jih a východ-západ. Pravidla jsou definována v tabulce brány firewall. Tabulku brány firewall lze připojit k sítím VLAN v privátním cloudu služby AVS. Podrobnosti najdete v tématu [Nastavení tabulek a pravidel brány firewall pro privátní cloudy služby AVS](firewall.md).

**Můžu k virtuálním počítačům přiřadit veřejné IP adresy v prostředí privátního cloudu pro funkci AVS?**

Na portálu služby AVS můžete přidělit novou veřejnou IP adresu a přidružit ji k privátní IP adrese virtuálního počítače nebo zařízení. Můžete také vytvořit nová pravidla brány firewall nebo použít existující pravidla brány firewall, která povolí provoz z konkrétních portů a IP adres na portálu. Podrobnosti najdete v tématu [přidělení veřejných IP adres pro prostředí služby AVS privátní cloud](public-ips.md).

## <a name="security"></a>Zabezpečení

**Jaké mám možnosti zabezpečení na funkci AVS?**

Funkce AVS poskytuje následující funkce zabezpečení pro zabezpečení vašeho privátního cloudového prostředí služby AVS:

* **Šifrování v klidovém umístění**. Můžete šifrovat data uložená v úložišti síti vSAN v privátním cloudu služby AVS. Síti vSAN podporuje servery pro správu externích klíčů, které se dají nasadit ve virtuální síti Azure nebo v místním prostředí. Podrobnosti najdete v tématu [Konfigurace šifrování síti vSAN pro privátní cloud služby AVS](vsan-encryption.md).
* **Zabezpečení sítě**. Řízení toku provozu sítě pomocí pravidel brány firewall, která platí mezi privátním cloudem služby AVS a internetem, Vaším privátním cloudem a místním prostředím služby AVS nebo v rámci podsítí vašeho privátního cloudu služby AVS.
* **Zabezpečené, soukromé připojení**. Mezi vaší místní sítí a vaším předplatným Azure je navázáno zabezpečené privátní připojení.

## <a name="compute"></a>Služby Compute

**Jaký druh hostitelů je k dispozici?**

Možnost AVS nabízí tyto typy hostitelů:

* **Uzel CS28:** Procesor: 2x 2,2 GHz, celkem 28 jader, 48 HT.  PAMĚŤ RAM: 256 GB.  Úložiště: 1600 GB NVMe cache, 5760 GB dat (all-Flash). Síť: 4x25Gbe NIC
* **Uzel CS36:** PROCESOR 2x 2,3 GHz, celkem 36 jader, 72 HT.  PAMĚŤ RAM: 512 GB.  Úložiště: 3200 GB NVMe cache 11520 GB data (vše-bliknutí).  Síť: 4x25Gbe NIC
* **Uzel CS36m:** PROCESOR 2x 2,3 GHz, celkem 36 jader, 72 HT.  PAMĚŤ RAM: 576 GB.  Úložiště: 3200 GB NVMe cache 13360 GB data (vše-bliknutí).  Síť: 4x25Gbe NIC

**Jak se zpracovávají chyby hardwaru?**

Veškerá infrastruktura služby AVS je nepřetržitě monitorovaná platformou AVS a týmy provozu služeb. Pokud se zjistí selhání hardwaru, přidá se do privátního cloudu služby AVS nový uzel a odebere se uzel, který se nezdařil.

## <a name="storage"></a>Storage

**Jaký typ úložiště podporuje privátní cloud služby AVS?**

Služba AVS nabízí všechna řešení VMware síti vSAN Storage s každým privátním cloudem služby AVS. Každý vSphere se vytvoří s vlastním úložištěm dat síti vSAN. Podrobnosti najdete v tématu [komponenty VMware privátního cloudu pro síti vSAN úložiště](vmware-components.md#vsan-storage).

**Je šifrování dat podporováno?**
Ano. Úložiště síti vSAN můžete nastavit v privátním cloudu služby AVS a používat klíčovou management server (službu správy klíčů), která je nasazená místně nebo v Azure pro šifrování dat uložených v síti vSAN.

**Jak se zpracovávají neúspěšné disky?**

AVS nepřetržitě monitoruje všechny hardwarové součásti privátního cloudu AVS. Pokud se zjistilo selhání disku nebo je disk označený jako neúspěšný (založený na heuristikách), do privátního cloudu služby AVS se automaticky přidá nový uzel. Uzel s neúspěšným nebo neúspěšným diskem se odebere z privátního cloudu služby AVS.

## <a name="vmware"></a>VMware

**Návody provádět rozsáhlé nahrávání nebo migraci aplikací a dat z místního prostředí?**

AVS poskytuje nativní řešení VMware vSphere. Všechny nástroje VMware pro migraci hromadných dat se dají použít spolu s Vaším privátním cloudem služby AVS. Mezi možnosti patří:

* HCX VMware pro hromadnou migraci dat.
* Dostudená migrace dat pomocí úložiště vMotion z místního prostředí do služby AVS

**Můžu nainstalovat nějaké nástroje VMware?**

AVS poskytuje nativní řešení VMware vSphere. Všechny nástroje VMware používané pro správu místního prostředí vSphere se dají použít na funkci AVS. AVS podporuje pro instalaci nástrojů VMware model BYOL (Přineste si vlastní licenci).

**Jak se spravují aktualizace a upgrady?**

V rámci bezproblémového nepřerušovaného cloudu udržuje a aktualizuje všechny součásti infrastruktury privátního cloudu služby AVS. Všechny aktualizace a opravy zabezpečení vydané dodavateli VMware nebo infrastruktur jsou naplánovány na aktualizaci, jakmile jsou kvalifikovány službou AVS.

Pro funkci AVS se neprovádí upgrade ani aktualizace aplikací nainstalovaných v privátním cloudu se systémem AVS.

## <a name="azure-integration"></a>Integrace Azure

**Které služby Azure jsou podporovány?**

AVS poskytuje připojení Azure ExpressRoute k vašemu předplatnému v Azure. Všechny služby, které běží ve vašem předplatném, se můžou připojit k privátnímu cloudu služby AVS. Patří mezi ně například:

* **Azure Active Directory** jako zdroj identity pro vaši funkci AVS vCenter.
* **Úložiště Azure** pro ukládání záloh, imagí a dalších dat z vašeho privátního cloudu služby AVS
* **Hybridní aplikace** s architekturou aplikace, která pokrývá veřejné cloudy a privátní cloudy služby AVS V Azure můžete například vytvořit servery webserver, které přistupují k aplikačním a databázovým serverům ve vašem privátním cloudu služby AVS.
* **Azure monitor** a **Azure Security Center** pro úlohy běžící na protokolování podpory VMware, metrikách výkonu a správě zabezpečení.

**Návody namapovat klienty VMware do Azure?**

Funkce AVS nabízí jedinečnou možnost správy virtuálních počítačů VMware v privátním cloudu služby AVS z Azure Portal. Fond zdrojů vCenter nakonfigurovaný s požadovanými omezeními prostředků může váš globální správce namapovat na vaše předplatné. 

**Jaké výhody licencování mám získat s Azure?**

Díky funkci AVS můžete využít výhod programu Azure Hybrid Usage a ušetřit až 90% licencí. Tato výhoda zachovává vaše investice do licencí Microsoftu a snižuje celkové náklady na vlastnictví v souvislosti s ostatními cloudových řešeními. Získáte také rozšířené aktualizace zabezpečení pro Windows Server 2008 a Microsoft SQL Server 2008. Model BYOL (Přineste si vlastní licenci) pomáhá udržet nízké náklady na běžné aplikace, jako je například Veeam a Zerto. 
