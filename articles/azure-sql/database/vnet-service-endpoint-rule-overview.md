---
title: Koncové body a pravidla virtuální sítě pro databáze v Azure SQL Database
description: Označte podsíť jako koncový bod služby virtuální sítě. Pak přidejte koncový bod jako pravidlo virtuální sítě do seznamu ACL pro vaši databázi. Vaše databáze pak přijme komunikaci ze všech virtuálních počítačů a dalších uzlů v podsíti.
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
ms.openlocfilehash: 0dcffe6731c177d1d45c569361fcb200f23af86c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095354"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Použití koncových bodů služeb virtuální sítě a pravidel pro servery služby Azure SQL Database

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Pravidla virtuální sítě* jsou funkcí zabezpečení brány firewall, která určuje, jestli Server pro vaše databáze a elastické fondy v [Azure SQL Database](sql-database-paas-overview.md) nebo pro vaše vyhrazené databáze SQL (dříve SQL DW) ve [službě Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) přijímá komunikaci, která se odesílají z konkrétních podsítí ve virtuálních sítích. Tento článek vysvětluje, proč jsou pravidla virtuální sítě někdy nejlepší volbou pro bezpečné povolení komunikace s databází v SQL Database a Azure synapse Analytics.

> [!NOTE]
> Tento článek se týká SQL Database a Azure synapse Analytics. V zájmu jednoduchosti se pojem *databáze* týká obou databází v SQL Database a Azure synapse Analytics. Podobně všechny odkazy na *Server* odkazují na [logický SQL Server](logical-servers.md) , který je hostitelem SQL Database a Azure synapse Analytics.

Aby bylo možné vytvořit pravidlo virtuální sítě, musí být nejprve [koncovým bodem služby virtuální sítě][vm-virtual-network-service-endpoints-overview-649d] pro pravidlo, na které se má odkazovat.

## <a name="create-a-virtual-network-rule"></a>Vytvoření pravidla virtuální sítě

Pokud chcete vytvořit pouze pravidlo virtuální sítě, můžete přeskočit kroky a vysvětlení [dále v tomto článku](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Podrobnosti o pravidlech virtuální sítě

Tato část popisuje několik podrobností o pravidlech virtuální sítě.

### <a name="only-one-geographic-region"></a>Jenom jedna geografická oblast

Všechny koncové body služby virtuální sítě se vztahují jenom na jednu oblast Azure. Koncový bod nepovoluje, aby komunikace z podsítě přijímala jiné oblasti.

Jakékoli pravidlo virtuální sítě je omezené na oblast, na kterou se vztahuje příslušný koncový bod.

### <a name="server-level-not-database-level"></a>Úroveň serveru, nikoli databáze

Každé pravidlo virtuální sítě se vztahuje na celý server, nikoli jenom na jednu konkrétní databázi na serveru. Jinými slovy pravidla virtuální sítě platí na úrovni serveru, ne na úrovni databáze.

Naproti tomu pravidla protokolu IP můžou platit na kterékoli úrovni.

### <a name="security-administration-roles"></a>Role správy zabezpečení

Ve správě koncových bodů služby virtuální sítě je oddělení rolí zabezpečení. Pro každou z následujících rolí se vyžaduje akce:

- **Správce sítě (role [Přispěvatel sítě](../../role-based-access-control/built-in-roles.md#network-contributor) ):** &nbsp; Zapněte koncový bod.
- **Správce databáze ([SQL Server role přispěvatele](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ):** &nbsp; Aktualizujte seznam řízení přístupu (ACL) pro přidání dané podsítě k serveru.

#### <a name="azure-rbac-alternative"></a>Alternativa Azure RBAC

Role správce sítě a správce databáze mají více možností, než je nutné ke správě pravidel virtuální sítě. Je potřeba jenom podmnožina jejich schopností.

V Azure máte možnost použít [řízení přístupu na základě role (RBAC)][rbac-what-is-813s] a vytvořit jednu vlastní roli, která bude mít jenom nezbytnou podmnožinu funkcí. Vlastní roli můžete použít místo zapojení správce sítě nebo správce databáze. Oblast povrchu při expozici zabezpečení je nižší, pokud přidáte uživatele do vlastní role a přidáte uživatele do dalších dvou hlavních rolí správce.

> [!NOTE]
> V některých případech je databáze v SQL Database a podsíť virtuální sítě v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
>
> - Oba odběry musí být ve stejném tenantovi Azure Active Directory (Azure AD).
> - Uživatel má požadovaná oprávnění k zahájení operací, jako je například povolení koncových bodů služby a přidání podsítě virtuální sítě k danému serveru.
> - Oba odběry musí mít zaregistrovaný poskytovatel Microsoft. SQL.

## <a name="limitations"></a>Omezení

Pro SQL Database funkce pravidla virtuální sítě má následující omezení:

- V bráně firewall pro vaši databázi v SQL Database každé pravidlo virtuální sítě odkazuje na podsíť. Všechny tyto odkazované podsítě musí být hostované ve stejné geografické oblasti, která je hostitelem databáze.
- Každý server může mít až 128 položek seznamu řízení přístupu (ACL) pro libovolnou virtuální síť.
- Pravidla virtuální sítě platí jenom pro Azure Resource Manager virtuální sítě a ne pro sítě [modelu nasazení Classic][arm-deployment-model-568f] .
- Zapnutím koncových bodů služby virtuální sítě SQL Database také umožní koncovým bodům Azure Database for MySQL a Azure Database for PostgreSQL. U koncových bodů nastavených na hodnotu **on** se pokusy o připojení z koncových bodů k vašemu Azure Database for MySQL nebo Azure Database for PostgreSQL instance mohou selhat.
  - Základním důvodem je to, že Azure Database for MySQL a Azure Database for PostgreSQL pravděpodobně nemají nakonfigurováno pravidlo virtuální sítě. Je nutné nakonfigurovat pravidlo virtuální sítě pro Azure Database for MySQL a Azure Database for PostgreSQL a připojení bude úspěšné.
  - Pokud chcete definovat pravidla brány firewall virtuální sítě na logickém serveru SQL, který je už nakonfigurovaný pomocí privátních koncových bodů, nastavte **Odepřít přístup k veřejné síti** na **ne**.
- V bráně firewall se rozsahy IP adres vztahují na následující síťové položky, ale pravidla virtuální sítě ne:
  - [Virtuální privátní síť (VPN) typu Site-to-Site (S2S)][vpn-gateway-indexmd-608y]
  - Místně prostřednictvím [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Pokyny k použití koncových bodů služby

Pokud používáte koncové body služby pro SQL Database, přečtěte si následující skutečnosti:

- **Pro Azure SQL Database veřejné IP adresy se vyžaduje odchozí IP adresa.** Aby bylo možné SQL Database IP adresy, je nutné otevřít skupiny zabezpečení sítě (skupin zabezpečení sítě), aby bylo možné připojení. To můžete provést pomocí [značek služby](../../virtual-network/network-security-groups-overview.md#service-tags) NSG pro SQL Database.

### <a name="expressroute"></a>ExpressRoute

Pokud používáte [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z vašich místních partnerských vztahů nebo partnerských vztahů Microsoftu, budete muset určit IP adresy NAT, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. U partnerského vztahu Microsoftu se používají IP adresy NAT, které poskytuje zákazník nebo poskytovatel služeb. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o překladu adres (NAT) pro veřejné partnerské vztahy ExpressRoute a Microsoftu najdete v tématu [požadavky na překlad adres (NAT) pro veřejný partnerský vztah Azure](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Aby bylo možné SQL Database komunikaci z okruhu, musíte vytvořit pravidla sítě IP pro veřejné IP adresy vašeho překladu adres (NAT).

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Dopad použití koncových bodů služby virtuální sítě s Azure Storage

Ve službě Azure Storage je implementovaná stejná funkce, která umožňuje omezit připojení k vašemu účtu služby Azure Storage. Pokud se rozhodnete tuto funkci použít s účtem Azure Storage, který SQL Database používá, můžete spustit problémy. Dál je seznam a diskuze nad SQL Database a funkcemi analýzy Azure synapse, které jsou ovlivněné.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Základ a příkaz COPY služby Azure synapse Analytics

Základ a příkaz COPY se běžně používají k načítání dat do služby Azure synapse Analytics z účtů Azure Storage pro příjem dat s vysokou propustností. Pokud účet Azure Storage, který načítáte data z omezení, přistupuje pouze k sadě podsítí virtuální sítě, připojení při použití základny a příkazu Kopírovat pro účet úložiště se přeruší. Pokud chcete povolit scénáře importu a exportu pomocí KOPÍROVACÍho a základu služby Azure synapse Analytics, která se připojuje k Azure Storage zabezpečené k virtuální síti, postupujte podle kroků v této části.

#### <a name="prerequisites"></a>Požadavky

- Pomocí [této příručky](/powershell/azure/install-az-ps)nainstalujte Azure PowerShell.
- Pokud máte účet pro obecné účely v1 nebo Azure Blob Storage, musíte nejdřív upgradovat na obecné účely v2 podle kroků v části [upgrade na účet úložiště pro obecné účely verze 2](../../storage/common/storage-account-upgrade.md).
- Abyste měli přístup k tomuto účtu úložiště zapnutý, musíte **Povolit důvěryhodné služby Microsoftu** v nabídce Azure Storage **brány firewall a nastavení virtuálních sítí** . Povolením této konfigurace umožníte, aby se příkaz Base a COPY připojil k účtu úložiště pomocí silného ověřování, kde síťový provoz zůstane v páteřní síti Azure. Další informace najdete v [této příručce](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020. Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Postup

1. Pokud máte samostatný vyhrazený fond SQL, zaregistrujte SQL Server pomocí Azure AD pomocí prostředí PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Tento krok není nutný pro vyhrazené fondy SQL v pracovním prostoru Azure synapse Analytics.

1. Pokud máte pracovní prostor analýzy Azure synapse, zaregistrujte identitu spravovanou systémem v pracovním prostoru:

   1. V Azure Portal přejdete do pracovního prostoru Azure synapse Analytics.
   2. Přejít do podokna **spravované identity** .
   3. Ujistěte se, že je povolená možnost **Povolit kanály** .
   
1. Pomocí postupu v části [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md)vytvořte **účet úložiště pro obecné účely v2** .

   > [!NOTE]
   >
   > - Pokud máte účet pro obecné účely v1 nebo Blob Storage, musíte *nejdřív upgradovat na verzi v2* podle kroků v části [upgrade na účet úložiště pro obecné účely verze 2](../../storage/common/storage-account-upgrade.md).
   > - Známé problémy s Azure Data Lake Storage Gen2 najdete v tématu [známé problémy s Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. V rámci svého účtu úložiště, přejít na **Access Control (IAM)** a vyberte **Přidat přiřazení role**. Přiřaďte roli Azure **Přispěvatel dat objektů BLOB úložiště** k serveru nebo pracovnímu prostoru, který je hostitelem vyhrazeného fondu SQL, který jste zaregistrovali ve službě Azure AD.

   > [!NOTE]
   > Tento krok můžou provést jenom členové s oprávněním vlastníka v účtu úložiště. Informace o různých předdefinovaných rolích Azure najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md).
  
1. Pokud chcete povolit připojení k účtu Azure Storage jako základní:

   1. Pokud jste ještě nevytvořili [hlavní klíč](/sql/t-sql/statements/create-master-key-transact-sql) databáze, vytvořte ho.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Vytvořte pověření v oboru databáze s **identitou = ' identita spravované služby '**.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Není nutné zadávat tajný klíč pomocí Azure Storage přístupového klíče, protože tento mechanismus používá [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) v rámci pokrývání.
       > - Název IDENTITY by měl být **"identita spravované služby"** , aby připojení s účtem Azure Storage zabezpečené k virtuální síti fungovalo.

   1. Vytvořte externí zdroj dat se `abfss://` schématem pro připojení k účtu úložiště pro obecné účely v2 pomocí základu.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Pokud již máte externí tabulky přidružené k účtu v1 nebo Blob Storage pro obecné účely, měli byste tyto externí tabulky vyřadit. Pak vyřaďte odpovídající externí zdroj dat. Potom vytvořte externí zdroj dat se `abfss://` schématem, který se připojuje k účtu úložiště pro obecné účely v2, jak je uvedeno výše. Pak vytvořte všechny externí tabulky znovu pomocí tohoto nového externího zdroje dat. Pomocí [Průvodce Generovat a publikovat skripty](/sql/ssms/scripting/generate-and-publish-scripts-wizard) můžete snadno vytvořit vytváření skriptů pro všechny externí tabulky.
       > - Další informace o `abfss://` schématu najdete v tématu [použití Azure Data Lake Storage GEN2ho identifikátoru URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Další informace o vytvoření externího zdroje dat najdete v [této příručce](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Dotazujte jako normální pomocí [externích tabulek](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>Auditování SQL Database objektů BLOB

Auditování objektů BLOB přenáší protokoly auditu do vlastního účtu úložiště. Pokud tento účet úložiště používá funkci koncových bodů služby virtuální sítě, dojde k přerušení připojení z SQL Database k účtu úložiště.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Přidání pravidla brány firewall virtuální sítě na server

Před zvýšením této funkce jste před tím, než v bráně firewall naimplementovali aktivní pravidlo virtuální sítě, bylo nutné zapnout koncové body služby virtuální sítě. Koncové body související se zadanou podsítí virtuální sítě k databázi v SQL Database. Od ledna 2018 můžete tento požadavek obejít nastavením příznaku **IgnoreMissingVNetServiceEndpoint** . Teď můžete na server přidat pravidlo brány firewall virtuální sítě bez zapnutí koncových bodů služby virtuální sítě.

Pouze nastavení pravidla brány firewall nezabezpečuje Server. Aby se zabezpečení projevilo, musíte také zapnout koncové body služby virtuální sítě. Při zapnutí koncových bodů služby dojde k výpadku vaší podsítě virtuální sítě, dokud se přechod nedokončí z vypnuté na zapnuto. Tato doba výpadku je obzvláště pravdivá v kontextu rozsáhlých virtuálních sítí. Pomocí příznaku **IgnoreMissingVNetServiceEndpoint** můžete snížit nebo odstranit výpadky během přechodu.

Příznak **IgnoreMissingVNetServiceEndpoint** můžete nastavit pomocí prostředí PowerShell. Další informace najdete v tématu [prostředí PowerShell pro vytvoření koncového bodu služby virtuální sítě a pravidla pro SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Chyby 40914 a 40615

Chyba připojení 40914 se vztahuje k *pravidlům virtuální sítě*, jak je uvedeno v podokně **brána firewall** v Azure Portal. Chyba 40615 je podobná, s tím rozdílem, že se vztahují na *pravidla IP adres* v bráně firewall.

### <a name="error-40914"></a>Chyba 40914

**Text zprávy:** Nejde otevřít server *[název serveru]*, který požádal o přihlášení. Klient nemá povolený přístup k serveru. "

**Popis chyby:** Klient se nachází v podsíti, která má koncové body virtuálního síťového serveru. Server ale nemá žádné pravidlo virtuální sítě, které by dané podsíti získalo právo komunikovat s databází.

**Řešení chyb:** V podokně **Brána Firewall** Azure Portal pomocí ovládacího prvku pravidla virtuální sítě [přidejte pravidlo virtuální sítě](#anchor-how-to-by-using-firewall-portal-59j) pro podsíť.

### <a name="error-40615"></a>Chyba 40615

**Text zprávy:** "Nelze otevřít server" {0} požadovaný přihlášením. Klient s IP adresou {1} nemá povolený přístup k serveru.

**Popis chyby:** Klient se pokouší připojit z IP adresy, která nemá autorizaci pro připojení k serveru. Brána firewall serveru nemá žádné pravidlo IP adresy, které klientovi umožňuje komunikovat z dané IP adresy do databáze.

**Řešení chyb:** Zadejte IP adresu klienta jako pravidlo protokolu IP. K provedení tohoto kroku použijte podokno **firewall** v Azure Portal.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Vytvoření pravidla virtuální sítě pomocí portálu

Tato část ukazuje, jak můžete pomocí [Azure Portal][http-azure-portal-link-ref-477t] vytvořit *pravidlo virtuální sítě* v databázi nástroje v SQL Database. Pravidlo instruuje vaši databázi, aby přijímala komunikaci z konkrétní podsítě, která je označena jako *koncový bod služby virtuální sítě*.

> [!NOTE]
> Pokud máte v úmyslu přidat koncový bod služby do pravidel brány firewall virtuální sítě vašeho serveru, nejprve zajistěte, aby byla pro podsíť zapnutá koncová bodu služby.
>
> Pokud nejsou pro podsíť zapnuté koncové body služby, portál vás vyzve, abyste je povolili. Vyberte tlačítko **Povolit** ve stejném podokně, na kterém přidáte pravidlo.

## <a name="powershell-alternative"></a>Alternativní prostředí PowerShell

Skript může také vytvořit pravidla virtuální sítě pomocí rutiny prostředí PowerShell **New-AzSqlServerVirtualNetworkRule** nebo [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). Pokud vás zajímá, přečtěte si téma [PowerShell k vytvoření koncového bodu služby virtuální sítě a pravidla pro SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternativa

Rutiny PowerShellu pro virtuální sítě SQL volají interně volání rozhraní REST API. Rozhraní REST API můžete volat přímo.

- [Pravidla virtuální sítě: operace][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Požadavky

Musíte už mít podsíť, která je označená konkrétním *typem* koncového bodu služby virtuální sítě, který se týká SQL Database.

- Odpovídajícím názvem typu koncového bodu je **Microsoft. SQL**.
- Pokud vaše podsíť nemusí být označená názvem typu, přečtěte si téma [ověření, že je podsíť koncovým bodem][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal kroky

1. Přihlaste se na [Azure Portal][http-azure-portal-link-ref-477t].

1. Vyhledejte a vyberte **SQL servery** a pak vyberte svůj server. V části **zabezpečení** vyberte možnost **brány firewall a virtuální sítě**.

1. Nastavte možnost **Povolení přístupu ke službám Azure** na **off**.

    > [!IMPORTANT]
    > Pokud ovládací prvek necháte nastavený na **zapnuto**, server přijme komunikaci z jakékoli podsítě v rámci hranice Azure. Jedná se o komunikaci, která pochází z jedné z IP adres, která je rozpoznaná jako ta v oblastech definovaných pro datacentra Azure. Ponechání ovládacího prvku na **zapnuto** může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Microsoft Azure Virtual Network v koordinaci s funkcí pravidla virtuální sítě SQL Database společně může snížit oblast zabezpečení.

1. V části **virtuální sítě** vyberte **+ Přidat existující** .

    ![Snímek obrazovky zobrazující výběr + přidat existující (koncový bod podsítě, jako pravidlo SQL)][image-portal-firewall-vnet-add-existing-10-png]

1. V podokně nový **vytvořit/aktualizovat** vyplňte pole názvy vašich prostředků Azure.

    > [!TIP]
    > Je nutné zadat správnou předponu adresy pro vaši podsíť. Hodnotu **předpony adresy** můžete najít na portálu. Přejít na **všechny prostředky** &gt; **všechny typy** &gt; **virtuálních sítí**. Filtr zobrazuje vaše virtuální sítě. Vyberte virtuální síť a pak vyberte **podsítě**. Sloupec **Rozsah adres** obsahuje předponu adresy, kterou potřebujete.

    ![Snímek obrazovky, který zobrazuje vyplnění polí pro nové pravidlo][image-portal-firewall-create-update-vnet-rule-20-png]

1. V dolní části podokna klikněte na tlačítko **OK** .

1. Prohlédněte si výsledné pravidlo virtuální sítě v podokně **Brána firewall** .

    ![Snímek obrazovky, který zobrazuje nové pravidlo v podokně brána firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Následující stavy nebo stavy se vztahují na pravidla:
>
> - **Připraveno**: označuje, že operace, kterou jste iniciovali, byla úspěšná.
> - **Selhalo**: označuje, že operace, kterou jste iniciovali, se nezdařila.
> - **Odstraněno**: platí pouze pro operaci odstranění a označuje, že pravidlo bylo odstraněno a již není použito.
> - **InProgress**: označuje, že operace probíhá. Původní pravidlo se použije, pokud je operace v tomto stavu.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Související články

- [Koncové body služby virtuální sítě Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Pravidla brány firewall na úrovni serveru a databáze][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Další kroky

- [Použití PowerShellu k vytvoření koncového bodu služby virtuální sítě a pak pravidla virtuální sítě pro SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Pravidla virtuální sítě: operace][rest-api-virtual-network-rules-operations-862r] s rozhraními REST API

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
