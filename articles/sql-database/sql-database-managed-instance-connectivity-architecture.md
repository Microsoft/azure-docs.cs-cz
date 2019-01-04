---
title: Azure SQL Database Managed Instance připojení architektury | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komunikace Azure SQL Database Managed Instance a také architektura připojení vysvětluje, jak různé součásti fungovat směrovat přenos dat do Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: e69f6869911555730fe723b340e224c0d5a1e4bb
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536045"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database Managed Instance architektura připojení

Tento článek obsahuje přehled komunikace Azure SQL Database Managed Instance a také architektura připojení vysvětluje, jak různé součásti fungovat směrovat přenos dat do Managed Instance.  

Azure SQL Database Managed Instance je umístěn uvnitř virtuální sítě Azure a v podsíti vyhrazená pro Managed instance. Toto nasazení umožňuje následující scénáře: 
- Zabezpečené privátní IP adresu.
- Připojení k Managed Instance přímo z místní sítě.
- Připojení Managed Instance pro odkazovaný server nebo jiné v místním úložišti.
- Připojování k prostředkům Azure Managed Instance.

## <a name="communication-overview"></a>Přehled komunikace

Následující diagram znázorňuje entity, které se připojují k Managed Instance, jakož i prostředky, kdy se mi nás kontaktujte, aby mohl správně fungovat.

![připojení k architektuře entity](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Komunikaci, která je znázorněn v dolní části diagramu představuje zákazníka aplikacím a nástrojům připojení k Managed Instance jako zdroj dat.  

Managed Instance je platforma jako služby (PaaS) nabídku, která Microsoft spravuje tato služba pomocí automatizovaných agents (Správa, nasazení a údržbě) založené na datových proudů telemetrie. Správu Managed Instance je výhradně jen společnost Microsoft odpovědnosti, nejsou zákazníkům přístup k Managed Instance clusteru virtuálních počítačů pomocí protokolu RDP.

Některé operace serveru SQL Server iniciované koncovým uživatelům nebo aplikacemi může vyžadovat Managed Instance pro interakci s platformou. Jeden případ je vytvoření Managed Instance databáze - prostředek, který je zveřejněný prostřednictvím portálu, Powershellu a rozhraní příkazového řádku Azure.

Managed Instance, závisí na jiné služby Azure pro správné fungování (jako je například Azure Storage pro zálohování, telemetrických dat služby Azure Service Bus, Azure AD pro ověřování služby Azure Key Vault pro transparentní šifrování dat a tak dále) a zahájí připojení k nim odpovídajícím způsobem.

Veškerá komunikace, uvedené výše, jsou zašifrovaná a podepsaná certifikáty. Pokud chcete mít jistotu, že jsou důvěryhodná komunikujících stran, spravované Instance neustále ověří tyto certifikáty kontaktováním certifikační autority. Pokud jsou odvolané certifikáty nebo je nelze ověřit Managed Instance, uzavře připojení k ochraně dat.

## <a name="high-level-connectivity-architecture"></a>Architektura vysoké úrovně připojení

Na vysoké úrovni Managed Instance je sada součástí služby hostované na vyhrazenou sadu izolované virtuální počítače, které jsou spouštěny v rámci podsítě virtuální sítě zákazníků a vytvoří virtuální cluster.

Více spravovaných instancí můžou být hostované jediného virtuálního clusteru. Cluster automaticky rozbalená nebo v případě potřeby při změně zákazníka číslo zřízené instance v podsíti na zakázku.

Zákaznické aplikace může připojit k Managed Instance, dotazů a aktualizace databází jenom v případě, že spuštěny uvnitř virtuální sítě nebo partnerské virtuální sítě nebo VPN / Express Route propojená síť pomocí koncového bodu se privátní IP adresou.  

![diagram architektury připojení](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Nasazení a správu služby běží mimo virtuální síť, takže připojení mezi Managed Instance a služeb Microsoftu prochází přes koncové body s veřejnými IP adresami. Když mi vytvoří odchozí připojení, na přijímající straně pravděpodobně pochází z této veřejné IP adresy z důvodu překlad síťových adres (NAT).

Provoz správy, prochází přes virtuální síti zákazníka. To znamená, že ovlivňují prvky infrastruktury virtuální sítě a by mohlo potenciálně provoz správy poškození příčinou instance do chybného stavu a přestanou být dostupné.

> [!IMPORTANT]
> K vylepšení zkušeností uživatelů a dostupnost služeb, Microsoft využívá zásady sítě záměr na prvky infrastruktury virtuální sítě Azure, které by mohly ovlivnit fungování Managed Instance. Jedná se o platformě mechanismus pro komunikaci transparentně síťové požadavky na koncové uživatele, s hlavním cílem, aby se předešlo chybného síťového nastavení a zajistit normální provoz Managed Instance. Při odstranění Managed Instance bude odebrán také záměr zásady sítě.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuální cluster architektura připojení

Pojďme se dozvědět více o v architektuře připojení k Managed Instance. Následující diagram znázorňuje koncepční rozložení virtuálních clusterů.

![připojení k architektuře diagram virtuální cluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienti připojit k Managed Instance pomocí názvu hostitele, který má formulář `<mi_name>.<dns_zone>.database.windows.net`. Tento název hostitele se přeloží na privátní IP adresu, i když je zaregistrovaný ve veřejné zóny DNS a je veřejně přeložitelného. `zone-id` Se automaticky vygeneruje, když se cluster vytvoří. Pokud nově vytvořený cluster je hostitelem sekundární managed instance, sdílí jeho id zóny s primární clusteru. Další informace najdete v tématu [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Tato privátní IP adresa patří k na spravované instanci interní zatížení nástroje pro vyrovnávání (ILB), která směruje provoz do na spravované instanci brány (gs). Více spravovaných instancí může potenciálně spuštění ve stejném clusteru GW používá název hostitele Managed Instance pro přesměrování přenosu dat na správné služby stroj SQL.

Nasazení a správu služeb připojení k Managed Instance pomocí [koncový bod správy](#management-endpoint) , který se mapuje na externím vyrovnáváním zatížení. Provoz se směruje do uzlů pouze v případě, že byla přijata na předdefinovanou sadu porty, které používá jediná komponentami pro správu Managed Instance. Integrované brána firewall na uzlech je nakonfigurována pro povolení provozu pouze z konkrétní rozsahy IP Microsoft. Veškerá komunikace mezi komponentami správy a rovina správy se vzájemně certifikát ověřit.

## <a name="management-endpoint"></a>Koncový bod správy

Virtuální cluster Azure SQL Database Managed Instance obsahuje koncový bod správy, který Microsoft používá ke správě Managed Instance. Koncový bod správy je chráněný pomocí předdefinovaných brány firewall sítě certifikát úrovně a proces vzájemného ověření na úrovni aplikace. Je možné [najít ip adresu koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Zdá se, že provoz pochází z veřejnou IP adresu koncového bodu správy po připojení se zahájilo Managed Instance (zálohování, protokolu auditu). Může omezit přístup k veřejné služby ze Managed Instance, tak, že nastavíte pravidla brány firewall pro povolení pouze spravované Instance IP adres. Najít mor einformation o metodě, která se dá [ověření integrované firewall Managed Instance](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> To neplatí pro nastavení pravidla brány firewall pro služby Azure, které jsou ve stejné oblasti jako Managed Instance, Platforma Azure má optimalizace pro provoz, který prochází mezi službami, které jsou společně umístěná.

## <a name="network-requirements"></a>Síťové požadavky

Managed Instance můžete nasadit ve vyhrazené podsíti (podsíť Managed Instance) uvnitř virtuální sítě, který splňuje následující požadavky:
- **Vyhrazené podsíti**: Podsíť Managed Instance nesmí obsahovat jiné cloudové služby s ním spojená, a nesmí se jednat o podsíť brány. Nebudete mít k vytvoření Managed Instance v podsíti, která obsahuje prostředky než Managed Instance a dalších prostředků nelze přidat později v podsíti.
- **Kompatibilní se skupina zabezpečení sítě (NSG)**: Skupina zabezpečení sítě přidružená k podsíti Managed Instance musí obsahovat pravidla je znázorněno v následujících tabulkách (pravidla povinné zabezpečení příchozích dat a povinná odchozí pravidla zabezpečení) před všechna pravidla. Skupina zabezpečení sítě můžete použít plně řídit přístup ke koncovému bodu data Managed Instance pomocí filtrování provozu na portu 1433. 
- **Tabulku kompatibilní trasy definované uživatelem (UDR)**: Podsíť Managed Instance musí mít uživatel směrovací tabulku s **0.0.0.0/0 dalšího segmentu směrování Internetu** jako povinné přiřazené uživatelem definovaná TRASA. Kromě toho můžete přidat trasu UDR této směruje provoz, který má místní rozsahy privátních IP jako cíl přes bránu virtuální sítě nebo virtuální síťové zařízení (NVA). 
- **Volitelné vlastní DNS**: Pokud vlastní DNS je zadána ve službě virtual network, Azure rekurzivní překladač IP adresu (například adresy 168.63.129.16) musí být přidaný do seznamu. Další informace najdete v tématu [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md). Vlastní server DNS musí být schopen překladu názvů hostitelů na následující domény a jejich subdomény: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, a *microsoftonline-p.com*. 
- **Žádné koncové body služby**: Podsíť Managed Instance nesmí mít přiřazen koncový bod služby. Ujistěte se, že při vytváření virtuální sítě je zakázána možnost koncových bodů služby.
- **Dostatek IP adres**: Podsíť Managed Instance musí mít minimální 16 IP adres (Doporučená minimální je 32 IP adresy). Další informace najdete v tématu [určit velikost podsítě pro Managed instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Můžete nasadit spravované instance v [existující síť](sql-database-managed-instance-configure-vnet-subnet.md) po můžete nakonfigurovat k uspokojení [Managed Instance, požadavky na síť](#network-requirements), nebo vytvořte [novou síť a podsíť](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nebude moct nasadit nové Managed Instance, pokud není kompatibilní se všemi těmito požadavky cílové podsíti. Po vytvoření Managed Instance *zásady sítě záměr* se použije v podsíti, aby se zabránilo nekompatibilních změn konfigurace sítě. Po poslední instance se odebere z podsítě *zásady sítě záměr* bude odebrán také

### <a name="mandatory-inbound-security-rules"></a>Pravidla povinné zabezpečení příchozích dat 

| Název       |Port                        |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|Správa  |9000, 9003, 1438, 1440, 1452|TCP     |Všechny              |Všechny        |Povolit |
|mi_subnet   |Všechny                         |Všechny     |MI PODSÍTĚ        |Všechny        |Povolit |
|health_probe|Všechny                         |Všechny     |AzureLoadBalancer|Všechny        |Povolit |

### <a name="mandatory-outbound-security-rules"></a>Povinné odchozí pravidla zabezpečení 

| Název       |Port          |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|Správa  |80, 443, 12000|TCP     |Všechny              |Všechny        |Povolit |
|mi_subnet   |Všechny           |Všechny     |Všechny              |MI PODSÍTĚ  |Povolit |

  > [!Note]
  > I když povinné zabezpečení příchozích pravidel povolit provoz z _jakékoli_ zdroje na portech 9000 9003, 1438, 1440, 1452 tyto porty jsou chráněné bránou firewall integrované. To [článku](sql-database-managed-instance-find-management-endpoint-ip-address.md) ukazuje, jak lze zjistit IP adresu koncového bodu správy a ověřte pravidla brány firewall. 
  
  > [!Note]
  > Pokud používáte transakční replikace ve spravované instanci a všechny databáze ve spravované instanci slouží jako vydavatel či distributor, port 445 (odchozí TCP) také musí být otevřené v pravidlech zabezpečení podsítě pro přístup ke sdílené složce Azure.
  
## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md)
- Zjistěte, jak [nakonfigurovat nové sítě VNet](sql-database-managed-instance-create-vnet-subnet.md) nebo [konfigurace stávající virtuální síť](sql-database-managed-instance-configure-vnet-subnet.md) kde můžete nasadit spravované instance.
- [Vypočítat na velikost podsítě](sql-database-managed-instance-determine-size-vnet-subnet.md) nasazování spravovaných instancí. 
- Pro rychlý start zjistit, jak vytvořit spravovanou instanci:
  - Z [webu Azure portal](sql-database-managed-instance-get-started.md)
  - pomocí [prostředí PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - pomocí [šablony Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - pomocí [šablony Azure Resource Manageru (jumpbox pomocí SSMS zahrnuté)](https://portal.azure.com/)
