---
title: Úvodní příručka – správa privátních koncových bodů v Azure
description: Naučte se, jak vytvořit privátní koncový bod pomocí portálu Azure v tomto rychlém startu
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1bdc089bb89a732e329bf7d3ffd3d5b5c09ba408
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637248"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Úvodní příručka: Vytvoření privátního koncového bodu pomocí portálu Azure

Privátní koncový bod je základní stavební blok pro privátní propojení v Azure. Umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikovat s prostředky privátní ho propojení. V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač ve virtuální síti Azure, sql database server s privátním koncovým bodem Azure pomocí portálu Azure. Potom můžete bezpečně přistupovat k databázovému serveru SQL z virtuálního účtu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku Private Link (SQL server v Azure v tomto příkladu).

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení.

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroup |
| **\<>názvů virtuálních sítí** | myVirtualNetwork          |
| **\<>názvu oblasti**          | USA – středozápad    |
| **\<>adresního prostoru IPv4**   | 10.1.0.0/16          |
| **\<>názvu podsítě**          | mySubnet        |
| **\<>rozsah emitované sítě** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Vytvořit virtuální počítač

1. Na levé horní straně obrazovky na webu Azure Portal vyberte **Vytvořit prostředek** > **Výpočetní** > **virtuální počítač**.

1. V **části Vytvořit virtuální počítač – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Region (Oblast) | Vyberte **možnost WestCentralUS**. |
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

## <a name="create-a-sql-database-server"></a>Vytvoření databázového serveru SQL
V této části vytvoříte databázový server SQL v Azure. 

1. Na levé horní straně obrazovky na webu Azure Portal vyberte **Vytvořit** > **databázi SQL****databáze prostředků** > .

1. V **části Vytvořit databázi SQL – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti databáze** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Název databáze  | Zadejte *mydatabase*. Pokud je tento název přijat, vytvořte jedinečný název. |
    |||
5. V **serveru**vyberte **Vytvořit nový**. 
6. Na **novém serveru**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Název serveru  | Zadejte *myserver*. Pokud je tento název přijat, vytvořte jedinečný název.|
    | Přihlašovací jméno správce serveru| Zadejte jméno správce, které si vyberete. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít tloušat alespoň 8 znaků a musí splňovat definované požadavky. |
    | Umístění | Vyberte oblast Azure, kde chcete, aby se váš SQL Server navašem nasazuje. |
    
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci. 
9. Když se zobrazí zpráva Ověření předáno, vyberte **vytvořit**. 
10. Když se zobrazí zpráva Ověření předáno, vyberte Vytvořit. 

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

V této části vytvoříte sql server a přidáte do něj soukromý koncový bod. 

1. Na levé horní straně obrazovky na webu Azure Portal vyberte Vytvořit**Networking** > **privátní centrum připojení k** **prostředkům** > (Preview).
2. V **Centru soukromých odkazů – přehled**vyberte možnost Vytvoření **soukromého připojení ke službě**možnost **Start**.
1. V **části Vytvořit soukromý koncový bod (náhled) – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Name (Název) | Zadejte *myPrivateEndpoint*. Pokud je tento název přijat, vytvořte jedinečný název. |
    |Region (Oblast)|Vyberte **možnost WestCentralUS**.|
    |||
5. Vyberte **další: Zdroj**.
6. V **části Vytvořit soukromý koncový bod – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | Vyberte připojit k prostředku Azure v mém adresáři.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **položku Microsoft.Sql/servers**. |
    | Prostředek |Vybrat *myServer*|
    |Cílový dílčí zdroj |Vybrat *sqlServer*|
    |||
7. Vyberte **další: Konfigurace**.
8. V **části Vytvořit soukromý koncový bod (náhled) – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Sítí**| |
    | Virtuální síť| Vyberte *možnost MyVirtualNetwork*. |
    | Podsíť | Vyberte *možnost mySubnet*. |
    |**PRIVÁTNÍ INTEGRACE DNS**||
    |Integrace se soukromou zónou DNS |Vyberte **ano**. |
    |Soukromá zóna DNS |Vybrat *(Nový)privatelink.database.windows.net* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci. 
2. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**. 
 
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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Soukromý přístup k databázovému serveru SQL z virtuálního serveru

1. Na vzdálené ploše *myVM*otevřete PowerShell.

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
3. Nainstalujte [aplikaci SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. V **popřípadě Připojit k serveru**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru| Vyberte **Databázový stroj**.|
    | Název serveru| Vybrat *myserver.database.windows.net* |
    | Uživatelské jméno | Zadejte uživatelské username@servername jméno, které je k dispozici při vytváření serveru SQL. |
    |Heslo |Zadejte heslo poskytnuté během vytváření serveru SQL. |
    |Zapamatovat heslo|Vyberte **ano**.|
    |||
1. Vyberte **Connect** (Připojit).
2. Procházejte databáze z levé nabídky.
3. (Volitelně) Vytvořte informace nebo dotazz databáze.
4. Zavřete připojení ke vzdálené ploše *myVm*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete hotovi s privátním koncovým bodem, sql serverem a virtuálním serverem, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 
1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup* a z výsledků hledání vyberte *myResourceGroup.* 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Zadejte myResourceGroup pro **typ názvu skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač ve virtuální síti, databázový server SQL a soukromý koncový bod pro soukromý přístup. Připojili jste se k jednomu virtuálnímu virtuálnímu serveru z internetu a bezpečně jste je komunikovali s databázovým serverem SQL pomocí privátního propojení. Další informace o privátní koncové body, najdete v [tématu Co je azure privátní koncový bod?](private-endpoint-overview.md).
