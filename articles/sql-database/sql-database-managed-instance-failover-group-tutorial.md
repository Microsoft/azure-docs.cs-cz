---
title: 'Kurz: Přidání spravované instance SQL Database do skupiny převzetí služeb při selhání'
description: Naučte se konfigurovat skupinu převzetí služeb při selhání pro vaši Azure SQL Database spravovanou instanci.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: 3e5b96cf4227e933aa99b37469410276a775dbed
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103088"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Kurz: Přidání spravované instance SQL Database do skupiny převzetí služeb při selhání

Přidejte SQL Database spravovanou instanci do skupiny převzetí služeb při selhání. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> - Vytvoření primární spravované instance
> - Vytvořte sekundární spravovanou instanci jako součást [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md). 
> - Testovací převzetí služeb při selhání

  > [!NOTE]
  > - Při procházení tohoto kurzu se ujistěte, že konfigurujete prostředky s [požadavky pro nastavení skupin převzetí služeb při selhání pro spravovanou instanci](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Vytvoření spravované instance může trvat poměrně dlouhou dobu. V důsledku toho může dokončení tohoto kurzu trvat několik hodin. Další informace o časech zřizování najdete v tématu [operace správy spravované instance](sql-database-managed-instance.md#managed-instance-management-operations). 
  > - Používání skupin převzetí služeb při selhání se spravovanými instancemi je aktuálně ve verzi Preview. 

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) , pokud ho ještě nemáte. 


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1\. Vytvoření skupiny prostředků a primární spravované instance
V tomto kroku vytvoříte skupinu prostředků a primární spravovanou instanci pro skupinu převzetí služeb při selhání pomocí Azure Portal. 

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici databáze vyberete Zobrazit podrobnosti.
1. Na dlaždici **spravované instance SQL** vyberte **vytvořit** . 

    ![Vybrat spravovanou instanci](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na stránce **Vytvoření spravované Instance Azure SQL Database** na kartě **základy**
    1. V části **Podrobnosti o projektu**vyberte v rozevíracím seznamu své **předplatné** a pak zvolte **vytvořit novou** skupinu prostředků. Zadejte název vaší skupiny prostředků, `myResourceGroup`jako je například. 
    1. V části **Podrobnosti spravované instance**zadejte název spravované instance a oblast, do které chcete nasadit spravovanou instanci. Ve službě **COMPUTE + Storage** ponechte výchozí hodnoty. 
    1. V části **účet správce**zadejte přihlašovací jméno správce, například `azureuser`a složitá hesla správce. 

    ![Vytvořit primární MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Ponechte zbývající nastavení na výchozí hodnoty a vyberte **zkontrolovat a vytvořit** a zkontrolujte nastavení spravované instance. 
1. Vyberte **vytvořit** k vytvoření vaší primární spravované instance. 


## <a name="2---create-a-virtual-network"></a>2\. vytvoření virtuální sítě
V tomto kroku vytvoříte virtuální síť pro sekundární spravovanou instanci. Tento krok je nezbytný, protože existuje požadavek, aby podsíť primárních a sekundárních spravovaných instancí nepřesahoval rozsahy adres. 

Pokud chcete ověřit rozsah podsítě vaší primární virtuální sítě, postupujte podle těchto kroků:
1. V [Azure Portal](https://portal.azure.com)přejděte do skupiny prostředků a vyberte virtuální síť pro vaši primární instanci. 
1. V části **Nastavení** vyberte **podsítě** a poznamenejte si **Rozsah adres**. Rozsah adres podsítě virtuální sítě pro sekundární spravovanou instanci se nemůže překrývat. 


   ![Primární podsíť](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Pokud chcete vytvořit virtuální síť, postupujte takto:

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek** a vyhledejte *virtuální síť*. 
1. Vyberte možnost **Virtual Network** publikovanou Microsoftem a pak na další stránce vyberte **vytvořit** . 
1. Vyplňte požadovaná pole pro konfiguraci virtuální sítě pro sekundární spravovanou instanci a pak vyberte **vytvořit**. 

   Následující tabulka uvádí hodnoty nutné pro sekundární virtuální síť:

    | **Pole** | Value |
    | --- | --- |
    | **Název** |  Název virtuální sítě, která se má použít u sekundární spravované instance, například `vnet-sql-mi-secondary`. |
    | **Adresní prostor** | Adresní prostor pro virtuální síť, například `10.128.0.0/16`. | 
    | **Předplatné** | Předplatné, ve kterém se nachází vaše primární spravovaná instance a skupina prostředků |
    | **Oblast** | Umístění, do kterého budete nasazovat sekundární spravovanou instanci. |
    | **Podsíť** | Název vaší podsítě. `default`je ve výchozím nastavení k dispozici pro vás. |
    | **Rozsah adres**| Rozsah adres pro vaši podsíť. Musí se lišit od rozsahu adres podsítě používaného virtuální sítí vaší primární spravované instance, například `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Hodnoty sekundární virtuální sítě](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3\. vytvoření sekundární spravované instance
V tomto kroku vytvoříte sekundární spravovanou instanci v Azure Portal, která bude také konfigurovat síť mezi dvěma spravovanými instancemi. 

Vaše druhá spravovaná instance musí:
- Být prázdné. 
- Mít jinou podsíť a rozsah IP adres než primární spravovaná instance. 

K vytvoření sekundární spravované instance použijte následující postup: 

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici databáze vyberete Zobrazit podrobnosti.
1. Na dlaždici **spravované instance SQL** vyberte **vytvořit** . 

    ![Vybrat spravovanou instanci](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na kartě **základy** na stránce **vytvořit Azure SQL Database spravované instance** vyplňte požadovaná pole pro konfiguraci sekundární spravované instance. 

   Následující tabulka uvádí hodnoty nutné pro sekundární spravovanou instanci:
 
    | **Pole** | Value |
    | --- | --- |
    | **Předplatné** |  Předplatné, ve kterém je vaše primární spravovaná instance. |
    | **Skupina prostředků**| Skupina prostředků, ve které je vaše primární spravovaná instance. |
    | **Název spravované instance** | Název vaší nové sekundární spravované instance, například`sql-mi-secondary`  | 
    | **Oblast**| Umístění sekundární spravované instance.  |
    | **Přihlašovací jméno správce spravované instance** | Přihlašovací jméno, které chcete použít pro novou sekundární spravovanou instanci, například `azureuser`. |
    | **Heslo** | Složitá hesla, která budou používána přihlášením správce pro novou sekundární spravovanou instanci.  |
    | &nbsp; | &nbsp; |

1. Na kartě **sítě** pro **Virtual Network**v rozevíracím seznamu vyberte virtuální síť, kterou jste vytvořili pro sekundární spravovanou instanci.

   ![Sekundární síť MI](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Na kartě **Další nastavení** pro geografickou **replikaci**vyberte **Ano** , aby se _použil jako sekundární převzetí služeb při selhání_. Z rozevíracího seznamu vyberte primární spravovanou instanci. 
    1. Ujistěte se, že kolace a časová zóna odpovídají hodnotě primární spravované instance. Primární spravovaná instance vytvořená v tomto kurzu použila výchozí `SQL_Latin1_General_CP1_CI_AS` řazení `(UTC) Coordinated Universal Time` a časové pásmo. 

   ![Sekundární síť MI](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte nastavení sekundární spravované instance. 
1. Vyberte **vytvořit** a vytvořte svou sekundární spravovanou instanci. 


## <a name="4---create-primary-virtual-network-gateway"></a>4 – vytvoření primární brány virtuální sítě 

Aby se dvě spravované instance účastnily skupiny převzetí služeb při selhání, musí existovat brána nakonfigurovaná mezi virtuálními sítěmi dvou spravovaných instancí, aby bylo možné síťovou komunikaci. Bránu pro primární spravovanou instanci můžete vytvořit pomocí Azure Portal:

1. V [Azure Portal](https://portal.azure.com)přejdete do skupiny prostředků a vyberete prostředek **virtuální sítě** pro vaši primární spravovanou instanci. 
1. V části **Nastavení** vyberte **podsítě** a pak vyberte, pokud chcete přidat novou **podsíť brány**. Ponechte výchozí hodnoty. 

   ![Přidat bránu pro primární spravovanou instanci](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Po vytvoření brány podsítě v levém navigačním podokně vyberte **vytvořit prostředek** a potom do vyhledávacího pole zadejte `Virtual network gateway` . Vyberte prostředek **brány virtuální sítě** publikovaný Microsoftem. 

   ![Vytvořit novou bránu virtuální sítě](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Vyplňte požadovaná pole a nakonfigurujte bránu vaší primární spravované instance. 

   Následující tabulka uvádí hodnoty nutné pro bránu pro primární spravovanou instanci:
 
    | **Pole** | Value |
    | --- | --- |
    | **Předplatné** |  Předplatné, ve kterém je vaše primární spravovaná instance. |
    | **Název** | Název brány virtuální sítě, například `primary-mi-gateway`. | 
    | **Oblast** | Oblast, ve které je vaše sekundární spravovaná instance. |
    | **Typ brány** | Vyberte **VPN**. |
    | **Typ sítě VPN** | Vybrat **na základě trasy** |
    | **SKU**| Ponechte výchozí `VpnGw1`hodnotu. |
    | **Location**| Umístění, kde je vaše primární spravovaná instance a primární virtuální síť.   |
    | **Virtuální síť**| Vyberte virtuální síť, která byla vytvořena v části 2, například `vnet-sql-mi-primary`. |
    | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
    | **Název veřejné IP adresy**| Zadejte název vaší IP adresy, například `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |
1. Ostatní hodnoty ponechte jako výchozí a potom vyberte **zkontrolovat + vytvořit** a zkontrolujte nastavení brány virtuální sítě.

   ![Nastavení primární brány](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Vyberte **vytvořit** a vytvořte novou bránu virtuální sítě. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5 – konfigurace brány sekundární virtuální sítě 

Zopakováním kroků v předchozí části vytvořte podsíť virtuální sítě a bránu pro sekundární spravovanou instanci. Vyplňte požadovaná pole a nakonfigurujte bránu pro sekundární spravovanou instanci. 

   V následující tabulce jsou uvedeny hodnoty nutné pro bránu sekundární spravované instance:

   | **Pole** | Value |
   | --- | --- |
   | **Předplatné** |  Předplatné, ve kterém je vaše sekundární spravovaná instance. |
   | **Název** | Název brány virtuální sítě, například `secondary-mi-gateway`. | 
   | **Oblast** | Oblast, ve které je vaše sekundární spravovaná instance. |
   | **Typ brány** | Vyberte **VPN**. |
   | **Typ sítě VPN** | Vybrat **na základě trasy** |
   | **SKU**| Ponechte výchozí `VpnGw1`hodnotu. |
   | **Location**| Umístění, kde je sekundární spravovaná instance a sekundární virtuální síť.   |
   | **Virtuální síť**| Vyberte virtuální síť, která byla vytvořena v části 2, například `vnet-sql-mi-secondary`. |
   | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
   | **Název veřejné IP adresy**| Zadejte název vaší IP adresy, například `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Nastavení sekundární brány](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6\. připojení bran
V tomto kroku vytvoříte připojení mezi bránami. Připojení musí být navázáno z primární na sekundární bránu a pak musí být mezi sekundárním serverem a primární bránou navázáno samostatné připojení. Nezapomeňte stejný **sdílený klíč** použít při konfiguraci připojení mezi oběma branami. 

K nakonfigurování připojení použijte následující postup:

1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com) a vyberte primární bránu, kterou jste vytvořili v kroku 4. 
1. V části **Nastavení** vyberte **připojení** a pak vyberte **Přidat** . vytvoří se nové připojení. 

   ![Přidat připojení k primární bráně](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Zadejte název připojení, například `Primary-connection`a zadejte hodnotu pro **sdílený** `mi1mi2psk`klíč, například. 
1. Vyberte **druhou bránu virtuální sítě** a potom vyberte bránu pro sekundární spravovanou instanci, například `secondary-mi-gateway`. 

   ![Vytvořit primární pro sekundární připojení](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Pokud chcete přidat nové připojení k sekundární bráně, vyberte **OK** .
1. Zopakováním těchto kroků vytvoříte připojení z brány sekundární spravované instance k bráně primární spravované instance. 

   ![Vytvořit sekundární primární připojení](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7\. Vytvoření skupiny převzetí služeb při selhání
V tomto kroku vytvoříte skupinu převzetí služeb při selhání a přidáte do ní obě spravované instance. 

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte primární spravovanou instanci, kterou jste vytvořili v první části, například `sql-mi-primary`. 
1. V části **Nastavení**přejděte na **instance skupiny převzetí služeb při selhání** a pak zvolte **Přidat skupinu** a otevřete stránku **skupiny převzetí služeb při selhání instance** . 

   ![Přidat skupinu převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte název skupiny převzetí služeb při selhání, `failovergrouptutorial` například a pak zvolte sekundární spravovanou `sql-mi-secondary` instanci, například z rozevíracího seznamu. Vyberte **vytvořit** a vytvořte skupinu převzetí služeb při selhání. 

   ![Vytvořit skupinu převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Po dokončení nasazení skupiny převzetí služeb při selhání se vrátíte zpátky na stránku **skupiny převzetí služeb při selhání** . 

## <a name="8---test-failover"></a>8\. testování převzetí služeb při selhání
V tomto kroku dojde k selhání skupiny převzetí služeb při selhání pro sekundární server a následnému navrácení služeb po obnovení pomocí Azure Portal. 

1. Přejděte do spravované instance v rámci [Azure Portal](https://portal.azure.com) a v části nastavení vyberte **instance skupiny převzetí služeb při selhání** . 
1. Zkontrolujte, která spravovaná instance je primární a která spravovaná instance je sekundární. 
1. Vyberte **převzetí služeb při selhání** a pak pro upozornění na odpojené relace TDS vyberte **Ano** . 

   ![Převzít služby při selhání skupiny převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Zkontrolujte, která instance spravovaných je primární a která instance je sekundární. Pokud se převzetí služeb při selhání úspěšně zdařilo, měly by tyto dvě instance mít přepnuté role. 

   ![Spravované instance mají po převzetí služeb při selhání přepnuté role.](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Znovu vyberte **převzetí služeb při selhání** , aby se primární instance znovu nezdařila do primární role. 


## <a name="clean-up-resources"></a>Vyčištění prostředků
Vyčistěte prostředky tak, že nejprve odstraníte spravovanou instanci, potom virtuální cluster, potom všechny zbývající prostředky a nakonec skupinu prostředků. 

1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com). 
1. Vyberte spravovanou instanci a pak vyberte **Odstranit**. Do `yes` textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. Dokončení tohoto procesu může nějakou dobu trvat na pozadí a až do dokončení, nebudete moci odstranit *virtuální cluster* ani žádné jiné závislé prostředky. Sledujte odstranění na kartě aktivita a potvrďte, že se vaše spravovaná instance odstranila. 
1. Po odstranění spravované instance odstraňte *virtuální cluster* tak, že ho vyberete ve vaší skupině prostředků, a pak zvolíte **Odstranit**. Do `yes` textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. 
1. Odstraňte všechny zbývající prostředky. Do `yes` textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. 
1. Odstraňte skupinu prostředků výběrem možnosti **Odstranit skupinu prostředků**, zadáním názvu skupiny `myResourceGroup`prostředků a pak výběrem možnosti **Odstranit**. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali skupinu převzetí služeb při selhání mezi dvěma spravovanými instancemi. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření primární spravované instance
> - Vytvořte sekundární spravovanou instanci jako součást [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md). 
> - Testovací převzetí služeb při selhání

Přejděte k dalšímu rychlému startu, jak se připojit ke spravované instanci a jak obnovit databázi do spravované instance: 

> [!div class="nextstepaction"]
> [Připojení k spravované instanci](sql-database-managed-instance-configure-vm.md)
> [obnovení databáze do spravované instance](sql-database-managed-instance-get-started-restore.md)


