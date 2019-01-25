---
title: Zabezpečení sítě v Azure Data Lake Storage Gen1 | Microsoft Docs
description: Pochopení virtuálních integrace sítě funguje v Azure Data Lake Storage Gen1
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
ms.openlocfilehash: ed2d1256508e588000970879dae7ac653797fbf9
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883309"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integrace služby Virtual network pro Azure Data Lake Storage Gen1

Tento článek představuje integrace služby virtual network pro Azure Data Lake Storage Gen1. Díky integraci virtuální sítě můžete své účty nakonfigurovat tak, aby přijímaly provoz pouze z konkrétních virtuálních sítí a podsítí. 

Tato funkce vám pomůže zabezpečit účet Data Lake Storage před externími hrozbami.

Integrace virtuální sítě pro Data Lake Storage Gen1 využívá zabezpečení koncového bodu služby virtuální sítě mezi vaší virtuální sítí a službou Azure Active Directory (Azure AD) ke generování dalších deklarací identity zabezpečení v přístupovém tokenu. Tyto deklarace identity pak slouží k ověření vaší virtuální sítě v účtu Data Lake Storage Gen1 a povolení přístupu.

> [!NOTE]
> Na používání těchto funkcí se nevztahují žádné další poplatky. Váš účet se účtuje standardní sazbou za Data Lake Storage Gen1. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Ceny všech ostatních služeb Azure, které používáte, najdete na stránce s [cenami](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Scénáře pro integraci virtuální sítě pro Data Lake Storage Gen1

Díky integraci virtuální sítě Data Lake Storage Gen1 můžete omezit přístup k vašemu účtu Data Lake Storage Gen1 z konkrétních virtuálních sítí a podsítí. Jakmile se váš účet uzamkne pro zadané virtuální sítě a podsítě, ostatní virtuální sítě a virtuální počítače v Azure k němu nebudou mít povolený přístup. Integrace virtuální sítě Data Lake Storage Gen1 umožňuje funkčně stejný scénář jako [koncové body služby virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). V následujících částech najdete podrobný popis několika klíčových rozdílů. 

![Diagram scénáře pro integraci virtuální sítě Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Kromě pravidel virtuální sítě je možné k povolení přístupu z místních sítí použít také stávající pravidla brány firewall protokolu IP. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Optimální směrování s využitím integrace virtuální sítě Data Lake Storage Gen1

Klíčovou výhodou koncových bodů služby virtuální sítě je [optimální směrování](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) z vaší virtuální sítě. Stejnou optimalizaci tras můžete provést i u účtů Data Lake Storage Gen1. Použijte následující [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) z vaší virtuální sítě k vašemu účtu Data Lake Storage Gen1.

**Data Lake Storage public IP address** (Veřejná IP adresa Data Lake Storage) – Použijte veřejnou IP adresu cílových účtů Data Lake Storage Gen 1. IP adresy vašeho účtu Data Lake Storage Gen1 můžete zjistit [překladem názvů DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) vašich účtů. Pro každou adresu vytvořte samostatný záznam.

    ```azurecli
    # Create a route table for your resource group.
    az network route-table create --resource-group $RgName --name $RouteTableName
    
    # Create route table rules for Data Lake Storage public IP addresses.
    # There's one rule per Data Lake Storage public IP address. 
    az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet
    
    # Update the virtual network, and apply the newly created route table to it.
    az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
    ```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Únik dat z virtuální sítě zákazníka

Kromě zabezpečení přístupu k účtům Data Lake Storage z virtuálních sítí by vás mohlo také zajímat, jak zajistit, že nedojde k žádnému úniku do neoprávněného účtu.

Použijte řešení brány firewall ve vaší virtuální síti k filtrování odchozího provozu na základě adresy URL cílového účtu. Povolte přístup pouze schváleným účtům Data Lake Storage Gen1.

Mezi dostupné možnosti patří:
- [Brány Firewall Azure](https://docs.microsoft.com/azure/firewall/overview): [Nasazení a konfigurace Azure bránu firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) pro vaši virtuální síť. Zajistěte zabezpečení odchozího provozu Data Lake Storage a uzamkněte ho pro adresu URL známého a schváleného účtu.
- [Síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) brány firewall: Správce může povolit použití jenom některé obchodní brány firewall na dodavatele. Stejného výsledku dosáhnete použitím řešení brány firewall síťového virtuálního zařízení, které je k dispozici na webu Azure Marketplace.

> [!NOTE]
> Použitím brány firewall v cestě k datům se do ní přidá další segment směrování. To může mít vliv na výkon sítě při výměně dat od začátku do konce. Ovlivněná může být i dostupná propustnost a latence připojení. 

## <a name="limitations"></a>Omezení

- Clustery HDInsight, které byly vytvořeny před podporou integrace Data Lake Storage Gen1 virtuální sítě byla k dispozici je potřeba znovu vytvořit na podporu této nové funkci.
 
- Pokud vytvoříte nový cluster HDInsight a vyberete účet Data Lake Storage Gen1 s povolenou integrací virtuální sítě, proces selže. Nejprve zakažte pravidlo virtuální sítě. Případně v okně **Brána firewall a virtuální sítě** účtu Data Lake Storage vyberte **Povolit přístup ze všech sítí a služeb**. Vytvořte HDInsight cluster před nakonec znovu ji povolit pravidlo virtuální sítě nebo zrušit výběr **povolit přístup ze všech sítí a služby**. Další informace najdete v části [Výjimky](##Exceptions).

- Integrace služby virtual network data Lake Storage Gen1 nefunguje při využití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
  
- Data v souborech a složkách ve vašem účtu Data Lake Storage Gen1 s podporou virtuálních sítí nejsou přístupná na portálu. Toto omezení zahrnuje přístup z virtuálního počítače v rámci virtuální sítě a aktivity, jako je používání Průzkumníka dat. Aktivity správy účtu fungují dál. Data v souborech a složkách ve vašem účtu Data Lake Storage s podporou virtuálních sítí jsou přístupná přes všechny prostředky mimo portál. Mezi tyto prostředky patří přístup s využitím sad SDK, skripty PowerShellu a další služby Azure, pokud nepocházejí z portálu. 

## <a name="configuration"></a>Konfigurace

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Krok 1: Konfigurace virtuální sítě můžete použít koncový bod služby Azure AD

1.  Přejděte na web Azure Portal a přihlaste se ke svému účtu.
 
2.  Ve svém předplatném [vytvořte novou virtuální síť](https://docs.microsoft.com/azure/virtual-network/quick-create-portal). Případně můžete přejít k existující virtuální síti. Virtuální síť musí být ve stejné oblasti jako účet Data Lake Storage Gen1.
 
3.  V okně **Virtuální síť** vyberte **Koncové body služby**.
 
4.  Vyberte **Přidat** a přidejte nový koncový bod služby.

    ![Přidání koncového bodu služby virtuální sítě](media/data-lake-store-network-security/config-vnet-1.png)

5.  Jako službu pro koncový bod vyberte **Microsoft.AzureActiveDirectory**.

     ![Výběr koncového bodu služby Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)

6.  Vyberte podsítě, pro které chcete povolit možnosti připojení. Vyberte **Přidat**.

    ![Výběr podsítě](media/data-lake-store-network-security/config-vnet-3.png)

7.  Přidání koncového bodu služby může trvat až 15 minut. Po přidání se zobrazí v seznamu. Ověřte, že se zobrazí a že všechny podrobnosti odpovídají konfiguraci.
 
    ![Úspěšné přidání koncového bodu služby](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Krok 2: Nastavení povolených virtuální síť nebo podsítě pro svůj účet Data Lake Storage Gen1

1.  Po dokončení konfigurace virtuální sítě ve svém předplatném [vytvořte nový účet Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account). Případně můžete přejít k existujícímu účtu Data Lake Storage Gen1. Účet Data Lake Storage Gen1 musí být ve stejné oblasti jako virtuální síť.
 
2.  Vyberte **Brána firewall a virtuální sítě**.

    > [!NOTE]
    > Pokud se **Brána firewall a virtuální sítě** v nastavení nezobrazí, odhlaste se z portálu. Zavřete prohlížeč a vymažte mezipaměť prohlížeče. Restartujte počítač a zkuste to znovu.

       ![Přidání pravidla virtuální sítě do účtu Data Lake Storage](media/data-lake-store-network-security/config-adls-1.png)

3.  Vyberte **Vybrané sítě**.
 
4.  Vyberte **Přidat existující virtuální síť**.

    ![Přidání existující virtuální sítě](media/data-lake-store-network-security/config-adls-2.png)

5.  Vyberte virtuální sítě a podsítě, kterým chcete povolit možnosti připojení. Vyberte **Přidat**.

    ![Výběr virtuálních sítí a podsítí](media/data-lake-store-network-security/config-adls-3.png)

6.  Ujistěte se, že se virtuální sítě a podsítě správně zobrazí v seznamu. Vyberte **Uložit**.

    ![Uložení nového pravidla](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Po uložení může trvat až 5 minut, než se nastavení projeví.

7.  [Volitelné] Na stránce **Brána firewall a virtuální sítě** v části **Brána firewall** můžete povolit možnosti připojení z konkrétních IP adres. 

## <a name="exceptions"></a>Výjimky
Můžete povolit možnosti připojení ze služeb Azure a virtuálních počítačů mimo vaše vybrané virtuální sítě. V okně **Brána firewall a virtuální sítě** v oblasti **Výjimky** máte na výběr dvě možnosti:
 
- **Povolit přístup k tomuto účtu Data Lake Storage Gen1 všem službám Azure**. Tato možnost povolí službám Azure, jako je Azure Data Factory nebo Azure Event Hubs, a všem virtuálním počítačům Azure komunikovat s vaším účtem Data Lake Storage.

- **Povolit přístup k tomuto účtu Data Lake Storage Gen1 službě Azure Data Lake Analytics**. Tato možnost povolí možnosti připojení služby Data Lake Analytics k tomuto účtu Data Lake Storage. 

  ![Výjimky brány firewall a virtuální sítě](media/data-lake-store-network-security/firewall-exceptions.png)

Doporučujeme nechat tyto výjimky vypnuté. Zapněte je pouze v případě, že potřebujete možnosti připojení z těchto dalších služeb mimo vaši virtuální síť.
