---
title: Důležité informace o zabezpečení ve službě Azure Data Factory | Dokumentace Microsoftu
description: Popisuje základní zabezpečení infrastruktury, služby pro přesun dat ve službě Azure Data Factory použít pomáhají zabezpečit vaše data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: d684ec56c7dfcc28d1057d0b20905db49bce9723
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498060"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Informace o zabezpečení pro přesouvání dat ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
>
> * [Verze 1](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuální verze](data-movement-security-considerations.md)

Tento článek popisuje základní zabezpečení infrastruktury, služby pro přesun dat ve službě Azure Data Factory použít pomáhají zabezpečit vaše data. Prostředky data Factory správy jsou postavené na Azure zabezpečení infrastruktury a použijte všechny možné bezpečnostní opatření, které nabízí Azure.

V řešení Data Factory vytváříte jeden nebo více datových [kanálů](concepts-pipelines-activities.md). Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Tyto kanály se nacházejí v oblasti, kde byl objekt pro vytváření dat vytvořen. 

I když Data Factory je dostupná jenom v několika oblastech, služba pro přesun dat je [dostupná globálně](concepts-integration-runtime.md#integration-runtime-location) zajistit dodržování předpisů pro data, efektivita a snížená síťová výchozí přenos dat náklady. 

Azure Data Factory neukládá data s výjimkou přihlašovacích údajů propojené služby pro cloudovými úložišti dat, které jsou šifrované pomocí certifikátů. Pomocí služby Data Factory vytvoříte pracovní postupy řízené daty k orchestraci přesouvání dat mezi [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats)a zpracování dat pomocí [výpočetních služeb](compute-linked-services.md) v jiných oblastech nebo v v místním prostředí. Můžete také sledovat a spravovat pracovní postupy pomocí sady SDK a Azure Monitor.

Data Factory má certifikaci pro:
| **[Certifikace CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Pokud vás zajímá dodržování předpisů Azure a jak zabezpečuje svou vlastní infrastrukturu Azure, přejděte [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). Nejnovější seznam všech nabídek kontrola dodržování předpisů Azure – http://aka.ms/AzureCompliance.

V tomto článku jsme projděte si informace o zabezpečení v následujících scénářích Přesun do dvou datových: 

- **Scénář cloudu**: V tomto scénáři jsou veřejně přístupné prostřednictvím Internetu zdroj a cíl. Mezi ně patří služby spravované cloudové úložiště jako je Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, služeb SaaS, jako je Salesforce a webové protokoly, jako je například FTP a OData. Úplný seznam podporovaných zdrojů dat v [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hybridní scénář**: V tomto scénáři je váš zdroj nebo cíl za bránou firewall nebo uvnitř podnikové sítě v místním prostředí. Nebo úložiště dat je v privátní síti nebo virtuální síti (nejčastěji zdroj) a není veřejně přístupná. Databázové servery hostované ve virtuálních počítačích také spadají pod tento scénář.

## <a name="cloud-scenarios"></a>Cloudové scénáře

### <a name="securing-data-store-credentials"></a>Ukládání přihlašovacích údajů, zabezpečení dat

- **Store zašifrované přihlašovací údaje ve spravovaném obchodu Azure Data Factory**. Objekt pro vytváření dat pomáhá chránit vaše přihlašovací údaje úložiště dat tím, že šifruje pomocí certifikátů spravovaný microsoftem. Tyto certifikáty jsou otočen za dva roky (to zahrnuje i obnovení certifikátu a migrace přihlašovacích údajů). Zašifrované přihlašovací údaje jsou bezpečně uložené v účtu služby Azure storage spravuje Správa služby Azure Data Factory. Další informace o zabezpečení služby Azure Storage najdete v tématu [Přehled zabezpečení služby Azure Storage](../security/security-storage-overview.md).
- **Store přihlašovacích údajů ve službě Azure Key Vault**. Můžete také ukládat přihlašovací údaje úložiště dat v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Objekt pro vytváření dat načte přihlašovací údaje, které během provádění aktivity. Další informace najdete v tématu [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Šifrování dat při přenosu
Pokud cloudovým úložištěm dat podporuje protokol HTTPS nebo TLS, všechny datové přenosy mezi služby pro přesun dat ve službě Data Factory a cloudovým úložištěm dat jsou prostřednictvím zabezpečeného kanálu protokolu HTTPS nebo TLS.

> [!NOTE]
> Všechna připojení k Azure SQL Database a Azure SQL Data Warehouse vyžaduje šifrování (SSL/TLS), zatímco jsou data přenášená do a z databáze. Pokud při vytváření kanálu s použitím souboru JSON, přidejte vlastnost šifrování a nastavte ho na **true** v připojovacím řetězci. Pro službu Azure Storage, můžete použít **HTTPS** v připojovacím řetězci.

> [!NOTE]
> Povolit šifrování během přenosu při přesouvání dat od Oraclu, proveďte jeden z níže uvedených možností:
> 1. Na serveru Oracle, přejděte k Oracle rozšířené zabezpečení (OAS) a konfigurovat nastavení šifrování, která podporuje Triple-DES šifrování (3DES) a Advanced Encryption (Standard AES), přečtěte si [tady](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) podrobnosti. ADF automatické způsob šifrování použít ten, který konfigurujete v OAS při navazování připojení k systému Oracle.
> 2. Ve službě ADF, můžete přidat EncryptionMethod = 1 v připojovacím řetězci (v propojené službě). Tímto dojde k použití protokolu SSL/TLS jako metodu šifrování. K tomu je nutné zakázat jiného typu než SSL nastavení šifrování v OAS na straně serveru Oracle aby nedošlo ke konfliktu šifrování.

> [!NOTE]
> Je použita verze protokolu TLS 1.2.

### <a name="data-encryption-at-rest"></a>Šifrování v klidovém stavu
Některá data uloží podpora šifrování dat v klidovém stavu. Doporučujeme, abyste povolili mechanismus pro šifrování dat pro úložiště těchto dat. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparentní šifrování dat (TDE) ve službě Azure SQL Data Warehouse pomáhá chránit před hrozbou škodlivých aktivit pomocí provádí v reálném čase šifrování a dešifrování dat v klidovém stavu. Toto chování je pro klienta transparentní. Další informace najdete v tématu [zabezpečit databázi ve službě SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database také podporuje transparentní šifrování dat (TDE), která pomáhá chránit před hrozbou škodlivé aktivity tím, že provádí v reálném čase šifrování a dešifrování dat, bez nutnosti změny aplikace. Toto chování je pro klienta transparentní. Další informace najdete v tématu [transparentní šifrování dat pro SQL Database a Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store také zajišťuje šifrování dat uložených v účtu. Pokud povolená, Data Lake Store automaticky šifruje data před uložením a dešifruje před načtení, takže transparentní klienta, který přistupuje k datům. Další informace najdete v tématu [zabezpečení v Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob storage a Azure Table storage
Azure Blob storage a Azure Table storage podporuje šifrování služby Storage (SSE), který automaticky šifruje vaše data před zachováním v úložišti a dešifruje před načítání. Další informace najdete v tématu [šifrování služby Azure Storage pro neaktivní uložená Data](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 podporuje klient i server šifrování dat v klidovém stavu. Další informace najdete v tématu [ochranu dat pomocí šifrování](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift podporuje clusteru šifrování pro neaktivní uložená data. Další informace najdete v tématu [šifrování databáze Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce podporuje šifrování platformy Shield, který umožňuje šifrování všech souborů, příloh a vlastních polí. Další informace najdete v tématu [Principy tok webového serveru pro ověřování OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybridní scénáře
Hybridní scénáře vyžadují místní prostředí integration runtime nainstalovaný v místní síti, ve virtuální síti (Azure) nebo ve virtuálním privátním cloudu (Amazon). Místní prostředí integration runtime musí být schopni přistupovat k úložištím dat místní. Další informace o místní prostředí integration runtime najdete v tématu [jak vytvořit a nakonfigurovat v místním prostředí integration runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![místní prostředí integration runtime kanály](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kanál příkazu umožňuje komunikaci mezi služby pro přesun dat ve službě Data Factory a místní prostředí integration runtime. Komunikace obsahuje informace týkající se aktivity. Datový kanál se používá pro přenos dat mezi úložišti dat s místními a cloudovými datovými úložišti.    

### <a name="on-premises-data-store-credentials"></a>Místní přihlašovací údaje úložiště dat
Přihlašovací údaje pro vaše místní úložiště dat jsou vždy zašifrované a uložené. Může být buď ukládají místně na počítači s modulem runtime integrace v místním prostředí nebo uložené v úložišti spravované služby Azure Data Factory (stejně jako přihlašovací údaje úložiště v cloudu). 

- **Store pověření místně**. Pokud chcete k šifrování a ukládání přihlašovacích údajů místně do místního prostředí integration runtime, postupujte podle kroků v [šifrovat přihlašovací údaje pro místní úložiště dat ve službě Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Všechny konektory podporují tuto možnost. Modul runtime integrace v místním prostředí používá Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) pro šifrování citlivých informací data a přihlašovací údaje. 

   Použití **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** rutiny pro šifrování přihlašovacích údajů propojené služby a citlivých podrobnosti v propojené službě. Pak můžete použít vráceném kódu JSON (s **EncryptedCredential** element v připojovacím řetězci) k vytvoření propojené služby s použitím **Set-AzureRmDataFactoryV2LinkedService** rutiny.  

- **Store ve službě Azure Data Factory managed storage**. Pokud používáte přímo **Set-AzureRmDataFactoryV2LinkedService** rutiny s připojením řetězce a přihlašovací údaje vložený v kódu JSON, je zašifrované a uložené v úložišti spravované služby Azure Data Factory propojené služby. Certifikát je stále šifrovaný citlivé informace a Microsoft spravuje tyto certifikáty.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porty používané při šifrování propojené služby v místním prostředí integration runtime
Prostředí PowerShell ve výchozím nastavení používá port 8050 na počítači s místní prostředí integration runtime pro zabezpečenou komunikaci. V případě potřeby můžete tento port změnit.  

![Port HTTPS pro brány](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Šifrování během přenosu
Všechny přenosy dat jsou prostřednictvím zabezpečeného kanálu protokolu HTTPS a protokol TLS přes protokol TCP. aby se zabránilo útokům man-in-the-middle během komunikace se službami Azure.

Můžete také použít [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) nebo [Azure ExpressRoute](../expressroute/expressroute-introduction.md) na další Zabezpečte komunikační kanál mezi místní sítí a Azure.

Azure Virtual Network je logickou reprezentaci vaší vlastní sítě v cloudu. Nastavením (site-to-site) VPN typu IPSec nebo ExpressRoute (soukromého partnerského vztahu) můžete připojit místní sítě k virtuální síti.    

Následující tabulka shrnuje sítě a doporučené konfigurace modulu runtime integrace v místním prostředí založené na různé kombinace zdrojové a cílové umístění pro přesun hybridních dat.

| Zdroj      | Cíl                              | Konfigurace sítě                    | Instalace prostředí Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokálně | Virtuální počítače a cloudové aplikace nasazené ve virtuálních sítích | (Point-to-site a site-to-site) VPN typu IPSec | Místní prostředí integration runtime může být nainstalována na místní nebo na virtuálním počítači ve virtuální síti Azure. |
| Lokálně | Virtuální počítače a cloudové aplikace nasazené ve virtuálních sítích | ExpressRoute (soukromého partnerského vztahu)           | Místní prostředí integration runtime může být nainstalována na místní nebo na virtuálním počítači ve virtuální síti Azure. |
| Lokálně | Službám Azure, které mají veřejný koncový bod | ExpressRoute (veřejného partnerského vztahu)            | Místní prostředí integration runtime musí být nainstalovaný místní. |

Následující obrázky znázorňují použití místní prostředí IR pro přesun dat mezi místní databáze a služby Azure pomocí ExpressRoute a VPN typu IPSec (s Azure Virtual Network):

**ExpressRoute**

![Pomocí ExpressRoute s bránou](media/data-movement-security-considerations/express-route-for-gateway.png) 

**Protokol IPSec VPN**

![IPSec VPN s bránou](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Konfigurace bran firewall a přidávání na seznam povolených IP adres

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Požadavky na bránu firewall pro o premises/soukromé síti  
V podniku běží podniková brána firewall centrální směrovače organizace. Brána Windows Firewall spouští se jako démon na místním počítači, ve kterém je nainstalována místní prostředí integration runtime. 

Následující tabulka obsahuje odchozí požadavky na port a domény pro podnikové brány firewall:

| Názvy domén                  | Odchozí porty | Popis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Místní prostředí integration runtime vyžaduje pro připojení služby pro přesun dat ve službě Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Místní prostředí integration runtime vyžaduje pro připojení ke službě Data Factory. |
| `download.microsoft.com`    | 443            | Vyžaduje místní prostředí integration runtime pro stahování aktualizací. Pokud jste zakázali automatickou aktualizaci může toto přeskočit. |
| `*.core.windows.net`          | 443            | Používá pro připojení k účtu úložiště Azure, když použijete místní prostředí integration runtime [fázovaného kopírování](copy-activity-performance.md#staged-copy) funkce. |
| `*.database.windows.net`      | 1433           | (Volitelné) Vyžadováno při kopírování z nebo do Azure SQL Database nebo Azure SQL Data Warehouse. Pomocí funkce dvoufázové instalace kopírování ke kopírování dat do Azure SQL Database nebo Azure SQL Data Warehouse, aniž byste museli otevírat port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Volitelné) Vyžadováno při kopírování z nebo do Azure Data Lake Store. |

> [!NOTE] 
> Budete muset spravovat porty nebo přidávání na seznam povolených domén na úrovni podnikovou bránu firewall podle potřeby podle příslušné datové zdroje. Tato tabulka pouze používá Azure SQL Database, Azure SQL Data Warehouse a Azure Data Lake Store jako příklady.   

Následující tabulka uvádí požadavky na příchozí port pro bránu Windows Firewall:

| Příchozí porty | Popis                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Vyžaduje šifrování rutina prostředí PowerShell, jak je popsáno v [šifrovat přihlašovací údaje pro místní úložiště dat ve službě Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)a pomocí aplikace Správce pověření bezpečně nastavíte přihlašovací údaje pro místní úložiště dat v místním prostředí integration runtime. |

![Požadavky na porty brány](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>Konfigurace protokolu IP a přidávání na seznam povolených v úložištích dat.
Některá úložiště dat v cloudu vyžadovat také IP adresu počítače přístup k úložišti povolených. Ujistěte se, že IP adresa počítače místní prostředí integration runtime je povolený nebo správně nakonfigurována v bráně firewall.

Následující cloudovými úložišti dat vyžadují IP adresu počítače místní prostředí integration runtime povolených. Některé z těchto úložišť dat, ve výchozím nastavení, nemusí potřebovat přidávání na seznam povolených. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Je možné sdílet místní prostředí integration runtime v různých datových továren?**

Tato funkce zatím nepodporujeme. Aktivně pracujeme na něj.

**Jaké jsou požadavky na porty pro místní prostředí integration runtime pro práci?**

Místní prostředí integration runtime je založené na protokolu HTTP připojení pro přístup k Internetu. Odchozí porty 443 musí být otevřen pro místní prostředí integration runtime k vytvoření tohoto připojení. Otevřete příchozí port 8050 pouze na úrovni počítače (ne na úrovni podniková brána firewall) pro aplikace Správce pověření. Pokud se databáze SQL Azure nebo Azure SQL Data Warehouse používá jako zdroj nebo cíl, budete muset otevřít port 1433 také. Další informace najdete v tématu [brány Firewall, konfigurace a vytváření seznamu povolených IP adres](#firewall-configurations-and-whitelisting-ip-address-of-gateway) oddílu. 


## <a name="next-steps"></a>Další postup
Informace o výkonu aktivity kopírování objekt pro vytváření dat Azure najdete v tématu [Průvodce laděním a výkonem aktivity kopírování](copy-activity-performance.md).

 
