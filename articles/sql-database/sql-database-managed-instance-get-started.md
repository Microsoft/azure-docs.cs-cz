---
title: 'Azure Portal: Vytvoření spravované instance SQL | Microsoft Docs'
description: Vytvoříte spravovanou instanci SQL, síťové prostředí a klientský virtuální počítač pro přístup.
keywords: sql database tutorial, create a sql managed instance
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42022667"
---
# <a name="create-an-azure-sql-managed-instance"></a>Vytvoření spravované instance Azure SQL

Tento rychlý start vás provede vytvořením spravované instance SQL v Azure. Spravovaná instance Azure SQL Database je instance databázového stroje SQL Serveru v podobě platformy jako služby (PaaS), která umožňuje spouštět a škálovat vysoce dostupné databáze SQL Serveru v cloudu Azure. Tento rychlý start ukazuje, jak začít tím, že vytvoříte spravovanou instanci SQL.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="prepare-network-environment"></a>Příprava síťového prostředí

Spravovaná instance SQL je zabezpečená služba umístěná ve vaší vlastní virtuální síti Azure. Abyste mohli vytvořit spravovanou instanci, je potřeba připravit síťové prostředí Azure, což zahrnuje:
 - Virtuální síť Azure, kde bude spravovaná instance umístěná.
 - Podsíť ve virtuální síti Azure, kde budou umístěné spravované instance.
 - Trasu definovanou uživatelem, která spravované instanci umožní komunikovat se službami Azure, které instanci řídí a spravují.

Podsíť je vyhrazená pro spravované instance a v této podsíti není možné vytvářet žádné jiné prostředky (například virtuální počítače Azure). V tomto rychlém startu se ve vaší virtuální síti Azure vytvoří dvě podsítě, abyste mohli umístit spravované instance do podsítě vyhrazené pro spravované instance a ostatní prostředky do výchozí podsítě.

1. Kliknutím na následující tlačítko nasadíte síťové prostředí Azure připravené pro spravovanou instanci Azure SQL Database:

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    Tímto tlačítkem se otevře formulář na webu Azure Portal, kde můžete před nasazením nakonfigurovat své síťové prostředí.

2. Volitelně změňte názvy virtuální sítě a podsítí a upravte rozsahy IP adres přidružené k vašim síťovým prostředkům. Pak stisknutím tlačítka Koupit vytvořte a nakonfigurujte své prostředí:

    ![vytvoření prostředí spravované instance](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > Toto nasazení Azure Resource Manageru vytvoří ve virtuální síti dvě podsítě – jednu pro spravované instance s názvem **ManagedInstances** a druhou s názvem **Default** pro ostatní prostředky, jako jsou klientské virtuální počítače, které je možné použít pro připojení ke spravované instanci. Pokud nepotřebujete dvě podsítě, můžete výchozí podsíť odstranit později. V takovém případě však nebudete moct dokončit krok 3 v této příručce rychlý start – [příprava klientského počítače](#prepare-client-machine).

    > [!Note]
    > Pokud změníte názvy virtuální sítě a podsítí, ujistěte se, že si pamatujete nové názvy, protože je budete potřebovat v následujících částech. Ve zbývající části tohoto kurzu se předpokládá, že jste vytvořili virtuální síť **MyNewVNet**, podsíť **ManagedInstances** pro spravované instance SQL a podsíť **Default** pro virtuální počítače a ostatní prostředky.

## <a name="create-a-managed-instance"></a>Vytvoření Managed Instance

Následující kroky ukazují, jak po schválení pro verzi Preview vytvořit Managed Instance.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte **Managed Instance** a pak vyberte **Azure SQL Database Managed Instance (Preview)**.
3. Klikněte na možnost **Vytvořit**.

   ![Vytvoření spravované instance](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. Vyberte své předplatné a ověřte, že se u podmínek verze Perview zobrazuje **Přijato**.

   ![přijaté podmínky verze Preview pro Managed Instance](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. Ve formuláři Managed Instance vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název spravované instance**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Přihlašovací jméno správce spravované instance**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nepoužívejte „serveradmin“, protože tento název je vyhrazená role na úrovni serveru.| 
   |**Heslo**|Libovolné platné heslo|Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Skupina prostředků**|Skupina prostředků, kterou jste vytvořili dříve||
   |**Umístění**|Umístění, které jste vybrali dříve|Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/).|
   |**Virtuální síť**|Virtuální síť, kterou jste vytvořili dříve| Pokud jste v předchozím kroku nezměnili názvy, zvolte položku **MyNewVNet/ManagedInstances**. Jinak zvolte název virtuální sítě a podsítě spravované instance, které jste zadali v předchozí části. **Nepoužívejte výchozí podsíť, protože není nakonfigurovaná pro hostování spravovaných instancí**. |

   ![formulář pro vytvoření Managed Instance](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. Klikněte na **Cenová úroveň** a určete velikost výpočetních prostředků a prostředků úložiště a prohlédněte si možnosti cenové úrovně. Ve výchozím nastavení vaše instance získá bezplatně 32 GB prostoru úložiště, **což pro vaše aplikace nemusí být dostatečné**.
7. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader. 
   ![spravovaná instance cenové úrovně](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. Jakmile budete hotovi, kliknutím na **Použít** uložte provedený výběr.  
9. Kliknutím na **Vytvořit** nasaďte Managed Instance.
10. Kliknutím na ikonu **Oznámení** zobrazte stav nasazení.
 
   ![průběh nasazení](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. Kliknutím na **Probíhá nasazení** otevřete okno Managed Instance, kde můžete dále monitorovat průběh nasazení.
 
   ![průběh nasazení 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Jakmile dojde k nasazení, pokračujte k dalšímu postupu.

> [!IMPORTANT]
> Čas nasazení první instance v podsíti je obvykle mnohem delší než v případě dalších instancí – dokončení někdy může trvat déle než 24 hodin. Nerušte operaci nasazování jenom proto, že trvá déle, než jste očekávali. Dlouhá doba potřebná k nasazení první instance je pouze dočasná. Očekávejte výrazné zkrácení doby nasazení krátce po zahájení verze Public Preview. Vytvoření druhé spravované instance v podsíti bude trvat několik minut.

## <a name="prepare-client-machine"></a>Příprava klientského počítače

Vzhledem k tomu, že je spravovaná instance SQL umístěná ve vaší privátní virtuální síti, je potřeba vytvořit virtuální počítač Azure s nainstalovaným klientským nástrojem SQL, jako je SQL Server Management Studio nebo SQL Operations Studio, umožňujícím připojení ke spravované instanci a spouštění dotazů.

> [!Note]
> Místo klientského virtuálního počítače Azure můžete nakonfigurovat připojení typu [Point-to-Site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) a připojit se ke spravované instanci z místního počítače.

Nejjednodušší způsob, jak vytvořit klientský virtuální počítač se všemi potřebnými nástroji, je použít šablony Azure Resource Manageru.

1. Klikněte na následující tlačítko (na jiné kartě prohlížeče se ujistěte, že jste přihlášeni k webu Azure Portal):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Do formuláře, který se otevře, zadejte název virtuálního počítače, uživatelské jméno správce a heslo, pomocí kterého se budete připojovat k virtuálnímu počítači.

    ![vytvoření klientského virtuálního počítače](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    Pokud jste nezměnili název virtuální sítě a výchozí podsítě, nemusíte měnit poslední dva parametry. Jinak je potřeba tyto hodnoty změnit na hodnoty, které jste zadali při nastavování síťového prostředí.

3. Klikněte na tlačítko Koupit a virtuální počítač Azure se nasadí v síti, kterou jste připravili.

4. Připojte se ke svému virtuálnímu počítači prostřednictvím připojení ke vzdálené ploše a vyhledejte aplikaci SQL Server Management Studio nebo SQL Operations Studio, které jsou na virtuálním počítači automaticky nainstalované.

5. Otevřete SQL Server Management Studio a v dialogovém okně **Připojení k serveru** do pole **Název serveru** zadejte **název hostitele** vaší spravované instance, vyberte **Ověřování SQL Serveru**, zadejte své přihlašovací jméno a heslo a pak klikněte na **Připojit**.

    ![Připojení přes SSMS](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Po připojení můžete zobrazit systémové a uživatelské databáze v uzlu Databáze a různé objekty v uzlech Zabezpečení, Objekty serveru, Replikace, Správa, Agent SQL Serveru a Profiler XEvent.

## <a name="next-steps"></a>Další kroky

 - [Připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md)
 - [Migrace databází z místního prostředí do spravované instance](sql-database-managed-instance-migrate.md)


