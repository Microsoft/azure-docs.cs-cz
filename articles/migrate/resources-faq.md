---
title: Nejčastější dotazy k Azure Migrateu (FAQ) | Microsoft Docs
description: Řeší Nejčastější dotazy týkající se Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snehaa
ms.openlocfilehash: 0708502087ae6880d9559cf17f0ba9982b2ba040
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372483"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Nejčastější dotazy k Azure Migrate (FAQ)

Tento článek obsahuje nejčastější dotazy týkající se Azure Migrate. Pokud máte další dotazy po přečtení tohoto článku, publikujte je na [Azure Migrate Fórum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Obecné

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Které geografické oblasti Azure podporuje Azure Migrate?

Seznam pro [VMware sem](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) můžete najít tady a pro [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Jak se Azure Migrate liší od Azure Site Recovery?

Azure Migrate poskytuje centralizované centrum pro spouštění, spouštění a sledování zjišťování, posuzování a migrace počítačů a úloh do Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) je řešení zotavení po havárii. Migrace Azure Migrate serveru využívá Azure Site Recovery v back-endu k povolení migračních scénářů pro migraci místních počítačů pomocí přenesených a posunutí.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Zařízení Azure Migrate (VMware/fyzické servery)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Jak se zařízení Azure Migrate připojuje k Azure?

Připojení může být přes Internet nebo můžete použít ExpressRoute s veřejným partnerským vztahem/Microsoftu.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Jaké požadavky na připojení k síti jsou potřeba pro Azure Migrate posouzení a migrace serveru

Pro adresy URL a porty potřebné pro Azure Migrate komunikace s Azure si přečtěte matrice podpory [VMware](migrate-support-matrix-vmware.md) a [Hyper-V](migrate-support-matrix-hyper-v.md) .

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>Je možné posílit virtuální počítač zařízení, který jste nastavili pomocí šablony?

Do šablony se dají přidat další komponenty (například anti-virus), pokud jsou potřeba komunikační a bezpečnostní pravidla brány firewall, která Azure Migrate zařízení vynechala.   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Jaká data shromažďuje zařízení Azure Migrate?

Podrobnosti o datech shromažďovaných v Azure Migrate zařízení najdete [tady](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Existuje nějaký dopad na výkon analyzovaného prostředí VMware nebo Hyper-V?

Díky průběžnému profilování dat o výkonu Azure Migrate zařízení profily na místních počítačích k měření údajů o výkonu virtuálních počítačů. To má skoro nulový dopad na výkon hostitele Hyper-V/ESXi a také na vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Kde jsou shromážděná data uložená a jak dlouho?

Data shromažďovaná zařízením Azure Migrate se ukládají do umístění Azure, které zadáte při vytváření projektu migrace. Data jsou bezpečně uložená v rámci předplatného Microsoft a odstraňují se při odstranění Azure Migrate projektu.

Pokud pro vizualizaci závislostí nainstalujete agenty na virtuální počítače, uloží se data shromážděná agenty závislostí do USA v Log Analytics pracovním prostoru vytvořeném v předplatném Azure. Tato data se odstraní při odstranění pracovního prostoru Log Analytics v rámci vašeho předplatného. [Další informace](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>Jaký je objem dat odesílaných zařízením Azure Migrate během nepřetržitého profilace?

Objem dat odesílaných do Azure Migrate se liší v závislosti na několika parametrech. Aby bylo možné poskytnout orientační číslo, Azure Migrate projekt s 10 počítači (každý s jedním diskem a jedním síťovým ROZHRANÍm) posílá přibližně 50 MB za den. Jedná se o přibližnou hodnotu, která se mění v závislosti na počtu datových bodů pro síťové karty a disky (posílaná data je nelineární, pokud se zvyšuje počet počítačů, síťových karet nebo disků).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Jsou data zašifrovaná na klidovém a přenosovém stavu?

Ano pro obojí. Metadata se bezpečně odesílají do služby Azure Migrate přes Internet prostřednictvím protokolu HTTPS. Metadata se ukládají do [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)a v [Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) v předplatném Microsoftu a jsou šifrovaná v klidovém stavu.

Data shromážděná agenty závislosti se zašifrují i při přenosu (zabezpečený protokol HTTPS) a ukládají se do Log Analyticsho pracovního prostoru v rámci předplatného uživatele. Je také zašifrovaný v klidovém stavu.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Jak zařízení Azure Migrate komunikuje s vCenter Server a službou Azure Migrate?

Zařízení se připojí k vCenter Server (port 443) s použitím přihlašovacích údajů, které jste zadali při nastavení zařízení. Pomocí VMware PowerCLI se dotazuje na vCenter Server k shromažďování metadat o virtuálních počítačích spravovaných pomocí vCenter Server. Shromažďuje jak konfigurační data virtuálních počítačů (jader, paměti, disků, síťových adaptérů atd.), tak i historii výkonu každého virtuálního počítače za poslední měsíc. Shromážděná metadata se pak odesílají do posouzení serveru Azure Migrate (přes Internet prostřednictvím protokolu HTTPS) pro posouzení.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Můžu připojit stejné zařízení k více serverům vCenter?

Ano, jedno zařízení Azure Migrate se dá použít ke zjišťování více serverů vCenter, ale ne souběžně. Je nutné spustit zjišťování jednu po druhém.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Změnil (a) jsem velikost mého počítače. Můžu znovu spustit posouzení?

Zařízení Azure Migrate průběžně shromažďuje informace o místním prostředí. Posouzení je ale snímkem v čase místních virtuálních počítačů. Pokud změníte nastavení na virtuálním počítači, který chcete vyhodnotit, použijte k aktualizaci posouzení nejnovější změny možnost přepočítat.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Jak můžu zjistit víceklientské prostředí v Azure Migrate posouzení serveru?

V případě VMware máte prostředí, které je sdíleno mezi klienty a nechcete zjišťovat virtuální počítače jednoho klienta v rámci předplatného jiného tenanta, vytvořit vCenter Server přihlašovací údaje s přístupem pouze k těmto virtuálním počítačům, které chcete zjistit. Pak použijte přihlašovací údaje při zahájení zjišťování v zařízení Azure Migrate.

V případě technologie Hyper-V používá zjišťování přihlašovací údaje hostitele Hyper-V, a pokud virtuální počítače sdílejí stejný Hostitel Hyper-V, neexistuje v současnosti žádný způsob, jak zjišťování oddělit.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Kolik virtuálních počítačů je možné zjistit pomocí jediného zařízení migrace?

Můžete zjistit až 10 000 virtuálních počítačů VMware a až 5 000 virtuálních počítačů Hyper-V s použitím jediného zařízení migrace.  Pokud máte ve svém místním prostředí více počítačů, Naučte se škálovat [technologie Hyper-V](scale-hyper-v-assessment.md) a odhad [VMware](scale-vmware-assessment.md) .

## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Hodnocení serverů

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: Vyhodnocování podpory serverů pro fyzické servery?

Ne, Azure Migrate aktuálně nepodporuje posouzení fyzických serverů.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Vyžaduje Azure Migrate vCenter Server zjišťování prostředí VMware?

Ano, Azure Migrate potřebuje vCenter Server ke zjišťování prostředí VMware. Nepodporuje zjišťování hostitelů ESXi, které nejsou spravované pomocí vCenter Server.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaký je rozdíl mezi použitím Azure Migrate: Vyhodnocování serveru a mapová sada nástrojů?

Azure Migrate: Posouzení serveru poskytuje vyhodnocení migrace, které vám pomůžou s připraveností na migraci, a vyhodnocení úloh pro migraci do Azure. [Sada nástrojů Microsoft Assessment and Planning (map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) má jiné funkce, jako je například plánování migrace pro novější verze klientských a serverových operačních systémů Windows a sledování využívání softwaru. Pro tyto scénáře pokračujte v používání sady MAP Toolkit.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Jak je Azure Migrate: Vyhodnocování serveru se liší od Plánovač nasazení služby Azure Site Recovery?

Azure Migrate: Posuzování serveru je nástroj pro plánování migrace. Plánovač nasazení služby Azure Site Recovery je nástroj pro plánování zotavení po havárii.

- **Migrace z VMware/Hyper-V do Azure**: Pokud máte v úmyslu migrovat vaše místní servery do Azure, použijte Azure Migrate: Nástroj pro vyhodnocení serveru pro plánování migrace. Nástroj posuzuje místní úlohy a poskytuje pokyny, poznatky a mechanismy, které vám pomůžou při migraci do Azure. Jakmile budete připraveni k vašemu plánu migrace, můžete použít nástroje, jako je Azure Migrate: Migrace serveru, aby se počítače migrovali do Azure.
- **Zotavení po havárii z VMware/Hyper-V do Azure**: Pro zotavení po havárii do Azure pomocí Site Recovery použijte Plánovač nasazení Site Recovery pro plánování zotavení po havárii. Site Recovery Plánovač nasazení provede hloubkové, Site Recovery specifické vyhodnocení místního prostředí. Poskytuje doporučení Site Recovery pro úspěšné operace zotavení po havárii, jako je replikace a převzetí služeb při selhání virtuálních počítačů.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Podporuje Azure Migrate odhad nákladů na základě smlouva Enterprise (EA)?

Azure Migrate v tuto chvíli nepodporuje odhad nákladů pro [smlouva Enterprise nabídku](https://azure.microsoft.com/offers/enterprise-agreement-support/). Alternativním řešením je zadat průběžné platby jako nabídku a ručně zadat procento slevy (použitelné pro předplatné) v poli sleva ve vlastnostech posouzení.

  ![Sleva](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaký je rozdíl mezi tím, jak se mění velikost podle místního nastavení a velikosti na základě výkonu?

- V rámci as, Azure Migrate nepovažují data o výkonu virtuálních počítačů za místní. Velikost virtuálních počítačů vychází z místních konfigurací. – Při změně velikosti na základě výkonu je velikost založena na datech využití.
- Například pokud má místní virtuální počítač 4 jádra a 8 GB paměti s 50% využitím procesoru a 50% využití paměti, jako při změně velikosti v místním prostředí se doporučuje SKU virtuálního počítače Azure se čtyřmi jádry a 8 GB paměti. Velikost na základě výkonu však doporučuje SKU virtuálních počítačů se dvěma jádry a 4 GB, protože procento využití je považováno za.
- Podobně závisí velikost disku na dvou kritériích pro určení velikosti a typu úložiště.
= Pokud je kritérium změny velikosti v závislosti na výkonu a typ úložiště je automatický, hodnoty IOPS a propustnosti disku se při určování typu cílového disku (Standard nebo Premium) posuzují.
- Pokud je kritérium změny velikosti v závislosti na výkonu a typ úložiště je Premium, doporučuje se disk Premium. SKU disku úrovně Premium se vybere na základě velikosti místního disku. Stejná logika se používá k určení velikosti disku, pokud je kritérium změny velikosti místní a typ úložiště je Standard nebo Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaký dopad má využití historie výkonu a percentilu na velikost doporučení?

Tyto vlastnosti se zohledňují pouze při určování velikosti na základě výkonu.

- Azure Migrate shromažďuje historii výkonu místních počítačů a používá je k tomu, aby v Azure doporučila velikost virtuálního počítače a typ disku.
- Zařízení nepřetržitě profiluje místní prostředí za účelem shromažďování dat o využití v reálném čase každých 20 sekund. Zařízení seskupuje 20sekundové vzorky a pro každých 15 minut vytvoří jeden datový bod. Jeden datový bod se vytvoří tak, že zařízení ze všech 20sekundových vzorků vybere maximální hodnotu a odešle ji do Azure.
- Když vytvoříte posouzení v Azure (na základě hodnoty doby trvání a percentilu historie výkonu), Azure Migrate vypočítá efektivní hodnotu využití a použije ji ke změně velikosti.
- Pokud například nastavíte dobu trvání výkonu na jeden den a hodnotu percentilu na 95. percentil, Azure Migrate použije pro poslední den vzorové body 15 minut odeslané kolektorem, seřadí je ve vzestupném pořadí a hodnota 95. percentilu se vybere jako efektivní využití.
- Hodnota 95. percentilu zajišťuje, že budete ignorovat jakékoli odlehlé hodnoty, ke kterým může dojít, pokud použijete 99 percentil. Pokud chcete vybrat maximální využití v určitém období a nechcete přijít o žádné odlehlé hodnoty, měli byste vybrat 99. percentil.

### <a name="what-is-dependency-visualization"></a>Co je Vizualizace závislostí?

Vizualizace závislostí umožňuje vyhodnotit skupiny virtuálních počítačů pro migraci s větší jistotou. Křížově kontroluje závislosti počítačů před spuštěním posouzení. Vizualizace závislostí pomáhá zajistit, že nic nezbývá, a při migraci do Azure se vyhnete neočekávanému výpadku. Azure Migrate využívá řešení Service Map v protokolech Azure Monitor, aby bylo možné povolit vizualizaci závislostí.

> [!NOTE]
> Funkce vizualizace závislosti není v Azure Government k dispozici.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Musím platit za použití funkce Vizualizace závislostí?

Ne. [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Musím pro vizualizaci závislostí nainstalovat cokoli?

Pokud chcete používat vizualizaci závislostí, je potřeba stáhnout a nainstalovat agenty na každý místní počítač, který chcete vyhodnotit.

- Na každém počítači je potřeba nainstalovat [agenta Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) .
- Na každém počítači je potřeba nainstalovat [agenta závislostí](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) .
- Kromě toho, pokud máte počítače bez připojení k Internetu, je potřeba stáhnout a nainstalovat Log Analytics bránu.

Pokud nepoužíváte vizualizaci závislostí, nepotřebujete tyto agenty.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Můžu použít existující pracovní prostor pro vizualizaci závislostí?

Ano, existující pracovní prostor můžete připojit k projektu migrace a využít ho pro vizualizaci závislostí. [Další informace](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Můžu sestavu Vizualizace závislostí exportovat?

Ne, vizualizace závislosti se nedá exportovat. Vzhledem k tomu, že Azure Migrate používá Service Map pro vizualizaci závislostí, můžete použít [rozhraní Service map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) k získání závislostí ve formátu JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Jak můžu automatizovat instalaci Microsoft Monitoring Agent (MMA) a agenta závislostí?

[Použijte tento skript](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) pro instalaci agentů. [Postupujte podle těchto pokynů](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) a nainstalujte MMA pomocí příkazového řádku nebo automatizace. Pro MMA [Tento skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)Využijte.

Kromě skriptů můžete použít nástroje pro nasazení, jako je System Center Configuration Manager, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) atd. k nasazení agentů.

### <a name="what-operating-systems-are-supported-by-mma"></a>Které operační systémy podporuje MMA?

- [Projděte](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) si seznam operačních systémů Windows, které podporuje MMA.
- [Projděte](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) si seznam operačních systémů Linux podporovaných nástrojem MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Jaké jsou operační systémy podporované agentem závislostí?

[Přečtěte si](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) operační systémy Windows podporované agentem závislostí.
[Projděte](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) si seznam operačních systémů Linux podporovaných agentem závislostí.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Je možné vizualizovat závislosti v Azure Migrate po dobu delší než hodinu?
Ne, můžete vizualizovat závislosti až o hodinu. Můžete se vrátit k určitému datu v historii, až do posledního měsíce, ale maximální doba trvání vizualizace je hodina. Například můžete použít dobu trvání na mapě závislostí k zobrazení závislostí včera, ale lze je zobrazit pouze v rámci jednoho hodiny okna. Můžete ale použít protokoly Azure Monitor k dotazování na [data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) v delší době trvání.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Podporuje se pro skupiny s více než 10 virtuálními počítači Vizualizace závislostí?
Můžete [vizualizovat závislosti pro skupiny](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) s až 10 virtuálními počítači. Pokud máte skupinu s více než 10 virtuálními počítači, doporučujeme rozdělit skupinu do menších skupin a potom tyto závislosti vizualizovat.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Migrace serverů

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Jak je Azure Migrate: Migrace serveru se liší od Azure Site Recovery?

Azure Migrate: Migrace serveru využívá replikační modul Site Recovery pro migraci virtuálních počítačů VMware založenou na agentech, migraci virtuálních počítačů Hyper-V a migraci fyzických serverů do Azure. Možnost bez agenta pro migraci virtuálních počítačů VMware je nativně integrovaná do migrace serveru.

## <a name="next-steps"></a>Další postup
Přečtěte si [přehled Azure Migrate](migrate-services-overview.md)
