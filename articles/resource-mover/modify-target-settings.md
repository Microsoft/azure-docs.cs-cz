---
title: Úprava nastavení cíle při přesunu virtuálních počítačů Azure mezi oblastmi pomocí Azure Resource stěhovací
description: Naučte se upravovat nastavení cíle při přesunu virtuálních počítačů Azure mezi oblastmi pomocí Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: eb28e4c8f6b465e2a9b38cc4571bc4a00baf4ef7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979619"
---
# <a name="modify-target-settings"></a>Úprava nastavení cíle

Tento článek popisuje, jak upravit nastavení cíle při přesouvání prostředků mezi oblastmi Azure pomocí nástroje [Azure Resource stěhovací](overview.md).


## <a name="modify-vm-settings"></a>Úprava nastavení virtuálního počítače

Při přesunu virtuálních počítačů Azure a přidružených prostředků můžete upravit nastavení cíle. 

- Doporučujeme změnit pouze nastavení cíle po ověření kolekce Move.
- Doporučujeme, abyste před přípravou prostředků změnili nastavení, protože některé vlastnosti cíle můžou být po dokončení přípravy nedostupné pro úpravy.

Naopak
- Pokud přesouváte zdrojový prostředek, můžete obvykle změnit cílové nastavení, dokud nespustíte proces zahájení přesunutí.
- Pokud přiřadíte existující prostředek ve zdrojové oblasti, můžete změnit nastavení cíle, dokud se potvrzení přesunu nedokončí.

### <a name="settings-you-can-modify"></a>Nastavení, která lze upravit

Nastavení konfigurace, která lze upravit, jsou shrnuta v tabulce.

**Prostředek** | **Možnosti** 
--- | --- | --- 
**název virtuálního počítače** | Možnosti:<br/><br/> – Vytvoří nový virtuální počítač se stejným názvem v cílové oblasti.<br/><br/> – Vytvoří nový virtuální počítač s jiným názvem v cílové oblasti.<br/><br/> – Použijte existující virtuální počítač v cílové oblasti.<br/><br/> Pokud vytvoříte nový virtuální počítač s výjimkou nastavení, které upravíte, bude novému cílovému virtuálnímu počítači přiřazeno stejné nastavení jako zdroj.
**Zóna dostupnosti virtuálního počítače** | Zóna dostupnosti, do které bude cílový virtuální počítač umístěn. Pokud nechcete změnit nastavení zdroje, nebo pokud nechcete virtuální počítač umístit do zóny dostupnosti, vyberte možnost **Nepoužito** .
**Skladová položka virtuálního počítače** | [Typ virtuálního počítače](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (dostupný v cílové oblasti), který se použije pro cílový virtuální počítač.<br/><br/> Vybraný cílový virtuální počítač by neměl být menší než zdrojový virtuální počítač.
* * Skupina dostupnosti virtuálních počítačů | Skupina dostupnosti, do které bude cílový virtuální počítač umístěn. Vyberte **nepoužitelné**  , nechcete změnit nastavení zdroje, nebo pokud virtuální počítač nechcete umístit do skupiny dostupnosti.
**Trezor klíčů virtuálních počítačů** | Přidružený Trezor klíčů, když povolíte Azure Disk Encryption na virtuálním počítači.
**Sada šifrování disku** | Přidružená šifra disku nastavená, pokud virtuální počítač používá klíč spravovaný zákazníkem pro šifrování na straně serveru.
**Skupina prostředků** | Skupina prostředků, do které bude cílový virtuální počítač umístěn.
**Síťové prostředky** | Možnosti pro síťová rozhraní, virtuální sítě (virtuální sítě/) a skupiny zabezpečení sítě/síťová rozhraní:<br/><br/> – Vytvoří nový prostředek se stejným názvem v cílové oblasti.<br/><br/> – Vytvoří nový prostředek s jiným názvem v cílové oblasti.<br/><br/> – Použijte existující síťový prostředek v cílové oblasti.<br/><br/> Pokud vytvoříte nový cílový prostředek, s výjimkou nastavení, které upravíte, je mu přiřazeno stejné nastavení jako zdrojový prostředek.
**Název veřejné IP adresy, SKU a zóna** | Určuje název, [skladovou](../virtual-network/public-ip-addresses.md#sku)položku a [zónu](../virtual-network/public-ip-addresses.md#standard) pro standardní veřejné IP adresy.<br/><br/> Pokud chcete, aby byla zóna redundantní, zadejte jako **zónu redundantní**.
* * Název nástroje pro vyrovnávání zatížení, SKU a zóna * * | Určuje název, SKLADOVOU položku (Basic nebo Standard) a zónu pro nástroj pro vyrovnávání zatížení.<br/><br/> Doporučujeme použít standardní sKU.<br/><br/> Pokud chcete, aby byla zóna redundantní, zadejte jako **zónu redundantní**.
**Závislosti prostředků** | Možnosti pro každou závislost:<br/><br/>– Prostředek používá zdrojové závislé prostředky, které se přesunou do cílové oblasti.<br/><br/> – Prostředek používá jiné závislé prostředky, které se nacházejí v cílové oblasti. V takovém případě si můžete vybrat z libovolných podobných prostředků v cílové oblasti.

### <a name="edit-vm-target-settings"></a>Upravit nastavení cíle virtuálního počítače

Pokud nechcete, aby byly závislé prostředky ze zdrojové oblasti k cíli, máte několik dalších možností:

- Vytvořte nový prostředek v cílové oblasti. Pokud nezadáte jiná nastavení, bude mít nový prostředek stejné nastavení jako zdrojový prostředek.
- Použijte existující prostředek v cílové oblasti.

Přesné chování závisí na typu prostředku. [Přečtěte si další informace](modify-target-settings.md) o změnách nastavení cíle.

Nastavení cíle pro prostředek upravíte pomocí položky **cílová konfigurace** v kolekci přesunutí prostředků. 

Postup úpravy nastavení: 

1. Na stránce **různých oblastí** > **cílový sloupec konfigurace** klikněte na odkaz pro položku prostředku.
2. V **nastavení konfigurace** můžete vytvořit nový virtuální počítač v cílové oblasti.
3. Přiřaďte k cílovému virtuálnímu počítači novou zónu dostupnosti, skupinu dostupnosti nebo SKU. **Zóna dostupnosti** a **SKU**.

Změny se provedou jenom pro prostředky, které upravujete. Všechny závislé prostředky je potřeba aktualizovat samostatně.


## <a name="modify-sql-settings"></a>Upravit nastavení SQL

Při přesunu Azure SQL Database prostředků můžete změnit nastavení cíle pro přesunutí. 

- Pro SQL Database:
    - Doporučujeme, abyste před přípravou pro přesunutí změnili nastavení konfigurace cíle.
    - Můžete upravit nastavení pro cílovou databázi a redundanci zóny pro databázi.
- Pro elastické fondy:
    -  Cílovou konfiguraci můžete změnit kdykoli před zahájením přesunutí.
    - Můžete upravit cílový elastický fond a redundanci zóny pro fond. 

### <a name="sql-settings-you-can-modify"></a>Nastavení SQL, která můžete upravit

**Nastavení** | **Databáze SQL** | **Elastický fond**
--- | --- | ---
**Název** | Vytvoří novou databázi se stejným názvem v cílové oblasti.<br/><br/> Vytvořte novou databázi s jiným názvem v cílové oblasti.<br/><br/> Použijte existující databázi v cílové oblasti. | Vytvoří nový elastický fond se stejným názvem v cílové oblasti.<br/><br/> Vytvořte nový elastický fond s jiným názvem v cílové oblasti.<br/><br/> Použijte existující elastický fond v cílové oblasti.
**Zónová redundance** | Pokud chcete přejít z oblasti, která podporuje redundanci zóny, do oblasti, která není, zadejte do nastavení zóny možnost **Zakázat** .<br/><br/> Pokud chcete přejít z oblasti, která nepodporuje redundanci zóny, do konkrétní oblasti, zadejte do nastavení zóny **možnost Povolit** . | Pokud chcete přejít z oblasti, která podporuje redundanci zóny, do oblasti, která není, zadejte do nastavení zóny možnost **Zakázat** .<br/><br/> Pokud chcete přejít z oblasti, která nepodporuje redundanci zóny, do konkrétní oblasti, zadejte do nastavení zóny **možnost Povolit** .

### <a name="edit-sql-target-settings"></a>Upravit nastavení cíle SQL

Nastavení cíle Azure SQL Database prostředku upravíte následujícím způsobem: 

1. V **různých oblastech** u prostředku, který chcete upravit, klikněte na položku **cílová konfigurace** .
2. V **nastavení konfigurace** zadejte cílové nastavení shrnuté v tabulce výše.

## <a name="next-steps"></a>Další kroky

[Přesuňte virtuální počítač Azure](tutorial-move-region-virtual-machines.md) do jiné oblasti.