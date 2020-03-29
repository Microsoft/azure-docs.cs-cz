---
title: Migrace dat z Amazonu S3 do Azure Storage
description: Azure Data Factory slouží k migraci dat z Amazonu S3 do Azure Storage.
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
ms.openlocfilehash: 6f2db91a35573bc2cbdd0df2cb1ac09914cc956b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122640"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Migrace dat z Amazonu S3 do Azure Storage pomocí Azure Data Factory 

Azure Data Factory poskytuje výkonný, robustní a nákladově efektivní mechanismus pro migraci dat ve velkém měřítku z Amazon S3 do Azure Blob Storage nebo Azure Data Lake Storage Gen2.  Tento článek obsahuje následující informace pro datové inženýry a vývojáře: 

> [!div class="checklist"]
> * Výkon 
> * Odolnost kopírování
> * Zabezpečení sítě
> * Architektura řešení na vysoké úrovni 
> * Osvědčené postupy při provádění  

## <a name="performance"></a>Výkon

ADF nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních, což umožňuje vývojářům vytvářet kanály pro plné využití šířky pásma sítě, stejně jako úložiště VOPS a šířku pásma pro maximalizaci propustnost pohybu dat pro vaše prostředí. 

Zákazníci úspěšně migrovali petabajty dat skládající se ze stovek milionů souborů z Amazons3 do úložiště objektů blob Azure s trvalou propustností 2 GB/s a vyšší. 

![výkon](media/data-migration-guidance-s3-to-azure-storage/performance.png)

Obrázek nahoře ukazuje, jak můžete dosáhnout velké rychlosti pohybu dat prostřednictvím různých úrovní paralelismu:
 
- Aktivita jedné kopie může využívat škálovatelné výpočetní prostředky: při použití prostředí Azure Integration Runtime můžete zadat [až 256 DIU](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) pro každou aktivitu kopírování bez serveru. při použití prostředí Integration Runtime s vlastním hostitelem můžete ručně vertikálně vertikálně navýšit kapacitu počítače nebo vertikálně navýšit kapacitu na více počítačů[(až 4 uzly)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)a jedna aktivita kopírování rozdělí jeho soubor nastavený na všechny uzly. 
- Jedna aktivita kopírování čte a zapisuje do úložiště dat pomocí více vláken. 
- Tok řízení ADF může spustit více aktivit kopírování paralelně, například pomocí [pro každou smyčku](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Odolnost

V rámci spuštění jedné aktivity kopírování má adf vestavěný mechanismus opakování, takže může zpracovat určitou úroveň přechodných selhání v úložištích dat nebo v základní síti. 

Při provádění binární kopírování z S3 do objektu Blob a z S3 na ADLS Gen2, ADF automaticky provádí vytváření kontrolních bodů.  Pokud se spuštění aktivity kopírování nezdařilo nebo časový rozsah, při následném opakování se kopie obnoví od posledního bodu selhání namísto začátku. 

## <a name="network-security"></a>Zabezpečení sítě 

Ve výchozím nastavení adf přenáší data z Amazon S3 do Azure Blob Storage nebo Azure Data Lake Storage Gen2 pomocí šifrovaného připojení přes protokol HTTPS.  Protokol HTTPS poskytuje šifrování dat při přenosu a zabraňuje odposlouchávání a útokům prostředníkem. 

Případně pokud nechcete, aby se data přenášela přes veřejný Internet, můžete dosáhnout vyššího zabezpečení přenosem dat přes privátní partnerské propojení mezi AWS Direct Connect a Azure Express Route.  Podívejte se na architekturu řešení níže o tom, jak toho lze dosáhnout. 

## <a name="solution-architecture"></a>Architektura řešení

Migrace dat přes veřejný Internet:

![řešení-architektura-veřejná síť](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- V této architektuře jsou data bezpečně přenášena pomocí protokolu HTTPS přes veřejný internet. 
- Zdroj Amazon S3, stejně jako cíl Azure Blob Storage nebo Azure Data Lake Storage Gen2 jsou nakonfigurované tak, aby povolovaly provoz ze všech síťových IP adres.  Informace o tom, jak omezit přístup k síti na určitý rozsah IP adres, naleznete v následující architektuře. 
- Můžete snadno škálovat množství koňských sil bez serveru, abyste plně využili šířku pásma sítě a úložiště, abyste mohli získat nejlepší propustnost pro vaše prostředí. 
- Pomocí této architektury lze dosáhnout počáteční migrace snímků a migrace delta dat. 

Migrace dat přes soukromý odkaz: 

![řešení-architektura-privátní síť](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- V této architektuře migrace dat se provádí přes privátní partnerský vztah propojení mezi AWS Direct Connect a Azure Express Route tak, aby data nikdy prochází přes veřejný Internet.  Vyžaduje použití AWS VPC a Virtuální sítě Azure. 
- K dosažení této architektury je potřeba nainstalovat runtime integrace s vlastním hostitelem ADF na virtuální ms Windows v rámci virtuální sítě Azure.  Můžete ručně vertikálně škálovat vlastní hostované infračervené virtuální počítače nebo škálovat na více virtuálních počítačích (až 4 uzly) a plně využívat síť a úložiště VOPS/šířka pásma. 
- Pokud je přijatelné přenášet data přes PROTOKOL HTTPS, ale chcete uzamknout přístup k síti ke zdroji S3 do určitého rozsahu IP, můžete přijmout variantu této architektury odebráním AWS VPC a nahrazením soukromého propojení protokolem HTTPS.  Budete chtít zachovat Azure Virtual a vlastní hostované infračervené ovládání na virtuálním počítači Azure, takže můžete mít statické veřejně směrovatelné IP pro účely whitelisting. 
- Pomocí této architektury lze dosáhnout počáteční migrace dat snímku a migrace delta dat. 

## <a name="implementation-best-practices"></a>Osvědčené postupy při provádění 

### <a name="authentication-and-credential-management"></a>Ověřování a správa pověření 

- Chcete-li ověřit účet Amazon S3, musíte použít [přístupový klíč pro účet IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Pro připojení k Azure Blob Storage je podporované několik typů ověřování.  Použití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) se doporučuje: postaveno na automaticky spravované identifikaci ADF ve službě Azure AD umožňuje konfigurovat kanály bez zadání přihlašovacích údajů v definici propojené služby.  Případně můžete ověřit azure blob storage pomocí [instančního objektu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [podpisu sdíleného přístupu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)nebo [klíče účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Pro připojení k Azure Data Lake Storage Gen2 je taky podporováno víc typů ověřování.  Je vysoce doporučeno používat [spravované identity pro prostředky Azure,](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) i když se dá použít taky [hlavní ho instančního](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) objektu nebo [klíč účtu úložiště.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 
- Pokud nepoužíváte spravované identity pro prostředky Azure, [ukládání přihlašovacích údajů v azure key vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) je vysoce doporučeno usnadnit centrální správu a otočení klíčů bez úpravy služby propojené SDF.  To je také jeden z [osvědčených postupů pro CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Počáteční migrace dat snímku 

Datový oddíl se doporučuje zejména při migraci více než 100 TB dat.  Chcete-li rozdělit data, využijte nastavení "prefix" k filtrování složek a souborů v AmazonS3 podle názvu a pak každá úloha kopírování ADF může kopírovat jeden oddíl najednou.  Pro lepší propustnost můžete spustit více úloh kopírování ADF současně. 

Pokud některá z úloh kopírování selže z důvodu sítě nebo úložiště dat přechodný problém, můžete znovu spustit neúspěšnou úlohu kopírování znovu načíst konkrétní oddíl z AWS S3.  Všechny ostatní úlohy kopírování načítání jiných oddílů nebudou ovlivněny. 

### <a name="delta-data-migration"></a>Migrace rozdílových dat 

Nejvýkonnější způsob, jak identifikovat nové nebo změněné soubory z AWS S3, je pomocí časově dělené konvence pojmenování - když jsou vaše data v AWS S3 časově rozdělena s informacemi o časovém úseku v názvu souboru nebo složky (například /yyyy/mm/dd/file.csv), pak váš kanál může snadno identifikovat soubory / složky, které chcete kopírovat postupně. 

Případně Pokud vaše data v AWS S3 není čas rozdělen, ADF můžete identifikovat nové nebo změněné soubory podle jejich LastModifiedDate.   Funguje to tak, že ADF prohledá všechny soubory z AWS S3 a zkopíruje pouze nový a aktualizovaný soubor, jehož poslední upravené časové razítko je větší než určitá hodnota.  Uvědomte si, že pokud máte velký počet souborů v S3, počáteční prohledávání souborů může trvat dlouhou dobu bez ohledu na to, kolik souborů odpovídá podmínce filtru.  V takovém případě se doporučuje nejprve rozdělit data pomocí stejného nastavení "prefix" pro počáteční migraci snímků, takže prohledávání souborů může probíhat paralelně.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Pro scénáře, které vyžadují runtime integrace s vlastním hostitelem na virtuálním počítači Azure 

Ať už migrujete data přes soukromé propojení nebo chcete povolit konkrétní rozsah IP adres na bráně firewall Amazon S3, musíte nainstalovat runtime integrace s vlastním hostitelem na azure Windows VM. 

- Doporučená konfigurace pro každý virtuální počítač Azure je Standard_D32s_v3 s 32 virtuálními procesory a 128 GB paměti.  Během migrace dat můžete sledovat využití procesoru a paměti infračerveného virtuálního počítače a zjistit, jestli potřebujete dále vertikálně navýšit kapacitu virtuálního počítače pro lepší výkon nebo zmenšit kapacitu virtuálního počítače, abyste ušetřili náklady. 
- Horizontální navýšení kapacity můžete také tak, že přisuzujete až 4 uzly virtuálních virtuálních společností pomocí jednoho infračerveného systému s vlastním hostitelem.  Úloha jedné kopie spuštěná proti samoobslužné infračervené společnosti automaticky rozdělí sadu souborů a využije všechny uzly virtuálních virtuálních společností ke kopírování souborů paralelně.  Pro vysokou dostupnost se doporučuje začít s 2 uzly virtuálních aplikací, aby se zabránilo jeden bod selhání během migrace dat. 

### <a name="rate-limiting"></a>Omezování rychlosti 

Jako osvědčený postup proveďte výkon POC s reprezentativní ukázkovou datovou sadou, abyste mohli určit vhodnou velikost oddílu. 

Začněte s jedním oddílem a aktivitou jedné kopie s výchozím nastavením DIU.  Postupně zvyšujte nastavení DIU, dokud nedosáhnete limitu šířky pásma sítě nebo limitu šířky pásma úložišť dat nebo dokud nedosáhnete maximálního množství 256 DIU povoleného pro jednu kopii. 

Dále postupně zvyšujte počet souběžných aktivit kopírování, dokud nedosáhnete omezení prostředí. 

Pokud narazíte na chyby omezení hlášené aktivitou kopírování adf, buď snížit souběžnost nebo nastavení DIU v ADF, nebo zvažte zvýšení šířky pásma/IOPS limity sítě a úložišť dat.  

### <a name="estimating-price"></a>Odhad ceny 

> [!NOTE]
> Toto je hypotetický příklad ceny.  Vaše skutečné ceny závisí na skutečné propustnosti ve vašem prostředí.

Zvažte následující kanál vytvořený pro migraci dat z S3 do úložiště objektů blob Azure: 

![cenový kanál](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Předpokládejme následující: 

- Celkový objem dat je 2 PB 
- Migrace dat přes protokol HTTPS pomocí první architektury řešení 
- 2 PB je rozděleno do 1 K oddílů a každá kopie se pohybuje o jeden oddíl 
- Každá kopie je konfigurována s DIU= 256 a dosahuje propustnost 1 BPs 
- ProKaždá souběžnost je nastavena na 2 a agregační propustnost je 2 BPS 
- Celkem trvá dokončení migrace 292 hodin. 

Zde je odhadovaná cena založená na výše uvedených předpokladech: 

![cenová tabulka](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Další odkazy 
- [Konektor amazonské služby Simple Storage Service](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Konektor úložiště objektů blob Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Konektor Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Kopírovat průvodce optimalizací výkonu aktivity](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Vytváření a konfigurace prostředí Runtime integrace s vlastním hostitelem](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Runtime HA integrace s vlastním hostitelem a škálovatelnost](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Aspekty zabezpečení přesunu dat](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Uložení přihlašovacích údajů v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopírování souboru postupně na základě názvu souboru s časovým rozdělením](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopírovat nové a změněné soubory na základě LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Cenová stránka ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Šablona

Zde je [šablona](solution-template-migration-s3-azure.md) pro migraci petabajtů dat skládajících se ze stovek milionů souborů z AmazonS3 do Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
