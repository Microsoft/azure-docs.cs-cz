---
title: Skupiny dostupnosti SQL serveru – Azure virtual machines – zajistěte | Dokumentace Microsoftu
description: Tento kurz ukazuje, jak konfigurovat požadavky pro vytvoření skupiny dostupnosti AlwaysOn SQL serveru na virtuálních počítačích Azure.
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
ms.openlocfilehash: 2d8a98e6ab38f4156b6e2f5bda81b44e1789a6ed
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253070"
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Zajistěte splnění požadavků pro vytváření skupin dostupnosti AlwaysOn na virtuálních počítačích Azure

Tento kurz ukazuje, jak splnit požadavky pro vytváření [SQL serveru skupiny dostupnosti Always On na Azure virtual machines (VM)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Po dokončení požadavků, máte řadič domény, dva virtuální počítače SQL serveru a monitorovací server v jedné skupiny prostředků.

**Časový odhad**: může trvat několik hodin požadavky. Většina této doby se neztrácí vytváření virtuálních počítačů.

Následující diagram znázorňuje, co vytvoříte v tomto kurzu.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Projděte si dokumentaci k skupiny dostupnosti

V tomto kurzu se předpokládá, že máte základní znalosti o skupinách dostupnosti AlwaysOn SQL serveru. Pokud nejste obeznámeni s touto technologií, přečtěte si téma [přehled vždy na skupin dostupnosti (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Vytvoření účtu Azure
Potřebujete mít účet Azure. Je možné [otevřete si bezplatný účet Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) nebo [aktivovat výhody pro předplatitele sady Visual Studio](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. Klikněte na tlačítko **+** k vytvoření nového objektu na portálu.

   ![Nový objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Typ **skupiny prostředků** v **Marketplace** okno hledání.

   ![Skupina prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Klikněte na tlačítko **skupiny prostředků**.
5. Klikněte na možnost **Vytvořit**.
6. V části **název skupiny prostředků**, zadejte název skupiny prostředků. Zadejte například **sql-ha-rg**.
7. Pokud máte více předplatných Azure, zkontrolujte, že je předplatné předplatné Azure, kterou chcete vytvořit skupinu dostupnosti v.
8. Vyberte umístění. Umístění je oblast Azure, ve kterém chcete vytvořit skupinu dostupnosti. Tento článek vytvoří všechny prostředky v jednom umístění Azure.
9. Ověřte, že **připnout na řídicí panel** je zaškrtnuté políčko. Toto volitelné nastavení umístí klávesovou zkratku pro skupinu prostředků na řídicím panelu portálu Azure.

   ![Skupina prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Klikněte na tlačítko **vytvořit** a vytvořte skupinu prostředků.

Azure vytvoří skupinu prostředků a PIN kódů zástupce do skupiny prostředků na portálu.

## <a name="create-the-network-and-subnets"></a>Vytvoření sítě a podsítě
Dalším krokem je vytvoření sítě a podsítě ve skupině prostředků Azure.

Toto řešení využívá jednu virtuální síť se dvěma podsítěmi. [Přehled virtuálních sítí](../../../virtual-network/virtual-networks-overview.md) poskytuje další informace o sítích v Azure.

Vytvoření virtuální sítě:

1. Na portálu Azure ve vaší skupině prostředků, klikněte na tlačítko **+ přidat**. 

   ![Nová položka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Vyhledejte **virtuální sítě**.

     ![Hledat virtuální síť](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Klikněte na tlačítko **virtuální síť**.
4. Na **virtuální síť**, klikněte na tlačítko **Resource Manageru** model nasazení a pak klikněte na tlačítko **vytvořit**.

    Následující tabulka uvádí nastavení pro virtuální síť:

   | **Pole** | Hodnota |
   | --- | --- |
   | **Název** |autoHAVNET |
   | **Adresní prostor** |10.33.0.0/24 |
   | **Název podsítě** |Správa |
   | **Rozsah adres podsítě** |10.33.0.0/29 |
   | **Předplatné** |Určete předplatné, kterou chcete použít. **Předplatné** je prázdné, pokud máte pouze jedno předplatné. |
   | **Skupina prostředků** |Zvolte **použít existující** a vyberte název skupiny prostředků. |
   | **Umístění** |Zadejte umístění Azure. |

   Vaše adresa prostoru a podsítě rozsah adres se může lišit od tabulky. V závislosti na předplatné navrhne portálu k dispozici adresní prostor a odpovídající rozsah adres podsítě. Pokud je k dispozici žádné dostatečný Adresní prostor, použijte jiný odběr.

   V příkladu se používá název podsítě **správce**. Tato podsíť je pro řadiče domény.

5. Klikněte na možnost **Vytvořit**.

   ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure se vrátíte k řídicímu panelu portálu a vás upozorní, když je vytvořena nová síť.

### <a name="create-a-second-subnet"></a>Vytvořte druhou podsíť
Nová virtuální síť má jednu podsíť s názvem **správce**. Řadiče domény použít tuto podsíť. Virtuální počítače s SQL serverem pomocí druhou podsíť s názvem **SQL**. Ke konfiguraci této podsítě:

1. Na řídicím panelu, klikněte na skupinu prostředků, kterou jste vytvořili, **SQL-HA-RG**. Vyhledejte síť ve skupině prostředků v rámci **prostředky**.

    Pokud **SQL-HA-RG** nezobrazuje, vyhledejte kliknutím **skupiny prostředků** a filtrování podle názvu skupiny prostředků.
2. Klikněte na tlačítko **autoHAVNET** v seznamu prostředků. 
3. Na **autoHAVNET** virtuální síť, v části **nastavení** , klikněte na tlačítko **podsítě**.

    Mějte na paměti, kterou jste již vytvořili podsíť.

   ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Vytvořte druhou podsíť. Klikněte na tlačítko **+ podsíť**.
6. Na **přidat podsíť**, konfigurovat tak, že zadáte podsíť **sqlsubnet** pod **název**. Azure automaticky určuje platnou **rozsah adres**. Ověřte, že tento rozsah adres v sobě obsahuje alespoň 10 adres. V produkčním prostředí může vyžadovat více adres.
7. Klikněte na **OK**.

    ![Konfigurace virtuální sítě](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

V následující tabulce najdete souhrn nastavení konfigurace sítě:

| **Pole** | Hodnota |
| --- | --- |
| **Název** |**autoHAVNET** |
| **Adresní prostor** |Tato hodnota závisí na dostupných adresních prostorů v rámci vašeho předplatného. Typická hodnota je 10.0.0.0/16. |
| **Název podsítě** |**Správce** |
| **Rozsah adres podsítě** |Tato hodnota závisí na rozsahy adres k dispozici v rámci vašeho předplatného. Typická hodnota je 10.0.0.0/24. |
| **Název podsítě** |**sqlsubnet** |
| **Rozsah adres podsítě** |Tato hodnota závisí na rozsahy adres k dispozici v rámci vašeho předplatného. Typická hodnota je 10.0.1.0/24. |
| **Předplatné** |Určete předplatné, kterou chcete použít. |
| **Skupina prostředků** |**SQL-HA-RG** |
| **Umístění** |Určení stejného umístění jako pro skupinu prostředků. |

## <a name="create-availability-sets"></a>Vytvoření skupin dostupnosti

Než začnete vytvářet virtuální počítače, je potřeba vytvořit skupiny dostupnosti. Skupiny dostupnosti snížit prostoje událostem plánované i neplánované údržby. Skupině dostupnosti Azure je logická skupina prostředků, které Azure umístí na fyzických domén selhání a aktualizačních doménách. Doména selhání zajistí, že členové skupiny dostupnosti mají samostatné prostředky pro napájení a sítě. Aktualizační domény zajistí, že členové skupiny dostupnosti nedostaly mimo provoz kvůli údržbě ve stejnou dobu. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Je třeba dvě skupiny dostupnosti. Jedna je pro řadiče domény. Druhá je pro virtuální počítače SQL serveru.

Pokud chcete vytvořit skupinu dostupnosti, přejděte do skupiny prostředků a klikněte na tlačítko **přidat**. Filtrování výsledků zadáním **dostupnosti**. Klikněte na tlačítko **dostupnosti** výsledky a pak klikněte na **vytvořit**.

Konfigurace dvou skupin dostupnosti podle parametry v následující tabulce:

| **Pole** | Skupinu dostupnosti řadiče domény | Skupinu dostupnosti systému SQL Server |
| --- | --- | --- |
| **Název** |adavailabilityset |sqlavailabilityset |
| **Skupina prostředků** |SQL-HA-RG |SQL-HA-RG |
| **Domény selhání** |3 |3 |
| **Aktualizační domény** |5 |3 |

Po vytvoření sady dostupnosti, vraťte se do skupiny prostředků na webu Azure Portal.

## <a name="create-domain-controllers"></a>Vytvoření řadiče domény
Po vytvoření sítě, podsítě, dostupnosti a nástroje pro vyrovnávání zatížení přístupem k Internetu, můžete začít vytvářet virtuální počítače pro řadiče domény.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Vytvoření virtuálního počítače pro řadiče domény
Chcete-li vytvořit a nakonfigurovat řadiče domény, vraťte se na **SQL-HA-RG** skupinu prostředků.

1. Klikněte na tlačítko **Add** (Přidat). 
2. Typ **systému Windows Server 2016 Datacenter**.
3. Klikněte na tlačítko **systému Windows Server 2016 Datacenter**. V **systému Windows Server 2016 Datacenter**, ověřte, zda je model nasazení **Resource Manageru**a potom klikněte na tlačítko **vytvořit**. 

Zopakujte předchozí kroky k vytvoření dvou virtuálních počítačů. Pojmenujte dva virtuální počítače:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > **Řadiče domény ad sekundární** virtuálního počítače je volitelný, pro zajištění vysoké dostupnosti pro službu Active Directory Domain Services.
  >
  >

Následující tabulka ukazuje nastavení pro tyto dva počítače:

| **Pole** | Hodnota |
| --- | --- |
| **Název** |První řadič domény: *řadiče domény ad primární*.</br>Druhý řadič domény *řadiče domény ad sekundární*. |
| **Typ disku virtuálního počítače** |SSD |
| **Uživatelské jméno** |DomainAdmin |
| **Heslo** |Contoso!0000 |
| **Předplatné** |*Vaše předplatné* |
| **Skupina prostředků** |SQL-HA-RG |
| **Umístění** |*Vaše poloha* |
| **Velikost** |DS1_V2 |
| **Storage** | **Použití spravovaných disků** - **Ano** |
| **Virtuální síť** |autoHAVNET |
| **Podsíť** |Správce |
| **Veřejná IP adresa** |*Stejný název jako virtuální počítač* |
| **Skupina zabezpečení sítě** |*Stejný název jako virtuální počítač* |
| **Skupina dostupnosti** |adavailabilityset </br>**Domény selhání**: 2</br>**Aktualizační domény**: 2|
| **Diagnostika** |Povoleno |
| **Účet úložiště diagnostiky** |*Automaticky vytvoří* |

   >[!IMPORTANT]
   >Virtuální počítač můžete umístit pouze ve skupině dostupnosti při jeho vytvoření. Nelze změnit po vytvoření virtuálního počítače skupinu dostupnosti. Zobrazit [Správa dostupnosti virtuálních počítačů](../manage-availability.md).

Azure vytvoří virtuální počítače.

Po vytvoření virtuálních počítačů nakonfigurujte řadič domény.

### <a name="configure-the-domain-controller"></a>Konfigurace řadiče domény
V následujících krocích konfigurace **řadiče domény ad primární** strojově jako řadič domény corp.contoso.com.

1. Na portálu, otevřete **SQL-HA-RG** prostředku, skupiny a vyberte **řadiče domény ad primární** počítače. Na **řadiče domény ad primární**, klikněte na tlačítko **připojit** otevřete soubor RDP pro přístup přes vzdálenou plochu.

    ![Připojení k virtuálnímu počítači](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Přihlaste se pomocí účtu správce nakonfigurované (**\DomainAdmin**) a heslo (**Contoso! 0000**).
3. Ve výchozím nastavení **správce serveru** by se mělo zobrazit řídicí panel.
4. Klikněte na tlačítko **přidat role a funkce** odkaz na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Vyberte **Další** dokud nezískáte **role serveru** oddílu.
6. Vyberte **Active Directory Domain Services** a **DNS Server** role. Po zobrazení výzvy, přidejte všechny další funkce, které jsou nutné tyto role.

   > [!NOTE]
   > Windows vás upozorní, že neexistuje žádná statická IP adresa. Pokud testujete konfiguraci, klikněte na tlačítko **pokračovat**. Pro produkční scénáře nastavena na statickou na webu Azure Portal, IP adresa nebo [pomocí prostředí PowerShell můžete nastavit statickou IP adresu počítače řadiče domény](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Přidat dialog role](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klikněte na tlačítko **Další** dokud se nedostanete **potvrzení** oddílu. Vyberte **cílový server automaticky restartovat, pokud je to nutné** zaškrtávací políčko.
8. Klikněte na **Nainstalovat**.
9. Po dokončení instalace funkce vrátit **správce serveru** řídicího panelu.
10. Vyberte novou **služby AD DS** možnost v levém podokně.
11. Klikněte na tlačítko **Další** odkaz na žlutý pruh upozornění.

    ![Dialogové okno AD DS na virtuálních počítačích serveru DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. V **akce** sloupec **všechny podrobnosti úlohy serveru** dialogového okna, klikněte na tlačítko **povýšit tento server na řadič domény**.
13. V **Průvodce konfigurací služby domény služby Active Directory**, použijte následující hodnoty:

    | **Stránka** | Nastavení |
    | --- | --- |
    | **Konfigurace nasazení** |**Přidat novou doménovou strukturu**<br/> **Název kořenové domény** = corp.contoso.com |
    | **Možnosti řadiče domény** |**Heslo režimu obnovení adresářových služeb** = Contoso! 0000<br/>**Potvrzení hesla** = Contoso! 0000 |
14. Klikněte na tlačítko **Další** projít další stránky průvodce. Na **Kontrola předpokladů** stránce, ověřte, že se zobrazí následující zpráva: **všech požadovaných součástí byly úspěšně zkontrolovány**. Můžete zkontrolovat všechny příslušné zprávy upozornění, ale je možné pokračovat v instalaci.
15. Klikněte na **Nainstalovat**. **Řadiče domény ad primární** virtuální počítač automaticky restartuje.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Poznamenejte si IP adresu primárního řadiče domény

Použijte primárního řadiče domény pro službu DNS. Poznámka: IP adresa adaptéru primární doménu.

Jedním ze způsobů k získání IP adresy primárního kontroleru je na webu Azure portal.

1. Na webu Azure portal otevřete skupinu prostředků.

2. Klikněte na primárním řadiči domény.

3. Na primárním řadiči domény, klikněte na tlačítko **síťová rozhraní**.

![Síťová rozhraní](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Poznámka: privátní IP adresu pro tento server.

### <a name="configure-the-virtual-network-dns"></a>Konfigurace virtuální sítě DNS
Když vytvoříte první řadič domény a povolíte DNS na prvním serveru, nakonfigurujte virtuální síť pro použití tohoto serveru DNS.

1. Na webu Azure Portal klikněte na tlačítko ve virtuální síti.

2. V části **nastavení**, klikněte na tlačítko **DNS Server**.

3. Klikněte na tlačítko **vlastní**a zadejte privátní IP adresu primárního řadiče domény.

4. Klikněte na **Uložit**.

### <a name="configure-the-second-domain-controller"></a>Konfigurace druhého řadiče domény
Po restartování primárním řadiči domény, můžete nakonfigurovat druhého řadiče domény. Tento volitelný krok je pro zajištění vysoké dostupnosti. Postupujte podle těchto kroků a nakonfigurujte druhého řadiče domény:

1. Na portálu, otevřete **SQL-HA-RG** prostředku, skupiny a vyberte **řadiče domény ad sekundární** počítače. Na **řadiče domény ad sekundární**, klikněte na tlačítko **připojit** otevřete soubor RDP pro přístup přes vzdálenou plochu.
2. Přihlaste se k virtuálnímu počítači pomocí účtu správce nakonfigurované (**BUILTIN\DomainAdmin**) a heslo (**Contoso! 0000**).
3. Změňte upřednostňovanou adresu serveru DNS na adresu řadiče domény.
4. V **Centrum sítí a sdílení**, klikněte na síťové rozhraní.
   ![Síťové rozhraní](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Klikněte na **Vlastnosti**.
6. Vyberte **Internet Protocol verze 4 (TCP/IPv4)** a klikněte na tlačítko **vlastnosti**.
7. Vyberte **použít následující adresy serverů DNS** a zadejte adresu primárního řadiče domény v **upřednostňovaný server DNS**.
8. Klikněte na tlačítko **OK**a potom **Zavřít** tím potvrdíte změny. Nyní budete moci připojit k virtuálnímu počítači **corp.contoso.com**.

   >[!IMPORTANT]
   >Pokud ztratíte připojení k vzdálené ploše po změně nastavení serveru DNS, přejděte na web Azure Portal a restartujte virtuální počítač.

9. Vzdálenou plochu sekundární řadič domény, otevřete **řídicí panel Správce serveru**.
10. Klikněte na tlačítko **přidat role a funkce** odkaz na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Vyberte **Další** dokud nezískáte **role serveru** oddílu.
12. Vyberte **Active Directory Domain Services** a **DNS Server** role. Po zobrazení výzvy, přidejte všechny další funkce, které jsou nutné tyto role.
13. Po dokončení instalace funkce vrátit **správce serveru** řídicího panelu.
14. Vyberte novou **služby AD DS** možnost v levém podokně.
15. Klikněte na tlačítko **Další** odkaz na žlutý pruh upozornění.
16. V **akce** sloupec **všechny podrobnosti úlohy serveru** dialogového okna, klikněte na tlačítko **povýšit tento server na řadič domény**.
17. V části **konfigurace nasazení**vyberte **přidat řadič domény do již existující domény**.
   ![Konfigurace nasazení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Klikněte na **Vybrat**.
19. Připojte se pomocí účtu správce (**CORP. CONTOSO.COM\domainadmin**) a heslo (**Contoso! 0000**).
20. V **vyberte doménu v doménové struktuře**, klikněte na vaši doménu a pak klikněte na tlačítko **OK**.
21. V **možnosti řadiče domény**, použijte výchozí hodnoty a nastavte heslo režimu obnovení adresářových služeb.

   >[!NOTE]
   >**Možnosti služby DNS** stránky možná by vás varovala, že nelze vytvořit delegování pro tento server DNS. Můžete ignorovat toto upozornění v neprodukčních prostředích.
22. Klikněte na tlačítko **Další** dokud nedosáhne dialogového okna **požadavky** zkontrolovat. Pak klikněte na **Nainstalovat**.

Jakmile server dokončí změny konfigurace, restartujte server.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Přidejte privátní IP adresa druhého řadiče domény DNS serveru sítě VPN

Na webu Azure Portal, v rámci virtuální sítě změňte obsahující IP adresu řadiče domény sekundární Server DNS. Toto nastavení umožňuje redundance služby DNS.

### <a name=DomainAccounts></a> Konfigurace účtů domény

V dalších krocích konfigurace účtů služby Active Directory. V následující tabulce jsou uvedeny účty:

| |Účet instalace<br/> |sqlserver-0 <br/>Účet serveru SQL Server a služba SQL Agent |sqlserver-1<br/>Účet serveru SQL Server a služba SQL Agent
| --- | --- | --- | ---
|**Křestní jméno** |Instalace |SQLSvc1 | SQLSvc2
|**SamAccountName uživatele** |Instalace |SQLSvc1 | SQLSvc2

Pomocí následujících kroků vytvořte každý účet.

1. Přihlaste se k **řadiče domény ad primární** počítače.
2. V **správce serveru**vyberte **nástroje**a potom klikněte na tlačítko **Centrum správy služby Active Directory**.   
3. Vyberte **corp (místní)** v levém podokně.
4. Na pravé straně **úlohy** vyberte **nový**a potom klikněte na tlačítko **uživatele**.
   ![Centrum správy služby Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Nastavení pro každý účet složité heslo.<br/> Neprodukčních prostředích nastavte účet uživatele nikdy nevyprší.

5. Klikněte na tlačítko **OK** pro vytvoření uživatele.
6. Předchozí kroky opakujte pro všechny tři účty.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Udělit požadovaná oprávnění pro účet instalace
1. V **Centrum správy služby Active Directory**vyberte **corp (místní)** v levém podokně. Potom v pravém **úlohy** podokně klikněte na tlačítko **vlastnosti**.

    ![Vlastností uživatele Corp.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Vyberte **rozšíření**a potom klikněte na tlačítko **Upřesnit** tlačítko **zabezpečení** kartu.
3. V **Upřesnit nastavení zabezpečení pro corp** dialogového okna, klikněte na tlačítko **přidat**.
4. Klikněte na tlačítko **vybrat objekt zabezpečení**, vyhledejte **CORP\Install**a potom klikněte na tlačítko **OK**.
5. Vyberte **číst vlastnosti všech** zaškrtávací políčko.

6. Vyberte **vytvářet objekty počítačů** zaškrtávací políčko.

     ![Oprávnění uživatele Corp.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **OK** znovu. Zavřít **corp** okna Vlastnosti.

Teď, když jste dokončili konfiguraci služby Active Directory a uživatelských objektů, vytvořte dva virtuální počítače SQL serveru a serveru s kopií clusteru virtuálních počítačů. Připojte k doméně všech třech umístěních.

## <a name="create-sql-server-vms"></a>Vytvořit virtuální počítače s SQL serverem

Vytvořte další tři virtuální počítače. Toto řešení vyžaduje dva virtuální počítače s instancí systému SQL Server. Třetí virtuální počítač bude fungovat jako určující kopii. Můžete použít Windows Server 2016 [cloudu s kopií clusteru](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), ale konzistence s starších operačních systémech používá tento dokument virtuálního počítače pro určující kopii.  

Než budete pokračovat, zvažte následující rozhodnutí o návrhu.

* **Storage – Azure Managed Disks**

   Pro úložiště virtuálního počítače pomocí Azure Managed Disks. Microsoft doporučuje službu Managed Disks pro virtuální počítače s SQL serverem. Služba Managed Disks se stará o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se službou Managed Disks ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v tématu [Přehled služby Azure Managed Disks](../managed-disks-overview.md). Podrobnosti o spravovaných discích ve skupině dostupnosti, najdete v části [použít spravované disky pro virtuální počítače ve skupině dostupnosti](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Sítě – privátní IP adresy v produkčním prostředí**

   Tento kurz používá pro virtuální počítače veřejné IP adresy. Veřejná IP adresa umožňuje připojení ke vzdálené přímo k virtuálnímu počítači přes internet – to usnadňuje kroky konfigurace. V produkčním prostředí společnost Microsoft doporučuje pouze privátní IP adresy za účelem snížení nároky na ohrožení zabezpečení systému SQL Server instance prostředku virtuálního počítače.

### <a name="create-and-configure-the-sql-server-vms"></a>Vytvoření a konfigurace virtuální počítače SQL serveru
Dále vytvořte tři virtuální počítače – dva virtuální počítače SQL serveru a virtuálních počítačů pro do dodatečného uzlu clusteru. Pro každý virtuální počítač vytvořit, přejděte zpět na **SQL-HA-RG** skupinu prostředků, klikněte na tlačítko **přidat**, vyhledejte položku odpovídající galerie, klikněte na tlačítko **virtuální počítač**a potom klikněte na tlačítko  **Z Galerie**. Pomocí informací v následující tabulce můžete vytvořit virtuální počítače:


| Stránka | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Vyberte položku odpovídající Galerie |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise ve Windows serveru 2016** |**SQL Server 2016 SP1 Enterprise ve Windows serveru 2016** |
| Konfigurace virtuálního počítače **základy** |**Název** = fsw clusteru<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso! 0000<br/>**Předplatné** = předplatného<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = azure umístění |**Název** = 0 systému SQL Server<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso! 0000<br/>**Předplatné** = předplatného<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = azure umístění |**Název** = 1 systému SQL Server<br/>**Uživatelské jméno** = DomainAdmin<br/>**Heslo** = Contoso! 0000<br/>**Předplatné** = předplatného<br/>**Skupina prostředků** = SQL-HA-RG<br/>**Umístění** = azure umístění |
| Konfigurace virtuálního počítače **velikost** |**VELIKOST** = DS1\_V2 (1 virtuální procesor, 3,5 GB) |**VELIKOST** = DS2\_V2 (2 virtuální procesory, 7 GB)</br>Velikost musí podporovat úložiště SSD (podpora prémiových disků. )) |**VELIKOST** = DS2\_V2 (2 virtuální procesory, 7 GB) |
| Konfigurace virtuálního počítače **nastavení** |**Úložiště**: použít spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet(10.1.1.0/24)<br/>**Veřejná IP adresa** automaticky generované.<br/>**Skupina zabezpečení sítě** = None<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít účet úložiště automaticky generované<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |**Úložiště**: použít spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet(10.1.1.0/24)<br/>**Veřejná IP adresa** automaticky generované.<br/>**Skupina zabezpečení sítě** = None<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít účet úložiště automaticky generované<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |**Úložiště**: použít spravované disky.<br/>**Virtuální síť** = autoHAVNET<br/>**Podsíť** = sqlsubnet(10.1.1.0/24)<br/>**Veřejná IP adresa** automaticky generované.<br/>**Skupina zabezpečení sítě** = None<br/>**Monitorování diagnostiky** = povoleno<br/>**Účet úložiště diagnostiky** = použít účet úložiště automaticky generované<br/>**Skupina dostupnosti** = sqlAvailabilitySet<br/> |
| Konfigurace virtuálního počítače **nastavení systému SQL Server** |Neuvedeno |**Připojení k SQL** = privátní (uvnitř virtuální sítě)<br/>**Port** = 1433<br/>**Ověřování SQL** = Disable<br/>**Konfigurace úložiště** = obecné<br/>**Automatizované opravy** = neděli v 2:00<br/>**Automatizované zálohování** = zakázáno</br>**Integrace se službou Azure Key Vault** = zakázáno |**Připojení k SQL** = privátní (uvnitř virtuální sítě)<br/>**Port** = 1433<br/>**Ověřování SQL** = Disable<br/>**Konfigurace úložiště** = obecné<br/>**Automatizované opravy** = neděli v 2:00<br/>**Automatizované zálohování** = zakázáno</br>**Integrace se službou Azure Key Vault** = zakázáno |

<br/>

> [!NOTE]
> Velikosti počítačů navrhované Zde jsou určeny pro skupiny dostupnosti testování ve virtuálních počítačích Azure. Pro zajištění nejlepšího výkonu na produkční úlohy najdete v článku doporučení pro SQL Server velikosti a konfiguraci počítačů ve [osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Až tři virtuální počítače jsou úplně zřízená, budete muset připojovat je k **corp.contoso.com** domény a udělení CORP\Install práva správce na počítačích.

### <a name="joinDomain"></a>Připojte servery k doméně

Nyní budete moci připojit k virtuálním počítačům na **corp.contoso.com**. Pro virtuální počítače SQL serveru i souborovém serveru sdílenou složku s kopií clusteru proveďte následující kroky:

1. Vzdálené připojení k virtuálnímu počítači s **BUILTIN\DomainAdmin**.
2. V **správce serveru**, klikněte na tlačítko **místní Server**.
3. Klikněte na tlačítko **pracovní skupiny** odkaz.
4. V **název_počítače** klikněte na tlačítko **změnu**.
5. Vyberte **domény** políčko a zadejte **corp.contoso.com** v textovém poli. Klikněte na **OK**.
6. V **zabezpečení Windows** dialogové okno automaticky otevírané okno, zadejte přihlašovací údaje pro účet správce domény, výchozí (**CORP\DomainAdmin**) a heslo (**Contoso! 0000**) .
7. Když se zobrazí zpráva "Vítá v doméně corp.contoso.com", klikněte na tlačítko **OK**.
8. Klikněte na tlačítko **Zavřít**a potom klikněte na tlačítko **restartovat nyní** v dialogovém okně automaticky otevíraného okna.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Přidání Corp\Install uživatele s oprávněními správce na každý cluster virtuálních počítačů

Po restartování každý virtuální počítač jako člena domény, přidejte **CORP\Install** jako člen místní skupiny administrators.

1. Počkejte, až po restartování virtuálního počítače a pak spusťte soubor RDP znovu primárním řadiči domény pro přihlášení k **sqlserver 0** pomocí **CORP\DomainAdmin** účtu.
   >[!TIP]
   >Ujistěte se, že se přihlásíte pomocí účtu správce domény. V předchozích krocích jste používali VYTVOŘENÉ v účtu správce. Teď, když je server v doméně, použijte účet domény. V relaci RDP, zadejte *domény*\\*uživatelské jméno*.

2. V **správce serveru**vyberte **nástroje**a potom klikněte na tlačítko **Správa počítače**.
3. V **Správa počítače** okna rozbalte **místní uživatelé a skupiny**a pak vyberte **skupiny**.
4. Dvakrát klikněte **správci** skupiny.
5. V **správci vlastnosti** dialogového okna, klikněte na tlačítko **přidat** tlačítko.
6. Zadejte uživatele **CORP\Install**a potom klikněte na tlačítko **OK**.
7. Klikněte na tlačítko **OK** zavřete **vlastností správce** dialogového okna.
8. Zopakujte předchozí kroky ve **sqlserver 1** a **clusteru fsw**.

### <a name="setServiceAccount"></a>Nastavení účtů služby SQL Server

Na každém virtuálním počítači SQL serveru nastavte účet služby SQL Server. Používání účtů, které jste vytvořili při vám [nakonfigurované účty domény](#DomainAccounts).

1. Otevřít **Správce konfigurace systému SQL Server**.
2. Klikněte pravým tlačítkem na službu systému SQL Server a potom klikněte na tlačítko **vlastnosti**.
3. Nastavte účet a heslo.
4. Opakujte tyto kroky na další počítač serveru SQL.  

Pro skupiny dostupnosti systému SQL Server každý virtuální počítač s SQL serverem je potřeba spustit jako účet domény.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Vytvoření přihlášení na každém virtuálním počítači SQL serveru pro účet instalace

Účet instalace (CORP\install) použijte ke konfiguraci skupiny dostupnosti. Tento účet musí být členem skupiny **sysadmin** pevné role serveru na každém virtuálním počítači SQL serveru. Následujícím postupem se vytvoří u přihlášení pro účet instalace:

1. Připojení k serveru prostřednictvím protokol RDP (Remote Desktop) s použitím  *\<MachineName\>\DomainAdmin* účtu.

1. Otevřete SQL Server Management Studio a připojte se k místní instanci systému SQL Server.

1. V **Průzkumník objektů**, klikněte na tlačítko **zabezpečení**.

1. Klikněte pravým tlačítkem na **přihlášení**. Klikněte na tlačítko **nového přihlašovacího jména**.

1. V **přihlášení – nové**, klikněte na tlačítko **hledání**.

1. Klikněte na tlačítko **umístění**.

1. Zadejte síťové přihlašovací údaje správce domény.

1. Použijte účet instalace.

1. Nastavení přihlášení jako člen **sysadmin** pevné role serveru.

1. Klikněte na **OK**.

Zopakujte předchozí kroky v další počítač serveru SQL.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Přidání funkcí Clustering převzetí služeb při selhání do obou virtuálních počítačů s SQL serverem

Přidávání funkcí Clustering převzetí služeb při selhání, proveďte následující kroky na oba virtuální počítače s SQL serverem:

1. Připojení k virtuálnímu počítači SQL serveru prostřednictvím protokol RDP (Remote Desktop) s použitím *CORP\install* účtu. Otevřít **řídicí panel Správce serveru**.
2. Klikněte na tlačítko **přidat role a funkce** odkaz na řídicím panelu.

    ![Správce serveru – přidání rolí](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Vyberte **Další** dokud nezískáte **funkce serveru** oddílu.
4. V **funkce**vyberte **Clustering převzetí služeb při selhání**.
5. Přidejte všechny další požadované funkce.
6. Klikněte na tlačítko **nainstalovat** přidejte funkce.

Opakujte kroky na další počítač serveru SQL.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Konfigurace brány firewall na každém virtuálním počítači SQL serveru

Řešení vyžaduje, aby následující porty TCP v bráně firewall otevřen:

- **Virtuálního počítače s SQL serverem**:<br/>
   Port 1433 pro výchozí instanci systému SQL Server.
- **Test paměti nástroje pro vyrovnávání zatížení Azure:**<br/>
   Jakýkoli dostupný port. Příklady často používají 59999.
- **Koncový bod zrcadlení databáze:** <br/>
   Jakýkoli dostupný port. Příklady často používají 5022.

Porty brány firewall musí být otevřené v obou virtuálních počítačů s SQL serverem.

Způsob otevření portů závisí na vámi používané řešení brány firewall. V další části vysvětluje, jak otevřít porty v bráně Windows Firewall. Otevřete požadované porty na jednotlivých virtuálních počítačů s SQL serverem.

### <a name="open-a-tcp-port-in-the-firewall"></a>Otevřete TCP port v bráně firewall

1. Na prvním serveru SQL **Start** obrazovky, spusťte **brány Windows Firewall s pokročilým zabezpečením**.
2. V levém podokně vyberte **příchozí pravidla**. V pravém podokně klikněte na tlačítko **nové pravidlo**.
3. Pro **typ pravidla**, zvolte **Port**.
4. Port, zadejte **TCP** a zadejte odpovídající port čísla. Prohlédněte si následující příklad:

   ![Brána firewall služby SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Klikněte na **Další**.
6. Na **akce** stránce, zachovat **povolit připojení** vybrána a potom klikněte na tlačítko **Další**.
7. Na **profilu** stránce, přijměte výchozí nastavení a potom klikněte na tlačítko **Další**.
8. Na **název** stránky, zadejte název pravidla (například **Azure LB Probe**) v **název** textové pole a pak klikněte na tlačítko **Dokončit**.

Opakujte tyto kroky na druhém SQL Server.

## <a name="configure-system-account-permissions"></a>Konfigurace oprávnění pro účet systému

K vytvoření účtu pro účet system a udělit příslušná oprávnění, proveďte následující kroky pro každou instanci serveru SQL Server:

1. Vytvoření účtu pro `[NT AUTHORITY\SYSTEM]` pro každou instanci serveru SQL Server. Tento skript vytvoří tento účet:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Udělte oprávnění k `[NT AUTHORITY\SYSTEM]` pro každou instanci serveru SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Tento skript udělí tato oprávnění:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Další postup

* [Vytvoření skupiny dostupnosti AlwaysOn SQL serveru na virtuálních počítačích Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
