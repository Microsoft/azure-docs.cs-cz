---
title: Zabezpečení sítě v Azure Data Lake Storage Gen1 | Microsoft Docs
description: Principy integrace brány firewall protokolu IP a virtuální sítě v Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168064"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Integrace virtuální sítě pro Azure Data Lake Storage Gen1 – Preview

Představujeme integraci virtuální sítě pro Azure Data Lake Storage Gen1 (ve verzi Preview). Integrace virtuální sítě pomáhá zajistit ochranu před neoprávněným přístupem k účtům Azure Data Lake Storage Gen1 tím, že tyto účty uzamkne pro konkrétní virtuální sítě a podsítě. Účet ADLS Gen1 teď můžete nakonfigurovat tak, aby přijímal provoz pouze z určených virtuálních sítí a podsítí a blokoval přístup odjinud. Pomůžete tím zabezpečit účet ADLS před vnějšími hrozbami.

Integrace virtuální sítě pro ADLS Gen1 využívá zabezpečení koncového bodu služby virtuální sítě mezi vaší virtuální sítí a službou Azure Active Directory ke generování dalších deklarací identity zabezpečení v přístupovém tokenu. Tyto deklarace identity pak slouží k ověření vaší virtuální sítě v účtu ADLS Gen1 a povolení přístupu.

> [!NOTE]
> Tato technologie je ve verzi Preview a nedoporučujeme ji používat v produkčních prostředích.
>
> Na používání těchto funkcí se nevztahují žádné další poplatky. Za váš účet se bude účtovat standardní sazba za ADLS Gen1 ([ceny](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) a všechny služby Azure, které používáte ([ceny](https://azure.microsoft.com/pricing/#product-picker)).

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>Scénáře integrace virtuální sítě pro ADLS Gen1

Integrace virtuální sítě ADLS Gen1 umožňuje omezit přístup k účtu ADLS Gen1 na určené virtuální sítě a podsítě.  Jakmile se váš účet uzamkne pro zadané virtuální sítě a podsítě, ostatní virtuální sítě a virtuální počítače v Azure k němu nebudou mít povolený přístup.  Integrace virtuální sítě ADLS Gen1 umožňuje funkčně stejný scénář jako [koncové body služby virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview).  V následujících částech najdete podrobný popis několika klíčových rozdílů. 

![Diagram scénáře integrace virtuální sítě ADLS Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Kromě pravidel virtuální sítě je možné k povolení přístupu z místních sítí použít také stávající pravidla brány firewall protokolu IP. 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>Optimální směrování s využitím integrace virtuální sítě ADLS Gen1

Klíčovou výhodou koncových bodů služby virtuální sítě je [optimální směrování](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) z vaší virtuální sítě.  Pokud chcete dosáhnout stejné optimalizace tras u účtů ADLS Gen1, použijte následující [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) z vaší virtuální sítě k vašemu účtu ADLS Gen1:

- **ADLS Public IP Address** (Veřejná IP adresa ADLS) – Použijte veřejnou IP adresu cílových účtů ADLS Gen1.  IP adresy vašeho účtu ADLS Gen1 můžete zjistit [překladem názvů DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) vašich účtů.  Pro každou adresu vytvořte samostatný záznam.

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>Únik dat z virtuální sítě zákazníka

Kromě zabezpečení přístupu k účtům ADLS z virtuálních sítí by vás mohlo také zajímat, jak zajistit, že nedojde k žádnému úniku do neoprávněného účtu.

Doporučujeme ve virtuální síti použít řešení brány firewall k filtrování odchozího provozu na základě adresy URL cílového účtu a povolit přístup pouze autorizovaným účtům ADLS Gen1.

Mezi dostupné možnosti patří:
- [Azure Firewall:](https://docs.microsoft.com/azure/firewall/overview) Pro virtuální síť můžete [nasadit a nakonfigurovat službu Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) a zajistit tak zabezpečení odchozího provozu ADLS a jeho uzamčení pro adresu URL známého a autorizovaného účtu.
- Brána firewall [síťového virtuálního zařízení](https://azure.microsoft.com/solutions/network-appliances/): Pokud váš správce autorizuje používání bran firewall pouze od určitých komerčních dodavatelů, můžete stejného výsledku docílit použitím řešení brány firewall síťového virtuálního zařízení, které je k dispozici na webu Azure Marketplace.

> [!NOTE]
> Použitím brány firewall v cestě k datům se do ní přidá další segment směrování, což může mít vliv na výkon sítě při výměně dat od začátku do konce, včetně dostupné propustnosti a latence připojení. 

## <a name="limitations"></a>Omezení
1.  Clustery HDInsight je potřeba po přidání do verze Preview vytvořit znovu.  Clustery vytvořené před zavedením podpory integrace virtuální sítě ADLS Gen1 je potřeba vytvořit znovu, aby tuto novou funkci podporovaly. 
2.  Pokud při vytváření nového clusteru HDInsight vyberete účet ADLS Gen1 s povolenou integrací virtuální sítě, celý proces selže. Musíte nejprve zakázat pravidlo virtuální sítě nebo můžete v okně **Brána firewall a virtuální sítě** účtu ADLS **povolit přístup ze všech sítí a služeb**.  Další informace najdete v části [Výjimky](##Exceptions).
3.  Integrace virtuální sítě ADLS Gen1 ve verzi Preview nefunguje se [spravovanými identitami pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
4.  Data v souborech a složkách ve vašem účtu ADLS Gen1 s podporou virtuálních sítí nejsou přístupná na portálu.  To zahrnuje přístup z virtuálního počítače v rámci virtuální sítě a aktivity, jako je používání Průzkumníka dat.  Aktivity správy účtu fungují dál.  Data v souborech a složkách ve vašem účtu ADLS Gen1 s podporou virtuálních sítí jsou přístupná přes prostředky mimo portál: přístup s využitím sad SDK, skripty PowerShellu, další služby Azure (pokud nepocházejí z portálu) atd. 

## <a name="configuration"></a>Konfigurace

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>Krok 1: Konfigurace virtuální sítě pro používání koncového bodu služby AAD
1.  Přejděte na web Azure Portal a přihlaste se ke svému účtu. 
2.  Ve svém předplatném [vytvořte novou virtuální síť](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) nebo přejděte k existující virtuální síti.  V současné době musí být virtuální síť ve stejné oblasti jako účet ADLS Gen1. 
3.  V okně virtuální sítě zvolte **Koncové body služby**. 
4.  Kliknutím na **Přidat** přidejte nový koncový bod služby.
![Přidání koncového bodu služby virtuální sítě](media/data-lake-store-network-security/config-vnet-1.png)
5.  Jako službu pro koncový bod zvolte **Microsoft.AzureActiveDirectory**.
![Výběr koncového bodu služby Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)
6.  Zvolte podsítě, pro které chcete povolit možnosti připojení, a klikněte na **Přidat**.
![Výběr podsítě](media/data-lake-store-network-security/config-vnet-3.png)
7.  Přidání koncového bodu služby může trvat až 15 minut. Po přidání se zobrazí v seznamu. Ověřte, že se zobrazí a že všechny podrobnosti odpovídají konfiguraci. 
![Úspěšné přidání koncového bodu služby](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>Krok 2: Nastavení povolených virtuálních sítí a podsítí pro účet ADLS Gen1
1.  Po dokončení konfigurace virtuální sítě ve svém předplatném [vytvořte nový účet Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) nebo přejděte k existujícímu účtu ADLS Gen1. V současné době musí být účet ADLS Gen1 ve stejné oblasti jako virtuální síť. 
2.  Zvolte **Brána firewall a virtuální sítě**.

  > [!NOTE]
  > Pokud se **Brána firewall a virtuální sítě** v nastavení nezobrazí, odhlaste se z portálu. Zavřete prohlížeč. Vymažte mezipaměť prohlížeče. Restartujte počítač a zkuste to znovu.

  ![Přidání pravidla virtuální sítě k účtu ADLS](media/data-lake-store-network-security/config-adls-1.png)
3.  Zvolte **Vybrané sítě**. 
4.  Klikněte na **Přidat existující virtuální síť**.
  ![Přidání existující virtuální sítě](media/data-lake-store-network-security/config-adls-2.png)
5.  Zvolte virtuální sítě a podsítě, kterým chcete povolit možnosti připojení, a klikněte na **Přidat**.
  ![Výběr virtuálních sítí a podsítí](media/data-lake-store-network-security/config-adls-3.png)
6.  Ujistěte se, že se virtuální sítě a podsítě správně zobrazí v seznamu, a vyberte **Uložit**.
  ![Uložení nového pravidla](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > Po uložení může trvat až 5 minut, než se nastavení projeví.

7.  [Volitelné] Pokud chcete kromě virtuálních sítí a podsítí umožnit připojení z určitých IP adres, můžete to provést na stejné stránce v části **Brána firewall**. 

## <a name="exceptions"></a>Výjimky
V okně **Brána firewall a virtuální sítě** v oblasti Výjimky jsou dvě zaškrtávací políčka pro povolení připojení ze sady služeb a virtuálních počítačů v Azure.
![Výjimky brány firewall a virtuální sítě](media/data-lake-store-network-security/firewall-exceptions.png)
- Možnost **Povolit všem službám Azure přistupovat k tomuto účtu Data Lake Storage Gen1** umožňuje všem službám Azure, jako je Azure Data Factory, Event Hubs, všechny virtuální počítače Azure atd. komunikovat s vaším účtem ADLS.

- Možnost **Povolit službě Azure Data Lake Analytics přistupovat k tomuto účtu Data Lake Storage Gen1** umožňuje připojení služby Azure Data Lake Analytics k tomuto účtu ADLS. 

Doporučujeme nechat tyto výjimky vypnuté a zapnout je pouze v případě, že potřebujete možnosti připojení z těchto dalších služeb mimo vaši virtuální síť.
