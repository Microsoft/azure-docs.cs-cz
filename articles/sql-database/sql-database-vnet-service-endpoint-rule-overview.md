---
title: Koncové body a pravidla virtuální sítě pro databáze s jednou a fondem
description: Označte podsíť jako koncový bod služby Virtual Network. Pak koncový bod jako pravidlo virtuální sítě pro seznam ACL Azure SQL Database. Pak SQL Database přijmete komunikaci ze všech virtuálních počítačů a dalších uzlů v podsíti.
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
ms.openlocfilehash: faf3573178693ec806000eb89ce7a975955d61b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084123"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Použití koncových bodů a pravidel služby virtuální sítě pro databázové servery

*Pravidla virtuální sítě* jsou jedna funkce zabezpečení brány firewall, která určuje, jestli databázový server pro izolované databáze a elastický fond v Azure [SQL Database](sql-database-technical-overview.md) nebo pro vaše databáze v [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) akceptuje komunikaci. které se odesílají z konkrétních podsítí ve virtuálních sítích. Tento článek vysvětluje, proč je funkce pravidla virtuální sítě někdy nejlepší volbou pro bezpečné povolení komunikace s vaším Azure SQL Database a SQL Data Warehouse.

> [!IMPORTANT]
> Tento článek se týká Azure SQL serveru a databází SQL Database i SQL Data Warehouse, které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse. Tento článek se *nevztahuje na* nasazení **spravované instance** v Azure SQL Database, protože k němu není přidružen koncový bod služby.

Aby bylo možné vytvořit pravidlo virtuální sítě, musí být nejprve [koncovým bodem služby virtuální sítě][vm-virtual-network-service-endpoints-overview-649d] pro pravidlo, na které se má odkazovat.

## <a name="how-to-create-a-virtual-network-rule"></a>Postup vytvoření pravidla virtuální sítě

Pokud vytvoříte pouze pravidlo virtuální sítě, můžete přeskočit kroky a vysvětlení [dále v tomto článku](#anchor-how-to-by-using-firewall-portal-59j).

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Podrobnosti o pravidlech virtuální sítě

Tato část popisuje několik podrobností o pravidlech virtuální sítě.

### <a name="only-one-geographic-region"></a>Jenom jedna geografická oblast

Každý koncový bod služby Virtual Network se vztahuje jenom na jednu oblast Azure. Koncový bod nepovoluje, aby komunikace z podsítě přijímala jiné oblasti.

Jakékoli pravidlo virtuální sítě je omezené na oblast, na kterou se vztahuje příslušný koncový bod.

### <a name="server-level-not-database-level"></a>Na úrovni serveru, nikoli na úrovni databáze

Každé pravidlo virtuální sítě se vztahuje na celý Azure SQL Database Server, nikoli jenom na jednu konkrétní databázi na serveru. Jinými slovy, pravidlo virtuální sítě se vztahuje na úrovni serveru, nikoli na úrovni databáze.

- Naproti tomu pravidla protokolu IP můžou platit na kterékoli úrovni.

### <a name="security-administration-roles"></a>Role správy zabezpečení

V rámci správy koncových bodů služby Virtual Network je oddělení rolí zabezpečení. Pro každou z následujících rolí se vyžaduje akce:

- **Správce sítě:** &nbsp; zapnout koncový bod.
- **Správce databáze:** &nbsp; aktualizovat seznam řízení přístupu (ACL) pro přidání dané podsítě do serveru SQL Database.

*Alternativa RBAC:*

Role správce sítě a správce databáze mají více možností, než je nutné ke správě pravidel virtuální sítě. Je potřeba jenom podmnožina jejich schopností.

V Azure máte možnost použít [řízení přístupu na základě role (RBAC)][rbac-what-is-813s] a vytvořit jednu vlastní roli, která bude mít jenom nezbytnou podmnožinu funkcí. Vlastní roli můžete použít místo zapojení správce sítě nebo správce databáze. Pokud přidáte uživatele do vlastní role a přidáváte uživatele k ostatním dvěma hlavním rolím Správce, oblast Surface vašeho ohrožení zabezpečení je nižší.

> [!NOTE]
> V některých případech jsou Azure SQL Database a VNet-Subnet v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Oba odběry musí být ve stejném Azure Active Directory tenantovi.
> - Uživatel má požadovaná oprávnění k zahájení operací, jako je například povolení koncových bodů služby a přidání podsítě virtuální sítě do daného serveru.
> - Oba odběry musí mít zaregistrovaný poskytovatel Microsoft. SQL.

## <a name="limitations"></a>Omezení

Pro Azure SQL Database funkce pravidla virtuální sítě má následující omezení:

- V bráně firewall pro SQL Database se každé pravidlo virtuální sítě odkazuje na podsíť. Všechny tyto odkazované podsítě musí být hostované ve stejné geografické oblasti, která hostuje SQL Database.

- Každý Azure SQL Database Server může mít až 128 položek seznamu řízení přístupu (ACL) pro libovolnou danou virtuální síť.

- Pravidla virtuální sítě se vztahují jenom na Azure Resource Manager virtuální sítě; a ne pro [model nasazení Classic][arm-deployment-model-568f] .

- Zapnutím koncových bodů služby virtuální sítě Azure SQL Database také umožníte koncovým bodům pro služby Azure MySQL a PostgreSQL. S koncovými body v se však pokusy o připojení z koncových bodů do instancí MySQL nebo PostgreSQL mohou selhat.
  - Základním důvodem je, že MySQL a PostgreSQL pravděpodobně nemají nakonfigurováno pravidlo virtuální sítě. Je nutné nakonfigurovat pravidlo virtuální sítě pro Azure Database for MySQL a PostgreSQL a připojení bude úspěšné.

- V bráně firewall se rozsahy IP adres vztahují na následující síťové položky, ale pravidla virtuální sítě ne:
  - [Virtuální privátní síť (VPN) typu Site-to-Site (S2S)][vpn-gateway-indexmd-608y]
  - Místně prostřednictvím [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Pokyny k použití koncových bodů služby

Při použití koncových bodů služby pro Azure SQL Database se podívejte na následující skutečnosti:

- **Odchozí IP adresa pro Azure SQL Database veřejné IP adresy se vyžadují**: aby se Azure SQL Database IP adres umožňovalo připojení, musí se otevřít skupiny zabezpečení sítě (skupin zabezpečení sítě). To můžete provést pomocí [značek služby](../virtual-network/security-overview.md#service-tags) NSG pro Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z vašich místních partnerských vztahů nebo partnerských vztahů Microsoftu, budete muset určit IP adresy NAT, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. IP adresy pro překlad adres (NAT) používané v partnerských vztazích s Microsoftem poskytuje zákazník nebo poskytovatel služby. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Aby bylo možné Azure SQL Database komunikaci z okruhu, musíte vytvořit pravidla sítě IP pro veřejné IP adresy vašeho překladu adres (NAT).

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->



## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Dopad použití koncových bodů služby virtuální sítě se službou Azure Storage

Azure Storage implementoval stejnou funkci, která umožňuje omezit připojení k účtu Azure Storage. Pokud se rozhodnete tuto funkci použít s účtem Azure Storage, který používá Azure SQL Server, můžete nastavovat problémy. Dál je seznam a diskuzi o funkcích Azure SQL Database a Azure SQL Data Warehouse, na které má vliv.

### <a name="azure-sql-data-warehouse-polybase"></a>Základ Azure SQL Data Warehouse

Základ se běžně používá k načtení dat do Azure SQL Data Warehouse z Azure Storagech účtů. Pokud účet Azure Storage, ze kterého načítáte data, omezuje přístup jenom na sadu virtuálních sítí VNet, dojde k přerušení připojení od základu k účtu. Pokud chcete povolit jak základní scénáře importu i exportu, tak pomocí Azure SQL Data Warehouse připojení k Azure Storage, která je zabezpečená pro virtuální síť, postupujte podle kroků uvedených níže:

#### <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

1.  Pomocí této [příručky](https://docs.microsoft.com/powershell/azure/install-az-ps)nainstalujte Azure PowerShell.
2.  Pokud máte účet úložiště pro obecné účely v1 nebo blob, musíte nejdřív v této [příručce](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)upgradovat na obecné účely v2.
3.  Abyste měli přístup k tomuto účtu úložiště zapnutý, musíte mít **povolené důvěryhodné služby Microsoftu** v nabídce Azure Storage **brány firewall účtů a nastavení virtuálních sítí** . Další informace najdete v tomto [Průvodci](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) .
 
#### <a name="steps"></a>Kroky
1. V PowerShellu **Zaregistrujte Azure SQL Server** hostování vaší instance Azure SQL Data Warehouse pomocí Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```
    
   1. Pomocí této [příručky](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)vytvořte **účet úložiště pro obecné účely v2** .

   > [!NOTE]
   > - Pokud máte účet úložiště pro obecné účely v1 nebo blob, musíte **nejdřív upgradovat na verzi v2** pomocí této [příručky](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Známé problémy s Azure Data Lake Storage Gen2 najdete v tomto [Průvodci](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. V části účet úložiště přejděte na **Access Control (IAM)** a klikněte na **Přidat přiřazení role**. Přiřazení role RBAC **Přispěvatel dat objektů BLOB úložiště** k vašemu Azure SQL Server hostování Azure SQL Data Warehouse, které jste zaregistrovali v Azure Active Directory (AAD) jako v kroku č. 1.

   > [!NOTE] 
   > Tento krok mohou provádět pouze členové s oprávněním vlastníka. Informace o různých předdefinovaných rolích pro prostředky Azure najdete v tomto [Průvodci](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Základní připojení k účtu Azure Storage:**

   1. Pokud jste ještě nevytvořili **[hlavní klíč](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** databáze, vytvořte ho:
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. Vytvořit databázi s rozsahem pověření s **identitou = ' identita spravované služby '** :

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Není nutné zadávat tajný klíč pomocí Azure Storage přístupového klíče, protože tento mechanismus používá [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) v rámci pokrývání.
       > - Název IDENTITY by měl být **Identita spravované služby** , aby připojení k síti s Azure Storagem účtem zabezpečeném pro virtuální síť fungovalo.    
    
   1. Vytvoření externího zdroje dat pomocí schématu abfss://pro připojení k vašemu účtu úložiště pro obecné účely v2 pomocí základu:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Pokud už máte externí tabulky přidružené k účtu úložiště pro obecné účely v1 nebo blob, měli byste tyto externí tabulky nejdřív vyřadit a pak vyřadit odpovídající externí zdroj dat. Pak vytvořte externí zdroj dat s abfss://schématem připojujícím se k účtu úložiště pro obecné účely v2 a znovu vytvořte všechny externí tabulky pomocí tohoto nového externího zdroje dat. Pomocí [Průvodce Generovat a publikovat skripty](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) můžete snadno vytvořit vytváření skriptů pro všechny externí tabulky.
       > - Další informace o schématu abfss://najdete v tomto [Průvodci](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Další informace o vytvoření externího zdroje dat najdete v tomto [Průvodci](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
   1. Dotazování jako normální pomocí [externích tabulek](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)

### <a name="azure-sql-database-blob-auditing"></a>Auditování Azure SQL Database objektů BLOB

Auditování objektů BLOB přenáší protokoly auditu do vlastního účtu úložiště. Pokud tento účet úložiště používá funkci koncových bodů služby virtuální sítě, dojde k přerušení připojení z Azure SQL Database k účtu úložiště.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Přidání pravidla brány firewall virtuální sítě na server bez zapnutí koncových bodů služby virtuální sítě

Před zvýšením této funkce jste před tím, než v bráně firewall naimplementovali aktivní pravidlo virtuální sítě, bylo nutné zapnout koncové body služby virtuální sítě. Koncové body související s danou virtuální sítí a podsítí k Azure SQL Database. Ale teď od ledna 2018 můžete tento požadavek obejít nastavením příznaku **IgnoreMissingVNetServiceEndpoint** .

Pouze nastavení pravidla brány firewall nezabezpečuje Server. Aby se zabezpečení projevilo, musíte taky zapnout koncové body služby virtuální sítě. Při zapnutí koncových bodů služby ve vaší virtuální síti dojde k výpadku, dokud se přechod neukončí na zapnuto. To platí zejména v kontextu velkých virtuální sítě. Pomocí příznaku **IgnoreMissingVNetServiceEndpoint** můžete snížit nebo odstranit výpadky během přechodu.

Příznak **IgnoreMissingVNetServiceEndpoint** můžete nastavit pomocí prostředí PowerShell. Podrobnosti najdete v tématu [prostředí PowerShell pro vytvoření koncového bodu služby Virtual Network a pravidla pro Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Chyby 40914 a 40615

Chyba připojení 40914 se vztahuje k *pravidlům virtuální sítě*, jak je uvedeno v podokně Brána Firewall v Azure Portal. Chyba 40615 je podobná, s tím rozdílem, že se vztahují na *pravidla IP adres* v bráně firewall.

### <a name="error-40914"></a>Chyba 40914

*Text zprávy:* Nejde otevřít server *[název serveru]* , který požádal o přihlášení. Klient nemá povolený přístup k serveru.

*Popis chyby:* Klient se nachází v podsíti, která má koncové body virtuálního síťového serveru. Azure SQL Database ale nemá žádné pravidlo virtuální sítě, které dává podsíti oprávnění ke komunikaci s SQL Database.

*Řešení chyb:* V podokně brána firewall Azure Portal pomocí ovládacího prvku pravidla virtuální sítě [přidejte pravidlo virtuální sítě](#anchor-how-to-by-using-firewall-portal-59j) pro podsíť.

### <a name="error-40615"></a>Chyba 40615

*Text zprávy:* Server '{0}' požadovaný přihlašovacím jménem nelze otevřít. Klient s IP adresou{1}nemá povolený přístup k serveru.

*Popis chyby:* Klient se pokouší připojit z IP adresy, která nemá autorizaci pro připojení k serveru Azure SQL Database. Brána firewall serveru neobsahuje žádné pravidlo IP adres, které by klientovi umožňovalo komunikovat se službou SQL Database z dané IP adresy.

*Řešení chyb:* Zadejte IP adresu klienta jako pravidlo protokolu IP. To provedete v podokně Brána firewall na webu Azure Portal.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portál může vytvořit pravidlo virtuální sítě

Tato část ukazuje, jak můžete pomocí [Azure Portal][http-azure-portal-link-ref-477t] vytvořit *pravidlo virtuální sítě* v Azure SQL Database. Pravidlo přikáže SQL Database, aby přijímalo komunikaci z konkrétní podsítě označené jako *koncový bod služby Virtual Network*.

> [!NOTE]
> Pokud máte v úmyslu přidat koncový bod služby pro pravidla brány firewall virtuální sítě vašeho serveru Azure SQL Database, nejprve zajistěte, aby byla pro podsíť zapnutá koncová bodu služby.
>
> Pokud nejsou pro podsíť zapnuté koncové body služby, portál vás vyzve, abyste je povolili. Klikněte na tlačítko **Povolit** ve stejném okně, na kterém přidáte pravidlo.

## <a name="powershell-alternative"></a>Alternativní prostředí PowerShell

PowerShellový skript může také vytvořit pravidla virtuální sítě. Rozhodující rutina **New-AzSqlServerVirtualNetworkRule**. Pokud vás zajímá, přečtěte si téma [PowerShell a vytvořte koncový bod služby Virtual Network a pravidlo pro Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternativa

Rutiny PowerShellu pro akce virtuální sítě SQL volají interně volání rozhraní REST API. Rozhraní REST API můžete volat přímo.

- [Pravidla Virtual Network: operace][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Požadavky

Musíte už mít podsíť, která je označená konkrétním Virtual Networkm *typu* koncového bodu služby, který je relevantní pro Azure SQL Database.

- Odpovídajícím názvem typu koncového bodu je **Microsoft. SQL**.
- Pokud vaše podsíť nemusí být označená názvem typu, přečtěte si téma [ověření, že je podsíť koncovým bodem][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure Portal kroky

1. Přihlásit se na [Azure Portal][http-azure-portal-link-ref-477t].

2. Potom přejděte na portál a **servery SQL** &gt; **firewall/virtuální sítě**.

3. Nastavte řízení **Povolení přístupu ke službám Azure** na vypnuto.

    > [!IMPORTANT]
    > Pokud ovládací prvek necháte nastavený na ZAPNUTo, váš Azure SQL Database server přijme komunikaci z jakékoli podsítě v rámci hranice Azure, tj. pocházející z jedné z IP adres, která je rozpoznaná jako ta v oblastech definovaných pro datová centra Azure. Ponechání ovládacího prvku na ZAPNUTo může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Microsoft Azure Virtual Network, v koordinaci s funkcí pravidla virtuální sítě SQL Database, společně může snížit oblast zabezpečení.

4. Klikněte na tlačítko **+ Přidat existující** ovládací prvek v části **virtuální sítě** .

    ![Klikněte na Přidat existující (koncový bod podsítě, jako pravidlo SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. V podokně nový **vytvořit/aktualizovat** vyplňte ovládací prvky názvy vašich prostředků Azure.

    > [!TIP]
    > Je nutné zadat správnou **předponu adresy** pro vaši podsíť. Tuto hodnotu můžete najít na portálu.
    > Přejděte na **všechny prostředky** &gt; **všech typech** &gt; **virtuálních sítích**. Filtr zobrazuje vaše virtuální sítě. Klikněte na svou virtuální síť a potom klikněte na **podsítě**. Sloupec **Rozsah adres** obsahuje předponu adresy, kterou potřebujete.

    ![Vyplňte pole pro nové pravidlo.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klikněte na tlačítko **OK** v dolní části podokna.

7. Prohlédněte si výsledné pravidlo virtuální sítě v podokně brána firewall.

    ![V podokně brána firewall se zobrazí nové pravidlo.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Následující stavy nebo stavy se vztahují na pravidla:
> - **Připraveno:** Označuje, že operace, kterou jste iniciovali, byla úspěšná.
> - **Selhalo:** Indikuje, že operace, kterou jste iniciovali, se nezdařila.
> - **Odstraněno:** Platí pouze pro operaci odstranění a označuje, že pravidlo bylo odstraněno a již není použito.
> - **Probíhá zpracování:** Indikuje, že operace probíhá. Původní pravidlo se použije, pokud je operace v tomto stavu.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Související články

- [Koncové body služby virtuální sítě Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database pravidla brány firewall na úrovni serveru a databáze][sql-db-firewall-rules-config-715d]

Funkce pravidla virtuální sítě pro Azure SQL Database se stala k dispozici v 2017. září.

## <a name="next-steps"></a>Další kroky

- [Pomocí PowerShellu vytvořte koncový bod služby virtuální sítě a potom pravidlo virtuální sítě pro Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Pravidla Virtual Network: operace][rest-api-virtual-network-rules-operations-862r] s rozhraními REST API

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

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
