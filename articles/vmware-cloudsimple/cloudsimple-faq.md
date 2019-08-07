---
title: Nejčastější dotazy – řešení VMware podle CloudSimple
description: Nejčastější dotazy k řešení Azure VMware od CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816230"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Nejčastější dotazy týkající se řešení VMware podle CloudSimple

Nejčastější dotazy a odpovědi týkající se řešení Azure VMware vám CloudSimple, které vám pomůžou pochopit službu a jak ji používat. Otázky a odpovědi jsou uspořádány do následujících kategorií:

* Služba CloudSimple
* Připojení
* Sítě
* Zabezpečení
* Compute
* Storage
* VMware
* Integrace Azure
 
## <a name="cloudsimple-service"></a>Služba CloudSimple

**Co je řešení Azure VMware podle CloudSimple?**

Řešení Azure VMware podle CloudSimple transformuje a rozšiřuje úlohy VMware do privátních a vyhrazených cloudů v Azure během několika minut. Toto řešení zajišťuje správu infrastruktury a orchestruje úlohy mezi místními a Azure. Vzhledem k tomu, že vaše aplikace běží přesně v místním prostředí a v Azure, můžete využít flexibilitu a služby v cloudu bez nutnosti složitě měnit architekturu vašich aplikací. CloudSimple snižuje celkové náklady na vlastnictví pomocí modelu cloudové spotřeby, který poskytuje zřizování na vyžádání, průběžné platby a optimalizace kapacity. Informace o funkcích, výhodách a scénářích najdete v tématu [co je Azure VMware Solution by CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Co je privátní cloud CloudSimple?**

Zřizujete privátní vyhrazený Cloud, který se skládá z vysoce výkonného výpočetního prostředí, úložiště a síťového prostředí nasazeného v Microsoft Azure infrastruktury (hardware a prostor Datacenter) v umístěních Azure. Privátní Cloud poskytuje nativní platformu VMware jako službu. V terminologii VMware obsahuje každý privátní cloud přesně jednu instanci vCenter Server. VCenter Server spravuje více uzlů ESXi obsažených v jednom nebo několika clusterech vSphere spolu s odpovídajícím úložištěm síti vSAN. Služba CloudSimple může obsahovat několik privátních cloudů ve vašem předplatném Azure. Další informace o privátních cloudech najdete v tématu [Přehled privátního cloudu](cloudsimple-private-cloud.md).

**Kde je dostupná služba CloudSimple?**

CloudSimple je k dispozici v oblastech Východní USA a Západní USA.

**Návody povolit předplatné pro CloudSimple?**

Pokud chcete povolit službu CloudSimple [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) , obraťte se na svého zástupce účet Microsoft. Zadejte ID předplatného v e-mailu, pro který chcete povolit službu CloudSimple. 

**Návody přístup k portálu CloudSimple?**

Přístup k portálu CloudSimple z Azure Portal. Informace o tom, jak získat přístup k portálu CloudSimple, najdete v tématu [přístup k řešení VMware pomocí portálu CloudSimple z Azure Portal](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Návody zvýšit kapacitu privátního cloudu?**

Uzly můžete zřídit z Azure Portal a rozšířit svůj privátní cloud z portálu CloudSimple. Svůj privátní Cloud můžete rozšířit přidáním uzlů do existujícího clusteru vSphere nebo vytvořením nového clusteru vSphere. Informace o postupu najdete v tématu věnovaném [rozšíření privátního cloudu CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Co se stane s privátním cloudem během údržby?**

CloudSimple poskytuje pravidelné oznámení dnů před naplánovanou údržbou. Údržba se provádí nerušivým způsobem, aby se zajistila dostupnost vašeho privátního cloudu. Údržba může být z následujících typů:

- **Infrastruktura CloudSimple**: Infrastruktura CloudSimple je navržená tak, aby byla vysoce dostupná. Během údržby je připojení a dostupnost vašeho privátního cloudu zajištěno tím, že se v jednom okamžiku aktualizují redundantní komponenty, aniž by to mělo žádný vliv. Máte přístup k vašemu privátnímu cloudu vCenter, všem virtuálním počítačům, připojení k Internetu z vašeho privátního cloudu a připojení k místnímu nebo Azure.
- **Portál CloudSimple**: Během údržby nemusí být některé funkce na portálu CloudSimple dostupné nebo můžou být zakázané. Oznámení o údržbě zahrnuje informace o tom, co se dá na portálu provést.

## <a name="connectivity"></a>Připojení

**Jaké mám možnosti připojení k síti CloudSimple region?**

CloudSimple poskytuje tři různé možnosti připojení pro připojení k síti vaší CloudSimple oblasti. Všechny tři možnosti lze použít společně:

- Připojení Azure ExpressRoute z místního datacentra k síti CloudSimple region: Zabezpečená privátní připojení s nízkou latencí, které vychází z místního okruhu ExpressRoute s vaším okruhem CloudSimple ExpressRoute pomocí Global Reach. Pokud chcete nastavit připojení, přečtěte si téma [připojení z místního prostředí k CloudSimple pomocí ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- ExpressRoute připojení z vaší virtuální sítě Azure do vaší sítě CloudSimple region: Vysokorychlostní soukromé privátní připojení s nízkou latencí, které přemostění vaší virtuální sítě v Azure pomocí okruhu ExpressRoute CloudSimple pomocí bran virtuální sítě. Pokud chcete nastavit připojení, přečtěte si téma [připojení privátního cloudového prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Připojení VPN typu Site-to-Site z místního datacentra do vaší sítě v CloudSimple oblasti: Zabezpečená virtuální privátní síť z místního zařízení VPN do oblasti privátního cloudu CloudSimple. Pokud chcete nastavit připojení, přečtěte si téma [nastavení připojení VPN mezi místní sítí a privátním cloudem CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Návody se připojit k privátnímu cloudu?**

Podrobnosti o vašem privátním cloudu můžete zobrazit na portálu CloudSimple. Chcete-li se připojit k serveru vCenter, který odpovídá vašemu privátnímu cloudu, ujistěte se, že je vytvořeno připojení k síti pomocí typu Site-to-site, Point-to-site nebo ExpressRoute. Pak z Azure Portal spusťte portál CloudSimple. Vyberte **Spustit klienta vSphere** na domovské stránce nebo na stránce s podrobnostmi privátního cloudu.

**Jaká je výhoda okruhu ExpressRoute?**

Okruh Azure ExpressRoute poskytuje vysokorychlostní zabezpečené připojení s nízkou latencí. CloudSimple poskytuje vyhrazený okruh ExpressRoute na oblast na zákazníka. Pomocí tohoto okruhu můžete vytvořit zabezpečené připojení z místního prostředí a předplatného Azure.

**Jaké jsou náklady na síť pro připojení k CloudSimple? Jsou nějaké náklady na výstup a z CloudSimple do Azure? Existují v různých oblastech nějaké náklady na výstup?**

Za odchozí přenosy z sítě se neúčtují žádné poplatky. Standardní sazby za Azure se vztahují na jakýkoliv výstupní provoz z vaší virtuální sítě nebo z místního okruhu ExpressRoute.

## <a name="networking"></a>Sítě

**Jaké síťové funkce jsou k dispozici pro můj privátní cloud?**

Můžete zřídit sítě VLAN a jejich podsítě a tabulky brány firewall. Můžete přiřadit veřejné IP adresy a mapovat je na virtuální počítač, který běží ve vašem privátním cloudu. Další informace najdete v tématech [Přehled sítí VLAN a podsítí](cloudsimple-vlans-subnets.md), [Přehled tabulek brány firewall](cloudsimple-firewall-tables.md)a [Přehled veřejných IP adres](cloudsimple-public-ip-address.md).

**Návody nastavit různé podsítě pro moje aplikace v mém privátním cloudu?**

V privátním cloudu můžete vytvořit sítě VLAN z portálu CloudSimple. Po vytvoření sítě VLAN můžete v privátním cloudu vCenter Vytvořit distribuovanou skupinu portů pomocí sítě VLAN a vytvořit virtuální počítače připojené ke skupině distribuovaných portů. Můžete povolit tabulku brány firewall pro síť VLAN nebo podsíť a definovat pravidla brány firewall pro zabezpečení síťového provozu.

**Jaká nastavení brány firewall jsou pro moje privátní cloudy k dispozici?**

Můžete nakonfigurovat pravidla pro provoz sever-jih a východ-západ. Pravidla jsou definována v tabulce brány firewall. Tabulku brány firewall lze připojit k sítím VLAN v privátním cloudu. Postup instalace najdete v tématu [Nastavení tabulek a pravidel brány firewall pro privátní cloudy](https://docs.azure.cloudsimple.com/firewall).

**Můžu pro virtuální počítače přiřazovat veřejné IP adresy v prostředí privátního cloudu?**

Na portálu CloudSimple můžete snadno přidělit novou veřejnou IP adresu a přidružit ji k privátní IP adrese virtuálního počítače nebo zařízení. Můžete také vytvořit nová pravidla brány firewall nebo použít existující pravidla brány firewall, která umožní provoz z konkrétních portů a konkrétních sad IP adres na portálu. Pokyny k instalaci najdete v tématu [přidělení veřejných IP adres pro prostředí privátního cloudu](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Zabezpečení

**Jaké mám možnosti zabezpečení na CloudSimple?**

Privátní cloud CloudSimple poskytuje následující funkce zabezpečení pro zabezpečení vašeho prostředí privátního cloudu:

- **Šifrování dat v klidovém umístění:** Můžete zašifrovat neaktivní data, která se nachází v úložišti síti vSAN v privátním cloudu. Síti vSAN podporuje management server externích klíčů, které se dají nasadit ve vaší virtuální síti Azure nebo místním prostředí. Další informace najdete v tématu [Konfigurace šifrování síti vSAN pro privátní cloud CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Zabezpečení sítě:** Pomocí pravidel brány firewall můžete řídit tok provozu sítě z a do privátního cloudu z Internetu, místního prostředí a v rámci podsítí vašeho privátního cloudu.
- **Zabezpečené, privátní připojení:** Zabezpečené privátní připojení mezi vaší místní sítí a vaším předplatným Azure.

## <a name="compute"></a>Compute

**Jaký druh hostitelů je k dispozici?**

CloudSimple nabízí dva typy hostitelů:

* **Uzel CS28**: Procesor: 2x 2,2 GHz, celkem 28 jader, 48 HT. Paměť RAM: 256 GB. Storage: 1600-GB NVMe cache, 5760-GB dat (all-Flash). Sítě 2x25Gbe NIC.
* **Uzel CS36**: PROCESOR 2x 2,3 GHz, celkem 36 jader, 72 HT. Paměť RAM: 512 GB. Storage: 3200 GB NVMe cache 11 520-GB data (vše-Flash). Sítě 2x25Gbe NIC.

**Jak se zpracovávají chyby hardwaru?**

Všechny infrastruktury CloudSimple jsou nepřetržitě monitorovány platformou CloudSimple a týmy jeho provozu. Pokud se zjistí selhání hardwaru, přidá se do vašeho privátního cloudu nový uzel. Uzel, který selhal, se odebere, aby se zajistila vysoká dostupnost vašeho privátního cloudu.

## <a name="storage"></a>Storage

**Jaký typ úložiště podporuje privátní cloud?**

CloudSimple nabízí **úložiště VMware síti vSAN pro všechny aplikace** , které obsahuje všechny privátní cloudy. Každý vSphere se vytvoří s vlastním úložištěm dat síti vSAN. Další informace najdete v tématu [komponenty VMware privátního cloudu – síti vSAN úložiště](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Je šifrování dat podporováno?**
Ano. Úložiště síti vSAN můžete nastavit v privátním cloudu tak, aby používala službu Key management server (KMS), která je nasazená místně nebo v Azure pro šifrování dat uložených v síti vSAN.

**Jak se zpracovávají neúspěšné disky?**

CloudSimple monitoring průběžně monitoruje všechny hardwarové součásti privátního cloudu. Pokud je zjištěna chyba disku nebo je disk identifikován jako neúspěšný na základě heuristiky, do privátního cloudu se automaticky přidá nový uzel. Uzel s neúspěšným nebo neúspěšným diskem se odebere z privátního cloudu.

## <a name="vmware"></a>VMware

**Návody provést rozsáhlou nahrávání a migraci aplikací a dat z místního prostředí?**

CloudSimple poskytuje nativní řešení VMware vSphere. Libovolný nástroj, který se používá pro migraci hromadných dat, se dá použít s privátním cloudem CloudSimple. K dispozici jsou tyto možnosti:

- HCX VMware pro hromadnou migraci dat.
- Studená migrace dat pomocí úložiště vMotion z místního prostředí do CloudSimple.

**Můžu nainstalovat nějaké nástroje VMware?**

CloudSimple poskytuje nativní řešení VMware vSphere. Libovolný nástroj, který se používá ke správě místního prostředí vSphere, se dá použít na CloudSimple. CloudSimple podporuje model BYOL (Přineste si vlastní licenci) pro instalaci nástrojů VMware.

**Jak se spravují aktualizace a upgrady?**

CloudSimple spravuje a aktualizuje všechny součásti infrastruktury vašeho privátního cloudu plynule nerušivým způsobem. Aktualizace nebo oprava zabezpečení vydaná výrobci VMware nebo infrastruktury je naplánována na aktualizaci, jakmile bude kvalifikována službou CloudSimple.

CloudSimple neprovádí upgrady nebo aktualizace aplikací nainstalovaných v privátním cloudu.

## <a name="azure-integration"></a>Integrace Azure

**Které služby Azure jsou podporovány?**

CloudSimple poskytuje připojení Azure ExpressRoute k vašemu předplatnému v Azure. Všechny služby, které běží ve vašem předplatném, mají síťové připojení k vašemu privátnímu cloudu a můžou se připojit k privátnímu cloudu. Příklady:

- **Azure Active Directory**: Jako zdroj identity pro CloudSimple vCenter použijte Azure Active Directory.
- **Azure Storage**: Úložiště můžete použít k ukládání záloh, imagí a dalších dat z vašeho privátního cloudu.
- **Hybridní aplikace**: Můžete vytvořit architekturu aplikace, která zahrnuje veřejné a privátní cloudy. Můžete například vytvořit webové servery v Azure, které přistupují k aplikačním a databázovým serverům v privátním cloudu CloudSimple.
- **Azure monitor** a **Azure Security Center**: Úlohy, které běží na VMware, můžou používat monitorování a Security Center pro protokolování, metriky výkonu a správu zabezpečení.

**Návody namapovat klienty VMware do Azure?**

CloudSimple poskytuje jedinečnou schopnost spravovat vaše virtuální počítače VMware v privátním cloudu z Azure Portal. Fond zdrojů vCenter nakonfigurovaný pomocí omezení prostředků, která chcete, můžete namapovat na vaše předplatné globálním správcem. 

**Jaké výhody licencování mám získat s Azure?**

Pomocí CloudSimple můžete využít výhod Zvýhodněné hybridní využití Azure a ušetřit až 90 procent licencí, abyste zachovali investice do licencí Microsoftu a snížili celkové náklady na vlastnictví v porovnání s jinými cloudy. Získáte také rozšířené aktualizace zabezpečení pro Windows Server 2008 a Microsoft SQL Server 2008. BYOL do cloudu pro běžné aplikace, jako je Veeam, Zerto a další, zajistěte nízké náklady. 
