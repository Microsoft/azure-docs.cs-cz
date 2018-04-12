---
title: Azure Portal – Vytvoření SQL Database Managed Instance | Microsoft Docs
description: Vytvořte ve virtuální síti Azure SQL Database Managed Instance a pomocí aplikace SSMS obnovte zálohu databáze Wide World Importers.
keywords: sql database tutorial, create a sql database managed instance
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 03/14/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 774a761465cfd886b85378a35dd43ac656a7ee48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Vytvoření Azure SQL Database Managed Instance na webu Azure Portal

Tento kurz ukazuje, jak pomocí webu Azure Portal vytvořit ve vyhrazené podsíti virtuální sítě Azure SQL Database Managed Instance (Preview). Následně ukazuje, jak se k Managed Instance připojit pomocí aplikace SQL Server Management Studio na virtuálním počítači ve stejné virtuální síti a pak do Managed Instance obnovit zálohu databáze uložené v úložišti objektů blob v Azure.

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
   |**Zakázat šíření tras BCP**|Zakázáno||
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

## <a name="to-apply-the-route-table-to-the-managed-instance-subnet"></a>Použití směrovací tabulky pro podsíť Managed Instance

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

3. Vyberte své předplatné a ověřte, že se u podmínek verze Perview zobrazuje **Přijato**.

   ![přijaté podmínky verze Preview pro Managed Instance](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. Ve formuláři Managed Instance vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název spravované instance**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Přihlašovací jméno správce spravované instance**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Heslo**|Libovolné platné heslo|Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Skupina prostředků**|Skupina prostředků, kterou jste vytvořili dříve||
   |**Umístění**|Umístění, které jste vybrali dříve|Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/).|
   |**Virtuální síť**|Virtuální síť, kterou jste vytvořili dříve|

   ![formulář pro vytvoření Managed Instance](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. Klikněte na **Cenová úroveň** a určete velikost výpočetních prostředků a prostředků úložiště a prohlédněte si možnosti cenové úrovně. Ve výchozím nastavení vaše instance získá bezplatně 32 GB prostoru úložiště, což pro vaše aplikace nemusí být dostatečné.
6. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader. 
   ![formulář pro vytvoření Managed Instance](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. Jakmile budete hotovi, kliknutím na **Použít** uložte provedený výběr.  
8. Kliknutím na **Vytvořit** nasaďte Managed Instance.
9. Kliknutím na ikonu **Oznámení** zobrazte stav nasazení.
 
   ![průběh nasazení](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. Kliknutím na **Probíhá nasazení** otevřete okno Managed Instance, kde můžete dále monitorovat průběh nasazení.
 
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

3. Klikněte na **OK**.
4. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Pro účely tohoto kurzu vám stačí malý virtuální počítač.

    ![Velikost virtuálních počítačů](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. Klikněte na **Vybrat**.
6. Ve formuláři **Nastavení** klikněte na **Podsíť** a pak vyberte **vm_subnet**. Nevybírejte podsíť, ve které je zřízená Managed Instance, ale jinou podsíť ve stejné virtuální síti.

    ![nastavení virtuálního počítače](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. Klikněte na **OK**.
8. Na stránce Souhrn zkontrolujte podrobnosti o nabídce a pak kliknutím na **Vytvořit** spusťte nasazení virtuálního počítače.
 
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

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Stažení zálohy Wide World Importers – Standard

Pomocí následujících kroků stáhněte zálohu Wide World Importers – Standard.

V aplikaci Internet Explorer do pole Adresa URL zadejte https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak a po zobrazení výzvy kliknutím na **Uložit** uložte tento soubor do složky **Stažené soubory**.

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Vytvoření účtu úložiště Azure a nahrání zálohy

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte **Úložiště** a pak kliknutím na **Účet úložiště** otevřete formulář účtu úložiště.

   ![účet úložiště](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Ve formuláři účtu úložiště vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Model nasazení**|Model prostředků||
   |**Druh účtu**|Blob Storage||
   |**Výkon**|Standard nebo Premium|Magnetické jednotky nebo disky SSD|
   |**Replikace**|(Locally redundant storage) Místně redundantní úložiště||
   |** Úroveň přístupu (výchozí)|Studená nebo horká||
   |**Vyžádání bezpečného přenosu**|Zakázáno||
   |**Předplatné**|Vaše předplatné|Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions).|
   |**Skupina prostředků**|Skupina prostředků, kterou jste vytvořili dříve|| 
   |**Umístění**|Umístění, které jste vybrali dříve||
   |**Virtuální sítě**|Zakázáno||

4. Klikněte na možnost **Vytvořit**.

   ![podrobnosti o účtu úložiště](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Po úspěšném nasazení účtu úložiště otevřete svůj nový účet úložiště.
6. V části **Nastavení** kliknutím na **Sdílený přístupový podpis** otevřete formulář sdíleného přístupového podpisu (SAS).

   ![formulář SAS](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. Ve formuláři SAS podle potřeby upravte výchozí hodnoty. Všimněte si, že datum/čas vypršení platnosti je ve výchozím nastavení pouze 8 hodin.
8. Klikněte na **Vygenerovat SAS**.

   ![dokončený formulář SAS](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Zkopírujte a uložte hodnoty **Token SAS** a **Adresa URL SAS serveru Blob**.
10. V části **Nastavení** klikněte na **Kontejnery**.

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. Kliknutím na **+ Kontejner** vytvořte kontejner pro uložení vaší zálohy.
12. Ve formuláři kontejneru vyplňte požadované informace s použitím informací v následující tabulce:

    | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Úroveň veřejného přístupu**|Privátní (bez anonymního přístupu)||

    ![podrobnosti o kontejneru](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Klikněte na **OK**.
14. Po vytvoření kontejneru ho otevřete.

    ![kontejner](./media/sql-database-managed-instance-tutorial/container.png)

15. Klikněte na **Vlastnosti kontejneru** a pak zkopírujte adresu URL ke kontejneru.

    ![adresa URL kontejneru](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Kliknutím na **Nahrát** otevřete formulář **Nahrát objekt blob**.

    ![nahrání](./media/sql-database-managed-instance-tutorial/upload.png)

17. Přejděte do své složky pro stahování a vyberte soubor **AdventureWorks2016.bak**.

    ![nahrání](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Klikněte na **Odeslat**.
19. Nepokračujte, dokud se nahrávání nedokončí.

    ![dokončené nahrávání](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Obnovení databáze Wide World Importers ze zálohy

Pomocí aplikace SSMS podle následujících kroků obnovte ze zálohy databázi Wide World Importers do své Managed Instance.

1. V aplikaci SSMS otevřete nové okno dotazu.
2. Pomocí následujícího skriptu vytvořte pověření SAS – zadejte adresu URL kontejneru účtu úložiště a klíč SAS, jak je uvedeno níže.

   ```
CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
, SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![pověření](./media/sql-database-managed-instance-tutorial/credential.png)

3. Pomocí následující skriptu zkontrolujte platnost pověření SAS a zálohy – zadejte adresu URL kontejneru se zálohou:

   ```
   RESTORE FILELISTONLY FROM URL = 
   'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![seznam souborů](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Pomocí následujícího skriptu obnovte ze zálohy databázi Wide World Importers – zadejte adresu URL kontejneru se zálohou:

   ```
   RESTORE DATABASE [Wide World Importers] FROM URL =
  'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![provádění obnovení](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Pokud chcete sledovat stav obnovení, spusťte v nové relaci dotazu následující dotaz:

   ```
SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
, dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
FROM sys.dm_exec_requests r 
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![průběh obnovení v procentech](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Po dokončení obnovení zobrazte databázi v Průzkumníku objektů. 

    ![dokončené obnovení](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Další kroky

- Informace o Managed Instance najdete v tématu [Co je Managed Instance?](sql-database-managed-instance.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě pro Managed Instance](sql-database-managed-instance-vnet-configuration.md).
- Informace o připojování aplikací najdete v tématu věnovaném [připojení aplikací](sql-database-managed-instance-connect-app.md).
- Kurz migrace pomocí Azure Database Migration Service (DMS) najdete v tématu věnovaném [migraci Managed Instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
