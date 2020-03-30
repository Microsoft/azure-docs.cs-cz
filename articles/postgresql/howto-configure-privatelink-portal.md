---
title: Privátní propojení – portál Azure – databáze Azure pro PostgreSQL – jeden server
description: Zjistěte, jak nakonfigurovat privátní propojení pro Azure Database for PostgreSQL- Single server z webu Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 72dcf95c8ae8d8da34532fa96e3bf0371f5112fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370912"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Vytvoření a správa privátního propojení pro Azure Database for PostgreSQL – jeden server pomocí portálu

Privátní koncový bod je základní stavební blok pro privátní propojení v Azure. Umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikovat s prostředky privátní ho propojení.  V tomto článku se dozvíte, jak pomocí portálu Azure vytvořit virtuální počítač ve virtuální síti Azure a Azure Database for PostgreSQL Single server s privátním koncovým bodem Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for PostgreSQL Single server podporuje cenové úrovně optimalizované pro obecné účely a paměť.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Vytvoření virtuálního počítače Azure

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku private link (postgresql server v Azure).

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě
V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení.

1. Na levé horní straně obrazovky vyberte Vytvořit virtuální**Networking** > **síť** **pro síť o prostředku** > .
2. V **možnosti Vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **Vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **Západní Evropa**.|
    | Podsíť – název | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
    |||
3. Zbytek ponechejte jako výchozí a vyberte **Vytvořit**.

### <a name="create-virtual-machine"></a>Vytvořit virtuální počítač

1. Na levé horní straně obrazovky na webu Azure Portal vyberte **Vytvořit prostředek** > **Výpočetní** > **virtuální počítač**.

2. V **části Vytvořit virtuální počítač – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Region (Oblast) | Vyberte **Západní Evropa**. |
    | Možnosti dostupnosti | Ponechte výchozí **Není vyžadována redundance žádné infrastruktury**. |
    | Image | Vyberte **Datové centrum Windows Server 2019**. |
    | Velikost | Ponechte výchozí **standardní DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno podle svého výběru. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Znovu zadejte heslo. |
    | **PRAVIDLA PŘÍCHOZÍHO PORTU** |  |
    | Veřejné příchozí porty | Ponechte výchozí **žádný**. |
    | **UŠETŘETE PENÍZE** |  |
    | Máte už licenci na Windows? | Ponechte výchozí **ne**. |
    |||

1. Vyberte **další: Disky**.

1. V **části Vytvoření virtuálního počítače – disky**ponechte výchozí hodnoty a vyberte **Další: Síť**.

1. V **panelu Vytvořit virtuální počítač – Vytváření sítí**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **program MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **hodnotu 10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)**.|
    | Veřejná IP adresa | Ponechte výchozí **(nový) myVm-ip**. |
    | Veřejné příchozí porty | Vyberte **Povolit vybrané porty**. |
    | Výběr příchozích portů | Vyberte **možnosti HTTP** a **RDP**.|
    |||


1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**.

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Vytvoření databáze Azure pro server PostgreSQL Single

V této části vytvoříte azure database pro postgresql server v Azure. 

1. Na levé horní straně obrazovky na webu Azure Portal vyberte Vytvořit**databázi azure** > databází **prostředků** > **pro PostgreSQL**.

1. V **možnosti nasazení Azure Database for PostgreSQL**vyberte **Jeden server** a poskytněte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.|
    | **Podrobnosti o serveru** |  |
    |Název serveru  | Zadejte *myserver*. Pokud je tento název přijat, vytvořte jedinečný název.|
    | Uživatelské jméno správce| Zadejte jméno správce, které si vyberete. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít tloušat alespoň 8 znaků a musí splňovat definované požadavky. |
    | Umístění | Vyberte oblast Azure, kde chcete, aby se váš PostgreSQL Server nacházet. |
    |Version  | Vyberte databázovou verzi serveru PostgreSQL, která je požadována.|
    | Výpočetní a úložiště| Vyberte cenovou úroveň, která je potřebná pro server na základě zatížení. |
    |||
 
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci. 
9. Když se zobrazí zpráva Ověření předáno, vyberte **vytvořit**. 
10. Když se zobrazí zpráva Ověření předáno, vyberte Vytvořit. 

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

V této části vytvoříte postgresql server a přidáte do něj soukromý koncový bod. 

1. Na levé horní straně obrazovky na webu Azure Portal vyberte Vytvořit > **privátní odkaz** **pro síť prostředků** > **Networking**.
2. V **Centru soukromých odkazů – přehled**vyberte možnost Vytvoření **soukromého připojení ke službě**možnost **Start**.

    ![Soukromý odkaz – přehled](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. V **části Vytvořit soukromý koncový bod – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.|
    | **Detaily instance** |  |
    | Name (Název) | Zadejte *myPrivateEndpoint*. Pokud je tento název přijat, vytvořte jedinečný název. |
    |Region (Oblast)|Vyberte **Západní Evropa**.|
    |||
5. Vyberte **další: Zdroj**.
6. V **části Vytvořit soukromý koncový bod – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | Vyberte připojit k prostředku Azure v mém adresáři.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **microsoft.dbforpostgreSQL/servery**. |
    | Prostředek |Vybrat *myServer*|
    |Cílový dílčí zdroj |Vybrat *postgresqlServer*|
    |||
7. Vyberte **další: Konfigurace**.
8. V **části Vytvořit soukromý koncový bod – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Sítí**| |
    | Virtuální síť| Vyberte *možnost MyVirtualNetwork*. |
    | Podsíť | Vyberte *možnost mySubnet*. |
    |**PRIVÁTNÍ INTEGRACE DNS**||
    |Integrace se soukromou zónou DNS |Vyberte **ano**. |
    |Soukromá zóna DNS |Vyberte *(Nový)privatelink.postgres.database.azure.com* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci. 
2. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**. 

    ![Soukromé propojení vytvořeno](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

    > [!NOTE] 
    > Hlavní název domény v nastavení DNS zákazníka se nepřekládá na nakonfigurovanou privátní IP adresu. Budete muset nastavit zónu DNS pro nakonfigurovaný program ReQDN, jak je znázorněno [zde](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Připojení k virtuálnímu počítači pomocí vzdálené plochy (RDP)


Po vytvoření **myVm**, připojte se k němu z internetu takto: 

1. Na vyhledávacím panelu portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **Připojit** se otevře **možnost Připojit k virtuálnímu počítači.**

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor rdp (Remote Desktop Protocol *)* a stáhne ho do počítače.

1. Otevřete *soubor Downloaded.rdp.*

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji a vraťte se na místní plochu.

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Přístup k serveru PostgreSQL soukromě z virtuálního účtu

1. Na vzdálené ploše *myVM*otevřete PowerShell.

2. Zadejte `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Otestujte připojení privátního propojení pro server PostgreSQL pomocí libovolného dostupného klienta. V níže uvedeném příkladu jsem k operaci použil [Azure Data studio.](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15)

4. V **novém připojení**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru| Vyberte **možnost PostgreSQL**.|
    | Název serveru| Vybrat *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | Uživatelské jméno | Zadejte uživatelské username@servername jméno, které je k dispozici při vytváření serveru PostgreSQL. |
    |Heslo |Zadejte heslo poskytnuté během vytváření serveru PostgreSQL. |
    |SSL|Vyberte **povinné**.|
    ||

5. Vyberte Connect (Připojit).

6. Procházejte databáze z levé nabídky.

7. (Volitelně) Vytvořte nebo dotazovat informace z postgreSQL serveru.

8. Zavřete připojení ke vzdálené ploše myVm.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Až budete hotovi s privátním koncovým bodem, postgreSQL serverem a virtuálním serverem, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup* a z výsledků hledání vyberte *myResourceGroup.* 
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte myResourceGroup pro **typ názvu skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto návodu jste vytvořili virtuální počítač ve virtuální síti, databázi Azure pro PostgreSQL – jeden server a privátní koncový bod pro soukromý přístup. Připojili jste se k jednomu virtuálnímu virtuálnímu zařízení z internetu a bezpečně jste komunikovali se serverem PostgreSQL pomocí private link. Další informace o privátní koncové body, najdete [v tématu Co je Azure privátní koncový bod](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
