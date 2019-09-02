---
title: K migraci dat z místního clusteru Hadoop do Azure Storage použijte Azure Data Factory | Microsoft Docs
description: K migraci dat z místního clusteru Hadoop do Azure Storage použijte Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211608"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>K migraci dat z místního clusteru Hadoop do Azure Storage použijte Azure Data Factory 

Azure Data Factory poskytuje výkonný, robustní a nákladově efektivní mechanismus pro migraci dat ve velkém měřítku z místního HDFS do Azure Blob Storage nebo Azure Data Lake Storage Gen2. V podstatě Azure Data Factory obsahuje dva přístupy k migraci dat z místního HDFS do Azure. Každý z nich můžete vybrat na základě vašeho scénáře. 

- Režim DistCp ADF. Podpora ADF pomocí [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) ke kopírování souborů do objektu blob Azure (včetně [dvoufázové kopie](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) nebo Azure Data Lake Store, v takovém případě může plně využít výkon vašeho clusteru místo spuštění v místním prostředí Integration runtime (IR) automatického hostování. Poskytne lepší propustnost při kopírování, zejména v případě, že je cluster velmi výkonný. V závislosti na vaší konfiguraci v Azure Data Factory se aktivita kopírování automaticky vytvoří příkaz DistCp, odešle do vašeho clusteru Hadoop a monitoruje stav kopírování. Pomocí ADF integrovaných s DistCp zákazník nemůže využít stávající výkonný cluster jenom k dosažení nejlepší propustnosti kopírování, ale taky získá výhodu flexibilního plánování a sjednocení monitorovacího prostředí z ADF. Ve výchozím nastavení je režim ADF DistCp doporučeným způsobem, jak migrovat data z místního clusteru Hadoop do Azure.
- Nativní infračervený režim ADF. V některých případech DistCp nemůže fungovat pro vaše případy (například v prostředí virtuální sítě, nástroj DistCp nepodporuje privátní partnerský vztah Express-Route + Azure Storage). Kromě toho už nechcete, aby váš stávající cluster Hadoop používal jako modul pro migraci dat, protože bude mít velký objem zátěže v clusteru, což může mít vliv na výkon stávajících úloh ETL. V takovém případě můžete použít nativní funkci ADF, kde je možné modul runtime Integration runtime (IR) stroj zkopírovat data z Prem HDFS do Azure.

Tento článek poskytuje následující informace o obou přístupech k datovým technikům a vývojářům:
> [!div class="checklist"]
> * Výkon 
> * Kopírovat odolnost
> * Zabezpečení sítě
> * Architektura řešení vysoké úrovně 
> * Osvědčené postupy implementace  

## <a name="performance"></a>Výkon

V režimu ADF DistCp je propustnost shodná s tím, že se nezávisle používá nástroj DistCp, což využívá kapacitu stávajícího clusteru Hadoop. DistCp (distribuované kopírování) je nástroj, který slouží k rozsáhlému kopírování mezi clustery. Používá MapReduce k ovlivnění distribuce, zpracování a obnovení chyb a vytváření sestav. Rozbalí seznam souborů a adresářů do vstupu na úlohy mapování, z nichž každý bude kopírovat oddíl souborů zadaných v seznamu zdroj. Pomocí ADF integrovaných s DistCp můžete vytvářet kanály, které plně využívají šířku pásma sítě a také vstupně-výstupní operace úložiště a šířku pásma pro maximalizaci propustnosti přesunu dat pro vaše prostředí.  

V nativním režimu infračerveného přenosu dat umožňuje také paralelismus na různých úrovních, což může plně využít šířku pásma sítě a také vstupně-výstupní operace úložiště a šířku pásma, aby se maximalizovala propustnost přesunu dat, a to ručním škálováním počítače v místním prostředí a jeho horizontálním škálováním. místní prostředí IR více počítačů.

- Jedna aktivita kopírování může využít výhod škálovatelných výpočetních prostředků. V místním prostředí Integration runtime můžete ručně škálovat počítač nebo škálovat na více počítačů ([až 4 uzly](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) a jedna aktivita kopírování bude rozdělit svou sadu souborů napříč všemi uzly. 
- Jedna aktivita kopírování čte z a zapisuje do úložiště dat pomocí více vláken. 
- Tok řízení ADF může spustit více aktivit kopírování paralelně, například pomocí [pro každou smyčku](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Další podrobnosti můžete získat z [Průvodce výkonem aktivity kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) .

## <a name="resilience"></a>Odolnost

V režimu ADF DistCp můžete využít různé parametry příkazového řádku DistCp (například-i, ignorovat selhání;-aktualizovat, zapsat data, když se zdrojový soubor a cílový soubor liší velikost) a dosáhnout tak různých úrovní odolnosti.

V nativním režimu IR v nativním režimu je v rámci jednoho spuštění aktivity kopírování ADF s vestavěným mechanismem opakování, aby mohl zpracovávat určitou úroveň přechodných chyb v úložištích dat nebo v základní síti. Při provádění binárního kopírování z místního HDFS do objektu BLOB a z místního HDFS do ADLS Gen2 provádí ADF automaticky kontrolu nad velkými rozsahy. Pokud při následném pokusu o spuštění aktivity kopírování došlo k chybě nebo vypršel časový limit, při následném opakování (je třeba opakovat počet > 1), kopie pokračuje od posledního bodu selhání místo od začátku.

## <a name="network-security"></a>Zabezpečení sítě 

Ve výchozím nastavení ADF přenáší data z místního HDFS do Azure Blob Storage nebo Azure Data Lake Storage Gen2 pomocí šifrovaného připojení přes protokol HTTPS.  Protokol HTTPS zajišťuje šifrování dat při přenosu a znemožňuje odposlouchávání a útoky prostředníkem. 

Případně, pokud nechcete, aby se data přenesla prostřednictvím veřejného Internetu, můžete dosáhnout vyššího zabezpečení prostřednictvím přenosu dat přes privátní partnerský vztah přes Azure Express Route. Informace o tom, jak se dá dosáhnout, najdete v níže uvedené architektuře řešení.

## <a name="solution-architecture"></a>Architektury řešení

Migrace dat prostřednictvím veřejného Internetu:

![řešení – architektura – veřejná síť](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- V této architektuře se data přenáší zabezpečeně pomocí protokolu HTTPS prostřednictvím veřejného Internetu. 
- Režim DistCp ADF se doporučuje používat ve veřejném síťovém prostředí. Díky tomu můžete nejen využít stávající výkonný cluster, abyste dosáhli nejlepší propustnosti kopírování, ale taky získáte výhodu flexibilního plánování a sjednocení monitorování z ADF.
- V počítači s Windows za podnikovou bránou firewall pro odeslání příkazu DistCp do clusteru Hadoop a sledování stavu kopírování musíte nainstalovat modul runtime integrace ADF (Autohost). Vzhledem k tomu, že tento počítač nebude modulem přesunovat data (pouze pro účely kontroly), kapacita počítače nemá vliv na propustnost přesunu dat.
- Stávající parametry z příkazu DistCp jsou podporovány.


Migrovat data prostřednictvím privátního propojení: 

![řešení – architektura – privátní síť](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- V této architektuře se migrace dat provádí přes privátní partnerský vztah přes Azure Express Route tak, aby se data nikdy neprováděla přes veřejný Internet.
- Nástroj DistCp nepodporuje privátní partnerský vztah a Azure Storage VNet pro Express Route, takže při migraci dat doporučujeme použít nativní funkci ADF prostřednictvím prostředí Integration runtime.
- Aby bylo možné dosáhnout této architektury, je nutné nainstalovat modul runtime integrace ADF (autohostd) na virtuální počítač s Windows v rámci služby Azure Virtual Network. Můžete ručně škálovat virtuální počítač nebo škálovat na více virtuálních počítačů, aby se plně využila vaše síť a IOPS úložiště/šířka pásma.
- Doporučená konfigurace, která se má začít používat pro každý virtuální počítač Azure (s nainstalovanou integrací prostředí autohosta ADF), je Standard_D32s_v3 s 32 vCPU a 128-GB paměti. Při migraci dat můžete sledovat využití CPU a paměti virtuálního počítače, abyste viděli, jestli potřebujete ještě víc škálovat virtuální počítač, aby se zajistil vyšší výkon, nebo snížit kapacitu virtuálního počítače za účelem úspory nákladů.
- Horizontální navýšení kapacity můžete také škálovat tak, že přiřadíte až 4 uzly virtuálních počítačů s jedním místním prostředím IR. Jedna úloha kopírování spuštěná v místním prostředí IR bude automaticky dělit oddíly sady souborů a využívat všechny uzly virtuálních počítačů ke kopírování souborů paralelně. Pro zajištění vysoké dostupnosti se doporučuje začít se dvěma uzly virtuálních počítačů, aby při migraci dat nedocházelo k jednomu bodu selhání.
- Pomocí této architektury se dá dosáhnout jak migrace dat počátečního snímku, tak migrace rozdílových dat.


## <a name="implementation-best-practices"></a>Osvědčené postupy implementace 

### <a name="authentication-and-credential-management"></a>Ověřování a Správa přihlašovacích údajů 

- K ověření pro HDFS můžete použít [buď Windows (Kerberos), nebo anonymní](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Pro připojení k Azure Blob Storage se podporuje víc typů ověřování.  Použití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) se důrazně doporučuje: postavené na automaticky spravované identifikaci ADF ve službě Azure AD umožňuje konfigurovat kanály bez zadání přihlašovacích údajů v definici propojené služby.  Případně můžete provést ověření v Azure Blob Storage pomocí [instančního objektu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [sdíleného přístupového podpisu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)nebo [klíče účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Pro připojení k Azure Data Lake Storage Gen2 se podporuje taky více typů ověřování.  Použití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) se důrazně doporučuje, i když je možné také použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) nebo [klíč účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) . 
- Pokud nepoužíváte spravované identity pro prostředky Azure, důrazně se doporučuje [ukládat přihlašovací údaje v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) , aby bylo snazší centrálně spravovat a střídat klíče bez nutnosti úprav propojených služeb ADF.  Toto je také jedním z [osvědčených postupů pro CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrace dat počátečního snímku 

V režimu ADF DistCp můžete vytvořit jednu aktivitu kopírování k odeslání příkazu DistCp s různými parametry pro řízení počátečního chování migrace dat. 

V nativním režimu infračerveného přenosu ADF se datový oddíl doporučuje hlavně při migraci více než 10 TB dat. Pokud chcete rozdělit data na oddíly, využijte názvy složek na HDFS a potom jednotlivé úlohy kopírování ADF mohou kopírovat vždy jeden oddíl složky. Pro zajištění lepší propustnosti můžete souběžně spustit více úloh kopírování přes ADF.
Pokud některá z úloh kopírování selže kvůli přechodnému problému se sítí nebo úložiště dat, můžete znovu znovu načíst tento konkrétní oddíl ze HDFS. Všechny ostatní úlohy kopírování načítající jiné oddíly nebudou mít vliv na.

### <a name="delta-data-migration"></a>Migrace rozdílových dat 

V režimu ADF DistCp můžete využít parametry příkazového řádku DistCp "-Update, zapsat data, když se zdrojový soubor a cílový soubor liší velikost", abyste mohli dosáhnout rozdílové migrace dat.

V nativním režimu infračerveného přenosu do ADF je nejpohodlnější způsob, jak identifikovat nové nebo změněné soubory ze HDFS, použít konvence pojmenování podle časových oddílů – když jsou data v HDFS v čase rozdělena s informacemi o časovém řezu v názvu souboru nebo složky (například/yyyy/MM/DD/ soubor. csv), kanál může snadno určit, které soubory nebo složky se mají kopírovat přírůstkově.
Případně, pokud vaše data v HDFS nejsou rozdělená do oddílů, může ADF identifikovat nové nebo změněné soubory podle jejich LastModifiedDate. To funguje tak, že ADF bude kontrolovat všechny soubory z HDFS a kopírovat pouze nové a aktualizované soubory, jejichž časové razítko poslední změny je větší než určitá hodnota. Uvědomte si, že pokud máte v HDFS velký počet souborů, může prvotní kontrola souborů trvat dlouhou dobu bez ohledu na to, kolik souborů odpovídá podmínce filtru. V takovém případě navrhujete nejprve rozdělit data pomocí stejného oddílu, aby při prvotní migraci snímků bylo skenování souborů možné.

### <a name="estimating-price"></a>Odhad ceny 

Vezměte v úvahu následující kanál vytvořený pro migraci dat ze HDFS do Azure Blob Storage: 

![ceny – kanál](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Můžeme předpokládat následující: 

- Celkový objem dat je 1 PB.
- Migrace dat pomocí druhé architektury řešení (režim ADF Native IR)
- 1 PB se dělí na oddíly 1000 a každá kopie přesune jeden oddíl.
- Každá aktivita kopírování je nakonfigurovaná s jedním místním prostředím IR přidruženým k 4 počítačům a dosáhne propustnosti 500 MB/s.
- Souběžnost ForEach je nastavená na 4 a agregovaná propustnost je 2 GB/s.
- V celkovém případě dokončení migrace trvá 146 hodin.


Tady je odhadovaná cena na základě výše uvedených předpokladů: 

![ceny – tabulka](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Toto je hypotetický příklad ceny.  Vaše skutečné ceny závisí na skutečné propustnosti ve vašem prostředí.
> Cena za virtuální počítač Azure s Windows (s nainstalovanou místní integrací Runtime) není zahrnutá.

### <a name="additional-references"></a>Další odkazy 
- [Konektor HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Konektor Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Konektor Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Průvodce laděním výkonu aktivity kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Vytváření a konfigurace Integration Runtime pro místní hostování](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Vysoce hostované prostředí Integration runtime – HA a škálovatelnost](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Požadavky na zabezpečení přesunu dat](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Ukládat přihlašovací údaje v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopírovat soubor postupně na základě názvu souboru s oddíly](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopírování nových a změněných souborů na základě LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Stránka s cenami ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Další postup

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)