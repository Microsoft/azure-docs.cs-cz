---
title: Nejčastější dotazy – řešení Azure VMware podle CloudSimple
description: Mezi nejčastější dotazy týkající se řešení Azure VMware by CloudSimple patří připojení, sítě, zabezpečení, úložiště, Integrace Azure a další.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0db8c13a7467ed864c0845319b37f958f60fa4d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140883"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Nejčastější dotazy týkající se řešení VMware podle CloudSimple

## <a name="cloudsimple-service"></a>Služba CloudSimple

**Co je řešení Azure VMware podle CloudSimple?**

Řešení Azure VMware podle CloudSimple transformuje a rozšiřuje úlohy VMware do privátních a vyhrazených cloudů v Azure během několika minut. CloudSimple se postará o zřizování, správu infrastruktury a orchestraci úloh mezi místními a Azure. Vzhledem k tomu, že vaše aplikace běží přesně v místním prostředí a v Azure, můžete využít flexibilitu a služby v cloudu bez nutnosti složitě měnit architekturu vašich aplikací. CloudSimple snižuje celkové náklady na vlastnictví pomocí modelu cloudové spotřeby, který poskytuje zřizování na vyžádání, průběžné platby a optimalizace kapacity.  Seznamte se s funkcemi, výhodami a scénáři v tématu [co je řešení VMware v Azure, které se CloudSimple](cloudsimple-vmware-solutions-overview.md) .

**Co je privátní cloud CloudSimple?**

Privátní cloud CloudSimple je privátní vyhrazený Cloud, který se skládá z vysoce výkonného výpočetního prostředí, úložiště a síťového prostředí nasazeného v Microsoft Azure infrastruktury (hardware a prostor Datacenter) ve službě Azure Locations.  Privátní Cloud poskytuje nativní VMware "platformu jako službu". V terminologii VMware obsahuje každý privátní cloud přesně jednu instanci vCenter Server. VCenter Server spravuje více uzlů ESXi obsažených v jednom nebo několika clusterech vSphere spolu s odpovídajícím úložištěm virtuální sítě SAN (síti vSAN). Služba CloudSimple může obsahovat několik privátních cloudů ve vašem předplatném Azure.  Další podrobnosti najdete v tématu [Přehled privátního cloudu](cloudsimple-private-cloud.md).

**Kde je služba CloudSimple k dispozici?**

CloudSimple je k dispozici v oblastech Východní USA, Západní USA a Západní Evropa s dalšími oblastmi, které už brzy připravujeme.

**Návody povolit předplatné pro CloudSimple?**

Pokud chcete povolit službu CloudSimple, obraťte se na svého zástupce účet Microsoft [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) . Zadejte ID předplatného v e-mailu, pro který chcete povolit službu CloudSimple.  

**Návody přístup k portálu CloudSimple?**

Přístup k portálu CloudSimple z Azure Portal.  Podrobnosti najdete v tématu [přístup k řešení VMware pomocí portálu CloudSimple z Azure Portal](access-cloudsimple-portal.md).

**Návody zvýšit kapacitu privátního cloudu?**

Pokud chcete zvýšit kapacitu, kupte si další uzly z Azure Portal a pak pomocí uzlů Rozšiřte svůj privátní cloud z portálu CloudSimple.  Do existujícího clusteru vSphere můžete přidat další uzly nebo je přidat do nového clusteru vSphere.  Podrobnosti najdete v tématu věnovaném [rozšíření privátního cloudu CloudSimple](expand-private-cloud.md).

**Co se stane s privátním cloudem během údržby?**

CloudSimple poskytuje oznámení několik dní před plánovaným intervalem údržby.  Údržba se provádí nerušivým způsobem, aby se zajistila dostupnost vašeho privátního cloudu.  Údržba může být z následujících typů:

* **Infrastruktura CloudSimple**  Infrastruktura CloudSimple je navržená tak, aby byla vysoce dostupná.  Během tohoto typu intervalu údržby se redundantní komponenty aktualizují po jednom, aby se předešlo jakémukoli přerušení služby. Udržujete přístup k vašemu privátnímu cloudu vCenter, všem virtuálním počítačům, připojení k Internetu z vašeho privátního cloudu a připojení k místnímu nebo Azure.
* **Portál CloudSimple** Během tohoto typu intervalu údržby můžou být některé funkce na portálu CloudSimple zakázané nebo nedostupné.  Oznámení před intervalem údržby zahrnuje podrobnosti o omezeních funkcí během údržby.

## <a name="connectivity"></a>Připojení

**Jaké mám možnosti připojení k síti CloudSimple region?**

CloudSimple poskytuje následující možnosti připojení pro připojení k síti vaší CloudSimple oblasti. Současně lze použít více možností.

* **ExpressRoute připojení z místního datacentra do sítě CloudSimple region**. Jedná se o vysokorychlostní a nízkou latenci, zabezpečené privátní připojení, které používá Global Reach k přemostění místního okruhu ExpressRoute do vašeho okruhu CloudSimple ExpressRoute. Pokyny k nastavení připojení najdete v tématu [připojení z místního prostředí k CloudSimple pomocí ExpressRoute](on-premises-connection.md).
* **ExpressRoute připojení z vaší virtuální sítě Azure do vaší místní sítě CloudSimple**. Jedná se o vysokorychlostní a nízkou latenci, zabezpečené privátní připojení, které používá brány virtuální sítě k přemostění virtuální sítě na Azure k okruhu CloudSimple ExpressRoute. Pokyny k nastavení připojení najdete v tématu [připojení privátního cloudového prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute](azure-expressroute-connection.md).
* **Připojení VPN typu Site-to-Site z místního datacentra do vaší sítě v CloudSimple oblasti**. Jedná se o zabezpečenou virtuální privátní síť z místního zařízení VPN do oblasti privátního cloudu CloudSimple.  Podrobnosti najdete v tématu [Nastavení bran sítě VPN v CloudSimple síti](vpn-gateway.md).

**Návody se připojit k privátnímu cloudu?**

Podrobnosti o vašem privátním cloudu můžete zobrazit na portálu CloudSimple. Pokud se chcete připojit k serveru vCenter, který odpovídá vašemu privátnímu cloudu, nejdřív ověřte, že je navázáno připojení k síti pomocí sítě VPN typu Site-to-site, VPN typu Point-to-site nebo ExpressRoute. Pak z Azure Portal spusťte portál CloudSimple a klikněte na tlačítko **Spustit klienta vSphere** na domovské stránce nebo na stránce s podrobnostmi privátního cloudu.

**Jaká je výhoda okruhů ExpressRoute?**

Okruh Azure ExpressRoute je vysokorychlostní a s nízkou latencí a zabezpečeným připojením.  CloudSimple poskytuje vyhrazený okruh ExpressRoute na oblast na zákazníka.  Pomocí tohoto okruhu můžete vytvořit zabezpečené připojení z místního prostředí nebo předplatného Azure.

**Jaké jsou náklady na síť pro připojení k CloudSimple?  Platí jakékoliv poplatky za odchozí přenosy mezi CloudSimple a Azure nebo napříč oblastmi?**

Za odchozí přenosy z sítě se neúčtují žádné CloudSimple poplatky.  Standardní sazby za Azure se vztahují na jakýkoliv výstupní provoz z vaší virtuální sítě nebo z místního okruhu ExpressRoute.

## <a name="networking"></a>Sítě

**Jaké síťové funkce jsou k dispozici pro můj privátní cloud?**

Můžete zřídit sítě VLAN (a jejich podsítě) a tabulky brány firewall a přiřadit veřejné IP adresy, které se mapují k virtuálnímu počítači spuštěnému v privátním cloudu. Podrobnosti o funkcích sítě najdete v tématech [Přehled sítí VLAN a podsítí](cloudsimple-vlans-subnets.md), [Přehled tabulek brány firewall](cloudsimple-firewall-tables.md)a [Přehled veřejných IP adres](cloudsimple-public-ip-address.md).

**Návody nastavit různé podsítě pro moje aplikace v mém privátním cloudu?**

SÍTĚ VLAN můžete vytvořit na svém privátním cloudu z portálu CloudSimple.  Po vytvoření sítě VLAN můžete ve svém privátním cloudu vCenter Vytvořit distribuovanou skupinu portů pomocí sítě VLAN a vytvořit virtuální počítače, které jsou připojené ke skupině distribuovaných portů.  Můžete povolit tabulky brány firewall pro síť VLAN nebo podsíť a definovat pravidla brány firewall pro zabezpečení síťového provozu.

**Jaká nastavení brány firewall jsou pro moje privátní cloudy k dispozici?**

Můžete nakonfigurovat pravidla pro provoz sever-jih a východ-západ.  Pravidla jsou definována v tabulce brány firewall.  Tabulku brány firewall lze připojit k sítím VLAN v privátním cloudu.  Podrobnosti najdete v tématu [Nastavení tabulek a pravidel brány firewall pro privátní cloudy](firewall.md).

**Můžu k virtuálním počítačům v prostředí privátního cloudu přiřadit veřejné IP adresy?**

Na portálu CloudSimple můžete přidělit novou veřejnou IP adresu a přidružit ji k privátní IP adrese virtuálního počítače nebo zařízení.  Můžete také vytvořit nová pravidla brány firewall nebo použít existující pravidla brány firewall, která povolí provoz z konkrétních portů a IP adres na portálu. Podrobnosti najdete v tématu [přidělení veřejných IP adres pro prostředí privátního cloudu](public-ips.md).

## <a name="security"></a>Zabezpečení

**Jaké mám možnosti zabezpečení na CloudSimple?**

CloudSimple poskytuje následující funkce zabezpečení pro zabezpečení vašeho privátního cloudového prostředí:

* **Šifrování v klidovém umístění**. Můžete šifrovat data uložená v úložišti síti vSAN v privátním cloudu. Síti vSAN podporuje servery pro správu externích klíčů, které se dají nasadit ve virtuální síti Azure nebo v místním prostředí.  Podrobnosti najdete v tématu [Konfigurace šifrování síti vSAN pro privátní cloud CloudSimple](vsan-encryption.md).
* **Zabezpečení sítě**. Řízení toku provozu sítě pomocí pravidel brány firewall, která platí mezi Vaším privátním cloudem a internetem, Vaším privátním cloudem a místním prostředím nebo v podsítích vašeho privátního cloudu.
* **Zabezpečené, soukromé připojení**. Mezi vaší místní sítí a vaším předplatným Azure je navázáno zabezpečené privátní připojení.

## <a name="compute"></a>Compute

**Jaký druh hostitelů je k dispozici?**

CloudSimple nabízí tyto typy hostitelů:

* **Uzel CS28:** Procesor: 2x 2,2 GHz, celkem 28 jader, 48 HT.  PAMĚŤ RAM: 256 GB.  Úložiště: 1600 GB NVMe cache, 5760 GB dat (all-Flash). Síť: 4x25Gbe NIC
* **Uzel CS36:** PROCESOR 2x 2,3 GHz, celkem 36 jader, 72 HT.  PAMĚŤ RAM: 512 GB.  Úložiště: 3200 GB NVMe cache 11520 GB data (vše-bliknutí).  Síť: 4x25Gbe NIC
* **Uzel CS36m:** PROCESOR 2x 2,3 GHz, celkem 36 jader, 72 HT.  PAMĚŤ RAM: 576 GB.  Úložiště: 3200 GB NVMe cache 13360 GB data (vše-bliknutí).  Síť: 4x25Gbe NIC

**Jak se zpracovávají chyby hardwaru?**

Všechny infrastruktury CloudSimple jsou nepřetržitě monitorovány platformou CloudSimple a týmy provozu služeb.  Pokud dojde k selhání hardwaru, do privátního cloudu se přidá nový uzel a neúspěšný uzel se odebere.

## <a name="storage"></a>Storage

**Jaký typ úložiště podporuje privátní cloud?**

CloudSimple nabízí úložiště VMware síti vSAN pro všechny aplikace, které obsahuje všechny privátní cloudy.  Každý vSphere se vytvoří s vlastním úložištěm dat síti vSAN.  Podrobnosti najdete v tématu [komponenty VMware privátního cloudu – úložiště síti vSAN](vmware-components.md#vsan-storage).

**Je šifrování dat podporováno?**
Ano.  Úložiště síti vSAN můžete nastavit v privátním cloudu, abyste mohli používat klíčovou management server (KMS) nasazenou místně nebo v Azure k šifrování dat uložených v síti vSAN.

**Jak se zpracovávají neúspěšné disky?**

CloudSimple nepřetržitě monitoruje všechny hardwarové součásti privátního cloudu.  Pokud se zjistilo selhání disku nebo je disk označený jako neúspěšný (založený na heuristikách), do privátního cloudu se automaticky přidá nový uzel.  Uzel s neúspěšným nebo neúspěšným diskem se odebere z privátního cloudu.

## <a name="vmware"></a>VMware

**Návody provádět rozsáhlé nahrávání nebo migraci aplikací a dat z místního prostředí?**

CloudSimple poskytuje nativní řešení VMware vSphere.  Všechny nástroje VMware pro migraci hromadných dat se dají použít spolu s Vaším privátním cloudem.  Mezi možnosti patří:

* HCX VMware pro hromadnou migraci dat.
* Studená migrace dat pomocí úložiště vMotion z místního prostředí do CloudSimple.

**Můžu nainstalovat nějaké nástroje VMware?**

CloudSimple poskytuje nativní řešení VMware vSphere.  Všechny nástroje VMware používané pro správu místního prostředí vSphere se dají použít na CloudSimple.  CloudSimple podporuje model BYOL (Přineste si vlastní licenci) pro instalaci nástrojů VMware.

**Jak se spravují aktualizace a upgrady?**

CloudSimple spravuje a aktualizuje všechny součásti infrastruktury vašeho privátního cloudu plynule bez rušivého způsobem.  Všechny aktualizace a opravy zabezpečení vydané dodavateli VMware nebo infrastruktur jsou naplánovány na aktualizaci, jakmile jsou kvalifikovány pomocí CloudSimple.

CloudSimple neprovede upgrady ani aktualizace aplikací nainstalovaných v privátním cloudu.

## <a name="azure-integration"></a>Integrace Azure

**Které služby Azure jsou podporovány?**

CloudSimple poskytuje připojení Azure ExpressRoute k vašemu předplatnému v Azure.  Všechny služby běžící v rámci vašeho předplatného se můžou připojit k vašemu privátnímu cloudu.  Příklady:

* **Azure Active Directory** jako zdroj identity pro CloudSimple vCenter.
* **Úložiště Azure** pro ukládání záloh, obrázků a dalších dat z vašeho privátního cloudu.
* **Hybridní aplikace** s architekturou aplikace, která pokrývá veřejné a privátní cloudy.  V Azure můžete například vytvořit servery webserver, které přistupují k aplikačním a databázovým serverům ve vašem privátním cloudu.
* **Azure monitor** a **Azure Security Center** pro úlohy běžící na protokolování podpory VMware, metrikách výkonu a správě zabezpečení.

**Návody namapovat klienty VMware do Azure?**

CloudSimple poskytuje jedinečnou schopnost spravovat vaše virtuální počítače VMware v privátním cloudu z Azure Portal.  Fond zdrojů vCenter nakonfigurovaný s požadovanými omezeními prostředků může váš globální správce namapovat na vaše předplatné.  

**Jaké výhody licencování mám získat s Azure?**

Pomocí CloudSimple můžete využít výhod programu Azure Hybrid Usage a ušetřit až 90% licencí. Tato výhoda zachovává vaše investice do licencí Microsoftu a snižuje celkové náklady na vlastnictví v souvislosti s ostatními cloudových řešeními. Získáte také rozšířené aktualizace zabezpečení pro Windows Server 2008 a Microsoft SQL Server 2008.  Model BYOL (Přineste si vlastní licenci) pomáhá udržet nízké náklady na běžné aplikace, jako je například Veeam a Zerto.  
