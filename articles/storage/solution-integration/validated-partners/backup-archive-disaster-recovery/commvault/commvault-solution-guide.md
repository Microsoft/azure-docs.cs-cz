---
title: Zálohování dat do Azure pomocí CommVault
titleSuffix: Azure Storage
description: Poskytuje přehled o faktorech, které je potřeba vzít v úvahu, a postup pro použití Azure jako cíle úložiště a umístění pro obnovení pro CommVault kompletního zálohování a obnovení.
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: fa60b6f002e49babc1e1f014bcb941e7953a43a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484774"
---
# <a name="backup-to-azure-with-commvault"></a>Zálohování do Azure pomocí CommVault

Tento článek vám pomůže integrovat infrastrukturu CommVault s úložištěm objektů BLOB v Azure. Zahrnuje požadavky, požadavky, implementaci a provozní pokyny. Tento článek se zaměřuje na použití Azure jako cíle zálohování mimo lokalitu a na lokalitu pro obnovení, pokud dojde k havárii, což brání normálnímu provozu v primární lokalitě.

> [!NOTE]
> CommVault nabízí řešení pro kratší dobu obnovení (RTO), CommVault živou synchronizaci. Toto řešení vám umožňuje vytvořit pohotovostní virtuální počítač, který vám pomůže rychleji se zotavit v případě havárie v produkčním prostředí Azure. Tyto možnosti jsou mimo rozsah tohoto dokumentu.

## <a name="reference-architecture"></a>Referenční architektura

Následující diagram poskytuje referenční architekturu pro místní nasazení do Azure a nasazení v Azure.

![Referenční architektura CommVault do Azure](../media/commvault-diagram.png)

Stávající nasazení CommVault můžete snadno integrovat s Azure přidáním účtu úložiště Azure nebo několika účtů jako cíle cloudového úložiště. CommVault také umožňuje obnovit zálohy z místního prostředí v rámci Azure a poskytnout vám tak web na vyžádání pro obnovení v Azure.

## <a name="commvault-interoperability-matrix"></a>Tabulka interoperability CommVault

| Úloha | GPv2 a BLOB Storage | Podpora na studené úrovni | Podpora archivní úrovně | Podpora řady Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Místní virtuální počítače/data | v 11,5 | v 11,5 | v 11.10 | v 11.10 |
| Virtuální počítače Azure | v 11,5 | v 11,5 | v 11,5 | – |
| Azure Blob | v 11.6 | v 11.6 | v 11.6 | – |
| Azure Files | v 11.6 | v 11.6 | v 11.6 | – |

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

K určení, kolik šířky pásma budete potřebovat, použijte následující zdroje:

- Sestavy ze zálohovacího softwaru.
- CommVault poskytuje standardní sestavy pro určení [četnosti změn](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) a [celkové velikosti zálohovacího skladu](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) pro počáteční základní přenosovou hodnotu do Azure.
- Zálohování a nástroje pro vytváření sestav nezávislé na softwaru:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
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
|**počet oblastí**     | 1         | 1         | 2         | 2 |
|**Ruční převzetí služeb při selhání do sekundární oblasti**     | N/A         | N/A         | Ano         | Yes |

**Úrovně úložiště objektů BLOB:**

|  | Horká vrstva   |Studená vrstva   | Úroveň archivu |
| ----------- | ----------- | -----------  | -----------  |
| **Dostupnost** | 99,9 %         | 99 %         | Offline      |
| **Poplatky za využití** | Vyšší náklady na úložiště, nižší přístup a náklady na transakce | Snížení nákladů na úložiště, vyššího přístupu a transakčních nákladů | Nejnižší náklady na úložiště, nejvyšší přístup a náklady na transakce |
| **Vyžaduje se minimální doba uchovávání dat.**| – | 30 dní | 180 dnů |
| **Latence (čas do prvního bajtu)** | Milisekund | Milisekund | Hodiny |

#### <a name="sample-backup-to-azure-cost-model"></a>Ukázka zálohování do modelu Azure cost model

S platbami za použití můžou být těžké zákazníkům, kteří jsou pro cloudu noví. Když platíte jenom za využitou kapacitu, platíte taky za transakce (čtení a zápisy) a [odchozí data pro čtení dat](https://azure.microsoft.com/pricing/details/bandwidth/) zpátky do místního prostředí, když se v rámci služby [Azure Express Route Direct nebo Express Route neomezený datový tarif](https://azure.microsoft.com/pricing/details/expressroute/) používá, kde jsou zahrnuté odchozí datové přenosy z Azure. Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete provádět analýzu "Co je potřeba". Můžete se založit na cenách na seznamu nebo na [Azure Storage rezervované ceny](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), což může přinést úspory až 38%. Tady je příklad cenové cvičení, které umožňuje modelovat měsíční náklady na zálohování do Azure. Toto je pouze příklad. *Ceny se mohou lišit v závislosti na aktivitách, které zde nejsou zaznamenány.*

|Nákladový faktor  |Měsíční náklady  |
|---------|---------|
|100 TB zálohovaných dat ve studeném úložišti     |$1556,48         |
|2 TB nových zapsaných dat za den × 30 dnů     |$39 v transakcích          |
|Měsíční odhadovaný součet     |$1595,48         |
|---------|---------|
|Jednorázové obnovení z 5 TB do místní sítě přes veřejný Internet   | $491,26         |

> [!NOTE]
> Tento odhad byl vygenerován v cenové Kalkulačkě Azure s využitím Východní USA cen s průběžnými platbami a vychází z CommVault výchozí velikosti 32 MB, který generuje 65 536 žádosti o vložení (transakcí zápisu) za den. Tento příklad se nedá použít k vašim požadavkům.

## <a name="implementation-guidance"></a>Pokyny k implementaci

V této části najdete Stručný návod, jak přidat Azure Storage k místnímu nasazení CommVault. Podrobné pokyny a informace týkající se plánování najdete v tématu [Průvodce architekturou veřejného cloudu CommVault pro Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Otevřete Azure Portal a vyhledejte **účty úložiště**. Můžete také kliknout na ikonu výchozích **účtů úložiště** .

    ![Zobrazuje přidání účtů úložiště v Azure Portal.](../media/azure-portal.png)
  
    ![Zobrazuje, kde jste zadali úložiště do vyhledávacího pole Azure Portal.](../media/locate-storage-account.png)

2. Vyberte **vytvořit** a přidejte účet. Vyberte nebo vytvořte skupinu prostředků, zadejte jedinečný název, zvolte oblast, vyberte **standardní** výkon, vždy ponechte druh účtu jako **úložiště v2**, zvolte úroveň replikace, která odpovídá vašemu SLA, a výchozí úroveň, kterou bude váš zálohovací software používat. Účet Azure Storage zpřístupňuje horké, studené a archivní úrovně v rámci jednoho účtu a zásady CommVault vám umožní efektivně spravovat životní cyklus vašich dat pomocí několika vrstev.

    ![Zobrazuje nastavení účtu úložiště na portálu.](../media/account-create-1.png)

3. Nechejte teď výchozí možnosti sítě a přejděte k **ochraně dat**. Tady se můžete rozhodnout povolit obnovitelné odstranění, které vám umožní obnovit omylem odstraněný záložní soubor v rámci definované doby uchovávání a nabízí ochranu před náhodným nebo škodlivým odstraněním.

    ![Zobrazuje nastavení ochrany dat na portálu.](../media/account-create-2.png)

4. V dalším kroku doporučujeme výchozí nastavení z **Rozšířené** obrazovky pro případy použití zálohování do Azure.

    ![Zobrazí na portálu kartu Rozšířená nastavení.](../media/account-create-3.png)

5. Přidejte značky pro organizaci, pokud používáte označování, a vytvořte svůj účet.

6. K přidání účtu do prostředí CommVault se teď vyžadují dva rychlé kroky. Přejděte k účtu, který jste vytvořili v Azure Portal a vyberte **kontejnery** v nabídce **BLOB Service** . Přidejte kontejner a vyberte smysluplný název. Pak přejděte na položku **přístupové klíče** v části **Nastavení** a zkopírujte **název účtu úložiště** a jeden ze dvou přístupových klíčů. V dalších krocích budete potřebovat název kontejneru, název účtu a přístupový klíč.

    ![Ukazuje vytvoření kontejneru na portálu.](../media/container.png)

    ![Zobrazuje nastavení přístupového klíče na portálu.](../media/access-key.png)

7. (*Volitelné*) Do nasazení můžete přidat další vrstvy zabezpečení.

    1. Nakonfigurujte přístup na základě role, abyste omezili, kdo může provádět změny v účtu úložiště. Další informace najdete v tématu [předdefinované role pro operace správy](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Omezte přístup k účtu na konkrétní segmenty sítě s [nastavením brány firewall úložiště](../../../../common/storage-network-security.md) , aby nedocházelo k pokusům o přístup z oblasti mimo vaši podnikovou síť.

        ![Zobrazuje nastavení brány firewall úložiště na portálu.](../media/storage-firewall.png)

    1. Nastavte [Zámek proti odstranění](../../../../../azure-resource-manager/management/lock-resources.md) na účtu, abyste zabránili nechtěnému odstranění účtu úložiště.

        ![Zobrazuje nastavení zámku pro odstranění na portálu.](../media/resource-lock.png)

    1. Nakonfigurujte další [osvědčené postupy zabezpečení](../../../../../storage/blobs/security-recommendations.md).

1. V CommVault příkazového centra přejděte na **Správa**  ->  **zabezpečení**  ->  **pověření správce**. Zvolte **účet cloudu**, **typ dodavatele** **Microsoft Azure Storage**, vyberte **MediaAgent**, který bude přenášet data do a z Azure, přidejte název účtu úložiště a přístupový klíč.

    ![Zobrazuje Přidání přihlašovacích údajů v CommVault příkazového centra.](../media/commvault-credential.png)

9. Pak přejděte do   ->  **cloudu** úložiště v CommVault Command Center. Vyberte, chcete-li **Přidat**. Zadejte popisný název účtu úložiště a potom v seznamu **typ** vyberte **Microsoft Azure Storage** . Vyberte server agenta médií, který se použije k přenosu záloh do Azure Storage. Přidejte kontejner, který jste vytvořili, zvolte vrstvu úložiště, kterou chcete použít v účtu úložiště Azure, a vyberte pověření vytvořená v kroku #8. Nakonec vyberte, jestli chcete přenášet zálohy s odstraněním duplicit, nebo ne, a umístění databáze odstranění duplicitních dat.

     ![Snímek obrazovky s uživatelským rozhraním pro přidání cloudu CommVault V rozevírací nabídce archivace je vybrána možnost * * archiv * *.](../media/commvault-add-storage.png)

10. Nakonec přidejte nový prostředek Azure Storage do existujícího nebo nového plánu v CommVault příkazového centra pomocí **možnosti spravovat**  ->  **plány** jako cíl zálohování.

    ![Snímek obrazovky s uživatelským rozhraním centra příkazů CommVault V levém navigačním panelu je vybrána možnost * * spravovat * *, * * plány * *.](../media/commvault-plan.png)

11. *(Volitelné)* Pokud máte v úmyslu použít Azure jako lokalitu pro obnovení nebo CommVault k migraci serverů a aplikací do Azure, je osvědčeným postupem nasazení podřízeného serveru proxy v Azure. Podrobné pokyny najdete [tady](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).

## <a name="operational-guidance"></a>Provozní pokyny

### <a name="azure-alerts-and-performance-monitoring"></a>Výstrahy a monitorování výkonu Azure

Doporučuje se monitorovat jak prostředky Azure, tak i možnost CommVault, abyste je mohli využít jako jakýkoli cíl úložiště, na který jste spoléhat na ukládání vašich záloh. Kombinace Azure Monitor a sledování příkazového centra CommVault vám pomůže udržet si prostředí v dobrém stavu.

#### <a name="azure-portal"></a>portál Azure

Azure poskytuje robustní řešení monitorování ve formě [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Můžete [nakonfigurovat Azure monitor](../../../../blobs/monitor-blob-storage.md) ke sledování Azure Storage kapacity, transakcí, dostupnosti, ověřování a dalších. Můžete najít úplný odkaz na metriky, které jsou [zde](../../../../blobs/monitor-blob-storage-reference.md)shromažďovány. Několik užitečných metrik, které je třeba sledovat, je BlobCapacity – abyste se ujistili, že zůstanete pod maximálním [limitem kapacity účtu úložiště](../../../../common/scalability-targets-standard-account.md), příchozí a odchozí přenos dat, ke sledování množství dat zapsaných do a čtení z vašeho účtu Azure Storage a SuccessE2ELatency – ke sledování času zpětného odezvy pro požadavky na a z Azure Storage a na MediaAgent.

Můžete také [vytvořit výstrahy protokolu](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) pro sledování stavu služby Azure Storage a [řídicí panel stavu Azure](https://status.azure.com/status) kdykoli zobrazit.

#### <a name="commvault-command-center"></a>CommVault příkazového centra

- [Vytvoření výstrahy pro fondy cloudových úložišť](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Informace o tom, kde můžete zobrazit sestavy výkonu, dokončit úlohy a zahájit základní řešení potíží, najdete v tématu [řídicí panely](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Postup otevření případů podpory

Pokud potřebujete pomáhat se zálohováním řešení Azure, měli byste otevřít případ s CommVault i s Azure. To pomáhá organizacím podpory spolupracovat v případě potřeby.

#### <a name="to-open-a-case-with-commvault"></a>Otevření případu pomocí CommVault

Na [webu podpory CommVault](https://www.commvault.com/support)se přihlaste a otevřete případ.

Informace o možnostech pro kontrakty podpory, které jsou k dispozici, najdete v tématu [CommVault support Options](https://ma.commvault.com/support) .

Můžete se také zavolat, abyste otevřeli případ nebo mohli kontaktovat podporu CommVault prostřednictvím e-mailu:

- Bezplatná linka: + 1 877-780-3077
- [Celosvětová čísla podpory](https://ma.commvault.com/Support/TelephoneSupport)
- [Podpora e-mailu CommVault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Otevření případu pomocí Azure

V [Azure Portal](https://portal.azure.com) vyhledejte **podporu** na panelu hledání v horní části. Vyberte možnost **help + podpora**  ->  **nové žádosti o podporu**.

> [!NOTE]
> Když otevřete nějaký případ, budete potřebovat pomoc s Azure Storage nebo sítí Azure. Nezadávejte Azure Backup. Azure Backup je název služby Azure a váš případ bude směrován nesprávně.

### <a name="links-to-relevant-commvault-documentation"></a>Odkazy na relevantní dokumentaci k CommVault

Další podrobnosti najdete v následující dokumentaci k CommVault:

- [Uživatelská příručka k CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Průvodce architekturou Azure CommVault](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>Nabídky Marketplace

CommVault usnadnila nasazení řešení v Azure za účelem ochrany Azure Virtual Machines a mnoha dalších služeb Azure. Další informace najdete v následujících referenčních materiálech:

- [Nasazení CommVault prostřednictvím Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [CommVault pro datový list Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Seznam podporovaných funkcí a služeb Azure v CommVault](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Jak používat CommVault k ochraně SAP HANA v Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Další kroky

Další informace o specializovaných scénářích použití najdete v těchto dalších materiálech CommVault.

- [Migrace serverů a aplikací do Azure pomocí CommVault](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Ochrana SAP v Azure pomocí CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Ochrana Office 365 pomocí CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)