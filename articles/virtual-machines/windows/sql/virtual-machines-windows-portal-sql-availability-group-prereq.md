---
title: Skupiny dostupnosti SQL serveru – virtuální počítače Azure - Prereqs | Microsoft Docs
description: Tento kurz ukazuje, jak konfigurovat požadavky pro vytvoření skupiny dostupnosti SQL serveru Always On na virtuálních počítačích Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: f2a0af65af068f3a78a08e46e0e42caefd87d7b1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Splnit požadavky pro vytváření skupin dostupnosti Always On na virtuálních počítačích Azure

Tento kurz ukazuje, jak splnit požadavky pro vytváření [SQL serveru skupiny dostupnosti Always On na virtuálních počítačích Azure (VM)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Pokud jste dokončili požadavky, máte řadiče domény, dva virtuální počítače serveru SQL a monitorovací server v jedna skupina prostředků.

**Odhad času**: může trvat několik hodin, požadavky. Velká část této doby je stráví vytváření virtuálních počítačů.

Následující diagram znázorňuje, co vytvoříte v tomto kurzu.

![Skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Přečtěte si dokumentaci k skupiny dostupnosti

Tento kurz předpokládá, že máte základní znalosti o SQL serveru Always On skupiny dostupnosti. Pokud si nejste obeznámeni s touto technologií, přečtěte si téma [přehled o skupin dostupnosti Always On (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Vytvoření účtu Azure
Potřebujete mít účet Azure. Můžete [otevřít bezplatný účet Azure](/pricing/free-trial/?WT.mc_id=A261C142F) nebo [aktivovat výhody předplatitele Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
1. Přihlaste se k [portálu Azure](http://portal.azure.com).
2. Klikněte na tlačítko **+** k vytvoření nového objektu na portálu.

   ![Nový objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Typ **skupiny prostředků** v **Marketplace** okně vyhledávání.

   ![Skupina prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Klikněte na tlačítko **skupiny prostředků**.
5. Klikněte na možnost **Vytvořit**.
6. V části **název skupiny prostředků**, zadejte název pro skupinu prostředků. Můžete například zadat **sql-ha-rg**.
7. Pokud máte víc předplatných Azure, ověřte, že odběr je předplatné Azure, kterou chcete vytvořit skupiny dostupnosti v.
8. Vyberte umístění. Umístění je oblast Azure, kde chcete vytvořit skupinu dostupnosti. Tento článek vytvoří všechny prostředky v jednom místě, Azure.
9. Ověřte, že **připnout na řídicí panel** je zaškrtnuté. Toto volitelné nastavení umístí zástupce pro skupinu prostředků na řídicím panelu portálu Azure.

   ![Skupina prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Klikněte na tlačítko **vytvořit** vytvořte skupinu prostředků.

Azure vytvoří skupinu prostředků a kódy PIN zástupce do skupiny prostředků na portálu.

## <a name="create-the-network-and-subnets"></a>Vytvoření sítě a podsítě
Dalším krokem je vytvoření sítě a podsítě ve skupině prostředků Azure.

Toto řešení využívá jednu virtuální síť se dvěma podsítěmi. [Přehled virtuálních sítí](../../../virtual-network/virtual-networks-overview.md) poskytuje další informace o sítích v Azure.

Vytvoření virtuální sítě:

1. Na portálu Azure ve vaší skupině prostředků, klikněte na tlačítko **+ přidat**. 

   ![Nová položka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Vyhledejte **virtuální sítě**.

     ![Hledání virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Klikněte na tlačítko **virtuální síť**.
4. Na **virtuální síť**, klikněte na tlačítko **Resource Manager** modelu nasazení a pak klikněte na tlačítko **vytvořit**.

    Následující tabulka uvádí nastavení virtuální sítě:

   | **Pole** | Hodnota |
   | --- | --- |
   | **Název** |autoHAVNET |
   | **Adresní prostor** |10.33.0.0/24 |
   | **Název podsítě** |Správa |
   | **Rozsah adres podsítě** |10.33.0.0/29 |
   | **Předplatné** |Určete předplatné, který chcete použít. **Předplatné** je prázdná, pokud máte pouze jedno předplatné. |
   | **Skupina prostředků** |Zvolte **použít existující** a vyberte název skupiny prostředků. |
   | **Umístění** |Zadejte umístění v Azure. |

   Vaše adresa prostoru a podsítě rozsah adres se mohou lišit z tabulky. V závislosti na vaše předplatné navrhuje portálu k dispozici adresní prostor a odpovídající rozsah adres podsítě. Pokud je k dispozici žádné dostatečný Adresní prostor, použijte jiný odběr.

   V příkladu je název podsítě **správce**. Tato podsíť je pro řadiče domény.

5. Klikněte na možnost **Vytvořit**.

   ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure se vrátíte k řídicímu panelu portálu a upozorní vás při vytvoření nové sítě.

### <a name="create-a-second-subnet"></a>Vytvořte druhou podsíť
Nové virtuální sítě obsahuje jednu podsíť, s názvem **správce**. Řadiče domény použít tuto podsíť. Použití virtuálních počítačů serveru SQL druhá podsíť s názvem **SQL**. Konfigurace této podsítě:

1. Na řídicím panelu, klikněte na skupinu prostředků, kterou jste vytvořili, **SQL-HA-RG**. Vyhledejte ve skupině prostředků v síti **prostředky**.

    Pokud **SQL-HA-RG** není viditelná, klepněte na tlačítko **skupiny prostředků** a filtrování podle názvu skupiny prostředků.
2. Klikněte na tlačítko **autoHAVNET** v seznamu prostředků. 
3. Na **autoHAVNET** virtuální sítě, v části **nastavení** , klikněte na tlačítko **podsítě**.

    Všimněte si, podsítě, které jste už vytvořili.

   ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Vytvořte druhou podsíť. Klikněte na tlačítko **+ podsítě**.
6. Na **přidat podsíť**, nakonfigurujte podsíť zadáním **sqlsubnet** pod **název**. Azure automaticky určuje platná **rozsahu adres**. Ověřte, zda tento rozsah adres má minimálně 10 adresy v ní. V produkčním prostředí může vyžadovat další adresy.
7. Klikněte na **OK**.

    ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

Následující tabulka shrnuje nastavení konfigurace sítě:

| **Pole** | Hodnota |
| --- | --- |
| **Název** |**autoHAVNET** |
| **Adresní prostor** |Tato hodnota závisí na k dispozici adresní prostory v rámci vašeho předplatného. Typické hodnota je 10.0.0.0/16. |
| **Název podsítě** |**admin** |
| **Rozsah adres podsítě** |Tato hodnota závisí na rozsahů adres k dispozici v rámci vašeho předplatného. Typické hodnota je 10.0.0.0/24. |
| **Název podsítě** |**sqlsubnet** |
| **Rozsah adres podsítě** |Tato hodnota závisí na rozsahů adres k dispozici v rámci vašeho předplatného. Typické hodnota je 10.0.1.0/24. |
| **Předplatné** |Určete předplatné, který chcete použít. |
| **Skupina prostředků** |**SQL-HA-RG** |
| **Umístění** |Zadejte stejné umístění, které jste zvolili pro skupinu prostředků. |

## <a name="create-availability-sets"></a>Vytvoření skupin dostupnosti

Než vytvoříte virtuální počítače, budete muset vytvořit skupiny dostupnosti. Skupiny dostupnosti zkrátit dobu prostojů pro události plánované i neplánované údržby. Nastavení Azure dostupnosti je logické skupiny prostředků, které Azure umístí na fyzických domén selhání a aktualizace domény. Domény selhání zajistí, že členové skupiny dostupnosti samostatné napájení a síťové prostředky. Doména aktualizace zajistí, že členové skupiny dostupnosti nejsou snížila pro údržbu ve stejnou dobu. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Je třeba dvě skupiny dostupnosti. Jeden je pro řadiče domény. Druhá je pro virtuální počítače serveru SQL.

Pokud chcete vytvořit skupinu dostupnosti, přejděte do skupiny prostředků a klikněte na tlačítko **přidat**. Filtrování výsledků zadáním **skupinu dostupnosti**. Klikněte na tlačítko **sadu dostupnosti** výsledků a pak klikněte na **vytvořit**.

Konfigurace dvou sad dostupnosti podle parametry v následující tabulce:

| **Pole** | Skupina dostupnosti řadiče domény | Skupinu dostupnosti systému SQL Server |
| --- | --- | --- |
| **Název** |adavailabilityset |sqlavailabilityset |
| **Skupina prostředků** |SQL-HA-RG |SQL-HA-RG |
| **Domén selhání** |3 |3 |
| **Aktualizace domén** |5 |3 |

Po vytvoření skupiny dostupnosti, vraťte se ke skupině prostředků na portálu Azure.

## <a name="create-domain-controllers"></a>Vytvoření řadiče domény
Po vytvoření sítě, podsítě, skupiny dostupnosti a k straně Internetu pro vyrovnávání zatížení, budete připraveni k vytvoření virtuálních počítačů pro řadiče domény.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Vytváření virtuálních počítačů pro řadiče domény
Chcete-li vytvořit a nakonfigurovat řadiče domény, vraťte se k **SQL-HA-RG** skupinu prostředků.

1. Klikněte na tlačítko **Add** (Přidat). 
2. Typ **systému Windows Server 2016 Datacenter**.
3. Klikněte na tlačítko **systému Windows Server 2016 Datacenter**. V **Windows Server 2016 Datacenter**, ověřte, zda je model nasazení **Resource Manager**a potom klikněte na **vytvořit**. 

Opakujte předchozí kroky k vytvoření dvou virtuálních počítačů. Název dva virtuální počítače:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > **Řadiče domény ad sekundární** virtuální počítač je volitelné, pro zajištění vysoké dostupnosti pro Active Directory Domain Services.
  >
  >

Následující tabulka uvádí nastavení pro tyto dva počítače:

| **Pole** | Hodnota |
| --- | --- |
| **Název** |První řadič domény: *řadiče domény ad primární*.</br>Druhý řadič domény *řadiče domény ad sekundární*. |
| **Typ disku virtuálního počítače** |SSD |
| **Uživatelské jméno** |DomainAdmin |
| **Heslo** |Contoso!0000 |
| **Předplatné** |*Vaše předplatné* |
| **Skupina prostředků** |SQL-HA-RG |
| **Umístění** |*Vaše umístění* |
| **Velikost** |DS1_V2 |
| **Úložiště** | **Používat spravované disky** - **Ano** |
| **Virtuální síť** |autoHAVNET |
| **Podsíť** |správce |
| **Veřejná IP adresa** |*Stejný název jako virtuální počítač* |
| **Skupina zabezpečení sítě** |*Stejný název jako virtuální počítač* |
| **Sady dostupnosti.** |adavailabilityset </br>**Poruch domén**: 2</br>**Aktualizovat domén**: 2|
| **Diagnostika** |Povoleno |
| **Účet úložiště diagnostiky** |*Automaticky vytvoří* |

   >[!IMPORTANT]
   >Virtuální počítač můžete umístit pouze ve skupině dostupnosti nastavena při jeho vytvoření. Nelze změnit po vytvoření virtuálního počítače skupinu dostupnosti. V tématu [Správa dostupnosti virtuálních počítačů](../manage-availability.md).

Azure vytvoří virtuální počítače.

Po vytvoření virtuálních počítačů, konfigurace řadiče domény.

### <a name="configure-the-domain-controller"></a>Konfigurace řadiče domény
V následujících krocích konfigurace **řadiče domény ad primární** počítače jako řadič domény pro spolecnost.contoso.com.

1. Na portálu, otevřete **SQL-HA-RG** prostředku, skupiny a vyberte **řadiče domény ad primární** počítače. Na **řadiče domény ad primární**, klikněte na tlačítko **Connect** otevřete soubor RDP pro přístup ke vzdálené ploše.

    ![Připojit k virtuálnímu počítači](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Přihlaste se pomocí účtu správce nakonfigurované (**\DomainAdmin**) a heslo (**Contoso! 0000**).
3. Ve výchozím nastavení **správce serveru** měla by se zobrazit řídicí panel.
4. Klikněte **přidat role a funkce** odkaz na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Vyberte **Další** až na **role serveru** části.
6. Vyberte **Active Directory Domain Services** a **DNS Server** role. Když se zobrazí výzva, přidejte všechny další funkce, které jsou vyžadované tyto role.

   > [!NOTE]
   > Windows vás varuje, že neexistuje žádná statická IP adresa. Pokud testujete konfiguraci, klikněte na tlačítko **pokračovat**. U produkčních scénářích nastavit IP adresu v portálu Azure nebo [nastavit statickou IP adresu počítače řadiče domény pomocí prostředí PowerShell](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Role dialogové okno Přidání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klikněte na tlačítko **Další** dokud nepřejdete **potvrzení** části. Vyberte **cílový server automaticky restartovat, pokud je to nutné** zaškrtávací políčko.
8. Klikněte na **Nainstalovat**.
9. Po dokončení instalace funkce vraťte k **správce serveru** řídicího panelu.
10. Vyberte novou **služby AD DS** možnost v levém podokně.
11. Klikněte **Další** odkaz na žlutý pruh upozornění.

    ![Dialogové okno AD DS na virtuální počítač Server DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. V **akce** sloupec **všechny podrobnosti úlohy serveru** dialogové okno, klikněte na tlačítko **zvýšit úroveň tohoto serveru na řadič domény**.
13. V **Průvodce konfigurací služby domény služby Active Directory**, použijte následující hodnoty:

    | **Stránka** | Nastavení |
    | --- | --- |
    | **Konfigurace nasazení** |**Přidat novou doménovou strukturu**<br/> **Název kořenové domény** = corp.contoso.com |
    | **Možnosti řadiče domény** |**DSRM Password** = Contoso!0000<br/>**Potvrzení hesla** = Contoso! 0000 |
14. Klikněte na tlačítko **Další** projít dalších stránek v průvodci. Na **Kontrola předpokladů** ověřte, že, zobrazí se následující zpráva: **všech požadovaných součástí byly úspěšně zkontrolovány**. Můžete zkontrolovat všechny příslušné zprávy upozornění, ale je možné pokračovat v instalaci.
15. Klikněte na **Nainstalovat**. **Řadiče domény ad primární** virtuální počítač automaticky restartuje.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Poznamenejte si adresu IP primárního řadiče domény

Použijte primární řadič domény pro službu DNS. Všimněte si IP adresu řadiče domény.

Jeden způsob, jak získat IP adresu řadiče domény je prostřednictvím portálu Azure.

1. Na portálu Azure otevřete skupinu prostředků.

2. Klikněte na primárním řadiči domény.

3. Na primárním řadiči domény, klikněte na tlačítko **síťových rozhraní**.

![Síťová rozhraní](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Všimněte si, privátní IP adresu pro tento server.

### <a name="configure-the-virtual-network-dns"></a>Konfigurace virtuální sítě DNS
Když vytvoříte první řadič domény a povolit DNS na prvním serveru, nakonfigurujte virtuální sítě na používání tohoto serveru DNS.

1. Na portálu Azure klikněte na virtuální síť.

2. V části **nastavení**, klikněte na tlačítko **DNS Server**.

3. Klikněte na tlačítko **vlastní**a zadejte privátní IP adresu primárního řadiče domény.

4. Klikněte na **Uložit**.

### <a name="configure-the-second-domain-controller"></a>Konfigurace druhého řadiče domény
Po restartování počítače primárním řadiči domény, můžete nakonfigurovat druhého řadiče domény. Tento volitelný krok je pro vysokou dostupnost. Postupujte podle těchto kroků nakonfigurujete druhého řadiče domény:

1. Na portálu, otevřete **SQL-HA-RG** prostředku, skupiny a vyberte **řadiče domény ad sekundární** počítače. Na **řadiče domény ad sekundární**, klikněte na tlačítko **Connect** otevřete soubor RDP pro přístup ke vzdálené ploše.
2. Přihlaste se k virtuálnímu počítači pomocí účtu správce nakonfigurované (**BUILTIN\DomainAdmin**) a heslo (**Contoso! 0000**).
3. Změňte upřednostňovanou adresu serveru DNS na adresu řadiče domény.
4. V **Centrum sítí a sdílení**, klikněte na síťové rozhraní.
   ![Síťové rozhraní](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Klikněte na **Vlastnosti**.
6. Vyberte **Internet Protocol verze 4 (TCP/IPv4)** a klikněte na tlačítko **vlastnosti**.
7. Vyberte **použít následující adresy serverů DNS** a zadat adresu primárním řadiči domény v **upřednostňovaný server DNS**.
8. Klikněte na tlačítko **OK**a potom **Zavřít** k provedení změn. Nyní budete moci připojit k virtuálnímu počítači **corp.contoso.com**.

   >[!IMPORTANT]
   >Pokud ztratíte připojení k vzdálené ploše po změně nastavení DNS, přejděte na portál Azure a restartování virtuálního počítače.

9. Vzdálenou plochu sekundární řadič domény, otevřete **řídicí panel Správce serveru**.
10. Klikněte **přidat role a funkce** odkaz na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Vyberte **Další** až na **role serveru** části.
12. Vyberte **Active Directory Domain Services** a **DNS Server** role. Když se zobrazí výzva, přidejte všechny další funkce, které jsou vyžadované tyto role.
13. Po dokončení instalace funkce vraťte k **správce serveru** řídicího panelu.
14. Vyberte novou **služby AD DS** možnost v levém podokně.
15. Klikněte **Další** odkaz na žlutý pruh upozornění.
16. V **akce** sloupec **všechny podrobnosti úlohy serveru** dialogové okno, klikněte na tlačítko **zvýšit úroveň tohoto serveru na řadič domény**.
17. V části **konfigurace nasazení**, vyberte **přidat řadič domény do existující domény**.
   ![Konfigurace nasazení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Klikněte na **Vybrat**.
19. Připojit pomocí účtu správce (**CORP. CONTOSO.COM\domainadmin**) a heslo (**Contoso! 0000**).
20. V **vyberte doménu doménové struktuře**, klikněte na doménu a pak klikněte na tlačítko **OK**.
21. V **možnosti řadiče domény**, použijte výchozí hodnoty a nastavit heslo režimu obnovení adresářových služeb.

   >[!NOTE]
   >**Možnosti služby DNS** stránky může varovat, že nelze vytvořit delegování pro tento server DNS. Toto upozornění v testovacím prostředí můžete ignorovat.
22. Klikněte na tlačítko **Další** dokud nedosáhne dialogu **požadavky** zkontrolujte. Pak klikněte na **Nainstalovat**.

Jakmile server dokončí změny konfigurace, restartujte server.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Přidejte privátní IP adresa druhého řadiče domény na server DNS sítě VPN

Na portálu Azure v rámci virtuální sítě změňte obsahující IP adresu řadiče domény sekundární Server DNS. Toto nastavení umožňuje redundance služby DNS.

### <a name=DomainAccounts></a> Konfigurovat účty domény

V dalších krocích můžete nakonfigurovat účty služby Active Directory. Následující tabulka uvádí účty:

| |Účet instalace<br/> |sqlserver-0 <br/>Účet systému SQL Server a služba SQL Agent |sqlserver-1<br/>Účet systému SQL Server a služba SQL Agent
| --- | --- | --- | ---
|**Křestní jméno** |Instalace |SQLSvc1 | SQLSvc2
|**SamAccountName uživatele** |Instalace |SQLSvc1 | SQLSvc2

K vytvoření každého účtu použijte následující postup.

1. Přihlaste se k **řadiče domény ad primární** počítače.
2. V **správce serveru**, vyberte **nástroje**a potom klikněte na **Centrum správy služby Active Directory**.   
3. Vyberte **corp (místní)** v levém podokně.
4. Na pravé straně **úlohy** podokně, vyberte **nový**a potom klikněte na **uživatele**.
   ![Centrum správy služby Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Nastavte pro každý účet složité heslo.<br/> Pro nevýrobní prostředí nastavte uživatelský účet nikdy nevyprší.

5. Klikněte na tlačítko **OK** vytvořit uživateli.
6. Opakujte předchozí kroky pro každou z uvedených účtů.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Přidělení požadovaných oprávnění k účtu instalace
1. V **Centrum správy služby Active Directory**, vyberte **corp (místní)** v levém podokně. Poté v pravém **úlohy** podokně klikněte na tlačítko **vlastnosti**.

    ![Vlastnosti uživatele CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Vyberte **rozšíření**a klikněte **Upřesnit** na tlačítko **zabezpečení** kartě.
3. V **Upřesnit nastavení zabezpečení pro corp** dialogové okno, klikněte na tlačítko **přidat**.
4. Klikněte na tlačítko **vybrat objekt zabezpečení**, vyhledejte **CORP\Install**a potom klikněte na **OK**.
5. Vyberte **číst vlastnosti všech** zaškrtávací políčko.

6. Vyberte **vytvářet objekty počítačů** zaškrtávací políčko.

     ![Oprávnění uživatele Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Klikněte na tlačítko **OK**a potom klikněte na **OK** znovu. Zavřít **corp** vlastnosti – okno.

Teď, když jste dokončili konfigurace služby Active Directory a uživatelských objektů, vytvořte dva virtuální počítače SQL serveru a serveru s kopií clusteru virtuálních počítačů. Potom připojí všechny tři k doméně.

## <a name="create-sql-server-vms"></a>Vytvoření virtuálních počítačů serveru SQL

Vytvořte tři další virtuální počítače. Řešení vyžaduje dva virtuální počítače s instancí systému SQL Server. Třetí virtuální počítač bude fungovat jako určující. Můžete použít Windows Server 2016 [cloudu určující](http://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), ale tento dokument konzistenci předchozích operačních systémech používá virtuální počítač pro určující.  

Než budete pokračovat, zvažte následující rozhodnutí o návrhu.

* **Úložiště – spravované Azure disky**

   Pro úložiště virtuálního počítače použijte Azure spravované disky. Společnost Microsoft doporučuje spravované disky pro virtuální počítače systému SQL Server. Služba Managed Disks se stará o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se službou Managed Disks ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v tématu [Přehled služby Azure Managed Disks](../managed-disks-overview.md). Podrobnosti o spravovaných disků v nastavení dostupnosti, najdete v části [použijte spravované disky pro virtuální počítače v nastavení dostupnosti](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Síť – privátní IP adresy v produkčním prostředí**

   Pro virtuální počítače tento kurz používá veřejné IP adresy. Veřejná IP adresa umožňuje vzdálené připojení přímo k virtuálnímu počítači přes internet – umožňuje jednodušší kroky konfigurace. V produkčním prostředí společnost Microsoft doporučuje pouze privátních IP adres kvůli snížení nároků ohrožení zabezpečení instance serveru SQL Server prostředků virtuálního počítače.

### <a name="create-and-configure-the-sql-server-vms"></a>Vytvoření a konfigurace virtuálních počítačů serveru SQL
Dále vytvořte tři virtuální počítače – dva virtuální počítače serveru SQL a virtuálního počítače pro do dodatečného uzlu clusteru. Chcete-li vytvořit všech virtuálních počítačích, přejděte zpět na **SQL-HA-RG** skupinu prostředků, klikněte na tlačítko **přidat**, vyhledejte položku odpovídající galerie, klikněte na **virtuálního počítače**a potom klikněte na **z Galerie**. Použijte informace v následující tabulce vám pomohou vytvořit virtuální počítače:


| Stránka | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Vyberte položky odpovídající Galerie |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |
| Konfigurace virtuálního počítače **základy** |**Název** = fsw clusteru<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso! 0000<br/>**Předplatné** = vaše předplatné<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = vaše umístění azure |**Name** = sqlserver-0<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso! 0000<br/>**Předplatné** = vaše předplatné<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = vaše umístění azure |**Name** = sqlserver-1<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso! 0000<br/>**Předplatné** = vaše předplatné<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = vaše umístění azure |
| Konfigurace virtuálního počítače **velikost** |**VELIKOST** = DS1\_V2 (1 virtuální procesor, 3.5 GB) |**VELIKOST** = DS2\_V2 (2 Vcpu, 7 GB)</br>Velikost musí podporovat úložiště SSD (podpora disku Premium. )) |**VELIKOST** = DS2\_V2 (2 Vcpu, 7 GB) |
| Konfigurace virtuálního počítače **nastavení** |**Úložiště**: použijte spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet(10.1.1.0/24)<br/>**Veřejná IP adresa** automaticky generovaný.<br/>**Skupina zabezpečení sítě** = None<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít účet úložiště automaticky generované<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |**Úložiště**: použijte spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet(10.1.1.0/24)<br/>**Veřejná IP adresa** automaticky generovaný.<br/>**Skupina zabezpečení sítě** = None<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít účet úložiště automaticky generované<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |**Úložiště**: použijte spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet(10.1.1.0/24)<br/>**Veřejná IP adresa** automaticky generovaný.<br/>**Skupina zabezpečení sítě** = None<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít účet úložiště automaticky generované<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |
| Konfigurace virtuálního počítače **nastavení systému SQL Server** |Neuvedeno |**Připojení k SQL** = privátní (uvnitř virtuální sítě)<br/>**Port** = 1433<br/>**Ověřování SQL** = zakázat<br/>**Konfigurace úložiště** = obecné<br/>**Automatizované opravy** = neděle na 2:00<br/>**Automatizované zálohování** = zakázáno</br>**Integrace se službou Azure Key Vault** = zakázáno |**Připojení k SQL** = privátní (uvnitř virtuální sítě)<br/>**Port** = 1433<br/>**Ověřování SQL** = zakázat<br/>**Konfigurace úložiště** = obecné<br/>**Automatizované opravy** = neděle na 2:00<br/>**Automatizované zálohování** = zakázáno</br>**Integrace se službou Azure Key Vault** = zakázáno |

<br/>

> [!NOTE]
> Velikosti počítačů navrhované tady jsou určené pro testování skupin dostupnosti ve virtuálních počítačích Azure. Nejlepšího výkonu dosáhnete v produkčním prostředí, najdete v části doporučení pro velikosti počítačů systému SQL Server a konfigurace v [nejlepší postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Po tři virtuální počítače jsou plně zřízený, je nutné připojit, aby **corp.contoso.com** domény a přidělit CORP\Install práva správce na počítače.

### <a name="joinDomain"></a>Připojení serveru k doméně

Teď už moct připojit virtuální počítače na **corp.contoso.com**. Pro virtuální počítače SQL serveru i souborovém serveru určující sdílenou složku proveďte následující kroky:

1. Vzdálené připojení k virtuálnímu počítači s **BUILTIN\DomainAdmin**.
2. V **správce serveru**, klikněte na tlačítko **místní Server**.
3. Klikněte **pracovní skupiny** odkaz.
4. V **název počítače** klikněte na tlačítko **změnu**.
5. Vyberte **domény** zaškrtávací políčko a zadejte **corp.contoso.com** v textovém poli. Klikněte na **OK**.
6. V **zabezpečení systému Windows** místní dialogové okno, zadejte pověření pro účet správce domény výchozí (**CORP\DomainAdmin**) a heslo (**Contoso! 0000**).
7. Když se zobrazí zpráva "Vítejte v doméně corp.contoso.com", klikněte na tlačítko **OK**.
8. Klikněte na tlačítko **Zavřít**a potom klikněte na **restartovat nyní** v automaticky otevřeném okně. dialog.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Přidejte Corp\Install uživatele s oprávněními správce na každý cluster virtuálních počítačů

Po restartování každý virtuální počítač jako člena domény, přidejte **CORP\Install** jako člen místní skupiny administrators.

1. Počkejte, až po restartování virtuálního počítače a potom spusťte soubor RDP znovu z primárního řadiče domény se přihlásit k **sqlserver-0** pomocí **CORP\DomainAdmin** účtu.
   >[!TIP]
   >Ujistěte se, přihlásit pomocí účtu správce domény. V předchozích krocích jste používali účet správce VYTVOŘENÉ v. Teď, když je server v doméně, použijte účet domény. V relaci RDP, zadejte *domény*\\*uživatelské jméno*.

2. V **správce serveru**, vyberte **nástroje**a potom klikněte na **Správa počítače**.
3. V **Správa počítače** okně rozbalte **místní uživatelé a skupiny**a potom vyberte **skupiny**.
4. Dvakrát klikněte **správci** skupiny.
5. V **správci vlastnosti** dialogové okno, klikněte na tlačítko **přidat** tlačítko.
6. Zadejte uživatele **CORP\Install**a potom klikněte na **OK**.
7. Klikněte na tlačítko **OK** zavřete **vlastností správce** dialogové okno.
8. Opakujte předchozí kroky na **sqlserver-1** a **clusteru fsw**.

### <a name="setServiceAccount"></a>Nastavit účty služby SQL Server

Na každý virtuální počítač SQL Server nastavte účet služby SQL Server. Používání účtů, které jste vytvořili při zpracování je [nakonfigurované účty domény](#DomainAccounts).

1. Otevřete **Správce konfigurace systému SQL Server**.
2. Klikněte pravým tlačítkem na službu SQL Server a pak klikněte na **vlastnosti**.
3. Nastavte účet a heslo.
4. Opakujte tyto kroky na další počítač serveru SQL.  

Pro skupiny dostupnosti systému SQL Server musí každý virtuální počítač SQL Server spustit jako účet domény.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Vytvořte na každý virtuální počítač SQL Server k účtu instalace přihlášení

Použijte účet instalace (CORP\install) ke konfiguraci skupiny dostupnosti. Tento účet musí být členem skupiny **sysadmin** pevné role serveru na každý virtuální počítač SQL Server. Přihlášení k účtu instalace vytvořit následující kroky:

1. Připojení k serveru pomocí protokol RDP (Remote Desktop) pomocí  *\<MachineName\>\DomainAdmin* účtu.

1. Otevřete aplikaci SQL Server Management Studio a připojte se k místní instanci systému SQL Server.

1. V **Průzkumník objektů**, klikněte na tlačítko **zabezpečení**.

1. Klikněte pravým tlačítkem na **přihlášení**. Klikněte na tlačítko **nového přihlašovacího jména**.

1. V **přihlášení – nové**, klikněte na tlačítko **vyhledávání**.

1. Klikněte na tlačítko **umístění**.

1. Zadejte přihlašovací údaje domény správce sítě.

1. Použijte účet instalace.

1. Nastavit přihlášení jako člen **sysadmin** pevné role serveru.

1. Klikněte na **OK**.

Opakujte předchozí kroky na další počítač serveru SQL.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Přidání funkcí Clustering převzetí služeb při selhání do obou virtuálním počítačům systému SQL Server

K přidání funkcí Clustering převzetí služeb při selhání, proveďte tyto kroky u virtuálních počítačů i SQL serveru:

1. Připojit k virtuálnímu počítači systému SQL Server pomocí protokol RDP (Remote Desktop) pomocí *CORP\install* účtu. Otevřete **řídicí panel Správce serveru**.
2. Klikněte **přidat role a funkce** odkaz na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Vyberte **Další** až na **funkce serveru** části.
4. V **funkce**, vyberte **Clustering převzetí služeb při selhání**.
5. Přidejte všechny další požadované funkce.
6. Klikněte na tlačítko **nainstalovat** a přidejte funkce.

Opakujte kroky na další počítač serveru SQL.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Konfigurace brány firewall na každý virtuální počítač s SQL serverem

Řešení vyžaduje následující porty TCP otevřen v bráně firewall:

- **Virtuální počítač systému SQL Server**:<br/>
   Port 1433 pro výchozí instanci systému SQL Server.
- **Sondu nástroje pro vyrovnávání zatížení Azure:**<br/>
   Jakýkoli dostupný port. Příklady často používají 59999.
- **Koncový bod zrcadlení databáze:** <br/>
   Jakýkoli dostupný port. Příklady často používají 5022.

Porty brány firewall musí být otevřen v obou virtuálním počítačům systému SQL Server.

Způsob otevření portů závisí na řešení brány firewall, který používáte. V další části vysvětluje, jak otevřít porty v bráně Windows Firewall. Otevřete požadované porty na všech virtuálních počítačů serveru SQL.

### <a name="open-a-tcp-port-in-the-firewall"></a>Otevřete TCP port v bráně firewall

1. Na prvním serveru SQL **spustit** obrazovky, spusťte **brány Windows Firewall s pokročilým zabezpečením**.
2. V levém podokně, vyberte **příchozí pravidla**. V pravém podokně klikněte na tlačítko **nové pravidlo**.
3. Pro **typ pravidla**, zvolte **Port**.
4. Port, zadejte **TCP** a zadejte odpovídající port čísla. Podívejte se na následující příklad:

   ![Brány firewall pro SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Klikněte na **Další**.
6. Na **akce** ponechte **povolit připojení** vybrané a potom klikněte na **Další**.
7. Na **profil** stránky, přijměte výchozí nastavení a pak klikněte na tlačítko **Další**.
8. Na **název** stránky, zadejte název pravidla (například **Azure LB testu**) v **název** textového pole a pak klikněte na tlačítko **Dokončit**.

Opakujte tyto kroky na druhý virtuální počítač SQL Server.

## <a name="configure-system-account-permissions"></a>Konfigurace oprávnění pro uživatelský účet systému

K vytvoření účtu pro účet system a udělení příslušných oprávnění, proveďte tyto kroky u každé instance systému SQL Server:

1. Vytvoření účtu pro `[NT AUTHORITY\SYSTEM]` na každou instanci serveru SQL. Tento skript vytvoří tento účet:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Udělit následující oprávnění k `[NT AUTHORITY\SYSTEM]` na jednotlivé instance SQL serveru:

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

* [Vytvoření skupiny dostupnosti SQL serveru Always On na virtuálních počítačích Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
