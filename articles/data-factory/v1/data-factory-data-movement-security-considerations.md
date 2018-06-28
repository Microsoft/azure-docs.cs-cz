---
title: Důležité informace o zabezpečení pro přesun dat v Azure Data Factory | Microsoft Docs
description: Další informace o zabezpečení přesun dat v Azure Data Factory.
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
ms.openlocfilehash: 222558a6596c676034e52812d3b2dd0c77e1466b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046897"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - důležité informace o zabezpečení pro přesun dat

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [aspekty zabezpečení přesunu dat pro objekt pro vytváření dat](../data-movement-security-considerations.md).

## <a name="introduction"></a>Úvod
Tento článek popisuje základní zabezpečení infrastrukturu, která pomocí služby pro přesun dat v Azure Data Factory zabezpečit data. Správa prostředků Azure Data Factory jsou postaveny na infrastrukturu zabezpečení Azure a použít všechny možné bezpečnostní opatření, které nabízí Azure.

V řešení Data Factory vytváříte jeden nebo více datových [kanálů](data-factory-create-pipelines.md). Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Tyto kanály nacházet v oblasti, kde byl vytvořen služby data factory. 

I když objekt pro vytváření dat je k dispozici v pouze **západní USA**, **východní USA**, a **Severní Evropa** oblastí, služba pro přesun dat je k dispozici [globálně v několik oblastí](data-factory-data-movement-activities.md#global). Služba data Factory zajistí, že data nenechává zeměpisné oblasti nebo oblast Pokud dáte pokyn explicitně službu na používání alternativní oblast, pokud služba pro přesun dat, ještě není nasazený do této oblasti. 

Azure Data Factory, samotné neukládá všechna data s výjimkou pověření propojené služby pro cloudové úložiště dat, které jsou zašifrované pomocí certifikátů. Umožňuje vytvářet pracovní postupy řízené daty k orchestraci přesouvání dat mezi [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a zpracování dat pomocí [výpočetních služeb](data-factory-compute-linked-services.md) v jiných oblastech nebo v místním prostředí. Také vám umožňuje [monitorovat a spravovat pracovní postupy](data-factory-monitor-manage-pipelines.md) pomocí uživatelského prostředí nebo prostřednictvím kódu programu.

Přesun dat pomocí Azure Data Factory byl **certifikované** pro:
-   [HIPAA NEBO HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA HVĚZDIČKOU](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Pokud vás zajímá Azure dodržování předpisů a jak Azure zabezpečuje svou vlastní infrastrukturu, navštivte [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

V tomto článku jsme zkontrolujte důležité informace o zabezpečení v následujících scénářích přesun dvě dat: 

- **Scénář cloudu**– v tomto scénáři jsou veřejně přístupná prostřednictvím Internetu zdrojový i cílový. Mezi ně patří spravované cloudové úložiště služby Azure Storage, Azure SQL Data Warehouse, databáze SQL Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, služby SaaS, jako je například služby Salesforce a webové protokoly například protokoly FTP a OData. Úplný seznam podporovaných zdrojů dat můžete najít [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Scénář hybridního**– v tomto scénáři vaše zdrojové nebo cílové je za bránou firewall nebo uvnitř podnikové sítě na místní nebo data úložiště je v privátní síti / virtuální sítě (nejčastěji zdroje) a není veřejně přístupná. Databázové servery hostované ve virtuálních počítačích také spadají pod tento scénář.

## <a name="cloud-scenarios"></a>Scénáře cloudu
### <a name="securing-data-store-credentials"></a>Zabezpečení údaje k úložišti dat.
Azure Data Factory chrání přihlašovací údaje k úložišti dat pomocí **šifrování** je pomocí **certifikáty, které spravuje Microsoft**. Tyto certifikáty otáčejí každých **dva roky** (která zahrnuje obnovení certifikátu a migrace přihlašovacích údajů). Tyto zašifrované přihlašovací údaje jsou bezpečně uložené v **Azure Storage spravuje služby Azure Data Factory pro**. Další informace o zabezpečení Azure Storage, najdete v části [Přehled zabezpečení služby Azure Storage](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Šifrování dat při přenosu
Pokud cloudové úložiště dat podporuje protokol HTTPS nebo TLS, všech dat přenesených mezi služby pro přesun dat v datové továrně a cloudové úložiště dat jsou prostřednictvím zabezpečeného kanálu HTTPS nebo TLS.

> [!NOTE]
> Všechna připojení k **Azure SQL Database** a **Azure SQL Data Warehouse** vždy vyžadovat šifrování (SSL/TLS) se při přenosu dat, do a z databáze. Při vytváření kanálu pomocí editoru JSON, přidejte **šifrování** vlastnost a nastavte ji na **true** v **připojovací řetězec**. Při použití [Průvodce kopírováním](data-factory-azure-copy-wizard.md), průvodce se tato vlastnost nastaví ve výchozím nastavení. Pro **Azure Storage**, můžete použít **HTTPS** v připojovacím řetězci.

### <a name="data-encryption-at-rest"></a>Šifrování v klidovém stavu
Některá data ukládá podpora šifrování dat v klidovém stavu. Doporučujeme, abyste povolili mechanismus šifrování dat pro tyto datové úložiště. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparentní dat šifrování (TDE) v Azure SQL Data Warehouse pomáhá s provedením v reálném čase šifrování a dešifrování dat v klidovém stavu ochrany proti riziko, že škodlivé aktivity. Toto chování je pro klienta transparentní. Další informace najdete v tématu [zabezpečení databáze v SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database podporuje také transparentní šifrování dat (šifrování TDE), která pomáhá s ochrany proti riziko, že škodlivé aktivity provedením v reálném čase šifrování a dešifrování dat bez nutnosti změny aplikace. Toto chování je pro klienta transparentní. Další informace najdete v tématu [transparentní šifrování dat s Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store taky zajišťuje šifrování dat uložených v účtu. Když je povolené, úložiště Data Lake store automaticky šifruje data před uložením a dešifruje před načtení, což transparentní klientovi přístupu k datům. Další informace najdete v tématu [zabezpečení v Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage a Azure Table Storage
Úložiště Azure Blob Storage a Azure Table podporuje šifrování služby úložiště (SSE), který automaticky šifruje vaše data před uložením do úložiště a dešifruje před načtení. Další informace najdete v tématu [šifrování služby úložiště Azure pro Data v klidovém stavu](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 podporuje klient i server šifrování dat v klidovém stavu. Další informace najdete v tématu [ochranu šifrování dat pomocí](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Objekt pro vytváření dat v současné době nepodporuje Amazon S3 uvnitř virtuální privátní cloud (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift podporuje šifrování clusteru pro neaktivní uložená data. Další informace najdete v tématu [šifrování databáze Redshift Amazon](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Objekt pro vytváření dat v současné době nepodporuje Amazon Redshift uvnitř VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce podporuje šifrování štítu platformy, která umožňuje šifrování všech souborů, přílohy, vlastní pole. Další informace najdete v tématu [Princip toku webového serveru pro ověřování OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybridní scénáře (s použitím brány pro správu dat)
Hybridní scénáře vyžadují Brána pro správu dat k instalaci v místní síti nebo uvnitř virtuální sítě (Azure) nebo virtuální privátní cloud (Amazon). Brány musí být schopen získat přístup k místní datová úložiště. Další informace o bráně najdete v tématu [Brána pro správu dat](data-factory-data-management-gateway.md). 

![Datové kanály Brána pro správu](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Příkaz kanál** umožňuje komunikaci mezi služby pro přesun dat v datové továrně a Data Management Gateway. Komunikace obsahuje informace související s aktivity. Datový kanál se používá pro přenos dat mezi místním úložištím dat a úložiště dat v cloudu.    

### <a name="on-premises-data-store-credentials"></a>Místní přihlašovací údaje k úložišti dat
Přihlašovací údaje pro vaše místní úložiště dat jsou uloženy místně (není v cloudu). Může být nastavena v třemi různými způsoby. 

- Pomocí **prostého textu** (méně bezpečné) přes HTTPS z portálu Azure nebo Průvodce kopírováním. Přihlašovací údaje jsou ve formátu prostého textu předaný místní brána.
- Pomocí **knihovny JavaScript kryptografie z Průvodce kopírováním**.
- Pomocí **klikněte na tlačítko-po aplikaci přihlašovací údaje správce na základě**. Kliknutím na možnost-Jakmile se aplikace spustí na místním počítači, který má přístup k bráně a nastaví pověření pro úložiště dat. Tuto možnost a dalším jsou nejbezpečnější možnosti. Aplikace Správce přihlašovacích údajů: ve výchozím nastavení, používá port 8050 na počítači s bránou pro zabezpečenou komunikaci.  
- Použití [New-AzureRmDataFactoryEncryptValue](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) rutiny prostředí PowerShell k šifrování přihlašovacích údajů. Rutina používá certifikát tuto bránu Pokud chcete použít k šifrování přihlašovacích údajů. Můžete použít zašifrované přihlašovací údaje vrácená touto rutinou a přidat jej do **EncryptedCredential** element **connectionString** v souboru JSON, který používáte s [ Nový-AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) rutiny nebo v tomto fragmentu kódu JSON v editoru služby Data Factory na portálu. Tato možnost a kliknutím na možnost-Jakmile nejbezpečnější možnosti, jsou aplikace. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Šifrování na základě knihovny JavaScript kryptografie
Můžete šifrovat přihlašovací údaje k úložišti dat pomocí [knihovny JavaScript kryptografie](https://www.microsoft.com/download/details.aspx?id=52439) z [Průvodce kopírováním](data-factory-copy-wizard.md). Když vyberete tuto možnost, Průvodce kopírováním načte veřejný klíč brány a použije ho k šifrování přihlašovacích údajů úložiště dat. Přihlašovací údaje jsou dešifrovat počítače brány a chráněn systémem Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Podporované prohlížeče:** IE8, Internet Explorer 9, IE10, IE11, Microsoft Edge a nejnovější Firefox, Chrome, Opera prohlížeče Safari. 

#### <a name="click-once-credentials-manager-app"></a>Klikněte na tlačítko-jednou aplikaci přihlašovací údaje správce
Můžete spustit kliknutím na možnost-po na základě přihlašovacích údajů správce aplikace z Azure portal nebo zkopírování Průvodce při vytváření kanálů. Tuto aplikaci zajistí, že se přihlašovací údaje nejsou přenášet ve formátu prostého textu prostřednictvím sítě. Ve výchozím nastavení, používá port **8050** na počítači s bránou pro zabezpečenou komunikaci. V případě potřeby můžete změnit tento port.  
  
![Port HTTPS pro brány](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Brána pro správu dat v současné době používá jeden **certifikát**. Tento certifikát je vytvořen během instalace brány (platí pro vytvoření po listopadu 2016 Brána pro správu dat a verze 2.4.xxxx.x nebo novější). Tento certifikát můžete nahradit vlastním certifikátem SSL/TLS. Tento certifikát je používán a klikněte na-jednou přihlašovacích údajů správce aplikace pro zabezpečené připojení k počítači s bránou pro nastavení přihlašovací údaje k úložišti dat. Ukládá data přihlašovací údaje k úložišti bezpečně místně pomocí Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) na počítači s bránou. 

> [!NOTE]
> Starší brány, které byly nainstalovány před listopadu 2016 nebo verze 2.3.xxxx.x dál používat přihlašovací údaje zašifrovány a uloženy v cloudu. I když provedete upgrade brány na nejnovější verzi, přihlašovací údaje nebyly migrovány do místního počítače    
  
| Verze brány (během vytváření) | Přihlašovací údaje uložené | Přihlašovací údaje, šifrování nebo zabezpečení | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | V cloudu | Šifrovat pomocí certifikátu (jiný než používají aplikaci správce přihlašovacích údajů) | 
| > = 2.4.xxxx.x | Místní | Zabezpečené pomocí rozhraní DPAPI | 
  

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Všechny přenosy dat jsou prostřednictvím zabezpečeného kanálu **HTTPS** a **TLS přes protokol TCP** aby se zabránilo útokům man-in-the-middle při komunikaci se službami Azure.
 
Můžete také použít [IPSec pro síť VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) nebo [Express Route](../../expressroute/expressroute-introduction.md) na další Zabezpečte komunikační kanál mezi místní sítí a Azure.

Virtuální síť se znázornění logické sítě v cloudu. Můžete připojit k místní síti do virtuální sítě Azure (VNet) pomocí nastavení sítě VPN IPSec (site-to-site) nebo Express Route (soukromý partnerský vztah)     

Následující tabulka shrnuje doporučené konfigurace sítě a brány založené na různé kombinace zdrojového a cílového umístění pro přesun dat hybridní.

| Zdroj | Cíl | Konfigurace sítě | Instalační program brány |
| ------ | ----------- | --------------------- | ------------- | 
| Lokálně | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | IPSec pro síť VPN (point-to-site nebo site-to-site) | Brány lze nainstalovat místně nebo v Azure virtuální počítač (VM) ve virtuální síti | 
| Lokálně | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | ExpressRoute (soukromý partnerský vztah) | Brána může být nainstalované místně nebo na virtuální počítač Azure ve virtuální síti | 
| Lokálně | Služeb založených na Azure, které mají veřejný koncový bod | ExpressRoute (veřejný partnerský vztah) | Brány musí být nainstalované na místě | 

Následující obrázky znázorňují použití brány pro správu dat pro přesun dat mezi místní databázi a službami Azure pomocí expressroute a VPN IPSec (s virtuální sítě):

**Express Route:**
 
![Použití Express Route s bránou](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec pro síť VPN:**

![IPSec pro síť VPN s bránou](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Konfigurace bran firewall a vytvoření seznamu povolených IP adresu brány

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Požadavky na brány firewall pro ve místní nebo soukromé síti  
V podniku **podniková brána firewall** běží na směrovači centrální organizace. A, **brány Windows firewall** spouští se jako démon na místním počítači, na kterém je brána nainstalovaná. 

Následující tabulka obsahuje **odchozí port** a požadavky domény **podniková brána firewall**.

| Názvy domén | Odchozí porty | Popis |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Požadované bránou pro připojení k služby pro přesun dat v datové továrně |
| `*.core.windows.net` | 443 | Používá bránu pro připojení k účtu úložiště Azure při použití [připravený kopie](data-factory-copy-activity-performance.md#staged-copy) funkce. | 
| `*.frontend.clouddatahub.net` | 443 | Požadované bránou pro připojení ke službě Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (Volitelné) je nutné zadat cíl je Azure SQL Database / Azure SQL datového skladu. Použijte funkci dvoufázové instalace kopírování zkopírovat data do Azure SQL Database nebo Azure SQL Data Warehouse bez otevření portu 1433. | 
| `*.azuredatalakestore.net` | 443 | (Volitelné) je nutné zadat cíl je Azure Data Lake store | 

> [!NOTE] 
> Možná budete muset spravovat porty / vytvoření seznamu povolených domén v podnikové brány firewall na úrovni podle potřeby podle příslušné datové zdroje. Tato tabulka pouze používá databázi SQL Azure, Azure SQL Data Warehouse, Azure Data Lake Store jako příklady.   

Následující tabulka obsahuje **příchozí port** požadavky **brány windows firewall**.

| Příchozí porty | Popis | 
| ------------- | ----------- | 
| 8050 (TCP) | Vyžadují aplikace Správce přihlašovacích údajů a bezpečně nastavte přihlašovací údaje pro místní úložiště dat v bráně. | 

![Požadavky na porty brány](media\data-factory-data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Konfigurace protokolu IP / vytvoření seznamu povolených v datech úložiště
Některé datové úložiště v cloudu i vyžadovat povolených IP adresu počítače k nim přistupovat. Ujistěte se, že je seznam povolených adres IP adresu počítače brány, a správně nakonfigurována v bráně firewall.

Následující cloudové úložiště dat vyžaduje vytvoření seznamu povolených IP adresu počítače brány. Některé z těchto dat úložiště, ve výchozím nastavení, ale nemusí vyžadovat vytvoření seznamu povolených IP adresy. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** můžete bránu sdílet mezi různé datové továrny?
**Odpověď:** tato funkce ještě nepodporujeme. Aktivně pracujeme na něm.

**Otázka:** jaké jsou požadavky na port pro bránu pro práci?
**Odpověď:** brána slouží k propojení založené na protokolu HTTP otevřete internet. **Odchozí porty 443 a 80** musí být otevřen pro bránu pro toto připojení. Otevřete **příchozí Port 8050** pouze na úrovni počítače (ne na úrovni podniková brána firewall) pro aplikaci správce přihlašovacích údajů. Pokud se databáze SQL Azure nebo Azure SQL Data Warehouse používá jako zdroj / cíl a pak muset otevřít **1433** také portu. Další informace najdete v tématu [konfigurace a vytvoření seznamu povolených IP adresy brány Firewall](#firewall-configurations-and-whitelisting-ip-address-of gateway) části. 

**Otázka:** jaké jsou požadavky na certifikát pro bránu?
**Odpověď:** certifikát, který se používá v aplikaci správce přihlašovacích údajů k bezpečně nastavení přihlašovací údaje k úložišti dat vyžaduje aktuální brány. Tento certifikát je certifikát podepsaný svým držitelem, vytvořit a nakonfigurovat nastavení brány. Můžete použít vlastní protokol TLS / SSL certifikát místo. Další informace najdete v tématu [klikněte na tlačítko-jednou přihlašovacích údajů správce aplikací](#click-once-credentials-manager-app) části. 

## <a name="next-steps"></a>Další postup
Informace o výkonu aktivitě kopírování najdete v tématu [zkopírujte aktivity výkonu a vyladění průvodce](data-factory-copy-activity-performance.md).

 
