---
title: 'Kurz: předpoklady pro skupinu dostupnosti'
description: V tomto kurzu se dozvíte, jak nakonfigurovat předpoklady pro vytvoření skupiny dostupnosti Always On SQL Server v Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: ea9c8b91237f4590d1999c99fbb356d78994390d
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166892"
---
# <a name="tutorial-prerequisites-for-creating-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Kurz: předpoklady pro vytváření skupin dostupnosti v SQL Server v Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

V tomto kurzu se dozvíte, jak dokončit požadavky pro vytvoření [skupiny dostupnosti Always on SQL Server ve službě Azure Virtual Machines (VM)](availability-group-manually-configure-tutorial.md). Po dokončení požadovaných součástí budete mít řadič domény, dva SQL Server virtuální počítače a monitorovací server v jedné skupině prostředků.

I když tento článek konfiguruje prostředí skupiny dostupnosti ručně, je možné to provést také pomocí [Azure Portal](availability-group-azure-portal-configure.md), [PowerShellu nebo Azure CLI](availability-group-az-commandline-configure.md)nebo [šablon Azure pro rychlý Start](availability-group-quickstart-template-configure.md) . 

**Časový odhad**: dokončení požadavků může trvat několik hodin. Mnohé z těchto časů stráví vytváření virtuálních počítačů.

Následující diagram znázorňuje, co sestavíte v tomto kurzu.

![Skupina dostupnosti](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Zkontrolovat dokumentaci ke skupině dostupnosti

V tomto kurzu se předpokládá, že máte základní znalosti skupin dostupnosti Always On SQL Server. Pokud tuto technologii neznáte, přečtěte si téma [Přehled skupin dostupnosti Always On (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Vytvoření účtu Azure

Potřebujete mít účet Azure. Můžete si [otevřít bezplatný účet Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) nebo [aktivovat výhody pro předplatitele sady Visual Studio](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Tuto možnost vyberte **+** , pokud chcete vytvořit nový objekt na portálu.

   ![Nový objekt](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. V okně hledání na **Marketplace** zadejte **skupinu prostředků** .

   ![Skupina prostředků](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)

4. Vyberte **skupinu prostředků**.
5. Vyberte **Vytvořit**.
6. V části **název skupiny prostředků**zadejte název skupiny prostředků. Zadejte například příkaz **SQL-ha-RG**.
7. Pokud máte více předplatných Azure, ověřte, že předplatné je předplatné Azure, ve kterém chcete vytvořit skupinu dostupnosti.
8. Vyberte umístění. Umístění je oblast Azure, ve které chcete vytvořit skupinu dostupnosti. Tento článek vytvoří všechny prostředky v jednom umístění Azure.
9. Ověřte, že je zaškrtnuté políčko **Připnout na řídicí panel** . Toto volitelné nastavení umístí zástupce pro skupinu prostředků na řídicím panelu Azure Portal.

   ![Zástupce skupiny prostředků pro Azure Portal](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. Vyberte **vytvořit** a vytvořte skupinu prostředků.

Azure vytvoří skupinu prostředků a PIN zástupce pro skupinu prostředků na portálu.

## <a name="create-the-network-and-subnets"></a>Vytvoření sítě a podsítí

V dalším kroku vytvoříte sítě a podsítě ve skupině prostředků Azure.

Řešení používá jednu virtuální síť se dvěma podsítěmi. [Přehled služby Virtual Network](../../../virtual-network/virtual-networks-overview.md) poskytuje další informace o sítích v Azure.

Vytvoření virtuální sítě v Azure Portal:

1. Ve vaší skupině prostředků vyberte **+ Přidat**. 

   ![Nová položka](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. Vyhledejte **virtuální síť**.

     ![Hledat virtuální síť](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. Vyberte **virtuální síť**.
4. Ve **virtuální síti**vyberte model nasazení **Správce prostředků** a pak vyberte **vytvořit**.

    Následující tabulka ukazuje nastavení pro virtuální síť:

   | **Pole** | Hodnota |
   | --- | --- |
   | **Název** |autoHAVNET |
   | **Adresní prostor** |10.33.0.0/24 |
   | **Název podsítě** |Správce |
   | **Rozsah adres podsítě** |10.33.0.0/29 |
   | **Předplatné** |Zadejte předplatné, které chcete použít. Pokud máte pouze jedno předplatné, je **předplatné** prázdné. |
   | **Skupina prostředků** |Zvolte **použít existující** a vyberte název skupiny prostředků. |
   | **Umístění** |Zadejte umístění Azure. |

   Rozsah adresního prostoru a rozsahu adres podsítě se může lišit od tabulky. V závislosti na vašem předplatném navrhne portál dostupný adresní prostor a odpovídající rozsah adres podsítě. Pokud není k dispozici žádný dostatek adresního prostoru, použijte jiné předplatné.

   V příkladu se používá název podsítě **správce**. Tato podsíť je určena pro řadiče domény.

5. Vyberte **Vytvořit**.

   ![Konfigurace virtuální sítě](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

Azure vás vrátí na řídicí panel portálu a upozorní vás, když se vytvoří nová síť.

### <a name="create-a-second-subnet"></a>Vytvoření druhé podsítě

Nová virtuální síť má jednu podsíť s názvem **admin**. Řadiče domény používají tuto podsíť. Virtuální počítače s SQL Server používají druhou podsíť s názvem **SQL**. Konfigurace této podsítě:

1. Na řídicím panelu vyberte skupinu prostředků, kterou jste vytvořili, **SQL-ha-RG**. Vyhledejte síť ve skupině prostředků v části **prostředky**.

    Pokud není **SQL-ha-RG** vidět, Najděte si ho tak, že vyberete **skupiny prostředků** a filtrování podle názvu skupiny prostředků.

2. V seznamu prostředků vyberte **autoHAVNET** . 
3. Ve virtuální síti **autoHAVNET** v části **Nastavení** vyberte **podsítě**.

    Poznamenejte si podsíť, kterou jste už vytvořili.

   ![Poznamenejte si podsíť, kterou jste už vytvořili.](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)

5. Druhou podsíť vytvoříte tak, že vyberete **+ podsíť**.
6. V části **Přidat podsíť**nakonfigurujte podsíť zadáním **sqlsubnet** pod **názvem**. Azure automaticky určí platný **Rozsah adres**. Ověřte, zda je v tomto rozsahu adres alespoň 10 adres. V produkčním prostředí můžete potřebovat víc adres.
7. Vyberte **OK**.

    ![Konfigurace podsítě](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

Následující tabulka shrnuje nastavení konfigurace sítě:

| **Pole** | Hodnota |
| --- | --- |
| **Název** |**autoHAVNET** |
| **Adresní prostor** |Tato hodnota závisí na dostupných adresních prostorech v rámci vašeho předplatného. Typická hodnota je 10.0.0.0/16. |
| **Název podsítě** |**správce** |
| **Rozsah adres podsítě** |Tato hodnota závisí na dostupných rozsahech adres v rámci vašeho předplatného. Typická hodnota je 10.0.0.0/24. |
| **Název podsítě** |**sqlsubnet** |
| **Rozsah adres podsítě** |Tato hodnota závisí na dostupných rozsahech adres v rámci vašeho předplatného. Typickou hodnotou je 10.0.1.0/24. |
| **Předplatné** |Zadejte předplatné, které chcete použít. |
| **Skupina prostředků** |**SQL-HA – RG** |
| **Umístění** |Zadejte stejné umístění, které jste zvolili pro skupinu prostředků. |

## <a name="create-availability-sets"></a>Vytvoření skupin dostupnosti

Před vytvořením virtuálních počítačů je potřeba vytvořit skupiny dostupnosti. Skupiny dostupnosti omezují výpadky plánovaných nebo neplánovaných událostí údržby. Skupina dostupnosti Azure je logickou skupinou prostředků, které Azure umístí do fyzických domén selhání a aktualizačních domén. Doména selhání zajišťuje, že členové skupiny dostupnosti mají samostatné prostředky napájení a sítě. Aktualizační doména zajišťuje, že členové skupiny dostupnosti nejsou zavedeni pro údržbu ve stejnou dobu. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../../../virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Potřebujete dvě skupiny dostupnosti. Jedna je určena pro řadiče domény. Druhý je pro SQL Server virtuálních počítačů.

Skupinu dostupnosti vytvoříte tak, že přejdete do skupiny prostředků a vyberete **Přidat**. Vyfiltrujte výsledky zadáním **skupiny dostupnosti**. Ve výsledcích vyberte **skupinu dostupnosti** a pak vyberte **vytvořit**.

Nakonfigurujte dvě skupiny dostupnosti podle parametrů v následující tabulce:

| **Pole** | Skupina dostupnosti řadiče domény | SQL Server Skupina dostupnosti |
| --- | --- | --- |
| **Název** |adavailabilityset |sqlavailabilityset |
| **Skupina prostředků** |SQL-HA – RG |SQL-HA – RG |
| **Domény selhání** |3 |3 |
| **Aktualizační domény** |5 |3 |

Po vytvoření skupin dostupnosti se vraťte do skupiny prostředků v Azure Portal.

## <a name="create-domain-controllers"></a>Vytvoření řadičů domény

Po vytvoření sítě, podsítí a skupin dostupnosti jste připraveni vytvořit virtuální počítače pro řadiče domény.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Vytváření virtuálních počítačů pro řadiče domény

Řadiče domény vytvoříte a nakonfigurujete tak, že se vrátíte do skupiny prostředků **SQL-ha-RG** .

1. Vyberte **Přidat**. 
2. Zadejte **Windows Server 2016 Datacenter**.
3. Vyberte **Windows Server 2016 Datacenter**. V **systému Windows Server 2016 Datacenter**ověřte, zda je model nasazení **Správce prostředků**a pak vyberte **vytvořit**. 

Zopakováním předchozích kroků vytvořte dva virtuální počítače. Pojmenujte dva virtuální počítače:

* AD – primární – DC
* AD – sekundární – DC

  > [!NOTE]
  > Virtuální počítač **sekundárního řadiče domény** je nepovinný, aby se zajistila vysoká dostupnost pro Active Directory Domain Services.
  >

Následující tabulka uvádí nastavení těchto dvou počítačů:

| **Pole** | Hodnota |
| --- | --- |
| **Název** |První řadič domény: *AD-Primary-DC*.</br>Druhý řadič domény *služby AD-Secondary-DC*. |
| **Typ disku virtuálního počítače** |SSD |
| **Uživatelské jméno** |DomainAdmin |
| **Heslo** |Contoso! 0000 |
| **Předplatné** |*Vaše předplatné* |
| **Skupina prostředků** |SQL-HA – RG |
| **Umístění** |*Vaše umístění* |
| **Velikost** |DS1_V2 |
| **Storage** | **Použití spravovaných disků**  -  **Ano** |
| **Virtuální síť** |autoHAVNET |
| **Podsíť** |správce |
| **Veřejná IP adresa** |*Stejný název jako virtuální počítač* |
| **Skupina zabezpečení sítě** |*Stejný název jako virtuální počítač* |
| **Skupina dostupnosti** |adavailabilityset </br>**Domény selhání**: 2 </br>**Aktualizovat domény**: 2|
| **Diagnostika** |Povoleno |
| **Účet úložiště diagnostiky** |*Automaticky vytvořeno* |

   >[!IMPORTANT]
   >Virtuální počítač můžete umístit do skupiny dostupnosti jenom při jeho vytváření. Po vytvoření virtuálního počítače už skupinu dostupnosti nemůžete změnit. Viz [Správa dostupnosti virtuálních počítačů](../../../virtual-machines/linux/manage-availability.md).

Azure vytvoří virtuální počítače.

Po vytvoření virtuálních počítačů Nakonfigurujte řadič domény.

### <a name="configure-the-domain-controller"></a>Konfigurace řadiče domény

V následujících krocích nakonfigurujte počítač **AD-Primary-DC** jako řadič domény pro Corp.contoso.com.

1. Na portálu otevřete skupinu prostředků **SQL-ha-RG** a vyberte počítač **AD-Primary-DC** . V **AD-Primary-DC**vyberte **připojit** a otevřete soubor RDP pro přístup ke vzdálené ploše.

    ![Připojení k virtuálnímu počítači](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)

2. Přihlaste se pomocí nakonfigurovaného účtu správce (**\DomainAdmin**) a hesla (**Contoso! 0000**).
3. Ve výchozím nastavení by se měl zobrazit řídicí panel **Správce serveru** .
4. Na řídicím panelu vyberte odkaz **Přidat role a funkce** .

    ![Správce serveru – přidání rolí](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

5. Vyberte **Další** , dokud se nedostanete do části **role serveru** .
6. Vyberte role serveru **Active Directory Domain Services** a **DNS** . Po zobrazení výzvy přidejte všechny další funkce, které tyto role vyžadují.

   > [!NOTE]
   > Systém Windows vás upozorní, že není k dispozici žádná statická IP adresa. Pokud testujete konfiguraci, vyberte **pokračovat**. V produkčních scénářích nastavte IP adresu na hodnotu Static ve Azure Portal, nebo [pomocí PowerShellu nastavte STATICKOU IP adresu počítače řadiče domény](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >

    ![Dialogové okno Přidat role](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)

7. Vyberte **Další** , dokud se nedostanete do části **potvrzení** . V **případě potřeby zaškrtněte políčko automaticky restartovat cílový server** .
8. Vyberte **Nainstalovat**.
9. Po dokončení instalace funkcí se vraťte na řídicí panel **Správce serveru** .
10. V levém podokně vyberte možnost Nová **Služba AD DS** .
11. Na žlutém výstražném panelu vyberte odkaz **Další** .

    ![Dialog služba AD DS na virtuálním počítači serveru DNS](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
    
12. Ve sloupci **Akce** v dialogu **Podrobnosti úlohy serveru** vyberte možnost **povýšit tento server na řadič domény**.
13. V **Průvodci konfigurací Active Directory Domain Services**použijte následující hodnoty:

    | **Stránka** | Nastavení |
    | --- | --- |
    | **Konfigurace nasazení** |**Přidat novou doménovou strukturu**<br/> **Název kořenové domény** = Corp.contoso.com |
    | **Možnosti řadiče domény** |**DSRM Password** = contoso! 0000<br/>**Potvrzení hesla** = contoso! 0000 |

14. Kliknutím na tlačítko **Další** přejdete na ostatní stránky v průvodci. Na stránce **Kontrola předpokladů** ověřte, že se zobrazí následující zpráva: **všechny kontroly požadovaných součástí byly úspěšně úspěšné**. Můžete si prohlédnout jakékoli použitelné varovné zprávy, ale je možné pokračovat v instalaci.
15. Vyberte **Nainstalovat**. Virtuální počítač **AD-Primary-DC** se automaticky restartuje.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Poznamenejte si IP adresu primárního řadiče domény.

Použijte primární řadič domény pro DNS. Poznamenejte si IP adresu primárního řadiče domény.

Jedním ze způsobů, jak získat IP adresu primárního řadiče domény, je prostřednictvím Azure Portal.

1. V Azure Portal otevřete skupinu prostředků.

2. Vyberte primární řadič domény.

3. V primárním řadiči domény vyberte **Síťová rozhraní**.

![Síťová rozhraní](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

Poznamenejte si privátní IP adresu tohoto serveru.

### <a name="configure-the-virtual-network-dns"></a>Konfigurace DNS virtuální sítě

Po vytvoření prvního řadiče domény a povolení DNS na prvním serveru nakonfigurujte virtuální síť tak, aby používala tento server pro DNS.

1. V Azure Portal vyberte ve virtuální síti.

2. V části **Nastavení**vyberte **Server DNS**.

3. Vyberte **vlastní**a zadejte privátní IP adresu primárního řadiče domény.

4. Vyberte **Uložit**.

### <a name="configure-the-second-domain-controller"></a>Konfigurace druhého řadiče domény

Po restartování primárního řadiče domény můžete nakonfigurovat druhý řadič domény. Tento volitelný krok je určen pro vysokou dostupnost. Pomocí těchto kroků nakonfigurujete druhý řadič domény:

1. Na portálu otevřete skupinu prostředků **SQL-ha-RG** a vyberte počítač **AD-Secondary-DC** . V **AD-Secondary-DC**vyberte **připojit** a otevřete soubor RDP pro přístup ke vzdálené ploše.
2. Přihlaste se k virtuálnímu počítači pomocí nakonfigurovaného účtu správce (**BUILTIN\DomainAdmin**) a hesla (**Contoso! 0000**).
3. Změňte upřednostňovanou adresu serveru DNS na adresu řadiče domény.
4. V **Centru síťových připojení a sdílení**vyberte síťové rozhraní.

   ![Síťové rozhraní](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. Vyberte **Vlastnosti**.
6. Vyberte **Internet Protocol verze 4 (TCP/IPv4)** a pak vyberte **vlastnosti**.
7. Vyberte **použít následující adresy serverů DNS** a pak zadejte adresu primárního řadiče domény v **upřednostňovaném serveru DNS**.
8. Vyberte **OK**a pak kliknutím na **Zavřít** potvrďte změny. Nyní se můžete připojit k virtuálnímu počítači a **Corp.contoso.com**.

   >[!IMPORTANT]
   >Pokud po změně nastavení DNS ztratíte připojení ke vzdálené ploše, přečtěte si Azure Portal a restartujte virtuální počítač.

9. Z vzdálené plochy na sekundární řadič domény otevřete **Správce serveru řídicí panel**.
10. Na řídicím panelu vyberte odkaz **Přidat role a funkce** .

    ![Správce serveru – přidání rolí](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. Vyberte **Další** , dokud se nedostanete do části **role serveru** .
12. Vyberte role serveru **Active Directory Domain Services** a **DNS** . Po zobrazení výzvy přidejte všechny další funkce, které tyto role vyžadují.
13. Po dokončení instalace funkcí se vraťte na řídicí panel **Správce serveru** .
14. V levém podokně vyberte možnost Nová **Služba AD DS** .
15. Na žlutém výstražném panelu vyberte odkaz **Další** .
16. Ve sloupci **Akce** v dialogu **Podrobnosti úlohy serveru** vyberte možnost **povýšit tento server na řadič domény**.
17. V části **Konfigurace nasazení**vyberte **Přidat řadič domény do existující domény**.

    ![Konfigurace nasazení](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)

18. Klikněte na **Vybrat**.
19. Připojte se pomocí účtu správce (**Corp. CONTOSO. COM\domainadmin**) a heslo (**Contoso! 0000**).
20. V **části Vybrat doménu z doménové struktury**vyberte doménu a pak vyberte **OK**.
21. V **Možnosti řadič domény**použijte výchozí hodnoty a nastavte heslo DSRM.

    >[!NOTE]
    >Na stránce **Možnosti služby DNS** se může zobrazit upozornění, že delegování pro tento server DNS nelze vytvořit. Toto upozornění můžete ignorovat i v neprodukčních prostředích.
    >

22. Vyberte **Další** , dokud dialogové okno nedosáhne kontroly **požadovaných součástí** . Pak vyberte **nainstalovat**.

Poté, co server dokončí změny konfigurace, restartujte server.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Přidejte privátní IP adresu k druhému řadiči domény na server DNS VPN.

V Azure Portal v části virtuální síť změňte server DNS tak, aby zahrnoval IP adresu sekundárního řadiče domény. Toto nastavení povoluje redundanci služby DNS.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a> Konfigurace doménových účtů

V dalších krocích nakonfigurujete účty služby Active Directory. Následující tabulka uvádí účty:

| |Účet instalace<br/> |SQLServer – 0 <br/>Účet služby SQL Server a SQL Agent |SQLServer – 1<br/>Účet služby SQL Server a SQL Agent
| --- | --- | --- | ---
|**Jméno** |Instalace |SQLSvc1 | SQLSvc2
|**SamAccountName uživatele** |Instalace |SQLSvc1 | SQLSvc2

Jednotlivé účty vytvoříte pomocí následujících kroků.

1. Přihlaste se k počítači **AD-Primary-DC** .
2. V **Správce serveru**vyberte **nástroje**a pak vyberte **Centrum správy služby Active Directory**.   
3. V levém podokně vyberte **Corp (místní)** .
4. V podokně pravé **úlohy** vyberte **Nový**a pak vyberte **uživatel**.

   ![Centrum správy služby Active Directory](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >Pro každý účet nastavte složitá hesla.<br/> V případě neprodukčních prostředí nastavte uživatelský účet na nikdy nevyprší platnost.
   >

5. Vyberte **OK** a vytvořte uživatele.
6. Předchozí kroky opakujte pro každý ze tří účtů.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Udělte účtu instalace požadovaná oprávnění.

1. V **Centrum správy služby Active Directory**v levém podokně vyberte **Corp (místní)** . Pak v podokně **úlohy** na pravé straně vyberte **vlastnosti**.

    ![Vlastnosti uživatele CORP](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)

2. Vyberte **rozšíření**a pak na kartě **zabezpečení** vyberte tlačítko **Upřesnit** .
3. V dialogovém okně **Upřesnit nastavení zabezpečení pro Corp** vyberte **Přidat**.
4. Klikněte na **Vybrat objekt zabezpečení**, vyhledejte **CORP\Install**a pak vyberte **OK**.
5. Zaškrtněte políčko **číst všechny vlastnosti** .

6. Zaškrtněte políčko **vytvořit objekty počítačů** .

     ![Uživatelská oprávnění Corp](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)

7. Vyberte **OK** a potom znovu vyberte **OK**. Zavřete okno vlastnosti **Corp** .

Teď, když jste dokončili konfiguraci služby Active Directory a objektů uživatele, vytvořte dva SQL Server virtuální počítače a virtuální počítač s monitorovacím serverem. Pak připojte všechny tři k doméně.

## <a name="create-sql-server-vms"></a>Vytvoření virtuálních počítačů s SQL Server

Vytvořte tři další virtuální počítače. Řešení vyžaduje dva virtuální počítače s instancemi SQL Server. Třetí virtuální počítač bude fungovat jako určující. Systém Windows Server 2016 může používat [disk s kopií cloudu](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness). V souladu s předchozími operačními systémy ale v tomto článku se používá virtuální počítač pro určující disk.  

Než budete pokračovat, zvažte následující rozhodnutí o návrhu.

* **Storage – Azure Managed Disks**

   Pro úložiště virtuálního počítače použijte Azure Managed Disks. Microsoft doporučuje Managed Disks pro SQL Server virtuálních počítačů. Spravované disky se starají o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se Spravovanými disky ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v tématu [Přehled Spravovaných disků Azure](../../../virtual-machines/managed-disks-overview.md). Konkrétní informace o spravovaných discích ve skupině dostupnosti najdete v tématu [použití Managed disks pro virtuální počítače ve skupině dostupnosti](../../../virtual-machines/linux/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Privátní IP adresy sítě v produkčním prostředí**

   V tomto kurzu se pro virtuální počítače používají veřejné IP adresy. Veřejná IP adresa umožňuje vzdálené připojení přímo k virtuálnímu počítači přes Internet a usnadňuje postup konfigurace. V produkčních prostředích doporučuje společnost Microsoft pouze privátní IP adresy, aby bylo možné snížit nároky na ohrožení zabezpečení prostředku virtuálního počítače SQL Server instance.

### <a name="create-and-configure-the-sql-server-vms"></a>Vytvoření a konfigurace SQL Serverch virtuálních počítačů

Dále vytvořte tři virtuální počítače – dva SQL Server virtuální počítače a jeden virtuální počítač pro další uzel clusteru. Pro vytvoření každého virtuálního počítače se vraťte do skupiny prostředků **SQL-ha-RG** a pak vyberte **Přidat**. Vyhledejte příslušnou položku galerie, vyberte **virtuální počítač**a potom vyberte **z Galerie**. Informace v následující tabulce vám pomůžou při vytváření virtuálních počítačů:


| Stránka | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Vyberte příslušnou položku galerie. |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise v systému Windows Server 2016** |**SQL Server 2016 SP1 Enterprise v systému Windows Server 2016** |
| **Základy** konfigurace virtuálních počítačů |**Název** = cluster – FSW<br/>**Uživatelské jméno** = DomainAdmin<br/>**Password** = contoso! 0000<br/>**Předplatné** = vaše předplatné<br/>**Skupina prostředků** = SQL-ha-RG<br/>**Location** = umístění Azure |**Název** = SQLServer-0<br/>**Uživatelské jméno** = DomainAdmin<br/>**Password** = contoso! 0000<br/>**Předplatné** = vaše předplatné<br/>**Skupina prostředků** = SQL-ha-RG<br/>**Location** = umístění Azure |**Název** = SQLServer-1<br/>**Uživatelské jméno** = DomainAdmin<br/>**Password** = contoso! 0000<br/>**Předplatné** = vaše předplatné<br/>**Skupina prostředků** = SQL-ha-RG<br/>**Location** = umístění Azure |
| **Velikost** konfigurace virtuálního počítače |**Velikost** = DS1 \_ v2 (1 VCPU, 3,5 GB) |**Size** = DS2 \_ v2 (2 VCPU, 7 GB)</br>Velikost musí podporovat úložiště SSD (podpora disků úrovně Premium. )) |**Size** = DS2 \_ v2 (2 VCPU, 7 GB) |
| **Nastavení** konfigurace virtuálního počítače |**Storage**: použijte spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet (10.1.1.0/24)<br/>**Veřejná IP adresa** se automaticky vygenerovala.<br/>**Skupina zabezpečení sítě** = žádné<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít automaticky generovaný účet úložiště<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |**Storage**: použijte spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet (10.1.1.0/24)<br/>**Veřejná IP adresa** se automaticky vygenerovala.<br/>**Skupina zabezpečení sítě** = žádné<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít automaticky generovaný účet úložiště<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |**Storage**: použijte spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet (10.1.1.0/24)<br/>**Veřejná IP adresa** se automaticky vygenerovala.<br/>**Skupina zabezpečení sítě** = žádné<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít automaticky generovaný účet úložiště<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |
| **Nastavení SQL Server** konfigurace virtuálního počítače |Nelze použít |**Připojení SQL** = privátní (v rámci Virtual Network)<br/>**Port** = 1433<br/>**Ověřování SQL** = zakázat<br/>**Konfigurace úložiště** = obecné<br/>**Automatizované opravy** = neděle v 2:00<br/>**Automatizované zálohování** = zakázáno</br>**Azure Key Vault Integration** = disabled |**Připojení SQL** = privátní (v rámci Virtual Network)<br/>**Port** = 1433<br/>**Ověřování SQL** = zakázat<br/>**Konfigurace úložiště** = obecné<br/>**Automatizované opravy** = neděle v 2:00<br/>**Automatizované zálohování** = zakázáno</br>**Azure Key Vault Integration** = disabled |

<br/>

> [!NOTE]
> Doporučené velikosti počítačů jsou určené pro testování skupin dostupnosti v Azure Virtual Machines. Nejlepšího výkonu pro produkční úlohy najdete v tématu doporučení pro SQL Server velikosti počítačů a konfiguraci v tématu [osvědčené postupy pro SQL Server ve službě Azure Virtual Machines](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

Po úplném zřízení těchto tří virtuálních počítačů je budete muset připojit k doméně **Corp.contoso.com** a udělit počítačům CORP\Install práva správce.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Připojte servery k doméně.

Nyní se můžete připojit k virtuálním počítačům a **Corp.contoso.com**. Proveďte následující kroky pro SQL Server virtuální počítače a monitorovací server sdílené složky:

1. Vzdáleně se připojte k virtuálnímu počítači přes **BUILTIN\DomainAdmin**.
2. V **Správce serveru**vyberte **místní server**.
3. Vyberte odkaz **pracovní skupina** .
4. V části **název počítače** vyberte **změnit**.
5. Zaškrtněte políčko **doména** a do textového pole zadejte **Corp.contoso.com** . Vyberte **OK**.
6. V místním okně **zabezpečení systému Windows** zadejte pověření pro výchozí účet správce domény (**CORP\DomainAdmin**) a heslo (**Contoso! 0000**).
7. Až se zobrazí zpráva "Vítejte ve corp.contoso.com doméně", vyberte **OK**.
8. Vyberte **Zavřít**a pak v automaticky otevřeném okně vyberte **restartovat** .

## <a name="add-accounts"></a>Přidání účtů

Přidejte účet instalace jako správce na každý virtuální počítač, udělte účtu instalace a místním účtům v rámci služby SQL Server oprávnění a aktualizujte účet služby SQL Server. 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Přidejte uživatele Corp\Install jako správce na každý virtuální počítač clusteru.

Po restartování každého virtuálního počítače jako člena domény přidejte **CORP\Install** jako člena místní skupiny Administrators.

1. Počkejte, až se virtuální počítač restartuje, a pak znovu spusťte soubor RDP z primárního řadiče domény, abyste se přihlásili k **SQLServer-0** pomocí účtu **CORP\DomainAdmin** .

   >[!TIP]
   >Ujistěte se, že se přihlašujete pomocí účtu správce domény. V předchozích krocích jste používali integrovaný účet správce. Teď, když je server v doméně, použijte účet domény. V relaci RDP zadejte *DOMAIN* \\ *uživatelské jméno*domény.
   >

2. V **Správce serveru**vyberte **nástroje**a pak vyberte **Správa počítače**.
3. V okně **Správa počítače** rozbalte **místní uživatelé a skupiny**a pak vyberte **skupiny**.
4. Dvakrát klikněte na skupinu **Administrators** .
5. V dialogovém okně **Vlastnosti Správce** vyberte tlačítko **Přidat** .
6. Zadejte **CORP\Install**uživatele a pak vyberte **OK**.
7. Kliknutím na **tlačítko OK** zavřete dialogové okno **Vlastnosti Správce** .
8. Opakujte předchozí kroky na **SQLServer-1** a **cluster-FSW**.


### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Vytvoření přihlášení na každém virtuálním počítači s SQL Server pro účet instalace

Pro konfiguraci skupiny dostupnosti použijte účet instalace (CORP\install). Tento účet musí být členem pevné role serveru **sysadmin** na každém SQL SERVERm virtuálním počítači. Následující postup vytvoří přihlášení k instalačnímu účtu:

1. Připojte se k serveru přes protokol RDP (Remote Desktop Protocol) (RDP) pomocí účtu * \<MachineName\> \DomainAdmin* .

1. Otevřete SQL Server Management Studio a připojte se k místní instanci SQL Server.

1. V **Průzkumník objektů**vyberte **zabezpečení**.

1. Klikněte pravým tlačítkem na **přihlašovací údaje**. Vyberte **nové přihlášení**.

1. V **přihlašování – nové**vyberte **Hledat**.

1. Vyberte **umístění**.

1. Zadejte přihlašovací údaje pro síť správce domény.

1. Použijte účet instalace (CORP\install).

1. Nastavte přihlášení jako člen pevné role serveru **sysadmin** .

1. Vyberte **OK**.

Předchozí kroky opakujte na druhém virtuálním počítači s SQL Server.

### <a name="configure-system-account-permissions"></a>Konfigurace oprávnění systémového účtu

Chcete-li vytvořit účet pro systémový účet a udělit příslušná oprávnění, proveďte následující kroky u každé instance SQL Server:

1. Vytvořte účet pro `[NT AUTHORITY\SYSTEM]` každou instanci SQL Server. Tento účet vytvoří následující skript:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Pro `[NT AUTHORITY\SYSTEM]` každou instanci SQL Server udělte následující oprávnění:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Následující skript udělí tato oprávnění:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Nastavit účty služby SQL Server

Na každém virtuálním počítači s SQL Server nastavte účet služby SQL Server. Použijte účty, které jste vytvořili při konfiguraci doménových účtů.

1. Otevřete nástroj **SQL Server Configuration Manager**.
2. Pravým tlačítkem myši klikněte na službu SQL Server a pak vyberte **vlastnosti**.
3. Nastavte účet a heslo.
4. Opakujte tyto kroky na jiném virtuálním počítači SQL Server.  

U SQL Server skupin dostupnosti musí být každý SQL Server virtuální počítač spuštěný jako doménový účet.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Přidání funkcí clusteringu s podporou převzetí služeb při selhání do obou SQL Serverch virtuálních počítačů

Chcete-li přidat funkce clusteringu s podporou převzetí služeb při selhání, proveďte následující kroky na obou SQL Server virtuálních počítačích:

1. Připojte se k virtuálnímu počítači s SQL Server přes protokol RDP (Remote Desktop Protocol) (RDP) pomocí účtu *CORP\install* . Otevřete **řídicí panel Správce serveru**.
2. Na řídicím panelu vyberte odkaz **Přidat role a funkce** .

    ![Správce serveru – přidání rolí](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

3. Vyberte **Další** , dokud se nedostanete do oddílu **funkce serveru** .
4. V **Možnosti funkce**vyberte **Clustering s podporou převzetí služeb při selhání**.
5. Přidejte další požadované funkce.
6. Vyberte **nainstalovat** a přidejte funkce.

Opakujte postup na jiném SQL Serverovém virtuálním počítači.

  >[!NOTE]
  > Tento krok, společně se skutečným připojením k SQL Server virtuálním počítačům do clusteru s podporou převzetí služeb při selhání, se teď dá automatizovat pomocí [Azure SQL VM CLI](availability-group-az-cli-configure.md) a [šablon Azure pro rychlý Start](availability-group-quickstart-template-configure.md).
  >


## <a name="configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"></a> Konfigurace brány firewall na každém virtuálním počítači s SQL Server

Řešení vyžaduje, aby v bráně firewall byly otevřené následující porty TCP:

- **SQL Server virtuální počítač**: port 1433 pro výchozí instanci SQL Server.
- **Test nástroje pro vyrovnávání zatížení Azure:** Libovolný dostupný port. Příklady často používají 59999.
- **Koncový bod zrcadlení databáze:** Libovolný dostupný port. Příklady často používají 5022.

Porty brány firewall musí být otevřené na obou SQL Server virtuálních počítačích.

Způsob otevření portů závisí na použitém řešení brány firewall. V další části se dozvíte, jak otevřít porty v bráně Windows Firewall. Otevřete požadované porty na každém z vašich SQL Server virtuálních počítačů.

### <a name="open-a-tcp-port-in-the-firewall"></a>Otevření portu TCP v bráně firewall

1. Na první obrazovce SQL Server **Start** otevřete **bránu Windows Firewall s pokročilým zabezpečením**.
2. V levém podokně vyberte **příchozí pravidla**. V pravém podokně vyberte **nové pravidlo**.
3. Jako **Typ pravidla**vyberte **port**.
4. Pro port zadejte **TCP** a zadejte odpovídající čísla portů. Prohlédněte si následující příklad:

   ![Brána firewall SQL](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. Vyberte **Další**.
6. Na stránce **Akce** ponechte vybrané **připojení** a pak vyberte **Další**.
7. Na stránce **profil** přijměte výchozí nastavení a pak vyberte **Další**.
8. Na stránce **název** zadejte do textového pole **název** název pravidla (třeba **Azure dissonde**) a pak vyberte **Dokončit**.

Opakujte tyto kroky na druhém SQL Serverm virtuálním počítači.


## <a name="next-steps"></a>Další kroky

* [Vytvoření skupiny dostupnosti Always On SQL Server v Azure Virtual Machines](availability-group-manually-configure-tutorial.md)
