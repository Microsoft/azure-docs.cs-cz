---
title: Připojit virtuální virtuální virtuální byl virtuální msa klienta – spravovaná instance
description: Připojte se ke spravované instanci Azure SQL Database pomocí sql serveru Management Studio z virtuálního počítače Azure.
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
ms.openlocfilehash: 8b5dce0b43fac7cfd0e974f26451338ca1541f8f
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528422"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Úvodní příručka: Konfigurace virtuálního počítače Azure pro připojení k spravované instanci Azure SQL Database

Tento úvodní příručka ukazuje, jak nakonfigurovat virtuální počítač Azure pro připojení k spravované instanci Azure SQL Database Managed Instance pomocí SQL Server Management Studio (SSMS). Úvodní příručka o připojení z místního klientského počítače pomocí připojení bodu k síti najdete v [tématu Konfigurace připojení bodu k síti.](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>Požadavky

Tento rychlý start používá prostředky vytvořené v [vytvořit spravovanou instanci](sql-database-managed-instance-get-started.md) jako výchozí bod.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Vytvoření nové podsítě ve virtuální síti spravované instance

Následující kroky vytvoří novou podsíť ve virtuální síti spravované instance, aby se virtuální počítač Azure mohl připojit ke spravované instanci. Podsíť spravované instance je vyhrazena pro spravované instance. V této podsíti nelze vytvořit žádné jiné prostředky, jako jsou virtuální počítače Azure.

1. Otevřete skupinu prostředků pro spravovanou instanci, kterou jste vytvořili v rychlém startu [Vytvořit spravovanou instanci.](sql-database-managed-instance-get-started.md) Vyberte virtuální síť pro spravovanou instanci.

   ![Prostředky spravované instance](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Vyberte **Podsítě** a pak vyberte **+ Podsíť,** abyste vytvořili novou podsíť.

   ![Podsítě spravované instance](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Vyplňte formulář podle informací v této tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ---------------- | ----------------- | ----------- |
   | **Název** | Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   | **Rozsah adres (blok CIDR)** | Platný rozsah | Výchozí hodnota je vhodná pro tento rychlý start.|
   | **Skupina zabezpečení sítě** | Žádný | Výchozí hodnota je vhodná pro tento rychlý start.|
   | **Tabulka směrování** | Žádný | Výchozí hodnota je vhodná pro tento rychlý start.|
   | **Koncové body služby** | Vybráno: 0 | Výchozí hodnota je vhodná pro tento rychlý start.|
   | **Delegování podsítě** | Žádný | Výchozí hodnota je vhodná pro tento rychlý start.|

   ![Nová podsíť spravované instance pro klientský virtuální virtuální mísu](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Výběrem **možnosti OK** vytvořte tuto další podsíť ve virtuální síti spravované instance.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Vytvoření virtuálního počítače v nové podsíti ve virtuální síti

Následující kroky ukazují, jak vytvořit virtuální počítač v nové podsíti pro připojení ke spravované instanci.

## <a name="prepare-the-azure-virtual-machine"></a>Příprava virtuálního počítače Azure

Vzhledem k tomu, že spravovaná instance SQL je umístěna ve vaší privátní virtuální síti, je potřeba vytvořit virtuální počítač Azure s nainstalovaným nástrojem sql klienta, jako je SQL Server Management Studio nebo Azure Data Studio. Tento nástroj umožňuje připojit se ke spravované instanci a spouštět dotazy. V tomto rychlém startu se používá SQL Server Management Studio.

Nejjednodušší způsob, jak vytvořit klientský virtuální počítač se všemi potřebnými nástroji, je použít šablony Azure Resource Manageru.

1. Ujistěte se, že jste přihlášení k portálu Azure na jiné kartě prohlížeče. Potom vyberte následující tlačítko pro vytvoření klientského virtuálního počítače a instalaci sql server management studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Vyplňte formulář podle informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ---------------- | ----------------- | ----------- |
   | **Předplatné** | Platné předplatné | Musí se jednat o předplatné, ve kterém máte oprávnění k vytváření nových prostředků. |
   | **Skupina prostředků** |Skupina prostředků, kterou jste zadali v rychlém startu [Vytvořit spravovanou instanci.](sql-database-managed-instance-get-started.md)|Tato skupina prostředků musí být ten, ve kterém virtuální síť existuje.|
   | **Umístění** | Umístění pro skupinu prostředků | Tato hodnota je naplněna na základě vybrané skupiny prostředků. |
   | **Název virtuálního počítače**  | Libovolné platné jméno | Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   |**Uživatelské jméno správce**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). Nepoužívejte "serveradmin", protože se jedná o vyhrazenou roli na úrovni serveru.<br>Toto uživatelské jméno použijete při každém [připojení k virtuálnímu virtuálnímu virtuálnímu zařízení](#connect-to-virtual-machine).|
   |**Heslo**|Libovolné platné heslo|Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Toto heslo použijete při každém [připojení k virtuálnímu počítače](#connect-to-virtual-machine).|
   | **Velikost virtuálního počítače** | Libovolná platná velikost | Výchozí hodnota v této šabloně **Standard_B2s** je dostatečná pro tento rychlý start. |
   | **Umístění**|[resourceGroup(.location].| Tuto hodnotu neměňte. |
   | **Název virtuální sítě**|Virtuální síť, ve které jste vytvořili spravovanou instanci.|
   | **Název podsítě**|Název podsítě, kterou jste vytvořili v předchozím postupu| Nevybírejte podsíť, ve které jste vytvořili spravovanou instanci.|
   | **artefakty Umístění** | [deployment().properties.templateLink.uri] | Tuto hodnotu neměňte. |
   | **token umístění artefaktů Sas** | Ponechejte prázdné. | Tuto hodnotu neměňte. |

   ![vytvoření klientského virtuálního počítače](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Pokud jste při [vytváření spravované instance](sql-database-managed-instance-get-started.md)použili navrhovaný název virtuální sítě a výchozí podsíť , nemusíte měnit poslední dva parametry. V opačném případě byste měli tyto hodnoty změnit na hodnoty, které jste zadali při nastavovat síťové prostředí.

3. Zaškrtněte políčko **Souhlasím s výše uvedenými podmínkami.**
4. Vyberte **Nákup,** chcete-li nasadit virtuální počítač Azure ve vaší síti.
5. Chcete-li zobrazit stav nasazení, vyberte ikonu **Oznámení.**

> [!IMPORTANT]
> Nepokračujte až přibližně 15 minut po vytvoření virtuálního počítače poskytnout čas pro skripty po vytvoření nainstalovat SQL Server Management Studio.

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Následující kroky ukazují, jak se k nově vytvořenému virtuálnímu počítači připojit pomocí připojení ke vzdálené ploše.

1. Po dokončení nasazení přejděte k prostředku virtuálního počítače.

    ![Virtuální počítač](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Vyberte **Connect** (Připojit).

   Zobrazí se formulář souboru protokolu RDP (.rdp) s veřejnou IP adresou a číslem portu virtuálního počítače.

   ![Formulář RDP](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Vyberte **stáhnout soubor RDP**.

   > [!NOTE]
   > SSH můžete taky použít k připojení k virtuálnímu počítači.

4. Zavřete formulář **Připojit k virtuálnímu počítači.**
5. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP.
6. Po zobrazení výzvy vyberte **Připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) na Mac App Storu.

7. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a pak zvolte **OK**.

8. Při přihlášení se může zobrazit upozornění na certifikát. Chcete-li pokračovat v připojení, zvolte **Ano** nebo **Pokračovat.**

Jste připojeni k virtuálnímu počítači na řídicím panelu Správce serveru.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Připojení ke spravované instanci pomocí SSMS

1. Ve virtuálním počítači otevřete SQL Server Management Studio (SSMS).

   Trvá několik okamžiků otevřít, protože je třeba dokončit svou konfiguraci, protože je to poprvé, co byla zahájena SSMS.
2. V dialogovém okně **Připojit k serveru** zadejte do pole **Název serveru** úplný název **hostitele** spravované instance. Vyberte **sql server authentication**, zadejte své uživatelské jméno a heslo a pak vyberte **Připojit**.

    ![Připojení přes SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po připojení můžete zobrazit systémové a uživatelské databáze v uzlu Databáze a různé objekty v uzlech Zabezpečení, Objekty serveru, Replikace, Správa, Agent SQL Serveru a Profiler XEvent.

## <a name="next-steps"></a>Další kroky

- Úvodní příručka o připojení z místního klientského počítače pomocí připojení bodu k síti najdete v [tématu Konfigurace připojení bodu k síti](sql-database-managed-instance-configure-p2s.md).
- Přehled možností připojení pro aplikace najdete v článku o [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Chcete-li obnovit existující databázi SERVERU SQL Server z místní na spravovanou instanci, můžete použít [službu Migrace databáze Azure (DMS) pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) nebo [příkaz T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) k obnovení ze záložního souboru databáze.
