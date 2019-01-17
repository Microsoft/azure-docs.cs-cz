---
title: Koncové body služeb virtuální sítě a pravidla pro Azure SQL Database a SQL Data Warehouse | Dokumentace Microsoftu
description: Označte jako koncový bod služby virtuální sítě podsítě. Klikněte koncový bod jako pravidlo virtuální sítě do seznamu řízení přístupu Azure SQL Database. SQL Database je pak podporují komunikaci ze všech virtuálních počítačů a ostatní uzly v podsíti.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: vanto, genemi
manager: craigg
ms.date: 1/16/2019
ms.openlocfilehash: 2c022bd002700426eea2c6b38a667cd5a1381c02
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359846"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql"></a>Použití koncové body služeb virtuální sítě a pravidel pro Azure SQL

*Pravidla virtuální sítě* jsou jeden funkce zabezpečení brány firewall, která určuje, zda Azure [SQL Database](sql-database-technical-overview.md) nebo [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) server přijímá komunikaci, kterou jsou odesílány z konkrétní podsítí ve virtuálních sítích. Tento článek vysvětluje, proč funkce pravidlo virtuální sítě je někdy nejlepší možnost pro umožnění bezpečné komunikace s Azure SQL Database a SQL Data Warehouse.

> [!IMPORTANT]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse. Tento článek provádí *není* platí pro **Azure SQL Database Managed Instance**.

K vytvoření pravidla virtuální sítě, nejprve musí být [koncový bod služby virtuální sítě] [ vm-virtual-network-service-endpoints-overview-649d] pravidla pro odkazování.

## <a name="how-to-create-a-virtual-network-rule"></a>Jak vytvořit pravidlo virtuální sítě

Pokud vytvoříte pouze pravidlo virtuální sítě, můžete přeskočit ke krokům nebo vysvětlení [dále v tomto článku](#anchor-how-to-by-using-firewall-portal-59j).

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie a popis

**Virtuální síť:** Může mít virtuální sítě přidružený k vašemu předplatnému Azure.

**Podsíť:** Virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure (VM), ke kterým máte jsou přidruženy k podsítím. Jedna podsíť může obsahovat několik virtuálních počítačů nebo jiných výpočetních uzlech. Výpočetní uzly, které jsou mimo virtuální síť nemůže přistupovat k vaší virtuální sítě, pokud konfiguraci zabezpečení pro povolení přístupu.

**Koncový bod pro služby virtuální sítě:** A [koncový bod služby virtuální sítě] [ vm-virtual-network-service-endpoints-overview-649d] je podsíť, jejichž hodnoty vlastností zahrnují jeden nebo víc názvů typu formální služby Azure. V tomto článku jsme se zajímat název typu **Microsoft.Sql**, která odkazuje na službu Azure SQL Database s názvem.

**Pravidlo virtuální sítě:** Pravidlo virtuální sítě pro váš server SQL Database je podsíť, která je uvedena v seznamu řízení přístupu (ACL) serveru služby SQL Database. Chcete-li se v seznamu ACL pro vaši databázi SQL, musí obsahovat podsítě **Microsoft.Sql** název typu.

Pravidlo virtuální sítě říká databáze SQL serveru tak, aby přijímal komunikaci od každý uzel, který je v podsíti.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Výhody pravidlo virtuální sítě

Dokud je provést akce, virtuální počítače na podsítě nemůže komunikovat s SQL Database. Jednu akci, která vytváří komunikace je vytvoření pravidla virtuální sítě. Důvody pro výběr přístup pravidlo virtuální sítě vyžaduje diskuzi porovnání a kontrast zahrnující konkurenční možnosti zabezpečení nabízí bránou firewall.

### <a name="a-allow-access-to-azure-services"></a>A. Povolit přístup ke službám Azure

V podokně brány firewall **ON/OFF** tlačítko, které je označené jako **povolit přístup ke službám Azure**. **ON** nastavení umožňuje komunikaci ze všech IP adres Azure a všech podsítí Azure. Tyto IP adresy Azure nebo podsítě nemusí být vlastníte. To **ON** nastavení je pravděpodobně otevřenější než se vaše databáze SQL jako. Funkce pravidlo virtuální sítě nabízí mnohem lepší kontrolu.

### <a name="b-ip-rules"></a>B. Pravidla protokolu IP

Brána firewall SQL Database vám umožní určit rozsahy IP adres, ze kterých se přijímají komunikaci do služby SQL Database. Tento přístup je v pořádku pro stabilní IP adresy, které jsou mimo privátní síť Azure. Ale počet uzlů v rámci Azure privátní sítě jsou nakonfigurovány s *dynamické* IP adresy. Dynamické IP adresy můžou změnit, například když váš virtuální počítač se restartuje. Bylo by pošetilost dynamickou IP adresu určit v pravidlu brány firewall, v produkčním prostředí.

Možnosti IP můžete zachránit získáním *statické* IP adresu vašeho virtuálního počítače. Podrobnosti najdete v tématu [konfigurace privátních IP adres pro virtuální počítač pomocí webu Azure portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Ale statické IP přístup může být obtížné spravovat a je nákladná provádět ve velkém měřítku. Pravidla virtuální sítě je snazší, vytvořit a spravovat.

> [!NOTE]
> SQL Database ještě nemůžete mít v podsíti. Pokud váš server Azure SQL Database byl uzel v podsíti ve virtuální síti, všechny uzly v rámci virtuální sítě může komunikovat s databází SQL. V takovém případě může vaše virtuální počítače komunikovat s SQL Database bez nutnosti jakékoli pravidla virtuální sítě nebo IP.

Ale v září 2017 služby Azure SQL Database ještě není mezi službami, které je možné přiřadit k podsíti.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Podrobnosti o pravidel virtuální sítě

Tato část popisuje několik podrobností o pravidel virtuální sítě.

### <a name="only-one-geographic-region"></a>Pouze jedné zeměpisné oblasti

Každý koncový bod služby virtuální sítě se týká pouze jedné oblasti Azure. Koncový bod neumožňuje jiných oblastech tak, aby přijímal komunikaci z podsítě.

Žádné pravidlo virtuální sítě je omezená na oblasti, která se vztahuje na jeho základní koncový bod.

### <a name="server-level-not-database-level"></a>Na úrovni serveru, ne databáze na úrovni

Každé pravidlo virtuální sítě se vztahuje na celý server Azure SQL Database, ne jenom na jednu konkrétní databázi na serveru. Jinými slovy pravidlo virtuální sítě se vztahuje na úrovni serveru, ne na úrovni databáze.

- Naproti tomu můžete použít IP pravidla buď na úrovni.

### <a name="security-administration-roles"></a>Role zabezpečení správy

Je oddělení rolí zabezpečení ve správě koncových bodů služby virtuální sítě. Akce je zapotřebí ve směru z každé z následujících rolí:

- **Správce sítě:** &nbsp; Zapněte koncový bod.
- **Správce databáze:** &nbsp; Aktualizujte seznam řízení přístupu (ACL) pro přidání dané podsítě k serveru SQL Database.

*Ve zkratce RBAC:*

Role správce sítě a správce databáze mají další funkce, než jsou potřeba ke správě pravidel virtuální sítě. Je potřeba pouze podmnožinu jejich schopnosti.

Máte možnost použití [řízení přístupu na základě role (RBAC)] [ rbac-what-is-813s] v Azure k vytvoření jedné vlastní roli, která obsahuje pouze nezbytné dílčí sadu možností. Vlastní role může použít namísto správce sítě nebo správce databáze. Oblast povrchu napadení zabezpečení je nižší, pokud přidáte vlastní roli, a přidejte tohoto uživatele do další dvě hlavní správce role uživatele.

> [!NOTE]
> V některých případech jsou Azure SQL Database a podsítěmi virtuálních sítí v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Obě předplatná musí být ve stejném tenantovi Azure Active Directory.
> - Uživatel nemá potřebná oprávnění k zahájení operace, např. povolují se koncové body služby a přidáte k podsíti virtuální sítě na daném serveru.
> - Obě předplatná musí mít registrovaný poskytovatel Microsoft.Sql.

## <a name="limitations"></a>Omezení

Funkce pravidel virtuální sítě pro službu Azure SQL Database má následující omezení:

- Webové aplikace lze mapovat na privátní IP adresu ve virtuální síti nebo podsíti. I v případě, že koncové body služby se zapnutým z dané virtuální sítě nebo podsítě, budou mít připojení k serveru z webové aplikace Azure veřejné IP zdroj, ne zdroj virtuálních sítí/podsítí. Pokud chcete povolit připojení z webové aplikace na server, který má pravidla brány firewall virtuální sítě, je nutné **služby Azure umožňují přístup k serveru** na serveru.

- Každé pravidlo virtuální sítě v bráně firewall pro SQL Database, odkazuje na podsíť. Všechny odkazované podsítě musí být hostovaný ve stejné zeměpisné oblasti, který je hostitelem databáze SQL.

- Každý server Azure SQL Database můžete mít až 128 položky seznamů ACL pro jakékoli dané virtuální síti.

- Virtuální síť pravidla se vztahují pouze k virtuálním sítím Azure Resource Manageru; a nikoli k [modelu nasazení classic] [ arm-deployment-model-568f] sítě.

- Zapnutí na koncové body služby virtuální sítě do služby Azure SQL Database také umožňuje koncové body služby MySQL a PostgreSQL Azure. Pomocí koncových bodů dále, ale pokusy o připojení k instancím MySQL nebo PostgreSQL z koncových bodů může selhat.
  - Základní důvodem je, MySQL a PostgreSQL pravděpodobně nemají nakonfigurované pravidlo virtuální sítě. Je nutné nakonfigurovat pravidlo virtuální sítě pro Azure Database for MySQL a PostgreSQL a připojení bude úspěšné.

- V bráně firewall rozsahy IP adres se vztahují na následující síťové položky, ale nepodporují pravidla virtuální sítě:
  - [Site-to-Site (S2S) virtuální privátní sítě (VPN)][vpn-gateway-indexmd-608y]
  - On-premises prostřednictvím [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Důležité informace o používání koncových bodů služby

Při používání koncových bodů služby pro službu Azure SQL Database, přečtěte si následující aspekty:

- **Odchozí do veřejné IP adresy na Azure SQL Database se vyžaduje**: Skupiny zabezpečení sítě (Nsg) musí být otevřen pro Azure SQL Database IP adres umožňující připojení k. Můžete to provést pomocí skupiny zabezpečení sítě [značky služeb](../virtual-network/security-overview.md#service-tags) pro službu Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z místního pro veřejný partnerský vztah a partnerský vztah Microsoftu, budete muset identifikovat překladu adres IP adresy, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. IP adresy pro překlad adres (NAT) používané v partnerských vztazích s Microsoftem poskytuje zákazník nebo poskytovatel služby. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Chcete-li umožňovat komunikaci ze váš okruh ke službě Azure SQL Database, musíte vytvořit pravidla sítě protokolu IP pro veřejné IP adresy vaší NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>Dopady odebrání "Povolit Azure services pro přístup k serveru.

Mnoho uživatelů chcete odebrat **služby Azure umožňují přístup k serveru** ze svých serverů SQL Azure a nahraďte ji metodou pravidlo brány Firewall virtuální sítě.
Ale odebrání to ovlivní následující funkce:

### <a name="import-export-service"></a>Služba import exportu

Služba Azure SQL Database importovat exportu běží na virtuálních počítačích v Azure. Tyto virtuální počítače nejsou ve vaší virtuální síti a proto získat integrační balíček Azure při připojování k vaší databázi. Na odebrání **služby Azure umožňují přístup k serveru** tyto virtuální počítače nebudou moci přistupovat k vaší databáze.
Tento problém můžete obejít. Spustit import souboru BACPAC nebo exportovat přímo v kódu s použitím rozhraní API DACFx. Ujistěte se, že to je nasazen ve virtuálním počítači, který je v podsíti virtuální sítě, u kterého nastavíte pravidlo brány firewall.

### <a name="sql-database-query-editor"></a>Editor dotazů SQL Database

Editor dotazů Azure SQL Database se nasadí na virtuálních počítačích v Azure. Tyto virtuální počítače nejsou ve vaší virtuální síti. Proto virtuální počítače získají integrační balíček Azure při připojování k vaší databázi. Na odebrání **služby Azure umožňují přístup k serveru**, tyto virtuální počítače nebudou moci přistupovat k vaší databáze.

### <a name="table-auditing"></a>Auditování tabulek

V současné době existují dva způsoby, jak povolit auditování pro SQL Database. Auditování tabulek selže po povolení koncových bodů služby na serveru SQL Azure. Zmírnění dopadů zde je přejít na auditování objektů Blob.

### <a name="impact-on-data-sync"></a>Dopad na synchronizace dat

Azure SQL Database má funkce synchronizace dat, která se připojuje k vaší databáze pomocí IP adresy Azure. Při použití koncové body služby, je pravděpodobné, že se vypne **služby Azure umožňují přístup k serveru** přístup ke svému logickému serveru. Tímto přerušíte funkce synchronizace Data.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Dopad koncové body služby virtuální sítě pomocí služby Azure storage

Azure Storage implementoval stejné funkce, která vám umožní omezit připojení k vašemu účtu Azure Storage. Pokud se rozhodnete tuto funkci používat s účtem Azure Storage, který používá Azure SQL Server, můžete spustit do problémy. Následuje seznam a diskuzi o Azure SQL Database a Azure SQL Data Warehouse funkce, které jsou ovlivněné tímto objektem.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse PolyBase

PolyBase se běžně používá k načtení dat do Azure SQL Data Warehouse z účtů služby Azure Storage. Pokud účet služby Azure Storage, která se načítají data z omezuje přístup jenom na sadu podsítí virtuální sítě, dojde k přerušení připojení z PolyBase k účtu. Umožňující použití obou PolyBase importovat a exportovat scénáře s Azure SQL Data Warehouse připojení k Azure Storage, která je zabezpečena k virtuální síti, postupujte podle kroků uvedených dole:

#### <a name="prerequisites"></a>Požadavky
1.  Instalace Azure Powershellu pomocí tohoto [průvodce](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
2.  Pokud máte účet pro obecné účely v1 a blob storage, je nutné nejprve upgradovat na v2 pro obecné účely použití této funkce [průvodce](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Musíte mít **Povolit důvěryhodné služby Microsoftu pro přístup k tomuto účtu úložiště** zapnuté pod účtem služby Azure Storage **brány firewall a virtuální sítě** nabídky nastavení. Projít tento [průvodce](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) Další informace.
 
#### <a name="steps"></a>Kroky
1.  V prostředí PowerShell **zaregistrovat logického SQL serveru** s Azure Active Directory (AAD):

    ```powershell
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId your-subscriptionId
    Set-AzureRmSqlServer -ResourceGroupName your-logical-server-resourceGroup -ServerName your-logical-servername -AssignIdentity
    ```
    
 1. Vytvoření **pro obecné účely v2 účtu úložiště** použití této funkce [průvodce](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

    > [!NOTE]
    > - Pokud máte účet pro obecné účely v1 a blob storage, je nutné **nejprve upgradovat na v2** použití této funkce [průvodce](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
    > - Známé problémy s Azure Data Lake Storage Gen2 najdete to [průvodce](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1.  V rámci účtu úložiště, přejděte do **řízení přístupu (IAM)** a klikněte na tlačítko **přidat přiřazení role**. Přiřadit **Přispěvatel dat objektu Blob služby Storage (Preview)** role RBAC ke svému logickému SQL serveru.

    > [!NOTE] 
    > Tento krok lze provést pouze členové s oprávněními vlastníka. Různé předdefinované role pro prostředky Azure, najdete to [průvodce](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1.  **Polybase připojením k účtu Azure Storage:**

    1. Vytvoření databáze **[hlavní klíč](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?view=sql-server-2017)** Pokud jste ještě nevytvořili dříve:
        ```SQL
        CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
        ```
    
    1. Vytvoření přihlašovacích údajů s rozsahem databáze s **IDENTITY = "Identita spravované služby"**:

        ```SQL
        CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
        ```
        > [!NOTE] 
        > - Není nutné zadat tajný kód se přístupový klíč k úložišti Azure, protože tento mechanismus využívá [Identity spravované](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pod pokličkou.
        > - Název IDENTITY by měl být **Managed Service Identity** PolyBase připojení k práci s účtem úložiště Azure pro zabezpečené virtuální síti.    
    
    1. Vytvoření externího zdroje dat s abfss: / / schéma pro připojení k účtu úložiště pro obecné účely v2 používáním funkce PolyBase:

        ```SQL
        CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
        ```
        > [!NOTE] 
        > - Pokud už máte externí tabulky přidružené k účtu pro obecné účely v1 nebo blob storage, by měly nejprve vyřadit tyto externí tabulky a pak vyřadit odpovídající externí zdroj dat. Pak vytvoříte externí zdroj dat se abfss: / / schéma připojení k účtu úložiště pro obecné účely v2, jak je uvedeno výše a znovu vytvořit všechny externí tabulky, které pomocí tohoto nového zdroje externí data. Můžete použít [generovat a Průvodce publikováním skripty](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard?view=sql-server-2017) vygenerovat vytvořit skript pro externí tabulky pro snadné.
        > - Další informace o abfss: / / schéma, přečtěte si toto [průvodce](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
        > - Další informace o CREATE EXTERNAL DATA SOURCE, najdete to [průvodce](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
    1. Dotaz jako normální použití [externí tabulky](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database auditování objektů Blob

Auditování objektů BLOB nahraje protokoly auditu do účtu úložiště. Pokud tento účet úložiště používá funkce koncových bodů služby virtuální sítě dojde k přerušení připojení ze služby Azure SQL Database k účtu úložiště.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Přidání pravidla brány Firewall virtuální sítě k vašemu serveru neaktivuje na koncové body služby virtuální sítě

Dávno předtím, než tuto funkci došlo k rozšíření, jste byli vyzváni k zapnutí koncové body služby virtuální sítě předtím, než je možné implementovat živé pravidlo virtuální sítě v bráně Firewall. Koncové body související dané podsíti virtuální sítě ke službě Azure SQL Database. Od ledna 2018, můžou teď obejít tento požadavek, tak, že nastavíte, ale **IgnoreMissingVNetServiceEndpoint** příznak.

Pouze nastavení pravidla brány Firewall nepomůže zabezpečení serveru. Musíte také zapnout koncové body služby virtuální sítě pro zabezpečení, než se projeví. Když zapnete koncové body služby, prostředí VNet subnet až do dokončení přechodu z vypnout na výpadky. To platí zejména v souvislosti s velké virtuální sítě. Můžete použít **IgnoreMissingVNetServiceEndpoint** příznak ke snížení nebo eliminaci výpadek během přechodu.

Můžete nastavit **IgnoreMissingVNetServiceEndpoint** příznak pomocí prostředí PowerShell. Podrobnosti najdete v tématu [prostředí PowerShell k vytvoření koncového bodu služby virtuální sítě a pravidlo pro službu Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Chyby 40914 a 40615

Chyba připojení 40914 má vztah k *pravidel virtuální sítě*, jak je určeno v podokně brány Firewall na webu Azure Portal. Chyba 40615 je podobné, s výjimkou má vztah k *IP adresa pravidla* v bráně Firewall.

### <a name="error-40914"></a>Chyba 40914

*Text zprávy:* Nejde otevřít server "*[název_serveru]*' požadovaný v přihlášení. Klient není povolen přístup k serveru.

*Popis chyby:* Klient je v podsíti, která má koncové body serveru virtuální sítě. Ale nemá žádné pravidlo virtuální sítě, která uděluje k podsíti práva ke komunikaci s databází SQL serveru Azure SQL Database.

*Řešení chyb:* V podokně brány Firewall na webu Azure portal pomocí ovládacího prvku pravidla virtuální sítě k [přidáte pravidlo virtuální sítě](#anchor-how-to-by-using-firewall-portal-59j) pro podsíť.

### <a name="error-40615"></a>Chyba 40615

*Text zprávy:* Nejde otevřít server "{0}' požadovaný v přihlášení. Klient s IP adresou{1}' nemá povolený přístup k serveru.

*Popis chyby:* Klient se pokouší o připojení z IP adresy, která není autorizován pro připojení k serveru Azure SQL Database. Brána firewall serveru nemá žádné pravidlo adresy IP, která umožňuje klientům komunikovat z dané IP adresy do služby SQL Database.

*Řešení chyb:* Zadejte IP adresu klienta jako pravidlo IP. To lze proveďte pomocí podokna brány Firewall na webu Azure Portal.

Seznam chybových zpráv SQL Database je zdokumentován [tady][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portálu může vytvořit pravidlo virtuální sítě

Tato část ukazuje, jak můžete používat [webu Azure portal] [ http-azure-portal-link-ref-477t] k vytvoření *pravidlo virtuální sítě* ve službě Azure SQL Database. Pravidlo informuje tak, aby přijímal komunikaci z konkrétní podsítě, která byla označena jako databáze SQL *koncový bod služby virtuální sítě*.

> [!NOTE]
> Pokud chcete přidat koncový bod služby virtuální sítě pravidla brány firewall vašeho serveru Azure SQL Database, nejprve ověřte koncové body jsou zapnuté pro podsíť služby.
>
> Koncové body služby nejsou zapnuta pro podsíť, na portálu žádostí je chcete povolit. Klikněte na tlačítko **povolit** tlačítko ve stejném okně, ve kterém můžete přidat pravidlo.

## <a name="powershell-alternative"></a>Alternativní prostředí PowerShell

Skript prostředí PowerShell můžete také vytvořit pravidla virtuální sítě. Zásadní rutiny **New-AzureRmSqlServerVirtualNetworkRule**. Pokud chcete, přečtěte si téma [prostředí PowerShell k vytvoření koncového bodu služby virtuální sítě a pravidlo pro službu Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativní rozhraní REST API

Rutiny Powershellu pro virtuální síť SQL akce interně, volání rozhraní REST API. Rozhraní REST API můžete volat přímo.

- [Pravidla virtuální sítě: Operace][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Požadavky

Podsíť, která je označena jako konkrétní koncový bod služby virtuální sítě musíte již mít *název typu* relevantní pro Azure SQL Database.

- Název typu relevantní koncový bod je **Microsoft.Sql**.
- Pokud vaše podsíť nemusí být označená pomocí názvu typu, přečtěte si téma [ověřte vaše podsíť je koncový bod][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure portal kroky

1. Přihlaste se na web [Azure Portal][http-azure-portal-link-ref-477t].

2. Přejděte na portál a **SQL servery** &gt; **Brána Firewall / virtuální sítě**.

3. Nastavte **povolit přístup ke službám Azure** ovládacího prvku na hodnotu OFF.

    > [!IMPORTANT]
    > Pokud necháte ovládacího prvku na hodnotu ON, váš server Azure SQL Database přijímá sdělení jakoukoli podsíť. Opuštění ovládacího prvku na hodnotu ON, může být nadměrné přístupu z hlediska zabezpečení. Funkce koncového bodu služby Microsoft Azure Virtual Network ve spolupráci s funkcí pravidlo virtuální sítě služby SQL Database, můžete snížit společně zabezpečení plochy.

4. Klikněte na tlačítko **+ přidat existující** ovládacího **virtuální sítě** oddílu.

    ![Klikněte na Přidat existující (zpravidla SQL endpoint podsítě).][image-portal-firewall-vnet-add-existing-10-png]

5. Na novém **Create/Update** podokno, vyplňte ovládacích prvků s názvy vašich prostředků Azure.

    > [!TIP]
    > Je nutné uvést správné **předponu adresy** pro vaši podsíť. Hodnotu lze najít na portálu.
    > Přejděte **všechny prostředky** &gt; **všechny typy** &gt; **virtuální sítě**. Filtr zobrazí vaše virtuální sítě. Klikněte na virtuální síť a pak klikněte na tlačítko **podsítě**. **Rozsah adres** sloupec má předponu adresy, budete potřebovat.

    ![Přejít k vyplnění polí pro nové pravidlo.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klikněte na tlačítko **OK** tlačítko v dolní části podokna.

7. V podokně brány firewall najdete v článku výsledné pravidlo virtuální sítě.

    ![V podokně brány firewall najdete v článku nové pravidlo.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Na tato pravidla se vztahují následující stavy nebo stavy:
> - **Připravené:** Označuje, že operace, která jste spustili proběhla úspěšně.
> - **Nezdařilo se:** Označuje, že operace, které jste spustili se nezdařila.
> - **Odstranit:** Pouze platí pro operaci odstranění zopakovat a označuje, že pravidlo byl odstraněn a už neplatí.
> - **Probíhá zpracování:** Označuje, že operace probíhá. Staré pravidlo během operace je v tomto stavu.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Související články

- [Koncové body služby virtuální sítě Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Pravidla brány firewall na úrovni serveru a databáze Azure SQL Database][sql-db-firewall-rules-config-715d]

Funkce pravidlo virtuální sítě pro službu Azure SQL Database jsou dostupné v pozdní. září 2017.

## <a name="next-steps"></a>Další postup

- [Použití Powershellu k vytvoření koncového bodu služby virtuální sítě a pravidlo virtuální sítě pro službu Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Pravidla virtuální sítě: Operace] [ rest-api-virtual-network-rules-operations-862r] pomocí rozhraní REST API

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
