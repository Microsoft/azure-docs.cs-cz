---
title: Koncové body sítě virtuální sítě a pravidla pro jednu a sdružené databáze
description: Označte podsíť jako koncový bod služby virtuální sítě. Pak koncový bod jako pravidlo virtuální sítě pro ACL databáze Azure SQL. Sql Database pak přijímá komunikaci ze všech virtuálních počítačů a dalších uzlů v podsíti.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 7032f9e8f57ea9400bf6a92f89b13fa1866f8fc1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414395"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Použití koncových bodů a pravidel služby virtuální sítě pro databázové servery

*Pravidla virtuální sítě* jsou jedna funkce zabezpečení brány firewall, která určuje, zda databázový server pro jednotlivé databáze a elastické fondy v [Azure SQL Database](sql-database-technical-overview.md) nebo pro vaše databáze v [DATABÁZI SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) přijímá komunikaci odesílanou z konkrétních podsítí ve virtuálních sítích. Tento článek vysvětluje, proč je funkce pravidla virtuální sítě někdy nejlepší volbou pro bezpečné povolení komunikace s databází Azure SQL database a sql data warehouse.

> [!IMPORTANT]
> Tento článek se vztahuje na Server Azure SQL a databáze SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse. Tento článek se *nevztahuje* na nasazení **spravované instance** v Azure SQL Database, protože nemá koncový bod služby s ním spojené.

Chcete-li vytvořit pravidlo virtuální sítě, musí být nejprve [koncový bod služby virtuální sítě,][vm-virtual-network-service-endpoints-overview-649d] na který má pravidlo odkazovat.

## <a name="how-to-create-a-virtual-network-rule"></a>Jak vytvořit pravidlo virtuální sítě

Pokud vytvoříte pouze pravidlo virtuální sítě, můžete přeskočit na kroky a vysvětlení [dále v tomto článku](#anchor-how-to-by-using-firewall-portal-59j).

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Podrobnosti o pravidlech virtuální sítě

Tato část popisuje několik podrobností o pravidlech virtuální sítě.

### <a name="only-one-geographic-region"></a>Pouze jedna zeměpisná oblast

Každý koncový bod služby virtuální sítě se vztahuje pouze na jednu oblast Azure. Koncový bod neumožňuje jiné oblasti přijímat komunikaci z podsítě.

Všechna pravidla virtuální sítě je omezena na oblast, která jeho základní koncový bod se vztahuje.

### <a name="server-level-not-database-level"></a>Na úrovni serveru, nikoli na úrovni databáze

Každé pravidlo virtuální sítě se vztahuje na celý server Azure SQL Database, nejen na jednu konkrétní databázi na serveru. Jinými slovy pravidlo virtuální sítě platí na úrovni serveru, nikoli na úrovni databáze.

- Naproti tomu pravidla IP mohou platit na obou úrovních.

### <a name="security-administration-roles"></a>Role správy zabezpečení

V správě koncových bodů služby virtuální sítě dochází k oddělení rolí zabezpečení. Akce je vyžadována z každé z následujících rolí:

- **Správce sítě:** &nbsp; Zapněte koncový bod.
- **Správce databáze:** &nbsp; Aktualizujte seznam řízení přístupu (ACL) a přidejte danou podsíť na databázový server SQL.

*Alternativa RBAC:*

Role správce sítě a správce databáze mají více funkcí, než je potřeba ke správě pravidel virtuální sítě. Je potřeba pouze podmnožinu jejich schopností.

Máte možnost pomocí [řízení přístupu na základě rolí (RBAC)][rbac-what-is-813s] v Azure vytvořit jednu vlastní roli, která má pouze potřebnou podmnožinu možností. Vlastní role lze použít namísto zapojení správce sítě nebo správce databáze. Plocha expozice zabezpečení je nižší, pokud přidáte uživatele do vlastní role, oproti přidání uživatele do dalších dvou hlavních rolí správce.

> [!NOTE]
> V některých případech azure SQL database a podsítě virtuální sítě jsou v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
>
> - Obě předplatná musí být ve stejném tenantovi služby Azure Active Directory.
> - Uživatel má požadovaná oprávnění k zahájení operací, jako je například povolení koncových bodů služby a přidání podsítě virtuální sítě na daný server.
> - Obě předplatná musí mít registrovaného zprostředkovatele Microsoft.Sql.

## <a name="limitations"></a>Omezení

Pro Azure SQL Database má funkce pravidel virtuální sítě následující omezení:

- V bráně firewall pro databázi SQL odkazuje každé pravidlo virtuální sítě na podsíť. Všechny tyto odkazované podsítě musí být hostovány ve stejné geografické oblasti, která je hostitelem databáze SQL.

- Každý server Azure SQL Database může mít až 128 položek ACL pro danou virtuální síť.

- Pravidla virtuální sítě platí jenom pro virtuální sítě Azure Resource Manager; a nikoli do klasických sítí [modelů nasazení.][arm-deployment-model-568f]

- Zapnutí koncových bodů služby virtuální sítě do Azure SQL Database také umožňuje koncové body pro služby MySQL a PostgreSQL Azure. Však s koncovými body ON, pokusy o připojení z koncových bodů do mysql nebo PostgreSQL instance může selhat.
  - Základním důvodem je, že MySQL a PostgreSQL pravděpodobně nemají nakonfigurované pravidlo virtuální sítě. Je nutné nakonfigurovat pravidlo virtuální sítě pro Azure Database pro MySQL a PostgreSQL a připojení bude úspěšné.

- V bráně firewall se rozsahy adres IP vztahují na následující síťové položky, ale pravidla virtuální sítě ne:
  - [Virtuální privátní síť site-to-site (S2S) (VPN)][vpn-gateway-indexmd-608y]
  - Místní přes [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Důležité informace při používání koncových bodů služby

Při použití koncových bodů služby pro Azure SQL Database, zkontrolujte následující aspekty:

- **Odchozí do veřejných IP ip adresy Azure SQL Database je vyžadováno:** Skupiny zabezpečení sítě (NSGs) musí být otevřeny pro IP adresy Azure SQL Database, aby bylo možné připojení. Můžete to provést pomocí [značek služby](../virtual-network/security-overview.md#service-tags) NSG pro Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z vašich prostor, pro veřejný partnerský vztah nebo partnerský vztah Microsoftu, budete muset identifikovat IP adresy NAT, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. IP adresy pro překlad adres (NAT) používané v partnerských vztazích s Microsoftem poskytuje zákazník nebo poskytovatel služby. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Chcete-li povolit komunikaci z okruhu do azure sql database, musíte vytvořit pravidla sítě IP pro veřejné IP adresy vašeho NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Dopad používání koncových bodů služby Virtuální sítě s úložištěm Azure

Azure Storage implementoval stejnou funkci, která umožňuje omezit připojení k účtu Azure Storage. Pokud se rozhodnete používat tuto funkci s účtem Azure Storage, který používá Azure SQL Server, můžete se dostat do problémů. Dále je seznam a diskuse o Azure SQL Database a Azure SQL Data Warehouse funkce, které jsou ovlivněny tímto.

### <a name="azure-sql-data-warehouse-polybase"></a>Polybase datového skladu Azure SQL

PolyBase se běžně používá k načtení dat do Azure SQL Data Warehouse z účtů úložiště Azure. Pokud účet Azure Storage, který načítáte data z omezení přístupu jenom na sadu podsítí virtuální sítě, připojení z PolyBase k účtu se přeruší. Chcete-li povolit scénáře importu i exportu PolyBase pomocí Azure SQL Data Warehouse, který se připojuje k Azure Storage, který je zabezpečený k virtuální síti, postupujte podle následujících kroků:

#### <a name="prerequisites"></a>Požadavky

- Nainstalujte Azure PowerShell pomocí této [příručky](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Pokud máte účet úložiště pro obecné účely v1 nebo objektblo, musíte nejprve upgradovat na obecný účel v2 pomocí této [příručky](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
- Musíte mít **možnost Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** zapnuté v nabídce Firewally účtů Azure Storage a Nastavení **virtuálních sítí.** Další informace naleznete v této [příručce.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Kroky

1. V PowerShellu **zaregistrujte azure SQL server** hostující instanci Azure SQL Data Warehouse pomocí Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Vytvořte **účet úložiště pro obecné účely v2** pomocí této [příručky](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Pokud máte účet úložiště pro obecné účely v1 nebo objektblo, musíte **nejprve upgradovat na v2** pomocí této [příručky](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Známé problémy s Azure Data Lake Storage Gen2, naleznete v této [příručce](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).

1. V části účet úložiště přejděte na **příkaz Řízení přístupu (IAM)** a klikněte na **Přidat přiřazení role**. Přiřaďte roli RBAC **přispěvatele dat objektu blob úložiště** k vašemu Azure SQL Serveru, který hostuje váš Datový sklad Azure SQL, který jste zaregistrovali ve službě Azure Active Directory (AAD) jako v kroku#1.

   > [!NOTE]
   > Tento krok mohou provést pouze členové s oprávněním vlastníka. Různé předdefinované role pro prostředky Azure najdete v této [příručce](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Připojení Polybase k účtu Azure Storage:**

   1. Vytvořte **[hlavní klíč](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** databáze, pokud jste ho nevytvořili dříve:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Vytvořit pověření s rozsahem databáze s **identitou = Identita spravované služby**:

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - Není nutné zadávat SECRET s klíčem přístupu k úložišti Azure Storage, protože tento mechanismus používá [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pod kryty.
       > - Název identity by měl být **"Identita spravované služby"** pro připojení PolyBase pro práci s účtem úložiště Azure zabezpečené ho do virtuální sítě.

   1. Vytvořte externí `abfss://` zdroj dat pomocí schématu pro připojení k účtu úložiště pro obecné účely v2 pomocí PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - Pokud už máte externí tabulky přidružené k účtu úložiště pro obecné účely v1 nebo objektu blob, měli byste nejprve tyto externí tabulky přetáhnout a potom vynechat odpovídající externí zdroj dat. Pak vytvořte externí `abfss://` zdroj dat pomocí schématu, které se připojuje k účtu úložiště pro obecné účely v2, jak je uvedeno výše, a znovu vytvořte všechny externí tabulky pomocí tohoto nového externího zdroje dat. Pomocí [Průvodce generováním a publikováním skriptů](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) můžete snadno generovat skripty pro vytváření pro všechny externí tabulky.
       > - Další informace `abfss://` o schématu naleznete v této [příručce](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Další informace o vytvoření externího zdroje dat naleznete v této [příručce](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Dotaz jako obvykle pomocí [externích tabulek](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Auditování objektů blob databáze Azure SQL

Objekt blob auditování odešle protokoly auditu do vlastního účtu úložiště. Pokud tento účet úložiště používá funkci koncových bodů služby VNet, připojení z Azure SQL Database k účtu úložiště se přeruší.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Přidání pravidla brány firewall virtuální sítě na server bez zapnutí koncových bodů služby Virtuální sítě

Před dlouhou dobou, než byla tato funkce vylepšena, jste museli zapnout koncové body služby virtuální sítě, než jste mohli implementovat pravidlo živé virtuální sítě v bráně firewall. Koncové body související dané podsítě virtuální sítě do databáze Azure SQL. Ale teď od ledna 2018, můžete obejít tento požadavek nastavením **IgnorMissingVNetServiceEndpoint** příznak.

Pouhé nastavení pravidla brány firewall nepomáhá zabezpečit server. Musíte také zapnout koncové body služby virtuální sítě, aby se zabezpečení projevilo. Když zapnete koncové body služby, vaše podsíť virtuální sítě předpokládá prostoje, dokud nedokončí přechod z Vypnuto na Zapnuto. To platí zejména v kontextu velkých virtuálních sítí. Příznak **IgnoreMissingVNetServiceEndpoint** můžete použít ke snížení nebo odstranění prostojů během přechodu.

Příznak **IgnoreMissingVNetServiceEndpoint** můžete nastavit pomocí prostředí PowerShell. Podrobnosti najdete v [tématu PowerShell vytvořit koncový bod služby virtuální sítě a pravidlo pro Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Chyby 40914 a 40615

Chyba připojení 40914 se týká *pravidel virtuální sítě*, jak je určeno v podokně brány firewall na webu Azure Portal. Chyba 40615 je podobná, s výjimkou, že se týká *pravidel IP adres* v bráně firewall.

### <a name="error-40914"></a>Chyba 40914

*Text zprávy:* Nelze otevřít server [*název_serveru]*' požadovaný přihlášením. Klient nemá přístup k serveru.

*Popis chyby:* Klient je v podsíti, která má koncové body virtuálního síťového serveru. Azure SQL Database ale nemá žádné pravidlo virtuální sítě, které dává podsíti oprávnění ke komunikaci s SQL Database.

*Řešení chyb:* V podokně Brány firewall na webu Portál Azure [přidejte pravidlo virtuální sítě](#anchor-how-to-by-using-firewall-portal-59j) pro podsíť pomocí ovládacího prvku pravidla virtuální sítě.

### <a name="error-40615"></a>Chyba 40615

*Text zprávy:* Nelze otevřít{0}server ' ' požadované přihlášením. Klient s IP{1}adresou ' ' nemá přístup k serveru.

*Popis chyby:* Klient se pokouší připojit z IP adresy, která není autorizovaná k připojení k serveru Azure SQL Database. Brána firewall serveru neobsahuje žádné pravidlo IP adres, které by klientovi umožňovalo komunikovat se službou SQL Database z dané IP adresy.

*Řešení chyb:* Zadejte IP adresu klienta jako pravidlo IP adresy. To provedete v podokně Brána firewall na webu Azure Portal.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portál může vytvořit pravidlo virtuální sítě.

Tato část ukazuje, jak můžete použít [portál Azure][http-azure-portal-link-ref-477t] k vytvoření pravidla *virtuální sítě* v azure sql database. Pravidlo říká databázi SQL, aby přijímala komunikaci z určité podsítě, která byla označena jako *koncový bod služby virtuální sítě*.

> [!NOTE]
> Pokud máte v úmyslu přidat koncový bod služby do pravidel brány firewall virtuální sítě vašeho serveru Azure SQL Database, nejprve se ujistěte, že koncové body služby jsou zapnuté pro podsíť.
>
> Pokud koncové body služby nejsou pro podsíť zapnuté, portál vás požádá o povolení. Klepněte na tlačítko **Povolit** ve stejném okně, ke kterému přidáte pravidlo.

## <a name="powershell-alternative"></a>Alternativa prostředí PowerShell

Skript může také vytvořit pravidla virtuální sítě pomocí rutiny prostředí PowerShell **New-AzSqlServerVirtualNetworkRule** nebo [vytvoření síťové sítě AZ](/cli/azure/network/vnet#az-network-vnet-create). Pokud máte zájem, přečtěte si [článek PowerShell a vytvořte koncový bod služby virtuální sítě a pravidlo pro Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Rest API alternativa

Rutiny prostředí PowerShell pro akce virtuální sítě SQL interně volají rozhraní REST API. Můžete volat REST API přímo.

- [Pravidla virtuální sítě: Operace][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Požadavky

Musíte už mít podsíť, která je označena konkrétní *název* koncového bodu služby Virtuální síť relevantní pro Azure SQL Database.

- Příslušný název typu koncového bodu je **Microsoft.Sql**.
- Pokud podsíť nemusí být označena názvem typu, přečtěte si část [Ověření, že podsíť je koncový bod][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Kroky portálu Azure

1. Přihlaste se k webu [Azure Portal][http-azure-portal-link-ref-477t].

2. Vyhledejte a vyberte **servery SQL**a vyberte server. V části **Zabezpečení**vyberte **brány firewall a virtuální sítě**.

3. Nastavte ovládací prvek **Povolit přístup ke službám Azure** na VYPNUTO.

    > [!IMPORTANT]
    > Pokud ponecháte ovládací prvek nastavený na ZAPNUTO, váš server Azure SQL Database přijímá komunikaci z libovolné podsítě uvnitř hranice Azure, tj. Ponechání ovládacího prvku nastaveného na zapnuto může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Virtuální síť Microsoft Azure v koordinaci s funkcí pravidla virtuální sítě databáze SQL database může společně snížit plochu zabezpečení.

4. Klikněte na ovládací prvek **+ Přidat existující** v části Virtuální **sítě.**

    ![Klikněte na přidat existující (koncový bod podsítě, jako pravidlo SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. V novém podokně **Vytvořit a aktualizovat** vyplňte ovládací prvky názvy prostředků Azure.

    > [!TIP]
    > Je nutné uvést správnou **předponu adresy** pro podsíť. Hodnotu najdete na portálu.
    > Navigace **Všechny prostředky** &gt; **Všechny typy** &gt; **Virtuální sítě**. Filtr zobrazí vaše virtuální sítě. Klikněte na virtuální síť a potom klikněte na **Podsítě**. Sloupec **Rozsah adres** obsahuje předponu Adresa, kterou potřebujete.

    ![Vyplňte pole pro nové pravidlo.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klikněte na tlačítko **OK** v dolní části podokna.

7. Podívejte se na výsledné pravidlo virtuální sítě v podokně brány firewall.

    ![Podívejte se na nové pravidlo v podokně brány firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Na pravidla se vztahují následující stavy nebo stavy:
>
> - **Připraveno:** Označuje, že operace, kterou jste zahájili, byla úspěšná.
> - **Nezdařilo se:** Označuje, že operace, kterou jste zahájili, selhala.
> - **Odstraněno:** Platí pouze pro operaci Odstranit a označuje, že pravidlo bylo odstraněno a již neplatí.
> - **Probíhá:** Označuje, že probíhá operace. Staré pravidlo platí, když je operace v tomto stavu.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Související články

- [Koncové body virtuální síťové služby Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Pravidla brány firewall na úrovni serveru Azure SQL Database a databáze][sql-db-firewall-rules-config-715d]

Funkce pravidla virtuální sítě pro Azure SQL Database byla dostupná koncem září 2017.

## <a name="next-steps"></a>Další kroky

- [Pomocí PowerShellu vytvořte koncový bod virtuální síťové služby a pak pravidlo virtuální sítě pro Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Pravidla virtuální sítě: Operace][rest-api-virtual-network-rules-operations-862r] s rest API

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
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
