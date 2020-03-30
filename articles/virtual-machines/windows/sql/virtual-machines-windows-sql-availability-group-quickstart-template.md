---
title: Konfigurace skupiny dostupnosti (šablona rychlého startu Azure)
description: Pomocí šablon rychlého startu Azure vytvořte cluster s podporou převzetí služeb při selhání Windows, připojte virtuální počítače SQL Serveru ke clusteru, vytvořte naslouchací proces a nakonfigurujte interní nástroje pro vyrovnávání zatížení v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022382"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Konfigurace skupiny dostupnosti pro SQL Server na virtuálním počítači Azure pomocí šablon Azure quickstart
Tento článek popisuje, jak pomocí šablon rychlého startu Azure částečně automatizovat nasazení konfigurace skupiny dostupnosti vždy na pro virtuální počítače SQL Server v Azure. V tomto procesu se používají dvě šablony rychlého startu Azure: 

   | Šablona | Popis |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Vytvoří cluster windows převzetí služeb při selhání a připojí virtuální chody SQL Server k němu. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Vytvoří naslouchací proces skupiny dostupnosti a nakonfiguruje interní vyrovnávání zatížení. Tuto šablonu lze použít pouze v případě, že cluster windows převzetí služeb při selhání byl vytvořen pomocí šablony **101-sql-vm-ag-setup.** |
   | &nbsp; | &nbsp; |

Ostatní části konfigurace skupiny dostupnosti musí být provedeny ručně, například vytvoření skupiny dostupnosti a vytvoření interního nástroje pro vyrovnávání zatížení. Tento článek obsahuje posloupnost automatizovaných a ruční kroky.
 

## <a name="prerequisites"></a>Požadavky 
Chcete-li automatizovat nastavení skupiny dostupnosti vždy zapnuté pomocí šablon rychlého startu, musíte mít následující požadavky: 
- Předplatné [Azure](https://azure.microsoft.com/free/).
- Skupina prostředků s řadičem domény. 
- Jeden nebo více virtuálních počítačů spojených s doménou [v Azure se spuštěnou edicí SQL Server 2016 (nebo novější) Enterprise,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) které jsou ve stejné skupině dostupnosti nebo v zóně dostupnosti a které byly [zaregistrovány u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Dvě dostupné (nepoužívané žádnou entitou) IP adresy: jedna pro interní vyrovnávání zatížení a jedna pro naslouchací proces skupiny dostupnosti v rámci stejné podsítě jako skupina dostupnosti. Pokud se používá existující správce zatížení, potřebujete pouze jednu dostupnou adresu IP.  

## <a name="permissions"></a>Oprávnění
Následující oprávnění jsou nezbytná ke konfiguraci skupiny dostupnosti vždy zapnuté pomocí šablon rychlého startu Azure: 

- Existující uživatelský účet domény, který má oprávnění **Vytvořit objekt počítače** v doméně.  Například účet správce domény má obvykle dostatečná account@domain.comoprávnění (například: ). _Tento účet by měl být také součástí skupiny místního správce na každém virtuálním počítači k vytvoření clusteru._
- Uživatelský účet domény, který řídí službu SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Krok 1: Vytvoření clusteru s podporou převzetí služeb při selhání a připojení virtuálních počítače SQL Server ke clusteru pomocí šablony rychlého startu 
Po registraci virtuálních počítačů SQL Server u poskytovatele prostředků virtuálního počítače SQL Se můžete připojit k virtuálním počítačům SQL Server ke *skupinám SqlVirtualMachineGroups*. Tento prostředek definuje metadata clusteru windows převzetí služeb při selhání. Metadata zahrnují verzi, edici, plně kvalifikovaný název domény, účty služby Active Directory pro správu clusteru i služby SQL Server a účet úložiště jako cloudový důkaz. 

Přidání virtuálních počítačů SQL Server do skupiny prostředků *SQLVirtualMachineGroups* spustí zasuňte clusterovou službu S podporou převzetí služeb při selhání systému Windows a vytvoří cluster a připojí tyto virtuální počítače SQL Server k tomuto clusteru. Tento krok je automatizován pomocí šablony rychlého startu **101-sql-vm-ag-setup.** Můžete ji implementovat pomocí následujících kroků:

1. Přejděte na šablonu rychlého startu [**101-sql-vm-ag-setup.**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Pak vyberte **Nasazení do Azure** a otevřete šablonu rychlého startu na webu Azure Portal.
1. Vyplňte požadovaná pole pro konfiguraci metadat pro cluster windows převzetí služeb při selhání. Volitelná pole můžete ponechat prázdná.

   V následující tabulce jsou uvedeny potřebné hodnoty pro šablonu: 

   | **Pole** | Hodnota |
   | --- | --- |
   | **Předplatné** |  Předplatné, kde existují virtuální počítače SQL Server. |
   |**Skupina prostředků** | Skupina prostředků, ve které jsou virtuální počítače SQL Server umístěny. | 
   |**Název clusteru s podporou převzetí služeb při selhání** | Název, který chcete pro nový cluster windows převzetí služeb při selhání. |
   | **Existující seznam virtuálních mís** | Virtuální servery SQL Server, které se chcete účastnit skupiny dostupnosti a být součástí tohoto nového clusteru. Oddělte tyto hodnoty čárkou a mezerou (například *SQLVM1, SQLVM2).* |
   | **Verze serveru SQL Server** | Verze sql serveru virtuálních počítačích SQL Server. Vyberte ji z rozevíracího seznamu. V současné době jsou podporovány pouze sql server 2016 a SQL Server 2017 bitové kopie. |
   | **Existující plně kvalifikovaný název domény** | Existující hlavní název domény pro doménu, ve které jsou virtuální počítače SQL Server umístěny. |
   | **Existující účet domény** | Existující uživatelský účet domény, který má **oprávnění Vytvořit objekt počítače** v doméně jako [CNO](/windows-server/failover-clustering/prestage-cluster-adds) je vytvořen během nasazení šablony. Například účet správce domény má obvykle dostatečná account@domain.comoprávnění (například: ). *Tento účet by měl být také součástí skupiny místního správce na každém virtuálním počítači k vytvoření clusteru.*| 
   | **Heslo účtu domény** | Heslo pro dříve uvedený uživatelský účet domény. | 
   | **Existující účet služby SQL** | Uživatelský účet domény, který řídí [službu SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) account@domain.comběhem nasazení skupiny dostupnosti (například: ). |
   | **Heslo služby SQL** | Heslo používané uživatelským účtem domény, který řídí službu SQL Server. |
   | **Název cloudového svědka** | Nový účet úložiště Azure, který se bude vytvářet a používat pro cloud svědka. Tento název můžete upravit. |
   | **\_artefakty Umístění** | Toto pole je nastaveno ve výchozím nastavení a nemělo by být měněno. |
   | **\_artefakty Umístění Sas Token** | Toto pole je záměrně ponecháno prázdné. |
   | &nbsp; | &nbsp; |

1. Pokud souhlasíte s podmínkami, zaškrtněte **políčko Souhlasím s výše uvedenými podmínkami.** Pak vyberte **Koupit a** dokončete nasazení šablony rychlého startu. 
1. Chcete-li sledovat nasazení, vyberte nasazení z ikony **zvonek oznámení** v horní navigační banner nebo přejděte na **skupinu prostředků** na webu Azure Portal. V části **Nastavení**vyberte **Nasazení** a zvolte nasazení **Microsoft.Template.** 

>[!NOTE]
> Pověření poskytnutá během nasazení šablony jsou uložena pouze po dobu trvání nasazení. Po dokončení nasazení jsou tato hesla odebrána. Budete vyzváni k jejich poskytnutí znovu, pokud přidáte další virtuální chod sql serveru do clusteru. 


## <a name="step-2-manually-create-the-availability-group"></a>Krok 2: Ruční vytvoření skupiny dostupnosti 
Ručně vytvořte skupinu dostupnosti obvyklým způsobem pomocí [sql server management studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)nebo [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Nevytvářejte* naslouchací proces v tomto okamžiku, protože **101-sql-vm-aglistener-setup** úvodní šablona to automaticky v kroku 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Krok 3: Ruční vytvoření interního systému vyrovnávání zatížení
Naslouchací proces skupiny dostupnosti always on vyžaduje interní instanci Azure Load Balancer. Interní vykladač zatížení poskytuje "plovoucí" IP adresu pro naslouchací proces skupiny dostupnosti, který umožňuje rychlejší převzetí služeb při selhání a opětovné připojení. Pokud jsou virtuální servery SQL Server ve skupině dostupnosti součástí stejné skupiny dostupnosti, můžete použít základní nástroje pro vyrovnávání zatížení. V opačném případě je třeba použít standardní vyrovnávání zatížení. 

> [!IMPORTANT]
> Interní nástroj pro vyrovnávání zatížení by měl být ve stejné virtuální síti jako instance virtuálního počítače SQL Server. 

Stačí vytvořit interní systém vyrovnávání zatížení. V kroku 4, **101-sql-vm-aglistener-setup** šablony zpracovává zbytek konfigurace (například back-endového fondu, sondy stavu a pravidla vyrovnávání zatížení). 

1. Na webu Azure Portal otevřete skupinu prostředků, která obsahuje virtuální počítače SQL Serveru. 
2. Ve skupině prostředků vyberte **Přidat**.
3. Vyhledejte **vyvažovače zatížení**. Ve výsledcích hledání vyberte **vykladač zatížení**, který je publikován společností **Microsoft**.
4. V noži **Balancer zatížení** vyberte **Vytvořit**.
5. V dialogovém okně **Vytvořit balancer** nakonfigurujte vyvažovač zatížení následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Zadejte textový název, který představuje vyrovnávání zatížení. Zadejte například **sqlLB**. |
   | **Typ** |**Interní**: Většina implementací používá interní nástroj pro vyrovnávání zatížení, který umožňuje aplikacím v rámci stejné virtuální sítě připojit se ke skupině dostupnosti.  </br> **Externí**: Umožňuje aplikacím připojit se ke skupině dostupnosti prostřednictvím veřejného připojení k internetu. |
   | **Virtuální síť** | Vyberte virtuální síť, ve které se nacházejí instance serveru SQL Server. |
   | **Podsíť** | Vyberte podsíť, ve které se nacházejí instance serveru SQL Server. |
   | **Přiřazení IP adresy** |**Statické** |
   | **Privátní IP adresa** | Zadejte dostupnou adresu IP z podsítě. |
   | **Předplatné** |Pokud máte více předplatných, může se toto pole zobrazit. Vyberte předplatné, které chcete přidružit k tomuto prostředku. Obvykle se jedná o stejné předplatné jako všechny prostředky pro skupinu dostupnosti. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, ve které se nacházejí instance serveru SQL Server. |
   | **Umístění** |Vyberte umístění Azure, ve kterých se nacházejí instance SQL Serveru. |
   | &nbsp; | &nbsp; |

6. Vyberte **Vytvořit**. 


>[!IMPORTANT]
> Veřejný prostředek IP pro každý virtuální počítač SQL Server by měl mít standardní skladovou položku, která by byla kompatibilní se standardním nástroji pro vyrovnávání zatížení. Pokud chcete určit skladovou položku veřejného prostředku IP virtuálního počítače, přejděte na **Skupinu prostředků**, vyberte prostředek **veřejné IP adresy** pro virtuální počítač SQL Server a vyhledejte hodnotu pod **skladovou položkou** v podokně **Přehled.** 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Krok 4: Vytvoření naslouchací proces skupiny dostupnosti a konfigurace interního nástrojů pro vyrovnávání zatížení pomocí šablony rychlého startu

Vytvořte naslouchací proces skupiny dostupnosti a nakonfigurujte interní nástroje pro vyrovnávání zatížení automaticky pomocí šablony rychlého spuštění **101-sql-vm-aglistener-setup.** Šablona zřdí prostředek Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Šablona rychlého startu **101-sql-vm-aglistener-setup** prostřednictvím zprostředkovatele prostředků virtuálního počítače SQL provádí následující akce:

- Vytvoří nový front-end IP prostředek (na základě hodnoty IP adresy zapředpokladu během nasazení) pro naslouchací proces. 
- Konfiguruje nastavení sítě pro cluster a interní systém vyrovnávání zatížení. 
- Konfiguruje back-endový fond pro interní vyrovnávání zatížení, sondu stavu a pravidla vyrovnávání zatížení.
- Vytvoří naslouchací proces skupiny dostupnosti s danou adresou IP a názvem.
 
>[!NOTE]
> **Nastavení 101-sql-vm-aglistener** můžete použít pouze v případě, že cluster s podporou převzetí služeb při selhání systému Windows byl vytvořen pomocí šablony **101-sql-vm-ag-setup.**
   
   
Chcete-li nakonfigurovat interní systém vyrovnávání zatížení a vytvořit naslouchací proces skupiny dostupnosti, postupujte takto:
1. Přejděte na šablonu rychlého startu [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) a vyberte **Nasazení do Azure** a spusťte šablonu rychlého startu na webu Azure Portal.
1. Vyplňte požadovaná pole pro konfiguraci interního systému vyrovnávání zatížení a vytvořte naslouchací proces skupiny dostupnosti. Volitelná pole můžete ponechat prázdná. 

   V následující tabulce jsou uvedeny potřebné hodnoty pro šablonu: 

   | **Pole** | Hodnota |
   | --- | --- |
   |**Skupina prostředků** | Skupina prostředků, kde existují virtuální počítače SQL Server a skupiny dostupnosti. | 
   |**Existující název clusteru s podporou převzetí služeb při selhání** | Název clusteru, ke kterému jsou připojeny virtuální počítače SQL Server. |
   | **Existující skupina dostupnosti sql**| Název skupiny dostupnosti, které jsou součástí virtuálních počítačích SQL Server. |
   | **Existující seznam virtuálních mís** | Názvy virtuálních počítačů SQL Server, které jsou součástí výše uvedené skupiny dostupnosti. Oddělte názvy čárkou a mezerou (například *SQLVM1, SQLVM2).* |
   | **Naslouchací proces** | Název DNS, který chcete přiřadit naslouchací proces. Ve výchozím nastavení tato šablona určuje název "aglistener", ale můžete jej změnit. Název by neměl přesáhnout 15 znaků. |
   | **Port posluchače** | Port, který má naslouchací proces použít. Obvykle tento port by měl být výchozí 1433. Toto je číslo portu, které určuje šablona. Ale pokud výchozí port byl změněn, naslouchací proces port by měl použít tuto hodnotu místo. | 
   | **Adresa IP posluchače** | IP adresa, kterou má naslouchací proces používat. Tato adresa bude vytvořena během nasazení šablony, takže zadejte takovou, která se ještě nepoužívá.  |
   | **Existující podsíť** | Název interní podsítě virtuálních počítačích SQL Server (například: *výchozí).* Tuto hodnotu můžete určit tak, že přejdete do **skupiny prostředků**, vyberete virtuální síť, vyberete **podsítě** v podokně **Nastavení** a zkopírujte hodnotu v části **Název**. |
   | **Stávající interní systém vyrovnávání zatížení** | Název interního systému vyrovnávání zatížení, který jste vytvořili v kroku 3. |
   | **Port sondy** | Port sondy, který chcete použít interní horečnatou systému vyrovnávání zatížení. Šablona používá 59999 ve výchozím nastavení, ale tuto hodnotu můžete změnit. |
   | &nbsp; | &nbsp; |

1. Pokud souhlasíte s podmínkami, zaškrtněte **políčko Souhlasím s výše uvedenými podmínkami.** Vyberte **Nákup,** chcete-li dokončit nasazení šablony rychlého startu. 
1. Chcete-li sledovat nasazení, vyberte nasazení z ikony **zvonek oznámení** v horní navigační banner nebo přejděte na **skupinu prostředků** na webu Azure Portal. V části **Nastavení**vyberte **Nasazení** a zvolte nasazení **Microsoft.Template.** 

>[!NOTE]
>Pokud se vaše nasazení nezdaří v polovině, budete muset ručně [odebrat nově vytvořený naslouchací proces](#remove-the-availability-group-listener) pomocí prostředí PowerShell před opětovným nasazením šablony rychlého startu **101-sql-vm-aglistener-setup.** 

## <a name="remove-the-availability-group-listener"></a>Odebrání naslouchací proces skupiny dostupnosti
Pokud později potřebujete odebrat naslouchací proces skupiny dostupnosti, který šablona nakonfigurovala, musíte projít poskytovatelem prostředků virtuálního počítače SQL. Vzhledem k tomu, že naslouchací proces je registrován prostřednictvím zprostředkovatele prostředků virtuálního virtuálního provozu SQL, stačí jej odstranění prostřednictvím SQL Server Management Studio je nedostatečná. 

Nejlepší metodou je odstranit prostřednictvím zprostředkovatele prostředků virtuálního virtuálního připojení SQL pomocí následujícího fragmentu kódu v prostředí PowerShell. Tím odeberete metadata posluchače skupiny dostupnosti z poskytovatele prostředků virtuálního virtuálního připojení SQL. Také fyzicky odstraní naslouchací proces ze skupiny dostupnosti. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Běžné chyby
Tato část popisuje některé známé problémy a jejich možné řešení. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Naslouchací\<proces skupiny dostupnosti pro skupinu dostupnosti " AG-Name>' již existuje.
Vybraná skupina dostupnosti použitá v šabloně rychlého startu Azure pro naslouchací proces skupiny dostupnosti již obsahuje naslouchací proces. Buď je fyzicky v rámci skupiny dostupnosti, nebo jeho metadata zůstanou v rámci zprostředkovatele prostředků virtuálního virtuálního připojení SQL. Před opětovným nasazením šablony rychlého spuštění **101-sql-vm-aglistener-setup** odeberte naslouchací proces pomocí [prostředí PowerShell.](#remove-the-availability-group-listener) 

### <a name="connection-only-works-from-primary-replica"></a>Připojení funguje pouze z primární repliky
Toto chování je pravděpodobně z neúspěšného nasazení šablony **101-sql-vm-aglistener-setup,** které ponechalo konfiguraci interního nástroje pro vyrovnávání zatížení v nekonzistentním stavu. Ověřte, zda back-endový fond obsahuje seznam skupindostupnosti a že existují pravidla pro sondu stavu a pro pravidla vyrovnávání zatížení. Pokud něco chybí, konfigurace vnitřního nástroje pro vyrovnávání zatížení je nekonzistentní stav. 

Chcete-li toto chování vyřešit, odeberte naslouchací proces pomocí [prostředí PowerShell](#remove-the-availability-group-listener), odstraňte interní vyvyřič zatížení prostřednictvím portálu Azure a začněte znovu v kroku 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - lze aktualizovat pouze seznam virtuálních strojů SQL
K této chybě může dojít při nasazování šablony **nastavení 101-sql-vm-aglistener-setup,** pokud byl posluchač odstraněn prostřednictvím sql server management studio (SSMS), ale nebyl odstraněn z poskytovatele prostředků virtuálního počítače SQL. Odstranění naslouchací proces prostřednictvím SSMS neodebere metadata naslouchací proces z poskytovatele prostředků virtuálního virtuálního připojení SQL. Naslouchací proces musí být odstraněn z poskytovatele prostředků prostřednictvím [prostředí PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>Účet domény neexistuje.
Tato chyba může mít dvě příčiny. Buď zadaný účet domény neexistuje, nebo mu chybí data [hlavního uživatelského jména (UPN).](/windows/desktop/ad/naming-properties#userprincipalname) Šablona **101-sql-vm-ag-setup** očekává, že účet domény ve formuláři UPN (to znamená ), *user@domain.com*ale některé účty domény mohou chybět. K tomu obvykle dochází, když byl místní uživatel migrován jako první účet správce domény, když byl server povýšen na řadič domény, nebo když byl uživatel vytvořen prostřednictvím prostředí PowerShell. 

Ověřte, zda účet existuje. Pokud ano, můžete se narazit na druhou situaci. Chcete-li jej vyřešit, postupujte takto:

1. V řadiči domény otevřete okno **Uživatelé a počítače služby Active Directory** z možnosti **Nástroje** ve **Správci serveru**. 
2. Přejděte na účet výběrem **možnosti Uživatelé** v levém podokně.
3. Klepněte pravým tlačítkem myši na účet a vyberte **příkaz Vlastnosti**.
4. Vyberte kartu **Účet.** Pokud je pole **přihlašovací jméno uživatele** prázdné, je příčinou chyby. 

    ![Prázdný uživatelský účet označuje chybějící upn](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Vyplňte pole **přihlašovací jméno uživatele** tak, aby odpovídalo jménu uživatele, a v rozevíracím seznamu vyberte správnou doménu. 
6. Vyberte **Použít,** chcete-li uložit změny, a zavřete dialogové okno tak, že vyberete **OK**. 

Po provedení těchto změn zkuste znovu nasadit šablonu rychlého startu Azure. 



## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled virtuálních měn SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy pro virtuální servery SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pokyny k cenovým informacím pro virtuální servery SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro virtuální servery SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Přepínání licenčních modelů pro virtuální modul SQL Server](virtual-machines-windows-sql-ahb.md)



