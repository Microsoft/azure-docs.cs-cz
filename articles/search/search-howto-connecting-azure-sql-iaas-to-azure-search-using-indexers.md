---
title: Azure VM připojení k virtuálnímu počítači SQL pro indexování – vyhledávání Azure
description: Povolit šifrované připojení a nastavení brány firewall a povolit připojení k systému SQL Server na virtuálním počítači Azure (VM) z indexeru Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2efc0b76c8556894119ed3f6dd216234414cf313
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732347"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurace připojení indexeru Azure Search k systému SQL Server na Virtuálním počítači Azure
Jak je uvedeno v [připojení Azure SQL Database a Azure Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), vytváření indexery proti **systému SQL Server na virtuálních počítačích Azure** (nebo **virtuální počítače s SQL Azure** zkráceně) je podporováno Služba Azure Search ale existuje několik požadavků souvisejících se zabezpečením abyste dbali na první. 

Připojení z Azure Search k systému SQL Server na virtuálním počítači je veřejné připojení k Internetu. Všechny bezpečnostní opatření, které byste běžně postupovali pro tato připojení použijte zde také:

+ Získat certifikát od [poskytovatele certifikační autority](https://en.wikipedia.org/wiki/Certificate_authority#Providers) pro plně kvalifikovaný název domény virtuálního počítače Azure.
+ Nainstalujte certifikát na virtuálním počítači a pak povolte a nakonfigurujte šifrovaná připojení na virtuálním počítači podle pokynů v tomto článku.

## <a name="enable-encrypted-connections"></a>Povolit šifrované připojení
Služba Azure Search vyžaduje šifrovaný kanál pro všechny požadavky indexer přes veřejné připojení k Internetu. Tato část uvádí kroky, aby tuto práci.

1. Zkontrolujte vlastnosti certifikát, který chcete ověřit, jestli že je název předmětu plně kvalifikovaný název domény (FQDN) virtuálního počítače Azure. Chcete-li zobrazit vlastnosti můžete použít nástroje, jako je CertUtils nebo modulu snap-in Certifikáty. Úplný název domény můžete získat z částí základní údaje okna služby virtuálního počítače, v **veřejné IP adresy nebo DNS název popisku** pole [webu Azure portal](https://portal.azure.com/).
   
   * Pro virtuální počítače vytvořené pomocí novější **Resource Manageru** šablony, plně kvalifikovaný název domény je formátován jako `<your-VM-name>.<region>.cloudapp.azure.com`. 
   * Pro starší virtuální počítače vytvořené jako **Classic** virtuálního počítače, plně kvalifikovaný název domény je formátován jako `<your-cloud-service-name.cloudapp.net>`. 
2. Konfigurace serveru SQL pro použití certifikátu pomocí Editoru registru (regedit). 
   
    I když SQL Server Configuration Manager se často používá pro tuto úlohu, ho nelze použít pro tento scénář. Importovaný certifikát nenajde protože plně kvalifikovaný název domény virtuálního počítače v Azure pravděpodobně neshoduje s plně kvalifikovaný název domény podle virtuálního počítače (identifikuje domény jako místního počítače nebo síťové domény, ke kterému je připojený). Když se názvy neshodují, použijte regedit a určete certifikát.
   
   * V editoru registru přejděte na tento klíč registru: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     `[MSSQL13.MSSQLSERVER]` Částí se liší v závislosti na verzi a instance název. 
   * Nastavte hodnotu **certifikát** klíč **kryptografický otisk** certifikátu protokolu SSL, který jste naimportovali do virtuálního počítače.
     
     Existuje několik způsobů, jak získat kryptografický otisk, některé lépe než jiné. Pokud zkopírujete z **certifikáty** modul snap-in konzoly MMC, budete pravděpodobně vyzvedne, až bude neviditelné úvodní znak [jak je popsáno v tomto článku podpory](https://support.microsoft.com/kb/2023869/), což vede k chybě při pokusu o připojení . Existuje několik alternativní řešení pro opravu tohoto problému. Nejjednodušší je smažte vše přes a opětovným zadáním prvního znaku kryptografický otisk, odebrat úvodní znak v poli hodnota klíče v editoru registru. Alternativně můžete jiný nástroj zkopírujte kryptografický otisk.
3. Udělení oprávnění k účtu služby. 
   
    Ujistěte se, že účet služby SQL Server je udělena příslušná oprávnění pro privátní klíč certifikátu protokolu SSL. Pokud jste tento krok přehlédnout, nebude spustit systém SQL Server. Můžete použít **certifikáty** modul snap-in nebo **CertUtils** pro tuto úlohu.
4. Restartujte službu SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurace připojení k serveru SQL Server ve virtuálním počítači
Jakmile nastavíte šifrované připojení vyžaduje služba Azure Search, existují další kroky konfigurace přirozené pro SQL Server na virtuálních počítačích Azure. Pokud jste tak ještě neučinili, dalším krokem je dokončení konfigurace pomocí jedné z těchto článků:

* Pro **Resource Manageru** virtuálního počítače, naleznete v tématu [připojit k SQL Server na virtuálním počítači Azure s využitím Resource Manageru](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Pro **Classic** virtuálního počítače, naleznete v tématu [připojit k SQL Server na virtuálním počítači Azure Classic](../virtual-machines/windows/classic/sql-connect.md).

Konkrétně si projděte informace v části každého článku "propojení prostřednictvím Internetu".

## <a name="configure-the-network-security-group-nsg"></a>Nakonfigurovat skupinu zabezpečení sítě (NSG)
Není ke konfiguraci NSG a odpovídající koncový bod Azure nebo seznamu řízení přístupu (ACL) zpřístupnit svého virtuálního počítače Azure do jiné strany. Je pravděpodobné, že kroky dokončíte před povolit vlastní aplikační logiku a připojte se k virtuálnímu počítači s SQL Azure. To se nijak neliší pro připojení k Azure Search k vašemu virtuálnímu počítači SQL Azure. 

Níže uvedené odkazy poskytují pokyny na konfiguraci NSG pro nasazení virtuálních počítačů. Pomocí těchto pokynů a řízení přístupu na základě jeho IP adresy koncového bodu Azure Search.

> [!NOTE]
> Na pozadí, naleznete v tématu [co je skupina zabezpečení sítě?](../virtual-network/security-overview.md)
> 
> 

* Pro **Resource Manageru** virtuálního počítače, naleznete v tématu [vytvoření skupin zabezpečení sítě pro nasazení ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Pro **Classic** virtuálního počítače, naleznete v tématu [vytvoření skupin zabezpečení sítě pro nasazení Classic](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Přidělování IP adres může představovat několik problémů, které jsou snadno překonat, pokud víte o problému a možná alternativní řešení. Zbývající části obsahují doporučení pro zpracování problémů souvisejících s IP adresami v seznamu ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Omezení přístupu na IP adresu služby search
Důrazně doporučujeme omezit přístup na IP adresu vaší služby search v seznamu ACL místo provedení zcela otevřená vašich virtuálních počítačích SQL Azure pro všechny žádosti o připojení. IP adresu můžete snadno zjistit pomocí příkazu ping plně kvalifikovaný název domény (například `<your-search-service-name>.search.windows.net`) vaší služby search.

#### <a name="managing-ip-address-fluctuations"></a>Správa IP adres kolísání
Pokud vaše vyhledávací služba má pouze jednu jednotku vyhledávání (to znamená, že jednu repliku a jeden oddíl), IP adresa se změní v průběhu restartování běžné služby, proto už existující seznam ACL s IP adresou vaše vyhledávací služba není platná.

Jedním ze způsobů, aby se zabránilo chybě následné připojení se má používat více než jednu repliku a jeden oddíl ve službě Azure Search. To zvyšuje náklady, ale také řeší problém IP adresu. Ve službě Azure Search neměnit IP adresy, když máte víc než jedné jednotky vyhledávání.

Druhý postup je povolit připojení služeb při selhání, a potom znovu nakonfigurovat seznamy ACL v této skupině. Můžete očekávat v průměru IP adresy, chcete-li změnit každých několik týdnů. Pro zákazníky, kteří řízené indexování na základě úlohy s řídkým tento přístup může být přijatelné.

Třetí možné (ale zejména zabezpečené) se zadává rozsah IP adres oblasti Azure, kde je zřízené služby search. Seznam rozsahů IP adres, ze kterých jsou přidělené veřejné IP adresy k prostředkům Azure je zveřejněný na webu [rozsahy IP adres Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Zahrnout IP adresy portálu Azure Search
Pokud používáte na webu Azure portal k vytvoření indexeru, logiku na portálu Azure Search také potřebuje přístup k vašemu virtuálnímu počítači SQL Azure při vytváření. Služba Azure search na portálu IP adresy najdete pomocí příkazu ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Další postup
Eliminuje konfigurací můžete teď určit systému SQL Server na virtuálním počítači Azure jako zdroj dat pro indexer Azure Search. Zobrazit [připojení Azure SQL Database a Azure Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) Další informace.

