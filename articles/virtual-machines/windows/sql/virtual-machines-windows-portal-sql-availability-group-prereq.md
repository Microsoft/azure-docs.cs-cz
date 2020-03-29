---
title: 'Kurz: Předpoklady pro skupinu dostupnosti'
description: Tento kurz ukazuje, jak nakonfigurovat požadavky pro vytvoření sql server vždy na dostupnost skupiny na virtuálních počítačích Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: c80a4c07e0649612b4c024cac79833c4b730f55e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060172"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Požadavky pro vytváření skupin dostupnosti always on na SQL Serveru na virtuálních počítačích Azure

Tento kurz ukazuje, jak dokončit požadavky pro vytvoření [sql server vždy na dostupnost skupiny na virtuálních počítačích Azure (VMs)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Po dokončení požadavků máte řadič domény, dva virtuální servery SQL Server a server s kopií clusteru v jedné skupině prostředků.

**Časový odhad**: Dokončení předpokladů může trvat několik hodin. Velká část tohoto času se stráví vytvářením virtuálních počítačů.

Následující diagram znázorňuje, co vytvoříte v kurzu.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Kontrola dokumentace skupiny dostupnosti

Tento kurz předpokládá, že máte základní znalosti sql server vždy na skupiny dostupnosti. Pokud nejste obeznámeni s touto technologií, naleznete [v tématu Přehled vždy na skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Vytvoření účtu Azure
Potřebujete mít účet Azure. Můžete [si otevřít bezplatný účet Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) nebo [aktivovat výhody předplatitele Visual Studia](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Klepnutím **+** vytvoříte nový objekt na portálu.

   ![Nový objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Do vyhledávacího okna **Marketplace** zadejte **skupinu prostředků.**

   ![Skupina prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Klepněte na **položku Skupina prostředků**.
5. Klikněte na **Vytvořit**.
6. V části **Název skupiny prostředků**zadejte název skupiny prostředků. Zadejte například **sql-ha-rg**.
7. Pokud máte více předplatných Azure, ověřte, že předplatné je předplatné Azure, ve kterém chcete vytvořit skupinu dostupnosti.
8. Vyberte umístění. Umístění je oblast Azure, kde chcete vytvořit skupinu dostupnosti. Tento článek vytvoří všechny prostředky v jednom umístění Azure.
9. Ověřte, zda je zaškrtnuto **políčko Připnout k řídicímu panelu.** Toto volitelné nastavení umístí zástupce pro skupinu prostředků na řídicím panelu portálu Azure.

   ![Skupina prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Kliknutím na **Vytvořit** skupinu prostředků vytvořte.

Azure vytvoří skupinu prostředků a připne zástupce skupiny prostředků na portálu.

## <a name="create-the-network-and-subnets"></a>Vytvoření sítě a podsítí
Dalším krokem je vytvoření sítí a podsítí ve skupině prostředků Azure.

Řešení používá jednu virtuální síť se dvěma podsítěmi. [Přehled virtuální sítě](../../../virtual-network/virtual-networks-overview.md) poskytuje další informace o sítích v Azure.

Vytvoření virtuální sítě:

1. Na webu Azure Portal klikněte ve skupině prostředků na **+ Přidat**. 

   ![Nová položka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Vyhledejte **virtuální síť**.

     ![Hledat ve virtuální síti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Klepněte na **položku Virtuální síť**.
4. Ve **virtuální síti**klikněte na model nasazení **Správce prostředků** a potom klikněte na **vytvořit**.

    V následující tabulce jsou uvedena nastavení pro virtuální síť:

   | **Pole** | Hodnota |
   | --- | --- |
   | **Název** |autoHAVNET |
   | **Adresní prostor** |10.33.0.0/24 |
   | **Název podsítě** |Správce |
   | **Rozsah adres podsítě** |10.33.0.0/29 |
   | **Předplatné** |Zadejte předplatné, které chcete použít. **Předplatné** je prázdné, pokud máte pouze jedno předplatné. |
   | **Skupina prostředků** |Zvolte **Použít existující** a vyberte název skupiny prostředků. |
   | **Umístění** |Zadejte umístění Azure. |

   Rozsah adresního prostoru a podsítě se může lišit od tabulky. V závislosti na vašem předplatném portál navrhne dostupný adresní prostor a odpovídající rozsah adres podsítě. Pokud není k dispozici dostatek adresního prostoru, použijte jiné předplatné.

   Příklad používá název podsítě **Admin**. Tato podsíť je určenpro řadiče domény.

5. Klikněte na **Vytvořit**.

   ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure vás vrátí na řídicí panel portálu a upozorní vás při vytvoření nové sítě.

### <a name="create-a-second-subnet"></a>Vytvoření druhé podsítě
Nová virtuální síť má jednu podsíť s názvem **Správce**. Řadiče domény používají tuto podsíť. Virtuální server YSQL Server používá druhou podsíť s názvem **SQL**. Postup konfigurace této podsítě:

1. Na řídicím panelu klikněte na skupinu prostředků, kterou jste vytvořili, **SQL-HA-RG**. Vyhledejte síť ve skupině prostředků v části **Prostředky**.

    Pokud **SQL-HA-RG** není viditelný, najděte ji kliknutím na **skupiny prostředků** a filtrování podle názvu skupiny prostředků.
2. V seznamu zdrojů klepněte na **položku autoHAVNET.** 
3. Ve virtuální síti **autoHAVNET** vyberte v části **Nastavení** **podsítě**.

    Poznamenejte si podsíť, kterou jste již vytvořili.

   ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Vytvořte druhou podsíť. Klepněte na **položku + Podsíť**.
6. V **podsíti Přidat**nakonfigurujte podsíť zadáním **příkazu sqlsubnet** v části **Název**. Azure automaticky určuje platný **rozsah adres**. Ověřte, zda v tomto rozsahu adres je alespoň 10 adres. V provozním prostředí může vyžadovat více adres.
7. Klikněte na tlačítko **OK**.

    ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

Následující tabulka shrnuje nastavení konfigurace sítě:

| **Pole** | Hodnota |
| --- | --- |
| **Název** |**autoHAVNET** |
| **Adresní prostor** |Tato hodnota závisí na dostupných adresových prostorech v předplatném. Typická hodnota je 10.0.0.0/16. |
| **Název podsítě** |**správce** |
| **Rozsah adres podsítě** |Tato hodnota závisí na dostupných rozsahech adres v rámci předplatného. Typická hodnota je 10.0.0.0/24. |
| **Název podsítě** |**sqlpodnet** |
| **Rozsah adres podsítě** |Tato hodnota závisí na dostupných rozsahech adres v rámci předplatného. Typická hodnota je 10.0.1.0/24. |
| **Předplatné** |Zadejte předplatné, které chcete použít. |
| **Skupina prostředků** |**SQL-HA-RG** |
| **Umístění** |Zadejte stejné umístění, které jste zvolili pro skupinu prostředků. |

## <a name="create-availability-sets"></a>Vytvoření skupin dostupnosti

Před vytvořením virtuálních počítačů je třeba vytvořit skupiny dostupnosti. Dostupné sady zkracují prostoje plánovaných nebo neplánovaných událostí údržby. Sada dostupnosti Azure je logická skupina prostředků, které Azure umístí na domény fyzické poruchy a aktualizační domény. Doména selhání zajišťuje, že členové skupiny dostupnosti mají samostatné napájecí a síťové prostředky. Aktualizační doména zajišťuje, že členové skupiny dostupnosti nejsou současně znevažováni z důvodu údržby. Další informace najdete [v tématu Správa dostupnosti virtuálních počítačů](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Potřebujete dvě skupiny dostupnosti. Jeden je pro řadiče domény. Druhý je pro virtuální server SQL Server.

Chcete-li vytvořit sadu dostupnosti, přejděte do skupiny prostředků a klepněte na tlačítko **Přidat**. Výsledky můžete filtrovat zadáním **skupiny dostupnosti**. Ve výsledcích klikněte na **Položku Dostupnost** a potom klikněte na **příkaz Vytvořit**.

Nakonfigurujte dvě sady dostupnosti podle parametrů v následující tabulce:

| **Pole** | Sada dostupnosti řadiče domény | Sada dostupnosti serveru SQL Server |
| --- | --- | --- |
| **Název** |sada adavailabilityset |sqlavailabilityset |
| **Skupina prostředků** |SQL-HA-RG |SQL-HA-RG |
| **Domény selhání** |3 |3 |
| **Aktualizační domény** |5 |3 |

Po vytvoření sad dostupnosti se vraťte do skupiny prostředků na webu Azure Portal.

## <a name="create-domain-controllers"></a>Vytvoření řadičů domény
Po vytvoření sítě, podsítí a sad dostupnosti jste připraveni vytvořit virtuální počítače pro řadiče domény.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Vytvoření virtuálních počítačů pro řadiče domény
Chcete-li vytvořit a nakonfigurovat řadiče domény, vraťte se do skupiny prostředků **SQL-HA-RG.**

1. Klikněte na **Přidat**. 
2. Zadejte **Windows Server 2016 Datacenter**.
3. Klikněte na **Windows Server 2016 Datacenter**. V **datovém centru Windows Server 2016**ověřte, zda je model nasazení **Správce prostředků**, a klepněte na tlačítko **Vytvořit**. 

Opakováním předchozích kroků vytvořte dva virtuální počítače. Pojmenujte dva virtuální počítače:

* ad-primary-dc
* ad-sekundární-dc

  > [!NOTE]
  > Virtuální počítač **ad secondary-dc** je volitelný, aby poskytoval vysokou dostupnost pro službu Active Directory Domain Services.
  >
  >

V následující tabulce jsou uvedena nastavení pro tyto dva počítače:

| **Pole** | Hodnota |
| --- | --- |
| **Název** |První řadič domény: *ad-primary-dc*.</br>Druhý řadič domény *ad-secondary-dc*. |
| **Typ disku virtuálního počítače** |SSD |
| **Uživatelské jméno** |DomainAdmin |
| **Heslo** |Contoso!0000 |
| **Předplatné** |*Vaše předplatné* |
| **Skupina prostředků** |SQL-HA-RG |
| **Umístění** |*Vaše poloha* |
| **Velikost** |DS1_V2 |
| **Úložiště** | **Použití spravovaných disků** - **Ano** |
| **Virtuální síť** |autoHAVNET |
| **Podsíť** |admin |
| **Veřejná IP adresa** |*Stejný název jako virtuální hod* |
| **Skupina zabezpečení sítě** |*Stejný název jako virtuální hod* |
| **Sada dostupnosti** |sada adavailabilityset </br>**Domény selhání**:2 </br>**Aktualizovat domény**:2|
| **Diagnostika** |Povoleno |
| **Účet úložiště diagnostiky** |*Automaticky vytvořeno* |

   >[!IMPORTANT]
   >Virtuální ho lze umístit jenom do sady dostupnosti při jeho vytvoření. Po vytvoření virtuálního virtuálního montovny nelze změnit sadu dostupnosti. Viz [Správa dostupnosti virtuálních počítačů](../manage-availability.md).

Azure vytváří virtuální počítače.

Po vytvoření virtuálních počítačů nakonfigurujte řadič domény.

### <a name="configure-the-domain-controller"></a>Konfigurace řadiče domény
V následujících krocích nakonfigurujte počítač **ad-primary-dc** jako řadič domény pro corp.contoso.com.

1. Na portálu otevřete skupinu prostředků **SQL-HA-RG** a vyberte počítač **ad-primary-dc.** V **aplikaci ad-primary-dc**klepnutím na **tlačítko Připojit** otevřete soubor RDP pro přístup ke vzdálené ploše.

    ![Připojení k virtuálnímu počítači](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Přihlaste se pomocí nakonfigurovaného účtu správce (**\DomainAdmin**) a hesla (**Contoso!0000**).
3. Ve výchozím nastavení by měl být zobrazen řídicí panel **Správce serveru.**
4. Klikněte na odkaz **Přidat role a funkce** na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Vyberte **Další,** dokud se nedostanete do oddílu **Role serveru.**
6. Vyberte role **služby Active Directory Domain Services** a serveru **DNS.** Po zobrazení výzvy přidejte další funkce, které tyto role vyžadují.

   > [!NOTE]
   > Systém Windows vás varuje, že neexistuje žádná statická IP adresa. Pokud testujete konfiguraci, klepněte na tlačítko **Pokračovat**. Pro produkční scénáře nastavte IP adresu na statický na webu Azure Portal nebo [pomocí Prostředí PowerShell nastavte statickou IP adresu počítače řadiče domény](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Dialogové okno Přidat role](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klikněte na **Další,** dokud se nedostanete do sekce **Potvrzení.** V případě potřeby zaškrtněte políčko **Restartovat cílový server automaticky.**
8. Klepněte na tlačítko **Instalovat**.
9. Po dokončení instalace se vraťte na řídicí panel **Správce serveru.**
10. V levém podokně vyberte novou možnost **ad ds.**
11. Klikněte na odkaz **Další** na žlutém výstražném pruhu.

    ![Dialogové okno Služby AD DS na virtuálním počítači dns serveru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Ve sloupci **Akce** v dialogovém okně **Všechny podrobnosti úkolu serveru** klepněte na tlačítko **Povýšit tento server na řadič domény**.
13. V **Průvodci konfigurací služby Active Directory Domain Services**použijte následující hodnoty:

    | **Stránka** | Nastavení |
    | --- | --- |
    | **Konfigurace nasazení** |**Přidání nové doménové struktury**<br/> **Název kořenové domény** = corp.contoso.com |
    | **Možnosti řadiče domény** |**Heslo služby DSRM** = Contoso!0000<br/>**Potvrdit heslo** = Contoso!0000 |
14. Klepnutím na tlačítko **Další** přejdete na další stránky průvodce. Na stránce **Kontrola požadavků** ověřte, zda se zobrazí následující zpráva: **Všechny kontroly předpokladů byly úspěšně předány**. Můžete zkontrolovat všechny příslušné varovné zprávy, ale je možné pokračovat v instalaci.
15. Klepněte na tlačítko **Instalovat**. Virtuální počítač **ad-primary-dc** se automaticky restartuje.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Poznamenejte si ip adresu primárního řadiče domény.

Použijte primární řadič domény pro službu DNS. Poznamenejte si adresu IP primárního řadiče domény.

Jedním ze způsobů, jak získat IP adresu primárního řadiče domény, je prostřednictvím portálu Azure.

1. Na webu Azure Portal otevřete skupinu prostředků.

2. Klikněte na primární řadič domény.

3. Na primárním řadiči domény klepněte na **položku Síťová rozhraní**.

![Síťová rozhraní](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Poznamenejte si privátní adresu IP tohoto serveru.

### <a name="configure-the-virtual-network-dns"></a>Konfigurace dns virtuální sítě
Po vytvoření prvního řadiče domény a povolení služby DNS na prvním serveru nakonfigurujte virtuální síť tak, aby tento server používala pro službu DNS.

1. Na webu Azure Portal klikněte na virtuální síť.

2. V části **Nastavení**klepněte na **položku DNS Server**.

3. Klepněte na **tlačítko Vlastní**a zadejte privátní ADRESU IP primárního řadiče domény.

4. Klikněte na **Uložit**.

### <a name="configure-the-second-domain-controller"></a>Konfigurace druhého řadiče domény
Po restartování primárního řadiče domény můžete nakonfigurovat druhý řadič domény. Tento volitelný krok je určen pro vysokou dostupnost. Chcete-li nakonfigurovat druhý řadič domény, postupujte takto:

1. Na portálu otevřete skupinu prostředků **SQL-HA-RG** a vyberte počítač **ad-secondary-dc.** V **sekundárním řadiči domény**klepněte na tlačítko **Připojit** a otevřete soubor RDP pro přístup ke vzdálené ploše.
2. Přihlaste se k virtuálnímu počítači pomocí nakonfigurovaného účtu správce (**BUILTIN\DomainAdmin**) a hesla (**Contoso!0000**).
3. Změňte upřednostňovanou adresu serveru DNS na adresu řadiče domény.
4. V **Centru síťových připojení a sdílení**klepněte na síťové rozhraní.
   ![Síťové rozhraní](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Klikněte na **Vlastnosti**.
6. Vyberte **možnost Internet Protocol verze 4 (TCP/IPv4)** a klepněte na příkaz **Vlastnosti**.
7. Vyberte **Použít následující adresy serverů DNS** a zadejte adresu primárního řadiče domény v **upřednostňovaném serveru DNS**.
8. Klepněte na tlačítko **OK**a potom **zavřete,** chcete-li potvrdit změny. Teď se můžete připojit k virtuálnímu virtuálnímu mněmu k **corp.contoso.com**.

   >[!IMPORTANT]
   >Pokud po změně nastavení DNS ztratíte připojení ke vzdálené ploše, přejděte na portál Azure a restartujte virtuální počítač.

9. Od vzdálené plochy k sekundárnímu řadiči domény otevřete **řídicí panel Správce serveru**.
10. Klikněte na odkaz **Přidat role a funkce** na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Vyberte **Další,** dokud se nedostanete do oddílu **Role serveru.**
12. Vyberte role **služby Active Directory Domain Services** a serveru **DNS.** Po zobrazení výzvy přidejte další funkce, které tyto role vyžadují.
13. Po dokončení instalace se vraťte na řídicí panel **Správce serveru.**
14. V levém podokně vyberte novou možnost **ad ds.**
15. Klikněte na odkaz **Další** na žlutém výstražném pruhu.
16. Ve sloupci **Akce** v dialogovém okně **Všechny podrobnosti úkolu serveru** klepněte na tlačítko **Povýšit tento server na řadič domény**.
17. V části **Konfigurace nasazení**vyberte Přidat řadič domény do **existující domény**.
    ![Konfigurace nasazení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Klepněte na **tlačítko Vybrat**.
19. Připojte se pomocí účtu správce (**CORP. CONTOSO.COM\domainadmin**) a heslo (**Contoso!0000**).
20. V **části Vyberte doménu z doménové struktury**klikněte na doménu a potom klikněte na **OK**.
21. V **okně Možnosti řadiče domény**použijte výchozí hodnoty a nastavte heslo služby DSRM.

    >[!NOTE]
    >Stránka **Možnosti DNS** vás může upozornit, že delegování tohoto serveru DNS nelze vytvořit. Toto upozornění můžete ignorovat v neprodukčních prostředích.
22. Klepněte na **tlačítko Další,** dokud dialogové okno nedosáhne **kontroly Požadavky.** Pak klikněte na **Nainstalovat**.

Po dokončení změn konfigurace serveru restartujte server.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Přidání privátní adresy IP do druhého řadiče domény na server DNS sítě VPN

Na webu Azure Portal v části virtuální síť změňte DNS server tak, aby zahrnoval IP adresu sekundárního řadiče domény. Toto nastavení umožňuje redundanci služby DNS.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>Konfigurace účtů domény

V dalších krocích nakonfigurujete účty služby Active Directory. V následující tabulce jsou uvedeny účty:

| |Instalační účet<br/> |sqlserver-0 <br/>Účet služby SQL Server a agenta SQL |sqlserver-1<br/>Účet služby SQL Server a agenta SQL
| --- | --- | --- | ---
|**Jméno** |Instalace |SQLSvc1 | SQLSvc2
|**Jméno uživatele SamAccountName** |Instalace |SQLSvc1 | SQLSvc2

K vytvoření každého účtu použijte následující kroky.

1. Přihlaste se k počítači **ad-primary-dc.**
2. Ve **Správci serveru**vyberte **nástroje**a klepněte na položku Centrum **správy služby Active Directory**.   
3. V levém podokně vyberte **položku corp (local).**
4. V pravém podokně **Úkoly** vyberte **Nový**a klepněte na tlačítko **Uživatel**.
   ![Centrum správy služby Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Nastavte složité heslo pro každý účet.<br/> V neprodukčních prostředích nastavte, aby nikdy nevypršela platnost uživatelského účtu.

5. Chcete-li vytvořit uživatele, klepněte na tlačítko **OK.**
6. Opakujte předchozí kroky pro každý ze tří účtů.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Udělení požadovaných oprávnění instalačnímu účtu
1. V **Centru správy služby Active Directory**vyberte v levém podokně **položku Corp (local).** Potom v pravém podokně **Úkoly** klikněte na **vlastnosti**.

    ![Vlastnosti uživatele CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Vyberte **Rozšíření**a potom klepněte na tlačítko **Upřesnit** na kartě **Zabezpečení.**
3. V dialogovém **okně Upřesnit nastavení zabezpečení pro korpus** klepněte na tlačítko **Přidat**.
4. Klepněte na **tlačítko Vybrat objekt zabezpečení**, vyhledejte **položku CORP\Install**a klepněte na tlačítko **OK**.
5. Zaškrtněte políčko **Číst všechny vlastnosti.**

6. Zaškrtněte políčko **Vytvořit objekty počítače.**

     ![Oprávnění uživatelů corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Klepněte na tlačítko **OK**a potom znovu klepněte na **tlačítko OK.** Zavřete okno vlastností **korpusu.**

Teď, když jste dokončili konfiguraci služby Active Directory a uživatelských objektů, vytvořte dva virtuální servery SQL Server a virtuální server s kopií clusteru. Pak připojte všechny tři do domény.

## <a name="create-sql-server-vms"></a>Vytvoření virtuálních měn SQL Server

Vytvořte tři další virtuální počítače. Řešení vyžaduje dva virtuální počítače s instancemi serveru SQL Server. Třetí virtuální počítač bude fungovat jako svědek. Windows Server 2016 můžete použít [cloud důkaz ,](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)ale pro konzistenci s předchozími operačními systémy tento dokument používá virtuální počítač pro důkaz.  

Než budete pokračovat, zvažte následující rozhodnutí o návrhu.

* **Úložiště – spravované disky Azure**

   Pro úložiště virtuálních počítačů použijte spravované disky Azure. Společnost Microsoft doporučuje spravované disky pro virtuální počítače sql serveru. Spravované disky se starají o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se Spravovanými disky ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v tématu [Přehled Spravovaných disků Azure](../managed-disks-overview.md). Podrobnosti o spravovaných discích v sadě dostupnosti najdete v tématu [Použití spravovaných disků pro virtuální](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)počítače v sadě dostupnosti .

* **Síť – privátní IP adresy v produkčním prostředí**

   Pro virtuální počítače tento kurz používá veřejné IP adresy. Veřejná IP adresa umožňuje vzdálené připojení přímo k virtuálnímu počítači přes internet - usnadňuje kroky konfigurace. V produkčním prostředí společnost Microsoft doporučuje pouze privátní IP adresy, aby se snížila závislost na chybě prostředku virtuálního počítače instance serveru SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Vytvoření a konfigurace virtuálních aplikací SQL Server
Dále vytvořte tři virtuální aplikace – dva virtuální chody SQL Serveru a virtuální hod pro další uzel clusteru. Chcete-li vytvořit každý z virtuálních počítačů, vraťte se do skupiny prostředků **SQL-HA-RG,** klepněte na tlačítko **Přidat**, vyhledejte příslušnou položku galerie, klepněte na **položku Virtuální počítač**a potom klepněte na **položku Z galerie**. Pomocí informací v následující tabulce můžete vytvořit virtuální chod:


| stránka | VM1 | VM2 | Virtuální měkce 3 |
| --- | --- | --- | --- |
| Výběr příslušné položky galerie |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise na Windows Serveru 2016** |**SQL Server 2016 SP1 Enterprise na Windows Serveru 2016** |
| Základy konfigurace **virtuálního** počítače |**Název** = cluster-fsw<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso!0000<br/>**Předplatné** = Vaše předplatné<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = Umístění azure |**Název** = sqlserver-0<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso!0000<br/>**Předplatné** = Vaše předplatné<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = Umístění azure |**Název** = sqlserver-1<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso!0000<br/>**Předplatné** = Vaše předplatné<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = Umístění azure |
| **Velikost** konfigurace virtuálního počítače |**VELIKOST** = DS1\_V2 (1 virtuální procesor, 3,5 GB) |**VELIKOST** = DS2\_V2 (2 virtuální procesory, 7 GB)</br>Velikost musí podporovat úložiště SSD (podpora disku Premium. )) |**VELIKOST** = DS2\_V2 (2 virtuální procesory, 7 GB) |
| **Nastavení** konfigurace virtuálního počítače |**Úložiště**: Použití spravovaných disků.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlpodnet(10.1.1.0/24)<br/>**Veřejná IP adresa** se automaticky vygeneruje.<br/>**Skupina zabezpečení sítě** = Žádná<br/>**Monitorování diagnostiky** = Povoleno<br/>**Účet úložiště diagnostiky** = Použití automaticky generovaného účtu úložiště<br/>**Dostupnost set** = sqlAvailabilitySet<br/> |**Úložiště**: Použití spravovaných disků.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlpodnet(10.1.1.0/24)<br/>**Veřejná IP adresa** se automaticky vygeneruje.<br/>**Skupina zabezpečení sítě** = Žádná<br/>**Monitorování diagnostiky** = Povoleno<br/>**Účet úložiště diagnostiky** = Použití automaticky generovaného účtu úložiště<br/>**Dostupnost set** = sqlAvailabilitySet<br/> |**Úložiště**: Použití spravovaných disků.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlpodnet(10.1.1.0/24)<br/>**Veřejná IP adresa** se automaticky vygeneruje.<br/>**Skupina zabezpečení sítě** = Žádná<br/>**Monitorování diagnostiky** = Povoleno<br/>**Účet úložiště diagnostiky** = Použití automaticky generovaného účtu úložiště<br/>**Dostupnost set** = sqlAvailabilitySet<br/> |
| Nastavení serveru **SQL Server** konfigurace virtuálního počítače |Neuvedeno |**Připojení SQL** = Soukromé (v rámci virtuální sítě)<br/>**Port** = 1433<br/>**Ověřování SQL** = Zakázat<br/>**Konfigurace úložiště** = Obecná<br/>**Automatické opravy** = neděle ve 2:00<br/>**Automatické zálohování** = Zakázáno</br>**Integrace úložiště klíčů Azure** = Zakázáno |**Připojení SQL** = Soukromé (v rámci virtuální sítě)<br/>**Port** = 1433<br/>**Ověřování SQL** = Zakázat<br/>**Konfigurace úložiště** = Obecná<br/>**Automatické opravy** = neděle ve 2:00<br/>**Automatické zálohování** = Zakázáno</br>**Integrace úložiště klíčů Azure** = Zakázáno |

<br/>

> [!NOTE]
> Velikosti počítače navržené tady jsou určené pro testování skupin dostupnosti ve virtuálních počítačích Azure. Nejlepší výkon na produkční úlohy, najdete v doporučení pro velikosti počítačů SQL Server a konfigurace v [doporučené postupy výkonu pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Po úplném zřízení tří virtuálních počítačů je nutné je připojit k **corp.contoso.com** doméně a udělit corp\install práva správce počítačům.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Připojení serverů k doméně

Teď se můžete připojit k virtuálním knám a **corp.contoso.com**. Proveďte následující kroky pro virtuální servery SQL Server a server s kopií clusteru pro sledování souborů:

1. Vzdáleně se připojte k virtuálnímu počítači pomocí **funkce BUILTIN\DomainAdmin**.
2. Ve **Správci serveru**klepněte na **položku Místní server**.
3. Klikněte na odkaz **PRACOVNÍ SKUPINA.**
4. V části **Název počítače** klepněte na tlačítko **Změnit**.
5. Zaškrtněte políčko **Doména** a do textového pole **zadejte corp.contoso.com.** Klikněte na tlačítko **OK**.
6. V místním okně **Zabezpečení systému Windows** zadejte pověření pro výchozí účet správce domény (**CORP\DomainAdmin**) a heslo (**Contoso!0000**).
7. Až se zobrazí zpráva Vítejte v corp.contoso.com doméně, klikněte na **OK**.
8. V místním dialogovém okně klepněte na **tlačítko Zavřít**a potom klepněte na tlačítko **Restartovat.**

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Přidání uživatele corp\install jako správce na každém virtuálním počítači clusteru

Po restartování každého virtuálního počítače jako člena domény přidejte **corp\install** jako člena místní skupiny administrators.

1. Počkejte, až se virtuální modul restartuje, a pak znovu spusťte soubor RDP z primárního řadiče domény a přihlaste se k **sqlserveru-0** pomocí účtu **CORP\DomainAdmin.**
   >[!TIP]
   >Ujistěte se, že se přihlásíte pomocí účtu správce domény. V předchozích krocích jste používali účet správce BUILT IN. Nyní, když je server v doméně, použijte účet domény. V relaci RDP zadejte*uživatelské jméno* *domény*\\.

2. Ve **Správci serveru**vyberte **položku Nástroje**a klepněte na tlačítko Správa **počítače**.
3. V okně **Správa počítače** rozbalte **položku Místní uživatelé a skupiny**a vyberte **položku Skupiny**.
4. Poklepejte na skupinu **Administrators.**
5. V dialogovém okně **Vlastnosti správců** klepněte na tlačítko **Přidat.**
6. Zadejte uživatele **CORP\Install**a klepněte na tlačítko **OK**.
7. Klepnutím na **tlačítko OK** zavřete dialogové okno **Vlastnosti správce.**
8. Opakujte předchozí kroky na **sqlserver-1** a **cluster-fsw**.

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Nastavení účtů služeb serveru SQL Server

Na každém virtuálním počítači serveru SQL Server nastavte účet služby SERVERU SQL Server. Použijte účty, které jste vytvořili při konfiguraci účtů domény.

1. Otevřete nástroj **SQL Server Configuration Manager**.
2. Klepněte pravým tlačítkem myši na službu SERVERU SQL Server a potom klepněte na příkaz **Vlastnosti**.
3. Nastavte účet a heslo.
4. Opakujte tyto kroky na jiném virtuálním počítači SQL Server.  

Pro skupiny dostupnosti serveru SQL Server musí každý virtuální modul SQL Server spustit jako účet domény.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Vytvoření přihlášení na každém virtuálním počítači serveru SQL Server pro účet instalace

Ke konfiguraci skupiny dostupnosti použijte instalační účet (CORP\install). Tento účet musí být členem **sysadmin** pevné role serveru na každém virtuálním počítači SQL Server. Následující kroky vytvoří přihlášení k instalačnímu účtu:

1. Připojte se k serveru prostřednictvím protokolu RDP (Remote Desktop Protocol) pomocí účtu * \<MachineName\>\DomainAdmin.*

1. Otevřete SQL Server Management Studio a připojte se k místní instanci serveru SQL Server.

1. V **Průzkumníkovi objektů**klepněte na tlačítko **Zabezpečení**.

1. Klepněte pravým tlačítkem myši na **položku Přihlášení**. Klepněte na tlačítko **Nové přihlášení**.

1. V **pole Přihlášení – nové**klepněte na tlačítko **Hledat**.

1. Klepněte na **položku Umístění**.

1. Zadejte síťová pověření správce domény.

1. Použijte instalační účet.

1. Nastavte přihlášení tak, aby bylo členem pevné role serveru **sysadmin.**

1. Klikněte na tlačítko **OK**.

Opakujte předchozí kroky na jiném virtuálním počítači serveru SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Přidání funkcí clusteringu s podporou převzetí služeb při selhání do obou virtuálních ms serveru SQL Server

Chcete-li přidat funkce clusteringu s podporou převzetí služeb při selhání, proveďte následující kroky na obou virtuálních počítačích SQL Server:

1. Připojte se k virtuálnímu počítači serveru SQL Server prostřednictvím protokolu RDP (Remote Desktop Protocol) pomocí účtu *CORP\install.* Otevřete **řídicí panel Správce serveru**.
2. Klikněte na odkaz **Přidat role a funkce** na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Vyberte **Další,** dokud se nedostanete do části **Funkce serveru.**
4. V **poobsahuje funkce**možnost **Clustering s podporou převzetí služeb při selhání**.
5. Přidejte další požadované funkce.
6. Kliknutím na **Instalovat** přidejte funkce.

Opakujte kroky na jiném virtuálním počítači SQL Server.

  >[!NOTE]
  > Tento krok spolu se skutečně připojením virtuálních počítačů SQL Server ke clusteru s podporou převzetí služeb při selhání lze nyní automatizovat pomocí [azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) a [azure quickstart templates](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">Konfigurace brány firewall pro každý virtuální počítač SQL Server

Řešení vyžaduje otevření následujících portů TCP v bráně firewall:

- **Virtuální modul SQL Server**:<br/>
   Port 1433 pro výchozí instanci serveru SQL Server.
- **Sonda pro vyrovnávání zatížení Azure:**<br/>
   Libovolný dostupný port. Příklady často používají 59999.
- **Koncový bod zrcadlení databáze:** <br/>
   Libovolný dostupný port. Příklady často používají 5022.

Porty brány firewall musí být otevřené na obou virtuálních počítačích SQL Server.

Způsob otevírání portů závisí na řešení brány firewall, které používáte. V další části je vysvětleno, jak otevřít porty v bráně Windows Firewall. Otevřete požadované porty na každém virtuálním počítači SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Otevření portu TCP v bráně firewall

1. Na první **úvodní** obrazovce serveru SQL Server spusťte **bránu Windows Firewall s pokročilým zabezpečením**.
2. V levém podokně vyberte **Příchozí pravidla**. V pravém podokně klepněte na tlačítko **Nové pravidlo**.
3. V **popřípadě Typ pravidla**zvolte **Port**.
4. Pro port zadejte **tcp** a zadejte příslušná čísla portů. Prohlédněte si následující příklad:

   ![Brána firewall SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Klikněte na **Další**.
6. Na stránce **Akce** ponechte **vybrané připojení povolit** a klepněte na tlačítko **Další**.
7. Na stránce **Profil** přijměte výchozí nastavení a klepněte na tlačítko **Další**.
8. Na stránce **Název** zadejte název pravidla (například **Azure LB Probe**) do textového pole **Název** a klikněte na **Dokončit**.

Opakujte tyto kroky na druhém virtuálním počítači SQL Server.

## <a name="configure-system-account-permissions"></a>Konfigurace oprávnění systémového účtu

Chcete-li vytvořit účet pro systémový účet a udělit příslušná oprávnění, proveďte následující kroky pro každou instanci serveru SQL Server:

1. Vytvořte účet `[NT AUTHORITY\SYSTEM]` pro každou instanci serveru SQL Server. Následující skript vytvoří tento účet:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Udělte pro `[NT AUTHORITY\SYSTEM]` každou instanci serveru SQL Server následující oprávnění:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Následující skript uděluje tato oprávnění:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Další kroky

* [Vytvoření skupiny dostupnosti SQL Serveru always on na virtuálních počítačích Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
