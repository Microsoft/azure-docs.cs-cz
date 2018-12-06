---
title: Informace o zabezpečení pro přesouvání dat ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o zabezpečení přesouvání dat ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 3c4bd08d2ba3aa4aeceb38a0ae498786f681d800
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960681"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – informace o zabezpečení pro přesun dat

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aspekty zabezpečení přesunu dat pro službu Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Úvod
Tento článek popisuje základní zabezpečení infrastruktury, služby pro přesun dat ve službě Azure Data Factory můžete zabezpečit vaše data. Správa prostředků Azure Data Factory jsou postavené na Azure zabezpečení infrastruktury a použít všechny možné bezpečnostní opatření, které nabízí Azure.

V řešení Data Factory vytváříte jeden nebo více datových [kanálů](data-factory-create-pipelines.md). Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Tyto kanály se nacházejí v oblasti, kde byl objekt pro vytváření dat vytvořen. 

I když služby Data Factory je dostupná jenom v **USA – západ**, **USA – východ**, a **Severní Evropa** oblastí, služba pro přesun dat je k dispozici [globálně v několik oblastí](data-factory-data-movement-activities.md#global). Služba data Factory zajišťuje, že data, nenechává zeměpisné oblasti a oblasti Pokud dáte pokyn explicitně službu používat alternativní oblast, pokud služba pro přesun dat ještě není nasazený pro tuto oblast. 

Azure Data Factory, samotný neukládá data s výjimkou přihlašovacích údajů propojené služby pro cloudovými úložišti dat, které jsou šifrované pomocí certifikátů. Umožňuje vytvářet pracovní postupy řízené daty k orchestraci přesouvání dat mezi [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a zpracování dat pomocí [výpočetních služeb](data-factory-compute-linked-services.md) v jiných oblastech nebo v místním prostředí. Také vám umožňuje [monitorovat a spravovat pracovní postupy](data-factory-monitor-manage-pipelines.md) pomocí uživatelského prostředí nebo prostřednictvím kódu programu.

Přesun dat pomocí Azure Data Factory bylo **certified** pro:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Pokud vás zajímají dodržování předpisů Azure a jak zabezpečuje svou vlastní infrastrukturu Azure, přejděte [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

V tomto článku jsme projděte si informace o zabezpečení v následujících scénářích Přesun do dvou datových: 

- **Scénář cloudu**– v tomto scénáři zdroj a cíl nacházejí veřejně přístupná prostřednictvím Internetu. Patří mezi ně úložiště spravované cloudové služby jako Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, služeb SaaS, jako je Salesforce a webové protokoly, jako je například FTP a OData. Úplný seznam podporovaných zdrojů dat můžete najít [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hybridní scénář**– v tomto scénáři zdroji nebo cíli nachází za bránou firewall nebo uvnitř podnikové sítě v místním nebo data store je v privátní síti nebo virtuální sítě (nejčastěji zdroj) a není veřejně přístupná. Databázové servery hostované ve virtuálních počítačích také spadají pod tento scénář.

## <a name="cloud-scenarios"></a>Cloudové scénáře
### <a name="securing-data-store-credentials"></a>Ukládání přihlašovacích údajů, zabezpečení dat
Azure Data Factory chrání vaše přihlašovací údaje úložiště dat podle **šifrování** je pomocí **certifikátů spravovaný microsoftem**. Tyto certifikáty jsou otočeny každý **dva roky** (která zahrnuje obnovení certifikátu a migrace přihlašovacích údajů). Tyto zašifrované přihlašovací údaje jsou bezpečně uložené v **služby Azure Storage spravovaných službou Azure Data Factory management services**. Další informace o zabezpečení služby Azure Storage, najdete v tématu [Přehled zabezpečení služby Azure Storage](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Šifrování dat při přenosu
Pokud cloudovým úložištěm dat podporuje protokol HTTPS nebo TLS, všechny datové přenosy mezi služby pro přesun dat ve službě Data Factory a cloudovým úložištěm dat jsou prostřednictvím zabezpečeného kanálu protokolu HTTPS nebo TLS.

> [!NOTE]
> Všechna připojení k **Azure SQL Database** a **Azure SQL Data Warehouse** vždy šifrování (SSL/TLS) se při přenosu dat do a z databáze. Při vytváření kanálu pomocí editoru JSON, přidejte **šifrování** vlastnost a nastavte ho na **true** v **připojovací řetězec**. Při použití [Průvodce kopírováním](data-factory-azure-copy-wizard.md), průvodce se tato vlastnost nastaví ve výchozím nastavení. Pro **služby Azure Storage**, můžete použít **HTTPS** v připojovacím řetězci.

### <a name="data-encryption-at-rest"></a>Šifrování v klidovém stavu
Některá data uloží podpora šifrování dat v klidovém stavu. Doporučujeme, abyste povolili mechanismus pro šifrování dat pro úložiště těchto dat. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparentní šifrování dat (TDE) ve službě Azure SQL Data Warehouse pomáhá s tím, že provádí v reálném čase šifrování a dešifrování dat v klidovém stavu ochranu před hrozbou škodlivých činností. Toto chování je pro klienta transparentní. Další informace najdete v tématu [zabezpečit databázi ve službě SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database také podporuje transparentní šifrování dat (TDE), který pomáhá při ochraně před hrozbou škodlivých aktivit pomocí provádí v reálném čase šifrování a dešifrování dat bez nutnosti změny aplikace. Toto chování je pro klienta transparentní. Další informace najdete v tématu [transparentního šifrování dat s využitím Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store také zajišťuje šifrování dat uložených v účtu. Pokud povolená, Data Lake store automaticky šifruje data před uložením a dešifruje před načtení, takže transparentní klient přistupuje k datům. Další informace najdete v tématu [zabezpečení v Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage a Azure Table Storage
Azure Blob Storage a Azure Table storage podporuje šifrování služby Storage (SSE), který automaticky šifruje vaše data před zachováním v úložišti a dešifruje před načítání. Další informace najdete v tématu [šifrování služby Azure Storage pro neaktivní uložená Data](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 podporuje klient i server šifrování dat v klidovém stavu. Další informace najdete v tématu [ochranu dat pomocí šifrování](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Data Factory v současné době nepodporuje Amazon S3 ve virtuálním privátním cloudu (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift podporuje clusteru šifrování pro neaktivní uložená data. Další informace najdete v tématu [šifrování databáze Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Data Factory v současné době nepodporuje Amazon Redshift uvnitř VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce podporuje šifrování platformy Shield, který umožňuje šifrování všech souborů, přílohy, vlastních polí. Další informace najdete v tématu [Principy tok webového serveru pro ověřování OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybridní scénáře (pomocí brány správy dat)
Hybridní scénáře vyžadují bránu správy dat nainstalovaný, v místní síti nebo uvnitř virtuální sítě (Azure) nebo virtuální privátní cloud (Amazon). Musí být schopni přistupovat k úložištím dat místní brána. Další informace o bráně najdete v tématu [brána správy dat](data-factory-data-management-gateway.md). 

![Kanály brána správy dat](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Kanál příkazu** umožňuje komunikaci mezi služby pro přesun dat ve službě Data Factory a brána správy dat. Komunikace obsahuje informace týkající se aktivity. Datový kanál se používá pro přenos dat mezi úložišti dat s místními a cloudovými datovými úložišti.    

### <a name="on-premises-data-store-credentials"></a>Místní přihlašovací údaje úložiště dat
Přihlašovací údaje pro vaše místní úložiště dat se ukládají místně (ne v cloudu). To můžete udělat třemi různými způsoby. 

- Pomocí **prostého textu** (méně bezpečné) prostřednictvím protokolu HTTPS z webu Azure Portal / Průvodce kopírováním. Přihlašovací údaje se předají jako prostý text místní brány.
- Pomocí **knihovny JavaScript kryptografie z Průvodce kopírováním**.
- Pomocí **klikněte na možnost-po na základě přihlašovacích údajů správce aplikace**. Kliknutím na – Jakmile se aplikace spustí na místním počítači, který má přístup k bráně a nastaví přihlašovací údaje k úložišti. Tuto možnost a další příkaz, je nejbezpečnější možnosti. Aplikace Správce přihlašovacích údajů, ve výchozím nastavení, používá port 8050 na počítači s bránou pro zabezpečenou komunikaci.  
- Použití [New-AzureRmDataFactoryEncryptValue](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) rutiny Powershellu pro šifrování přihlašovacích údajů. Rutina se používá certifikát této brány je nakonfigurován na použití pro šifrování přihlašovacích údajů. Můžete použít vrácená touto rutinou zašifrovanými přihlašovacími údaji a přidejte ho do **EncryptedCredential** elementu **connectionString** v souboru JSON, který používáte s [ Nové AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) rutiny nebo v tomto fragmentu kódu JSON v editoru služby Data Factory na portálu. Tuto možnost a kliknutím na-nejbezpečnější možnosti po aplikaci. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Šifrování na základě knihovny kryptografie jazyka JavaScript
Můžete šifrovat přihlašovací údaje úložiště dat pomocí [knihovny JavaScript kryptografie](https://www.microsoft.com/download/details.aspx?id=52439) z [Průvodce kopírováním](data-factory-copy-wizard.md). Když vyberete tuto možnost, Průvodce kopírováním načte veřejný klíč brány a použije k zašifrování dat úložiště pověření. Přihlašovací údaje jsou dešifrovat počítači brány a je chráněn Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Podporované prohlížeče:** aplikaci Internet Explorer 8, 9, aplikace Internet Explorer 10, 11, Microsoft Edge a poslední Firefox, Chrome, Opera, prohlížeče Safari. 

#### <a name="click-once-credentials-manager-app"></a>Klikněte na tlačítko-jednou aplikace Správce přihlašovacích údajů
Můžete spustit kliknutím na-až na základě přihlašovacích údajů správce aplikace z Azure portal nebo kopírování Průvodce při vytváření kanálů. Tuto aplikaci se zajistí, že přihlašovací údaje se nepřenesou ve formátu prostého textu při přenosu. Ve výchozím nastavení, používá port **8050** na počítači s bránou pro zabezpečenou komunikaci. V případě potřeby můžete tento port změnit.  
  
![Port HTTPS pro brány](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

V současné době brána správy dat používá jednu **certifikát**. Tento certifikát je vytvořena během instalace brány (platí pro bránu správy dat vytvořené po. listopadu 2016 a 2.4.xxxx.x verze nebo novější). Tento certifikát můžete nahradit vlastní certifikát SSL/TLS. Kliknutím na tento certifikát slouží-jednou přihlašovacích údajů správce aplikaci pro zabezpečené připojení k počítači brány pro nastavení pověření datového úložiště. Ukládá data přihlašovacích údajů úložiště bezpečně místní pomocí Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) na počítači s bránou. 

> [!NOTE]
> Starší brány, které byly nainstalovány před. listopadu 2016 nebo verze 2.3.xxxx.x i nadále používat přihlašovací údaje zašifrované a uložené v cloudu. I v případě, že upgradujete na nejnovější verzi brány, přihlašovací údaje se nebudou migrovat do místního počítače    
  
| Verze brány (během vytváření) | Přihlašovací údaje uložené | Šifrování přihlašovacích údajů a zabezpečení | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | V cloudu | Šifrované pomocí certifikátu (jiný než ten, který používá aplikace Správce přihlašovacích údajů) | 
| > = 2.4.xxxx.x | V místním prostředí | Zabezpečené pomocí rozhraní DPAPI | 
  

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Všechny přenosy dat jsou prostřednictvím zabezpečeného kanálu **HTTPS** a **protokol TLS přes protokol TCP** prevenci proti útokům man-in-the-middle během komunikace se službami Azure.
 
Můžete také použít [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) nebo [Express Route](../../expressroute/expressroute-introduction.md) na další Zabezpečte komunikační kanál mezi místní sítí a Azure.

Virtuální síť je logickou reprezentaci vaší vlastní sítě v cloudu. Místní sítě můžete připojit ke službě Azure virtual network (VNet) nastavením (site-to-site) VPN typu IPSec nebo Expressroute (soukromého partnerského vztahu)     

Následující tabulka shrnuje doporučené konfigurace sítě a brány založené na různé kombinace zdrojové a cílové umístění pro přesun hybridních dat.

| Zdroj | Cíl | Konfigurace sítě | Instalační program brány |
| ------ | ----------- | --------------------- | ------------- | 
| Lokálně | Virtuální počítače a cloudové aplikace nasazené ve virtuálních sítích | (Point-to-site a site-to-site) VPN typu IPSec | Může jí být nainstalována na místní nebo v Azure virtuální počítač (VM) ve virtuální síti | 
| Lokálně | Virtuální počítače a cloudové aplikace nasazené ve virtuálních sítích | ExpressRoute (soukromého partnerského vztahu) | Může jí být nainstalované místně nebo na Virtuálním počítači Azure ve virtuální síti | 
| Lokálně | Službám Azure, které mají veřejný koncový bod | ExpressRoute (veřejného partnerského vztahu) | Brána musí být nainstalovaný místní | 

Následující obrázky znázorňují použití brány správy dat pro přesun dat mezi místní databáze a služby Azure s využitím expressroute a VPN typu IPSec (službou Virtual Network):

**Expressroute:**
 
![Pomocí Expressroute s bránou](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**Protokol IPSec VPN:**

![IPSec VPN s bránou](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Konfigurace bran firewall a přidávání na seznam povolených IP adresu brány

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Požadavky na bránu firewall pro o premises/soukromé síti  
V podniku **podnikovou bránu firewall** běží v centrální směrovače organizace. A **brány Windows firewall** spouští se jako démon na místním počítači, na kterém je nainstalovaná brána. 

Následující tabulka obsahuje **odchozí port** a požadavky domény **podnikovou bránu firewall**.

| Názvy domén | Odchozí porty | Popis |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Vyžaduje bránu pro připojení služby pro přesun dat ve službě Data Factory |
| `*.core.windows.net` | 443 | Používat bránu pro připojení k účtu úložiště Azure při použití [fázovaného kopírování](data-factory-copy-activity-performance.md#staged-copy) funkce. | 
| `*.frontend.clouddatahub.net` | 443 | Vyžaduje bránu pro připojení ke službě Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (Volitelné) je nutné zadat cíl je Azure SQL Database nebo Azure SQL Data Warehouse. Pomocí funkce dvoufázové instalace kopírování ke kopírování dat do Azure SQL Database nebo Azure SQL Data Warehouse, aniž byste museli otevírat port 1433. | 
| `*.azuredatalakestore.net` | 443 | (Volitelné) je nutné zadat cíl je Azure Data Lake store | 

> [!NOTE] 
> Možná budete muset spravovat porty / přidávání na seznam povolených domén na podnikové brány firewall na úrovni podle potřeby podle příslušné datové zdroje. Tato tabulka používá pouze v Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store jako příklady.   

Následující tabulka obsahuje **příchozí port** požadavky **brány windows firewall**.

| Příchozí porty | Popis | 
| ------------- | ----------- | 
| 8050 (TCP) | Vyžadují aplikace Správce pověření bezpečně nastavíte přihlašovací údaje pro místní úložiště dat k bráně. | 

![Požadavky na porty brány](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Konfigurace protokolu IP nebo na seznam povolených v datech úložiště
Některá úložiště dat v cloudu také vyžadovat vytváření seznamu povolených IP adresu počítače přístup k nim. Ujistěte se, že IP adresa počítače brány je přidat na seznam povolených / nakonfigurují v bráně firewall odpovídajícím způsobem.

Následující cloudovými úložišti dat vyžadují vytváření seznamu povolených IP adres počítači brány. Některé z těchto úložišť dat, ve výchozím nastavení, ale nemusí od vyžadovat vytváření seznamu povolených IP adres. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** může bránu sdílet mezi různé datové továrny?
**Odpověď:** této funkce zatím nepodporujeme. Aktivně pracujeme na něj.

**Otázka:** jaké jsou požadavky na porty pro funkci brány?
**Odpověď:** brána umožňuje připojení založené na protokolu HTTP do otevřeného Internetu. **Odchozí porty 443 a 80** musí být otevřen pro bránu pro toto připojení. Otevřít **příchozí Port 8050** pouze na úrovni počítače (ne na podnikové brány firewall na úrovni) pro aplikaci správce přihlašovacích údajů. Pokud se používá Azure SQL Database nebo Azure SQL Data Warehouse jako zdroj / cíl, budete muset otevřít **1433** i port. Další informace najdete v tématu [brány Firewall, konfigurace a vytváření seznamu povolených IP adres](#firewall-configurations-and-whitelisting-ip-address-of gateway) oddílu. 

**Otázka:** jaké jsou požadavky na certifikát pro bránu?
**Odpověď:** aktuální brána vyžaduje certifikát, který používá aplikace Správce pověření pro zabezpečené přihlašovací údaje úložiště dat nastavení. Tento certifikát se certifikát podepsaný svým držitelem vytvořený a nakonfigurovaný nastavením brány. Můžete použít vlastní protokol TLS / SSL certifikátu místo toho. Další informace najdete v tématu [klikněte na tlačítko-jednou přihlašovacích údajů správce aplikace](#click-once-credentials-manager-app) oddílu. 

## <a name="next-steps"></a>Další postup
Informace o výkonu aktivity kopírování najdete v tématu [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md).

 
