---
title: Konfigurace připojení virtuálního počítače Azure
titleSuffix: Azure SQL Managed Instance
description: Připojte se ke spravované instanci Azure SQL pomocí SQL Server Management Studio z virtuálního počítače Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a1f496d59fa626dc8750493591128f7363afa40d
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91620252"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>Rychlý Start: konfigurace virtuálního počítače Azure pro připojení ke spravované instanci Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto rychlém startu se dozvíte, jak nakonfigurovat virtuální počítač Azure pro připojení ke spravované instanci Azure SQL pomocí SQL Server Management Studio (SSMS). 


Pro rychlý Start, který ukazuje, jak se připojit z místního klientského počítače pomocí připojení typu Point-to-site, najdete informace v tématu [Konfigurace připojení typu Point-to-site](point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start používá prostředky vytvořené v části [Vytvoření spravované instance](instance-create-quickstart.md) jako počáteční bod.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Vytvořit novou virtuální síť podsítě

Následující kroky vytvoří novou podsíť ve virtuální síti spravované instance SQL, aby se virtuální počítač Azure mohl připojit ke spravované instanci. Podsíť spravované instance SQL je vyhrazená pro spravované instance. V této podsíti nemůžete vytvořit žádné další prostředky, jako jsou virtuální počítače Azure.

1. Otevřete skupinu prostředků pro spravovanou instanci, kterou jste vytvořili v rychlém startu [Vytvoření spravované instance](instance-create-quickstart.md) . Vyberte virtuální síť pro spravovanou instanci.

   ![Prostředky spravované instance SQL](./media/connect-vm-instance-configure/resources.png)

2. Vyberte **podsítě** a pak vyberte **+ podsíť** pro vytvoření nové podsítě.

   ![Podsítě spravované instance SQL](./media/connect-vm-instance-configure/subnets.png)

3. Vyplňte formulář pomocí informací v této tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ---------------- | ----------------- | ----------- |
   | **Název** | Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   | **Rozsah adres (blok CIDR)** | Platný rozsah | Výchozí hodnota je pro tento rychlý Start vhodná.|
   | **Skupina zabezpečení sítě** | Žádné | Výchozí hodnota je pro tento rychlý Start vhodná.|
   | **Směrovací tabulka** | Žádné | Výchozí hodnota je pro tento rychlý Start vhodná.|
   | **Koncové body služby** | Vybráno: 0 | Výchozí hodnota je pro tento rychlý Start vhodná.|
   | **Delegování podsítě** | Žádné | Výchozí hodnota je pro tento rychlý Start vhodná.|

   ![Nová podsíť spravované instance SQL pro virtuální počítač klienta](./media/connect-vm-instance-configure/new-subnet.png)

4. Pokud chcete vytvořit tuto další podsíť ve virtuální síti spravované instance SQL, vyberte **OK** .

## <a name="create-a-vm-in-the-new-subnet"></a>Vytvoření virtuálního počítače v nové podsíti 

Následující kroky ukazují, jak vytvořit virtuální počítač v nové podsíti pro připojení ke spravované instanci SQL.

## <a name="prepare-the-azure-virtual-machine"></a>Příprava virtuálního počítače Azure

Vzhledem k tomu, že je spravovaná instance SQL umístěná v privátní virtuální síti, je potřeba vytvořit virtuální počítač Azure s nainstalovaným klientským nástrojem SQL, například SQL Server Management Studio nebo Azure Data Studio. Tento nástroj vám umožňuje připojit se ke spravované instanci SQL a provádět dotazy. V tomto rychlém startu se používá SQL Server Management Studio.

Nejjednodušší způsob, jak vytvořit klientský virtuální počítač se všemi potřebnými nástroji, je použití šablon Azure Resource Manager.

1. Ujistěte se, že jste přihlášeni k Azure Portal na jiné kartě prohlížeče. Pak vyberte následující tlačítko a vytvořte klientský virtuální počítač a nainstalujte SQL Server Management Studio:

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. Vyplňte formulář pomocí informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ---------------- | ----------------- | ----------- |
   | **Předplatné** | Platné předplatné | Musí se jednat o předplatné, ve kterém máte oprávnění k vytváření nových prostředků. |
   | **Skupina prostředků** |Skupina prostředků, kterou jste zadali v rychlém startu [Vytvoření spravované instance SQL](instance-create-quickstart.md)|Tato skupina prostředků musí být ta, ve které virtuální síť existuje.|
   | **Umístění** | Umístění pro skupinu prostředků | Tato hodnota se naplní na základě vybrané skupiny prostředků. |
   | **Název virtuálního počítače**  | Libovolné platné jméno | Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   |**Uživatelské jméno správce**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). Nepoužívejte "serveradmin", protože to je vyhrazená role na úrovni serveru.<br>Toto uživatelské jméno použijete při každém [připojení k virtuálnímu počítači](#connect-to-the-virtual-machine).|
   |**Heslo**|Libovolné platné heslo|Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Toto heslo použijete při každém [připojení k virtuálnímu počítači](#connect-to-the-virtual-machine).|
   | **Velikost virtuálního počítače** | Libovolná platná velikost | Výchozí hodnota v této šabloně **Standard_B2s** je pro tento rychlý Start dostačující. |
   | **Umístění**|[resourceName (). Location].| Tuto hodnotu neměňte. |
   | **Název Virtual Network**|Virtuální síť, ve které jste vytvořili spravovanou instanci|
   | **Název podsítě**|Název podsítě, kterou jste vytvořili v předchozím postupu| Nevybírejte podsíť, ve které jste vytvořili spravovanou instanci.|
   | **Umístění artefaktů** | [Deployment (). Properties. templateLink. URI] | Tuto hodnotu neměňte. |
   | **token SAS umístění artefaktů** | Ponechte prázdné. | Tuto hodnotu neměňte. |

   ![vytvoření klientského virtuálního počítače](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Pokud jste použili navrhovaný název virtuální sítě a výchozí podsíť při [vytváření spravované instance SQL](instance-create-quickstart.md), nemusíte měnit poslední dva parametry. V opačném případě byste tyto hodnoty měli změnit na hodnoty, které jste zadali při nastavování síťového prostředí.

3. Zaškrtněte políčko Souhlasím **s podmínkami a ujednáními uvedenými nahoře** .
4. Vyberte **koupit** a NASAĎTE virtuální počítač Azure ve vaší síti.
5. Výběrem ikony **oznámení** zobrazíte stav nasazení.

> [!IMPORTANT]
> Nepokračujte, dokud přibližně 15 minut po vytvoření virtuálního počítače zadáte čas pro instalaci SQL Server Management Studio skriptů po vytvoření.

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Následující kroky ukazují, jak se připojit k nově vytvořenému virtuálnímu počítači pomocí připojení ke vzdálené ploše.

1. Po dokončení nasazení přejděte k prostředku virtuálního počítače.

    ![Snímek obrazovky se zobrazí Azure Portal se stránkou přehled pro vybraný virtuální počítač a zvýrazněným připojením.](./media/connect-vm-instance-configure/vm.png)  

2. Vyberte **Připojit**.

   Zobrazí se formulář soubor protokol RDP (Remote Desktop Protocol) (soubor. RDP) s veřejnou IP adresou a číslem portu pro virtuální počítač.

   ![Formulář RDP](./media/connect-vm-instance-configure/rdp.png)  

3. Vyberte **Stáhnout soubor RDP**.

   > [!NOTE]
   > K připojení k VIRTUÁLNÍmu počítači můžete také použít SSH.

4. Zavřete formulář **připojit k virtuálnímu počítači** .
5. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP.
6. Po zobrazení výzvy vyberte **připojit**. Na Macu budete potřebovat klienta protokolu RDP, například [tohoto klienta vzdálené plochy](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) z Mac App Storu.

7. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a pak zvolte **OK**.

8. Při přihlášení se může zobrazit upozornění na certifikát. Vyberte **Ano** nebo **pokračovat** a pokračujte v připojování.

K virtuálnímu počítači jste připojeni na řídicím panelu Správce serveru.

## <a name="connect-to-sql-managed-instance"></a>Připojení ke spravované instanci SQL 

1. Na virtuálním počítači otevřete SQL Server Management Studio.

   Otevření může chvíli trvat, protože je potřeba dokončit jeho konfiguraci, protože to je první spuštění SSMS.
2. V dialogovém okně **připojit k serveru** zadejte do pole **název serveru** plně kvalifikovaný **název hostitele** vaší spravované instance. Vyberte **SQL Server ověřování**, zadejte svoje uživatelské jméno a heslo a pak vyberte **připojit**.

    ![SSMS připojit](./media/connect-vm-instance-configure/ssms-connect.png)  

Po připojení můžete zobrazit systémové a uživatelské databáze v uzlu Databáze a různé objekty v uzlech Zabezpečení, Objekty serveru, Replikace, Správa, Agent SQL Serveru a Profiler XEvent.

## <a name="next-steps"></a>Další kroky

- Rychlý Start ukazující, jak se připojit z místního klientského počítače pomocí připojení typu Point-to-site, najdete v tématu [Konfigurace připojení typu Point-to-site](point-to-site-p2s-configure.md).
- Přehled možností připojení pro aplikace najdete v tématu [připojení aplikací k spravované instanci SQL](connect-application-instance.md).
- Chcete-li obnovit stávající databázi SQL Server z místního prostředí do spravované instance, můžete použít [Azure Database Migration Service pro migraci](../../dms/tutorial-sql-server-to-managed-instance.md) nebo [příkaz T-SQL Restore](restore-sample-database-quickstart.md) pro obnovení ze záložního souboru databáze.
