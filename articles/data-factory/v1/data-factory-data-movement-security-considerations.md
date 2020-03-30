---
title: Důležité informace o zabezpečení pro přesun dat v Azure Data Factory
description: Další informace o zabezpečení pohybu dat v Azure Data Factory.
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
ms.openlocfilehash: 1f19d258531e5368238cba72c986aede3f4a64ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130842"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – důležité informace o zabezpečení pro přesun dat

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si informace o zabezpečení přesunu dat pro data factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Úvod
Tento článek popisuje základní infrastrukturu zabezpečení, kterou služby přesunu dat v Azure Data Factory používají k zabezpečení vašich dat. Prostředky správy Azure Data Factory jsou postavené na infrastruktuře zabezpečení Azure a využívají všechna možná bezpečnostní opatření nabízená Azure.

V řešení Data Factory vytváříte jeden nebo více datových [kanálů](data-factory-create-pipelines.md). Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Tyto kanály jsou umístěny v oblasti, kde byla vytvořena datová továrna. 

I když je data factory k dispozici pouze v **oblastech – západ USA**, Východní **USA**a **Severní Evropa,** služba přesunu dat je [dostupná globálně v několika oblastech](data-factory-data-movement-activities.md#global). Služba Data Factory zajišťuje, že data neopustí geografickou oblast nebo oblast, pokud explicitně nedáte službě pokyn, aby používala alternativní oblast, pokud služba přesunu dat ještě není nasazena do této oblasti. 

Azure Data Factory sama o sobě neukládá žádná data s výjimkou pověření propojené služby pro úložiště cloudových dat, které jsou šifrované pomocí certifikátů. Umožňuje vytvářet pracovní postupy řízené daty pro orchestraci pohybu dat mezi [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a zpracováním dat pomocí [výpočetních služeb](data-factory-compute-linked-services.md) v jiných oblastech nebo v místním prostředí. Umožňuje také [sledovat a spravovat pracovní postupy](data-factory-monitor-manage-pipelines.md) pomocí programových mechanismů i mechanismů ui.

Přesun dat pomocí Azure Data Factory byl **certifikován** pro:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [ČSA HVĚZDA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Pokud vás zajímá dodržování předpisů Azure a způsob, jakým Azure zabezpečuje vlastní infrastrukturu, navštivte [Centrum zabezpečení Microsoft Uvěř .](https://microsoft.com/en-us/trustcenter/default.aspx) 

V tomto článku kontrolujeme aspekty zabezpečení v následujících dvou scénářích přesunu dat: 

- **Scénář cloudu**– v tomto scénáři zdroj i cíl jsou veřejně přístupné prostřednictvím internetu. Patří mezi ně služby spravovaného cloudového úložiště, jako je Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, Služby SaaS, jako jsou Salesforce, a webové protokoly, jako jsou FTP a OData. Kompletní seznam podporovaných zdrojů dat naleznete [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hybridní scénář**– v tomto scénáři je zdroj nebo cíl za bránou firewall nebo uvnitř místní podnikové sítě nebo úložiště dat je v privátní síti/ virtuální síti (nejčastěji zdroj) a není veřejně přístupné. Databázové servery hostované na virtuálních počítačích také spadají do tohoto scénáře.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloudové scénáře
### <a name="securing-data-store-credentials"></a>Zabezpečení přihlašovacích údajů úložiště dat
Azure Data Factory chrání vaše přihlašovací údaje úložiště dat jejich **šifrováním** pomocí **certifikátů spravovaných společností Microsoft**. Tyto certifikáty se střídají každé **dva roky** (což zahrnuje obnovení certifikátu a migraci přihlašovacích údajů). Tyto šifrované přihlašovací údaje se bezpečně ukládají v **Azure Storage spravovaném službami azure data factory management .** Další informace o zabezpečení Azure Storage najdete v článku [Přehled zabezpečení úložiště Azure](../../security/fundamentals/storage-overview.md).

### <a name="data-encryption-in-transit"></a>Šifrování dat při přenosu
Pokud úložiště cloudových dat podporuje protokol HTTPS nebo TLS, všechny přenosy dat mezi službami přesunu dat v Datové továrně a úložištěm cloudových dat se nacházejí prostřednictvím zabezpečeného kanálu HTTPS nebo TLS.

> [!NOTE]
> Všechna připojení k **Azure SQL Database** a Azure SQL Data **Warehouse** vždy vyžadují šifrování (SSL/TLS), zatímco data jsou na cestě do a z databáze. Při vytváření kanálu pomocí editoru JSON přidejte vlastnost **šifrování** a nastavte ji na **hodnotu true** v **připojovacím řetězci**. Při použití [Průvodce kopírováním](data-factory-azure-copy-wizard.md)nastaví průvodce tuto vlastnost ve výchozím nastavení. Pro **Azure Storage**můžete použít protokol **HTTPS** v připojovacím řetězci.

### <a name="data-encryption-at-rest"></a>Šifrování v klidovém stavu
Některá úložiště dat podporují šifrování dat v klidovém stavu. Doporučujeme povolit mechanismus šifrování dat pro tato úložiště dat. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparentní šifrování dat (TDE) v Azure SQL Data Warehouse pomáhá chránit před hrozbou škodlivé aktivity tím, že provádí šifrování v reálném čase a dešifrování vašich dat v klidovém stavu. Toto chování je transparentní pro klienta. Další informace naleznete [v tématu Zabezpečení databáze v databázi SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database také podporuje transparentní šifrování dat (TDE), který pomáhá s ochranou před hrozbou škodlivé aktivity prováděním šifrování v reálném čase a dešifrování dat bez nutnosti změny v aplikaci. Toto chování je transparentní pro klienta. Další informace najdete [v tématu Transparentní šifrování dat pomocí Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Úložiště Azure Data Lake také poskytuje šifrování dat uložených v účtu. Pokud je povoleno, úložiště Data Lake automaticky šifruje data před uchováním a dešifruje před načtením, takže je transparentní pro klienta přístup k datům. Další informace najdete [v tématu Zabezpečení v Úložišti datových jezer Azure](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage a Azure Table Storage
Azure Blob Storage a Azure Table storage podporuje šifrování služby Storage Service Encryption (SSE), které automaticky šifruje vaše data před uchováním do úložiště a dešifruje před načtením. Další informace najdete v tématu [Šifrování služby Úložiště Azure pro data v klidovém stavu](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 podporuje šifrování dat klientem i serverem v klidovém stavu. Další informace naleznete [v tématu Ochrana dat pomocí šifrování](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). V současné době Data Factory nepodporuje Amazon S3 uvnitř virtuálního privátního cloudu (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift podporuje šifrování clusteru pro data v klidovém stavu. Další informace naleznete v tématu [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). V současné době Data Factory nepodporuje Amazon Redshift uvnitř VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce podporuje shield platformové šifrování, které umožňuje šifrování všech souborů, příloh, vlastních polí. Další informace naleznete [v tématu Principy toku ověřování oautu webového serveru](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybridní scénáře (pomocí brány pro správu dat)
Hybridní scénáře vyžadují, aby se brána pro správu dat nainstalovala v místní síti nebo uvnitř virtuální sítě (Azure) nebo virtuálního privátního cloudu (Amazon). Brána musí mít přístup k místním úložištím dat. Další informace o bráně naleznete v tématu [Brána pro správu dat](data-factory-data-management-gateway.md). 

![Kanály brány pro správu dat](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Příkazový kanál** umožňuje komunikaci mezi službami přesunu dat v Datové továrně a bráně pro správu dat. Sdělení obsahuje informace týkající se aktivity. Datový kanál se používá pro přenos dat mezi místními úložišti dat a cloudovými úložišti dat.    

### <a name="on-premises-data-store-credentials"></a>Přihlašovací údaje úložiště místních dat
Přihlašovací údaje pro místní úložiště dat jsou uloženy místně (ne v cloudu). Mohou být nastaveny třemi různými způsoby. 

- Použití **prostého textu** (méně zabezpečené) prostřednictvím protokolu HTTPS z portálu Azure/ Průvodce kopírováním. Pověření jsou předávány ve formátu prostého textu místní bráně.
- Použití **knihovny kryptografie javascriptu z Průvodce kopírováním**.
- Použití **aplikace správce přihlašovacích údajů založené na kliknutí jednou**. Aplikace click-once se spustí v místním počítači, který má přístup k bráně a nastaví pověření pro úložiště dat. Tato možnost a další jsou nejbezpečnější možnosti. Aplikace správce přihlašovacích údajů ve výchozím nastavení používá port 8050 v počítači s bránou pro zabezpečenou komunikaci.  
- K šifrování přihlašovacích údajů použijte rutinu [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell. Rutina používá certifikát, který je brána nakonfigurována k šifrování pověření. Můžete použít šifrovaná pověření vrácená touto rutinou a přidat je do prvku **EncryptedCredential** **prvku connectionString** v souboru JSON, který používáte s rutinou [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) nebo v fragmentu JSON v editoru datových toků na portálu. Tato možnost a aplikace click-once jsou nejbezpečnější možnosti. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Šifrování založené na kryptografické knihovně JavaScriptu
Pověření úložiště dat můžete šifrovat pomocí [knihovny kryptografie javascriptu](https://www.microsoft.com/download/details.aspx?id=52439) z [Průvodce kopírováním](data-factory-copy-wizard.md). Když vyberete tuto možnost, Průvodce kopírováním načte veřejný klíč brány a použije jej k šifrování přihlašovacích údajů úložiště dat. Pověření jsou dešifrována počítačem brány a chráněna systémem Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Podporované prohlížeče:** IE8, IE9, IE10, IE11, Microsoft Edge a nejnovější prohlížeče Firefox, Chrome, Opera, Safari. 

#### <a name="click-once-credentials-manager-app"></a>Aplikace Správce přihlašovacích údajů pro klepnutí jednou
Při vytváření kanálů můžete spustit aplikaci správce přihlašovacích údajů založenou na kliknutí jednou z portálu Azure Portal/Copy Wizard. Tato aplikace zajišťuje, že pověření nejsou přeneseny ve formátu prostého textu po drátě. Ve výchozím nastavení používá port **8050** v počítači s bránou pro zabezpečenou komunikaci. V případě potřeby lze tento port změnit.  
  
![Port HTTPS pro bránu](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

V současné době používá brána pro správu dat jeden **certifikát**. Tento certifikát je vytvořen během instalace brány (platí pro bránu pro správu dat vytvořenou po listopadu 2016 a verzi 2.4.xxxx.x nebo novější). Tento certifikát můžete nahradit vlastním certifikátem SSL/TLS. Tento certifikát používá aplikace správce přihlašovacích údajů pro klepnutí jednou pro bezpečné připojení k počítači brány pro nastavení přihlašovacích údajů pro ukládání dat. Ukládá přihlašovací údaje úložiště dat bezpečně místně pomocí Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) v počítači s bránou. 

> [!NOTE]
> Starší brány, které byly nainstalovány před listopadem 2016 nebo verzí 2.3.xxxx.x, nadále používají přihlašovací údaje zašifrované a uložené v cloudu. I v případě, že upgradujete bránu na nejnovější verzi, pověření nejsou migrovány do místního počítače    
  
| Verze brány (během vytváření) | Uložená pověření | Šifrování přihlašovacích údajů / zabezpečení | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | Na cloudu | Šifrované pomocí certifikátu (odlišného od certifikátu používaného aplikací Správce přihlašovacích údajů) | 
| > = 2.4.xxxx.x | V areálu | Zabezpečeno přes DPAPI | 
  

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Všechny přenosy dat jsou přes zabezpečený kanál **HTTPS** a **TLS přes TCP,** aby se zabránilo útokům prostředníkem během komunikace se službami Azure.
 
Pomocí protokolu [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) nebo [Express Route](../../expressroute/expressroute-introduction.md) můžete také dále zabezpečit komunikační kanál mezi místní sítí a Azure.

Virtuální síť je logická reprezentace vaší sítě v cloudu. Místní síť můžete připojit k virtuální síti Azure (VNet) nastavením sítě IPSec VPN (site-to-site) nebo Express Route (Private Peering).     

Následující tabulka shrnuje doporučení konfigurace sítě a brány na základě různých kombinací zdrojových a cílových umístění pro hybridní přesun dat.

| Zdroj | Cíl | Konfigurace sítě | Nastavení brány |
| ------ | ----------- | --------------------- | ------------- | 
| Lokálně | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | IPSec VPN (point-to-site nebo site-to-site) | Brána se dá nainstalovat místně nebo na virtuálním počítači (VM) Azure ve virtuální síti. | 
| Lokálně | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | ExpressRoute (soukromý partnerský vztah) | Brána se dá nainstalovat místně nebo na virtuální ms Azure ve virtuální síti. | 
| Lokálně | Služby založené na Azure, které mají veřejný koncový bod | ExpressRoute (veřejný partnerský vztah) | Brána musí být nainstalovaná místně. | 

Následující obrázky ukazují využití brány pro správu dat pro přesun dat mezi místní databází a službami Azure pomocí expresní trasy a sítě IPSec VPN (s virtuální sítí):

**Expresní trasa:**
 
![Použití expresní trasy s bránou](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN s bránou](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Konfigurace brány firewall a ip adresa brány na seznamu povolených

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Požadavky brány firewall pro místní/privátní síť  
V podniku běží **podniková brána firewall** na centrálním směrovači organizace. Brána **firewall systému Windows** je spuštěna jako daemon v místním počítači, ve kterém je brána nainstalována. 

V následující tabulce jsou uvedeny požadavky na **odchozí port** a doménu podnikové **brány firewall**.

| Názvy domén | Odchozí porty | Popis |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Vyžadované bránou pro připojení ke službám přesunu dat v datové továrně |
| `*.core.windows.net` | 443 | Používá se brána pro připojení k účtu úložiště Azure při použití funkce [fázované kopie.](data-factory-copy-activity-performance.md#staged-copy) | 
| `*.frontend.clouddatahub.net` | 443 | Vyžadované bránou pro připojení ke službě Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (VOLITELNÉ) potřebné, když je vaším cílem Azure SQL Database / Azure SQL Data Warehouse. Pomocí funkce fázované kopírování zkopírujte data do Azure SQL Database/Azure SQL Data Warehouse bez otevření portu 1433. | 
| `*.azuredatalakestore.net` | 443 | (VOLITELNÉ) potřebné, když je vaším cílem úložiště Azure Data Lake | 

> [!NOTE] 
> Možná budete muset spravovat porty / domény whitelisting na úrovni podnikové brány firewall podle požadavků příslušných zdrojů dat. Tato tabulka používá jenom Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store jako příklady.   

V následující tabulce jsou uvedeny požadavky na **příchozí port** brány **firewall systému Windows**.

| Příchozí porty | Popis | 
| ------------- | ----------- | 
| 8050 (TCP) | Aplikace správce pověření vyžaduje, aby bezpečně nastavila přihlašovací údaje pro místní úložiště dat v bráně. | 

![Požadavky na port brány](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Konfigurace IP/ whitelisting v úložišti dat
Některá úložiště dat v cloudu také vyžadují zařazení ip adresy počítače, která k nim přistupuje, na seznamu povolených adres. Ujistěte se, že IP adresa počítače brány je na seznamu povolených nebo nakonfigurovaných v bráně firewall odpovídajícím způsobem.

Následující cloudová data vyžadují whitelisting IP adresy počítače brány. Některá z těchto úložišť dat ve výchozím nastavení nemusí vyžadovat zařazení na seznam IP adres na seznamu povolených adres. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** Lze bránu sdílet v různých datových továrnách?
**Odpověď:** Tuto funkci zatím nepodporujeme. Aktivně na ní pracujeme.

**Otázka:** Jaké jsou požadavky na port pro bránu do práce?
**Odpověď:** Gateway umožňuje připojení založené na protokolu HTTP k otevření internetu. Chcete-li, aby brána vytvořila toto připojení, musí být **otevřeny odchozí porty 443 a 80.** Otevřete **příchozí port 8050** pouze na úrovni počítače (nikoli na úrovni podnikové brány firewall) pro aplikaci Správce přihlašovacích údajů. Pokud azure SQL database nebo Azure SQL Data Warehouse se používá jako zdroj/ cíl, pak je potřeba otevřít také port **1433.** Další informace naleznete v [části Konfigurace brány firewall a seznam adres IP seznamu povolených](#firewall-configurations-and-whitelisting-ip-address-of gateway) adres. 

**Otázka:** Jaké jsou požadavky na certifikát pro bránu?
**Odpověď:** Aktuální brána vyžaduje certifikát, který používá aplikace správce přihlašovacích údajů pro bezpečné nastavení přihlašovacích údajů úložiště dat. Tento certifikát je certifikát podepsaný svým držitelem vytvořený a nakonfigurovaný instalačním programem brány. Místo toho můžete použít vlastní certifikát TLS/SSL. Další informace naleznete v části aplikace [správce přihlašovacích údajů pro klepnutí jednou.](#click-once-credentials-manager-app) 

## <a name="next-steps"></a>Další kroky
Informace o výkonu aktivity kopírování naleznete v tématu [Kopírování výkonu aktivity a průvodce laděním](data-factory-copy-activity-performance.md).

 
