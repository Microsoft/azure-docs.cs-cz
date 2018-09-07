---
title: Azure SQL Database Managed Instance připojení architektury | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komunikace Azure SQL Database Managed Instance a také architektura připojení vysvětluje, jak různé součásti fungovat směrovat přenos dat do Managed Instance.
keywords: ''
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.subservice: managed instance
ms.custom: ''
ms.date: 08/16/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.topic: conceptual
ms.openlocfilehash: 56a1e7038515a64f1dfe3fda639213bcfcde63d6
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053427"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database Managed Instance architektura připojení 

Tento článek obsahuje přehled komunikace Azure SQL Database Managed Instance a také architektura připojení vysvětluje, jak různé součásti fungovat směrovat přenos dat do Managed Instance.  

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
> Ke zlepšení dostupnosti expirience a služby zákazníka, Microsoft využívá zásady sítě záměr na prvky infrastruktury virtuální sítě Azure, které by mohly ovlivnit fungování Managed Instance. Jedná se o platformě mechanismus pro komunikaci transparentně síťové požadavky na koncové uživatele, s hlavním cílem, aby se předešlo chybného síťového nastavení a zajistit normální provoz Managed Instance. Při odstranění Managed Instance bude odebrán také záměr zásady sítě. 

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuální cluster architektura připojení 

Pojďme se dozvědět více o v architektuře připojení k Managed Instance. Následující diagram znázorňuje koncepční rozložení virtuálních clusterů. 

![připojení k architektuře diagram virtuální cluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienti připojit k Managed Instance pomocí názvu hostitele, který má podobu < mi_name >. <clusterid>. database.windows.net. Tento název hostitele se přeloží na privátní IP adresu, i když je zaregistrovaný ve veřejné zóny DNS a je veřejně přeložitelného. 

Tato privátní IP adresa patří k na spravované instanci interní zatížení nástroje pro vyrovnávání (ILB), která směruje provoz do na spravované instanci brány (gs). Více spravovaných instancí může potenciálně spuštění ve stejném clusteru GW používá název hostitele Managed Instance pro přesměrování přenosu dat na správné služby stroj SQL. 

Nasazení a správu služeb se připojit k Managed Instance pomocí veřejného koncového bodu, který se mapuje na externím vyrovnáváním zatížení. Provoz se směruje do uzlů pouze tehdy, pokud byla přijata na předdefinované sady porty, které používá jediná komponentami pro správu Managed Instance. Veškerá komunikace mezi komponentami správy a rovina správy se vzájemně certifikát ověřit. 

## <a name="next-steps"></a>Další postup 

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md) 
- Další informace o konfiguraci virtuální sítě najdete v tématu [Managed Instance konfigurace virtuální sítě](sql-database-managed-instance-vnet-configuration.md). 
- Pro rychlý start zjistit, jak vytvořit spravovanou instanci: 
  - Z [webu Azure portal](sql-database-managed-instance-get-started.md) 
  - pomocí [prostředí PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 
  - pomocí [šablony Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 
  - pomocí [šablony Azure Resource Manageru (jumpbox pomocí SSMS zahrnuté)](https://portal.azure.com/) 

