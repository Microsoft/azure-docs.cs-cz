---
title: Koncové body a pravidla virtuální sítě pro databáze v Azure SQL Database
description: Označte podsíť jako koncový bod služby Virtual Network. Pak koncový bod jako pravidlo virtuální sítě pro seznam ACL vaší databáze. Vaše databáze pak přijme komunikaci ze všech virtuálních počítačů a dalších uzlů v podsíti.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 5c5276f11da687f14630bafd007532d172ef3737
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990801"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Použití koncových bodů a pravidel služby virtuální sítě pro servery v Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Pravidla virtuální sítě* jsou jedna funkce zabezpečení brány firewall, která určuje, jestli Server pro vaše databáze a elastické fondy v [Azure SQL Database](sql-database-paas-overview.md) nebo pro databáze v [Azure synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) přijímá komunikaci, která se odesílají z konkrétních podsítí ve virtuálních sítích. Tento článek vysvětluje, proč je funkce pravidla virtuální sítě někdy nejlepší volbou pro bezpečné povolení komunikace s databází v Azure SQL Database a Azure synapse Analytics (dřív SQL Data Warehouse).

> [!NOTE]
> Tento článek se týká Azure SQL Database a Azure synapse Analytics. Pro zjednodušení pojem "Database" odkazuje jak na databáze Azure SQL Database, tak na Azure synapse Analytics. Podobně všechny odkazy na server se odkazují na [logický SQL Server](logical-servers.md) , který je hostitelem Azure SQL Database a Azure synapse Analytics.

Aby bylo možné vytvořit pravidlo virtuální sítě, musí být nejprve [koncovým bodem služby virtuální sítě][vm-virtual-network-service-endpoints-overview-649d] pro pravidlo, na které se má odkazovat.

## <a name="how-to-create-a-virtual-network-rule"></a>Postup vytvoření pravidla virtuální sítě

Pokud vytvoříte pouze pravidlo virtuální sítě, můžete přeskočit kroky a vysvětlení [dále v tomto článku](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Podrobnosti o pravidlech virtuální sítě

Tato část popisuje několik podrobností o pravidlech virtuální sítě.

### <a name="only-one-geographic-region"></a>Jenom jedna geografická oblast

Každý koncový bod služby Virtual Network se vztahuje jenom na jednu oblast Azure. Koncový bod nepovoluje, aby komunikace z podsítě přijímala jiné oblasti.

Jakékoli pravidlo virtuální sítě je omezené na oblast, na kterou se vztahuje příslušný koncový bod.

### <a name="server-level-not-database-level"></a>Na úrovni serveru, nikoli na úrovni databáze

Každé pravidlo virtuální sítě se vztahuje na celý server, nikoli jenom na jednu konkrétní databázi na serveru. Jinými slovy, pravidlo virtuální sítě se vztahuje na úrovni serveru, nikoli na úrovni databáze.

- Naproti tomu pravidla protokolu IP můžou platit na kterékoli úrovni.

### <a name="security-administration-roles"></a>Role správy zabezpečení

V rámci správy koncových bodů služby Virtual Network je oddělení rolí zabezpečení. Pro každou z následujících rolí se vyžaduje akce:

- **Správce sítě:** &nbsp; Zapněte koncový bod.
- **Správce databáze:** &nbsp; Aktualizujte seznam řízení přístupu (ACL) pro přidání dané podsítě k serveru.

*Alternativa k Azure RBAC:*

Role správce sítě a správce databáze mají více možností, než je nutné ke správě pravidel virtuální sítě. Je potřeba jenom podmnožina jejich schopností.

Máte možnost pomocí [řízení přístupu na základě role Azure (RBAC)][rbac-what-is-813s] v Azure vytvořit jednu vlastní roli, která bude mít jenom nezbytnou podmnožinu funkcí. Vlastní roli můžete použít místo zapojení správce sítě nebo správce databáze. Pokud přidáte uživatele do vlastní role a přidáváte uživatele k ostatním dvěma hlavním rolím Správce, oblast Surface vašeho ohrožení zabezpečení je nižší.

> [!NOTE]
> V některých případech je databáze v Azure SQL Database a virtuální podsíť v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
>
> - Oba odběry musí být ve stejném Azure Active Directory tenantovi.
> - Uživatel má požadovaná oprávnění k zahájení operací, jako je například povolení koncových bodů služby a přidání podsítě virtuální sítě do daného serveru.
> - Oba odběry musí mít zaregistrovaný poskytovatel Microsoft. SQL.

## <a name="limitations"></a>Omezení

Pro Azure SQL Database funkce pravidla virtuální sítě má následující omezení:

- V bráně firewall pro vaši databázi v Azure SQL Database každé pravidlo virtuální sítě odkazuje na podsíť. Všechny tyto odkazované podsítě musí být hostované ve stejné geografické oblasti, která je hostitelem databáze.

- Každý server může mít až 128 položek seznamu řízení přístupu (ACL) pro libovolnou danou virtuální síť.

- Pravidla virtuální sítě se vztahují jenom na Azure Resource Manager virtuální sítě; a ne pro [model nasazení Classic][arm-deployment-model-568f] .

- Zapnutím koncových bodů služby virtuální sítě Azure SQL Database také umožníte koncovým bodům pro služby Azure MySQL a PostgreSQL. S koncovými body v se však pokusy o připojení z koncových bodů do instancí MySQL nebo PostgreSQL mohou selhat.
  - Základním důvodem je, že MySQL a PostgreSQL pravděpodobně nemají nakonfigurováno pravidlo virtuální sítě. Je nutné nakonfigurovat pravidlo virtuální sítě pro Azure Database for MySQL a PostgreSQL a připojení bude úspěšné.

- V bráně firewall se rozsahy IP adres vztahují na následující síťové položky, ale pravidla virtuální sítě ne:
  - [Virtuální privátní síť (VPN) typu Site-to-Site (S2S)][vpn-gateway-indexmd-608y]
  - Místně prostřednictvím [ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-using-service-endpoints"></a>Pokyny k použití koncových bodů služby

Při použití koncových bodů služby pro Azure SQL Database se podívejte na následující skutečnosti:

- **Odchozí IP adresa pro Azure SQL Database veřejné IP adresy se vyžadují**: aby se Azure SQL Database IP adres umožňovalo připojení, musí se otevřít skupiny zabezpečení sítě (skupin zabezpečení sítě). To můžete provést pomocí [značek služby](../../virtual-network/network-security-groups-overview.md#service-tags) NSG pro Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Pokud používáte [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z vašich místních partnerských vztahů nebo partnerských vztahů Microsoftu, budete muset určit IP adresy NAT, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. IP adresy pro překlad adres (NAT) používané v partnerských vztazích s Microsoftem poskytuje zákazník nebo poskytovatel služby. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Aby bylo možné Azure SQL Database komunikaci z okruhu, musíte vytvořit pravidla sítě IP pro veřejné IP adresy vašeho překladu adres (NAT).

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Dopad použití koncových bodů služby virtuální sítě se službou Azure Storage

Ve službě Azure Storage je implementovaná stejná funkce, která umožňuje omezit připojení k vašemu účtu služby Azure Storage. Pokud se rozhodnete tuto funkci využít u účtu služby Azure Storage, který používá služba Azure SQL Database, může dojít k problémům. Dál je seznam a diskuze o funkcích Azure SQL Database a službě Azure synapse Analytics, na které se to týká.

### <a name="azure-synapse-polybase-and-copy-statement"></a>Příkaz Base a COPY pro Azure synapse

Základ a příkaz COPY se běžně používá k načtení dat do služby Azure synapse Analytics z účtů Azure Storage pro příjem dat s vysokou propustností. Pokud účet Azure Storage, ze kterého načítáte data, přistupuje jenom k sadě virtuálních sítí VNet, připojení při použití základny a příkazu COPY pro účet úložiště dojde k přerušení. Pokud chcete povolit scénáře importu a exportu pomocí kopírování a základu s připojením služby Azure synapse Analytics k Azure Storage, která je zabezpečená pro virtuální síť, postupujte podle následujících kroků:

#### <a name="prerequisites"></a>Požadavky

- Pomocí tohoto [průvodce](/powershell/azure/install-az-ps) nainstalujte Azure PowerShell.
- Pokud máte účet úložiště pro obecné účely verze 1 nebo účet úložiště objektů blob, musíte nejprve pomocí tohoto [průvodce](../../storage/common/storage-account-upgrade.md) upgradovat na účet úložiště pro obecné účely verze 2.
- Abyste měli přístup k tomuto účtu úložiště zapnutý, musíte mít **povolené důvěryhodné služby Microsoftu** v nabídce Azure Storage **brány firewall účtů a nastavení virtuálních sítí** . Povolením této konfigurace umožníte, aby se příkaz Base a COPY připojil k účtu úložiště pomocí silného ověřování, kde síťový provoz zůstane v páteřní síti Azure. Další informace najdete v tomto [průvodci](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Postup

1. V prostředí PowerShell **Zaregistrujte server** hostující službu Azure Synapse pomocí Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Pomocí této [příručky](../../storage/common/storage-account-create.md)vytvořte **účet úložiště pro obecné účely v2** .

   > [!NOTE]
   >
   > - Pokud máte účet úložiště pro obecné účely v1 nebo blob, musíte **nejdřív upgradovat na verzi v2** pomocí této [příručky](../../storage/common/storage-account-upgrade.md).
   > - Známé problémy s Azure Data Lake Storage Gen2 najdete v tomto [Průvodci](../../storage/blobs/data-lake-storage-known-issues.md).

1. V části účet úložiště přejděte na **Access Control (IAM)** a vyberte **Přidat přiřazení role**. Přiřaďte roli Azure **Přispěvatel dat objektů BLOB úložiště** k serveru, který je hostitelem služby Azure synapse Analytics, kterou jste zaregistrovali v Azure Active Directory (AAD) jako v kroku #1.

   > [!NOTE]
   > Tento krok můžou provést jenom členové s oprávněním vlastníka v účtu úložiště. Informace o různých předdefinovaných rolích Azure najdete v tomto [Průvodci](../../role-based-access-control/built-in-roles.md).
  
1. **Základní připojení k účtu Azure Storage:**

   1. Pokud jste ještě nevytvořili **[hlavní klíč](/sql/t-sql/statements/create-master-key-transact-sql)** databáze, vytvořte ho:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Vytvořit databázi s rozsahem pověření s **identitou = ' identita spravované služby '**:

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Není nutné zadávat tajný klíč pomocí Azure Storage přístupového klíče, protože tento mechanismus používá [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) v rámci pokrývání.
       > - Název IDENTITY by měl být **Identita spravované služby** , aby připojení k síti s Azure Storagem účtem zabezpečeném pro virtuální síť fungovalo.

   1. Vytvořte externí zdroj dat se `abfss://` schématem pro připojení k účtu úložiště pro obecné účely v2 pomocí základu:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Pokud už máte externí tabulky přidružené k účtu úložiště pro obecné účely v1 nebo blob, měli byste tyto externí tabulky nejdřív vyřadit a pak vyřadit odpovídající externí zdroj dat. Pak vytvořte externí zdroj dat se `abfss://` schématem připojujícím se k účtu úložiště pro obecné účely v2 a znovu vytvořte všechny externí tabulky pomocí tohoto nového externího zdroje dat. Pomocí [Průvodce Generovat a publikovat skripty](/sql/ssms/scripting/generate-and-publish-scripts-wizard) můžete snadno vytvořit vytváření skriptů pro všechny externí tabulky.
       > - Další informace o schématu najdete v `abfss://` tomto [Průvodci](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Další informace o vytvoření externího zdroje dat najdete v tomto [Průvodci](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Dotazování jako normální pomocí [externích tabulek](/sql/t-sql/statements/create-external-table-transact-sql)

### <a name="azure-sql-database-blob-auditing"></a>Auditování Azure SQL Database objektů BLOB

Auditování objektů BLOB přenáší protokoly auditu do vlastního účtu úložiště. Pokud tento účet úložiště používá funkci koncových bodů služby virtuální sítě, dojde k přerušení připojení z Azure SQL Database k účtu úložiště.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Přidání pravidla brány firewall virtuální sítě na server bez zapnutí koncových bodů služby virtuální sítě

Před zvýšením této funkce jste před tím, než v bráně firewall naimplementovali aktivní pravidlo virtuální sítě, bylo nutné zapnout koncové body služby virtuální sítě. Koncové body související s danou sítí VNet-Subnet k databázi v Azure SQL Database. Ale teď od ledna 2018 můžete tento požadavek obejít nastavením příznaku **IgnoreMissingVNetServiceEndpoint** .

Pouze nastavení pravidla brány firewall nezabezpečuje Server. Aby se zabezpečení projevilo, musíte taky zapnout koncové body služby virtuální sítě. Při zapnutí koncových bodů služby ve vaší virtuální síti dojde k výpadku, dokud se přechod neukončí na zapnuto. To platí zejména v kontextu velkých virtuální sítě. Pomocí příznaku **IgnoreMissingVNetServiceEndpoint** můžete snížit nebo odstranit výpadky během přechodu.

Příznak **IgnoreMissingVNetServiceEndpoint** můžete nastavit pomocí prostředí PowerShell. Podrobnosti najdete v tématu [prostředí PowerShell pro vytvoření koncového bodu služby Virtual Network a pravidla pro Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Chyby 40914 a 40615

Chyba připojení 40914 se vztahuje k *pravidlům virtuální sítě*, jak je uvedeno v podokně Brána Firewall v Azure Portal. Chyba 40615 je podobná, s tím rozdílem, že se vztahují na *pravidla IP adres* v bráně firewall.

### <a name="error-40914"></a>Chyba 40914

*Text zprávy:* Nejde otevřít server *[název serveru]*, který požádal o přihlášení. Klient nemá povolený přístup k serveru.

*Popis chyby:* Klient se nachází v podsíti, která má koncové body virtuálního síťového serveru. Server ale nemá žádné pravidlo virtuální sítě, které by dané podsíti získalo právo komunikovat s databází.

*Řešení chyb:* V podokně brána firewall Azure Portal pomocí ovládacího prvku pravidla virtuální sítě [přidejte pravidlo virtuální sítě](#anchor-how-to-by-using-firewall-portal-59j) pro podsíť.

### <a name="error-40615"></a>Chyba 40615

*Text zprávy:* Server ' {0} ' požadovaný přihlášením ' ' nelze otevřít. Klient s IP adresou {1} nemá povolený přístup k serveru.

*Popis chyby:* Klient se pokouší připojit z IP adresy, která nemá autorizaci pro připojení k serveru. Brána firewall serveru nemá žádné pravidlo IP adresy, které klientovi umožňuje komunikovat z dané IP adresy do databáze.

*Řešení chyb:* Zadejte IP adresu klienta jako pravidlo protokolu IP. To provedete v podokně Brána firewall na webu Azure Portal.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="portal-can-create-a-virtual-network-rule"></a>Portál může vytvořit pravidlo virtuální sítě

Tato část ukazuje, jak můžete pomocí [Azure Portal][http-azure-portal-link-ref-477t] vytvořit *pravidlo virtuální sítě* v databázi nástroje v Azure SQL Database. Pravidlo instruuje vaši databázi, aby přijímala komunikaci z konkrétní podsítě, která byla označena jako *koncový bod služby Virtual Network*.

> [!NOTE]
> Pokud máte v úmyslu přidat koncový bod služby pro pravidla brány firewall virtuální sítě vašeho serveru, zajistěte, aby byla pro podsíť zapnutá koncová bodu služby.
>
> Pokud nejsou pro podsíť zapnuté koncové body služby, portál vás vyzve, abyste je povolili. Klikněte na tlačítko **Povolit** ve stejném okně, na kterém přidáte pravidlo.

## <a name="powershell-alternative"></a>Alternativní prostředí PowerShell

Skript může také vytvořit pravidla virtuální sítě pomocí rutiny prostředí PowerShell **New-AzSqlServerVirtualNetworkRule** nebo [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). Pokud vás zajímá, přečtěte si téma [PowerShell a vytvořte koncový bod služby Virtual Network a pravidlo pro Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternativa

Rutiny PowerShellu pro akce virtuální sítě SQL volají interně volání rozhraní REST API. Rozhraní REST API můžete volat přímo.

- [Pravidla Virtual Network: operace][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Požadavky

Musíte už mít podsíť, která je označená konkrétním Virtual Networkm *typu* koncového bodu služby, který je relevantní pro Azure SQL Database.

- Odpovídajícím názvem typu koncového bodu je **Microsoft. SQL**.
- Pokud vaše podsíť nemusí být označená názvem typu, přečtěte si téma [ověření, že je podsíť koncovým bodem][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal kroky

1. Přihlaste se na [Azure Portal][http-azure-portal-link-ref-477t].

2. Vyhledejte a vyberte **SQL servery** a pak vyberte svůj server. V části **zabezpečení** vyberte možnost **brány firewall a virtuální sítě**.

3. Nastavte řízení **Povolení přístupu ke službám Azure** na vypnuto.

    > [!IMPORTANT]
    > Pokud ovládací prvek necháte nastavený na ZAPNUTo, server přijme komunikaci z jakékoli podsítě v rámci hranice Azure, tj. pocházející z jedné z IP adres, která je rozpoznaná jako ta v oblastech definovaných pro datová centra Azure. Ponechání ovládacího prvku na ZAPNUTo může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Microsoft Azure Virtual Network, v koordinaci s funkcí pravidla virtuální sítě SQL Database, společně může snížit oblast zabezpečení.

4. Klikněte na tlačítko **+ Přidat existující** ovládací prvek v části **virtuální sítě** .

    ![Klikněte na Přidat existující (koncový bod podsítě, jako pravidlo SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. V podokně nový **vytvořit/aktualizovat** vyplňte ovládací prvky názvy vašich prostředků Azure.

    > [!TIP]
    > Je nutné zadat správnou **předponu adresy** pro vaši podsíť. Tuto hodnotu můžete najít na portálu.
    > Přejděte na **všechny prostředky** &gt; **všechny typy** &gt; **virtuálních sítí**. Filtr zobrazuje vaše virtuální sítě. Klikněte na svou virtuální síť a potom klikněte na **podsítě**. Sloupec **Rozsah adres** obsahuje předponu adresy, kterou potřebujete.

    ![Vyplňte pole pro nové pravidlo.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klikněte na tlačítko **OK** v dolní části podokna.

7. Prohlédněte si výsledné pravidlo virtuální sítě v podokně brána firewall.

    ![V podokně brána firewall se zobrazí nové pravidlo.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Následující stavy nebo stavy se vztahují na pravidla:
>
> - **Připraveno:** Označuje, že operace, kterou jste iniciovali, byla úspěšná.
> - **Selhalo:** Indikuje, že operace, kterou jste iniciovali, se nezdařila.
> - **Odstraněno:** Platí pouze pro operaci odstranění a označuje, že pravidlo bylo odstraněno a již není použito.
> - **Probíhá zpracování:** Indikuje, že operace probíhá. Původní pravidlo se použije, pokud je operace v tomto stavu.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Související články

- [Koncové body služby virtuální sítě Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Pravidla brány firewall na úrovni serveru a databáze][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Další kroky

- [Pomocí PowerShellu vytvořte koncový bod služby virtuální sítě a potom pravidlo virtuální sítě pro Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Pravidla Virtual Network: operace][rest-api-virtual-network-rules-operations-862r] s rozhraními REST API

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
