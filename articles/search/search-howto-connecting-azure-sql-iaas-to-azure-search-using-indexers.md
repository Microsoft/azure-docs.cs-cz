---
title: Připojení virtuálního počítače Azure SQL pro indexování vyhledávání
titleSuffix: Azure Cognitive Search
description: Povolte šifrovaná připojení a nakonfigurujte bránu firewall tak, aby umožňovala připojení k SQL Serveru na virtuálním počítači Azure (VM) z indexeru v Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1ab2b7860e8a75da5f8acef2fc4fa54d4b73a30d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256959"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurace připojení z indexeru Azure Cognitive Search na SQL Server na virtuálním počítači Azure

Jak je uvedeno v [článku Připojení Azure SQL Database k Azure Cognitive Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), vytváření indexerů proti **SQL Serveru na virtuálních počítačích Azure** (zkráceně **virtuálních počítačích SQL Azure)** je podporováno Azure Cognitive Search, ale existuje několik předpokladů souvisejících se zabezpečením, které se nejprve postarají. 

Připojení z Azure Cognitive Search na SQL Server na virtuálním počítači je veřejné připojení k internetu. Zde platí také všechna bezpečnostní opatření, která byste pro tato připojení normálně dodržovali:

+ Získejte certifikát od [poskytovatele certifikační autority](https://en.wikipedia.org/wiki/Certificate_authority#Providers) pro plně kvalifikovaný název domény instance serveru SQL Server na virtuálním počítači Azure.
+ Nainstalujte certifikát na virtuální počítač a potom povolte a nakonfigurujte šifrovaná připojení na virtuálním počítači podle pokynů v tomto článku.

## <a name="enable-encrypted-connections"></a>Povolení šifrovaných připojení
Azure Cognitive Search vyžaduje šifrovaný kanál pro všechny požadavky indexeru přes veřejné připojení k internetu. V této části jsou uvedeny kroky, aby tato práce fungovala.

1. Zkontrolujte vlastnosti certifikátu a ověřte, že název subjektu je plně kvalifikovaný název domény (Plně kvalifikovaný název domény) virtuálního počítače Azure. K zobrazení vlastností můžete použít nástroj, jako je CertUtils nebo modul snap-in Certifikáty. Hlavní název domény můžete získat z oddílu Essentials služby virtuálního počítače v poli **Veřejné IP adresy/POPISEK NÁZVU DNS** na webu Azure [Portal](https://portal.azure.com/).
   
   * Pro virtuální počítače vytvořené pomocí novější šablony **Správce prostředků** je hlavní název sítě formátován jako`<your-VM-name>.<region>.cloudapp.azure.com`
   * U starších virtuálních počítačů vytvořených jako **klasický** virtuální počítač `<your-cloud-service-name.cloudapp.net>`je hlavní název ve formátu formátován jako .

2. Nakonfigurujte SQL Server tak, aby používal certifikát pomocí Editoru registru (regedit). 
   
    Přestože SQL Server Configuration Manager se často používá pro tuto úlohu, nelze použít pro tento scénář. Importovaný certifikát nenajde, protože hlavní název domény virtuálního počítače v Azure neodpovídá vytesaného doménového názvu domény určeného virtuálním počítačem (identifikuje doménu jako místní počítač nebo síťovou doménu, ke které je připojen). Pokud se názvy neshodují, zadejte certifikát pomocí příkazu regedit.
   
   * V regedit, přejděte na `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`tento klíč registru: .
     
     Část `[MSSQL13.MSSQLSERVER]` se liší v závislosti na verzi a názvu instance. 
   * Nastavte hodnotu klíče **certifikátu** na **kryptografický otisk** certifikátu TLS/SSL, který jste importovali do virtuálního mě.
     
     Existuje několik způsobů, jak získat otisk palce, některé lepší než ostatní. Pokud jej zkopírujete z modulu **snap-in Certifikáty** v konzole MMC, pravděpodobně získáte neviditelný hlavní znak, [jak je popsáno v tomto článku podpory](https://support.microsoft.com/kb/2023869/), což má za následek chybu při pokusu o připojení. Existuje několik řešení pro opravu tohoto problému. Nejjednodušší je backspace přes a pak znovu zadejte první znak kryptografický otisk odebrat úvodní znak v poli hodnoty klíče v regedit. Případně můžete použít jiný nástroj ke kopírování kryptografického otisku.

3. Udělte oprávnění účtu služby. 
   
    Ujistěte se, že účtu služby SQL Server je uděleno příslušné oprávnění k soukromému klíči certifikátu TLS/SSL. Pokud přehlédnete tento krok, SQL Server se nespustí. Pro tuto úlohu můžete použít modul snap-in **Certifikáty** nebo **CertUtils.**
    
4. Restartujte službu SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurace připojení sql serveru ve virtuálním počítače
Po nastavení šifrované připojení vyžaduje Azure Cognitive Search, existují další kroky konfigurace vlastní SQL Server na virtuálních počítačích Azure. Pokud jste tak ještě neučinili , dalším krokem je dokončení konfigurace pomocí jednoho z těchto článků:

* Virtuální počítač **Správce prostředků** najdete v [tématu Připojení k virtuálnímu počítači SQL Server v Azure pomocí Správce prostředků](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Klasický **virtuální** počítač najdete v [tématu Připojení k virtuálnímu počítači SQL Serveru v Azure Classic](../virtual-machines/windows/classic/sql-connect.md).

Zejména si přečtěte část v každém článku pro "připojení přes internet".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurace skupiny zabezpečení sítě (NSG)
Není neobvyklé nakonfigurovat skupinu zabezpečení sítě a odpovídající koncový bod Azure nebo seznam řízení přístupu (ACL), aby váš virtuální počítač Azure přístupný ostatním stranám. Je pravděpodobné, že jste to udělali dříve, abyste povolili vlastní aplikační logiku pro připojení k virtuálnímu počítači SQL Azure. Pro připojení Azure Cognitive Search k virtuálnímu počítači SQL Azure se nijak neliší. 

Níže uvedené odkazy obsahují pokyny týkající se konfigurace sítě zabezpečení sítě pro nasazení virtuálních počítačů. Pomocí těchto pokynů acl koncový bod Azure Cognitive Search na základě jeho IP adresy.

> [!NOTE]
> Informace o pozadí naleznete [v tématu Co je skupina zabezpečení sítě?](../virtual-network/security-overview.md)
> 
> 

* Virtuální ho virtuálního počítače **Správce prostředků** najdete v tématu Jak [vytvořit skupiny nsg pro nasazení ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* U **klasického** virtuálního počítače najdete v tématu [Jak vytvořit sítě nsg pro klasická nasazení](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Adresování IP adresmůže představovat několik výzev, které lze snadno překonat, pokud jste si vědomi problému a potenciální řešení. Zbývající oddíly poskytují doporučení pro řešení problémů souvisejících s IP adresami v acl.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Omezení přístupu k Azure Cognitive Search
Důrazně doporučujeme omezit přístup k IP adrese vaší vyhledávací služby a `AzureCognitiveSearch` rozsahu IP adresy [značky služby](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) v acl namísto toho, aby vaše virtuální počítače SQL Azure byly otevřené všem požadavkům na připojení.

IP adresu můžete zjistit pomocí příkazu ping na hlavní `<your-search-service-name>.search.windows.net`skupinu k dosažení funkce (například) vyhledávací služby.

Rozsah IP adres `AzureCognitiveSearch` [servisní značky](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) můžete zjistit buď pomocí [souborů JSON ke stažení,](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) nebo pomocí [rozhraní API pro zjišťování výrobních značek](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). Rozsah IP adres se aktualizuje každý týden.

#### <a name="managing-ip-address-fluctuations"></a>Správa kolísání IP adres
Pokud má vyhledávací služba pouze jednu vyhledávací jednotku (tj. jednu repliku a jeden oddíl), změní se ip adresa během restartování běžné služby a zneplatní existující přístupový server s IP adresou vyhledávací služby.

Jedním ze způsobů, jak se vyhnout následné chybě připojení, je použití více než jedné repliky a jednoho oddílu v Azure Cognitive Search. Tím se zvýší náklady, ale také řeší problém s ADRESOU IP. V Azure Cognitive Search se IP adresy nezmění, když máte víc než jednu vyhledávací jednotku.

Druhý přístup je umožnit selhání připojení a potom překonfigurovat alokace ACv v souboru zabezpečení sítě. V průměru můžete očekávat, že ip adresy se budou měnit každých několik týdnů. Pro zákazníky, kteří provádějí řízené indexování zřídka, může být tento přístup životaschopný.

Třetí životaschopný (ale ne příliš bezpečný) přístup je určit rozsah IP adres oblasti Azure, kde je zřízena vyhledávací služba. Seznam rozsahů IP adres, ze kterých jsou veřejné IP adresy přiděleny prostředkům Azure, se publikuje v [rozsahu IP adres Datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Zahrnutí IP adres portálu Azure Cognitive Search
Pokud používáte portál Azure k vytvoření indexeru, logika portálu Azure Cognitive Search také potřebuje přístup k vašemu virtuálnímu počítači SQL Azure během doby vytvoření. IP adresy portálu Azure Cognitive Search `stamp2.search.ext.azure.com`najdete pomocí příkazu ping .

## <a name="next-steps"></a>Další kroky
S konfigurací z cesty, teď můžete zadat SQL Server na virtuálním počítači Azure jako zdroj dat pro Indexer Azure Cognitive Search. Další informace najdete [v tématu Připojení Azure SQL Database k Azure Cognitive Search pomocí indexerů.](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

