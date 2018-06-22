---
title: Databáze Azure SQL spravované Instance konfiguraci virtuální sítě | Microsoft Docs
description: Toto téma popisuje možnosti konfigurace pro virtuální síť (VNet) s Azure SQL Database spravované Instance.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: a51923738642b0e6a8ffd420b3cf433f7e869f59
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309329"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurace virtuální sítě pro spravované Instance databáze Azure SQL

SQL databáze spravované Instance Azure (preview) musí být nasazený v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md). Toto nasazení umožňuje následující scénáře: 
- Připojení k instanci spravované přímo z místní sítě 
- Připojení k odkazovaný server nebo jinou instanci a spravovat místní úložiště dat 
- Připojení do Instance spravované prostředky Azure  

## <a name="plan"></a>Plánování

Naplánujte, jak nasadit a spravovat Instance ve virtuální síti pomocí vaše odpovědi na tyto otázky: 
- Máte v úmyslu nasadit jeden nebo více instancí spravovat? 

  Počet instancí spravované Určuje minimální velikost podsítě přidělit pro vaše spravované instance. Další informace najdete v tématu [určit velikost podsítě pro spravované Instance](#create-a-new-virtual-network-for-managed-instances). 
- Potřebujete nasadit spravované Instance do existující virtuální síť nebo vytvoříte novou síť? 

   Pokud budete chtít použít existující virtuální síť, musíte ke změně této konfigurace sítě pro přizpůsobení Instance spravované. Další informace najdete v tématu [upravit existující virtuální síť pro spravované Instance](#modify-an-existing-virtual-network-for-managed-instances). 

   Pokud budete chtít vytvořit novou virtuální síť, přečtěte si téma [vytvořit novou virtuální síť pro spravované Instance](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Požadavky

Pro vytvoření Instance spravované nutné vyhradit podsíť uvnitř virtuální sítě, který splňuje následující požadavky:
- **Být prázdné**: podsíť nesmí obsahovat jiné cloudové služby přidružené k němu, a nesmí se podsíť brány. Nebudete moci vytvořit spravované Instance podsítě, která obsahuje prostředky než spravované instance, nebo přidejte další prostředky uvnitř podsíť později.
- **Žádné skupiny NSG**: podsíť nesmí mít skupinu zabezpečení sítě spojenou s ním.
- **Máte konkrétní směrovací tabulku**: podsíť musí mít uživatele trasy tabulky (UDR) s 0.0.0.0/0 Další směrování Internetu jako pouze trasy přiřazen. Další informace najdete v tématu [vytvořte požadované směrovací tabulka a přidružte ji](#create-the-required-route-table-and-associate-it)
3. **Volitelné vlastní DNS**: Pokud je zadán pro vlastní DNS virtuální sítě, musíte do seznamu přidat Azure rekurzivní překladače IP adresu (například 168.63.129.16). Další informace najdete v tématu [konfigurace DNS vlastní](sql-database-managed-instance-custom-dns.md).
4. **Žádný koncový bod služby**: podsíť nesmí mít koncového bodu služby (úložiště nebo Sql) přidružené k němu. Ujistěte se, že možnost koncové body služby není povolena při vytváření virtuální sítě.
5. **Dostatečná IP adresy**: podsíť musí mít minimálně 16 IP adres. Další informace najdete v tématu [určit velikost podsítě pro spravované instance](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Nebudete moct nasadit novou instanci spravované, pokud není kompatibilní se všemi požadavky na předchozí cílové podsíti. Cílový virtuální síť a podsíť musí uchovávat v souladu s požadavky na tyto spravované Instance (před a po nasazení), jak nedodržení může způsobit, že instance vadný stavu a k dispozici. Obnovení ze stavu vyžaduje vám umožní vytvořit novou instanci ve virtuální síti s předpisy sítě, vytvořte znovu dat na úrovni instance a obnovit své databáze. To představuje významné výpadky pro vaše aplikace.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Určit velikost podsítě pro spravované instance

Při vytváření Instance spravované Azure přiřadí počet virtuálních počítačů v závislosti na velikosti vrstvy, kterou vyberete při zřizování. Protože tyto virtuální počítače jsou spojeny s podsíť, vyžadují IP adresy. K zajištění vysoké dostupnosti během standardních operací a údržby služby, může Azure přidělit dalších virtuálních počítačů. V důsledku toho počet požadované IP adresy v podsíti, je větší než je počet instancí spravované v této podsíti. 

Standardně je spravované Instance vyžaduje minimálně 16 IP adres v podsíti a může využívat až 256 IP adresy. Masek podsítě velikosti/28 do /24 v důsledku toho můžete při definování vaší rozsahy IP podsíť. 

Pokud máte v úmyslu nasadit několik instancí spravované prostředí podsítě a k optimalizaci na velikost podsítě, použijte k výpočtu tyto parametry: 

- Azure používá pět IP adresy v podsíti pro vlastní požadavky 
- Každá instance obecné účely musí dvě adresy 

**Příklad**: Chcete mít osm spravované instance. Aby znamená potřebujete 5 + 8 * 2 = 21 IP adresy. Jako rozsahy IP adres jsou definovány v power 2, je třeba rozsah IP adres 32 (2 ^ 5) IP adresy. Proto musíte rezervovat podsíť s maskou podsítě/27. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Vytvořit novou virtuální síť pro spravované instance 

Vytvoření virtuální sítě Azure je předpokladem pro vytvoření Instance spravované. Portál Azure, můžete použít [prostředí PowerShell](../virtual-network/quick-create-powershell.md), nebo [rozhraní příkazového řádku Azure](../virtual-network/quick-create-cli.md). V následující části jsou uvedeny kroky, pomocí portálu Azure. Podrobnosti zmíněny v tomto článku se vztahují na každou z těchto metod.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte a klikněte na **Virtuální síť**, ověřte, že jako režim nasazení je vybraný **Resource Manager**, a pak klikněte na **Vytvořit**.

   ![vytvoření virtuální sítě](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Vyplňte formulář virtuální sítě se požadované informace, způsobem jako na následujícím snímku obrazovky:

   ![formulář pro vytvoření virtuální sítě](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Klikněte na možnost **Vytvořit**.

   Adresní prostor a podsítě jsou zadat v notaci CIDR. 

   > [!IMPORTANT]
   > Výchozí hodnoty vytvořte podsíť, která přebírá všechny adresní prostor virtuální sítě. Pokud zvolíte tuto možnost, nelze vytvořit žádné další prostředky ve virtuální síti než spravované Instance. 

   Doporučený postup bude následující: 
   - Vypočítat velikost podsítě podle [určit velikost podsítě pro spravované Instance](#determine-the-size-of-subnet-for-managed-instances) části  
   - Vyhodnocení potřeb pro zbytek virtuální sítě 
   - Zadejte virtuální síť a podsíť rozsahy adres odpovídajícím způsobem 

   Ujistěte se, že možnost koncové body služby zůstane **zakázané**. 

   ![formulář pro vytvoření virtuální sítě](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>Vytvořte požadované směrovací tabulka a přidružte ji

1. Přihlášení k webu Azure Portal  
2. Vyhledejte a klikněte na **Směrovací tabulka** a pak na stránce Směrovací tabulka klikněte na **Vytvořit**.

   ![formulář pro vytvoření směrovací tabulky](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Vytvořte další směrování Internet trasu 0.0.0.0/0 způsobem jako na následujících snímcích obrazovky:

   ![přidání do směrovací tabulky](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![trasa](./media/sql-database-managed-instance-tutorial/route.png)

4. Tato trasa přidružte podsítě pro spravované Instance způsobem jako na následujících snímcích obrazovky:

    ![podsíť](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![nastavení směrovací tabulky](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![nastavení směrovací tabulky – uložení](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


Po vytvoření virtuální sítě, budete chtít vytvořit vaší spravované Instance.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Úprava existující virtuální síť pro spravované instance 

Otázky a odpovědi v této části ukazují, jak přidat instanci spravované na existující virtuální síť. 

**Používáte pro existující virtuální síť modelu nasazení Classic nebo Resource Manager?** 

Do spravované Instance může vytvořit pouze ve virtuálních sítích Resource Manager. 

**Chcete vytvořit novou podsíť pro instanci SQL spravované nebo použijte existující?**

Pokud chcete vytvořit nový: 

- Vypočítat velikost podsítě podle pokynů v [určit velikost podsítě pro spravované instance](#determine-the-size-of-subnet-for-managed-instances) části.
- Postupujte podle kroků v [přidat, změnit nebo odstranit podsíť virtuální sítě](../virtual-network/virtual-network-manage-subnet.md). 
- Vytvořit směrovací tabulku, která obsahuje jednu položku **0.0.0.0/0**, jako další směrování Internet a přidružte ji k podsíti pro spravované Instance.  

V případě, že chcete vytvořit instanci spravované uvnitř existující podsítí: 
- Zkontrolujte, že pokud se podsíť prázdná - nelze vytvořit instanci a spravovat v podsíti, který obsahuje jiné prostředky, včetně podsítě brány 
- Vypočítat velikost podsítě podle pokynů v [určit velikost podsítě pro spravované instance](#determine-the-size-of-subnet-for-managed-instances) a ověřte, že je odpovídající velikost. 
- Zkontrolujte, že koncových bodů služby nejsou povoleny v podsíti.
- Ujistěte se, že neexistují žádné skupiny zabezpečení sítě spojenou s podsítí 

**Máte vlastní server DNS nakonfigurovaný?** 

Pokud ano, přečtěte si téma [konfiguraci DNS vlastní](sql-database-managed-instance-custom-dns.md). 

- Vytvořte požadované směrovací tabulka a přidružte ji: najdete v části [vytvořte požadované směrovací tabulka a přidružte ji](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je spravované Instance](sql-database-managed-instance.md)
- Zobrazuje postup vytvoření virtuální sítě, vytvořte instanci spravované a obnovit databázi ze zálohy databáze, na adrese [vytvoření Azure SQL Database spravované Instance](sql-database-managed-instance-create-tutorial-portal.md).
- Problémy s DNS, najdete v části [konfiguraci vlastní DNS](sql-database-managed-instance-custom-dns.md)
