---
title: Aspekty zabezpečení v Azure Data Factory | Microsoft Docs
description: Popisuje základní zabezpečení infrastrukturu, která využívají služby pro přesun dat v Azure Data Factory k zabezpečení vaše data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: 5d9061e12ac9fe0b9d858690897e582acab5169e
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754448"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Důležité informace o zabezpečení pro přesun dat v Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-data-movement-security-considerations.md)
> * [Verze 2 – Preview](data-movement-security-considerations.md)

Tento článek popisuje základní zabezpečení infrastrukturu, která pomocí služby pro přesun dat v Azure Data Factory pomáhají zabezpečit vaše data. Data Factory správu prostředky jsou postaveny na infrastrukturu zabezpečení Azure a použít všechny možné bezpečnostní opatření, které nabízí Azure.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aspekty zabezpečení přesunu dat pro datovou továrnu verze 1](v1/data-factory-data-movement-security-considerations.md).

V řešení Data Factory vytváříte jeden nebo více datových [kanálů](concepts-pipelines-activities.md). Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Tyto kanály nacházet v oblasti, kde byl vytvořen služby data factory. 

I když Data Factory je dostupná jenom v několika oblastech, služba pro přesun dat je [k dispozici globálně](concepts-integration-runtime.md#integration-runtime-location) zajistit dodržování předpisů data efektivitu a snížené sítě výstupních náklady. 

Azure Data Factory neukládá všechna data s výjimkou pověření propojené služby pro cloudové úložiště dat, které jsou zašifrované pomocí certifikátů. Pomocí služby Data Factory vytvořit pracovních řízené daty k orchestraci přesouvání dat mezi [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats)a zpracování dat pomocí [výpočetní služby](compute-linked-services.md) v jiných oblastech nebo v v místním prostředí. Můžete také sledovat a spravovat workflowy pomocí sady SDK a monitorování Azure.

Přesun dat pomocí služby Data Factory certifikovala pro:
-   [HIPAA NEBO HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA HVĚZDIČKOU](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Pokud vás zajímá Azure dodržování předpisů a jak Azure zabezpečuje svou vlastní infrastrukturu, navštivte [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

V tomto článku jsme zkontrolujte důležité informace o zabezpečení v následujících scénářích přesun dvě dat: 

- **Scénář cloudu**: V tomto scénáři jsou veřejně přístupná prostřednictvím Internetu svůj zdroj a cíl. Mezi ně patří služby spravované cloudového úložiště, jako je například Azure Storage, Azure SQL Data Warehouse, databáze SQL Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, služby SaaS, jako je například služby Salesforce a webové protokoly například protokoly FTP a OData. Najít úplný seznam podporovaných zdrojů dat v [podporované úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
- **Scénář hybridního**: V tomto scénáři vaše zdroj nebo cíl jsou za bránou firewall nebo uvnitř podnikové sítě na místní. Nebo úložiště dat je v soukromé síti nebo virtuální sítě (nejčastěji zdroje) a není veřejně přístupná. Databázové servery hostované ve virtuálních počítačích také spadají pod tento scénář.

## <a name="cloud-scenarios"></a>Scénáře cloudu

### <a name="securing-data-store-credentials"></a>Zabezpečení údaje k úložišti dat.

- **Uložení zašifrované přihlašovací údaje v úložišti Azure Data Factory spravované**. Objekt pro vytváření dat pomáhá chránit přihlašovací údaje k úložišti dat šifrováním s certifikáty, které spravuje Microsoft. Tyto certifikáty otáčejí každé dva roky (které zahrnuje obnovení certifikátu a migrace přihlašovacích údajů). Zašifrované přihlašovací údaje jsou bezpečně uložené v účtu úložiště Azure, který spravuje služby pro Azure Data Factory. Další informace o zabezpečení Azure Storage najdete v tématu [Přehled zabezpečení Azure Storage](../security/security-storage-overview.md).
- **Ukládat přihlašovací údaje v Azure Key Vault**. Také můžete uložit přihlašovací údaje úložiště dat v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Objekt pro vytváření dat načte přihlašovacích údajů během provádění aktivity. Další informace najdete v tématu [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Šifrování dat při přenosu
Pokud cloudové úložiště dat podporuje protokol HTTPS nebo TLS, všech dat přenesených mezi služby pro přesun dat v datové továrně a cloudové úložiště dat jsou prostřednictvím zabezpečeného kanálu HTTPS nebo TLS.

> [!NOTE]
> Všechna připojení k Azure SQL Database a Azure SQL Data Warehouse vyžadovat šifrování (SSL/TLS) dat při přenosu do a z databáze. Pokud jste vytváření kanálu pomocí JSON, přidejte vlastnost šifrování a nastavte ji na **true** v připojovacím řetězci. Pro úložiště Azure, můžete použít **HTTPS** v připojovacím řetězci.

### <a name="data-encryption-at-rest"></a>Šifrování v klidovém stavu
Některá data ukládá podpora šifrování dat v klidovém stavu. Doporučujeme, abyste povolili mechanismus šifrování dat pro tyto datové úložiště. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparentní dat šifrování (TDE) v Azure SQL Data Warehouse pomáhá chránit před ohrožením škodlivých aktivit provedením v reálném čase šifrování a dešifrování dat v klidovém stavu. Toto chování je pro klienta transparentní. Další informace najdete v tématu [zabezpečení databáze v SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database podporuje také transparentní šifrování dat (šifrování TDE), která pomáhá chránit před ohrožením škodlivých aktivit provedením v reálném čase šifrování a dešifrování dat, bez nutnosti změny aplikace. Toto chování je pro klienta transparentní. Další informace najdete v tématu [transparentní šifrování dat pro databázi SQL a datový sklad](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store taky zajišťuje šifrování dat uložených v účtu. Když je povolené, Data Lake Store automaticky šifruje data před uložením a dešifruje před načtení, což transparentní klientovi, který přistupuje k datům. Další informace najdete v tématu [zabezpečení v Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob storage a Azure Table storage
Azure Blob storage a Azure Table storage podporují šifrování služby úložiště (SSE), který automaticky šifruje vaše data před uložením do úložiště a dešifruje před načtení. Další informace najdete v tématu [šifrování služby úložiště Azure pro Data v klidovém stavu](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 podporuje klient i server šifrování dat v klidovém stavu. Další informace najdete v tématu [ochranu šifrování dat pomocí](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift podporuje šifrování clusteru pro neaktivní uložená data. Další informace najdete v tématu [šifrování databáze Redshift Amazon](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce podporuje šifrování štítu platformy, která umožňuje šifrování všech souborů, přílohy a vlastní pole. Další informace najdete v tématu [Princip toku webového serveru pro ověřování OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybridní scénáře
Hybridní scénáře vyžadují integraci s vlastním hostováním runtime být nainstalovaný v místní síti, ve virtuální síti (Azure) nebo uvnitř virtuální privátní cloud (Amazon). Modul runtime vlastním hostováním integrace musí být mít přístup k místní datové úložiště. Další informace o vlastním hostováním integrace modulu runtime najdete v tématu [jak vytvořit a nakonfigurovat hostovanou na vlastním integrace runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![Integrace s vlastním hostováním runtime kanály](media/data-movement-security-considerations/data-management-gateway-channels.png)

Příkaz kanál umožňuje komunikaci mezi služby pro přesun dat v datové továrně a integrace s vlastním hostováním runtime. Komunikace obsahuje informace související s aktivity. Datový kanál se používá pro přenos dat mezi místním úložištím dat a úložiště dat v cloudu.    

### <a name="on-premises-data-store-credentials"></a>Místní přihlašovací údaje k úložišti dat
Přihlašovací údaje pro vaše místní úložiště dat jsou vždy šifrována a uložené. Mohou být buď uložených místně na počítači runtime vlastním hostováním integrace nebo uložená v úložišti Azure Data Factory spravované (stejně jako cloudové úložiště přihlašovacích údajů). 

- **Uložit pověření místně**. Pokud chcete k šifrování a uložení přihlašovacích údajů místně na vlastním hostováním integrace modulu runtime, postupujte podle kroků v [šifrovat přihlašovací údaje pro místní úložiště dat v Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Všechny konektory podporu této možnosti. Modul runtime vlastním hostováním integrace používá Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) k šifrování citlivých informací data a přihlašovací údaje. 

   Použití **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** rutiny k šifrování přihlašovacích údajů propojené služby a citlivé údaje v propojené službě. Pak můžete použít JSON vrátil (s **EncryptedCredential** element v připojovacím řetězci) k vytvoření propojené služby pomocí **Set-AzureRmDataFactoryV2LinkedService** rutiny.  

- **Úložiště v úložišti Azure Data Factory spravované**. Pokud používáte přímo **Set-AzureRmDataFactoryV2LinkedService** rutiny s připojením řetězce a přihlašovací údaje vložené v kódu JSON, propojené služby je zašifrovaná a uložená v úložišti Azure Data Factory spravované. Důvěrné informace stále šifrován pomocí certifikátu a Microsoft spravuje tyto certifikáty.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porty používané při šifrování propojená služba na vlastním hostováním integrace modulu runtime
Ve výchozím nastavení prostředí PowerShell používá port 8050 na počítači s vlastním hostováním integrace runtime pro zabezpečenou komunikaci. V případě potřeby můžete změnit tento port.  

![Port HTTPS pro brány](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Šifrování během přenosu
Všechny přenosy dat jsou prostřednictvím zabezpečeného kanálu HTTPS a TLS přes protokol TCP. aby se zabránilo útokům man-in-the-middle při komunikaci se službami Azure.

Můžete také použít [IPSec pro síť VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) nebo [Azure ExpressRoute](../expressroute/expressroute-introduction.md) na další Zabezpečte komunikační kanál mezi místní sítí a Azure.

Virtuální síť Azure je logická reprezentace sítě v cloudu. Místní sítě můžete připojit k virtuální síti nastavením IPSec pro síť VPN (site-to-site) nebo ExpressRoute (soukromý partnerský vztah).    

Následující tabulka shrnuje sítě a doporučených konfigurací runtime vlastním hostováním integrace založený na různé kombinace zdrojové a cílové umístění pro přesun dat hybridní.

| Zdroj      | Cíl                              | Konfigurace sítě                    | Instalace prostředí Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokálně | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | IPSec pro síť VPN (point-to-site nebo site-to-site) | Modul runtime vlastním hostováním integrace může být nainstalované místně nebo na virtuální počítač Azure ve virtuální síti. |
| Lokálně | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | ExpressRoute (soukromý partnerský vztah)           | Modul runtime vlastním hostováním integrace může být nainstalované místně nebo na virtuální počítač Azure ve virtuální síti. |
| Lokálně | Služeb založených na Azure, které mají veřejný koncový bod | ExpressRoute (veřejný partnerský vztah)            | Modul runtime vlastním hostováním integrace musí být nainstalované na místě. |

Následující obrázky znázorňují použití vlastním hostováním integrace modulu runtime pro přesun dat mezi místní databázi a službami Azure pomocí ExpressRoute a VPN IPSec (s Azure Virtual Network):

**ExpressRoute**

![Pomocí brány ExpressRoute](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec pro síť VPN**

![IPSec pro síť VPN s bránou](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Konfigurace bran firewall a vytvoření seznamu povolených IP adres

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Požadavky na brány firewall pro ve místní nebo soukromé síti  
V podniku spustí podniková brána firewall ve směrovači centrální organizace. Brána Windows Firewall spouští se jako démon na místním počítači, ve kterém je nainstalován modul runtime vlastním hostováním integrace. 

Následující tabulka uvádí požadavky odchozí port a domény pro podnikové brány firewall:

| Názvy domén                  | Odchozí porty | Popis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Požadované pro připojení k služby pro přesun dat v datové továrně modulem runtime vlastním hostováním integrace. |
| `*.core.windows.net`          | 443            | Používané vlastním hostováním integrace modulu runtime pro připojení k účtu úložiště Azure při použití [připravený kopie](copy-activity-performance.md#staged-copy) funkce. |
| `*.frontend.clouddatahub.net` | 443            | Požadované modulem runtime vlastním hostováním integrace pro připojení ke službě Data Factory. |
| `*.database.windows.net`      | 1433           | (Volitelné) Vyžaduje se při kopírování z nebo na Azure SQL Database nebo Azure SQL Data Warehouse. Použijte funkci dvoufázové instalace kopírování zkopírovat data do Azure SQL Database nebo Azure SQL Data Warehouse bez otevření portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Volitelné) Vyžaduje se při kopírování z nebo do Azure Data Lake Store. |

> [!NOTE] 
> Můžete chtít spravovat porty nebo povolených domén na úrovni podniková brána firewall podle potřeby podle příslušné datové zdroje. Tato tabulka používá jako příklady pouze databáze SQL Azure, Azure SQL Data Warehouse a Azure Data Lake Store.   

Následující tabulka uvádí požadavky na portu pro příchozí spojení pro bránu Windows Firewall:

| Příchozí porty | Popis                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Vyžaduje šifrování rutiny prostředí PowerShell, jak je popsáno v [šifrovat přihlašovací údaje pro místní úložiště dat v Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)a aplikace Správce přihlašovacích údajů a bezpečně nastavte přihlašovací údaje pro místní úložiště dat na vlastním hostováním integrace modulu runtime. |

![Požadavky na porty brány](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>Konfigurace protokolu IP a povolených v úložišti dat
Některé datové úložiště v cloudu i vyžadovat IP adresu počítače přístup k úložišti povolených. Zkontrolujte, že IP adresa počítače runtime vlastním hostováním integrace je seznam povolených adres nebo správně nakonfigurována v bráně firewall.

Následující cloudové úložiště dat vyžaduje adresu IP počítače runtime vlastním hostováním integrace povolených. Některé z těchto dat úložiště, ve výchozím nastavení, nemusí vyžadovat vytvoření seznamu povolených. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Integrace s vlastním hostováním runtime v lze sdílet různé datové továrny?**

Tato funkce ještě nepodporujeme. Aktivně pracujeme na něm.

**Jaké jsou požadavky na portu pro integraci s vlastním hostováním runtime pro práci?**

Modul runtime vlastním hostováním integrace slouží k propojení založené na protokolu HTTP pro přístup k Internetu. Odchozí porty 443 a 80 musí být otevřen pro modul runtime s vlastním hostováním integrace pro toto připojení. Otevřete příchozí port 8050 pouze na úrovni počítače (ne úroveň podniková brána firewall) pro aplikaci správce přihlašovacích údajů. Pokud databáze SQL Azure nebo Azure SQL Data Warehouse slouží jako zdroj nebo cíl, budete muset otevřít port 1433 také. Další informace najdete v tématu [konfigurace a vytvoření seznamu povolených IP adresy brány Firewall](#firewall-configurations-and-whitelisting-ip-address-of-gateway) části. 


## <a name="next-steps"></a>Další postup
Informace o výkonu kopie aktivita služby Azure Data Factory najdete v tématu [výkonu kopie aktivity a vyladění průvodce](copy-activity-performance.md).

 
