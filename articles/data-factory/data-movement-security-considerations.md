---
title: Důležité informace o zabezpečení
description: Popisuje základní infrastrukturu zabezpečení, kterou služby pro přesun dat v Azure Data Factory používají k lepšímu zabezpečení vašich dat.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: b381f2f1871ea7e26950d5b02d5906a50c6129d3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445017"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Otázky zabezpečení při přesunu dat v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
>
> * [Verze 1](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuální verze](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje základní infrastrukturu zabezpečení, kterou služby pro přesun dat v Azure Data Factory používají k lepšímu zabezpečení vašich dat. Prostředky správy Data Factory jsou postavené na infrastruktuře zabezpečení Azure a využívají všechny možné bezpečnostní míry, které nabízí Azure.

V řešení Data Factory vytváříte jeden nebo více datových [kanálů](concepts-pipelines-activities.md). Kanál je logické seskupení aktivit, které společně provádějí úlohu. Tyto kanály se nacházejí v oblasti, ve které byl vytvořen objekt pro vytváření dat. 

I když je Data Factory k dispozici jenom v několika oblastech, služba přesunu dat je [dostupná globálně](concepts-integration-runtime.md#integration-runtime-location) , aby se zajistila kompatibilita dat, efektivita a snížení nákladů na výstup sítě. 

Azure Data Factory neukládá žádná data s výjimkou přihlašovacích údajů propojených služeb pro cloudová úložiště dat, která jsou zašifrovaná pomocí certifikátů. Pomocí Data Factory můžete vytvářet pracovní postupy řízené daty k orchestraci přesouvání dat mezi [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats)a zpracování dat pomocí [výpočetních služeb](compute-linked-services.md) v jiných oblastech nebo v místním prostředí. Pracovní postupy můžete monitorovat a spravovat také pomocí sad SDK a Azure Monitor.

Data Factory bylo certifikováno pro:

| **[Certifikace pro CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Pokud vás zajímá dodržování předpisů Azure a způsob, jakým Azure zabezpečuje svou vlastní infrastrukturu, přejděte na web [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). Poslední seznam všech kontrol nabídek dodržování předpisů Azure –  https://aka.ms/AzureCompliance .

V tomto článku prozkoumáme bezpečnostní opatření v následujících dvou scénářích přesunu dat: 

- **Scénář cloudu**: v tomto scénáři jsou vaše zdrojová i cílová služba veřejně přístupná prostřednictvím Internetu. Patří sem spravované cloudové úložiště, například Azure Storage, Azure synapse Analytics, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon RedShift, SaaS Services, jako je například Salesforce, a webové protokoly, jako jsou FTP a OData. V  [podporovaných úložištích a formátech dat](copy-activity-overview.md#supported-data-stores-and-formats)najdete úplný seznam podporovaných zdrojů dat.
- **Hybridní scénář**: v tomto scénáři je buď zdroj nebo cíl za bránou firewall nebo místní podnikovou sítí. Nebo je úložiště dat v privátní síti nebo virtuální síti (nejčastěji zdroj) a není veřejně přístupné. V tomto scénáři spadají i databázové servery hostované na virtuálních počítačích.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloudové scénáře

### <a name="securing-data-store-credentials"></a>Zabezpečení přihlašovacích údajů úložiště dat

- **Uloží šifrované přihlašovací údaje do spravovaného úložiště Azure Data Factory**. Data Factory pomáhá chránit přihlašovací údaje úložiště dat jejich šifrováním pomocí certifikátů spravovaných Microsoftem. Tyto certifikáty se otočí každé dva roky (včetně obnovení certifikátu a migrace přihlašovacích údajů). Další informace o zabezpečení Azure Storage najdete v tématu [Přehled zabezpečení Azure Storage](../storage/blobs/security-recommendations.md).
- **Ukládat přihlašovací údaje v Azure Key Vault**. Přihlašovací údaje úložiště dat můžete také uložit v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory načítá přihlašovací údaje během provádění aktivity. Další informace najdete v tématu [uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Šifrování dat při přenosu
Pokud cloudové úložiště dat podporuje protokol HTTPS nebo TLS, všechna přenosová data mezi službami přesunu dat v Data Factory a cloudovým úložištěm dat jsou prostřednictvím zabezpečeného kanálu HTTPS nebo TLS.

> [!NOTE]
> Všechna připojení k Azure SQL Database a Azure synapse Analytics vyžadují šifrování (SSL/TLS) při přenosu dat do a z databáze. Když vytváříte kanál pomocí formátu JSON, přidejte vlastnost šifrování a nastavte ji na **hodnotu true** v připojovacím řetězci. V případě Azure Storage můžete v připojovacím řetězci použít **https** .

> [!NOTE]
> Pokud chcete povolit šifrování při přenosu při přesouvání dat ze systému Oracle, postupujte podle jedné z následujících možností:
> 1. V Oracle serveru přejděte na Oracle Advanced Security (OAS) a nakonfigurujte nastavení šifrování, které podporuje šifrování Triple-DES (3DES) a standard AES (Advanced Encryption Standard) (AES). Podrobnosti najdete [tady](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) . ADF automaticky vyjednává metodu šifrování, aby používala tu, kterou nakonfigurujete v OAS při navazování připojení k Oracle.
> 2. V ADF můžete přidat EncryptionMethod = 1 do připojovacího řetězce (v propojené službě). Použije se jako metoda šifrování protokol SSL/TLS. Pokud to chcete použít, je třeba zakázat nastavení šifrování bez SSL v OAS na straně serveru Oracle a vyhnout se tak konfliktu šifrování.

> [!NOTE]
> Použitá verze protokolu TLS je 1,2.

### <a name="data-encryption-at-rest"></a>Šifrování v klidovém stavu
Některá úložiště dat podporují šifrování neaktivních uložených dat. Pro tato úložiště dat doporučujeme povolit mechanismus šifrování dat. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Transparentní šifrování dat (TDE) ve službě Azure synapse Analytics pomáhá chránit před hrozbou škodlivých aktivit tím, že provádí šifrování v reálném čase a dešifrování vašich dat v klidovém čase. Toto chování je pro klienta transparentní. Další informace najdete v tématu [zabezpečení databáze ve službě Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database také podporuje transparentní šifrování dat (TDE), které pomáhá chránit před hrozbou škodlivých aktivit pomocí šifrování a dešifrování dat v reálném čase, aniž by to vyžadovalo změny aplikace. Toto chování je pro klienta transparentní. Další informace najdete v tématu [transparentní šifrování dat pro SQL Database a datový sklad](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store taky poskytuje šifrování pro data uložená v účtu. Pokud je tato možnost povolená, Data Lake Store automaticky šifruje data před jejich uložením a dešifrováním, takže je transparentní pro klienta, který přistupuje k datům. Další informace najdete v tématu [zabezpečení v Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage a Azure Table Storage
Azure Blob Storage a Azure Table Storage podporují Šifrování služby Storage (SSE), které automaticky šifrují vaše data před tím, než se uloží do úložiště a dešifruje před načtením. Další informace najdete v tématu [šifrování služby Azure Storage pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 podporuje šifrování dat v klidovém stavu klienta i serveru. Další informace najdete v tématu [Ochrana dat pomocí šifrování](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon RedShift podporuje šifrování clusteru pro neaktivní neaktivní data. Další informace najdete v tématu [šifrování databáze Amazon RedShift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce podporuje šifrování platformy stínění, které umožňuje šifrování všech souborů, příloh a vlastních polí. Další informace najdete v tématu [Principy toku ověřování webového serveru OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybridní scénáře
Hybridní scénáře vyžadují instalaci místního prostředí Integration runtime v místní síti, ve virtuální síti (Azure) nebo ve virtuálním privátním cloudu (Amazon). Místní prostředí Integration runtime musí být schopné získat přístup k místním úložištím dat. Další informace o modulu Integration runtime v místním prostředí najdete v tématu [jak vytvořit a nakonfigurovat prostředí Integration runtime](./create-self-hosted-integration-runtime.md)v místním prostředí. 

![kanály prostředí Integration runtime pro místní hostování](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kanál příkazů umožňuje komunikaci mezi službami přesunu dat v Data Factory a v místním prostředí Integration runtime. Tato komunikace obsahuje informace související s aktivitou. Datový kanál se používá k přenosu dat mezi místními úložišti dat a cloudových úložišť dat.    

### <a name="on-premises-data-store-credentials"></a>Přihlašovací údaje místního úložiště dat
Přihlašovací údaje mohou být uloženy v rámci objektu pro vytváření dat nebo na [ně odkazovat pomocí služby Data Factory](store-credentials-in-key-vault.md) za běhu z Azure Key Vault. Pokud ukládáte přihlašovací údaje v rámci služby Data Factory, je vždy uložen šifrovaný v místním prostředí Integration runtime. 
 
- **Ukládat přihlašovací údaje lokálně**. Pokud přímo použijete rutinu **set-AzDataFactoryV2LinkedService** s připojovacími řetězci a přihlašovacími údaji, které jsou vložené ve formátu JSON, bude propojená služba zašifrovaná a uložená v místním prostředí Integration runtime.  V takovém případě přihlašovací údaje přes back-end službu Azure, která je mimořádně bezpečná, na integrační počítač v místním prostředí, kde je konečně zašifrovaný a uložený. Místní prostředí Integration runtime používá k šifrování citlivých dat a přihlašovacích údajů rozhraní Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) .

- **Ukládat přihlašovací údaje v Azure Key Vault**. Přihlašovací údaje úložiště dat můžete také uložit v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory načítá přihlašovací údaje během provádění aktivity. Další informace najdete v tématu [uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md).

- **Ukládat přihlašovací údaje místně bez přetečení přihlašovacích údajů prostřednictvím back-endu Azure do místního prostředí Integration runtime**. Pokud chcete místně šifrovat a ukládat přihlašovací údaje v místním prostředí Integration runtime, aniž byste museli procházet přihlašovací údaje prostřednictvím back-endu služby Data Factory, postupujte podle kroků v části [šifrování přihlašovacích údajů pro místní úložiště dat v Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Tato možnost podporuje všechny konektory. Místní prostředí Integration runtime používá k šifrování citlivých dat a přihlašovacích údajů rozhraní Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) . 

   Použijte rutinu **New-AzDataFactoryV2LinkedServiceEncryptedCredential** k šifrování přihlašovacích údajů propojené služby a citlivých podrobností v propojené službě. Pak můžete pomocí rutiny **set-AzDataFactoryV2LinkedService** použít vrácený kód JSON (s elementem **EncryptedCredential** v připojovacím řetězci) a vytvořit propojenou službu.  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porty používané při šifrování propojené služby v místním prostředí Integration runtime
Ve výchozím nastavení používá PowerShell pro zabezpečenou komunikaci port 8060 na počítači s místním prostředím Integration runtime. V případě potřeby můžete tento port změnit.  

![Port HTTPS pro bránu](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Šifrování během přenosu
Všechny přenosy dat jsou prostřednictvím zabezpečeného kanálu HTTPS a TLS přes protokol TCP, aby se zabránilo útokům prostředníkem při komunikaci se službami Azure.

K dalšímu zabezpečení komunikačního kanálu mezi vaší místní sítí a Azure můžete použít taky [protokol IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) nebo [Azure ExpressRoute](../expressroute/expressroute-introduction.md) .

Azure Virtual Network je logická reprezentace vaší sítě v cloudu. Můžete připojit místní síť k virtuální síti nastavením IPSec VPN (site-to-site) nebo ExpressRoute (privátní partnerské vztahy).    

Následující tabulka shrnuje doporučení konfigurace sítě a místního prostředí Integration runtime na základě různých kombinací zdrojového a cílového umístění pro pohyb hybridních dat.

| Zdroj      | Cíl                              | Konfigurace sítě                    | Instalace prostředí Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Místní | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | IPSec VPN (Point-to-site nebo site-to-site) | Místní prostředí Integration runtime by se mělo nainstalovat na virtuální počítač Azure ve virtuální síti.  |
| Místní | Virtuální počítače a cloudové služby nasazené ve virtuálních sítích | ExpressRoute (soukromý partnerský vztah)           | Místní prostředí Integration runtime by se mělo nainstalovat na virtuální počítač Azure ve virtuální síti.  |
| Místní | Služby založené na Azure s veřejným koncovým bodem | ExpressRoute (partnerský vztah Microsoftu)            | Místní prostředí Integration runtime se dá nainstalovat místně nebo na virtuální počítač Azure. |

Následující obrázky ukazují použití prostředí Integration runtime v místním prostředí pro přesun dat mezi místní databází a službami Azure pomocí ExpressRoute a IPSec VPN (s využitím Azure Virtual Network):

**ExpressRoute**

![Použití ExpressRoute s bránou](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN s bránou](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a> Nastavení konfigurace brány firewall a povolení seznamu povolených IP adres

> [!NOTE] 
> Možná budete muset spravovat porty nebo na úrovni podnikové brány firewall nastavit seznam povolených pro domény podle požadavků příslušných zdrojů dat. Tato tabulka používá jako příklady jenom Azure SQL Database, Azure synapse Analytics a Azure Data Lake Store.

> [!NOTE] 
> Podrobnosti o strategiích přístupu k datům prostřednictvím Azure Data Factory najdete v [tomto článku](./data-access-strategies.md#data-access-strategies-through-azure-data-factory).

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Požadavky na bránu firewall pro místní nebo privátní síť    
V podniku je podniková brána firewall provozována v centrálním směrovači organizace. Brána Windows Firewall běží jako démon na místním počítači, ve kterém je nainstalovaný modul runtime integrace v místním prostředí. 

Následující tabulka obsahuje požadavky na Odchozí porty a domény pro podnikové brány firewall:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Možná budete muset spravovat porty nebo na úrovni podnikové brány firewall nastavit seznam povolených pro domény podle požadavků příslušných zdrojů dat. Tato tabulka používá jako příklady jenom Azure SQL Database, Azure synapse Analytics a Azure Data Lake Store.   

Následující tabulka uvádí požadavky na porty pro bránu Windows Firewall:

| Příchozí porty | Popis                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Vyžadovaná rutinou šifrování PowerShellu, jak je popsáno v tématu [šifrování přihlašovacích údajů pro místní úložiště dat v Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)a aplikace Správce přihlašovacích údajů pro bezpečné nastavení přihlašovacích údajů pro místní úložiště dat v místním prostředí Integration runtime. |

![Požadavky na port brány](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Nastavení konfigurace protokolu IP a povolených seznamů povolených v úložištích dat
Některá úložiště dat v cloudu také vyžadují, abyste povolili IP adresu počítače, který přistupuje k úložišti. Ujistěte se, že je IP adresa počítače místního prostředí Integration runtime povolená nebo správně nakonfigurovaná v bráně firewall.

Následující cloudová úložiště dat vyžadují, abyste povolili IP adresu počítače místního prostředí Integration runtime. Některá z těchto úložišť dat nemusí ve výchozím nastavení vyžadovat seznam povolených. 

- [Azure SQL Database](../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Je možné místní prostředí Integration runtime sdílet mezi různými datovými továrnami?**

Ano. Další podrobnosti [najdete tady](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Jaké jsou požadavky na porty pro fungování prostředí Integration runtime v místním prostředí?**

Místní prostředí Integration runtime zpřístupňuje připojení založená na protokolu HTTP pro přístup k Internetu. Aby bylo možné vytvořit toto připojení, musí být otevřeny Odchozí porty 443 pro prostředí Integration runtime v místním prostředí. Pro aplikaci Správce přihlašovacích údajů otevřete příchozí port 8060 jenom na úrovni počítače (ne na úrovni firemní brány firewall). Pokud se jako zdroj nebo cíl používá Azure SQL Database nebo Azure synapse Analytics, musíte otevřít taky port 1433. Další informace najdete v části [Konfigurace brány firewall a nastavení seznamu povolených IP adres](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) . 


## <a name="next-steps"></a>Další kroky
Informace o Azure Data Factory výkonu aktivity kopírování najdete v tématu [Průvodce laděním a výkonem aktivity kopírování](copy-activity-performance.md).

