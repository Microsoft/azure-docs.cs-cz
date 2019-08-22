---
title: Připojení virtuálního počítače Azure SQL pro hledání indexování – Azure Search
description: Povolte šifrovaná připojení a nakonfigurujte bránu firewall tak, aby povolovala připojení SQL Server na virtuálním počítači Azure pomocí indexeru v Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7629750da8f58c2c62f15102b60b5b562689f087
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656701"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurace připojení z Azure Search indexeru pro SQL Server na virtuálním počítači Azure
Jak je uvedeno v článku [připojení Azure SQL Database k Azure Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), vytváření indexerů proti **SQL Server na virtuálních počítačích Azure** (nebo **SQL Azure virtuálních počítačů** pro krátké počítače) podporuje Azure Search, ale existuje několik požadavků týkajících se zabezpečení pro Nejdřív se postarujte. 

Připojení z Azure Search k SQL Server na virtuálním počítači je veřejné připojení k Internetu. Tady se vztahují všechny bezpečnostní míry, které byste normálně použili pro tato připojení:

+ Získejte certifikát od [poskytovatele certifikační autority](https://en.wikipedia.org/wiki/Certificate_authority#Providers) pro plně kvalifikovaný název domény instance SQL Server na virtuálním počítači Azure.
+ Nainstalujte certifikát na virtuální počítač a pak podle pokynů v tomto článku povolte a nakonfigurujte šifrovaná připojení na virtuálním počítači.

## <a name="enable-encrypted-connections"></a>Povolit šifrovaná připojení
Azure Search vyžaduje šifrovaný kanál pro všechny požadavky indexeru přes veřejné internetové připojení. V této části najdete seznam kroků, které je potřeba udělat.

1. Zkontrolujte vlastnosti certifikátu a ověřte, že název subjektu je plně kvalifikovaný název domény (FQDN) virtuálního počítače Azure. K zobrazení vlastností můžete použít nástroj jako například CertUtil nebo modul snap-in Certifikáty. Plně kvalifikovaný název domény můžete získat z oddílu základy v okně služby VM, a to v poli **název veřejné IP adresy/název DNS popisek** v [Azure Portal](https://portal.azure.com/).
   
   * Pro virtuální počítače vytvořené pomocí novější šablony **Správce prostředků** je plně kvalifikovaný název domény formátovaný jako`<your-VM-name>.<region>.cloudapp.azure.com`
   * U starších virtuálních počítačů vytvořených jako **klasický** virtuální počítač je plně kvalifikovaný název domény `<your-cloud-service-name.cloudapp.net>`formátovaný jako.

2. Nakonfigurujte SQL Server k použití certifikátu pomocí Editoru registru (Regedit). 
   
    I když se pro tuto úlohu často používá SQL Server Configuration Manager, nemůžete ji použít pro tento scénář. Nenalezne importovaný certifikát, protože plně kvalifikovaný název domény virtuálního počítače v Azure se neshoduje s plně kvalifikovaným názvem domény stanoveným VIRTUÁLNÍm počítačem (doména identifikuje buď jako místní počítač, nebo do síťové domény, ke které je připojená). Pokud se názvy neshodují, určete certifikát pomocí příkazu regedit.
   
   * V nástroji Regedit přejděte do tohoto klíče registru `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`:.
     
     Část `[MSSQL13.MSSQLSERVER]` se liší v závislosti na verzi a názvu instance. 
   * Nastavte hodnotu klíče **certifikátu** na **kryptografický otisk** certifikátu protokolu SSL, který jste importovali do virtuálního počítače.
     
     Existuje několik způsobů, jak kryptografický otisk získat, což je lepší než jiné. Pokud ho zkopírujete z modulu snap-in **certifikáty** konzoly MMC, pravděpodobně si vyřadíte neviditelný úvodní znak, [jak je popsáno v tomto článku podpory](https://support.microsoft.com/kb/2023869/), což způsobí chybu při pokusu o připojení. Pro opravu tohoto problému existuje několik alternativních řešení. Nejjednodušší je místo na začátku a pak znovu zadat první znak kryptografického otisku, který odebere úvodní znak v poli hodnota klíče v regedit. Alternativně můžete použít jiný nástroj ke zkopírování kryptografického otisku.

3. Udělte oprávnění k účtu služby. 
   
    Ujistěte se, že je účtu služby SQL Server uděleno příslušné oprávnění k privátnímu klíči certifikátu protokolu SSL. Pokud přehlédnout tento krok, SQL Server se nespustí. Pro tuto úlohu můžete použít modul snap-in **certifikáty** nebo **certutil** .
    
4. Restartujte službu SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurace připojení SQL Server ve virtuálním počítači
Po nastavení požadovaného šifrovaného připojení Azure Search jsou pro SQL Server na virtuálních počítačích Azure k dispozici další kroky konfigurace. Pokud jste to ještě neudělali, je dalším krokem dokončení konfigurace pomocí jednoho z těchto článků:

* **Správce prostředků** virtuálního počítače najdete v tématu [připojení k virtuálnímu počítači s SQL Server na Azure pomocí Správce prostředků](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Pro **klasický** virtuální počítač najdete informace v tématu [připojení k virtuálnímu počítači s SQL Server v Azure Classic](../virtual-machines/windows/classic/sql-connect.md).

Konkrétně si projděte část v každém článku věnované "připojování přes Internet".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurace skupiny zabezpečení sítě (NSG)
Není neobvyklé konfigurovat NSG a odpovídající koncový bod Azure ani seznam Access Control (ACL), aby váš virtuální počítač Azure byl přístupný ostatním stranám. Je pravděpodobné, že jste to udělali předtím, než povolíte vlastní aplikační logiku pro připojení k vašemu SQL Azuremu VIRTUÁLNÍmu počítači. Azure Search připojení k VIRTUÁLNÍmu počítači SQL Azure se neliší. 

Odkazy níže poskytují pokyny týkající se konfigurace NSG pro nasazení virtuálních počítačů. Tyto pokyny použijte pro seznam ACL Azure Search koncového bodu na základě jeho IP adresy.

> [!NOTE]
> Základní informace najdete v tématu [co je skupina zabezpečení sítě](../virtual-network/security-overview.md) .
> 
> 

* **Správce prostředků** virtuálního počítače najdete v tématu [jak vytvořit skupin zabezpečení sítě pro nasazení ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Pro **klasický** virtuální počítač si přečtěte téma [jak vytvořit skupin zabezpečení sítě pro nasazení Classic](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Přidělování IP adres může představovat několik výzev, které je možné snadno překonat, pokud víte o problému a možných alternativních řešeních. Zbývající části poskytují doporučení pro zpracování problémů souvisejících s IP adresami v seznamu ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Omezení přístupu k IP adrese vyhledávací služby
Důrazně doporučujeme, abyste omezili přístup k IP adrese vaší vyhledávací služby v seznamu ACL a nechcete, aby se vaše SQL Azure virtuální počítače otevíraly na jakékoli žádosti o připojení. IP adresu můžete snadno zjistit pomocí testu plně kvalifikovaného názvu domény (například `<your-search-service-name>.search.windows.net`) vaší vyhledávací služby.

#### <a name="managing-ip-address-fluctuations"></a>Správa výkyvů IP adres
Pokud vaše vyhledávací služba obsahuje jenom jednu jednotku vyhledávání (tj. jednu repliku a jeden oddíl), IP adresa se během rutiny běžného spuštění služby změní a naruší se platnost stávajícího seznamu ACL s IP adresou vaší vyhledávací služby.

Jedním ze způsobů, jak se vyhnout následné chybě připojení, je použít více než jednu repliku a jeden oddíl v Azure Search. Tím se zvýší náklady, ale také se vyřeší problém s IP adresou. V Azure Search se IP adresy nemění, pokud máte více než jednu jednotku vyhledávání.

Druhý postup je, aby bylo možné připojení selhat a pak znovu nakonfigurovat seznamy řízení přístupu (ACL) v NSG. V průměru můžete očekávat, že se IP adresy mění každých několik týdnů. Pro zákazníky, kteří provedli průběžné indexování, může být tento přístup životaschopný.

Třetí schopný (ale ne obzvláště zabezpečený) přístup je zadání rozsahu IP adres oblasti Azure, ve které se vaše vyhledávací služba zřídí. Seznam rozsahů IP adres, ze kterých se k prostředkům Azure přiděluje veřejné IP adresy, se zveřejňuje v [rozsahu IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Zahrnout IP adresy portálu Azure Search
Pokud používáte Azure Portal k vytvoření indexeru, Azure Search logika portálu také potřebuje přístup k vašemu VIRTUÁLNÍmu počítači s SQL Azure během vytváření. IP adresy portálu Azure Search můžete najít pomocí nástroje Testing `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Další postup
V případě konfigurace ze systému teď můžete jako zdroj dat pro Azure Search indexer zadat SQL Server na virtuálním počítači Azure. Další informace najdete v tématu [připojení Azure SQL Database k Azure Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .

