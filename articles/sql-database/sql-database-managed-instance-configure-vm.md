---
title: Připojit klientský virtuální počítač – spravovaná instance
description: Připojte se k Azure SQL Database spravované instanci pomocí SQL Server Management Studio z virtuálního počítače Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a455607b1459ebc9e37b1df70b454feea76d1f2f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822781"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Rychlý Start: konfigurace virtuálního počítače Azure pro připojení k spravované instanci Azure SQL Database

V tomto rychlém startu se dozvíte, jak nakonfigurovat virtuální počítač Azure pro připojení k Azure SQL Database spravované instanci pomocí SQL Server Management Studio (SSMS). Rychlý Start ukazující, jak se připojit z místního klientského počítače pomocí připojení typu Point-to-site, najdete v tématu [Konfigurace připojení typu Point-to-site](sql-database-managed-instance-configure-p2s.md) .

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start používá prostředky vytvořené v části [Vytvoření spravované instance](sql-database-managed-instance-get-started.md) jako počáteční bod.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Vytvoří novou podsíť ve virtuální síti spravované instance.

Následující kroky vytvoří novou podsíť ve virtuální síti spravované instance, aby se virtuální počítač Azure mohl připojit ke spravované instanci. Podsíť spravované instance je vyhrazená pro spravované instance. V této podsíti nemůžete vytvořit žádné další prostředky, jako jsou virtuální počítače Azure.

1. Otevřete skupinu prostředků pro spravovanou instanci, kterou jste vytvořili v rychlém startu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md) . Vyberte virtuální síť pro spravovanou instanci.

   ![Prostředky spravované instance](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Vyberte **podsítě** a pak vyberte **+ podsíť** pro vytvoření nové podsítě.

   ![Podsítě spravované instance](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Vyplňte formulář pomocí informací v této tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ---------------- | ----------------- | ----------- |
   | **Název** | Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).|
   | **Rozsah adres (blok CIDR)** | Platný rozsah | Výchozí hodnota je pro tento rychlý Start vhodná.|
   | **Skupina zabezpečení sítě** | Žádný | Výchozí hodnota je pro tento rychlý Start vhodná.|
   | **Směrovací tabulka** | Žádný | Výchozí hodnota je pro tento rychlý Start vhodná.|
   | **Koncové body služby** | 0 vybráno | Výchozí hodnota je pro tento rychlý Start vhodná.|
   | **Delegování podsítě** | Žádný | Výchozí hodnota je pro tento rychlý Start vhodná.|

   ![Nová podsíť spravované instance pro virtuální počítač klienta](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Pokud chcete vytvořit tuto další podsíť ve virtuální síti spravované instance, vyberte **OK** .

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Vytvoření virtuálního počítače v nové podsíti ve virtuální síti

Následující kroky ukazují, jak vytvořit virtuální počítač v nové podsíti pro připojení ke spravované instanci.

## <a name="prepare-the-azure-virtual-machine"></a>Příprava virtuálního počítače Azure

Vzhledem k tomu, že je spravovaná instance SQL umístěná v privátních Virtual Network, je potřeba vytvořit virtuální počítač Azure s nainstalovaným klientským nástrojem SQL, jako je SQL Server Management Studio nebo Azure Data Studio. Tento nástroj vám umožňuje připojit se ke spravované instanci a provádět dotazy. V tomto rychlém startu se používá SQL Server Management Studio.

Nejjednodušší způsob, jak vytvořit klientský virtuální počítač se všemi potřebnými nástroji, je použití šablon Azure Resource Manager.

1. Ujistěte se, že jste přihlášeni k Azure Portal na jiné kartě prohlížeče. Pak vyberte následující tlačítko a vytvořte klientský virtuální počítač a nainstalujte SQL Server Management Studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Vyplňte formulář pomocí informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ---------------- | ----------------- | ----------- |
   | **Předplatné** | Platné předplatné | Musí se jednat o předplatné, ve kterém máte oprávnění k vytváření nových prostředků. |
   | **Skupina prostředků** |Skupina prostředků, kterou jste zadali v rychlém startu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md) .|Tato skupina prostředků musí být ta, ve které virtuální síť existuje.|
   | **Umístění** | Umístění pro skupinu prostředků | Tato hodnota se naplní na základě vybrané skupiny prostředků. |
   | **Název virtuálního počítače**  | Libovolné platné jméno | Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).|
   |**Uživatelské jméno správce**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging). Nepoužívejte "serveradmin", protože to je vyhrazená role na úrovni serveru.<br>Toto uživatelské jméno použijete při každém [připojení k virtuálnímu počítači](#connect-to-virtual-machine).|
   |**Heslo**|Libovolné platné heslo|Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Toto heslo použijete při každém [připojení k virtuálnímu počítači](#connect-to-virtual-machine).|
   | **Velikost virtuálního počítače** | Libovolná platná velikost | Výchozí hodnota v této šabloně **Standard_B2s** je pro tento rychlý Start dostačující. |
   | **Umístění**|[resourceName (). Location].| Tuto hodnotu neměňte. |
   | **Název Virtual Network**|Virtuální síť, ve které jste vytvořili spravovanou instanci.|
   | **Název podsítě**|Název podsítě, kterou jste vytvořili v předchozím postupu| Nevybírejte podsíť, ve které jste vytvořili spravovanou instanci.|
   | **Umístění artefaktů** | [Deployment (). Properties. templateLink. URI] | Tuto hodnotu neměňte. |
   | **token SAS umístění artefaktů** | ponechat prázdné | Tuto hodnotu neměňte. |

   ![vytvoření klientského virtuálního počítače](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Pokud jste použili navrhovaný název virtuální sítě a výchozí podsíť při [vytváření spravované instance](sql-database-managed-instance-get-started.md), nemusíte měnit poslední dva parametry. V opačném případě byste tyto hodnoty měli změnit na hodnoty, které jste zadali při nastavování síťového prostředí.

3. Zaškrtněte políčko Souhlasím **s podmínkami a ujednáními uvedenými nahoře** .
4. Vyberte **koupit** a NASAĎTE virtuální počítač Azure ve vaší síti.
5. Výběrem ikony **oznámení** zobrazíte stav nasazení.

> [!IMPORTANT]
> Nepokračujte, dokud přibližně 15 minut po vytvoření virtuálního počítače zadáte čas pro instalaci SQL Server Management Studio skriptů po vytvoření.

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Následující kroky ukazují, jak se k nově vytvořenému virtuálnímu počítači připojit pomocí připojení ke vzdálené ploše.

1. Po dokončení nasazení přejděte k prostředku virtuálního počítače.

    ![Virtuální počítač](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Vyberte **Connect** (Připojit).

   Zobrazí se formulář soubor protokol RDP (Remote Desktop Protocol) (soubor. RDP) s veřejnou IP adresou a číslem portu pro virtuální počítač.

   ![Formulář RDP](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Vyberte **Stáhnout soubor RDP**.

   > [!NOTE]
   > K připojení k VIRTUÁLNÍmu počítači můžete také použít SSH.

4. Zavřete formulář **připojit k virtuálnímu počítači** .
5. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP.
6. Po zobrazení výzvy vyberte **připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) na Mac App Storu.

7. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a pak zvolte **OK**.

8. Při přihlášení se může zobrazit upozornění na certifikát. Vyberte **Ano** nebo **pokračovat** a pokračujte v připojování.

K virtuálnímu počítači jste připojeni na řídicím panelu Správce serveru.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Připojení ke spravované instanci pomocí SSMS

1. Na virtuálním počítači otevřete SQL Server Management Studio (SSMS).

   Otevření se může chvíli trvat, protože je potřeba k dokončení konfigurace, protože se jedná o první spuštění SSMS.
2. V dialogovém okně **připojit k serveru** zadejte do pole **název serveru** plně kvalifikovaný **název hostitele** vaší spravované instance. Vyberte **SQL Server ověřování**, zadejte svoje uživatelské jméno a heslo a pak vyberte **připojit**.

    ![Připojení přes SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po připojení můžete zobrazit systémové a uživatelské databáze v uzlu Databáze a různé objekty v uzlech Zabezpečení, Objekty serveru, Replikace, Správa, Agent SQL Serveru a Profiler XEvent.

## <a name="next-steps"></a>Další kroky

- Rychlý Start ukazující, jak se připojit z místního klientského počítače pomocí připojení typu Point-to-site, najdete v tématu [Konfigurace připojení typu Point-to-site](sql-database-managed-instance-configure-p2s.md).
- Přehled možností připojení aplikací najdete v článku o [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Pokud chcete obnovit stávající databázi SQL Server z místního prostředí do spravované instance, můžete k obnovení ze záložního souboru databáze použít [Azure Database Migration Service (DMS) pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) nebo [příkaz T-SQL Restore](sql-database-managed-instance-get-started-restore.md) .
