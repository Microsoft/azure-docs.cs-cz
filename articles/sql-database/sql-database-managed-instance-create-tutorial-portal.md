---
title: Azure Portal – Vytvoření SQL Database Managed Instance | Microsoft Docs
description: Vytvořte Azure SQL Database Managed ve virtuální síti.
keywords: sql database tutorial, create a sql database managed instance
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 05/09/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: e337a5c7c203e2e1048149dfeff71436a4d2752f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850606"
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Vytvoření Azure SQL Database Managed Instance na webu Azure Portal

V tomto kurzu se dozvíte, jak pomocí portálu Azure Portal ve vyhrazené podsíti virtuální sítě vytvořit Azure SQL Database Managed Instance (ve verzi Preview) a jak se následně připojit k Managed Instance pomocí SQL Server Management Studia na virtuálním počítači ve stejné virtuální síti.

> [!div class="checklist"]
> * Přidání předplatného na seznam povolených
> * Konfigurace virtuální sítě
> * Vytvoření nové směrovací tabulky a trasy
> * Použití směrovací tabulky pro podsíť Managed Instance
> * Vytvoření Managed Instance
> * Vytvoření nové podsítě ve virtuální síti pro virtuální počítač
> * Vytvoření virtuálního počítače v nové podsíti ve virtuální síti
> * Připojení k virtuálnímu počítači
> * Instalace aplikace SSMS a připojení k Managed Instance


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!IMPORTANT]
> Seznam oblastí, ve kterých je Managed Instance aktuálně k dispozici, najdete v tématu [Migrace databází do plně spravované služby pomocí Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="whitelist-your-subscription"></a>Přidání předplatného na seznam povolených

Managed Instance se zpočátku vydává ve verzi Gated Public Preview, která vyžaduje přidání vašeho předplatného na seznam povolených. Pokud vaše předplatné ještě není na seznamu povolených, pomocí následujících kroků přijměte nabídnuté podmínky verze Preview a odešlete žádost o přidání na seznam povolených.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte **Managed Instance** a pak vyberte **Azure SQL Database Managed Instance (Preview)**.
3. Klikněte na možnost **Vytvořit**.

   ![vytvoření Managed Instance](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Vyberte své předplatné, klikněte na **Podmínky verze Preview** a pak zadejte požadované informace.

   ![podmínky verze Preview pro Managed Instance](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. Jakmile budete hotovi, klikněte na **OK**.

   ![podmínky verze Preview pro Managed Instance](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > Během čekání na schválení verze Preview můžete pokračovat a dokončit několik částí tohoto kurzu.

## <a name="configure-a-virtual-network-vnet"></a>Konfigurace virtuální sítě

Následující kroky ukazují, jak vytvořit novou virtuální síť [Azure Resource Manageru](../azure-resource-manager/resource-manager-deployment-model.md), kterou bude používat vaše Managed Instance. Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě pro Managed Instance](sql-database-managed-instance-vnet-configuration.md).

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte a klikněte na **Virtuální síť**, ověřte, že jako režim nasazení je vybraný **Resource Manager**, a pak klikněte na **Vytvořit**.

   ![vytvoření virtuální sítě](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Ve formuláři virtuální sítě vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Adresní prostor**|Jakýkoli platný rozsah adres, například 10.14.0.0/24|Název adresy virtuální sítě v zápisu CIDR.|
   |**Předplatné**|Vaše předplatné|Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions).|
   |**Skupina prostředků**|Jakákoli platná skupina prostředků (nová nebo existující)|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Umístění**|Libovolné platné umístění| Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/).|
   |**Název podsítě**|Jakýkoli platný název podsítě, například mi_subnet|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Rozsah adres podsítě**|Jakákoli platná adresa podsítě, například 10.14.0.0/28. Použijte adresní prostor podsítě menší než celý adresní prostor, abyste ve virtuální síti ponechali místo na vytváření dalších podsítí, například podsítě pro hostování testovacích nebo klientských aplikací nebo podsítí brány pro připojení z místního prostředí nebo jiných virtuálních sítí.|Rozsah adres podsítě v zápisu CIDR. Musí spadat do adresního prostoru virtuální sítě.|
   |**Koncové body služby**|Zakázáno|Povolte pro tuto podsíť jeden nebo více koncových bodů služby.|
   ||||

   ![formulář pro vytvoření virtuální sítě](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Klikněte na možnost **Vytvořit**.

## <a name="create-new-route-table-and-a-route"></a>Vytvoření nové směrovací tabulky a trasy

Následující kroky ukazují, jak vytvořit internetovou trasu dalšího segmentu směrování 0.0.0.0/0.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte a klikněte na **Směrovací tabulka** a pak na stránce Směrovací tabulka klikněte na **Vytvořit**. 

   ![vytvoření směrovací tabulky](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. Ve formuláři směrovací tabulky vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Předplatné**|Vaše předplatné|Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions).|
   |**Skupina prostředků**|Vyberte skupinu prostředků, kterou jste vytvořili v rámci předchozího postupu.|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Umístění**|Vyberte umístění, které jste zadali v rámci předchozího postupu.| Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/).|
   |**Zakázat šíření tras BCP**|Povoleno||
   ||||

   ![formulář pro vytvoření směrovací tabulky](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. Klikněte na možnost **Vytvořit**.
5. Po vytvoření směrovací tabulky otevřete nově vytvořenou směrovací tabulku.

   ![směrovací tabulka](./media/sql-database-managed-instance-tutorial/route-table.png)

6. Klikněte na **Trasy** a pak na **Přidat**.

   ![přidání do směrovací tabulky](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  Přidejte **internetovou trasu dalšího segmentu směrování 0.0.0.0/0** jako **jedinou** trasu s použitím informací v následující tabulce:

    | Nastavení| Navrhovaná hodnota | Popis |
    | ------ | --------------- | ----------- |
    |**Název trasy**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
    |**Předpona adresy**|0.0.0.0/0|Cílová IP adresa v zápisu CIDR, na kterou se tato trasa vztahuje.|
    |**Typ dalšího segmentu směrování**|Internet|Další segment směrování zpracovává odpovídající pakety pro tuto trasu.|
    |||

    ![trasa](./media/sql-database-managed-instance-tutorial/route.png)

8. Klikněte na **OK**.

## <a name="apply-the-route-table-to-the-managed-instance-subnet"></a>Použití směrovací tabulky pro podsíť Managed Instance

Následující kroky ukazují, jak nastavit novou směrovací tabulku v podsíti Managed Instance.

1. Pokud chcete nastavit směrovací tabulku v podsíti Managed Instance, otevřete virtuální síť, kterou jste vytvořili dříve.
2. Klikněte na **Podsítě** a pak na podsíť Managed Instance (na následujícím snímku obrazovky je to **mi_subnet**).

    ![podsíť](./media/sql-database-managed-instance-tutorial/subnet.png)

11. Klikněte na **Směrovací tabulka** a pak vyberte **myMI_route_table**.

    ![nastavení směrovací tabulky](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. Klikněte na **Uložit**.

    ![nastavení směrovací tabulky – uložení](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>Vytvoření Managed Instance

Následující kroky ukazují, jak po schválení pro verzi Preview vytvořit Managed Instance.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte **Managed Instance** a pak vyberte **Azure SQL Database Managed Instance (Preview)**.
3. Klikněte na možnost **Vytvořit**.

   ![vytvoření Managed Instance](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

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
   |**Virtuální síť**|Virtuální síť, kterou jste vytvořili dříve|

   ![formulář pro vytvoření Managed Instance](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. Klikněte na **Cenová úroveň** a určete velikost výpočetních prostředků a prostředků úložiště a prohlédněte si možnosti cenové úrovně. Ve výchozím nastavení vaše instance získá bezplatně 32 GB prostoru úložiště, což pro vaše aplikace nemusí být dostatečné.
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
> Čas nasazení první instance v podsíti je obvykle mnohem delší než v případě dalších instancí – dokončení někdy může trvat déle než 24 hodin. Nerušte operaci nasazování jenom proto, že trvá déle, než jste očekávali. Dlouhá doba potřebná k nasazení první instance je pouze dočasná. Očekávejte výrazné zkrácení doby nasazení krátce po zahájení verze Public Preview.

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>Vytvoření nové podsítě ve virtuální síti pro virtuální počítač

Následující kroky ukazují, jak ve virtuální síti vytvořit druhou podsíť pro virtuální počítač, na kterém nainstalujete aplikaci SQL Server Management Studio a který připojíte k Managed Instance.

1. Otevřete prostředek své virtuální sítě.
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. Klikněte na **Podsítě** a pak na **+ Podsíť**.
 
   ![přidání podsítě](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. Ve formuláři podsítě vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Rozsah adres (blok CIDR)**|Jakýkoli platný rozsah adres v rámci virtuální sítě (použijte výchozí hodnotu)||
   |**Skupina zabezpečení sítě**|Žádný||
   |**Směrovací tabulka**|Žádný||
   |**Koncové body služby**|Žádný||

   ![podrobnosti o podsíti virtuálních počítačů](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Klikněte na **OK**.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Vytvoření virtuálního počítače v nové podsíti ve virtuální síti

Následující kroky ukazují, jak vytvořit virtuální počítač ve stejné virtuální síti, ve které se vytváří Managed Instance. 

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyberte **Compute** a pak vyberte **Windows Server 2016 Datacenter** nebo **Windows 10**. V této části kurzu se používá Windows Server. Konfigurace Windows 10 je velmi podobná. 

   ![compute](./media/sql-database-managed-instance-tutorial/compute.png)

3. Ve formuláři virtuálního počítače vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Typ disku virtuálního počítače**|SSD|Disky SSD poskytují nejlepší poměr mezi cenou a výkonem.|   
   |**Uživatelské jméno**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Heslo**|Libovolné platné heslo|Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).| 
   |**Předplatné**|Vaše předplatné|Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions).|
   |**Skupina prostředků**|Skupina prostředků, kterou jste vytvořili dříve||
   |**Umístění**|Umístění, které jste vybrali dříve||
   |**Máte už licenci Windows?**|Ne|Pokud vlastníte licence Windows s aktivním programem SA (Software Assurance), ušetří vám Zvýhodněné hybridní využití Azure náklady na výpočetní výkon.|
   ||||

   ![formulář pro vytvoření virtuálního počítače](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

4. Klikněte na **OK**.
5. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Pro účely tohoto kurzu vám stačí malý virtuální počítač.

    ![Velikost virtuálních počítačů](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

6. Klikněte na **Vybrat**.
7. Ve formuláři **Nastavení** klikněte na **Podsíť** a pak vyberte **vm_subnet**. Nevybírejte podsíť, ve které je zřízená Managed Instance, ale jinou podsíť ve stejné virtuální síti.

    ![nastavení virtuálního počítače](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

8. Klikněte na **OK**.
9. Na stránce Souhrn zkontrolujte podrobnosti o nabídce a pak kliknutím na **Vytvořit** spusťte nasazení virtuálního počítače.
 
## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Následující kroky ukazují, jak se k nově vytvořenému virtuálnímu počítači připojit pomocí připojení ke vzdálené ploše.

1. Po dokončení nasazení přejděte k prostředku virtuálního počítače.

    ![Virtuální počítač](./media/sql-database-managed-instance-tutorial/vm.png)  

2. Klikněte na tlačítko **Připojit** ve vlastnostech virtuálního počítače. Vytvoří a stáhne se soubor protokolu RDP (Remote Desktop Protocol) – soubor .rdp.
3. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. 
4. Po zobrazení výzvy klikněte na **Připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) na Mac App Storu.

5. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a potom klikněte na **Ok**.

6. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

Na řídicím panelu Správce serveru jste připojeni ke svému virtuálnímu počítači.

> [!IMPORTANT]
> Nepokračujte, dokud se Managed Instance úspěšně nezřídí. Po zřízení načtěte název hostitele pro vaši instanci v poli **Spravovaná instance** na kartě **Přehled** pro vaši Managed Instance. Název je podobný tomuto: **drfadfadsfd.tr23.westus1-a.worker.database.windows.net**.

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>Instalace aplikace SSMS a připojení k Managed Instance

Následující kroky ukazují, jak stáhnout a nainstalovat aplikaci SSMS a pak se připojit k Managed Instance.

1. Ve Správci serveru klikněte v levém podokně na **Místní server**.

    ![Správce serveru – vlastnosti](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. V podokně **Vlastnosti** kliknutím **Zapnuto** upravte položku Konfigurace rozšířeného zabezpečení aplikace Internet Explorer.
3. V dialogovém okně **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer** klikněte v části Správci na **Vypnuto** a pak klikněte na **OK**.

    ![konfigurace rozšířeného zabezpečení aplikace Internet Explorer](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. Z hlavního panelu otevřete aplikaci **Internet Explorer**.
5. Vyberte **Použít doporučené nastavení zabezpečení a kompatibility** a pak kliknutím na **OK** dokončete nastavení aplikace Internet Explorer 11.
6. Do pole Adresa URL zadejte https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms a klikněte na **Přejít**. 
7. Stáhněte nejnovější verzi aplikace SQL Server Management Studio a po zobrazení výzvy klikněte na **Spustit**.
8. Po zobrazení výzvy začněte kliknutím na **Nainstalovat**.
9. Až instalace skončí, klikněte na **Zavřít**.
10. Otevřete aplikaci SSMS.
11. V dialogovém okně **Připojení k serveru** do pole **Název serveru** zadejte *název hostitele* pro Managed Instance, vyberte **Ověřování SQL Serveru**, zadejte své přihlašovací jméno a heslo a pak klikněte na **Připojit**.

    ![Připojení přes SSMS](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Po připojení můžete zobrazit systémové a uživatelské databáze v uzlu Databáze a různé objekty v uzlech Zabezpečení, Objekty serveru, Replikace, Správa, Agent SQL Serveru a Profiler XEvent.

> [!NOTE]
> Pokud chcete obnovit stávající SQL databázi do Managed Instance, pomocí služby [Azure Database Migration Service (DMS) pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) nebo příkazu [T-SQL RESTORE](sql-database-managed-instance-restore-from-backup-tutorial.md) můžete provést obnovení ze záložního souboru, případně můžete provést [Import ze souboru BACPAC](sql-database-import.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak pomocí portálu Azure Portal ve vyhrazené podsíti virtuální sítě vytvořit Azure SQL Database Managed Instance (ve verzi Preview) a jak se následně připojit k Managed Instance pomocí SQL Server Management Studia na virtuálním počítači ve stejné virtuální síti.  Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Přidání předplatného na seznam povolených
> * Konfigurace virtuální sítě
> * Vytvoření nové směrovací tabulky a trasy
> * Použití směrovací tabulky pro podsíť Managed Instance
> * Vytvoření Managed Instance
> * Vytvoření nové podsítě ve virtuální síti pro virtuální počítač
> * Vytvoření virtuálního počítače v nové podsíti ve virtuální síti
> * Připojení k virtuálnímu počítači
> * Instalace aplikace SSMS a připojení k Managed Instance

Přejděte na další kurz, ve kterém se naučíte obnovit zálohu databáze do Azure SQL Database Managed Instance.

> [!div class="nextstepaction"]
>[Obnovení zálohy databáze do Azure SQL Database Managed Instance](sql-database-managed-instance-restore-from-backup-tutorial.md)
