---
title: Migrace dat z Amazonu S3 do Azure Storage
description: Pomocí Azure Data Factory migrujte data z Amazon S3 do Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 963a541835c5e45c5642f2d516da53fd165142b4
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91616920"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Migrace dat ze služby Amazon S3 do Azure Storage pomocí Azure Data Factory 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory poskytuje výkonný, robustní a nákladově efektivní mechanizmus migrace dat se škálováním z Amazon S3 do Azure Blob Storage nebo Azure Data Lake Storage Gen2.  Tento článek poskytuje následující informace pro inženýry dat a vývojáře: 

> [!div class="checklist"]
> * Výkon 
> * Kopírovat odolnost
> * Zabezpečení sítě
> * Architektura řešení vysoké úrovně 
> * Osvědčené postupy implementace  

## <a name="performance"></a>Výkon

ADF nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních, což vývojářům umožňuje vytvářet kanály pro plné využití šířky pásma sítě a vstupně-výstupních operací úložiště a šířky pásma pro maximalizaci propustnosti přesunu dat pro vaše prostředí. 

Zákazníci úspěšně migrovali petabajty dat sestávající ze stovek milionů souborů z Amazon S3 do Azure Blob Storage s trvalou propustností 2 GB/s a vyšší. 

![Diagram znázorňuje několik oddílů souborů v úložišti A W S S3 s přidruženými akcemi kopírování do Azure Blob Storage A D L S Gen2.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

Obrázek výše znázorňuje, jak můžete dosáhnout velkých rychlostí přesunu dat prostřednictvím různých úrovní paralelismu:
 
- Jediná aktivita kopírování může využít výhod škálovatelných výpočetních prostředků: při použití Azure Integration Runtime můžete pro každou aktivitu kopírování v rámci serveru zadat [až 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) . Při použití Integration Runtime v místním prostředí můžete ručně navýšení kapacity počítače nebo horizontální navýšení kapacity na více počítačů ([až 4 uzly](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) a jedna aktivita kopírování bude rozdělit svou sadu souborů napříč všemi uzly. 
- Jedna aktivita kopírování čte z a zapisuje do úložiště dat pomocí více vláken. 
- Tok řízení ADF může spustit více aktivit kopírování paralelně, například pomocí [pro každou smyčku](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Odolnost

V rámci jednoho spuštění aktivity kopírování má ADF vestavěný mechanismus opakování, aby mohl zpracovávat určitou úroveň přechodných chyb v úložištích dat nebo v podkladové síti. 

Při provádění binárního kopírování ze S3 do objektů BLOB a ze S3 do ADLS Gen2, ADF automaticky provede vytváření kontrolních bodů.  Pokud se spuštění aktivity kopírování nezdařilo nebo vypršel časový limit, při následném pokusu se kopírování obnoví z posledního bodu selhání místo od začátku. 

## <a name="network-security"></a>Zabezpečení sítě 

Ve výchozím nastavení ADF přenáší data z Amazon S3 do Azure Blob Storage nebo Azure Data Lake Storage Gen2 pomocí šifrovaného připojení přes protokol HTTPS.  Protokol HTTPS zajišťuje šifrování dat při přenosu a znemožňuje odposlouchávání a útoky prostředníkem. 

Případně, pokud nechcete, aby byla data přenášena prostřednictvím veřejného Internetu, můžete dosáhnout vyššího zabezpečení přenosem dat přes privátní partnerský vztah mezi AWS přímým připojením a trasou Azure Express.  Informace o tom, jak se dá dosáhnout, najdete v níže uvedené architektuře řešení. 

## <a name="solution-architecture"></a>Architektura řešení

Migrace dat prostřednictvím veřejného Internetu:

![Diagram znázorňuje migraci prostřednictvím Internetu H T T P z úložiště A W S S3 prostřednictvím Azure Integration Runtime v D F Azure pro Azure Storage. Modul runtime má řídicí kanál s Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- V této architektuře se data přenáší zabezpečeně pomocí protokolu HTTPS prostřednictvím veřejného Internetu. 
- Zdroj Amazon S3 i cílový Blob Storage Azure nebo Azure Data Lake Storage Gen2 jsou nakonfigurované tak, aby povolovaly provoz ze všech síťových IP adres.  Informace o tom, jak omezit přístup k síti na konkrétní rozsah IP adres, najdete v druhé níže uvedené architektuře. 
- Velikost síly je možné snadno škálovat bez serveru, abyste mohli plně využívat šířku pásma sítě a úložiště, abyste dosáhli nejlepší propustnosti pro vaše prostředí. 
- Pomocí této architektury se dá dosáhnout jak migrace počátečního snímku, tak migrace rozdílových dat. 

Migrovat data prostřednictvím privátního propojení: 

![Diagram znázorňuje migraci prostřednictvím privátního partnerského připojení z úložiště A W S S3 prostřednictvím místního prostředí Integration runtime na virtuální počítače Azure do koncových bodů služby V síti, které Azure Storage. Modul runtime má řídicí kanál s Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- V této architektuře se migrace dat provádí přes propojení privátního partnerského vztahu mezi AWS přímým připojením a trasou Azure Express, což znamená, že data nikdy neprochází přes veřejný Internet.  Vyžaduje použití AWS VPC a Azure Virtual Network. 
- Aby bylo možné dosáhnout této architektury, je nutné nainstalovat modul runtime integrace ADF (autohostd) na virtuální počítač s Windows v rámci služby Azure Virtual Network.  Ruční horizontální navýšení kapacity virtuálních počítačů IR nebo jejich horizontální navýšení kapacity na více virtuálních počítačů (až 4 uzly) můžete použít k plnému využití vaší sítě a IOPS/šířky pásma úložiště. 
- Pokud je přijatelné přenášet data přes protokol HTTPS, ale chcete uzamknout síťový přístup ke zdrojové S3 do konkrétního rozsahu IP adres, můžete tuto architekturu přijmout odebráním AWS VPC a nahrazením privátního propojení s protokolem HTTPS.  Na virtuálním počítači Azure budete chtít zachovat virtuální a místní prostředí IR Azure, abyste mohli mít statickou veřejně směrovatelný IP adresu pro účely filtrování. 
- Pomocí této architektury se dá dosáhnout jak migrace dat počátečního snímku, tak migrace rozdílových dat. 

## <a name="implementation-best-practices"></a>Osvědčené postupy implementace 

### <a name="authentication-and-credential-management"></a>Ověřování a Správa přihlašovacích údajů 

- K ověření v účtu Amazon S3 musíte použít [přístupová klávesu pro účet IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Pro připojení k Azure Blob Storage se podporuje víc typů ověřování.  Použití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) se důrazně doporučuje: postavené na automaticky spravované identifikaci ADF ve službě Azure AD umožňuje konfigurovat kanály bez zadání přihlašovacích údajů v definici propojené služby.  Případně můžete provést ověření v Azure Blob Storage pomocí [instančního objektu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [sdíleného přístupového podpisu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)nebo [klíče účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Pro připojení k Azure Data Lake Storage Gen2 se podporuje taky více typů ověřování.  Použití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) se důrazně doporučuje, i když je možné také použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) nebo [klíč účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) . 
- Pokud nepoužíváte spravované identity pro prostředky Azure, důrazně se doporučuje [ukládat přihlašovací údaje v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) , aby bylo snazší centrálně spravovat a střídat klíče bez nutnosti úprav propojených služeb ADF.  Toto je také jedním z [osvědčených postupů pro CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrace dat počátečního snímku 

Datový oddíl se doporučuje hlavně při migraci více než 100 TB dat.  Pokud chcete rozdělit data na oddíly, pomocí nastavení "prefix" vyfiltrujte složky a soubory v Amazon S3 podle názvu a potom jednotlivé úlohy kopírování ADF mohou kopírovat jeden oddíl současně.  Pro zajištění lepší propustnosti můžete souběžně spustit více úloh kopírování přes ADF. 

Pokud některá z úloh kopírování selže kvůli přechodnému problému se sítí nebo úložištěm dat, můžete znovu znovu načíst tento konkrétní oddíl z AWS S3.  Všechny ostatní úlohy kopírování načítající jiné oddíly nebudou mít vliv na. 

### <a name="delta-data-migration"></a>Migrace rozdílových dat 

Nejpohodlnější způsob, jak identifikovat nové nebo změněné soubory z AWS S3, je použití konvencí pojmenování na oddíly s časovou konvencí – když se data v AWS S3 zaregistrují s informacemi o časovém intervalu v názvu souboru nebo složky (například/yyyy/MM/DD/file.csv), váš kanál může snadno určit, které soubory/složky se mají kopírovat přírůstkově. 

Případně, pokud vaše data v AWS S3 nejsou rozdělená do oddílů, může ADF identifikovat nové nebo změněné soubory podle jejich LastModifiedDate.   To funguje tak, že ADF bude kontrolovat všechny soubory z AWS S3 a kopíruje jenom nový a aktualizovaný soubor, jehož časové razítko poslední změny je větší než určitá hodnota.  Uvědomte si, že pokud máte ve S3 velký počet souborů, může prvotní kontrola souborů trvat dlouhou dobu bez ohledu na to, kolik souborů odpovídá podmínce filtru.  V tomto případě navrhujete nejprve rozdělit data pomocí stejného nastavení ' prefix ' pro počáteční migraci snímku, aby bylo prohledávání souborů souběžně možné.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Scénáře, které vyžadují místní prostředí Integration runtime na virtuálním počítači Azure 

Bez ohledu na to, jestli migrujete data prostřednictvím privátního propojení nebo chcete v bráně firewall Amazon S3 povolen určitý rozsah IP adres, musíte na virtuální počítač Azure s Windows nainstalovat místní prostředí Integration runtime. 

- Doporučená konfigurace, která se má spustit pro každý virtuální počítač Azure, je Standard_D32s_v3 s 32 vCPU a 128-GB paměti.  Při migraci dat můžete sledovat využití procesoru a paměti pro virtuální počítač IR během migrace dat, abyste viděli, jestli potřebujete ještě víc škálovat virtuální počítač, aby se zlepšil výkon, nebo snížit kapacitu virtuálního počítače, aby se ušetřily náklady. 
- Horizontální navýšení kapacity můžete také škálovat tak, že přiřadíte až 4 uzly virtuálních počítačů s jedním místním prostředím IR.  Jedna úloha kopírování spuštěná v místním prostředí IR bude automaticky dělit oddíly sady souborů a využívat všechny uzly virtuálních počítačů ke kopírování souborů paralelně.  Pro zajištění vysoké dostupnosti se doporučuje začít se dvěma uzly virtuálních počítačů, aby při migraci dat nedocházelo k jednomu bodu selhání. 

### <a name="rate-limiting"></a>Omezování rychlosti 

Osvědčeným postupem je provést ověření výkonnosti pomocí reprezentativní vzorové datové sady, abyste mohli určit vhodnou velikost oddílu. 

Začněte s jedním oddílem a jednou aktivitou kopírování s výchozím nastavením DIÚ.  Postupně zvyšujte nastavení DIÚ, dokud nedosáhnete limitu šířky pásma sítě nebo limitu IOPS/šířky pásma úložišť dat nebo jste dosáhli maximálního 256 DIÚ povoleného pro jednu aktivitu kopírování. 

Dále postupně zvyšte počet souběžných aktivit kopírování, dokud nedosáhnete omezení vašeho prostředí. 

Když narazíte na chyby omezování hlášené aktivitou kopírování ADF, zmenšete nastavení Concurrency nebo DIÚ v ADF nebo zvažte zvýšení limitu šířky pásma a IOPS úložišť sítě a dat.  

### <a name="estimating-price"></a>Odhad ceny 

> [!NOTE]
> Toto je hypotetický příklad ceny.  Vaše skutečné ceny závisí na skutečné propustnosti ve vašem prostředí.

Vezměte v úvahu následující kanál vytvořený pro migraci dat ze S3 do Azure Blob Storage: 

![Diagram znázorňuje kanál pro migraci dat, s ručním tokem triggerem pro vyhledávání, Flowing do příkazu ForEach a přechodem k dílčímu kanálu pro každý oddíl, který obsahuje kopírování toků do uložené procedury. Mimo kanál je uložený postup uložený do Azure SQL D B, který přetéká na vyhledávání a toky s W S S3 ke kopírování, které přecházejí do úložiště objektů BLOB.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Můžeme předpokládat následující: 

- Celkový objem dat je 2 PB. 
- Migrace dat přes protokol HTTPS pomocí první architektury řešení 
- 2 PB je rozdělené do oddílů o 1 KB a každá kopie přesune jeden oddíl. 
- Každá kopie se nakonfiguruje s DIÚ = 256 a dosáhne propustnosti 1 GB/s. 
- Souběžnost ForEach je nastavená na 2 a agregovaná propustnost je 2 GB/s. 
- V celkovém případě dokončení migrace trvá 292 hodin. 

Tady je odhadovaná cena na základě výše uvedených předpokladů: 

![Snímek obrazovky tabulky zobrazuje odhadovanou cenu.](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Další odkazy 
- [Konektor služby Amazon Simple Storage Service Connector](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Konektor Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Konektor Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Průvodce laděním výkonu aktivity kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Vytváření a konfigurace Integration Runtime pro místní hostování](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Vysoce hostované prostředí Integration runtime – HA a škálovatelnost](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Aspekty zabezpečení přesunu dat](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Uložení přihlašovacích údajů v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopírovat soubor postupně na základě názvu souboru s oddíly](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopírování nových a změněných souborů na základě LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Stránka s cenami ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Šablona

Tady je [Šablona](solution-template-migration-s3-azure.md) , která se má začít používat k migraci petabajty dat složených ze stovek milionů souborů z Amazon S3 do Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
