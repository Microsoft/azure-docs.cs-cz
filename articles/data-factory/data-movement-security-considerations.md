---
title: Důležité informace o zabezpečení
description: Popisuje základní infrastrukturu zabezpečení, kterou služby pro přesun dat v Azure Data Factory používají k zabezpečení vašich dat.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: bee627ade4f66206cd5254fc32bc7aa9973c7bee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131316"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Důležité informace o zabezpečení pro přesun dat v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
>
> * [Verze 1](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuální verze](data-movement-security-considerations.md)

Tento článek popisuje základní infrastrukturu zabezpečení, kterou služby pro přesun dat v Azure Data Factory používají k zabezpečení vašich dat. Prostředky správy Data Factory jsou postavené na infrastruktuře zabezpečení Azure a využívají všechna možná bezpečnostní opatření nabízená Azure.

V řešení Data Factory vytváříte jeden nebo více datových [kanálů](concepts-pipelines-activities.md). Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Tyto kanály jsou umístěny v oblasti, kde byla vytvořena datová továrna. 

I když je data factory k dispozici jenom v několika oblastech, služba přesunu dat je [k dispozici globálně,](concepts-integration-runtime.md#integration-runtime-location) aby byla zajištěna kompatibilita dat, efektivita a snížené náklady na odchozí přenos dat. 

Azure Data Factory neukládá žádná data s výjimkou pověření propojené služby pro úložiště cloudových dat, které jsou šifrované pomocí certifikátů. Pomocí data factory vytváříte pracovní postupy řízené daty pro orchestraci pohybu dat mezi [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats)a zpracováním dat pomocí [výpočetních služeb](compute-linked-services.md) v jiných oblastech nebo v místním prostředí. Pracovní postupy můžete také monitorovat a spravovat pomocí sad SDK a Azure Monitor.

Data Factory byla certifikována pro:

| **[Certifikace ČSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Pokud vás zajímá dodržování předpisů Azure a způsob, jakým Azure zabezpečuje vlastní infrastrukturu, navštivte [Centrum zabezpečení Microsoft Uvěř .](https://microsoft.com/en-us/trustcenter/default.aspx) Nejnovější seznam všech nabídek dodržování předpisů https://aka.ms/AzureComplianceAzure zkontrolujte - .

V tomto článku kontrolujeme aspekty zabezpečení v následujících dvou scénářích přesunu dat: 

- **Scénář cloudu**: V tomto scénáři zdroj i cíl jsou veřejně přístupné prostřednictvím internetu. Patří mezi ně služby spravovaného cloudového úložiště, jako je Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, služby SaaS, jako je Salesforce, a webové protokoly, jako jsou FTP a OData. Úplný seznam podporovaných zdrojů dat najdete v [podporovaných úložištích a formátech dat](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hybridní scénář**: V tomto scénáři je zdroj nebo cíl za bránou firewall nebo uvnitř místní podnikové sítě. Nebo úložiště dat je v privátní nebo virtuální síti (nejčastěji zdroj) a není veřejně přístupné. Databázové servery hostované na virtuálních počítačích také spadají do tohoto scénáře.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloudové scénáře

### <a name="securing-data-store-credentials"></a>Zabezpečení přihlašovacích údajů úložiště dat

- **Uklápěte šifrovaná pověření ve spravovaném úložišti Azure Data Factory**. Data Factory pomáhá chránit vaše přihlašovací údaje úložiště dat šifrováním pomocí certifikátů spravovaných společností Microsoft. Tyto certifikáty se střídají každé dva roky (což zahrnuje obnovení certifikátu a migraci přihlašovacích údajů). Další informace o zabezpečení Azure Storage najdete v tématu [Přehled zabezpečení Azure Storage](../security/fundamentals/storage-overview.md).
- **Uložte přihlašovací údaje v trezoru klíčů Azure**. Přihlašovací údaje úložiště dat můžete také uložit do [úložiště klíčů Azure](https://azure.microsoft.com/services/key-vault/). Data Factory načte pověření během provádění aktivity. Další informace najdete [v tématu Store přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Šifrování dat při přenosu
Pokud úložiště cloudových dat podporuje protokol HTTPS nebo TLS, všechny přenosy dat mezi službami přesunu dat v Datové továrně a úložištěm cloudových dat se nacházejí přes zabezpečený kanál HTTPS nebo TLS .

> [!NOTE]
> Všechna připojení k Azure SQL Database a Azure SQL Data Warehouse vyžadují šifrování (SSL/TLS), zatímco data jsou na cestě do a z databáze. Při vytváření kanálu pomocí JSON přidejte vlastnost šifrování a nastavte ji na **hodnotu true** v připojovacím řetězci. Pro Azure Storage můžete použít **protokol HTTPS** v připojovacím řetězci.

> [!NOTE]
> Chcete-li povolit šifrování při přenosu při přesouvání dat z aplikace Oracle, postupujte podle jedné z následujících možností:
> 1. Na serveru Oracle přejděte na oracle advanced security (OAS) a nakonfigurujte nastavení šifrování, které podporuje triple-DES šifrování (3DES) a advanced encryption standard (AES), naleznete [zde](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) podrobnosti. ADF automaticky vyjedná metodu šifrování pro použití metody, kterou nakonfigurujete v oas při navazování připojení k oracle.
> 2. V adf můžete přidat EncryptionMethod = 1 v připojovacím řetězci (v propojené službě). To bude používat SSL/TLS jako metodu šifrování. Chcete-li použít, je třeba zakázat nastavení šifrování bez SSL v OAS na straně serveru Oracle, aby se zabránilo konfliktu šifrování.

> [!NOTE]
> Použitá verze TLS je 1.2.

### <a name="data-encryption-at-rest"></a>Šifrování v klidovém stavu
Některá úložiště dat podporují šifrování dat v klidovém stavu. Doporučujeme povolit mechanismus šifrování dat pro tato úložiště dat. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparentní šifrování dat (TDE) v Azure SQL Data Warehouse pomáhá chránit před hrozbou škodlivé aktivity tím, že provádí šifrování v reálném čase a dešifrování vašich dat v klidovém stavu. Toto chování je transparentní pro klienta. Další informace naleznete [v tématu Zabezpečení databáze v databázi SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database také podporuje transparentní šifrování dat (TDE), které pomáhá chránit před hrozbou škodlivé aktivity prováděním šifrování v reálném čase a dešifrování dat, aniž by bylo nutné změny aplikace. Toto chování je transparentní pro klienta. Další informace naleznete v [tématu Transparentní šifrování dat pro databázi SQL a datový sklad](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store také poskytuje šifrování pro data uložená v účtu. Pokud je povoleno, Úložiště dat automaticky šifruje data před uchováním a dešifruje před načtením, takže je transparentní pro klienta, který přistupuje k datům. Další informace najdete [v tématu Zabezpečení v Úložišti datových jezer Azure](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Úložiště objektů blob Azure a úložiště tabulek Azure
Úložiště objektů blob Azure a azure table storage podporují šifrování služby Storage Service Encryption (SSE), které automaticky šifruje vaše data před uchováním do úložiště a dešifruje před načtením. Další informace najdete v tématu [Šifrování služby Úložiště Azure pro data v klidovém stavu](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 podporuje šifrování dat klientem i serverem v klidovém stavu. Další informace naleznete [v tématu Ochrana dat pomocí šifrování](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift podporuje šifrování clusteru pro data v klidovém stavu. Další informace naleznete v tématu [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce podporuje šifrování platformy Shield, které umožňuje šifrování všech souborů, příloh a vlastních polí. Další informace naleznete [v tématu Principy toku ověřování oautu webového serveru](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybridní scénáře
Hybridní scénáře vyžadují, aby se prostředí integrace s vlastním hostitelem nainstalovalo do místní sítě, uvnitř virtuální sítě (Azure) nebo uvnitř virtuálního privátního cloudu (Amazon). Prostředí runtime integrace s vlastním hostitelem musí mít přístup k místním úložištím dat. Další informace o prostředí runtime integrace s vlastním hostitelem naleznete v tématu [Jak vytvořit a nakonfigurovat prostředí runtime s vlastním hostitelem integrace](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![runtime kanály integrace s vlastním hostitelem](media/data-movement-security-considerations/data-management-gateway-channels.png)

Příkazový kanál umožňuje komunikaci mezi službami přesunu dat v datové továrně a runtime integrace s vlastním hostitelem. Sdělení obsahuje informace týkající se aktivity. Datový kanál se používá pro přenos dat mezi místními úložišti dat a cloudovými úložišti dat.    

### <a name="on-premises-data-store-credentials"></a>Přihlašovací údaje úložiště místních dat
Přihlašovací údaje mohou být uloženy v rámci datové továrny nebo na ně během běhu z azure key [vaultu odkazovat.](store-credentials-in-key-vault.md) Pokud ukládáte přihlašovací údaje v rámci datové továrny, je vždy uložena šifrovaná v prostředí runtime integrace s vlastním hostitelem. 
 
- **Uložte pověření místně**. Pokud přímo používáte rutinu **Set-AzDataFactoryV2LinkedService** s připojovacími řetězci a přihlašovacími údaji vložených do jsonu, propojená služba je zašifrována a uložena v prostředí runtime integrace s vlastním hostitelem.  V tomto případě pověření toku prostřednictvím služby azure back-end, která je velmi zabezpečená, do počítače integrace s vlastním hostitelem, kde je nakonec zašifrována a uložena. Prostředí runtime integrace s vlastním hostitelem používá systém Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) k šifrování citlivých dat a informací o pověřeních.

- **Uložte přihlašovací údaje v trezoru klíčů Azure**. Přihlašovací údaje úložiště dat můžete také uložit do [úložiště klíčů Azure](https://azure.microsoft.com/services/key-vault/). Data Factory načte pověření během provádění aktivity. Další informace najdete [v tématu Store přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md).

- **Ukládat přihlašovací údaje místně bez přetékání přihlašovacích údajů přes back-end Azure do runtime integrace s vlastním hostitelem**. Pokud chcete šifrovat a ukládat přihlašovací údaje místně v prostředí runtime s vlastním hostitelem integrace, aniž byste museli protékat back-endem datové továrny, postupujte podle pokynů v [části Šifrování přihlašovacích údajů pro místní úložiště dat v Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Tuto možnost podporují všechny konektory. Prostředí runtime integrace s vlastním hostitelem používá systém Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) k šifrování citlivých dat a informací o pověřeních. 

   Pomocí rutiny **New-AzDataFactoryV2LinkedServiceEncryptedCredential** zašifrujte pověření propojené služby a citlivé podrobnosti v propojené službě. Potom můžete použít JSON vrácena (s **EncryptedCredential** element v připojovacím řetězci) k vytvoření propojené služby pomocí **Set-AzDataFactoryV2LinkedService** rutina.  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porty používané při šifrování propojené služby v modulu runtime s vlastním hostitelem integrace
Ve výchozím nastavení používá prostředí PowerShell port 8060 v počítači s prostředím runtime integrace s vlastním hostitelem pro zabezpečenou komunikaci. V případě potřeby lze tento port změnit.  

![Port HTTPS pro bránu](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Šifrování během přenosu
Všechny přenosy dat jsou přes zabezpečený kanál HTTPS a TLS přes TCP, aby se zabránilo útokům prostředníkem během komunikace se službami Azure.

Pomocí [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) nebo [Azure ExpressRoute](../expressroute/expressroute-introduction.md) můžete také dále zabezpečit komunikační kanál mezi místní sítí a Azure.

Virtuální síť Azure je logická reprezentace vaší sítě v cloudu. Místní síť můžete připojit k virtuální síti nastavením sítě IPSec VPN (site-to-site) nebo ExpressRoute (privátní partnerský vztah).    

Následující tabulka shrnuje doporučení konfigurace runtime integrace sítě a vlastní hospovací integrace založená na různých kombinacích zdrojových a cílových umístění pro hybridní přesun dat.

| Zdroj      | Cíl                              | Konfigurace sítě                    | Instalace prostředí Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokálně | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | IPSec VPN (point-to-site nebo site-to-site) | Runtime integrace s vlastním hostitelem by měl být nainstalovaný na virtuálním počítači Azure ve virtuální síti.  |
| Lokálně | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | ExpressRoute (soukromý partnerský vztah)           | Runtime integrace s vlastním hostitelem by měl být nainstalovaný na virtuálním počítači Azure ve virtuální síti.  |
| Lokálně | Služby založené na Azure, které mají veřejný koncový bod | ExpressRoute (partnerský vztah Microsoftu)            | Runtime integrace s vlastním hostitelem lze nainstalovat místně nebo na virtuálním počítači Azure. |

Následující obrázky ukazují použití modulu runtime integrace s vlastním hostitelem pro přesun dat mezi místní databází a službami Azure pomocí ExpressRoute a IPSec VPN (s virtuální sítí Azure):

**ExpressRoute**

![Použití ExpressRoute s bránou](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN s bránou](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>Konfigurace brány firewall a nastavení seznamu povolených adres IP

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Požadavky brány firewall pro místní/privátní síť    
V podniku běží podniková brána firewall na centrálním směrovači organizace. Brána Windows Firewall běží jako daemon v místním počítači, ve kterém je nainstalován vlastní hostovaný integrační runtime. 

Následující tabulka obsahuje požadavky na odchozí port y a domény pro podnikové brány firewall:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Podle požadavků příslušných zdrojů dat bude pravděpodobně nutné spravovat porty nebo nastavit seznam povolených domén pro domény na úrovni podnikové brány firewall. Tato tabulka používá jako příklady jenom Azure SQL Database, Azure SQL Data Warehouse a Azure Data Lake Store.   

V následující tabulce jsou uvedeny požadavky na příchozí porty brány Windows Firewall:

| Příchozí porty | Popis                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Vyžadováno rutinou šifrování Prostředí PowerShell, jak je popsáno v [části Šifrování přihlašovacích údajů pro místní úložiště dat v Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)a aplikací správce přihlašovacích údajů pro bezpečné nastavení přihlašovacích údajů pro místní úložiště dat v prostředí runtime integrace s vlastním hostitelem. |

![Požadavky na port brány](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Konfigurace IP adres a nastavení seznamu povolených adres v úložištích dat
Některá úložiště dat v cloudu také vyžadují, abyste povolili IP adresu počítače, který přistupuje k úložišti. Ujistěte se, že IP adresa samoobslužného integračního runtime stroje je v bráně firewall správně povolena nebo nakonfigurována.

Následující cloudová úložiště dat vyžadují povolení IP adresy počítače runtime integrace s vlastním hostitelem. Některá z těchto úložišť dat ve výchozím nastavení nemusí vyžadovat seznam povolených dat. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Lze vlastní hostovaný integrační runtime sdílet v různých datových továrnách?**

Ano. Další podrobnosti najdete [tady](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Jaké jsou požadavky na port pro vlastní hostované integrační zaběhu do práce?**

Vlastní hostované integrační runtime umožňuje připojení založené na protokolu HTTP pro přístup k Internetu. Chcete-li vytvořit toto připojení, musí být otevřeny odchozí porty 443 pro modul runtime integrace s vlastním hostitelem. Otevřete příchozí port 8060 pouze na úrovni počítače (nikoli na úrovni podnikové brány firewall) pro aplikaci správce pověření. Pokud azure SQL database nebo Azure SQL Data Warehouse se používá jako zdroj nebo cíl, budete muset otevřít port 1433 také. Další informace naleznete v [části Konfigurace brány firewall a nastavení seznamu povolených adres IP.](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) 


## <a name="next-steps"></a>Další kroky
Informace o výkonu aktivity kopírování v Azure Data Factory najdete v tématu [Kopírování výkonu aktivity a průvodce laděním](copy-activity-performance.md).

 
