---
title: Zálohování dat do Azure pomocí Veeam
titleSuffix: Azure Blob Storage Docs
description: Webová stránka poskytuje přehled o faktorech, které je potřeba vzít v úvahu, a postup pro využití Azure jako cíle úložiště a umístění pro obnovení pro zálohování a obnovení Veeam.
keywords: Veeam, zálohování do cloudu, zálohování, zálohování do Azure, zotavení po havárii, Kontinuita podnikových aplikací
author: Karl Rautenstrauch
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: Storage
ms.subservice: Blob Storage
ms.openlocfilehash: 9f07703d23c3be6e842a54ba4bb0d46467ccb71d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745008"
---
# <a name="backup-to-azure-with-veeam"></a>Zálohování do Azure pomocí Veeam

Tento článek poskytuje průvodce integrací infrastruktury Veeam s využitím Azure Blob Storage. Zahrnuje požadavky, Azure Storage principy, implementaci a provozní pokyny. Tento článek se zaměřuje jenom na použití Azure jako cíle zálohování mimo lokalitu a lokalitu pro obnovení v případě havárie, což zabraňuje normálnímu provozu v primární lokalitě. Veeam také nabízí nižší řešení RTO, Veeam replikaci, jako prostředek k tomu, aby byl počítač v pohotovostním stavu připravený k restartování a v případě havárie a ochrany prostředků v produkčním prostředí Azure rychleji obnovil. Veeam také vyhradí nástroje pro zálohování prostředků Azure a Office 365. Tyto možnosti jsou pro tento dokument mimo rozsah. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Referenční architektura pro nasazení v místním prostředí do Azure a In-Azure

![Referenční architektura Veeam do Azure](../media/veeam-architecture.png)

Stávající nasazení Veeam můžete snadno integrovat s Azure přidáním účtu Azure Storage nebo několika účtů jako cloudového úložiště záloh. Veeam také umožňuje obnovit zálohy z místního prostředí v rámci Azure a poskytnout vám tak web na vyžádání pro obnovení v Azure.

## <a name="veeam-interoperability-matrix"></a>Tabulka interoperability Veeam
| Úloha | GPv2 a Blob Storage | Podpora na studené úrovni | Podpora archivní úrovně | Podpora řady Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Místní virtuální počítače/data | v10a | v10a | NA | 10a |
| Virtuální počítače Azure | v10a | v10a | NA | 10a |
| Azure Blob | v10a | v10a | NA | 10a |
| Azure Files | v10a | v10a | NA | 10a | 

> [!Note]
Zálohování a replikace Veeam podporují REST API jenom pro Azure Data Box, proto Azure Data Box Disk není podporovaná. V Veeam V11 se očekává podpora pro archivní vrstvu Azure Blob Storage.

## <a name="before-you-begin"></a>Než začnete

Trochu plánované plánování zajistí, abyste se připojili k mnoha zákazníkům s mnoha zákazníky, kteří používají Azure jako cíl zálohování mimo lokalitu a web pro obnovení.

### <a name="are-you-new-to-azure"></a>Jste v Azure novinkou?

Microsoft nabízí rámec, který vám umožní začít s Azure. [Rozhraní pro přijetí do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( CAF \) je detailním přístupem k podnikové digitální transformaci a komplexní příručce pro plánování produkčního přijetí cloudu. CAF obsahuje podrobný [Průvodce nastavením Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) pro tyto novinky v Azure, které vám pomůžou rychle a bezpečně pracovat a v [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)můžete najít interaktivní verzi. Najdete ukázkové architektury a specifické osvědčené postupy pro nasazení aplikací a bezplatné školicí materiály, které vás zavedou na cestu ke odbornosti Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Vezměte v úvahu síť mezi vaší lokalitou a Azure

Ať už využívání cloudových prostředků ke spouštění výroby, testování a vývoje nebo jako cílení na zálohování a lokalitu pro obnovení, je důležité pochopit, jaké jsou požadavky na šířku pásma počátečního zálohování a pro každodenní přenosy.

Azure Data Box poskytuje způsob přenosu počátečního směrného plánu zálohování do Azure bez nutnosti větší šířky pásma, pokud je předpokládaný přenos standardních hodnot trvat déle, než můžete tolerovat. Přenos dat Estimator můžete využít při vytváření účtu úložiště k odhadu času potřebného k přenosu počáteční zálohy.

![Azure Storage Přenos dat Estimator](../media/az-storage-transfer.png)

Nezapomeňte, že budete potřebovat dostatečnou kapacitu sítě pro podporu denních přenosů dat v rámci požadovaného okna přenosu (zálohovací okno), aniž by to mělo vliv na produkční aplikace. V této části najdete přehled dostupných nástrojů a postupů pro posouzení vašich síťových potřeb.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Jak můžete určit, jakou šířku pásma budete potřebovat?

K dispozici je více možností hodnocení pro určení míry změny a celkové velikosti zálohovacího skladu pro počáteční základní přenos na Azure. Tady je několik příkladů nástrojů pro posuzování a vytváření sestav, jako jsou:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>Jak zjistím, kolik rezervy mám aktuální připojení k Internetu?

Je důležité, abyste věděli, kolik rezerv nebo typicky nevyužité šířky pásma máte k dispozici na každodenní bázi. To vám umožní správně vyhodnotit, jestli můžete vyhovět vašim cílům při počátečním nastavování, když nepoužíváte Azure Data Box pro offline osazení, a k dokončení každodenních záloh na základě výše uvedené míry změn a intervalu zálohování. Níže jsou uvedené metody, které můžete použít k identifikaci rezervy šířky pásma, které můžou vaše zálohy do Azure využívat.

- Jste stávající zákazník Azure ExpressRoute? Zobrazení [využití okruhu](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) v Azure Portal.
- Můžete kontaktovat poskytovatele internetových služeb. Měly by mít sestavy, které se mají sdílet, a ilustrovat tak stávající každodenní a měsíční využití.
- Existuje několik nástrojů, které umožňují měřit využití monitorováním síťového provozu na úrovni směrovače nebo přepínače, včetně:
  - [Sada Solarwindsch analýz šířky pásma](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Asistent sítě Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>Výběr správných možností úložiště

Při použití Azure jako cíle zálohování využívají zákazníci [azure BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. Azure Blob Storage je řešení úložiště objektů od Microsoftu. Úložiště objektů BLOB je optimalizované pro ukládání obrovských objemů nestrukturovaných dat, což jsou data, která nevyhovují žádnému datovému modelu ani definici. Azure Storage je navíc odolné, vysoce dostupné, zabezpečené a škálovatelné. Platforma Microsoftu nabízí flexibilitu pro výběr správného úložiště pro správnou úlohu, aby bylo možné zajistit [úroveň odolnosti](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) pro splnění interního SLA. Blob Storage je služba pro platby za použití. Účtuje se vám [každý měsíc](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) za množství uložených dat, přístup k těmto datům a – v případě studených a archivních vrstev – minimální požadovaná doba uchování. Možnosti odolnosti a vrstvení použitelné pro data záloh jsou shrnuté v následujících tabulkách.

**Možnosti odolnosti při Blob Storage v Azure:**

|  |Místně redundantní  |Zóna redundantní  |Geograficky redundantní  |Geografická zóna redundantní  |
|---------|---------|---------|---------|---------|
|Efektivní počet kopií     | 3         | 3         | 6         | 6 |
|počet Zóny dostupnosti     | 1         | 3         | 2         | 4 |
|počet oblastí     | 1         | 1         | 2         | 2 |
|Ruční převzetí služeb při selhání do sekundární oblasti     | Není k dispozici         | Není k dispozici         | Ano         | Ano |

**Blob Storage úrovně Azure:**

|  | Horká vrstva   |Studená vrstva   | Úroveň archivu |
| ----------- | ----------- | -----------  | -----------  |
| Dostupnost | 99,9 %         | 99 %         | Offline      |
| Poplatky za používání | Vyšší náklady na úložiště, nižší přístup a náklady na transakce | Snížení nákladů na úložiště, vyššího přístupu a transakčních nákladů | Nejnižší náklady na úložiště, nejvyšší přístup a náklady na transakce |
| Vyžaduje se minimální doba uchovávání dat. | NA | 30 dní | 180 dnů |
| Latence (čas do prvního bajtu) | Milisekund | Milisekund | Hodiny |

#### <a name="sample-backup-to-azure-cost-model"></a>Ukázka zálohování do modelu Azure cost model

Koncept plateb za použití může být těžké zákazníkům, kteří jsou noví ve veřejném cloudu. Když platíte jenom za využitou kapacitu, platíte taky za transakce (čtení a zápisy) a [odchozí data pro čtení dat](https://azure.microsoft.com/pricing/details/bandwidth/) zpátky do místního prostředí, když se v rámci služby [Azure Express Route Direct nebo Express Route neomezený datový tarif](https://azure.microsoft.com/pricing/details/expressroute/) používá, kde jsou zahrnuté odchozí datové přenosy z Azure. V [cenové kalkulačkě Azure](https://azure.microsoft.com/pricing/calculator/)můžete k tomu, co je třeba provést analýzu na základě cen seznamu nebo [Azure Storage rezervovaných cen](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations), zajistit až 38% úspor. Tady je příklad cenové cvičení pro modelování měsíčních nákladů na zálohování do Azure. Jedná se jenom o příklad a ***ceny se můžou lišit v závislosti na aktivitách, které tady nejsou zachycené:***


|Nákladový faktor  |Měsíční náklady  |
|---------|---------|
|100 TB zálohovaných dat ve studeném úložišti     |$1556,48         |
|2 TB nových zapsaných dat za den × 30 dnů     |$72 v transakcích          |
|Měsíční odhadovaný součet     |$1628,48         |
|---------|---------|
|Jednorázové obnovení z 5 TB do místní sítě přes veřejný Internet   | $527,26         |

> [!Note]
Tento odhad byl vygenerován v cenové Kalkulačkě Azure s využitím Východní USA ceny za průběžné platby a vychází z Veeam výchozí velikosti 256kb bloku dat pro přenosy v síti WAN. Tento příklad se nedá použít k vašim požadavkům.

## <a name="implementation-and-operational-guidance"></a>Prováděcí a provozní pokyny

V této části najdete stručný průvodce přidáním Azure Storage k místnímu nasazení Veeam. Pokud vás zajímá podrobné pokyny a plánování, doporučujeme, abyste si prostudovali [příručku zálohování Cloud Connect v Veeam](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Otevřete Azure Portal a vyhledejte "účty úložiště" nebo klikněte na ikonu výchozí služby.

      ![Azure Portal](../media/azure-portal.png)

      ![Účty úložiště na webu Azure Portal](../media/locate-storage-account.png)

2. Zvolte možnost Přidat účet a vyberte nebo vytvořte skupinu prostředků, zadejte jedinečný název, zvolte oblast, vyberte "standardní" výkon, vždy ponechte druh účtu jako "úložiště v2", zvolte úroveň replikace, která odpovídá vašemu SLA, a výchozí úroveň, kterou bude váš zálohovací software využívat. Účet Azure Storage zpřístupňuje horké, studené a archivní úrovně v rámci jednoho účtu a zásady Veeam vám umožní využít více vrstev a efektivně tak spravovat životní cyklus vašich dat. Přejděte k dalšímu kroku. 
    
      ![Vytváří se účet úložiště.](../media/account-create-1.png)

3. Přihlaste se s výchozími možnostmi sítě a přejděte na data Protection. Tady se můžete rozhodnout, že povolíte "obnovitelné odstranění", které vám umožní obnovit omylem odstraněný záložní soubor v rámci definované doby uchovávání a nabízí ochranu proti náhodnému nebo škodlivému odstranění. 
    ![Vytvoření účtu úložiště – část 2](../media/account-create-2.png)

4. V dalším kroku doporučujeme výchozí nastavení z obrazovky Upřesnit pro případy použití zálohování do Azure.

    ![Vytvoření účtu úložiště – část 3](../media/account-create-3.png) 

5. Přidejte značky pro organizaci, pokud používáte označování označením a vytvořením svého účtu. Teď máte k dispozici petabajty úložiště na vyžádání.

6. K přidání účtu do prostředí Veeam se teď vyžadují dva rychlé kroky. Přejděte na účet, který jste vytvořili na webu Azure Portal, a v okně portálu vyberte kontejnery v nabídce služba BLOB Service. Přidejte nový kontejner a vyberte smysluplný název. Potom v části Nastavení přejděte na položku přístupové klíče a zkopírujte název účtu úložiště a jeden ze dvou přístupových klíčů. V dalších krocích budete potřebovat název kontejneru, název účtu a přístupový klíč.

    ![Vytvoření kontejneru](../media/container-b.png)
    
    ![Převezměte informace o účtu.](../media/access-key.png)
    
    > [!Note]
Veeam Backup a replikace nabízí další možnosti pro připojení k Azure. Pro případ použití tohoto článku, který používá Microsoft Azure Blob Storage jako cíl zálohování, je doporučeným osvědčeným postupem použití výše uvedeného postupu.

7. ***(Volitelné)*** Do nasazení můžete přidat další vrstvy zabezpečení.

     1. Nakonfigurujte přístup na základě rolí, abyste omezili, kdo může provádět změny v účtu úložiště. [Další informace](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. Omezte přístup k účtu na konkrétní segmenty sítě pomocí [brány firewall úložiště](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) , aby nedocházelo k pokusům o přístup z oblasti mimo podnikovou síť.

    ![Brána firewall úložiště](../media/storage-firewall.png) 

    1. Nastavte [Zámek proti odstranění](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) na účtu, abyste zabránili nechtěnému odstranění účtu úložiště.

    ![Zámek prostředků ](../media/resource-lock.png) 1.) Nakonfigurujte další [osvědčené postupy zabezpečení](https://docs.microsoft.com/azure/storage/blobs/security-recommendations). 
8. V konzole pro správu zálohování a replikace Veaam přejděte do části infrastruktura zálohování – > klikněte pravým tlačítkem v podokně Přehled a výběrem možnosti Přidat úložiště záloh otevřete Průvodce konfigurací. V dialogovém okně vyberte úložiště objektů--> Microsoft Azure Blob Storage--> Azure Blob Storage.
    
    ![Obrazovka průvodce úložištěm Veeam a](../media/veeam-repo-a.png)

    ![Obrazovka průvodce úložištěm Veeam b](../media/veeam-repo-b.png)

    ![Průvodce úložištěm Veeam – obrazovka c](../media/veeam-repo-c.png)

9. Potom zadejte název a popis nového úložiště objektů BLOB Microsoft Azure.
    
    ![Obrazovka d Průvodce úložištěm Veeam](../media/veeam-repo-d.png)

10. V dalším kroku přidejte přihlašovací údaje pro přístup k účtu Azure Storage. V cloudu správce přihlašovacích údajů vyberte Microsoft Azure Storage účet, zadejte název účtu úložiště a přístupový klíč. V selektoru oblastí a v případě potřeby vyberte globální Azure.
    
    ![Obrazovka průvodce úložištěm Veeam-e](../media/veeam-repo-e.png)

> [!Note]
Pokud se rozhodnete Nepoužívat server brány Veeam, ujistěte se, že všechny rozsahy úložiště se škálováním na více instancí mají přímý přístup k Internetu.

11. V registru kontejneru vyberte kontejner Azure Storage a vyberte nebo vytvořte složku, do které se mají ukládat zálohy. Můžete také definovat částečný limit pro celkovou kapacitu úložiště, která se má použít v Veeam (doporučeno). Projděte si zobrazené informace v části Souhrn a dokončete konfigurační nástroj. Nové úložiště je pak možné vybrat v konfiguraci úlohy zálohování.

    ![Obrazovka průvodce úložištěm Veeam f](../media/veeam-repo-f.png)
    
    ![Obrazovka průvodce úložištěm Veeam g](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Monitorování výstrah a výkonu Azure

Doporučuje se monitorovat jak prostředky Azure, tak i možnost Veeam, abyste je mohli využít jako jakýkoli cíl úložiště, na který jste spoléhat na ukládání vašich záloh. Kombinace možností monitorování Azure Monitor a Veeam (to znamená, že karta Statistika v uzlu úlohy v konzole pro správu Veeam nebo pokročilejší možnosti, jako je Veeam ONE zpravodaj), vám pomůže udržet prostředí v dobrém stavu.

#### <a name="microsoft-azure-portal"></a>Microsoft Azure Portal

Microsoft Azure poskytuje robustní řešení monitorování ve formě [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Můžete [nakonfigurovat Azure monitor](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) ke sledování Azure Storage kapacity, transakcí, dostupnosti, ověřování a dalších. Úplný odkaz na sledované metriky najdete [tady](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Několik užitečných metrik, které je třeba sledovat, je BlobCapacity – abyste se ujistili, že zachováte maximální [kapacitu účtu úložiště](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), příchozí přenos dat a výstup – ke sledování množství dat zapsaných do a čtení z vašeho účtu Azure Storage a SuccessE2ELatency – ke sledování doby zpětného odezvy požadavků na a z Azure Storage a MediaAgent. 

Můžete také [vytvořit výstrahy protokolu](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) , které sledují stav služby Azure Storage, a kdykoli si budete moct zobrazit [řídicí panel stavu Azure](https://status.azure.com/status) .

#### <a name="veeam-reporting"></a>Vytváření sestav Veeam

[Konfigurace Veeam jednoho vytváření sestav](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Veeam výstrahy zálohování a replikace](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>Postup otevření případů podpory

Pokud potřebujete pomoc s řešením zálohování do Azure, doporučujeme otevřít případ s Veeam i Azure, aby naše organizace podpory mohly v případě potřeby spolupracovat.

#### <a name="how-to-open-a-case-with-veeam"></a>Jak otevřít případ pomocí Veeam

Přejděte na [portál zákaznické podpory Veeam](https://www.veeam.com/support.html), přihlaste se a otevřete případ.

Pokud potřebujete porozumět možnostem podpory dostupným na základě Veeam, přečtěte si téma [zásady zákaznické podpory Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf) .

Můžete také zavolat do aplikace a otevřít případ:

[Celosvětová čísla podpory](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Jak otevřít případ pomocí týmu podpory Azure

V [Azure Portal](https://portal.azure.com) vyhledejte "podpora" na panelu hledání v horní části portálu a vyberte + Nová žádost o podporu. 
> [!Note]
Při otevírání případu se prosím ujistěte, že potřebujete pomoc s "Azure Storage" nebo "sítě Azure", a **ne** "Azure Backup". Azure Backup je Microsoft Azure nativní služba a váš případ bude směrován nesprávně.

### <a name="links-to-relevant-veeam-documentation"></a>Odkazy na relevantní dokumentaci k Veeam

Dokumentace k Veeam poskytující další podrobnosti:

[Uživatelská příručka k Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Průvodce architekturou Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>Odkaz na nabídku Marketplace

Můžete také dál používat řešení Veeam, které znáte a důvěřujete, abyste chránili vaše úlohy běžící v Azure. Veeam usnadnila nasazení řešení v Azure a ochranu Azure Virtual Machines a mnoho dalších služeb Azure.

[Nasazení Veeam B&R prostřednictvím Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Datový list Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>Další kroky

Prozkoumejte další zdroje na těchto externích webech, abyste získali informace o specializovaných scénářích použití:

[Veeam videa](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Veeam technické dokumentace](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Veeam Knowledge Base a nejčastější dotazy](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
