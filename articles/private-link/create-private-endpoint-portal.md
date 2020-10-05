---
title: Rychlý Start – Správa privátních koncových bodů v Azure
description: Naučte se vytvořit privátní koncový bod pomocí Azure Portal v tomto rychlém startu.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: ef6d49c9046ba04bbac40ec9bf555e12d2faa8f6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "84021700"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Rychlý Start: Vytvoření privátního koncového bodu pomocí Azure Portal

Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení. V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač v Virtual Network Azure, což je logický SQL Server s privátním koncovým bodem Azure pomocí Azure Portal. Pak můžete bezpečně přistupovat k SQL Database z virtuálního počítače.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení (SQL Server v Azure v tomto příkladu).

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části vytvoříte Virtual Network a podsíť, která bude hostovat virtuální počítač, který se používá pro přístup k prostředku privátního propojení.

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | USA – středozápad    |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Vytvořit virtuální počítač

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit**  >  **Compute**  >  **virtuální počítač**Compute.

1. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Oblast | Vyberte **WestCentralUS**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Image | Vyberte **Windows Server 2019 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Nechejte výchozí nastavení **žádné**. |
    | **UŠETŘETE PENÍZE** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |
    |||

1. Vyberte **Další: disky**.

1. V okně **Vytvořit virtuální počítač – Disky** nechte vybrané výchozí hodnoty a vyberte **Další: Sítě**.

1. V nástroji **vytvořit virtuální počítač – síť**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)**.|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vyberte příchozí porty | Vyberte **http** a **RDP**.|
    |||


1. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

1. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

## <a name="create-a-logical-sql-server"></a>Vytvoření logického SQL serveru

V této části vytvoříte v Azure logický SQL Server. 

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit databáze prostředků**  >  **Databases**  >  **SQL Database**.

1. V nástroji **vytvořit SQL Database – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti databáze** | |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Název databáze  | Zadejte *MyDatabase*. Pokud se tento název povede, vytvořte jedinečný název. |
    |||
5. V **serveru**vyberte **vytvořit novou**. 
6. Na **novém serveru**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Název serveru  | Zadejte *MyServer*. Pokud se tento název povede, vytvořte jedinečný název.|
    | Přihlášení správce serveru| Zadejte jméno správce, které chcete zvolit. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku alespoň 8 znaků a musí splňovat definované požadavky. |
    | Umístění | Vyberte oblast Azure, ve které chcete, aby se vaše SQL Server nacházela. |
    
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci. 
9. Jakmile se zobrazí zpráva Ověření proběhlo úspěšně, vyberte **Vytvořit**. 
10. Jakmile se zobrazí zpráva Ověření proběhlo úspěšně, vyberte Vytvořit. 

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

V této části vytvoříte SQL Server a přidáte do něj privátní koncový bod. 

1. V levém horním rohu obrazovky Azure Portal vyberte **vytvořit prostředek**  >  **sítě**  >  **privátní Link Center (Preview)**.
2. V části **centrum privátních odkazů – přehled**na možnost **vytvořit privátní připojení ke službě**vyberte možnost **Spustit**.
1. V **Vytvoření privátního koncového bodu (Preview) – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Name | Zadejte *myPrivateEndpoint*. Pokud se tento název povede, vytvořte jedinečný název. |
    |Oblast|Vyberte **WestCentralUS**.|
    |||
5. Vyberte **Další: prostředek**.
6. V **Vytvoření privátního koncového bodu – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V adresáři vyberte připojit k prostředku Azure.|
    | Předplatné| Vyberte předplatné. |
    | Typ prostředku | Vyberte **Microsoft. SQL/servery**. |
    | Prostředek |Vybrat *MyServer*|
    |Cílový dílčí prostředek |Výběr *sqlServer*|
    |||
7. Vyberte **Další: Konfigurace**.
8. V **Vytvoření privátního koncového bodu (Preview) – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**SÍTĚ**| |
    | Virtuální síť| Vyberte *MyVirtualNetwork*. |
    | Podsíť | Vyberte *mySubnet*. |
    |**INTEGRACE S PRIVÁTNÍM DNS**||
    |Integrovat s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátního DNS |Select *(New) privatelink. Database. Windows. NET* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci. 
2. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Připojení k virtuálnímu počítači přes vzdálenou plochu (RDP)


Po vytvoření **myVm**se k němu připojte z Internetu následujícím způsobem: 

1. Na portálu zadejte na panelu hledání *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (*. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete *stažený soubor. RDP* .

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby**  >  **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **Ano** nebo **Pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  

## <a name="access-sql-database-privately-from-the-vm"></a>Přístup k SQL Database soukromě z virtuálního počítače

1. Ve vzdálené ploše *myVM*otevřete PowerShell.

2. Zadejte `nslookup myserver.database.windows.net`. 

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

4. V **Connect to Server (připojit k serveru**) zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru| Vyberte **Databázový stroj**.|
    | Název serveru| Vybrat *MyServer.Database.Windows.NET* |
    | Uživatelské jméno | Zadejte uživatelské jméno, username@servername které se poskytuje během vytváření SQL serveru. |
    |Heslo |Zadejte heslo, které jste zadali během vytváření SQL serveru. |
    |Zapamatovat heslo|Vyberte **Ano**.|
    |||
1. Vyberte **Připojit**.
2. Procházet databáze z levé nabídky
3. Volitelně Vytvoření nebo dotazování informací z MyDatabase.
4. Zavřete připojení ke vzdálené ploše pro *myVm*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete s použitím privátního koncového bodu, SQL serveru a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 
1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroup* a ve výsledcích hledání vyberte *myResourceGroup* . 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Zadejte myResourceGroup pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač ve virtuální síti, logickém SQL serveru a privátním koncovém bodu pro privátní přístup. Připojili jste se k jednomu virtuálnímu počítači z Internetu a zabezpečeně komunikovali SQL Database pomocí privátního odkazu. Další informace o privátních koncových bodech najdete v tématu [co je privátní koncový bod Azure](private-endpoint-overview.md).
