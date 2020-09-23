---
title: Privátní Link-Azure Portal-Azure Database for PostgreSQL-Single server
description: Přečtěte si, jak nakonfigurovat privátní odkaz pro Azure Database for PostgreSQL jeden server z Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 7d2bdb96485a811ea9b3dde5320084f666508622
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907498"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Vytvoření a správa privátního odkazu pro Azure Database for PostgreSQL pro jeden server pomocí portálu

Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení.  V tomto článku se naučíte, jak použít Azure Portal k vytvoření virtuálního počítače v Azure Virtual Network a Azure Database for PostgreSQL jednom serveru s privátním koncovým bodem Azure.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Funkce privátního odkazu je dostupná jenom pro Azure Database for PostgreSQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je databázový server v jedné z těchto cenových úrovní.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Vytvoření virtuálního počítače Azure

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k vašemu prostředku privátního propojení (PostgreSQL Server v Azure).

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě
V této části vytvoříte Virtual Network a podsíť, která bude hostovat virtuální počítač, který se používá pro přístup k prostředku privátního propojení.

1. V levé horní části obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **virtuální síť**.
2. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **Západní Evropa**.|
    | Název podsítě | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
    |||
3. Ponechte REST jako výchozí a vyberte **vytvořit**.

### <a name="create-virtual-machine"></a>Vytvořit virtuální počítač

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit**  >  **Compute**  >  **virtuální počítač**Compute.

2. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Oblast | Vyberte **Západní Evropa**. |
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

> [!NOTE]
> V některých případech jsou Azure Database for PostgreSQL a VNet-Subnet v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Ujistěte se, že oba odběry mají zaregistrovaný poskytovatel prostředků **Microsoft. DBforPostgreSQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Vytvoření jednoho serveru Azure Database for PostgreSQL

V této části vytvoříte Azure Database for PostgreSQL Server v Azure. 

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit**  >  **databáze**prostředků  >  **Azure Database for PostgreSQL**.

1. V **Azure Database for PostgreSQL možnosti nasazení**vyberte **jeden server** a zadejte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **Podrobnosti serveru** |  |
    |Název serveru  | Zadejte *MyServer*. Pokud se tento název povede, vytvořte jedinečný název.|
    | Uživatelské jméno správce| Zadejte jméno správce, které chcete zvolit. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku alespoň 8 znaků a musí splňovat definované požadavky. |
    | Umístění | Vyberte oblast Azure, ve které chcete, aby se server PostgreSQL nacházel. |
    |Verze  | Vyberte verzi databáze serveru PostgreSQL, která je povinná.|
    | Výpočty a úložiště| Vyberte cenovou úroveň, která je potřebná pro server na základě zatížení. |
    |||
 
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci. 
9. Když se zobrazí zpráva s potvrzením ověření, vyberte **vytvořit**. 
10. Když se zobrazí zpráva s potvrzením ověření, vyberte vytvořit. 

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

V této části vytvoříte server PostgreSQL a přidáte do něj privátní koncový bod. 

1. V levé horní části obrazovky v Azure Portal vyberte **vytvořit prostředek**  >  **sítě**  >  **privátní odkaz**.
2. V části **centrum privátních odkazů – přehled**na možnost **vytvořit privátní připojení ke službě**vyberte možnost **Spustit**.

    :::image type="content" source="media/concepts-data-access-and-security-private-link/privatelink-overview.png" alt-text="Přehled privátních odkazů":::

1. V **rámci vytvoření privátního koncového bodu základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **Podrobnosti instance** |  |
    | Name | Zadejte *myPrivateEndpoint*. Pokud se tento název povede, vytvořte jedinečný název. |
    |Oblast|Vyberte **Západní Evropa**.|
    |||
5. Vyberte **Další: prostředek**.
6. V **Vytvoření privátního koncového bodu – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V adresáři vyberte připojit k prostředku Azure.|
    | Předplatné| Vyberte předplatné. |
    | Typ prostředku | Vyberte **Microsoft. DBforPostgreSQL/servery**. |
    | Prostředek |Vybrat *MyServer*|
    |Cílový dílčí prostředek |Vybrat *postgresqlServer*|
    |||
7. Vyberte **Další: Konfigurace**.
8. V **Vytvoření privátního koncového bodu – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**SÍTĚ**| |
    | Virtuální síť| Vyberte *MyVirtualNetwork*. |
    | Podsíť | Vyberte *mySubnet*. |
    |**INTEGRACE S PRIVÁTNÍM DNS**||
    |Integrovat s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátního DNS |Select *(New) privatelink. Postgres. Database. Azure. com* |
    |||

    > [!Note] 
    > Pro vaši službu použijte předdefinovanou privátní zónu DNS nebo zadejte upřednostňovaný název zóny DNS. Podrobnosti najdete v tématu [Konfigurace zóny DNS služeb Azure](../private-link/private-endpoint-dns.md) .

1. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci. 
2. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**. 

    :::image type="content" source="media/concepts-data-access-and-security-private-link/show-postgres-private-link.png" alt-text="Privátní odkaz se vytvořil.":::

    > [!NOTE] 
    > Plně kvalifikovaný název domény v nastavení DNS zákazníka se nepřekladuje na nakonfigurovanou soukromou IP adresu. Bude nutné nastavit zónu DNS pro nakonfigurovaný plně kvalifikovaný název domény, jak je znázorněno [zde](../dns/dns-operations-recordsets-portal.md).

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

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Přístup k serveru PostgreSQL soukromě z virtuálního počítače

1. Ve vzdálené ploše *myVM*otevřete PowerShell.

2. Zadejte  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com` . 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Otestujte připojení privátního propojení pro server PostgreSQL pomocí libovolného dostupného klienta. V následujícím příkladu jsem k provedení operace použili [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) .

4. V **nové připojení**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru| Vyberte **PostgreSQL**.|
    | Název serveru| Vybrat *mydemopostgresserver.privatelink.Postgres.Database.Azure.com* |
    | Uživatelské jméno | Zadejte uživatelské jméno, username@servername které je k dispozici během vytváření PostgreSQL serveru. |
    |Heslo |Zadejte heslo, které jste zadali během vytváření PostgreSQL serveru. |
    |SSL|Vyberte možnost **požadováno**.|
    ||

5. Vyberte Připojit.

6. Procházet databáze z levé nabídky

7. Volitelně Vytvoření nebo dotazování informací ze serveru postgreSQL

8. Zavřete připojení ke vzdálené ploše pro myVm.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Až budete s použitím privátního koncového bodu, serveru PostgreSQL a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroup* a ve výsledcích hledání vyberte *myResourceGroup*   .
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte myResourceGroup pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto postupu jste vytvořili virtuální počítač ve virtuální síti, Azure Database for PostgreSQL jeden server a soukromý koncový bod pro privátní přístup. Připojili jste se k jednomu virtuálnímu počítači z Internetu a bezpečně komunikovali se serverem PostgreSQL pomocí privátního odkazu. Další informace o privátních koncových bodech najdete v tématu [co je privátní koncový bod Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md