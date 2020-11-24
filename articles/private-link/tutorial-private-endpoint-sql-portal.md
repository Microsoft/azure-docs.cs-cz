---
title: Kurz – připojení k serveru SQL Azure pomocí privátního koncového bodu Azure – portál
description: V tomto kurzu se dozvíte, jak pomocí Azure Portal vytvořit server SQL Azure s privátním koncovým bodem.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: a7eca29dc9390306ac6ad4e66eec75a25c2d33ff
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522154"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Kurz – připojení k serveru SQL Azure pomocí privátního koncového bodu Azure – Azure Portal

Privátní koncový bod Azure je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě s prostředky privátního propojení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte virtuální síť a hostitele bastionu.
> * Vytvoří virtuální počítač.
> * Vytvoření serveru SQL Azure a privátního koncového bodu.
> * Otestujte připojení k privátnímu koncovému bodu SQL serveru.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.


## <a name="create-a-virtual-network-and-bastion-host"></a>Vytvoření virtuální sítě a hostitele bastionu

V této části vytvoříte virtuální síť, podsíť a hostitele bastionu. 

Hostitel bastionu se bude používat k zabezpečenému připojení k virtuálnímu počítači za účelem testování privátního koncového bodu.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **CreateSQLEndpointTutorial-RG** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Region           | Vyberte **východní USA** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

5. V části **název podsítě** vyberte slovo **výchozí**.

6. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **mySubnet** |
    | Rozsah adres podsítě | Zadejte **10.1.0.0/24** |

7. Vyberte **Uložit**.

8. Vyberte kartu **zabezpečení** .

9. V části **BastionHost** vyberte **Povolit**. Zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název bastionu | Zadejte **myBastionHost** |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.1.1.0/24** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBastionIP**. </br> Vyberte **OK**. |


8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

V této části vytvoříte virtuální počítač, který se použije k otestování privátního koncového bodu.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **Compute**  >  **virtuální počítač** COMPUTE nebo ve vyhledávacím poli vyhledejte **virtuální počítač** .
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **CreateSQLEndpointTutorial** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM** |
    | Region | Vyberte **východní USA** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter – Gen1** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | **myVNet** |
    | Podsíť | **mySubnet** |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | **Basic**|
    | Veřejné příchozí porty | Vyberte **Žádná**. |
   
5. Vyberte **Zkontrolovat a vytvořit**. 
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Vytvoření serveru SQL Azure a privátního koncového bodu

V této části vytvoříte SQL Server v Azure. 

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit databáze prostředků**  >  **Databases**  >  **SQL Database**.

1. Na kartě **základy** pro **Vytvoření databáze SQL** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **CreateSQLEndpointTutorial**. Tuto skupinu prostředků jste vytvořili v předchozí části.|
    | **Podrobnosti databáze** |  |
    | Název databáze  | Zadejte **mysqldatabase**. Pokud se tento název povede, vytvořte jedinečný název. |
    | Server | Vyberte, že chcete **vytvořit novou** IP adresu. |

6. Na **novém serveru** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název serveru  | Zadejte **MySQLServer**. Pokud se tento název povede, vytvořte jedinečný název.|
    | Přihlášení správce serveru | Zadejte jméno správce, které chcete zvolit. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku alespoň 8 znaků a musí splňovat definované požadavky. |
    | Umístění | Vyberte **východní USA** |
    
7. Vyberte **OK**.

8. Vyberte kartu **síť** nebo vyberte tlačítko **Další: síťové** .

9. Na kartě **sítě** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Připojení k síti** | |
    | Metoda připojení | Vyberte **privátní koncový bod**. |
   
10. V **privátních koncových bodech** vyberte **+ Přidat privátní koncový bod** .

11. V **Vytvoření privátního koncového bodu** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **CreateSQLEndpointTutorial**. |
    | Umístění | Vyberte **USA – východ**. |
    | Name | Zadejte **myPrivateSQLendpoint**. |
    | Cílový dílčí prostředek | Vyberte **SQLServer**. |
    | **Sítě** |  |
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **mySubnet**. |
    | **Integrace Privátní DNS** | |
    | Integrovat s privátní zónou DNS | Ponechte výchozí **hodnotu Ano**. |
    | Zóna Privátního DNS | Ponechte výchozí **(New) privatelink.Database.Windows.NET**. |

12. Vyberte **OK**. 

13. Vyberte **Zkontrolovat a vytvořit**.

14. Vyberte **Vytvořit**.

## <a name="test-connectivity-to-private-endpoint"></a>Test připojení k privátnímu koncovému bodu

V této části použijete virtuální počítač, který jste vytvořili v předchozím kroku, abyste se připojili k SQL serveru přes soukromý koncový bod.

1. V levém navigačním podokně vyberte **skupiny prostředků** .

2. Vyberte **CreateSQLEndpointTutorial**.

3. Vyberte **myVM**.

4. Na stránce Přehled pro **myVM** vyberte **připojit** a pak **bastionu**.

5. Vyberte tlačítko modrého **použití bastionu** .

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Po připojení otevřete Windows PowerShell na serveru.

8. Zadejte `nslookup <sqlserver-name>.database.windows.net`. Nahraďte **\<sqlserver-name>** názvem SQL serveru, který jste vytvořili v předchozích krocích.  Zobrazí se zpráva podobná tomu, co se zobrazuje níže:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Pro název SQL serveru se vrátí privátní IP adresa **10.1.0.5** .  Tato adresa je v podsíti virtuální sítě, kterou jste vytvořili dříve.


9. Nainstalujte [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) v **myVM**.

10. Otevřete **SQL Server Management Studio**.

4. V **Connect to Server (připojit k serveru**) zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru | Vyberte **Databázový stroj**.|
    | Název serveru | Zadejte **\<sqlserver-name> . Database.Windows.NET** |
    | Ověřování | Vyberte **Ověřování SQL Serveru**. |
    | Uživatelské jméno | Zadejte uživatelské jméno, které jste zadali při vytváření serveru. |
    | Heslo | Zadejte heslo, které jste zadali při vytváření serveru. |
    | Zapamatovat heslo | Vyberte **Ano**. |

1. Vyberte **Connect** (Připojit).
2. Procházet databáze z levé nabídky
3. Volitelně Vytvoření nebo dotazování informací z **mysqldatabase**.
4. Zavřete připojení ke vzdálené ploše pro **myVM**. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete s použitím privátního koncového bodu, SQL serveru a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 
1. Do **vyhledávacího** pole v horní části portálu zadejte **CreateSQLEndpointTutorial** a ve výsledcích hledání vyberte **CreateSQLEndpointTutorial** . 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Zadejte CreateSQLEndpointTutorial pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili:

* Virtuální síť a hostitel bastionu
* Virtuální počítač.
* Azure SQL Server s privátním koncovým bodem.

Virtuální počítač jste použili k zabezpečenému otestování připojení k SQL serveru v rámci privátního koncového bodu.

Naučte se vytvořit službu privátního propojení:
> [!div class="nextstepaction"]
> [Vytvoření služby privátního propojení](create-private-link-service-portal.md)