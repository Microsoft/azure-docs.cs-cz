---
title: Virtuální počítač SQL připojení do služby Azure Search | Microsoft Docs
description: Povolit šifrované připojení a nakonfigurujte bránu firewall, aby umožňovaly připojení k systému SQL Server na virtuální počítač Azure (VM) z indexer na Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: heidist
ms.openlocfilehash: 34c5d1999625d1728e884adb794af235ba415c26
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurovat spojení z indexer Azure Search na SQL Server na virtuálním počítači Azure
Jak jsme uvedli v [připojení databáze SQL Azure do Azure Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), vytváření indexery proti **systému SQL Server na virtuálních počítačích Azure** (nebo **virtuálních počítačích SQL Azure** pro zkrácení) je podporováno ve službě Azure Search je ale několik předpoklady související se zabezpečením, která se postará o první. 

**Doba trvání úkolu:** asi 30 minut za předpokladu, že jste již nainstalovali certifikát ve virtuálním počítači.

## <a name="enable-encrypted-connections"></a>Povolit šifrované připojení
Vyhledávání systému Azure vyžaduje šifrovaný kanál pro všechny požadavky indexer přes veřejný připojení k Internetu. V této části jsou uvedené kroky, aby tato práce.

1. Zkontrolujte vlastnosti certifikát, který chcete ověřit, jestli název předmětu plně kvalifikovaný název domény (FQDN) virtuálního počítače Azure. Chcete-li zobrazit vlastnosti můžete použít nástroje, jako je CertUtils nebo modulu snap-in Certifikáty. Plně kvalifikovaný název domény můžete získat z části Essentials okně služby virtuálních počítačů v **veřejných IP adres a DNS název popisku** pole, [portál Azure](https://portal.azure.com/).
   
   * Pro virtuální počítače vytvořené pomocí novější **Resource Manager** šablony, plně kvalifikovaný název domény je formátováno jako `<your-VM-name>.<region>.cloudapp.azure.com`. 
   * Pro starší virtuální počítače vytvořené jako **Classic** virtuálních počítačů, plně kvalifikovaný název domény je formátováno jako `<your-cloud-service-name.cloudapp.net>`. 
2. Konfigurace serveru SQL pro použití certifikátu pomocí Editoru registru (regedit). 
   
    I když Správce konfigurace systému SQL Server se často používá pro tuto úlohu, nelze ho použít pro tento scénář. Importovaný certifikát nenajde protože plně kvalifikovaný název domény virtuálního počítače na platformě Azure se neshoduje se plně kvalifikovaný název domény, počítáno od virtuálního počítače (označuje domény jako místní počítač nebo doménu sítě, ke které je připojený). Když se názvy neshodují, použijte k určení certifikátu regedit.
   
   * V editoru registru, vyhledejte tento klíč registru: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     `[MSSQL13.MSSQLSERVER]` Částí se liší podle verze a instance název. 
   * Nastavte hodnotu **certifikát** klíče k **kryptografický otisk** certifikátu protokolu SSL, který jste importovali do virtuálního počítače.
     
     Chcete-li získat kryptografický otisk, některé lépe než jiné několika způsoby. Pokud zkopírujete z **certifikáty** modul snap-in konzoly MMC, budete pravděpodobně vyzvedne, až bude neviditelná úvodní znak [jak je popsáno v tomto článku podpory](https://support.microsoft.com/kb/2023869/), což vede k chybě při pokusu o připojení . Existuje několik alternativní řešení pro odstranění tohoto problému. Nejjednodušší je smažte vše přes a znovu zadejte první znak kryptografický otisk odebrat úvodní znak v poli hodnota klíče v editoru registru. Alternativně můžete jiný nástroj pro kopírování kryptografický otisk.
3. Udělení oprávnění k účtu služby. 
   
    Ujistěte se, že účet služby SQL Server je udělena příslušná oprávnění na privátní klíč certifikátu protokolu SSL. Pokud jste přehlédnout, tento krok, nebude spustit systém SQL Server. Můžete použít **certifikáty** modul snap-in nebo **CertUtils** pro tuto úlohu.
4. Restartujte službu SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurace připojení k systému SQL Server ve virtuálním počítači
Po nastavení šifrované připojení vyžaduje Azure Search, existují další kroky konfigurace vnitřní k systému SQL Server na virtuálních počítačích Azure. Pokud jste tak ještě neučinili, dalším krokem je dokončení konfigurace pomocí kterékoli z těchto článků:

* Pro **Resource Manager** virtuálních počítačů, najdete v části [připojení SQL serveru virtuálnímu počítači na platformě Azure pomocí Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Pro **Classic** virtuálních počítačů, najdete v části [připojit k SQL serveru virtuálního počítače na Azure Classic](../virtual-machines/windows/classic/sql-connect.md).

Konkrétně projděte si část v jednotlivých článků "propojení prostřednictvím Internetu".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurovat skupinu zabezpečení sítě (NSG)
Není konfigurace NSG a odpovídající koncového bodu Azure nebo seznamu řízení přístupu (ACL) pro zpřístupnění svého virtuálního počítače Azure jiných stran. Pravděpodobné, že jste to před nastavená na Povolit vlastní logiky aplikace pro připojení k virtuálnímu počítači Azure SQL. Je pro připojení k Azure Search, aby virtuální počítač SQL Azure žádné jiné. 

Odkazy dole poskytují pokyny NSG konfigurace pro nasazení virtuálních počítačů. Použijte tyto pokyny k seznamu ACL koncového bodu Azure SEarch na základě jeho IP adresy.

> [!NOTE]
> Pro informace viz [co je skupina zabezpečení sítě?](../virtual-network/virtual-networks-nsg.md)
> 
> 

* Pro **Resource Manager** virtuálních počítačů, najdete v části [postup vytvoření skupiny Nsg pro nasazení ARM](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 
* Pro **Classic** virtuálních počítačů, najdete v části [postup vytvoření skupiny Nsg pro nasazení Classic](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

IP adresy může představovat několik výzvy, které jsou snadno překonat Pokud víte o problému a potenciální řešení. Zbývající části poskytuje doporučení pro zpracování problémy související s IP adresami v seznamu ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Omezení přístupu na IP adresu služby vyhledávání
Důrazně doporučujeme omezit přístup na IP adresu služby search v seznamu ACL, místo provedení virtuální počítače Azure SQL celý otevřené žádné požadavky na připojení. Můžete snadno získat IP adresu příkazem ping plně kvalifikovaný název domény (například `<your-search-service-name>.search.windows.net`) služby search.

#### <a name="managing-ip-address-fluctuations"></a>Správa IP adres kolísání
Pokud vaši službu vyhledávání má jenom jednu jednotku vyhledávání (to znamená, jednu repliku a jeden oddíl), IP adresa se změní v průběhu běžné služby restartování zneplatnění existující ACL s IP adresou vaši službu vyhledávání.

Jedním ze způsobů, aby se zabránilo chybě následné připojení je použít víc než jednu repliku a jeden oddíl ve službě Azure Search. Tím se zvyšuje náklady, ale také řeší problém IP adresu. Ve službě Azure Search neměnit IP adresy, když máte více než jednu jednotku vyhledávání.

Druhý postup je povolit připojení k selhání a potom znovu nakonfigurovat seznamy ACL v této skupině. V průměru můžete očekávat, že IP adresy, chcete-li změnit každých několik týdnů. Pro zákazníky, kteří řízené indexu na základě jen zřídka může být vhodným tento přístup.

Třetí přístup přijatelná (ale zvlášť zabezpečené) slouží k zadání rozsah IP adres oblasti Azure, kde je zřízený vaši službu vyhledávání. Seznam rozsahů IP adres, ze kterých se veřejné IP adresy přidělené prostředky Azure je publikován v [rozsahy IP Datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Zahrnout IP adresy portálu Azure Search
Pokud používáte portál Azure k vytvoření indexer, logiky na portálu Azure Search také potřebuje přístup k virtuálnímu počítači Azure SQL při vytváření. IP adresy portálu Azure search najdete otestováním pomocí `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Další postup
S konfigurací stranou můžete nyní zadejte SQL Server na virtuálním počítači Azure jako zdroj dat pro indexer Azure Search. V tématu [připojení databáze SQL Azure do Azure Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) Další informace.

