---
title: Správa privátních koncových bodů v Azure
description: Zjistěte, jak vytvořit privátní koncový bod pomocí Azure Portal
services: private-link
author: KumudD
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: d7c2aee3ad73552a57776af5ce6585b36518d169
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687055"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Vytvoření privátního koncového bodu pomocí Azure Portal

Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení. V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač v Virtual Network Azure, SQL Database Server s privátním koncovým bodem Azure pomocí Azure PowerShell. Pak můžete z virtuálního počítače bezpečně přistupovat k serveru SQL Database.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


> [!NOTE]
> Ve spojení s koncovými body služby ve stejné podsíti nejsou povolené privátní koncové body.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení (SQL Server v Azure v tomto příkladu).

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě


V této části vytvoříte Virtual Network a podsíť, která bude hostovat virtuální počítač, který se používá pro přístup k prostředku privátního propojení.

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.
1. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **WestCentralUS**.|
    | Název podsítě | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
    |||
1. Ponechte REST jako výchozí a vyberte **vytvořit**.


### <a name="create-virtual-machine"></a>Vytvořit virtuální počítač

1. V levé horní části obrazovky v Azure Portal vyberte **vytvořit prostředek** > **výpočetní** > **virtuální počítač**.

1. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Oblast | Vyberte **WestCentralUS**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Image | Vyberte **Windows Server 2019 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrzení hesla | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Nechejte výchozí nastavení **žádné**. |
    | **ÚSPORA PENĚZ** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |
    |||

1. Vyberte **Další: disky**.

1. V části **vytvořit virtuální počítač – disky**ponechte výchozí hodnoty a vyberte **Další: sítě**.

1. V nástroji **vytvořit virtuální počítač – síť**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)** .|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vybrat příchozí porty | Vyberte **http** a **RDP**.|
    |||


1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

## <a name="create-a-sql-database-server"></a>Vytvoření serveru databáze SQL
V této části vytvoříte server služby SQL Database v Azure. 

1. V levé horní části obrazovky v Azure Portal vyberte **vytvořit prostředek** > **databáze** > **databáze SQL**.

1. V nástroji **vytvořit SQL Database – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti databáze** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Název databáze  | Zadejte *MyDatabase*. Pokud se tento název povede, vytvořte jedinečný název. |
    |||
5. V **serveru**vyberte **vytvořit novou**. 
6. Na **novém serveru**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Název serveru  | Zadejte *MyServer*. Pokud se tento název povede, vytvořte jedinečný název.|
    | Přihlašovací jméno správce serveru| Zadejte jméno správce, které chcete zvolit. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku alespoň 8 znaků a musí splňovat definované požadavky. |
    | Umístění | Vyberte oblast Azure, ve které chcete, aby se vaše SQL Server nacházela. |
    
7. Vyberte **OK**. 
8. Vyberte **zkontrolovat + vytvořit**. Přejdete na **recenzi + create** page, kde Azure ověřuje vaši konfiguraci. 
9. Když se zobrazí zpráva s potvrzením ověření, vyberte **vytvořit**. 
10. Když se zobrazí zpráva s potvrzením ověření, vyberte vytvořit. 

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

V této části vytvoříte SQL Server a přidáte do něj privátní koncový bod. 

1. V levém horním rohu obrazovky Azure Portal vyberte **vytvořit prostředek** > **síť** > **privátní Link Center (Preview)** .
2. V části **centrum privátních odkazů – přehled**na možnost **vytvořit privátní připojení ke službě**vyberte možnost **Spustit**.
1. V **Vytvoření privátního koncového bodu (Preview) – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Name (Název) | Zadejte * myPrivateEndpoint*. Pokud se tento název povede, vytvořte jedinečný název. |
    |Oblast|Vyberte **WestCentralUS**.|
    |||
5. Vyberte **Další: prostředek**.
6. V **Vytvoření privátního koncového bodu – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V adresáři vyberte připojit k prostředku Azure.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. SQL/servery**. |
    | Prostředek |Vybrat *MyServer*|
    |Cílový dílčí prostředek |Výběr *sqlServer*|
    |||
7. Vyberte **Další: Konfigurace**.
8. V **Vytvoření privátního koncového bodu (Preview) – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**SÍTĚ**| |
    | Virtuální síť| Vyberte *MyVirtualNetwork*. |
    | Podsíť | Vyberte *mySubnet*. |
    |**INTEGRACE PRIVÁTNÍ DNS**||
    |Integrace s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátní DNS |Select *(New) privatelink. Database. Windows. NET* |
    |||

1. Vyberte **zkontrolovat + vytvořit**. Přejdete na **recenzi + create** page, kde Azure ověřuje vaši konfiguraci. 
2. Po zobrazení **úspěšného ověření** Message vyberte **vytvořit**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Připojení k virtuálnímu počítači pomocí vzdálené plochy (RDP)


Po vytvoření **myVm*se k němu připojte z Internetu následujícím způsobem: 

1. Na panelu hledání na portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) ( *. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor. RDP *.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby** > **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Přístup k serveru SQL Database soukromě z virtuálního počítače

1. Ve vzdálené ploše *myVM*otevřete PowerShell.

2. Zadejte hodnotu @ no__t-0. 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Nainstalujte [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. V **Connect to Server (připojit k serveru**) zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru| Vyberte **Databázový stroj**.|
    | Název serveru| Vybrat *MyServer.Database.Windows.NET* |
    | Uživatelské jméno | Zadejte heslo, které jste zadali během vytváření SQL serveru. |
    |Heslo |Zadejte heslo, které jste zadali během vytváření SQL serveru. |
    |Zapamatovat heslo|Vyberte **Ano**.|
    |||
1. Vyberte **připojit**.
2. Procházet databáze z levé nabídky
3. Volitelně Vytvoření nebo dotazování informací z MyDatabase.
4. Zavřete připojení ke vzdálené ploše pro *myVm*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete s použitím privátního koncového bodu, SQL serveru a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 
1. Zadejte *myResourceGroup* In the **Search** box v horní části portálu a vyberte *myResourceGroup* from výsledků hledání. 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Zadejte myResourceGroup pro **typ název skupiny prostředků** And vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač ve virtuální síti, serveru služby SQL Database a privátním koncovém bodu pro privátní přístup. Připojili jste se k jednomu virtuálnímu počítači z Internetu a bezpečně komunikovali na serveru SQL Database pomocí privátního odkazu. Další informace o privátních koncových bodech najdete v tématu [co je privátní koncový bod Azure](private-endpoint-overview.md).

