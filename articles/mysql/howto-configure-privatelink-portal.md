---
title: Privátní odkaz pro metodu instalace portálu Azure Database for MySQL (Preview)
description: Postup konfigurace privátního odkazu pro Azure Database for MySQL z Azure Portal
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 6ad8726e61f4f8e79cfa9c437b3f0ea876b55e27
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425472"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-portal"></a>Vytvoření a správa privátního odkazu pro Azure Database for MySQL (ve verzi Preview) pomocí portálu

Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení. V tomto článku se naučíte, jak pomocí Azure Portal vytvořit virtuální počítač v Azure Virtual Network a Azure Database for MySQL server s privátním koncovým bodem Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for MySQL podporuje cenové úrovně optimalizované pro Pro obecné účely a paměť.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu [Portál Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Vytvoření virtuálního počítače Azure

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení (Server MySQL v Azure).

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě
V této části vytvoříte Virtual Network a podsíť, která bude hostovat virtuální počítač, který se používá pro přístup k prostředku privátního propojení.

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.
2. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **Západní Evropa**.|
    | Název podsítě | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
    |||
3. Ponechte REST jako výchozí a vyberte **vytvořit**.

### <a name="create-virtual-machine"></a>Vytvořit virtuální počítač

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit prostředek** > **výpočetní** > **virtuální počítač**.

2. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Oblast | Vyberte **Západní Evropa**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Image | Vyberte **Windows Server 2019 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte znovu heslo. |
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
    | Vyberte příchozí porty | Vyberte **http** a **RDP**.|
    |||


1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

## <a name="create-an-azure-database-for-mysql"></a>Vytvoření Azure Database for MySQL

V této části vytvoříte Azure Database for MySQL server v Azure. 

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit prostředek** > **databáze** > **Azure Database for MySQL**.

1. V **Azure Database for MySQL** zadejte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **Podrobnosti o serveru** |  |
    |Název serveru  | Zadejte *MyServer*. Pokud se tento název povede, vytvořte jedinečný název.|
    | Uživatelské jméno správce| Zadejte jméno správce, které chcete zvolit. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku alespoň 8 znaků a musí splňovat definované požadavky. |
    | Umístění | Vyberte oblast Azure, ve které chcete, aby se server MySQL nacházel. |
    |Verze  | Vyberte verzi databáze serveru MySQL, která je povinná.|
    | Výpočty a úložiště| Vyberte cenovou úroveň, která je potřebná pro server na základě zatížení. |
    |||
 
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci. 
9. Když se zobrazí zpráva s potvrzením ověření, vyberte **vytvořit**. 
10. Když se zobrazí zpráva s potvrzením ověření, vyberte vytvořit. 

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

V této části vytvoříte Server MySQL a přidáte do něj privátní koncový bod. 

1. V levém horním rohu obrazovky v Azure Portal vyberte **vytvořit prostředek** > **sítě** > **privátní Link Center (Preview)** .

2. V části **centrum privátních odkazů – přehled**na možnost **vytvořit privátní připojení ke službě**vyberte možnost **Spustit**.

    ![Přehled privátních odkazů](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. V **Vytvoření privátního koncového bodu (Preview) – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **Podrobnosti instance** |  |
    | Název | Zadejte *myPrivateEndpoint*. Pokud se tento název povede, vytvořte jedinečný název. |
    |Oblast|Vyberte **Západní Evropa**.|
    |||

5. Vyberte **Další: prostředek**.
6. V **Vytvoření privátního koncového bodu – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V adresáři vyberte připojit k prostředku Azure.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. DBforMySQL/servery**. |
    | Prostředek |Vybrat *MyServer*|
    |Cílový dílčí prostředek |Vybrat *mysqlServer*|
    |||
7. Vyberte **Další: Konfigurace**.
8. V **Vytvoření privátního koncového bodu (Preview) – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**SÍTĚ**| |
    | Virtuální síť| Vyberte *MyVirtualNetwork*. |
    | Podsíť | Vyberte *mySubnet*. |
    |**INTEGRACE PRIVÁTNÍ DNS**||
    |Integrace s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátní DNS |Select *(New) privatelink. MySQL. Database. Azure. com* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci. 
2. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**. 

    ![Privátní odkaz se vytvořil.](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Připojení k virtuálnímu počítači pomocí vzdálené plochy (RDP)


Po vytvoření **myVm**se k němu připojte z Internetu následujícím způsobem: 

1. Na panelu hledání na portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) ( *. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete *stažený soubor. RDP* .

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby** > **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Privátní přístup k serveru MySQL z virtuálního počítače

1. Ve vzdálené ploše *myVM*otevřete PowerShell.

2. Zadejte `nslookup  myServer.privatelink.mysql.database.azure.com`. 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Otestujte připojení k privátnímu propojení pro server MySQL pomocí libovolného dostupného klienta. V následujícím příkladu jsem k provedení operace použili aplikaci [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) .

4. V **nové připojení**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru| Vyberte **MySQL**.|
    | Název serveru| Vybrat *MyServer.privatelink.MySQL.Database.Azure.com* |
    | Uživatelské jméno | Zadejte uživatelské jméno jako username@servername, které je k dispozici během vytváření serveru MySQL. |
    |Heslo |Zadejte heslo, které jste zadali během vytváření serveru MySQL. |
    |SSL|Vyberte možnost **požadováno**.|
    ||

5. Vyberte připojit.

6. Procházet databáze z levé nabídky

7. Volitelně Vytvoření nebo dotazování informací ze serveru MySQL.

8. Zavřete připojení ke vzdálené ploše pro myVm.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Po dokončení používání privátního koncového bodu, serveru MySQL a virtuálního počítače odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroup* a ve výsledcích hledání vyberte *myResourceGroup* .
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte myResourceGroup pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto postupu jste vytvořili virtuální počítač ve virtuální síti, Azure Database for MySQL a privátní koncový bod pro privátní přístup. Připojili jste se k jednomu virtuálnímu počítači z Internetu a zabezpečeně komunikovali na serveru MySQL pomocí privátního odkazu. Další informace o privátních koncových bodech najdete v tématu [co je privátní koncový bod Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
