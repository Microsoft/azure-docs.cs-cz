---
title: Nejčastější dotazy – Řešení Azure VMware od CloudSimple
description: Nejčastější dotazy k řešení Azure VMware od CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025057"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Nejčastější dotazy týkající se řešení VMware od CloudSimple

## <a name="cloudsimple-service"></a>CloudSimple služba

**Co je řešení Azure VMware od CloudSimple?**

Řešení Azure VMware od CloudSimple transformuje a rozšiřuje úlohy VMware na privátní vyhrazené cloudy v Azure během několika minut. CloudSimple se stará o zřizování, správu infrastruktury a orchestraci úloh mezi místním i Azure. Vzhledem k tomu, že vaše aplikace běží přesně stejně místně a v Azure, můžete těžit z elasticity a služeb cloudu bez složitosti rearchitecting vašich aplikací. CloudSimple snižuje celkové náklady na vlastnictví pomocí modelu spotřeby cloudu, který poskytuje zřizování na vyžádání, růst průběžných plateb a optimalizaci kapacity.  Podívejte [se, co je řešení VMware v Azure by CloudSimple,](cloudsimple-vmware-solutions-overview.md) kde najdete funkce, výhody a scénáře.

**Co je cloudový cloudový cloud?**

CloudSimple Private Cloud je privátní, vyhrazený cloud, který se skládá z vysoce výkonného výpočetního, úložného a síťového prostředí nasazeného na infrastruktuře Microsoft Azure (hardware a prostor pro datové centrum) v umístěních Azure.  Privátní cloud poskytuje nativní platformu VMware jako službu. Z hlediska vmware každý privátní cloud obsahuje přesně jednu instanci serveru vCenter. Server vCenter spravuje více uzlů ESXi obsažených v jednom nebo více clusterech vSphere spolu s odpovídajícím úložištěm Virtual SAN (vSAN). Služba CloudSimple může ve vašem předplatném Azure obsahovat více privátních cloudů.  Další podrobnosti najdete [v tématu Přehled privátního cloudu](cloudsimple-private-cloud.md).

**Kde je služba CloudSimple dostupná?**

CloudSimple je k dispozici v oblastech Východní USA, Západní USA a Západní Evropa s dalšími oblastmi již brzy.

**Jak povolím předplatné clouduSimple?**

Můžete se obrátit na [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) zástupce účtu Microsoft na adrese povolit předplatné pro službu CloudSimple. Zadejte ID předplatného v e-mailu, pro který chcete, aby byla povolena služba CloudSimple.  

**Jak se dostanu na portál CloudSimple?**

Přístup k portálu CloudSimple z portálu Azure.  Podrobnosti najdete [v tématu Přístup k řešení VMware podle portálu CloudSimple z portálu Azure](access-cloudsimple-portal.md).

**Jak zvýším kapacitu privátního cloudu?**

Chcete-li zvýšit kapacitu, nakupujte další uzly z portálu Azure a pak pomocí uzlů rozbalte privátní cloud z portálu CloudSimple.  Do existujícího clusteru vSphere můžete přidat další uzly nebo je přidat do nového clusteru vSphere.  Podrobnosti najdete [v tématu Rozbalení clouduSimple privátní cloud](expand-private-cloud.md).

**Co se stane s mým privátním cloudem během údržby?**

CloudSimple poskytuje oznámení několik dní před plánovaným intervalem údržby.  Údržba se provádí nerušitým způsobem, aby byla zajištěna dostupnost vašeho privátního cloudu.  Údržba může být následujících typů:

* **CloudSimple infrastruktury**.  CloudSimple infrastruktura je navržena tak, aby byla vysoce dostupná.  Během tohoto typu intervalu údržby redundantní součásti jsou aktualizovány jeden po druhém, aby se zabránilo přerušení služby. Udržujete přístup k privátnímu cloudu vCenter, všem virtuálním počítačům, připojení k internetu z privátního cloudu a připojení k místnímu zařízení nebo Azure.
* **CloudSimple portál**. Během tohoto typu intervalu údržby některé funkce na portálu CloudSimple může být zakázánnebo nepřístupný.  Oznámení před intervalem údržby obsahuje podrobnosti o omezeních funkcí během údržby.

## <a name="connectivity"></a>Připojení

**Jaké jsou moje možnosti připojení k síti cloudsimple region?**

CloudSimple poskytuje následující možnosti připojení pro připojení k síti CloudSimple region. Současně lze použít více možností.

* **Připojení ExpressRoute z místního datového centra do sítě CloudSimple region**. Jedná se o vysokorychlostní zabezpečené privátní připojení s nízkou latencí, které používá globální dosah k přemostění místního okruhu ExpressRoute do okruhu CloudSimple ExpressRoute. Pokyny k nastavení připojení najdete [v tématu Připojení z místního cloudu Simple pomocí ExpressRoute](on-premises-connection.md).
* **Připojení ExpressRoute z virtuální sítě Azure do sítě CloudSimple region**. Jedná se o vysokorychlostní zabezpečené privátní připojení s nízkou latencí, které používá brány virtuální sítě k přemostění vaší virtuální sítě v Azure do okruhu CloudSimple ExpressRoute. Pokyny k nastavení připojení najdete v tématu [Připojení privátního cloudového prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute](azure-expressroute-connection.md).
* **Připojení VPN site-to-Site z místního datového centra do sítě cloudových oblastí**. Jedná se o zabezpečenou virtuální privátní síť z místního zařízení VPN do oblasti cloudového privátního cloudu.  Podrobnosti najdete v tématu [Nastavení bran VPN v síti CloudSimple](vpn-gateway.md).

**Jak se připojím k privátnímu cloudu?**

Podrobnosti o privátním cloudu můžete zobrazit na portálu CloudSimple. Chcete-li se připojit k virtuálnímu centru, které odpovídá vašemu privátnímu cloudu, nejprve ověřte, zda je síťové připojení navázáno pomocí sítě SITE-to-Site VPN, sítě SITE-to-Site VPN nebo ExpressRoute. Potom spusťte portál CloudSimple z portálu Azure a klikněte na **tlačítko Spustit klienta vSphere** na domovské stránce nebo na stránce podrobností privátního cloudu.

**Jaká je výhoda okruhů ExpressRoute?**

Okruh Azure ExpressRoute je vysokorychlostní zabezpečené připojení s nízkou latencí a nízkou latencí.  CloudSimple poskytuje vyhrazený okruh ExpressRoute na oblast na zákazníka.  Pomocí tohoto okruhu můžete navázat zabezpečené připojení z místního nebo vašeho předplatného Azure.

**Jaké jsou síťové náklady na připojení k CloudSimple?  Platí nějaké odchozí poplatky mezi CloudSimple a Azure nebo napříč oblastmi?**

Neexistuje žádný CloudSimple poplatek za odchozí sítě.  Standardní sazby Azure platí pro všechny odchozí provozy z vaší virtuální sítě nebo z místního okruhu ExpressRoute.

## <a name="networking"></a>Síťové služby

**Jaké síťové funkce jsou pro můj privátní cloud dostupné?**

Můžete zřídit sítě VLAN (a jejich podsítě) a tabulky brány firewall a přiřadit veřejné IP adresy, které se mapují, virtuálnímu počítači spuštěného v privátním cloudu. Podrobnosti o síťových funkcích naleznete v tématech Přehled sítí [VLAN a podsítí](cloudsimple-vlans-subnets.md), [Přehled tabulek brány firewall](cloudsimple-firewall-tables.md)a Přehled [veřejných IP adres](cloudsimple-public-ip-address.md).

**Jak nastavím různé podsítě pro své aplikace v privátním cloudu?**

Vlan y vytvářejíte ve svém privátním cloudu z portálu CloudSimple.  Po vytvoření sítě VLAN můžete vytvořit skupinu distribuovaných portů v programu Private Cloud vCenter pomocí sítě VLAN a vytvořit virtuální počítače připojené ke skupině distribuovaných portů.  Můžete povolit tabulky brány firewall pro síť VLAN/podsíť a definovat pravidla brány firewall pro zabezpečení síťového provozu.

**Jaká nastavení brány firewall jsou k dispozici pro mé soukromé cloudy?**

Můžete nakonfigurovat pravidla pro provoz mezi severem a jihem a východem a západem.  Pravidla jsou definována v tabulce brány firewall.  Tabulku brány firewall lze připojit k sítěem VLAN ve vašem privátním cloudu.  Podrobnosti naleznete v [tématu Nastavení tabulek brány firewall a pravidel pro privátní cloudy](firewall.md).

**Můžu virtuálním počítačům v privátním cloudu přiřadit veřejné IP adresy?**

Na portálu CloudSimple můžete přidělit novou veřejnou IP adresu a přidružit ji k privátní IP adrese virtuálního počítače nebo zařízení.  Můžete také vytvořit nová pravidla brány firewall nebo použít existující pravidla brány firewall, která umožní přenos y z konkrétních portů a IP adres na portálu. Podrobnosti naleznete v [tématu Přidělení veřejných IP adres pro prostředí privátního cloudu](public-ips.md).

## <a name="security"></a>Zabezpečení

**Jaké jsou moje možnosti zabezpečení na CloudSimple?**

CloudSimple poskytuje následující funkce zabezpečení pro zabezpečení prostředí privátního cloudu:

* **Šifrování dat v klidovém stavu**. Můžete šifrovat data v klidovém stavu, která se ubytovaly v úložišti vSAN ve vašem privátním cloudu. vSAN podporuje servery pro správu externích klíčů, které se můžou nasadit ve vaší virtuální síti Azure nebo v místním prostředí.  Podrobnosti najdete [v tématu Konfigurace šifrování vSAN pro cloudový privátní cloud](vsan-encryption.md).
* **Zabezpečení sítě**. Ovládejte tok síťového provozu pomocí pravidel brány firewall, která platí mezi privátním cloudem a internetem, privátním cloudem a místním prostředím nebo v podsítích privátního cloudu.
* **Zabezpečené, soukromé připojení**. Mezi místní sítí a předplatným Azure se napočne zabezpečené privátní připojení.

## <a name="compute"></a>Služba Compute

**Jaký druh hostitelů je k dispozici?**

CloudSimple nabízí tyto typy hostitelů:

* **Uzel CS28:** CPU: 2x 2,2 GHz, celkem 28 jader, 48 HT.  RAM: 256 GB.  Úložiště: 1600 GB NVMe cache, 5760 GB dat (All-Flash). Síť: 4x25Gbe NIC
* **Uzel CS36:** CPU 2x 2,3 GHz, celkem 36 jader, 72 HT.  RAM: 512 GB.  Úložiště: 3200 GB NVMe cache 11520 GB dat (All-Flash).  Síť: 4x25Gbe NIC
* **Uzel CS36m:** CPU 2x 2,3 GHz, celkem 36 jader, 72 HT.  RAM: 576 GB.  Úložiště: 3200 GB NVMe cache 13360 GB dat (All-Flash).  Síť: 4x25Gbe NIC

**Jak se řeší selhání hardwaru?**

Veškerá infrastruktura CloudSimple je průběžně monitorována platformou CloudSimple a našimi provozními týmy služeb.  Pokud je zjištěna chyba hardwaru, nový uzel je přidán do privátního cloudu a uzel se nezdařilo je odebrán.

## <a name="storage"></a>Úložiště

**Jaký typ úložiště je v privátním cloudu podporovaný?**

CloudSimple nabízí all-flash VMware vSAN úložiště s každým Privátním cloudem.  Každý vSphere je vytvořen s vlastním úložištěm dat vSAN.  Podrobnosti naleznete v [tématu Private Cloud VMware components - vSAN storage](vmware-components.md#vsan-storage).

**Je šifrování dat podporováno?**
Ano.  Úložiště vSAN můžete nastavit ve vašem privátním cloudu tak, aby používal server správy klíčů (KMS), který je nasazený místně nebo v Azure k šifrování dat uložených ve službě vSAN.

**Jak se zpracovávají nefunkční disky?**

CloudSimple průběžně monitoruje všechny hardwarové součásti privátního cloudu.  Pokud je zjištěna chyba disku nebo disk je identifikován jako selhání (na základě heuristiky), nový uzel je automaticky přidán do privátního cloudu.  Uzel s neúspěšným nebo selhávajícím diskem je odebrán z privátního cloudu.

## <a name="vmware"></a>VMware

**Jak můžu provádět rozsáhlé nahrávání nebo migraci aplikací a dat z místního prostředí?**

CloudSimple poskytuje nativní řešení VMware vSphere.  Všechny nástroje VMware pro hromadnou migraci dat lze použít s vaším privátním cloudem.  Mezi možnosti patří:

* VMware HCX pro hromadnou migraci dat.
* Studená migrace dat pomocí úložiště vMotion z místního clouduSimple.

**Mohu nainstalovat nějaké nástroje VMware?**

CloudSimple poskytuje nativní řešení VMware vSphere.  Všechny nástroje VMware používané pro správu místního prostředí vSphere lze použít na CloudSimple.  CloudSimple podporuje model BYOL (bring-your-own-license) pro instalaci nástrojů VMware.

**Jak jsou aktualizace a upgrady spravovány?**

CloudSimple spravuje a aktualizuje všechny součásti infrastruktury vašeho privátního cloudu bezproblémovým bezproblémovým způsobem.  Všechny aktualizace a opravy zabezpečení vydané společností VMware nebo dodavateli infrastruktury jsou naplánovány na aktualizaci, jakmile jsou kvalifikovány službou CloudSimple.

CloudSimple neprovádí upgrady nebo aktualizace aplikací nainstalovaných v privátním cloudu.

## <a name="azure-integration"></a>Integrace Azure

**Jaké služby Azure jsou podporované?**

CloudSimple poskytuje připojení Azure ExpressRoute k vašemu předplatnému v Azure.  Všechny služby spuštěné v rámci vašeho předplatného se mohou připojit k vašemu privátnímu cloudu.  Příklady obsahují:

* **Azure Active Directory** jako zdroj identity pro cloudsimple vCenter.
* **Úložiště Azure** pro ukládání záloh, bitových kopií a dalších dat z vašeho privátního cloudu.
* **Hybridní aplikace** s architekturou aplikací, která zahrnuje veřejné a soukromé cloudy.  Můžete například vytvořit webové servery v Azure, které mají přístup k aplikačním a databázovým serverům ve vašem privátním cloudu.
* **Azure Monitor** a **Azure centrum zabezpečení** pro úlohy spuštěné na VMware podporují protokolování, metriky výkonu a správu zabezpečení.

**Jak namapovat své klienty VMware do Azure?**

CloudSimple poskytuje jedinečnou možnost spravovat virtuální počítače VMware v privátním cloudu z portálu Azure.  Fond prostředků vCenter nakonfigurovaný s požadovanými omezeními prostředků může být mapován na vaše předplatné globálním správcem.  

**Jaké výhody licencování získám s Azure?**

S CloudSimple můžete využít výhod hybridního využití Azure a ušetřit až 90 % na licencích. Tato výhoda zachovává vaše investice do licencí Microsoftu a snižuje vaše náklady na ochranu a ochranu a ochranu nemovitostí ve srovnání s jinými cloudovými řešeními. Získáte také rozšířené aktualizace zabezpečení pro systémy Windows Server 2008 a Microsoft SQL Server 2008.  Model BYOL (bring-your-own-license) vám pomůže udržet nízké náklady na běžné aplikace, jako jsou Veeam a Zerto.  
