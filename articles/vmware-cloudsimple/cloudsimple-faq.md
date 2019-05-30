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
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358525"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Nejčastější dotazy ohledně řešení VMware podle CloudSimple

Nejčastější dotazy a odpovědi týkající se řešení VMware podle CloudSimple, které vám pomůžou pochopit, služby a jak ji používat.  Otázky a odpovědi jsou uspořádány do následujících kategorií.

* CloudSimple služby
* Připojení
* Sítě
* Zabezpečení
* Compute
* Úložiště
* VMware
* Azure Integration
  
## <a name="cloudsimple-service"></a>CloudSimple služby

**Co je řešení VMware podle CloudSimple?**

**Řešení VMware podle CloudSimple** transformuje a rozšiřuje úloh VMware do privátních, vyhrazených cloudů v Azure během několika minut. My zajistíme zřizování, správě infrastruktury a Orchestrace úloh mezi místními a Azure. Vzhledem k tomu, že vaše aplikace běží přesně stejnou místní i v Azure, můžete těžit z pružnost a služeb v cloudu bez složitosti změna architektury aplikace. CloudSimple snižuje celkové náklady na vlastnictví pomocí cloudového využití modelu, který poskytuje na vyžádání zřizování, platit jako růstu a optimalizace kapacity.  Zobrazit [co je řešení VMware v Azure v jednotlivých CloudSimple](cloudsimple-vmware-solutions-overview.md) pro scénáře, výhody a funkce.

**Co je CloudSimple privátního cloudu?**

Zřizování privátních, vyhrazených cloudu skládající se z vysokovýkonné výpočetní prostředí, úložiště a síťové prostředí, které jsou nasazené na infrastruktury Microsoft Azure (místo hardwaru a datového centra) v umístění Azure.  Privátní Cloud poskytuje nativní platformě VMware "as-a-service". V podmínkách VMware každý privátní Cloud obsahuje přesně jednu instanci serveru vcenter. VCenter Server spravuje více uzlů ESXi obsažené v jedné nebo více clusterech vSphere, společně s odpovídající virtuální síť SAN (vSAN) úložiště. CloudSimple služby může obsahovat více privátních cloudů ve vašem předplatném Azure.  Další informace o privátních cloudech, naleznete v tématu [přehled privátního cloudu](cloudsimple-private-cloud.md).

**Kde je služba CloudSimple dostupná?**

CloudSimple je dostupná v oblastech USA – východ a USA – západ.

**Jak povolím předplatného CloudSimple?**

Obraťte se na obchodního zástupce společnosti Microsoft [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) povolit předplatné služby CloudSimple. Zadejte své ID předplatného v e-mailu, pro které chcete CloudSimple služby povolena.  

**Jak získám přístup k portálu CloudSimple?**

CloudSimple portálu přístup z portálu Azure portal.  Zobrazit [přístup k řešení VMware portálem CloudSimple z webu Azure portal](https://docs.azure.cloudsimple.com/access-cloudsimple-portal) , kde najdete podrobnosti o přístupu k CloudSimple portálu. 

**Jak můžu zvýšit kapacitu privátního cloudu?**

Zakoupit uzly z webu Azure portal a rozbalte vašeho privátního cloudu z portálu CloudSimple.  Privátní Cloud můžete rozšířit přidáním dalších uzlů do existujícího clusteru vSphere nebo vytvořením nového clusteru vSphere.  Zobrazit [rozbalte CloudSimple privátního cloudu](https://docs.azure.cloudsimple.com/expand-private-cloud) článku pro proceduru.

**Co se stane Moje privátního cloudu při údržbě?**

CloudSimple poskytuje pravidelná oznámení dní před plánovanou údržbu.  Údržba se provádí v nenarušující způsob, jak zajistit dostupnost vašeho privátního cloudu.  Údržba může být z následujících typů:

1. **CloudSimple infrastruktury:**  CloudSimple infrastruktury byla navržena jako vysoce dostupný.  Během údržby připojení a dostupnosti vašeho privátního cloudu je, že jsou splněné aktualizací redundantní komponenty jeden po druhém žádný vliv.  Budete mít přístup k vCenter privátního cloudu, všechny virtuální počítače, připojení k Internetu z privátního cloudu a připojení k místní nebo v Azure.
2. **CloudSimple portálu:** Během údržby nemusí být některé funkce na portálu CloudSimple přístupné nebo zakázáno.  Oznámení o údržbě bude obsahovat podrobnosti o co se dá dělat na portálu.

## <a name="connectivity"></a>Připojení

**Jaké jsou možnosti připojení k síti CloudSimple oblasti?**

CloudSimple poskytuje tři různé možnosti připojení pro připojení k síti CloudSimple oblasti.  Všechny tři můžete použít společně.

1. Připojení ExpressRoute z vašeho místního datového centra k síti oblasti CloudSimple - vysokorychlostní s nízkou latencí zabezpečené privátní připojení přemostění váš okruh ExpressRoute s místními s váš okruh CloudSimple ExpressRoute pomocí globální dosah. Zobrazit [CloudSimple pomocí ExpressRoute připojit z místního](https://docs.azure.cloudsimple.com/on-premises-connection) článku pro nastavení připojení.
2. Připojení ExpressRoute z vaší virtuální sítí Azure k síti oblasti CloudSimple - vysokorychlostní s nízkou latencí zabezpečené privátní připojení přemostění vaší virtuální sítě v Azure s váš okruh CloudSimple ExpressRoute pomocí brány virtuální sítě.  Zobrazit [svoje prostředí CloudSimple privátního cloudu připojit k virtuální síti Azure, pomocí služby ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection) článku pro nastavení připojení.
3. Připojení VPN typu Site-to-Site z vašeho místního datového centra k oblasti sítě CloudSimple – zabezpečené virtuální privátní sítě z vašeho místního zařízení VPN pro vaši oblast CloudSimple privátního cloudu.  [Nastavení připojení VPN mezi vaší místní sítí a CloudSimple privátního cloudu] najdete v článku pro nastavení připojení k síti VPN.

**Jak připojit do privátního cloudu**

Na portálu CloudSimple můžete zobrazit podrobnosti o privátního cloudu. Pro připojení k serveru vCenter odpovídající privátní Cloud, zajistěte, aby se připojení k síti vytvořených pomocí Site-to-Site, Point-to-Site nebo ExpressRoute. Spusťte portál CloudSimple z webu Azure portal a klikněte na tlačítko *spuštění vSphere klienta* na domovské stránce nebo na stránce s podrobnostmi privátního cloudu.

**Co je výhodou okruh ExpressRoute?**

Okruh Azure ExpressRoute poskytuje zabezpečené připojení vysokorychlostní s nízkou latencí.  CloudSimple poskytuje vyhrazeného okruhu ExpressRoute v jedné oblasti na zákazníka.  Pomocí tohoto okruhu, můžou vytvořit zabezpečené připojení z místní nebo předplatného Azure.

**Jaké jsou náklady na síť pro připojení z CloudSimple. Všechny poplatky za odchozí přenos z CloudSimple do Azure? Napříč oblastmi?**

Neplatí žádné poplatky za všechny odchozí síťovou komunikaci.  Standardní sazby Azure platí pro veškerý odchozí provoz z vaší virtuální sítě nebo z místnímu okruhu ExpressRoute.

## <a name="networking"></a>Sítě

**Jaké síťové funkce jsou k dispozici pro tento privátní Cloud?**

Můžete zajistit virtuální místní sítě (a podsítě), tabulky, brány firewall a přiřazovat veřejné IP adresy a mapování na virtuální počítač do privátního cloudu.  Další informace najdete v tématu [Přehled sítí VLAN a podsítě](cloudsimple-vlans-subnets.md), [Přehled brány Firewall tabulky](cloudsimple-firewall-tables.md), a [přehled veřejných IP adres](cloudsimple-public-ip-address.md) článků.

**Jak můžu nastavit různé podsítě pro Moje aplikace v mé privátního cloudu?**

Sítě VLAN na privátní Cloud můžete vytvořit ze svého portálu CloudSimple.  Po vytvoření sítě VLAN, můžete vytvořit skupinu portů distribuované na vCenter privátního cloudu pomocí sítě VLAN a vytvořit virtuální počítače připojené ke skupině distribuované portu.  Můžete povolit bránu firewall tabulky pro sítě VLAN a podsítě a definovat pravidla brány firewall k zabezpečení síťových přenosů.

**Jaká nastavení brány firewall jsou k dispozici pro můj privátní Cloudy?**

Můžete nakonfigurovat pravidla pro provoz sever jih a východozápadním směrem.  Pravidla jsou definovaná v tabulce brány firewall.  Tabulka brány firewall může být připojena k VLAN(s) na privátní Cloud.  Zobrazit [nastavení brány firewall na tabulky a pravidel pro privátní Cloudy](https://docs.azure.cloudsimple.com/firewall) článku proceduře nastavování.

**Můžu přiřazovat veřejné IP adresy pro virtuální počítače ve své prostředí privátního cloudu?**

Na portálu CloudSimple můžete snadno přidělit novou veřejnou IP adresu a přidružte jej k privátní IP adresu vašeho virtuálního počítače nebo zařízení.  Můžete také vytvořit nová pravidla brány firewall nebo existující pravidla brány firewall tak, aby umožnily přenos z určité porty nebo konkrétní sadu IP adres na portálu. Zobrazit [přidělení veřejné IP adresy pro prostředí privátního cloudu](https://docs.azure.cloudsimple.com/public-ips) pro proceduře nastavování.

## <a name="security"></a>Zabezpečení

**Jaké jsou možnosti zabezpečení na CloudSimple?**

CloudSimple privátního cloudu poskytuje následující funkce zabezpečení pro zabezpečení prostředí privátního cloudu:

1. **Data šifrování neaktivních dat**: Můžete šifrovat data umístěná v úložišti sítě vSAN umístěných v privátní Cloud. síť vSAN podporuje externí key management server, který je možné nasadit v Azure virtuální síti nebo v místním prostředí.  Zobrazit [konfigurace sítě vSAN šifrování pro vaše CloudSimple privátního cloudu](https://docs.azure.cloudsimple.com/vsan-encryption) další podrobnosti.
2. **Zabezpečení sítě**: Ovládací prvek tokem provozu sítě z/do vašeho privátního cloudu z Internetu, místní a v rámci podsítě vašeho privátního cloudu pomocí pravidel brány firewall.
3. **Zabezpečené privátní připojení**: Zabezpečené privátní připojení mezi vaší místní sítí a vaše předplatné Azure.

## <a name="compute"></a>Compute

**Jaký druh hostitelé jsou k dispozici?**

CloudSimple nabízí dva typy hostitelů:

* **CS28 uzlu:** CPU:2 x 2,2 GHz, 28. Celkový počet jader, 48 HT.  Paměť RAM: 256 GB.  Storage: 1 600 GB NVMe mezipaměti, 5760 GB dat (typu Flash). Síť: 2x25Gbe NIC
* **CS36 uzlu:** Procesor 2 x 2.3 GHz, 36 celkový počet jader, 72 HT.  Paměť RAM: 512 GB.  Storage: 3200 GB NVMe mezipaměti 11,520 GB dat (typu Flash).  Síť: 2x25Gbe NIC

**Jak se zpracovává selhání hardwaru?**

Veškerá infrastruktura CloudSimple se nepřetržitě monitoruje CloudSimple platformou a naše služby provozní týmy.  Pokud se zjistí selhání hardwaru, přidání nového uzlu do privátního cloudu a neúspěšné uzel je odebrán zajištěna vysoká dostupnost vašeho privátního cloudu.

## <a name="storage"></a>Úložiště

**Jaký typ úložiště je podporována v privátním cloudu?**

Nabízí CloudSimple **úložiště typu flash VMware sítě vSAN** s každou privátního cloudu.  Každý vSphere se vytvoří s vlastní úložiště dat sítě vSAN.  Zobrazit [součásti privátního cloudu VMware - úložiště sítě vSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage) , kde najdete další podrobnosti.

**Je šifrování uložených dat, které jsou podporovány?**
Ano.  Můžete nastavit síť vSAN úložiště vašeho privátního cloudu použít serveru správy klíčů (KMS), která je nasazená místně nebo v Azure pro šifrování dat uložených v síti vSAN

**Jak se zpracovává selhávajících disků?**

Monitorování CloudSimple nepřetržitě monitoruje všechny hardwarové součásti privátního cloudu.  Pokud se detekuje jakékoli selhání disku nebo libovolném disku se identifikuje jako selhání (podle heuristiky), nový uzel se automaticky přidá do privátního cloudu.  Uzel s selhání disku je odebrán z privátního cloudu.

## <a name="vmware"></a>VMware

**Jak provádět ve velkém měřítku nahrávání nebo migrovat aplikace a data z místních?**

CloudSimple poskytuje nativní řešení VMware vSphere.  Libovolný nástroj používaný pro migrace hromadných dat jde použít s CloudSimple privátního cloudu.  Zde jsou některé z dostupných možností:

1. VMware HCX migrace hromadných dat
2. Studená migrace dat používá řešení Storage vMotion z místního na CloudSimple.

**Můžete nainstalovat všechny nástroje VMware?**

CloudSimple poskytuje nativní řešení VMware vSphere.  Libovolný nástroj používaný pro správu vSphere prostředí místní je možné na CloudSimple.  CloudSimple podporuje model Bring Your-používání vlastní licence (BYOL) pro instalaci nástroje VMware tools.

**Jak se spravují aktualizace a upgrady?**

CloudSimple spravuje a aktualizuje všechny součásti infrastruktury privátního cloudu bezproblémové způsobem omezovaly.  Žádné aktualizace nebo opravy zabezpečení vydané společností VMware nebo infrastrukturu dodavatelů bude naplánovaná aktualizace, poté, co je kvalifikována CloudSimple.

CloudSimple neprovádí upgradů nebo aktualizací aplikací instalovaných do privátního cloudu.

## <a name="azure-integration"></a>Azure Integration

**Jaké služby Azure jsou podporovány?**

CloudSimple představují připojení Azure ExpressRoute k vašemu předplatnému Azure.  Všechny služby spuštěné v rámci vašeho předplatného má síťové připojení k vašemu privátního cloudu a se může připojit k privátní Cloud.  Příklady:

1. **Azure Active Directory** jako zdroj identity pro CloudSimple vCenter
2. **Azure storage** pro ukládání záloh, obrázky a další data z vašeho privátního cloudu
3. **Hybridní aplikace** – můžete vytvářet aplikace architektury, která zahrnuje veřejných a privátních cloudů.  Můžete například vytvořit webové servery v Azure, které přístup k aplikační a databázové servery na CloudSimple privátního cloudu.
4. **Azure monitor** a **Azure security center** – úlohy běžící ve VMware je použít pro protokolování, metriky výkonu a správu zabezpečení.

**Jak se mapují klienti VMware do Azure?**

CloudSimple nabízí jedinečnou možnost spravovat virtuální počítače VMware na privátní Cloud z webu Azure portal.  Fond zdrojů serveru vCenter (nakonfigurované s omezeními požadovaný prostředek) lze mapovat na vaše předplatné globálním správcem.  

**Jaké výhod licencování začít s Azure?**

S CloudSimple můžete využít zvýhodněné hybridní využití a uložit až 90 % z licencí zachovávají svou investici do Licenses společnosti Microsoft, což snižuje celkové ve srovnání s jinými cloudy. Kromě toho můžete získat rozšířené aktualizace zabezpečení pro Windows Server 2008 a Microsoft SQL Server 2008.  Zachovat vaše náklady na nízkou s používání vlastní licence (BYOL) do cloudu pro běžné aplikace, jako je Veeam, řešení Zerto a dalších.  