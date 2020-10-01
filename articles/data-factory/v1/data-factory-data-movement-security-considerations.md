---
title: Otázky zabezpečení při přesunu dat v Azure Data Factory
description: Přečtěte si informace o zabezpečení přesunu dat v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 9ae4970383802adad755fff4a6ce382db6ce32fe
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619912"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – požadavky na zabezpečení při přesunu dat

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [informace o zabezpečení přesunu dat pro Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Úvod
Tento článek popisuje základní infrastrukturu zabezpečení, kterou služby pro přesun dat v Azure Data Factory používají k zabezpečení vašich dat. Prostředky správy Azure Data Factory jsou postavené na infrastruktuře zabezpečení Azure a využívají všechny možné bezpečnostní míry, které nabízí Azure.

V řešení Data Factory vytváříte jeden nebo více datových [kanálů](data-factory-create-pipelines.md). Kanál je logické seskupení aktivit, které společně provádějí úlohu. Tyto kanály se nacházejí v oblasti, ve které byl vytvořen objekt pro vytváření dat. 

I když je Data Factory k dispozici pouze v oblastech **západní USA**, **východní USA**a **Severní Evropa** , služba přesunu dat je k dispozici [globálně v několika oblastech](data-factory-data-movement-activities.md#global). Služba Data Factory zajišťuje, aby data nezůstala geografická oblast nebo oblast, pokud explicitně neurčíte, aby služba používala alternativní oblast, pokud se služba pro přesun dat ještě v této oblasti nenasadit. 

Azure Data Factory sám o sobě neukládá žádná data s výjimkou přihlašovacích údajů propojených služeb pro cloudová úložiště dat, která jsou zašifrovaná pomocí certifikátů. Umožňuje vytvářet pracovní postupy řízené daty k orchestraci přesouvání dat mezi [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a zpracování dat pomocí [výpočetních služeb](data-factory-compute-linked-services.md) v jiných oblastech nebo v místním prostředí. Také vám umožňuje [monitorovat a spravovat pracovní postupy](data-factory-monitor-manage-pipelines.md) pomocí mechanismů programování a uživatelského rozhraní.

Pro přesun dat pomocí Azure Data Factory bylo **Certifikováno** pro:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Pokud vás zajímá dodržování předpisů Azure a způsob, jakým Azure zabezpečuje svou vlastní infrastrukturu, přejděte na web [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

V tomto článku prozkoumáme bezpečnostní opatření v následujících dvou scénářích přesunu dat: 

- **Scénář cloudu**– v tomto scénáři jsou váš zdroj i cíl veřejně přístupný prostřednictvím Internetu. Mezi ně patří spravované služby cloudového úložiště, jako je Azure Storage, Azure synapse Analytics (dřív SQL Data Warehouse), Azure SQL Database Azure Data Lake Store, Amazon S3, Amazon RedShift, SaaS Services, jako je například Salesforce, a webové protokoly, jako jsou FTP a OData. Úplný seznam podporovaných zdrojů dat najdete [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hybridní scénář**– v tomto scénáři je buď zdroj nebo cíl za bránou firewall nebo místní podnikovou sítí, nebo úložiště dat v privátní síti nebo virtuální síti (nejčastěji zdroj) a není veřejně přístupný. V tomto scénáři spadají i databázové servery hostované na virtuálních počítačích.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloudové scénáře
### <a name="securing-data-store-credentials"></a>Zabezpečení přihlašovacích údajů úložiště dat
Azure Data Factory chrání přihlašovací údaje úložiště dat jejich **šifrováním** pomocí **certifikátů spravovaných Microsoftem**. Tyto certifikáty se otočí každé **dva roky** (včetně obnovení certifikátu a migrace přihlašovacích údajů). Tyto šifrované přihlašovací údaje jsou bezpečně uložené v **Azure Storage spravovaném pomocí služby Azure Data Factory Management**. Další informace o zabezpečení Azure Storage najdete v tématu [Přehled zabezpečení Azure Storage](../../security/fundamentals/storage-overview.md).

### <a name="data-encryption-in-transit"></a>Šifrování dat při přenosu
Pokud cloudové úložiště dat podporuje protokol HTTPS nebo TLS, všechna přenosová data mezi službami přesunu dat v Data Factory a cloudovým úložištěm dat jsou prostřednictvím zabezpečeného kanálu HTTPS nebo TLS.

> [!NOTE]
> Všechna připojení k **Azure SQL Database** a **Azure synapse Analytics** vždy vyžadují šifrování (SSL/TLS), zatímco data jsou přenášena do a z databáze. Při vytváření kanálu pomocí editoru JSON přidejte vlastnost **šifrování** a nastavte ji na **true** v **připojovacím řetězci**. Když použijete [Průvodce kopírováním](data-factory-azure-copy-wizard.md), průvodce tuto vlastnost nastaví ve výchozím nastavení. V případě **Azure Storage**můžete v připojovacím řetězci použít **https** .

### <a name="data-encryption-at-rest"></a>Šifrování v klidovém stavu
Některá úložiště dat podporují šifrování neaktivních uložených dat. Doporučujeme pro tato úložiště dat Povolit mechanismus šifrování dat. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Transparentní šifrování dat (TDE) ve službě Azure synapse Analytics pomáhá chránit před hrozbou škodlivých aktivit tím, že provádí šifrování v reálném čase a dešifrování vašich dat v klidovém čase. Toto chování je pro klienta transparentní. Další informace najdete v tématu [zabezpečení databáze v synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database také podporuje transparentní šifrování dat (TDE), které pomáhá chránit před hrozbou škodlivých aktivit pomocí šifrování a dešifrování dat v reálném čase, aniž by to vyžadovalo změny aplikace. Toto chování je pro klienta transparentní. Další informace najdete v tématu [transparentní šifrování dat s Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store taky poskytuje šifrování pro data uložená v účtu. Pokud je tato možnost povolená, Data Lake Store automaticky šifruje data před tím, než je zachová a dešifruje před jejich načítáním, takže je transparentní pro klienta přistupující k datům. Další informace najdete v tématu [zabezpečení v Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage a Azure Table Storage
Azure Blob Storage a Azure Table Storage podporují Šifrování služby Storage (SSE), které automaticky šifrují vaše data před tím, než se uloží do úložiště a dešifruje před načtením. Další informace najdete v tématu [šifrování služby Azure Storage pro](../../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 podporuje šifrování dat v klidovém stavu klienta i serveru. Další informace najdete v tématu [Ochrana dat pomocí šifrování](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). V současné době Data Factory nepodporuje službu Amazon S3 ve virtuálním privátním cloudu (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon RedShift podporuje šifrování clusteru pro neaktivní neaktivní data. Další informace najdete v tématu [šifrování databáze Amazon RedShift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). V současné době Data Factory nepodporuje Amazon RedShift uvnitř VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce podporuje šifrování platformy stínění, které umožňuje šifrování všech souborů, příloh a vlastních polí. Další informace najdete v tématu [Principy toku ověřování webového serveru OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybridní scénáře (použití brány Správa dat)
Hybridní scénáře vyžadují, aby byla Správa dat brána nainstalovaná v místní síti nebo ve virtuální síti (Azure) nebo ve virtuálním privátním cloudu (Amazon). Brána musí být schopna získat přístup k místním úložištím dat. Další informace o bráně najdete v tématu [Správa dat Gateway](data-factory-data-management-gateway.md). 

![Kanály Správa dat brány](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Kanál příkazů** umožňuje komunikaci mezi službami přesunu dat v Data Factory a správa dat bránou. Tato komunikace obsahuje informace související s aktivitou. Datový kanál se používá k přenosu dat mezi místními úložišti dat a cloudových úložišť dat.    

### <a name="on-premises-data-store-credentials"></a>Přihlašovací údaje místního úložiště dat
Přihlašovací údaje pro místní úložiště dat se ukládají místně (ne v cloudu). Lze je nastavit třemi různými způsoby. 

- Použití **prostého textu** (méně bezpečného) prostřednictvím protokolu HTTPS z webu Azure Portal nebo Průvodce kopírováním. Přihlašovací údaje se předávají do místní brány v prostém textu.
- Použití **knihovny kryptografie jazyka JavaScript z Průvodce kopírováním**.
- Použití **aplikace Správce přihlašovacích údajů založeného na programu Click** Aplikace kliknutí na jednou se spouští na místním počítači, který má přístup k bráně a nastavuje přihlašovací údaje pro úložiště dat. Tato možnost a další jsou nejbezpečnější možnosti. Ve výchozím nastavení používá aplikace Správce přihlašovacích údajů port 8050 na počítači s bránou pro zabezpečenou komunikaci.  
- K šifrování přihlašovacích údajů použijte rutinu [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) prostředí PowerShell. Rutina používá certifikát, který je nakonfigurován pro použití pro šifrování přihlašovacích údajů. Můžete použít šifrované přihlašovací údaje vrácené touto rutinou a přidat ji do **EncryptedCredential** elementu **CONNECTIONSTRING** v souboru JSON, který použijete pomocí rutiny [New-AZDATAFACTORYLINKEDSERVICE](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) nebo ve fragmentu JSON v editoru Data Factory na portálu. Tato možnost a aplikace jedním kliknutím jsou nejbezpečnější možnosti. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Šifrování založené na knihovně JavaScript Cryptography
Přihlašovací údaje úložiště dat můžete šifrovat pomocí [knihovny kryptografie JavaScriptu](https://www.microsoft.com/download/details.aspx?id=52439) z [Průvodce kopírováním](data-factory-copy-wizard.md). Když vyberete tuto možnost, Průvodce kopírováním načte veřejný klíč brány a použije ho k zašifrování přihlašovacích údajů úložiště dat. Přihlašovací údaje jsou dešifrovány počítačem brány a chráněny rozhraním [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx)systému Windows.

**Podporované prohlížeče:** IE8, IE9, IE10, IE11, Microsoft Edge a nejnovější prohlížeče Firefox, Chrome, Opera, Safari. 

#### <a name="click-once-credentials-manager-app"></a>Aplikace Správce přihlašovacích údajů po kliknutí
Při vytváření kanálů můžete spustit aplikaci Správce přihlašovacích údajů založenou na jediném kliknutí z Průvodce Azure Portal/kopírovat. Tato aplikace zajišťuje, aby se přihlašovací údaje přenesly přes tento kabel do prostého textu. Ve výchozím nastavení používá port **8050** na počítači s bránou pro zabezpečenou komunikaci. V případě potřeby můžete tento port změnit.  
  
![Port HTTPS pro bránu](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

V současné době Správa dat brána používá jeden **certifikát**. Tento certifikát se vytvoří během instalace brány (týká se Správa dat brány vytvořené po 2016. listopadu a verze 2.4. xxxx. x nebo novější). Tento certifikát můžete nahradit vlastním certifikátem SSL/TLS. Tento certifikát používá aplikace Správce přihlašovacích údajů pro kliknutí, která slouží k zabezpečenému připojení k počítači brány pro nastavení přihlašovacích údajů úložiště dat. Přihlašovací údaje úložiště dat se ukládají bezpečně místně pomocí rozhraní Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) na počítači s bránou. 

> [!NOTE]
> Starší brány nainstalované před vydáním listopadu 2016 nebo verze 2.3. xxxx. x nadále používají šifrované přihlašovací údaje a ukládají se do cloudu. I když bránu upgradujete na nejnovější verzi, přihlašovací údaje se nemigrují na místní počítač.    
  
| Verze brány (během vytváření) | Uložené přihlašovací údaje | Šifrování/zabezpečení přihlašovacích údajů | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3. xxxx. x | V cloudu | Šifrování pomocí certifikátu (liší se od verze používané aplikací Správce přihlašovacích údajů) | 
| > = 2.4. xxxx. x | Místně | Zabezpečené přes DPAPI | 
  

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Všechny přenosy dat jsou prostřednictvím zabezpečeného kanálu **https** a **TLS přes protokol TCP** , aby se zabránilo útokům prostředníkem při komunikaci se službami Azure.
 
K dalšímu zabezpečení komunikačního kanálu mezi vaší místní sítí a Azure můžete použít taky službu [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) nebo [Express Route](../../expressroute/expressroute-introduction.md) .

Virtuální síť je logická reprezentace vaší sítě v cloudu. Můžete připojit místní síť ke službě Azure Virtual Network (VNet) nastavením IPSec VPN (site-to-site) nebo Express Route (privátní partnerské vztahy).     

Následující tabulka shrnuje doporučení konfigurace sítě a brány na základě různých kombinací zdrojového a cílového umístění pro pohyb hybridních dat.

| Zdroj | Cíl | Konfigurace sítě | Nastavení brány |
| ------ | ----------- | --------------------- | ------------- | 
| Místní | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | IPSec VPN (Point-to-site nebo site-to-site) | Bránu můžete nainstalovat buď místně, nebo na virtuální počítač Azure (VM) ve virtuální síti. | 
| Místní | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | ExpressRoute (soukromý partnerský vztah) | Bránu můžete nainstalovat buď místně, nebo na virtuálním počítači Azure ve virtuální síti. | 
| Místní | Služby založené na Azure s veřejným koncovým bodem | ExpressRoute (veřejný partnerský vztah) | Brána musí být nainstalovaná místně. | 

Následující obrázky ukazují použití Správa dat brány pro přesun dat mezi místní databází a službami Azure pomocí Express Route a IPSec VPN (s Virtual Network):

**Expresní trasa:**
 
![Použití expresní trasy s bránou](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN s bránou](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-filtering-ip-address-of-gateway"></a>Konfigurace brány firewall a filtrování IP adresy brány

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Požadavky na bránu firewall pro místní nebo privátní síť  
V podniku je **podniková brána firewall** provozována v centrálním směrovači organizace. A **Brána Windows Firewall** běží jako démon na místním počítači, na kterém je brána nainstalovaná. 

Následující tabulka obsahuje požadavky na **Odchozí porty** a domény pro **podnikovou bránu firewall**.

| Názvy domén | Odchozí porty | Popis |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Vyžadovaná bránou pro připojení k pohybovým službám dat v Data Factory |
| `*.core.windows.net` | 443 | Používá se bránou k připojení k Azure Storage účtu při použití funkce [dvoufázové kopírování](data-factory-copy-activity-performance.md#staged-copy) . | 
| `*.frontend.clouddatahub.net` | 443 | Požadovaná bránou pro připojení ke službě Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (Volitelné) nutné, když je vaším cílem Azure SQL Database nebo Azure synapse Analytics. Pomocí funkce dvoufázové kopírování můžete kopírovat data do Azure SQL Database nebo Azure synapse Analytics bez otevření portu 1433. | 
| `*.azuredatalakestore.net` | 443 | (Volitelné) nutné, když je cíl Azure Data Lake Store | 

> [!NOTE] 
> Možná budete muset spravovat porty a domény filtrování na úrovni brány firewall společnosti podle požadavků příslušných zdrojů dat. Tato tabulka používá v příkladech jenom Azure SQL Database, Azure synapse Analytics Azure Data Lake Store.   

Následující tabulka uvádí požadavky na **porty** pro **bránu Windows Firewall**.

| Příchozí porty | Popis | 
| ------------- | ----------- | 
| 8050 (TCP) | Vyžaduje aplikaci Správce přihlašovacích údajů k bezpečnému nastavení přihlašovacích údajů pro místní úložiště dat v bráně. | 

![Požadavky na port brány](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurationsfiltering-in-data-store"></a>Konfigurace a filtrování protokolu IP v úložišti dat
Některá úložiště dat v cloudu také vyžadují schválení IP adresy počítače, ke kterému přistupuje. Ujistěte se, že IP adresa počítače brány je schválená nebo nakonfigurovaná v bráně firewall správně.

Následující cloudová úložiště dat vyžadují schválení IP adresy počítače brány. Některá z těchto úložišť dat ve výchozím nastavení nemusí vyžadovat schválení IP adresy. 

- [Azure SQL Database](../../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** Je možné bránu sdílet napříč různými datovými továrnami?
**Odpověď:** Tuto funkci zatím nepodporujeme. Aktivně na ní pracujeme.

**Otázka:** Jaké jsou požadavky na porty, které brána funguje?
**Odpověď:** Brána umožňuje připojení založená na protokolu HTTP k otevření Internetu. Aby brána mohla toto připojení vytvořit, musí se pro bránu otevřít **Odchozí porty 443 a 80** . Pro aplikaci Správce přihlašovacích údajů otevřete **příchozí Port 8050** jenom na úrovni počítače (ne na úrovni brány firewall pro firmy). Pokud se Azure SQL Database nebo Azure synapse Analytics používá jako zdroj nebo cíl, musíte taky otevřít port **1433** . Další informace najdete v části [Konfigurace brány firewall a filtrování IP adres](#firewall-configurations-and-filtering-ip-address-of gateway) . 

**Otázka:** Jaké jsou požadavky na certifikáty pro bránu?
**Odpověď:** Aktuální brána vyžaduje certifikát, který aplikace Správce přihlašovacích údajů používá pro bezpečné nastavení přihlašovacích údajů úložiště dat. Tento certifikát je certifikát podepsaný svým držitelem vytvořeného a nakonfigurovaného nastavením brány. Místo toho můžete použít vlastní certifikát TLS/SSL. Další informace najdete v části [aplikace Správce přihlašovacích údajů](#click-once-credentials-manager-app) . 

## <a name="next-steps"></a>Další kroky
Informace o výkonu aktivity kopírování najdete v tématu [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md).
