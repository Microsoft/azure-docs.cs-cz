---
title: Zálohování dat do Azure pomocí Veeam
titleSuffix: Azure Storage
description: Poskytuje přehled o faktorech, které je potřeba vzít v úvahu, a postup pro použití Azure jako cíle úložiště a umístění pro obnovení služby Veeam Backup and Recovery.
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 0b8bc0defd3314fcff691a049323201732644ff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589901"
---
# <a name="backup-to-azure-with-veeam"></a>Zálohování do Azure pomocí Veeam

Tento článek vám pomůže integrovat infrastrukturu Veeam s úložištěm objektů BLOB v Azure. Zahrnuje požadavky, požadavky, implementaci a provozní pokyny. Tento článek se zaměřuje na použití Azure jako cíle zálohování mimo lokalitu a na lokalitu pro obnovení, pokud dojde k havárii, což brání normálnímu provozu v primární lokalitě.

> [!NOTE]
> Veeam také nabízí nižší řešení pro plánované časy obnovení (RTO), replikaci Veeam. Toto řešení vám umožňuje vytvořit pohotovostní virtuální počítač, který vám pomůže rychleji se zotavit v případě havárie v produkčním prostředí Azure. Veeam má taky vyhrazené nástroje pro zálohování prostředků Azure a Office 365. Tyto možnosti jsou mimo rozsah tohoto dokumentu.

## <a name="reference-architecture"></a>Referenční architektura

Následující diagram poskytuje referenční architekturu pro místní nasazení do Azure a nasazení v Azure.

![Veeam diagram referenční architektury Azure.](../media/veeam-architecture.png)

Stávající nasazení Veeam můžete snadno integrovat s Azure přidáním účtu úložiště Azure nebo několika účtů jako cloudového úložiště záloh. Veeam také umožňuje obnovit zálohy z místního prostředí v rámci Azure a poskytnout vám tak web na vyžádání pro obnovení v Azure.

## <a name="veeam-interoperability-matrix"></a>Tabulka interoperability Veeam

| Úloha | GPv2 a Blob Storage | Podpora na studené úrovni | Podpora archivní úrovně | Podpora řady Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Místní virtuální počítače/data | v10a | v10a | – | 10a<sup>*</sup> |
| Virtuální počítače Azure | v10a | v10a | – | 10a<sup>*</sup> |
| Azure Blob | v10a | v10a | – | 10a<sup>*</sup> |
| Azure Files | v10a | v10a | – | 10a<sup>*</sup> |

<sup>*</sup>Veeam podporu zálohování a replikace REST API jenom pro Azure Data Box. Proto se Azure Data Box Disk nepodporuje.

## <a name="before-you-begin"></a>Než začnete

Malé plánování vám pomůže využít Azure jako cíl zálohování mimo lokalitu a web pro obnovení.

### <a name="get-started-with-azure"></a>Začínáme s Azure

Microsoft nabízí rámec, který vám umožní začít s Azure. Rozhraní CAF (Cloud [Framework](/azure/architecture/cloud-adoption/) ) představuje podrobný přístup k podnikové digitální transformaci a komplexní příručce pro plánování produkčního přijetí cloudu. CAF obsahuje podrobný [Průvodce nastavením Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) , který vám pomůže rychle a bezpečně začít pracovat. Interaktivní verzi můžete najít v [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Najdete ukázkové architektury, konkrétní osvědčené postupy pro nasazení aplikací a bezplatné školicí materiály, které vás zavedou na cestu ke odbornosti Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Vezměte v úvahu síť mezi vaší lokalitou a Azure

Ať už používáte cloudové prostředky ke spouštění produkčních, testovacích a vývojových prostředků, nebo jako cíl zálohování a lokalitu pro obnovení, je důležité pochopit, jaké jsou požadavky na šířku pásma počátečního zálohování a průběžné přenosy.

Azure Data Box poskytuje způsob, jak přenést počáteční směrný plán zálohování do Azure bez nutnosti větší šířky pásma. To je užitečné v případě, že je předpokládaný přenos standardních hodnot trvat déle, než můžete tolerovat. Přenos dat Estimator můžete použít při vytváření účtu úložiště k odhadu času potřebného k přenosu počáteční zálohy.

![Zobrazuje Azure Storage Estimator přenos dat na portálu.](../media/az-storage-transfer.png)

Nezapomeňte, že budete potřebovat dostatečnou kapacitu sítě pro podporu denních přenosů dat v rámci požadovaného okna přenosu (zálohovací okno), aniž by to mělo vliv na produkční aplikace. Tato část popisuje nástroje a techniky, které jsou k dispozici pro posouzení vašich síťových požadavků.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Určete, kolik šířky pásma budete potřebovat.

K dispozici je více možností hodnocení pro určení míry změny a celkové velikosti zálohovacího skladu pro počáteční základní přenos na Azure. Tady je několik příkladů nástrojů pro posuzování a vytváření sestav:

- [MiTrend](https://mitrend.com/)
- [Apt jsou](https://www.veritas.com/insights/aptare-it-analytics)
- [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Určení nevyužité šířky pásma internetu

Je důležité, abyste věděli, jak často nevyužité šířky pásma (nebo *rezervy*) máte k dispozici na každodenní bázi. To vám pomůže posoudit, jestli můžete splnit vaše cíle pro:

- počáteční čas odeslání, když nepoužíváte Azure Data Box pro offline dosazení
- dokončení denních záloh na základě výše uvedené frekvence změn a intervalu zálohování

Pomocí následujících metod Identifikujte rezervy šířky pásma, které mohou vaše zálohy do Azure využívat.

- Pokud jste zákazníkem Azure ExpressRoute, podívejte se na [použití okruhu](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) v Azure Portal.
- Kontaktujte poskytovatele internetových služeb. Měly by být schopné sdílet sestavy, které zobrazují vaše stávající denní a měsíční využití.
- Existuje několik nástrojů, které umožňují měřit využití monitorováním síťového provozu na úrovni směrovače nebo přepínače. Tady jsou některé z nich:

  - [Sada Solarwindsch analýz šířky pásma](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Asistent sítě Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Zvolit správné možnosti úložiště

Když použijete Azure jako cíl zálohování, budete používat službu [Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md). BLOB Storage je řešení úložiště objektů od Microsoftu. Úložiště objektů BLOB je optimalizované pro ukládání obrovských objemů nestrukturovaných dat, což jsou data, která nevyhovují žádnému datovému modelu ani definici. Azure Storage je navíc odolné, vysoce dostupné, zabezpečené a škálovatelné. Můžete vybrat správné úložiště pro vaše zatížení a zajistit tak [úroveň odolnosti](../../../../common/storage-redundancy.md) pro splnění interní SLA. BLOB Storage je služba pro platby za použití. Účtuje se vám [každý měsíc](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) za množství uložených dat, přístup k těmto datům a v případě studených a archivních vrstev, což je minimální požadovaná doba uchování. Možnosti odolnosti a vrstvení použitelné pro data záloh jsou shrnuté v následujících tabulkách.

**Možnosti odolnosti úložiště objektů BLOB:**

|  |Místně redundantní  |Zóna – redundantní  |Geograficky redundantní  |Geografické zóny – redundantní  |
|---------|---------|---------|---------|---------|
|**Efektivní počet kopií**     | 3         | 3         | 6         | 6 |
|**počet zón dostupnosti**     | 1         | 3         | 2         | 4 |
|**počet oblastí** s     | 1         | 1         | 2         | 2 |
|**Ruční převzetí služeb při selhání do sekundární oblasti**     | N/A         | N/A         | Ano         | Yes |

**Úrovně úložiště objektů BLOB:**

|  | Horká vrstva   |Studená vrstva   | Úroveň archivu |
| ----------- | ----------- | -----------  | -----------  |
| **Dostupnost** | 99,9 %         | 99 %         | Offline      |
| **Poplatky za využití** | Vyšší náklady na úložiště, nižší přístup a náklady na transakce | Snížení nákladů na úložiště, vyššího přístupu a transakčních nákladů | Nejnižší náklady na úložiště, nejvyšší přístup a náklady na transakce |
| **Vyžaduje se minimální doba uchovávání dat.** | NA | 30 dní | 180 dnů |
| **Latence (čas do prvního bajtu)** | Milisekund | Milisekund | Hodiny |

#### <a name="sample-backup-to-azure-cost-model"></a>Ukázka zálohování do modelu Azure cost model

S platbami za použití můžou být těžké zákazníkům, kteří jsou pro cloudu noví. Když platíte jenom za využitou kapacitu, platíte taky za transakce (čtení a zápisy) a [odchozí data pro čtení dat](https://azure.microsoft.com/pricing/details/bandwidth/) zpátky do místního prostředí, když se v rámci služby [Azure Express Route Direct nebo Express Route neomezený datový tarif](https://azure.microsoft.com/pricing/details/expressroute/) používá, kde jsou zahrnuté odchozí datové přenosy z Azure. Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete provádět analýzu "Co je potřeba". Můžete se založit na cenách na seznamu nebo na [Azure Storage rezervované ceny](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), což může přinést úspory až 38%. Tady je příklad cenové cvičení, které umožňuje modelovat měsíční náklady na zálohování do Azure. Toto je pouze příklad. *Ceny se mohou lišit v závislosti na aktivitách, které zde nejsou zaznamenány.*

|Nákladový faktor  |Měsíční náklady  |
|---------|---------|
|100 TB zálohovaných dat ve studeném úložišti     |$1556,48         |
|2 TB nových zapsaných dat za den × 30 dnů     |$72 v transakcích          |
|Měsíční odhadovaný součet     |$1628,48         |
|---------|---------|
|Jednorázové obnovení z 5 TB do místní sítě přes veřejný Internet   | $527,26         |

> [!Note]
> Tento odhad byl vygenerován v cenové Kalkulačkě Azure s využitím Východní USA cen s průběžnými platbami a vychází z Veeam výchozí velikosti bloků dat 256 KB pro přenosy v síti WAN. Tento příklad se nedá použít k vašim požadavkům.

## <a name="implementation-guidance"></a>Pokyny k implementaci

V této části najdete Stručný návod, jak přidat Azure Storage k místnímu nasazení Veeam. Podrobné pokyny a informace týkající se plánování najdete v [příručce k zálohování cloudového připojení Veeam](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Otevřete Azure Portal a vyhledejte **účty úložiště**. Můžete také kliknout na ikonu výchozí služby.

      ![Zobrazuje přidání účtů úložiště v Azure Portal.](../media/azure-portal.png)

      ![Zobrazuje, kde jste zadali úložiště do vyhledávacího pole Azure Portal.](../media/locate-storage-account.png)

2. Vyberte **vytvořit** a přidejte účet. Vyberte nebo vytvořte skupinu prostředků, zadejte jedinečný název, zvolte oblast, vyberte **standardní** výkon, vždy ponechte druh účtu jako **úložiště v2**, zvolte úroveň replikace, která odpovídá vašemu SLA, a výchozí úroveň, kterou bude váš zálohovací software používat. Účet Azure Storage zpřístupňuje horké, studené a archivní úrovně v rámci jednoho účtu a zásady Veeam vám umožní efektivně spravovat životní cyklus vašich dat pomocí několika vrstev.

    ![Zobrazuje nastavení účtu úložiště na portálu.](../media/account-create-1.png)

3. Nechejte teď výchozí možnosti sítě a přejděte k **ochraně dat**. Tady se můžete rozhodnout povolit obnovitelné odstranění, které vám umožní obnovit omylem odstraněný záložní soubor v rámci definované doby uchovávání a nabízí ochranu před náhodným nebo škodlivým odstraněním.

    ![Zobrazuje nastavení ochrany dat na portálu.](../media/account-create-2.png)

4. V dalším kroku doporučujeme výchozí nastavení z **Rozšířené** obrazovky pro případy použití zálohování do Azure.

    ![Zobrazí na portálu kartu Rozšířená nastavení.](../media/account-create-3.png)

5. Přidejte značky pro organizaci, pokud používáte označování, a vytvořte svůj účet.

6. K přidání účtu do prostředí Veeam se teď vyžadují dva rychlé kroky. Přejděte k účtu, který jste vytvořili v Azure Portal a vyberte **kontejnery** v nabídce **BLOB Service** . Přidejte kontejner a vyberte smysluplný název. Pak přejděte na položku **přístupové klíče** v části **Nastavení** a zkopírujte **název účtu úložiště** a jeden ze dvou přístupových klíčů. V dalších krocích budete potřebovat název kontejneru, název účtu a přístupový klíč.

    ![Ukazuje vytvoření kontejneru na portálu.](../media/container-b.png)

    ![Zobrazuje nastavení přístupového klíče na portálu.](../media/access-key.png)

    > [!Note]
    > Veeam Backup a replikace nabízí další možnosti pro připojení k Azure. Pro případ použití tohoto článku je doporučeným osvědčeným postupem použití Microsoft Azure Blob Storage jako cíle zálohování pomocí výše uvedené metody.

7. *(Volitelné)* Do nasazení můžete přidat další vrstvy zabezpečení.

     1. Nakonfigurujte přístup na základě role, abyste omezili, kdo může provádět změny v účtu úložiště. Další informace najdete v tématu [předdefinované role pro operace správy](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Omezte přístup k účtu na konkrétní segmenty sítě s [nastavením brány firewall úložiště](../../../../common/storage-network-security.md) , aby nedocházelo k pokusům o přístup z oblasti mimo vaši podnikovou síť.

        ![Zobrazuje nastavení brány firewall úložiště na portálu.](../media/storage-firewall.png)

     1. Nastavte [Zámek proti odstranění](../../../../../azure-resource-manager/management/lock-resources.md) na účtu, abyste zabránili nechtěnému odstranění účtu úložiště.

        ![Zámek prostředků](../media/resource-lock.png)

     1. Nakonfigurujte další [osvědčené postupy zabezpečení](../../../../../storage/blobs/security-recommendations.md).

8. V konzole pro správu zálohování a replikace Veaam přejděte na **infrastruktura zálohování** – > klikněte pravým tlačítkem v podokně Přehled a vyberte **Přidat úložiště zálohování** . otevře se Průvodce konfigurací. V dialogovém okně vyberte **úložiště objektů**  ->  **Microsoft Azure Blob Storage**  ->  **Azure Blob Storage**.

    ![V průvodci úložištěm Veeam se zobrazuje výběr úložiště objektů.](../media/veeam-repo-a.png)

    ![Zobrazuje výběr Microsoft Azure Blob Storage v průvodci úložištěm Veeam.](../media/veeam-repo-b.png)

    ![Zobrazuje výběr služby Azure Blob Storage v průvodci úložištěm Veeam.](../media/veeam-repo-c.png)

9. Dále zadejte název a popis nového úložiště BLOB Storage.

    ![Ukazuje zadání názvu úložiště v průvodci úložištěm Veeam.](../media/veeam-repo-d.png)

10. V dalším kroku přidejte přihlašovací údaje pro přístup k účtu služby Azure Storage. V cloudu správce přihlašovacích údajů vyberte **Microsoft Azure Storage účet** a zadejte název účtu úložiště a přístupový klíč. V selektoru oblastí vyberte **globální Azure** a v případě potřeby všechny servery brány.

    ![V průvodci úložištěm Veeam se dozvíte, jak zadat účet.](../media/veeam-repo-e.png)

    > [!Note]
    > Pokud se rozhodnete Nepoužívat server brány Veeam, ujistěte se, že všechny rozsahy úložiště se škálováním na více instancí mají přímý přístup k Internetu.

11. V registru kontejneru vyberte kontejner Azure Storage a vyberte nebo vytvořte složku, do které se mají ukládat zálohy. Můžete také definovat částečný limit pro celkovou kapacitu úložiště, která se má použít v Veeam, což se doporučuje. Projděte si zobrazené informace v části Souhrn a dokončete konfigurační nástroj. Nyní můžete vybrat nové úložiště v konfiguraci úlohy zálohování.

    ![V průvodci úložištěm Veeam se dozvíte, jak zadat kontejner.](../media/veeam-repo-f.png)

    ![V průvodci úložištěm Veeam se dozvíte, jak vytvořit složku.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Provozní pokyny

### <a name="azure-alerts-and-performance-monitoring"></a>Výstrahy a monitorování výkonu Azure

Doporučuje se monitorovat jak prostředky Azure, tak i možnost Veeam, abyste je mohli využít jako jakýkoli cíl úložiště, na který jste spoléhat na ukládání vašich záloh. Kombinace možností monitorování Azure Monitor a Veeam (karta **Statistika** na uzlu **úlohy** v konzole pro správu Veeam nebo pokročilejší možnosti, jako je například Veeam ONE), vám pomůže udržet prostředí v dobrém stavu.

#### <a name="azure-portal"></a>portál Azure

Azure poskytuje robustní řešení monitorování ve formě [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Můžete [nakonfigurovat Azure monitor](../../../../blobs/monitor-blob-storage.md) ke sledování Azure Storage kapacity, transakcí, dostupnosti, ověřování a dalších. Úplný odkaz na sledované metriky najdete [tady](../../../../blobs/monitor-blob-storage-reference.md). Několik užitečných metrik, které je třeba sledovat, je BlobCapacity – abyste se ujistili, že zachováte maximální [kapacitu účtu úložiště](../../../../common/scalability-targets-standard-account.md), příchozí a odchozí připojení – ke sledování množství dat zapsaných do a čtení z vašeho účtu úložiště Azure a SuccessE2ELatency – ke sledování doby zpětného odezvy pro požadavky na Azure Storage a MediaAgent.

Můžete také [vytvořit výstrahy protokolu](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) pro sledování stavu služby Azure Storage a [řídicí panel stavu Azure](https://status.azure.com/status) kdykoli zobrazit.

#### <a name="veeam-reporting"></a>Vytváření sestav Veeam

- [Nakonfigurovat Veeam jednu podřízenou zprávu](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeam výstrahy zálohování a replikace](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Postup otevření případů podpory

Pokud potřebujete pomáhat se zálohováním řešení Azure, měli byste otevřít případ s Veeam i s Azure. To pomáhá organizacím podpory spolupracovat v případě potřeby.

#### <a name="to-open-a-case-with-veeam"></a>Otevření případu pomocí Veeam

Na [webu zákaznická podpora Veeam](https://www.veeam.com/support.html)se přihlaste a otevřete případ.

Informace o možnostech podpory, které vám Veeam nabízí, najdete v tématu [zásady zákaznické podpory Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf).

Můžete zavolat také na otevřít případ: [Celosvětová čísla podpory](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Otevření případu pomocí Azure

V [Azure Portal](https://portal.azure.com) vyhledejte **podporu** na panelu hledání v horní části. Vyberte možnost **help + podpora**  ->  **nové žádosti o podporu**.

> [!NOTE]
> Když otevřete nějaký případ, budete potřebovat pomoc s Azure Storage nebo sítí Azure. Nezadávejte Azure Backup. Azure Backup je název služby Azure a váš případ bude směrován nesprávně.

### <a name="links-to-relevant-veeam-documentation"></a>Odkazy na relevantní dokumentaci k Veeam

Další podrobnosti najdete v následující dokumentaci k Veeam:

- [Uživatelská příručka k Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Průvodce architekturou Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Nabídky Marketplace

Můžete dál používat řešení Veeam, které znáte a důvěřovat, abyste chránili vaše úlohy běžící v Azure. Veeam usnadnila nasazení řešení v Azure a ochranu Azure Virtual Machines a mnoho dalších služeb Azure.

- [Nasazení služby Veeam Backup & pomocí Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Web Azure Backup and Recovery Veeam](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Další kroky

Informace o specializovaných scénářích použití najdete v následujících zdrojích na webu Veeam:

- [Veeam videa](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Veeam technické dokumentace](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam Knowledge Base a nejčastější dotazy](https://www.veeam.com/knowledge-base.html?ad=menu-resources)