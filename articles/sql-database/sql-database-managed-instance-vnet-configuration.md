---
title: Azure SQL Database Managed Instance konfigurace virtuální sítě | Dokumentace Microsoftu
description: Toto téma popisuje možnosti konfigurace pro virtuální síť (VNet) s Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: f634167f24c221e702696174ea86a212c535695b
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056642"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurace virtuální sítě pro spravovanou instanci Azure SQL Database

Azure SQL Database Managed Instance (preview) musí být nasazen v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md). Toto nasazení umožňuje následující scénáře: 
- Připojení k Managed Instance přímo z místní sítě 
- Připojení Managed Instance pro odkazovaný server nebo další místní úložiště dat 
- Připojování k prostředkům Azure Managed Instance  

## <a name="plan"></a>Plánování

Naplánujte, jak nasadit spravované Instance ve virtuální síti pomocí odpovědi na následující otázky: 
- Máte v úmyslu nasadit jeden nebo více spravovaných instancí? 

  Počet spravovaných instancí Určuje minimální velikost podsítě k přidělení pro Managed instance. Další informace najdete v tématu [určit velikost podsítě pro Managed Instance](#determine-the-size-of-subnet-for-managed-instances). 
- Je potřeba k nasazení Managed Instance do existující virtuální síť nebo vytvoříte novou síť? 

   Pokud plánujete použití existující virtuální sítě, budete muset upravit tuto konfiguraci sítě tak, aby vyhovovaly Managed Instance. Další informace najdete v tématu [upravit existující virtuální sítě pro Managed Instance](#modify-an-existing-virtual-network-for-managed-instances). 

   Pokud budete chtít vytvořit novou virtuální síť, přečtěte si téma [vytvořit novou virtuální síť pro Managed Instance](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Požadavky

Pro vytvoření Managed Instance je třeba vyhradit podsítě uvnitř virtuální sítě, který splňuje následující požadavky:
- **Být prázdný**: podsítě nesmí obsahovat jiné cloudové služby přidružené k němu a nesmí se jednat o podsíť brány. Nebudete mít k vytvoření Managed Instance v podsíti, která obsahuje prostředky než spravovaná instance nebo přidat další prostředky v podsíti později.
- **Žádnou skupinu zabezpečení sítě**: podsítě nesmí obsahovat skupinu zabezpečení sítě spojenou s ním.
- **Máte konkrétní směrovací tabulka**: podsíť musí mít uživatelem směrovací tabulky (UDR) 0.0.0.0/0 dalšího segmentu směrování Internetu jako pouze trasy přiřazenou. Další informace najdete v tématu [vytvoříte požadovaný směrovací tabulku a přidružíte ho](#create-the-required-route-table-and-associate-it)
3. **Volitelné vlastní DNS**: Pokud je vlastní DNS zadaná ve virtuální síti, Azure rekurzivní překladače IP adresu (například adresy 168.63.129.16) musí být přidaný do seznamu. Další informace najdete v tématu [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md).
4. **Žádný koncový bod služby**: podsítě nesmí mít koncový bod služby (Storage nebo Sql) k němu přidružené. Ujistěte se, že koncové body služeb možnost je zakázaná, při vytváření virtuální sítě.
5. **Dostatek IP adres**: podsíť musí mít minimálně 16 IP adres. Další informace najdete v tématu [určit velikost podsítě pro Managed instance](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Nebude moct nasadit nové Managed Instance, pokud není kompatibilní se všemi výše uvedených požadavků cílové podsíti. Cílový virtuální síť a podsíť musí uchovávat v souladu s požadavky na tyto spravované Instance (před a po nasazení), jako jakékoliv porušení může způsobit, že instance do chybného stavu a přestanou být dostupné. Obnovení ze stavu vyžaduje k vytvoření nové instance ve virtuální síti s předpisy sítě, vytvořte znovu dat na úrovni instance a obnovit své databáze. To přináší významné výpadky vašich aplikací.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Určit velikost podsítě pro Managed instance

Když vytvoříte Managed Instance, Platforma Azure přiřadí počet virtuálních počítačů v závislosti na velikosti vrstvy, který vyberete při zřizování. Protože tyto virtuální počítače jsou spojeny s vaší podsítě, vyžadují IP adresy. K zajištění vysoké dostupnosti během standardních operací a údržby služby, může Azure přidělte další virtuální počítače. V důsledku toho počet požadované IP adresy v podsíti je větší než počet spravovaných instancí v této podsíti. 

Návrh Managed Instance vyžaduje minimálně 16 IP adresy v podsíti a může použít až 256 IP adresy. V důsledku toho můžete použít masky podsítě velikosti/28 do /24 při definování rozsahy IP adres vaší podsítě. 

Pokud budete chtít nasadit víc instancí v podsíti spravované a třeba optimalizovat na velikost podsítě, použijte k výpočtu tyto parametry: 

- Azure používá 5 IP adres v podsíti pro vlastní potřeby 
- Každá instance pro obecné účely potřebuje dvě adresy 
- Každá instance pro důležité obchodní informace potřebuje čtyři adresy

**Příklad**: bude mít tři pro obecné účely a dvě obchodní kritické Managed instance. Že znamená, že potřebujete 5 + 3 * 2 + 2 * 4 = 19 IP adresy. Když rozsahy IP adres jsou definovány v Power BI 2, budete potřebovat IP rozsahu 32 (2 ^ 5) IP adresy. Proto musíte rezervovat podsíť s maskou podsítě/27. 

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>Vytvoření nové virtuální sítě pro Managed Instance pomocí modelu nasazení Azure Resource Manageru

Nejjednodušší způsob, jak vytvořit a konfigurovat virtuální sítě je použití šablony nasazení Azure Resource Manageru.

1. Přihlaste se k portálu Azure.

2. Použití **nasadit do Azure** tlačítko k nasazení virtuální sítě v cloudu Azure:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Toto tlačítko otevře formulář, který můžete použít ke konfiguraci síťového prostředí kde můžete nasadit Managed Instance.

  > [!Note]
  > Tuto šablonu Azure Resource Manageru nasadí virtuální síť se dvěma podsítěmi. Jedna podsíť s názvem **ManagedInstances** je vyhrazen pro Managed instance a má předem nakonfigurované směrovací tabulku, zatímco jiné podsíti nazývají **výchozí** slouží k jiným prostředkům, které by měl přístup ke spravované Instance (například virtuální počítače Azure). Můžete odebrat **výchozí** podsítě, pokud už nepotřebujete.

3. Konfigurace prostředí sítě. V následujícím formátu můžete nakonfigurovat parametry vaše síťové prostředí:

![Konfigurace sítě azure](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

Můžete změnit názvy virtuální sítě a podsítě a upravit rozsahy IP adres přidružené k síťové prostředky. Po stisknutí tlačítka "Koupit", bude tento formulář vytvoření a konfiguraci prostředí. Pokud už nebudete potřebovat dvě podsítě můžete odstranit výchozí hodnotu. 

## <a name="create-a-new-virtual-network-for-managed-instances-using-portal"></a>Vytvořit novou virtuální síť pro Managed instance pomocí portálu

Vytvoření služby Azure virtual network je předpokladem pro vytvoření Managed Instance. Na webu Azure portal, můžete použít [PowerShell](../virtual-network/quick-create-powershell.md), nebo [rozhraní příkazového řádku Azure](../virtual-network/quick-create-cli.md). Následující část popisuje kroky, pomocí webu Azure portal. Podrobnosti zde popsané použít u každého z těchto metod.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte a klikněte na **Virtuální síť**, ověřte, že jako režim nasazení je vybraný **Resource Manager**, a pak klikněte na **Vytvořit**.

   ![vytvoření virtuální sítě](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Vyplňte požadované informace způsobem, jako na následujícím snímku obrazovky ve formuláři virtuální sítě:

   ![formulář pro vytvoření virtuální sítě](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Klikněte na možnost **Vytvořit**.

   Adresní prostor a podsítě jsou uvedeny v zápisu CIDR. 

   > [!IMPORTANT]
   > Výchozí hodnoty vytvořit podsíť, která přebírá všechny adresní prostor virtuální sítě. Pokud zvolíte tuto možnost, nebude možné vytvořit všechny další prostředky ve virtuální síti, než je Managed Instance. 

   Doporučený postup by byl následující: 
   - Vypočítá velikost podsítě podle [určit velikost podsítě pro Managed Instance](#determine-the-size-of-subnet-for-managed-instances) oddílu  
   - Vyhodnocení požadavků pro ostatní virtuální sítě 
   - Odpovídajícím způsobem vyplňte rozsahy adres virtuální sítě a podsítě 

   Ujistěte se, že možnost koncových bodů služby zůstává **zakázané**. 

   ![formulář pro vytvoření virtuální sítě](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

### <a name="create-the-required-route-table-and-associate-it"></a>Vytvořit požadované směrovací tabulku a přidružíte ho

1. Přihlášení k webu Azure Portal  
2. Vyhledejte a klikněte na **Směrovací tabulka** a pak na stránce Směrovací tabulka klikněte na **Vytvořit**.

   ![formulář pro vytvoření směrovací tabulky](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Vytvořte trasu dalšího segmentu směrování Internetu 0.0.0.0/0 způsobem, jako jsou na následujících snímcích obrazovky:

   ![přidání do směrovací tabulky](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![trasa](./media/sql-database-managed-instance-tutorial/route.png)

4. Tato trasa přidružte k podsíti pro Managed Instance, způsobem, jako jsou na následujících snímcích obrazovky:

    ![podsíť](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![nastavení směrovací tabulky](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![nastavení směrovací tabulky – uložení](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


Po vytvoření virtuální sítě, budete chtít vytvořit Managed Instance.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Upravit existující virtuální sítě pro Managed instance 

Otázky a odpovědi v této části ukazují, jak přidat do existující virtuální síť Managed Instance. 

**Používáte pro existující virtuální síť modelu nasazení Classic nebo Resource Manager?** 

Managed Instance můžete vytvořit pouze ve virtuálním sítím Resource Manageru. 

**Chcete vytvořit novou podsíť pro spravované SQL instance nebo použijte existující?**

Pokud chcete vytvořit nový certifikát: 

- Vypočítá velikost podsítě podle pokynů v [určit velikost podsítě pro Managed instance](#determine-the-size-of-subnet-for-managed-instances) oddílu.
- Postupujte podle kroků v [přidání, změna nebo odstranění podsítě virtuální sítě](../virtual-network/virtual-network-manage-subnet.md). 
- Vytvoření směrovací tabulky, která obsahuje jednu položku **0.0.0.0/0**, jako další směrování Internetu a přidružte ji k podsíti pro Managed Instance.  

V případě, že chcete vytvořit Managed Instance uvnitř existující podsítě: 
- Zaškrtněte, že pokud je prázdný - podsíť Managed Instance nelze vytvořit v podsíti, která obsahuje další prostředky, včetně podsítě brány 
- Vypočítá velikost podsítě podle pokynů v [určit velikost podsítě pro Managed instance](#determine-the-size-of-subnet-for-managed-instances) a ověřte, že je odpovídající velikost. 
- Zkontrolujte, že nejsou povolené koncové body služby v podsíti.
- Ujistěte se, že neexistují žádné skupiny zabezpečení sítě spojenou s podsítí 

**Máte vlastní server DNS nakonfigurovaný?** 

Pokud ano, přečtěte si téma [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md). 

- Vytvořit požadované směrovací tabulku a přidružíte ho: viz [vytvoříte požadovaný směrovací tabulku a přidružíte ho](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md)
- Kurz ukazuje, jak vytvořit virtuální síť, vytvoříte Managed Instance a obnovit databázi ze zálohy databáze, najdete v tématu [vytvoření Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).
- Problémy s DNS, najdete v článku [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md)
