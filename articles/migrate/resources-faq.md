---
title: Nejčastější dotazy k Azure Migrateu (FAQ) | Microsoft Docs
description: Řeší Nejčastější dotazy týkající se Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: ec4cb58692cd98a799f1dc58f60b11a0552829c8
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934923"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: Nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Migrate. Pokud máte další dotazy po přečtení tohoto článku, publikujte je na [Azure Migrate Fórum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Obecné

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Které geografické oblasti Azure Azure Migrate podporují?

Podívejte se na [seznam pro VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) a [seznam pro Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Jaký je rozdíl mezi Azure Migrate a Azure Site Recovery?

Azure Migrate poskytuje centralizované centrum pro zahájení migrace, spouštění a sledování zjišťování a hodnocení počítačů a zatížení a provádění a sledování migrace počítačů a úloh do Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) je řešení zotavení po havárii. Migrace Azure Migrate serveru používá Azure Site Recovery na back-endu k povolení migračních scénářů migrace místních počítačů pomocí přebírání a posunutí.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Návody odstranit projekt Azure Migrate

Pokud chcete odstranit Azure Migrate projekt a jeho přidružené prostředky, včetně webů, trezorů služby Recovery Services, migrovat trezory, trezory klíčů, projekty vyhodnocení atd. Přejít na stránku skupiny prostředků na Azure Portal, vyberte skupinu prostředků, ve které se migruje projekt. bylo vytvořeno a vyberte možnost Zobrazit skryté typy. Pak vyberte níže uvedený projekt migrace a jeho přidružené prostředky a odstraňte je. Případně, pokud se skupina prostředků používá výhradně projektem migrace a přidruženými prostředky, můžete odstranit celou skupinu prostředků. Všimněte si, že tento seznam je vyčerpávající seznam všech typů prostředků vytvořených pro všechny scénáře (zjišťování, vyhodnocení a migrace). Vyhledáte jenom prostředky, které se pro váš scénář vytvořily ve skupině prostředků.

#### <a name="resources-created-for-discovered-assessed-or-migrated-servers-on-vmware-or-physical-servers-resource-type"></a>Prostředky vytvořené pro zjištěné, vyměřené nebo migrované servery na VMware nebo fyzických serverech [prostředek (typ)]:

- "Zařízení" kV (Trezor klíčů)
- Web "zařízení" (Microsoft. OffAzure/VMwareSites)
- ProjectName (Microsoft. migruje/migrateprojects)
- Projekt ProjectName (Microsoft. migruje/assessmentProjects)
- "ProjectName" rsvault (Recovery Services trezor)
- "ProjectName"-MigrateVault-* (Recovery Services trezor)
- migrateappligwsa * (účet úložiště)
- migrateapplilsa * (účet úložiště)
- migrateapplicsa * (účet úložiště)
- migrateapplikv * (Trezor klíčů)
- migrateapplisbns16041 (obor názvů Service Bus)

Poznámka: Odstraňte účty úložiště a trezory klíčů opatrně, protože můžou obsahovat data aplikace a bezpečnostní klíče v uvedeném pořadí.

#### <a name="resources-created-for-discovered-assessed-or-migrated-servers-on-hyper-v-resource-type"></a>Prostředky vytvořené pro zjištěné, vyměřené nebo migrované servery na Hyper-V [prostředek (typ)]:

- ProjectName (Microsoft. migruje/migrateprojects)
- Projekt ProjectName (Microsoft. migruje/assessmentProjects)
- HyperV * kV (Trezor klíčů)
- Web HyperV * (Microsoft. OffAzure/HyperVSites)
- "ProjectName"-MigrateVault-* (Recovery Services trezor) 

Poznámka: Odstraňte Trezor klíčů s upozorněním, že může obsahovat zabezpečovací klíče.


## <a name="azure-migrate-appliance"></a>Zařízení Azure Migrate

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Jak se zařízení Azure Migrate připojuje k Azure?

Připojení může být přes Internet nebo můžete použít Azure ExpressRoute s veřejným partnerským vztahem/Microsoftu.

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Jaké jsou požadavky na připojení k síti Azure Migrate posuzování a migrace serveru?

Informace o adresách URL a portech potřebných k tomu, aby Azure Migrate komunikovaly s Azure, najdete v matricích podpory [VMware](migrate-support-matrix-vmware.md) a [Hyper-V](migrate-support-matrix-hyper-v.md) .

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>Můžu pomocí šablony posílit virtuální počítač zařízení, který jsem nastavil?

Do šablony můžete přidat součásti (například antivirovou ochranu), pokud ponecháte komunikaci a pravidla brány firewall požadovaná pro Azure Migrate zařízení.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Jaká data shromažďuje zařízení Azure Migrate?

Podrobnosti o datech shromažďovaných zařízením Azure Migrate můžete zobrazit [v dokumentaci k Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Existuje nějaký dopad na výkon analyzovaného prostředí VMware nebo Hyper-V?

Zařízení Azure Migrate profily v místních počítačích průběžně měří data o výkonu virtuálních počítačů. Tato profilace má téměř žádný dopad na výkon hostitele Hyper-V/ESXi nebo na vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Kde jsou shromážděná data uložená a za jak dlouho?

Data shromážděná zařízením Azure Migrate se ukládají do umístění Azure, které zvolíte při vytváření projektu migrace. Data jsou bezpečně uložená v předplatném Microsoftu a při odstranění Azure Migrate projektu se odstraní.

Pokud pro vizualizaci závislostí nainstalujete agenty na virtuální počítače, uloží se data shromážděná agenty závislostí do USA v Log Analytics pracovním prostoru vytvořeném v předplatném Azure. Tato data se odstraní při odstranění pracovního prostoru Log Analytics v rámci vašeho předplatného. Další informace najdete v tématu [Vizualizace závislostí](concepts-dependency-visualization.md).

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>Jaký objem dat se během nepřetržitého profilace nahrává pomocí zařízení Azure Migrate?

Objem dat odesílaných do Azure Migrate se liší v závislosti na několika parametrech. Chcete-li získat představu o svazku: Azure Migrate projekt s 10 počítači (každý s jedním diskem a jedním síťovým ROZHRANÍm) posílá přibližně 50 MB za den. Tato hodnota je aproximace. Změní se na základě počtu datových bodů pro síťové karty a disky. (Zvýšení odeslaných dat je nelineární, pokud se zvyšuje počet počítačů, síťových karet nebo disků.)

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Jsou data v klidovém stavu zašifrovaná a přenosná?

Ano, obojí. Metadata se bezpečně odesílají do služby Azure Migrate přes Internet prostřednictvím protokolu HTTPS. Metadata se ukládají do databáze [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) a do služby [Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) v předplatném Microsoftu. Metadata jsou zašifrovaná v klidovém stavu.

Data shromážděná agenty závislosti se zašifrují i při přenosu (zabezpečený protokol HTTPS). Je uložený v pracovním prostoru Log Analytics v rámci vašeho předplatného. V klidovém stavu jsou také šifrované.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Jak zařízení Azure Migrate komunikuje s vCenter Server a službou Azure Migrate?

Zařízení se připojí k vCenter Server (port 443) pomocí přihlašovacích údajů, které jste zadali při nastavení zařízení. Používá VMware PowerCLI k dotazování vCenter Server ke shromažďování metadat o virtuálních počítačích spravovaných pomocí vCenter Server. Shromažďuje konfigurační data o virtuálních počítačích (jádrech, paměti, discích, síťových rozhraních atd.) a také historii výkonu každého virtuálního počítače za minulý měsíc. Shromážděná metadata se pak odesílají do Azure Migrate posouzení serveru (přes Internet přes protokol HTTPS) k posouzení.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Můžu připojit stejné zařízení k více instancím vCenter Server?

Ne. Mezi zařízením a vCenter Server existuje mapování 1:1. Pokud potřebujete zjistit virtuální počítače na více instancích vCenter Server, je nutné nasadit více zařízení.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>Změnil (a) jsem velikost mého počítače. Můžu znovu spustit posouzení?

Zařízení Azure Migrate průběžně shromažďuje informace o místním prostředí. Posouzení je ale snímkem v čase místních virtuálních počítačů. Pokud změníte nastavení na virtuálním počítači, který chcete vyhodnotit, použijte k aktualizaci posouzení nejnovější změny pomocí možnosti Přepočítat.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Jak můžu provést zjišťování ve víceklientském prostředí v Azure Migrate posouzení serveru?

V případě VMware máte prostředí, které je sdíleno mezi klienty a nechcete zjišťovat virtuální počítače jednoho klienta v rámci předplatného jiného tenanta, vytvořit vCenter Server přihlašovací údaje, které budou mít přístup pouze k virtuálním počítačům, které chcete zjišťovat. Tyto přihlašovací údaje pak použijte při zahájení zjišťování v zařízení Azure Migrate.

U technologie Hyper-V používá zjišťování přihlašovací údaje hostitele Hyper-V. Pokud virtuální počítače sdílejí stejného hostitele Hyper-V, není v současnosti žádný způsob, jak zjišťování oddělit.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>Kolik virtuálních počítačů je možné zjistit pomocí jediného zařízení migrace?

Můžete zjistit až 10 000 virtuálních počítačů VMware a až 5 000 virtuálních počítačů Hyper-V s jedním zařízením migrace. Pokud máte ve svém místním prostředí více počítačů, Naučte se škálovat [technologie Hyper-V](scale-hyper-v-assessment.md) a odhad [VMware](scale-vmware-assessment.md) .

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Můžu z projektu odstranit zařízení Azure Migrate?
V současné době odstranění zařízení z projektu se nepodporuje. Jediným způsobem, jak zařízení odstranit, je odstranit skupinu prostředků, která má Azure Migrate projekt, přidružená k zařízení, ale zároveň odstraní další registrovaná zařízení, zjištěný inventář, posouzení a všechny další artefakty Azure. přidruženo k projektu ve skupině prostředků.

## <a name="azure-migrate-server-assessment"></a>Vyhodnocování serveru Azure Migrate

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Podporuje Azure Migrate vyhodnocení na fyzických serverech hodnocení serveru?

Ne, Azure Migrate v současné době nepodporuje posouzení fyzických serverů.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Vyžaduje Azure Migrate vCenter Server provádění zjišťování v prostředí VMware?

Ano, Azure Migrate potřebuje vCenter Server k provedení zjišťování v prostředí VMware. Nepodporuje zjišťování hostitelů ESXi, které nejsou spravované pomocí vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaký je rozdíl mezi Azure Migrate posuzování serveru a mapou sady nástrojů?

Azure Migrate posouzení serveru poskytuje vyhodnocení migrace, které vám pomůžou s připraveností na migraci, a vyhodnocení úloh pro migraci do Azure. [Sada nástrojů Microsoft Assessment and Planning (map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) pomáhá s dalšími úkoly, jako je plánování migrace novějších verzí klientských a serverových operačních systémů Windows a sledování využívání softwaru. Pro tyto scénáře pokračujte v používání sady MAP Toolkit.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Jaký je rozdíl mezi Azure Migrate posouzení serveru a Plánovač nasazení služby Azure Site Recovery?

Vyhodnocování serveru Azure Migrate je nástroj pro plánování migrace. Plánovač nasazení služby Azure Site Recovery je nástroj pro plánování zotavení po havárii.

**Migrace z VMware/Hyper-V do Azure**: Pokud plánujete migrovat vaše místní servery do Azure, použijte Azure Migrate hodnocení serveru pro plánování migrace. Posuzuje místní úlohy a poskytuje doprovodné materiály, poznatky a nástroje, které vám pomůžou s migrací vašich serverů do Azure. Až budete připraveni k vašemu plánu migrace, můžete k migraci počítačů do Azure použít nástroje, jako je Azure Migrate migrace serveru.

**Zotavení po havárii z VMware/Hyper-V do Azure**: Pro zotavení po havárii do Azure prostřednictvím Site Recovery použijte Plánovač nasazení Site Recovery pro plánování. Site Recovery Plánovač nasazení provede hloubkové, Site Recovery specifické vyhodnocení místního prostředí. Poskytuje doporučení Site Recovery pro úspěšné operace po havárii, jako je replikace a převzetí služeb při selhání virtuálních počítačů.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Podporuje Azure Migrate odhad nákladů pro program smlouva Enterprise (EA)?

Azure Migrate v tuto chvíli nepodporuje odhad nákladů pro [smlouva Enterprise program](https://azure.microsoft.com/offers/enterprise-agreement-support/). Alternativním řešením je zadat průběžné **platby** jako **nabídku** a ručně zadat procento slevy (platné pro předplatné) v poli **sleva** ve vlastnostech posouzení:

  ![Vlastnosti posouzení](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaký je rozdíl mezi tím, jak se mění velikost podle místního nastavení a velikosti na základě výkonu?

Při změně velikosti místních Azure Migrate neuvažují data o výkonu virtuálních počítačů. Velikost virtuálních počítačů vychází z místních konfigurací. Při změně velikosti na základě výkonu je velikost založena na datech využití.

Představte si třeba místní virtuální počítač se 4 jádry a 8 GB paměti při 50% využití CPU a 50% využití paměti. U tohoto virtuálního počítače doporučí velikost SKU virtuálního počítače Azure, který má čtyři jádra a 8 GB paměti. Změna velikosti na základě výkonu doporučuje SKU virtuálních počítačů, které mají dvě jádra a 4 GB paměti, protože je zváženo procento využití.

Podobně závisí velikost disku na dvou vlastnostech posouzení: kritéria změny velikosti a typ úložiště. Pokud jsou kritéria změny velikosti založená na výkonu a typ úložiště je automatický, Azure Migrate vezme v úvahu hodnoty IOPS a propustnosti disku při identifikaci cílového typu disku (Standard nebo Premium).

Pokud jsou kritéria změny velikosti v závislosti na výkonu a typ úložiště je Premium, Azure Migrate doporučuje disk Premium. SKU disku úrovně Premium se vybere na základě velikosti místního disku. Stejná logika se používá k určení velikosti disku, pokud jsou kritéria změny velikosti místní a typ úložiště je Standard nebo Premium.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>Jaký dopad má historie výkonu a percentil využití v doporučeních velikosti?

Tyto vlastnosti se zohledňují pouze při určování velikosti na základě výkonu.

Azure Migrate shromažďuje historii výkonu místních počítačů a používá je k tomu, aby v Azure doporučila velikost virtuálního počítače a typ disku.

Zařízení nepřetržitě profiluje místní prostředí za účelem shromažďování dat o využití v reálném čase každých 20 sekund. Zařízení shrnuje ukázky 20 sekund a vytvoří jeden datový bod každých 15 minut. Pokud chcete vytvořit datový bod, zařízení vybere nejvyšší hodnotu ze všech ukázek s 20 sekundami. Zařízení odešle tento datový bod do Azure.

Když vytvoříte posouzení v Azure (na základě hodnoty doby trvání a percentilu historie výkonu), Azure Migrate vypočítá efektivní hodnotu využití a použije ji ke změně velikosti.

Předpokládejme například, že jste nastavili dobu trvání výkonu na jeden den a hodnotu percentilu na 95. percentil. Azure Migrate seřadí vzorové body na 15 minut odeslané kolektorem za poslední den ve vzestupném pořadí a jako efektivní využití vybere hodnotu 95. percentilu.

Hodnota 95. percentilu zajišťuje, že budete ignorovat nějaké odlehlé hodnoty. Pokud použijete 99 percentil, je možné zahrnout mimo jiné odlehlé hodnoty. Pokud chcete vybrat špičku pro období a nechcete vyznačit žádné z nich, vyberte 99 percentil.

### <a name="what-is-dependency-visualization"></a>Co je Vizualizace závislostí?

Vizualizace závislostí umožňuje vyhodnotit skupiny virtuálních počítačů pro migraci s větší jistotou. Křížově kontroluje závislosti počítačů před spuštěním posouzení. Vizualizace závislostí pomáhá zajistit, že nic není k disčase, abyste se vyhnuli neočekávanému výpadku při migraci do Azure. Azure Migrate používá řešení Service Map v protokolech Azure Monitor k povolení Vizualizace závislostí.

> [!NOTE]
> Vizualizace závislostí není v Azure Government k dispozici.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Musím pro používání Vizualizace závislostí platit?

Ne. Další informace najdete v tématu [Azure Migrate ceny](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Musím pro vizualizaci závislostí nainstalovat cokoli?

Pokud chcete používat vizualizaci závislostí, je potřeba stáhnout a nainstalovat agenty na každý místní počítač, který chcete vyhodnotit.

Na každý počítač je potřeba nainstalovat následující agenty:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Pokud máte počítače bez připojení k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.

Pokud nepoužíváte vizualizaci závislostí, nepotřebujete tyto agenty.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Můžu použít existující pracovní prostor pro vizualizaci závislostí?

Ano, existující pracovní prostor můžete připojit k projektu migrace a použít ho pro vizualizaci závislostí. Další informace najdete v článku Jak to funguje v článku [vizualizace závislosti](concepts-dependency-visualization.md#how-does-it-work) .

### <a name="can-i-export-the-dependency-visualization-report"></a>Můžu sestavu Vizualizace závislostí exportovat?

Ne, vizualizace závislosti se nedá exportovat. Vzhledem k tomu, že Azure Migrate používá Service Map pro vizualizaci závislostí, můžete použít [REST API Service map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) k získání závislostí ve formátu JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Jak mohu automatizovat instalaci Microsoft Monitoring Agent (MMA) a agenta závislostí?

Pomocí tohoto [skriptu nainstalujte agenta závislostí](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Postupujte podle těchto [pokynů a nainstalujte MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) pomocí příkazového řádku nebo automatizace. Pro MMA použijte [Tento skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Kromě skriptů můžete k nasazení agentů použít taky nástroje pro nasazení, jako je System Center Configuration Manager a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) .

### <a name="what-operating-systems-are-supported-by-mma"></a>Které operační systémy podporuje MMA?

- Prohlédněte si seznam [operačních systémů Windows podporovaných nástrojem MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Prohlédněte si seznam [operačních systémů Linux podporovaných nástrojem MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>Jaké operační systémy podporuje agent závislostí?

Zobrazí seznam [operačních systémů Windows a Linux, které Azure monitor pro virtuální počítače podporuje](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Je možné vizualizovat závislosti v Azure Migrate po dobu delší než hodinu?
Ne. Závislosti můžete vizualizovat až o hodinu. Můžete se vrátit k určitému datu v historii, pokud je to v měsíci zpátky, ale maximální doba trvání vizualizace je hodina. Například můžete použít dobu trvání na mapě závislostí k zobrazení závislostí včera, ale můžete je zobrazit pouze v rámci jednoho hodiny okna. Můžete ale použít protokoly Azure Monitor k dotazování na [data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) v delší době trvání.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>Můžu použít vizualizaci závislostí pro skupiny, které obsahují více než 10 virtuálních počítačů?
Můžete [vizualizovat závislosti pro skupiny](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , které obsahují až 10 virtuálních počítačů. Pokud máte skupinu s více než 10 virtuálními počítači, doporučujeme rozdělit skupinu do menších skupin a potom tyto závislosti vizualizovat.

## <a name="azure-migrate-server-migration"></a>Migrace serveru Azure Migrate

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Jaký je rozdíl mezi migrací Azure Migrate serverem a Azure Site Recovery?

Migrace Azure Migrate serveru používá modul replikace Site Recovery pro migraci virtuálních počítačů VMware založenou na agentech, migraci virtuálních počítačů Hyper-V a migraci fyzických serverů do Azure. Možnost bez agenta pro migraci virtuálních počítačů VMware je nativně integrovaná do migrace serveru.

## <a name="next-steps"></a>Další postup
Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).
