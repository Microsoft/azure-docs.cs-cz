---
title: Přehled šifrování Azure | Dokumentace Microsoftu
description: Další informace o různých možnostech šifrování v Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 272cc843ab90eade06525f665d3cf2decf74a26f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114650"
---
# <a name="azure-encryption-overview"></a>Přehled šifrování Azure

Tento článek obsahuje základní informace o použití šifrování v Microsoft Azure. Popisuje hlavní oblasti šifrování, včetně šifrování v klidovém stavu, šifrování v letu a správy klíčů se službou Azure Key Vault. Každý oddíl obsahuje odkazy na podrobnější informace.

## <a name="encryption-of-data-at-rest"></a>Šifrování neaktivních uložených dat

Neaktivní uložená data zahrnují informace, které se nacházejí v trvalého úložiště na fyzickém médiu, v libovolném formátu digitální. Médium můžete zahrnout soubory magnetické nebo optické médií, Archivovaná data a data záloh. Microsoft Azure nabízí celou řadu řešení úložiště dat pro různé potřeby, včetně souborů, disků, objektů blob a table storage. Microsoft také zajišťuje šifrování pro ochranu [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [služby Azure Cosmos DB](../cosmos-db/introduction.md)a Azure Data Lake.

Šifrování dat v klidovém stavu je k dispozici pro služby napříč software jako služba (SaaS), platforma jako služba (PaaS) a infrastruktura jako služba (IaaS) cloudových modelech. Tento článek shrnuje a obsahuje materiály, které vám pomůžou s používáním možnosti šifrování Azure.

Podrobnější informace o tom, jak data v klidovém stavu zašifrovaná v Azure, najdete v článku [Azure Data šifrování neaktivních](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modely šifrování Azure

Azure podporuje různé modely šifrování, včetně šifrování na straně serveru, které používá spravovaný službou klíče, klíče spravované zákazníkem ve službě Key Vault nebo klíče spravované zákazníkem zákazníka řídí hardware. Pomocí šifrování na straně klienta můžete spravovat a ukládat klíče v místním nebo v jiném Zabezpečte umístění.

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Šifrování na straně klienta se provádí mimo Azure. Zahrnuje:

- Data zašifrovaná podle aplikace, která běží v datacentru zákazníka nebo aplikace služby.
- Data, která je už zašifrovali při přijetí Azure.

Poskytovatelé cloudových služeb pomocí šifrování na straně klienta, nebudete mít přístup k šifrovacím klíčům a nelze dešifrovat data. Budete mít naprostou kontrolu nad klíči.

### <a name="server-side-encryption"></a>Šifrování na straně serveru

Všechny tři modely šifrování na straně serveru nabízet správu různých klíčů vlastnostmi, které můžete použít podle vašich požadavků:

- **Klíče spravované službou**: Obsahuje kombinaci ovládacího prvku a usnadnění práce s nízkou režií.

- **Klíče spravované zákazníkem**: Umožňuje kontrolu nad klíči, včetně podpory Bring Your Own klíče (BYOK), nebo můžete vygenerovat nové značky.

- **Klíče spravované zákazníkem služby zákazníka řídí hardware**: Umožňuje spravovat klíče v úložišti proprietární mimo Microsoft ovládacího prvku. Tato vlastnost se nazývá hostitele Your Own Key (HYOK). Ale konfigurace je složitá a většina služeb Azure nepodporují tento model.

### <a name="azure-disk-encryption"></a>Azure disk encryption

Můžete chránit virtuální počítače s Windows a Linux pomocí [Azure disk encryption](azure-security-disk-encryption.md), který používá [nástroj Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) technologie a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) chránit i disky operačního systému a datové disky pomocí šifrování celého svazku.

Šifrování klíče a tajné klíče jsou chráněné ve vaší [předplatné služby Azure Key Vault](../key-vault/key-vault-whatis.md). Pomocí služby Azure Backup můžete zálohování a obnovení šifrovaných virtuálních počítačů (VM), které používá klíč šifrování klíčů (KEK) konfiguraci.

### <a name="azure-storage-service-encryption"></a>Šifrování služby Azure Storage

Ve scénářích na straně serveru a na straně klienta je možné zašifrovat data umístěná v úložišti objektů Blob v Azure a sdílených složek Azure.

[Šifrování služby Azure Storage (SSE)](../storage/common/storage-service-encryption.md) automaticky data můžete šifrovat před jsou uložená, a automaticky dešifruje data po načtení. Proces je pro uživatele zcela transparentní. Šifrování služby Storage pomocí 256bitového [šifrování Advanced Encryption (Standard AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedna z nejsilnějších dostupných variant blokového šifrování. AES transparentně zpracovává šifrování, dešifrování a správu klíčů.

### <a name="client-side-encryption-of-azure-blobs"></a>Šifrování na straně klienta objektů BLOB Azure

Pokud chcete provádět na straně klienta šifrování Azure blobs různými způsoby.

Klientská knihovna pro úložiště Azure pro balíček NuGet pro rozhraní .NET můžete použít k šifrování dat v rámci vaší klientské aplikace před jejich odesláním do služby Azure storage.

Další informace a stáhnout Klientská knihovna pro úložiště Azure pro balíček NuGet pro rozhraní .NET najdete v tématu [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Pokud používáte šifrování na straně klienta se službou Key Vault, vaše data zašifrují pomocí jednorázové symetrického obsahu šifrovací klíč (CEK), který je generován klienta služby Azure Storage SDK. CEK se šifrují pomocí na klíč šifrování klíčů (KEK), což může být buď symetrický klíč, nebo asymetrický pár klíčů. Můžete spravovat místně nebo ukládat ve službě Key Vault. Šifrovaná data se pak nahrají do služby Azure Storage.

Další informace o šifrování na straně klienta se službou Key Vault a začít pracovat s pokyny k používání najdete v tématu [kurzu: Šifrování a dešifrování objektů BLOB ve službě Azure Storage s použitím služby Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Provést šifrování na straně klienta předtím, než nahrání dat do služby Azure Storage a k dešifrování dat při stahování do klienta a konečně, můžete použít také klientskou knihovnu pro úložiště Azure pro Javu. Tato knihovna také podporuje integraci s [služby Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů účtu úložiště.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Šifrování dat v klidovém stavu pomocí Azure SQL Database

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) je služba relačních databází pro obecné účely v Azure, která podporuje struktury, jako jsou relační data, JSON, prostorová data a XML. SQL Database podporuje šifrování na straně serveru prostřednictvím funkce transparentní šifrování dat (TDE) a šifrování na straně klienta pomocí funkce Always Encrypted.

#### <a name="transparent-data-encryption"></a>Transparentní šifrování dat

[Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) se používá k šifrování [systému SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md), a [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) datové soubory v reálném čase pomocí databáze šifrovací klíč (DEK) , který je uložen v spouštěcí záznam databáze dostupnosti během obnovení.

Transparentní šifrování dat chrání soubory protokolu a data, pomocí standardu AES a Data šifrování (3DES Triple Standard) šifrovacích algoritmů. Šifrování souboru databáze se provádí na úrovni stránky. Na stránkách v šifrované databázi zašifrují předtím, než se zapisují do disku a jsou dešifrovat, pokud se načtení do paměti. TDE je nyní povolena ve výchozím nastavení u nově vytvořených databází Azure SQL.

#### <a name="always-encrypted-feature"></a>Always Encrypted funkce

S [s funkcí Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkce v Azure SQL data v rámci klientské aplikace před uložením ve službě Azure SQL Database můžete šifrovat. Můžete také povolit delegování správy databází v místním třetím stranám a udržovat oddělení mezi těmi, kdo vlastní a zobrazovat data a těmi, kdo ho spravovat, ale by neměly mít přístup k němu.

#### <a name="cell-level-or-column-level-encryption"></a>Šifrování na úrovni buňky nebo sloupec

S Azure SQL Database můžete použít šifrování se symetrickým sloupec dat s použitím příkazů jazyka Transact-SQL. Tento přístup se nazývá [úrovni buněk nebo šifrování na úrovni sloupce (Vymazat)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), protože ho můžete Pokud chcete šifrovat určité sloupce nebo dokonce konkrétní buňky dat pomocí různých šifrovacích klíčů. Tím získáte podrobnější funkci šifrování, kterou než transparentní šifrování dat, která šifruje data na stránkách.

Vymazat má integrované funkce, které můžete použít k šifrování dat pomocí symetrických nebo asymetrické klíče, veřejný klíč certifikátu nebo přístupové heslo pomocí 3DES.

### <a name="cosmos-db-database-encryption"></a>Šifrování databáze cosmos DB

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) je globálně distribuovaná a vícemodelová databáze Microsoftu pro. Ve výchozím nastavení je šifrovaná uživatelská data, která je uložená ve službě Cosmos DB v stálé úložiště (jednotky SSD). Neexistují žádná opatření můžete zapnout nebo vypnout. Šifrování v klidovém stavu je implementovaný s využitím řadu bezpečnostních technologií, včetně systémů zabezpečené úložiště klíčů, šifrované sítě a rozhraní API kryptografických. Šifrovací klíče jsou spravované microsoftem a jsou otočen za interní pokyny Microsoftu.

### <a name="at-rest-encryption-in-data-lake"></a>Šifrování v klidovém stavu ve službě Data Lake

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) je je úložiště na podnikové úrovni všechny typy dat soustředěné na jednom místě před jakýmkoliv formálním definováním požadavků nebo schématu. Data Lake Store podporuje "na ve výchozím nastavení," transparentní šifrování dat v klidovém stavu, která se nastavuje během vytváření účtu. Ve výchozím nastavení Azure Data Lake Store klíče spravuje za vás, ale budete mít možnost spravovat sami.

Šifrování a dešifrování dat se používají tři typy klíčů: hlavní šifrovací klíč (MEK), datový šifrovací klíč (DEK) a blok šifrovací klíč (klíče BEK). Hlavní šifrovací klíč se používá k šifrování klíče DEK, který je uložený na trvalé médium, a klíč BEK je odvozený od klíče DEK a příslušného datového bloku. Pokud spravujete vlastní klíče, můžete obměňovat klíč MEK.

## <a name="encryption-of-data-in-transit"></a>Šifrování přenášených dat

Azure nabízí mnoho mechanismů pro zachování dat soukromých při jejich přesunu z jednoho umístění do druhého.

### <a name="tlsssl-encryption-in-azure"></a>Šifrování TLS/SSL v Azure

Společnost Microsoft používá [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) protokol (TLS) k ochraně dat, když je cestách mezi cloudovými službami a zákazníky. Datová centra Microsoft vyjednávání TLS připojení s klientské systémy, které se připojují ke službám Azure. Protokol TLS poskytuje silné ověřování, ochrana soukromí zpráv a integrita (když se povolí zjišťování manipulaci se zprávami, zachycení a proti padělání), vzájemná funkční spolupráce, flexibilita algoritmu a snadného nasazení a použití.

[Perfektní Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) chrání připojení mezi klientské systémy a cloudovými službami Microsoftu zákazníků tím, že jedinečné klíče. Připojení se používají také délky klíčů šifrování RSA podle 2 048 bitů. Tato kombinace ztěžuje pro uživatele k zachycení a přístup k datům, který je při přenosu.

### <a name="azure-storage-transactions"></a>Transakce služby Azure Storage

Pokud pracujete s Azure Storage na webu Azure portal, všechny transakce provedou přes protokol HTTPS. Můžete také použít API REST úložiště přes protokol HTTPS pro interakci s Azure Storage. Při volání rozhraní REST API pro přístup k objektům v účtech úložiště tím, že umožňuje zabezpečený přenos, který je potřeba pro účet úložiště, můžete vynutit použití protokolu HTTPS.

Sdílené přístupové podpisy ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), který umožňuje delegovat přístup k objektům služby Azure Storage, zahrnují možnost určit, že při použití sdílené přístupové podpisy lze použít pouze protokolu HTTPS. Tento přístup zajišťuje, že každý, kdo odešle propojení s tokeny SAS používá správný protokol.

[Protokol SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), který používá pro přístup k Azure Files sdílené složky, podporuje šifrování a to je k dispozici v systému Windows Server 2012 R2, Windows 8, Windows 8.1 a Windows 10. Umožňuje přístup mezi oblastmi a přejít dokonce i v klientských počítačích.

Šifrování na straně klienta šifruje data před odesláním do instance služby Azure Storage tak, aby se šifrují při jejich přenosu v síti.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Šifrování paketů SMB přes virtuální sítě Azure 

S použitím [protokolu SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) ve virtuálních počítačích, na kterých běží Windows Server 2012 nebo novější, můžete provést datové přenosy zabezpečit tím, že šifrování dat při přenosu přes virtuální sítě Azure. Tím, že šifruje data, můžete ochránit proti falšování a útoky odposloucháváním. Správci mohou povolit šifrování protokolu SMB pro celý server, nebo jenom určité sdílené složky.

Ve výchozím nastavení po zapnutí šifrování protokolu SMB pro sdílenou složku nebo server, pouze klienti SMB 3.0 je povolen přístup k šifrované sdílené složky.

## <a name="in-transit-encryption-in-vms"></a>Šifrování během přenosu do virtuálních počítačů

Data přenášená do, z a mezi virtuálními počítači, na kterých běží Windows zašifrovaná různými způsoby v závislosti na povaze připojení.

### <a name="rdp-sessions"></a>Relace protokolu RDP

Můžete se připojit a přihlásit k virtuálnímu počítači pomocí [protokolu RDP (Remote Desktop)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) z klientského počítače Windows nebo na macu se nainstalovat klienta protokolu RDP. Data přenášená přes síť v relace protokolu RDP se dá chránit TLS.

Můžete také použít vzdálené plochy pro připojení k virtuálnímu počítači s Linuxem v Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Zabezpečený přístup k virtuálním počítačům s Linuxem pomocí protokolu SSH

Pro vzdálenou správu, můžete použít [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) pro připojení k virtuální počítače s Linuxem v Azure. SSH je šifrované připojení protokol, který umožňuje zabezpečené přihlášení přes zabezpečená připojení. Je výchozím protokolem připojení pro virtuální počítače Linux hostované v Azure. S použitím klíče SSH pro ověřování, eliminuje nutnost používat k přihlášení hesla. Protokol SSH používá dvojice veřejného/soukromého klíče (asymetrické šifrování) pro ověřování.

## <a name="azure-vpn-encryption"></a>Šifrování služby Azure VPN

Můžete se připojit k Azure přes virtuální privátní sítě, která vytvoří zabezpečené tunelové propojení na ochranu soukromí dat odesílaných po síti.

### <a name="azure-vpn-gateways"></a>Azure VPN Gateway

Můžete použít [brána Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) k posílání šifrovaného provozu mezi virtuální sítí a místním umístěním přes veřejné připojení, nebo k posílání síťového provozu mezi virtuálními sítěmi.

Site-to-site VPN použijte [IPsec](https://en.wikipedia.org/wiki/IPsec) pro šifrování přenosu. Azure VPN Gateway pomocí sady výchozí návrhy. Můžete nakonfigurovat bran Azure VPN k použití vlastních zásad IPsec/IKE s konkrétní kryptografické algoritmy a síly klíče, spíše než nastaví Azure výchozí zásady.

### <a name="point-to-site-vpns"></a>Point-to-site VPN

Point-to-site VPN povolit jednotlivé klientské počítače přístup ke službě Azure virtual network. [Soketu tunelového propojení protokolu SSTP (Secure)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) slouží k vytváření tunelového připojení sítě VPN. Ho můžete procházet brány firewall (tunelové propojení se zobrazí jako připojení HTTPS). Vlastní infrastruktury interní veřejných klíčů (PKI) kořenové certifikační autority (CA) můžete použít pro připojení point-to-site.

Můžete nakonfigurovat připojení VPN typu point-to-site k virtuální síti pomocí webu Azure portal pomocí ověřování certifikátů nebo prostředí PowerShell.

Další informace o připojení point-to-site VPN k virtuálním sítím Azure najdete v tématu:

[Konfigurace připojení typu point-to-site k virtuální síti pomocí ověřování certifikace: Azure portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurace připojení point-to-site k virtuální síti s použitím ověření certifikátu: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>VPN typu Site-to-site 

Připojení brány VPN typu site-to-site lze použít k připojení místní sítě ke službě Azure virtual network přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, který má externího veřejnou IP adresu přiřazenou.

Můžete nakonfigurovat připojení VPN typu site-to-site k virtuální síti pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure.

Další informace naleznete v tématu:

[Vytvoření připojení site-to-site na webu Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Vytvoření připojení site-to-site v prostředí PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Vytvoření virtuální sítě s připojením VPN typu site-to-site s použitím rozhraní příkazového řádku](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Šifrování během přenosu ve službě Data Lake

Přenášená data se ve službě Data Lake Store také vždy šifrují. Kromě šifrování dat před uložením v trvalé médium se také vždy šifrují data při přenosu pomocí protokolu HTTPS. HTTPS je jediný protokol, který se podporuje v rozhraních REST služby Data Lake Store.

Další informace o šifrování dat při přenosu ve službě Data Lake najdete v tématu [šifrování dat v Data Lake Store](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Správa klíčů se službou Key Vault

Bez správného ochrana a Správa klíčů je vykreslen šifrování zbytečné. Key Vault je společnost Microsoft doporučuje řešení pro správu a řízení přístupu k šifrovacím klíčům, které používá služba cloud services. Oprávnění pro přístup ke klíči je možné přiřadit ke službám nebo uživatelům prostřednictvím účtů služby Azure Active Directory.

Key Vault přišla organizace potřeba konfigurovat, opravovat a udržovat moduly hardwarového zabezpečení (HSM) a software na správu klíčů. Při použití služby Key Vault, Udržovat ovládacího prvku. Microsoft nikdy uvidí vaše klíče a aplikací nemají přímý přístup k nim. Můžete také importujte nebo generujte klíče v modulech hardwarového zabezpečení.

## <a name="next-steps"></a>Další postup

- [Přehled zabezpečení Azure](security-get-started-overview.md)
- [Přehled zabezpečení sítě Azure](security-network-overview.md)
- [Přehled zabezpečení služby Azure database](azure-database-security-overview.md)
- [Přehled zabezpečení Azure virtual machines](security-virtual-machines-overview.md)
- [Šifrování dat v klidovém stavu](azure-security-encryption-atrest.md)
- [Osvědčené postupy šifrování a zabezpečení dat](azure-security-data-encryption-best-practices.md)
