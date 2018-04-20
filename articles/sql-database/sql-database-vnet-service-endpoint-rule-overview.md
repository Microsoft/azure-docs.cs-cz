---
title: Koncové body služby virtuální sítě a pravidla pro Azure SQL Database | Microsoft Docs
description: Podsíť označte jako koncový bod služby virtuální sítě. Potom koncový bod jako virtuální sítě se pravidlo seznamu řízení přístupu vaší databázi SQL Azure. Pak můžete SQL Database přijme komunikaci ze všech virtuálních počítačů a dalších uzlů v podsíti.
services: sql-database
ms.service: sql-database
author: MightyPen
manager: craigg
ms.custom: VNet Service endpoints
ms.topic: article
ms.date: 03/15/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: 6037659eb419a785b01d4cbb6a2428cbd7f852da
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Použít koncové body služby virtuální sítě a pravidla pro databázi SQL Azure

*Pravidla pro virtuální sítě* jsou jeden funkce zabezpečení brány firewall, která určuje, zda váš server Azure SQL Database přijímá komunikaci, kterou jsou odesílány z konkrétní podsítě ve virtuálních sítích. Tento článek vysvětluje, proč funkci pravidlo virtuální sítě se někdy nejlepší možnost pro bezpečně povolení komunikace k vaší databázi SQL Azure.

K vytvoření pravidla, virtuální sítě, nejprve musí být [koncový bod služby virtuální sítě] [ vm-virtual-network-service-endpoints-overview-649d] pravidlo odkazovat.


#### <a name="how-to-create-a-virtual-network-rule"></a>Jak vytvořit pravidlo pro virtuální sítě

Pokud vytvoříte pouze pravidlo virtuální sítě, můžete přeskočit kroky a vysvětlení [dále v tomto článku](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie a popis

**Virtuální sítě:** může mít virtuální sítě, které jsou spojené s předplatným Azure.

**Podsítě:** virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure (VM), které máte přiřazené k podsítím. Jedna podsíť může obsahovat více virtuálních počítačů nebo dalších výpočetních uzlů. Výpočetní uzly, které jsou mimo virtuální síť nemůže přistupovat k virtuální síti, pokud konfigurace vaší zabezpečení pro povolení přístupu.

**Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě] [ vm-virtual-network-service-endpoints-overview-649d] je podsíť, jejichž hodnoty vlastností obsahovat jeden nebo více názvy typů formální služby Azure. V tomto článku jsme mají zájem o název typu **Microsoft.Sql**, které odkazuje na službu Azure s názvem databáze SQL.

**Pravidlo virtuální sítě:** pravidlo virtuální sítě pro server služby SQL Database je podsíť, která je uvedena v seznamu řízení přístupu (ACL) server služby SQL Database. Chcete-li být v seznamu ACL pro vaši databázi SQL, musí obsahovat podsíť **Microsoft.Sql** název typu.

Pravidlo pro virtuální sítě informuje server služby SQL Database tak, aby přijímal komunikaci od každému uzlu, který je v podsíti.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Výhody pravidlo virtuální sítě

Dokud provedení akce, virtuální počítače na podsítě nemůže komunikovat s vaší databázi SQL. Jedna akce, který stanoví komunikace je vytvoření pravidla virtuální sítě. Při výběru virtuální sítě pravidlo přístupu vyžaduje se zabývat porovnání a kontrast zahrnující konkurenční možnosti zabezpečení nabízené bránou firewall.

#### <a name="a-allow-access-to-azure-services"></a>A. Povolit přístup ke službám Azure

V podokně brány firewall **zapnout nebo vypnout** tlačítko, které je označeno **povolit přístup ke službám Azure**. **ON** nastavení umožňuje komunikaci od všech Azure IP adres a všech Azure podsítí. Tyto Azure IP adresy nebo podsítě nemusí být vlastníte. To **ON** nastavení je pravděpodobně více otevřete než chcete být databáze SQL. Funkce pravidlo virtuální sítě nabízí mnohem přesnější přesná kontrola.

#### <a name="b-ip-rules"></a>B. Pravidla IP

Brány firewall SQL Database umožňuje určit rozsahy IP adres, ze kterých přijata komunikace do databáze SQL. Tento přístup je vhodná pro stabilní IP adresy, které jsou mimo privátní síť Azure. Ale velký počet uzlů v rámci Azure privátní sítě jsou nakonfigurovány s *dynamické* IP adresy. Dynamické IP adresy mohou změnit, například když je virtuální počítač restartovat. Je pošetilost k určení dynamickou IP adresu v pravidle brány firewall v produkčním prostředí.

IP adresy můžete vyřazení získáním *statické* IP adresu pro virtuální počítač. Podrobnosti najdete v tématu [nakonfigurovat privátní IP adresy pro virtuální počítač pomocí portálu Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Ale statické IP přístupu se může stát obtížné spravovat a je nákladná, pokud provádí ve velkém měřítku. Pravidla pro virtuální sítě se snadněji můžete vytvořit a spravovat.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Nelze ještě žádné databáze SQL v podsíti

Pokud váš server Azure SQL Database byl uzel na podsíť ve virtuální síti, všechny uzly v rámci virtuální sítě může komunikovat s vaší databázi SQL. V takovém případě virtuální počítače může komunikovat s SQL Database bez nutnosti všech pravidel virtuální sítě a IP pravidla.

Ale od září 2017 služby Azure SQL Database není ještě mezi služby, které lze přiřadit k podsíti.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Údaje o pravidlech virtuální sítě

Tato část popisuje několik podrobnosti o pravidlech virtuální sítě.

#### <a name="only-one-geographic-region"></a>Pouze jedné zeměpisné oblasti

Každý koncový bod služby virtuální sítě se týká pouze jedna oblast Azure. Koncový bod není povolen jiných oblastí tak, aby přijímal komunikaci z podsítě.

Pravidlo pro všechny virtuální sítě je omezený na oblast, která se vztahuje na jeho základní koncového bodu.

#### <a name="server-level-not-database-level"></a>Na úrovni serveru, ne úroveň databáze

Každé pravidlo virtuální sítě platí pro celý server Azure SQL Database, ne jenom pro jednu konkrétní databázi na serveru. Jinými slovy virtuální sítě pravidlo se vztahuje na úrovni serveru, ne na úrovni databáze.

- Naproti tomu IP pravidla můžete použít buď úrovni.

#### <a name="security-administration-roles"></a>Role zabezpečení správy

Je oddělení rolí zabezpečení v části Správa koncových bodů služby virtuální sítě. Akci je potřeba z každé z následujících rolí:

- **Správce sítě:** &nbsp; zapnout koncový bod.
- **Správce databáze:** &nbsp; aktualizace seznamu řízení přístupu (ACL) pro přidání do databáze SQL serveru dané podsíti.

*Alternativní RBAC:*

Správce sítě a správce databáze mají další možnosti, než jsou potřeba ke správě pravidel virtuální sítě. Je potřeba jenom podmnožinu jejich možnosti.

Máte možnost použití [řízení přístupu na základě role (RBAC)] [ rbac-what-is-813s] v Azure vytvořit jednu vlastní roli, která má pouze nezbytné podmnožinu funkcí. Vlastní role může místo zahrnující správce sítě nebo správce databáze. Možnosti útoku vaší ohrožení zabezpečení je nižší, pokud přidáte vlastní roli, a přidáním uživatele do jiných dvě hlavní správce role uživatele.

> [!NOTE]
> V některých případech databáze SQL Azure a podsíť virtuální sítě jsou v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Oba odběry musí být ve stejném klientovi služby Azure Active Directory.
> - Uživatel nemá potřebná oprávnění k zahájení operace, například povolení koncové body služby a přidáte podsíť virtuální sítě na daný Server.

## <a name="limitations"></a>Omezení

Pro databázi SQL Azure funkci pravidla virtuální sítě má následující omezení:

- Webové aplikace lze mapovat na privátní IP adresa ve virtuální sítě a podsítě. I v případě, že koncových bodů služby jsou zapnuté ON z danou virtuální síť nebo podsíť, bude mít připojení z webové aplikace na server Azure veřejné IP zdroji, není zdroj virtuální sítě a podsítě. Pokud chcete povolit připojení z webové aplikace na server, který má pravidla brány firewall virtuální síť, musíte **povolit všech služeb Azure** na serveru.

- Každé pravidlo virtuální sítě v bráně firewall pro vaši databázi SQL, odkazuje na podsíť. Všechny tyto odkazované podsítě musí být uloženy ve stejné zeměpisné oblasti, který je hostitelem databáze SQL.

- Každý server Azure SQL Database může mít maximálně 128 položky seznamů ACL pro jakékoli dané virtuální síti.

- Pravidla virtuální sítě se vztahují pouze k virtuálním sítím Azure Resource Manager; a nikoli k [modelu nasazení classic] [ arm-deployment-model-568f] sítě.

- Zapnutí ON koncové body služby virtuální sítě do Azure SQL Database umožňuje také koncové body pro službu MySQL a PostgreSQL Azure. Však s ON koncové body, se nezdaří pokusy o připojení z koncových bodů pro vaše instance databáze MySQL nebo PostgreSQL.
    - Základní důvodem je, že MySQL a PostgreSQL nepodporují v současné době funkce ACLing.

- V bráně firewall rozsahy IP adres se vztahují k následujícím položkám sítě, ale nepodporují pravidla pro virtuální sítě:
    - [Site-to-Site (S2S) virtuální privátní sítě (VPN)][vpn-gateway-indexmd-608y]
    - Místní prostřednictvím [ExpressRoute][expressroute-indexmd-744v]

#### <a name="considerations-when-using-service-endpoints"></a>Informace týkající se použití koncové body služby
Pokud používáte koncové body služby pro databázi SQL Azure, projděte si následující aspekty:

- **Odchozí do veřejných IP adres Azure SQL Database je požadována**: skupiny zabezpečení sítě (Nsg) musí být otevřené pro IP adresy databáze SQL Azure umožňující připojení. Můžete to provést pomocí NSG [služby značky](../virtual-network/security-overview.md#service-tags) pro databázi SQL Azure.
- **Azure databáze PostgreSQL a MySQL nejsou podporovány**: koncové body služby nejsou podporovány pro databázi Azure pro PostgreSQL nebo MySQL. Povolování koncových bodů služby SQL Database bude přerušení připojení k těmto službám. Máme zmírnění dopadů pro tento; Obraťte se na *dmalik@microsoft.com*.

#### <a name="expressroute"></a>ExpressRoute

Pokud vaše síť připojená k síti Azure prostřednictvím použití [ExpressRoute][expressroute-indexmd-744v], každý okruh je nakonfigurován s dvě veřejné IP adresy v Microsoft Edge. Dvě IP adresy se používají k připojení k Microsoft Services, například do služby Azure Storage, pomocí veřejného partnerského vztahu Azure.

Povolit komunikaci z okruhu do Azure SQL Database, musíte vytvořit pravidla pro sítě IP pro veřejné IP adresy vaší okruhů. Aby bylo možné najít veřejné IP adresy váš okruh ExpressRoute, otevřete lístek podpory s ExpressRoute pomocí webu Azure portal.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>Dopad odebrání "Povolit všechny služby Azure.

Řada uživatelů chcete odebrat **povolit všechny služby Azure** ze svých serverů SQL Azure a nahraďte ji metodou pravidlo brány Firewall virtuální sítě.
Ale odebrání to ovlivní následující funkce Azure SQLDB:

#### <a name="import-export-service"></a>Import Export služby
Služba Azure SQLDB Import Export běží na virtuálních počítačích v Azure. Tyto virtuální počítače nejsou ve vaší virtuální síti a proto získat IP adresy Azure při připojování k databázi. Na odebrání **povolit všechny služby Azure** tyto virtuální počítače nebudou mít přístup k vaší databáze.
Problém můžete vyřešit. Spuštění souboru BACPAC importu nebo exportu přímo v kódu pomocí rozhraní API DACFx. Zajistěte, aby to byl nasazen ve virtuálním počítači, který je v podsíti virtuální sítě, pro které jste nastavili pravidlo brány firewall.

#### <a name="sql-database-query-editor"></a>Editor dotazů databáze SQL
Editoru dotazů databáze SQL Azure je nasazen na virtuálních počítačů v Azure. Tyto virtuální počítače nejsou ve vaší virtuální síti. Proto získat virtuální počítače Azure IP při připojování k databázi. Na odebrání **povolit všechny služby Azure**, tyto virtuální počítače nebudou mít přístup k vaší databáze.

#### <a name="table-auditing"></a>Tabulka auditování
V současné době jsou dva způsoby, jak povolit auditování ve vaší databázi SQL. Auditování tabulka selže po povolení koncové body služby na serveru SQL Azure. Zmírnění dopadů tady je přesunout do auditování objektů Blob.

#### <a name="impact-on-data-sync"></a>Dopad na synchronizaci dat
Azure SQLDB má funkce synchronizace dat, která se připojuje k vaší databáze pomocí IP adresy Azure. Při použití koncové body služby, je pravděpodobné, že se vypne **povolit všechny služby Azure** přístup ke svému logickému serveru. Tím dojde k přerušení funkce synchronizace Data.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Dopad pomocí koncových bodů služby virtuální síť s Azure storage

Úložiště Azure implementovala stejné funkce, která vám umožní omezit připojení k účtu úložiště.
Pokud se rozhodnete tuto funkci používat s účtem úložiště, který používá Azure SQL Server, můžete spustit na problémy. Následuje seznam a diskuzi o Azure SQLDB funkce, které jsou ovlivněny to.

#### <a name="azure-sqldw-polybase"></a>Azure SQLDW PolyBase
PolyBase se běžně používá k načtení dat do Azure SQLDW z úložiště účtů. Pokud účet úložiště, který se načítání dat z omezuje přístup pouze na sadu podsítí virtuální sítě, dojde k přerušení připojení k z PolyBase k účtu. Je zmírnění dopadů pro tento; Obraťte se na *dmalik@microsoft.com* Další informace.

#### <a name="azure-sqldb-blob-auditing"></a>Objekt Blob Azure SQLDB auditování
Auditování objektů BLOB doručí protokolů auditu na účtu úložiště. Pokud tento účet úložiště používá funkce koncové body služby ECYKLACI dojde k přerušení připojení z Azure SQLDB k účtu úložiště.


## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Přidání pravidla brány Firewall virtuální sítě na server bez vypnutí na koncové body služby virtuální sítě

Dávno před tato funkce byla vylepšené, nebyly potřeba vám virtuální sítě služby koncových bodů na před za provozu pravidlo virtuální sítě může implementovat v bráně Firewall. Koncové body související s danou podsíť virtuální sítě do Azure SQL Database. Ale od ledna 2018, můžete nyní obejít tento požadavek, a to nastavením **IgnoreMissingServiceEndpoint** příznak.

Jenom nastavení pravidla brány Firewall nepomůže zabezpečení serveru. Musíte také zapnout koncové body služby virtuální sítě pro zabezpečení vstoupily v platnost. Když zapnete koncové body služby, vyskytne VNet subnet až do dokončení přechod z vypnout na výpadek. To platí hlavně v kontextu velké virtuální sítě. Můžete použít **IgnoreMissingServiceEndpoint** příznak, který omezit nebo odstranit výpadek během přechodu.

Můžete nastavit **IgnoreMissingServiceEndpoint** příznak pomocí prostředí PowerShell. Podrobnosti najdete v tématu [prostředí PowerShell vytvořit koncový bod služby virtuální sítě a pravidla pro databázi SQL Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].


## <a name="errors-40914-and-40615"></a>Chyby 40914 a 40615

Došlo k chybě připojení 40914 má vztah k *pravidla virtuální sítě*, jak je uvedeno v podokně brány Firewall na portálu Azure. Chyba 40615 je podobné, s výjimkou má vztah k *pravidla IP adres* v bráně Firewall.

#### <a name="error-40914"></a>Chyba 40914

*Text zprávy:* nelze otevřít serveru '*[název serveru]*' požadovaný v přihlášení. Klient nemá povolen přístup k serveru.

*Popis chyby:* klienta je v podsíti, který má koncové body serveru virtuální sítě. Ale serveru Azure SQL Database nemá žádné pravidlo virtuální sítě, která uděluje k podsíti právo ke komunikaci s databází SQL.

*Řešení chyb:* na brány Firewall podokně portálu Azure, použijte řídit pravidla virtuální sítě k [přidat pravidlo pro virtuální sítě](#anchor-how-to-by-using-firewall-portal-59j) podsítě.

#### <a name="error-40615"></a>Chyba 40615

*Text zprávy:* server: {0} požadovaný v přihlášení nelze otevřít. Klient s IP adresou objekt {1}' nemá povolen přístup k serveru.

*Popis chyby:* klient se pokouší o připojení z IP adresy, která nemá oprávnění pro připojení k serveru Azure SQL Database. Brány firewall serveru nemá žádné pravidlo adresy IP, která umožňuje klientům pro komunikaci z dané IP adresy k databázi SQL.

*Řešení chyb:* zadejte IP adresu klienta jako pravidlo IP. To provedete v podokně brány Firewall pomocí portálu Azure.


Seznam chybových zpráv SQL Database je popsána [sem][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portál můžete vytvořit pravidlo pro virtuální sítě

Tato část ukazuje, jak můžete použít [portál Azure] [ http-azure-portal-link-ref-477t] k vytvoření *pravidlo virtuální sítě* ve vaší databázi SQL Azure. Pravidlo informuje vaší databázi SQL tak, aby přijímal komunikaci z konkrétní podsítě, kterých je označen jako *koncový bod služby virtuální sítě*.

> [!NOTE]
> Ověřte, zda služby, které jsou zapnuté koncové body pro virtuální síť nebo podsíť, který chcete přidat do virtuální sítě pravidla brány Firewall serveru.
> Pokud nejsou vypnuté koncové body služby pro virtuální síť nebo podsíť, zobrazí se dotaz na portálu je povolit, klikněte na povolte na okno, ve kterém můžete přidat pravidlo.

#### <a name="powershell-alternative"></a>Alternativní prostředí PowerShell

Skript prostředí PowerShell můžete taky vytvořit pravidla virtuální sítě. Rutinu zásadní **New-AzureRmSqlServerVirtualNetworkRule**. Pokud uvažujete o, přečtěte si téma [prostředí PowerShell vytvořit koncový bod služby virtuální sítě a pravidla pro databázi SQL Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="rest-api-alternative"></a>Alternativní REST API

Interně rutin prostředí PowerShell pro virtuální síť SQL akce volání rozhraní REST API. Rozhraní REST API můžete volat přímo.

- [Pravidla pro virtuální sítě: operace][rest-api-virtual-network-rules-operations-862r]

#### <a name="prerequisites"></a>Požadavky

Již musí mít podsíť, která je označené konkrétní koncový bod služby virtuální sítě *název typu* relevantní pro Azure SQL Database.

- Název typu relevantní koncový bod je **Microsoft.Sql**.
- Pokud podsíť nemusí být označené název typu, najdete v části [Ověřte podsíť je koncový bod][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Portál Azure kroky

1. Přihlaste se k [portál Azure][http-azure-portal-link-ref-477t].

2. Pak přejděte na portál pro **servery SQL** &gt; **Brána Firewall / virtuální sítě**.

3. Nastavte **povolit přístup ke službám Azure** ovládacího prvku na hodnotu OFF.

    > [!IMPORTANT]
    > Pokud necháte ovládacího prvku na hodnotu ON, server služby Azure SQL Database přijímá komunikaci z žádné podsítě. Opouštění ovládacího prvku na hodnotu ON, může být příliš přístupu z hlediska zabezpečení. Funkce koncový bod služby Microsoft Azure Virtual Network v koordinaci s funkcí pravidlo virtuální sítě SQL Database, můžete snížit společně zabezpečení útoku.

4. Klikněte **+ přidat existující** řídit, v **virtuální sítě** části.

    ![Klikněte na tlačítko Přidat existující (podsíť koncový bod, jako pravidlo SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. V novém **vytvořit nebo aktualizovat** podokně, vyplňte ovládacích prvků s názvy prostředků Azure.

    > [!TIP]
    > Je nutné zahrnout správný **předpona adresy** pro podsíť. Hodnota můžete najít na portálu.
    > Přejděte **všechny prostředky** &gt; **všechny typy** &gt; **virtuální sítě**. Filtr zobrazí virtuálních sítí. Klikněte na virtuální síti a potom klikněte na **podsítě**. **Rozsah adres** sloupec má předpona adresy je nutné.

    ![Vyplňte pole pro nové pravidlo.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klikněte **OK** tlačítko v dolní části podokna.

7.  V podokně brány firewall najdete v části výsledné pravidlo virtuální sítě.

    ![Nové pravidlo, najdete v podokně brány firewall.][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> Následující stavy nebo stavy, které platí pro pravidla:
> - **Připravené:** označuje, že operace, které jste spustili proběhla úspěšně.
> - **Se nezdařilo:** označuje, že operace, které jste spustili se nezdařila.
> - **Odstranit:** pouze platí pro operace odstranění a označuje, že pravidlo byl odstraněn a už neplatí.
> - **InProgress:** označuje, že probíhá operace. Původní pravidlo se použije při operaci v tomto stavu.


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Související články

- [Koncové body služby virtuální síť Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Azure pravidla brány firewall serveru úroveň a databáze SQL Database][sql-db-firewall-rules-config-715d]

Funkce pravidlo virtuální sítě pro databázi SQL Azure jsou k dispozici v pozdní září 2017.

## <a name="next-steps"></a>Další postup

- [Vytvoření koncového bodu služby virtuální sítě a potom pravidlo virtuální sítě pro Azure SQL Database pomocí prostředí PowerShell.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Pravidla pro virtuální sítě: Operace] [ rest-api-virtual-network-rules-operations-862r] pomocí rozhraní REST API



<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules



<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
