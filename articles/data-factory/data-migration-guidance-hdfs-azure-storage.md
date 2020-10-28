---
title: Migrace dat z místního clusteru Hadoop do Azure Storage
description: Naučte se používat Azure Data Factory k migraci dat z místního clusteru Hadoop do Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 3e691244c4c03635eb87a7905eff6756da5c04f9
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638121"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>K migraci dat z místního clusteru Hadoop do Azure Storage použijte Azure Data Factory 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory poskytuje výkonný, robustní a nákladově efektivní mechanismus pro migraci dat ve velkém měřítku z místního HDFS do Azure Blob Storage nebo Azure Data Lake Storage Gen2. 

Data Factory nabízí dva základní přístupy k migraci dat z místního HDFS do Azure. Můžete vybrat přístup v závislosti na vašem scénáři. 

- **Data Factory režim DistCp** (doporučeno): v Data Factory můžete k kopírování souborů do Azure Blob Storage (včetně [dvoufázové kopie](./copy-activity-performance.md#staged-copy)) nebo Azure Data Lake Store Gen2 použít [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (distribuované kopírování). Pomocí Data Factory integrovaných s DistCp můžete využít stávající výkonný cluster, abyste dosáhli nejlepší propustnosti kopírování. Získáte také výhodu flexibilního plánování a sjednocení monitorovacího prostředí od Data Factory. V závislosti na konfiguraci Data Factory se aktivita kopírování automaticky vytvoří příkaz DistCp, odešle data do vašeho clusteru Hadoop a pak monitoruje stav kopírování. Pro migraci dat z místního clusteru Hadoop do Azure doporučujeme Data Factory režim DistCp.
- **Data Factory režim nativního prostředí Integration runtime** : DistCp není možnost ve všech scénářích. Například v prostředí Azure Virtual Networks nástroj DistCp nepodporuje privátní partnerské vztahy Azure ExpressRoute s koncovým bodem Azure Storage virtuální sítě. V některých případech navíc nebudete chtít použít stávající cluster Hadoop jako modul pro migraci dat, takže nebudete mít v clusteru velké zatížení, což může mít vliv na výkon stávajících úloh ETL. Místo toho můžete použít nativní schopnost prostředí Data Factory Integration runtime jako modul, který kopíruje data z místního HDFS do Azure.

Tento článek poskytuje následující informace o obou metodách:
> [!div class="checklist"]
> * Výkon 
> * Kopírovat odolnost
> * Zabezpečení sítě
> * Architektura řešení vysoké úrovně 
> * Osvědčené postupy implementace  

## <a name="performance"></a>Výkon

V režimu Data Factory DistCp je propustnost stejná, jako kdybyste používali nástroj DistCp nezávisle. Data Factory režim DistCp maximalizuje kapacitu stávajícího clusteru Hadoop. DistCp můžete použít pro velké souběžné kopírování mezi clustery nebo uvnitř clusteru. 

DistCp používá MapReduce k ovlivnění distribuce, zpracování a obnovení chyb a vytváření sestav. Rozbalí seznam souborů a adresářů do vstupu pro mapování úloh. Každý úkol zkopíruje oddíl souboru, který je zadaný v seznamu zdroj. Pomocí Data Factory integrovaných s DistCp můžete vytvářet kanály, které plně využívají šířku pásma sítě, IOPS úložiště a šířku pásma pro maximalizaci propustnosti přesunu dat pro vaše prostředí.  

Režim Data Factory Native Integration runtime také umožňuje paralelismus na různých úrovních. Paralelismus můžete využít k plnému využití šířky pásma sítě, IOPS úložiště a šířky pásma pro maximalizaci propustnosti přesunu dat:

- Jedna aktivita kopírování může využít výhod škálovatelných výpočetních prostředků. V místním prostředí Integration runtime můžete ručně škálovat počítač nebo škálovat na více počítačů ([až na čtyři uzly](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)). Jedna aktivita kopírování rozdělí svou sadu souborů napříč všemi uzly. 
- Jedna aktivita kopírování čte z a zapisuje do úložiště dat pomocí více vláken. 
- Tok řízení Data Factory může současně spustit více aktivit kopírování. Můžete například použít [pro každou smyčku](./control-flow-for-each-activity.md). 

Další informace najdete v tématu [Průvodce výkonem aktivity kopírování](./copy-activity-performance.md).

## <a name="resilience"></a>Odolnost

V režimu Data Factory DistCp můžete použít různé parametry příkazového řádku DistCp (například `-i` , ignorovat chyby nebo `-update` zapisovat data, když se zdrojový soubor a cílový soubor liší velikost) pro různé úrovně odolnosti.

V režimu Data Factory Native Integration runtime v rámci jedné aktivity kopírování má Data Factory vestavěný mechanismus opakování. Může zpracovávat určitou úroveň přechodných chyb v úložištích dat nebo v podkladové síti. 

Při provádění binárního kopírování z místního HDFS do úložiště objektů BLOB a z místního HDFS do Data Lake Store Gen2 Data Factory automaticky provádí kontrolní body ve velkém rozsahu. Pokud při následném opakování dojde k chybě nebo vypršení časového limitu aktivity kopírování (Ujistěte se, že počet opakování je > 1), kopie pokračuje od posledního bodu selhání a ne od začátku.

## <a name="network-security"></a>Zabezpečení sítě 

Ve výchozím nastavení Data Factory přenáší data z místního HDFS do úložiště objektů BLOB nebo do Azure Data Lake Storage Gen2 pomocí šifrovaného připojení přes protokol HTTPS. Protokol HTTPS zajišťuje šifrování dat při přenosu a znemožňuje odposlouchávání a útoky prostředníkem. 

Případně, pokud nechcete, aby byla data přenášena prostřednictvím veřejného Internetu, pro zajištění vyššího zabezpečení můžete přenést data prostřednictvím privátního partnerského propojení přes ExpressRoute. 

## <a name="solution-architecture"></a>Architektura řešení

Tento obrázek znázorňuje migraci dat prostřednictvím veřejného Internetu:

![Diagram znázorňující architekturu řešení pro migraci dat prostřednictvím veřejné sítě](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- V této architektuře se data přenáší zabezpečeně pomocí protokolu HTTPS prostřednictvím veřejného Internetu. 
- Ve veřejném síťovém prostředí doporučujeme použít Data Factory DistCp režim. Abyste dosáhli nejlepší propustnosti kopírování, můžete využít výkonný stávající cluster. Získáte také výhodu flexibilního plánování a Unified monitoring z Data Factory.
- Pro tuto architekturu je nutné nainstalovat modul runtime integrace Data Factory v místním prostředí do počítače s Windows za podnikovou bránou firewall a odeslat příkaz DistCp do clusteru Hadoop a monitorovat stav kopírování. Vzhledem k tomu, že počítač není modul, který přesouvá data (jenom pro účely kontroly), kapacita počítače nemá vliv na propustnost přesunu dat.
- Jsou podporovány existující parametry z příkazu DistCp.

Tento obrázek znázorňuje migraci dat prostřednictvím privátního propojení: 

![Diagram znázorňující architekturu řešení pro migraci dat přes soukromou síť](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- V této architektuře se data migrují přes privátní partnerský odkaz prostřednictvím Azure ExpressRoute. Data nikdy neprochází přes veřejný Internet.
- Nástroj DistCp nepodporuje privátní partnerské vztahy ExpressRoute s koncovým bodem Azure Storage virtuální sítě. K migraci dat doporučujeme použít nativní funkci Data Factory prostřednictvím prostředí Integration runtime.
- Pro tuto architekturu je nutné nainstalovat modul runtime integrace Data Factory v místním prostředí do virtuálního počítače s Windows ve vaší virtuální síti Azure. Můžete ručně škálovat virtuální počítač nebo škálovat na více virtuálních počítačů, abyste plně využili své síťové a úložné IOPS nebo šířku pásma.
- Doporučená konfigurace, která se má spustit pro každý virtuální počítač Azure (s nainstalovanou Data Factory v místním prostředí Integration Runtime), je Standard_D32s_v3 s 32 vCPU a 128 GB paměti. Během migrace dat můžete monitorovat využití procesoru a paměti virtuálního počítače, abyste viděli, jestli potřebujete škálovat virtuální počítač pro lepší výkon, nebo snížit kapacitu virtuálního počítače, abyste snížili náklady.
- Horizontální navýšení kapacity můžete také škálovat přidružením až čtyř uzlů virtuálních počítačů s jedním místním prostředím Integration runtime. Jedna úloha kopírování spuštěná v místním prostředí Integration runtime automaticky rozděluje oddíly sady souborů a využívá všechny uzly virtuálních počítačů ke kopírování souborů paralelně. Pro zajištění vysoké dostupnosti doporučujeme začít se dvěma uzly virtuálních počítačů, abyste se vyhnuli scénáři s jedním bodem selhání při migraci dat.
- Když použijete tuto architekturu, k dispozici je migrace dat snímků a migrace rozdílových dat.

## <a name="implementation-best-practices"></a>Osvědčené postupy implementace

Při implementaci migrace dat doporučujeme dodržovat tyto osvědčené postupy.

### <a name="authentication-and-credential-management"></a>Ověřování a Správa přihlašovacích údajů 

- K ověření pro HDFS můžete použít [buď Windows (Kerberos), nebo anonymní](./connector-hdfs.md#linked-service-properties). 
- Pro připojení k úložišti objektů BLOB v Azure se podporuje víc typů ověřování.  Důrazně doporučujeme používat [pro prostředky Azure spravované identity](./connector-azure-blob-storage.md#managed-identity). Spravované identity založené na automaticky spravované Data Factory identitě v Azure Active Directory (Azure AD) umožňují konfigurovat kanály bez zadání přihlašovacích údajů do definice propojené služby. Případně můžete provést ověření pro úložiště objektů BLOB pomocí [instančního objektu](./connector-azure-blob-storage.md#service-principal-authentication), [sdíleného přístupového podpisu](./connector-azure-blob-storage.md#shared-access-signature-authentication)nebo [klíče účtu úložiště](./connector-azure-blob-storage.md#account-key-authentication). 
- Pro připojení k Data Lake Storage Gen2 se podporuje taky více typů ověřování.  Důrazně doporučujeme používat [pro prostředky Azure spravované identity](./connector-azure-data-lake-storage.md#managed-identity), ale taky můžete použít [instanční objekt](./connector-azure-data-lake-storage.md#service-principal-authentication) nebo [klíč účtu úložiště](./connector-azure-data-lake-storage.md#account-key-authentication). 
- Pokud nepoužíváte spravované identity pro prostředky Azure, důrazně doporučujeme [ukládat přihlašovací údaje v Azure Key Vault](./store-credentials-in-key-vault.md) , aby bylo snazší centrálně spravovat a střídat klíče bez nutnosti úprav Data Factory propojených služeb. To je také [doporučený postup pro CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrace dat počátečního snímku 

V Data Factory režimu DistCp můžete vytvořit jednu aktivitu kopírování pro odeslání příkazu DistCp a použít jiné parametry pro řízení chování při prvotní migraci dat. 

V Data Factory režimu nativního prostředí Integration runtime doporučujeme použít oddíl dat, zejména při migraci více než 10 TB dat. Chcete-li rozdělit data na oddíly, použijte názvy složek na HDFS. Každá úloha kopírování Data Factory pak může kopírovat jeden oddíl složky po druhém. Pro lepší propustnost můžete spustit více úloh kopírování Data Factory současně.

Pokud některá z úloh kopírování selže kvůli přechodným problémům se sítí nebo úložištěm dat, můžete znovu spustit úlohu, která se nezdařila, a znovu načíst konkrétní oddíl ze HDFS. Jiné úlohy kopírování, které načítají jiné oddíly, to neovlivní.

### <a name="delta-data-migration"></a>Migrace rozdílových dat 

V režimu Data Factory DistCp můžete použít parametr příkazového řádku DistCp `-update` , zapsat data, když se zdrojový soubor a cílový soubor liší velikost, pro migraci rozdílových dat.

V režimu Data Factory Native Integration je nejvhodnější způsob, jak identifikovat nové nebo změněné soubory ze HDFS, použít konvenci pojmenování podle časových oddílů. Když jsou data v HDFS v čase rozdělená na oddíly s informacemi o časovém intervalu v názvu souboru nebo složky (například */yyyy/mm/dd/file.csv* ), váš kanál může snadno určit, které soubory a složky se mají kopírovat přírůstkově.

Případně, pokud vaše data v HDFS nebudou rozdělená na oddíly, Data Factory můžou identifikovat nové nebo změněné soubory pomocí jejich hodnoty **LastModifiedDate** . Data Factory kontroluje všechny soubory z HDFS a kopíruje jenom nové a aktualizované soubory, které mají poslední upravená časová razítka, která je větší než nastavená hodnota. 

Pokud máte v HDFS velký počet souborů, může prvotní skenování souborů trvat dlouhou dobu, bez ohledu na to, kolik souborů vyhovuje podmínkám filtru. V tomto scénáři doporučujeme nejprve rozdělit data na oddíly pomocí stejného oddílu, který jste použili při migraci počátečního snímku. Skenování souborů se pak může vyskytnout paralelně.

### <a name="estimate-price"></a>Odhad ceny 

Vezměte v úvahu následující kanál pro migraci dat ze HDFS do úložiště objektů BLOB v Azure: 

![Diagram znázorňující cenové kanály](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Pojďme předpokládat následující informace: 

- Celkový objem dat je 1 PB.
- Data migrujete pomocí Data Factory režimu nativního prostředí Integration runtime.
- 1 PB je rozdělené na oddíly 1 000 a každá kopie přesouvá jeden oddíl.
- Každá aktivita kopírování je nakonfigurována pomocí jednoho prostředí Integration runtime v místním prostředí, které je přidruženo ke čtyřem počítačům a dosahuje propustnosti 500 MB/s.
- Souběžnost ForEach je nastavená na **4** a agregovaná propustnost je 2 GB/s.
- V celkovém případě dokončení migrace trvá 146 hodin.

Tady je odhadovaná cena na základě našich předpokladů: 

![Tabulka, která zobrazuje cenové výpočty](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Toto je hypotetický příklad ceny. Vaše skutečné ceny závisí na skutečné propustnosti ve vašem prostředí.
> Není zahrnutá cena za virtuální počítač Azure s Windows (s nainstalovanou místním prostředím Integration Runtime).

### <a name="additional-references"></a>Další odkazy

- [Konektor HDFS](./connector-hdfs.md)
- [Konektor služby Azure Blob Storage](./connector-azure-blob-storage.md)
- [Konektor Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md)
- [Průvodce laděním výkonu aktivity kopírování](./copy-activity-performance.md)
- [Vytvoření a konfigurace místního prostředí Integration Runtime](./create-self-hosted-integration-runtime.md)
- [Vysoká dostupnost a škálovatelnost místního prostředí Integration runtime](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Aspekty zabezpečení přesunu dat](./data-movement-security-considerations.md)
- [Uložení přihlašovacích údajů v Azure Key Vault](./store-credentials-in-key-vault.md)
- [Postupné kopírování souboru na základě názvu souboru s časovým segmentem](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Kopírování nových a změněných souborů na základě LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Stránka s cenami Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)