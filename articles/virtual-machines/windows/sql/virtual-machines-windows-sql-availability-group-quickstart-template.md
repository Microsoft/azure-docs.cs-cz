---
title: Vytvoření služby WSFC, naslouchací proces a nakonfigurujte ILB pro skupiny dostupnosti Always On na virtuálním počítači SQL serveru pomocí šablony Azure Quickstart
description: Použití šablon rychlý start Azure zjednodušuje proces vytváření skupin dostupnosti pro virtuální počítače s SQL serverem v Azure pomocí šablony k vytvoření clusteru, připojí virtuální počítače s SQL do clusteru, vytvořte naslouchací proces a nakonfigurujte ILB.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9be8717bc9b1d15a59486edf206dd0657a711c06
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360247"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>Vytvoření služby WSFC, naslouchací proces a nakonfigurujte ILB pro skupiny dostupnosti Always On na virtuálním počítači SQL serveru pomocí šablony Azure Quickstart
Tento článek popisuje, jak pomocí šablon Azure Quickstart částečně automatizovat nasazení konfigurace dostupnosti skupin Always On pro SQL Server Virtual Machines v Azure. Existují dvě šablony Quickstart pro Azure, které se používají v tomto procesu. 

   | Šablona | Popis |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Vytvoří Cluster převzetí služeb při selhání Windows a připojí k němu virtuální počítače SQL serveru. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Vytvoří naslouchací proces skupiny dostupnosti a nakonfiguruje interní nástroj pro vyrovnávání zatížení. |
   | &nbsp; | &nbsp; |

Ostatní části Konfigurace skupiny dostupnosti je nutné provést ručně, například vytváření skupiny dostupnosti a vytvoření interního nástroje pro vyrovnávání zatížení. Tento článek obsahuje posloupnost automatizované a ruční kroky.
 

## <a name="prerequisites"></a>Požadavky 
K automatizaci instalací skupiny dostupnosti Always On pomocí šablony pro rychlý start, musí už máte splněné následující požadavky: 
- [Předplatného Azure](https://azure.microsoft.com/free/).
- Skupina prostředků se [řadič domény](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-forest). 
- Jeden nebo více připojených k doméně [virtuálních počítačů v Azure spuštěné systému SQL Server 2016 (nebo vyšší) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) ve stejné sadě nebo dostupnost zóně dostupnosti, které byly [zaregistrované u poskytovatele prostředků SQL VM](#register-existing-sql-vm-with-new-resource-provider).  

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Registrace existujícího virtuálního počítače SQL pomocí nového poskytovatele prostředků
Protože tyto skupiny dostupnosti, které šablony pro rychlý start Azure závisí na poskytovateli prostředků virtuálního počítače s SQL (Microsoft.SqlVirtualMachine) musí být stávající virtuální počítače SQL serveru zaregistrované u poskytovatele prostředků virtuálního počítače s SQL. Tento krok přeskočte, pokud jste vytvořili virtuální počítač s SQL serverem od prosince 2018, jsou všechny virtuální počítače SQL serveru, vytvořené po tomto datu se automaticky registraci. Tato část vysvětluje, jak zaregistrovat u poskytovatele pomocí webu Azure portal, ale můžete také použít [Powershellu](virtual-machines-windows-sql-ahb.md#powershell). 

  >[!IMPORTANT]
  > Pokud odstraníte váš prostředek virtuálního počítače s SQL serverem, přejdete zpět na pevně zakódované licence nastavení bitové kopie. 

1. Otevřete na webu Azure portal a přejděte do **všechny služby**. 
1. Přejděte do **předplatná** a vyberte předplatné, které vás zajímají.  
1. V **předplatná** okno, přejděte na **poskytovatele prostředků**. 
1. Typ `sql` ve filtru, abyste vyvolali se zprostředkovatelé prostředků souvisejících s SQL. 
1. Vyberte buď *zaregistrovat*, *přeregistrovat*, nebo *Unregister* pro **Microsoft.SqlVirtualMachine** poskytovateli v závislosti na vaší požadovanou akci. 

  ![Upravit poskytovatele](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>Krok 1 – vytvoření služby WSFC a připojte se ke clusteru pomocí šablony pro rychlý start k virtuální počítače s SQL serverem 
Jakmile se vaše virtuální počítače s SQL serverem jste zaregistrovali pomocí nového poskytovatele prostředků virtuálního počítače s SQL, se můžete zapojit vaše virtuální počítače SQL serveru do *SqlVirtualMachineGroup*. Tento prostředek definuje metadata clusteru převzetí služeb při selhání Windows, včetně verze, edice, plně kvalifikovaný název domény, účty služby AD pro správu clusteru a účtu úložiště jako disk s kopií cloudu. Přidání virtuálního počítače SQL serveru do *SqlVirtualMachineGroup* bootstraps služby clusteru převzetí služeb při selhání Windows a připojí virtuální počítače SQL serveru do clusteru. Tento krok je automatické s **101--vm-ag – instalační program systému sql** šablonu pro rychlý start a je možné implementovat pomocí následujících kroků:

1. Přejděte [ **101--vm-ag – instalační program systému sql** ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) šablonu pro rychlý start a vyberte **nasadit do Azure** spustíte šablonu pro rychlý start na webu Azure portal.
1. Vyplňte požadovaná pole ke konfiguraci clusteru převzetí služeb při selhání Windows metadata. Volitelná pole může být ponecháno prázdné.

    Následující tabulka zobrazuje hodnoty potřebné pro šablony: 

   | **Pole** | Hodnota |
   | --- | --- |
   | **Předplatné** |  Předplatné, kde existují virtuální počítače s SQL serverem. |
   |**Skupina prostředků** | Skupina prostředků, kde jsou umístěné vaše virtuální počítače s SQL serverem. | 
   |**Název clusteru převzetí služeb při selhání** | Požadovaný název pro nový Cluster převzetí služeb při selhání Windows. |
   | **Existující seznam virtuálních počítačů** | Virtuální počítače SQL serveru, který chcete účast ve skupině dostupnosti a v důsledku toho být součástí tohoto nového clusteru. Tyto hodnoty oddělte čárkou a mezerou (ex: SQLVM1, SQLVM2). |
   | **Verze SQL serveru** | Z rozevíracího seznamu vyberte verzi systému SQL Server virtuální počítače s SQL serverem. Momentálně se podporuje jenom imagí SQL 2016 a SQL 2017 jsou podporovány. |
   | **Existující plně kvalifikovaný název domény** | Stávající plně kvalifikovaný název domény pro doménu, ve kterém jsou umístěny virtuální počítače s SQL serverem. |
   | **Existující účet domény** | Existující účet domény, který má přístup správce k serveru SQL Server. | 
   | **Domain Account Password** | Heslo pro účet výše uvedené domény. | 
   | **Existující účet služby Sql** | Účet uživatele domény, který se používá k řízení službu systému SQL Server. Tyto informace lze nalézt pomocí [ **Správce konfigurace systému SQL Server**](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager?view=sql-server-2017). |
   | **Heslo služby SQL** | Heslo použité uživatelským účtem domény, který řídí službu systému SQL Server. |
   | &nbsp; | &nbsp; |

1. Pokud souhlasíte s podmínkami a ujednáními, zaškrtněte políčko vedle položky **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře** a vyberte **nákupní** pro dokončení nasazení šablony rychlý start. 
1. K monitorování vašeho nasazení, vyberte nasazení **oznámení** sek ikony v horním navigačním panelu nápisu nebo přejděte na vaše **skupiny prostředků** na webu Azure Portal, vyberte  **Nasazení** v **nastavení** pole a možnost nasazení "Microsoft.Template". 

## <a name="step-2---manually-create-the-availability-group"></a>Krok 2: ruční vytvoření skupiny dostupnosti 
Ruční vytvoření skupiny dostupnosti jako obvykle, buď pomocí [PowerShell](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017), [SQL Server Management Studio](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) nebo [příkazů jazyka Transact-SQL](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017). 

  >[!IMPORTANT]
  > Proveďte **není** v tuto chvíli vytvořit naslouchací proces, protože toto je automatizováno pomocí **101--vm-aglistener – instalační program systému sql** šablonu pro rychlý start v kroku 4. 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>Krok 3: ruční vytvoření interního nástroje pro vyrovnávání zatížení (ILB)
Always On naslouchací proces skupiny dostupnosti (AG) vyžaduje vnitřní Azure zatížení nástroje pro vyrovnávání (ILB). ILB zajišťující "plovoucí" IP adresu naslouchacího procesu AG, které umožňuje rychlejší převzetí služeb při selhání a opětovné připojení. Pokud virtuální počítače SQL serveru ve skupině dostupnosti jsou součástí stejné skupiny dostupnosti, pak můžete použít základní nástroje pro vyrovnávání zatížení; v opačném případě budete muset použít standardní nástroje pro vyrovnávání zatížení.  **ILB musí být ve stejné virtuální síti jako instance virtuálního počítače s SQL serverem.** ILB jenom je potřeba vytvořit, zbývající konfiguraci (například back-endový fond, sondy stavu a vyrovnávání zatížení pravidla) se zpracovává souborem **101--vm-aglistener – instalační program systému sql** šablonu pro rychlý start v kroku 4. 

1. Na webu Azure Portal otevřete skupinu prostředků obsahující virtuální počítače systému SQL Server. 
2. Ve skupině prostředků, klikněte na tlačítko **přidat**.
3. Vyhledejte **nástroj pro vyrovnávání zatížení** a potom ve výsledcích hledání vyberte **Load Balancer**, která je publikována serverem **Microsoft**.
4. Na **nástroje pro vyrovnávání zatížení** okna, klikněte na tlačítko **vytvořit**.
5. V **vytvořit nástroj pro vyrovnávání zatížení** dialogové okno nástroje pro vyrovnávání zatížení nakonfigurujte následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název, který představuje nástroj pro vyrovnávání zatížení. Například **sqlLB**. |
   | **Typ** |**Interní**: Většina implementací pomocí interního nástroje, které umožňuje aplikacím v rámci stejné virtuální síti připojit ke skupině dostupnosti.  </br> **Externí**: Umožňuje aplikacím pro připojení ke skupině dostupnosti prostřednictvím veřejného Internetu. |
   | **Virtuální síť** |Vyberte virtuální síť, která instance systému SQL Server jsou v. |
   | **Podsíť** |Vyberte podsíť, která instance systému SQL Server jsou v. |
   | **Přiřazení IP adresy** |**Statická** |
   | **Privátní IP adresa** |Zadejte dostupnou IP adresu z podsítě. Při vytváření naslouchacího procesu v clusteru, použijte tuto IP adresu.|
   | **Předplatné** |Pokud máte více předplatných, může se zobrazit tato pole. Vyberte předplatné, které chcete přidružit k tomuto prostředku. Je obvykle stejné předplatné jako všechny prostředky pro skupinu dostupnosti. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, instance SQL serveru jsou v. |
   | **Umístění** |Vyberte oblast Azure, které jsou instance SQL serveru v. |
   | &nbsp; | &nbsp; |

6. Vyberte **Vytvořit**. 


  >[!NOTE]
  > Standardní SKU se kvůli kompatibilitě s Load balanceru úrovně Standard by měl mít prostředek veřejné IP pro každý virtuální počítač s SQL serverem. Určit SKU prostředek veřejné IP adresy Virtuálního počítače, přejděte na vaše **skupiny prostředků**vyberte vaše **veřejnou IP adresu** prostředek požadovaný virtuální počítač SQL Server a vyhledejte hodnotu v rámci **SKU**  z **přehled** podokně. 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>Krok 4 – vytvoření naslouchacího procesu AG a nakonfigurovat ILB šablonu pro rychlý start

Vytvoření naslouchacího procesu skupiny dostupnosti a konfigurace nástroje pro vyrovnávání interní zatížení (ILB) automaticky **101--vm-aglistener – instalační program systému sql** zřizuje virtuální Microsoft.SqlVirtualMachine/Sql šablony rychlý start Počítač naslouchacího procesu skupiny dostupnosti se/skupiny prostředků. **101--vm-aglistener – instalační program systému sql** šablony rychlý start, prostřednictvím poskytovatele prostředků virtuálního počítače s SQL, provádí následující akce:

 - Nakonfiguruje nastavení sítě pro cluster a ILB. 
 - Nakonfiguruje ILB back-endový fond, sondy stavu a vyrovnávání zatížení pravidla.
 - Vytvoří naslouchacího procesu AG s danou IP adresu a název.

Pokud chcete nakonfigurovat ILB a vytvoření naslouchacího procesu AG, postupujte takto:
1. Přejděte [ **101--vm-aglistener – instalační program systému sql** ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) šablonu pro rychlý start a vyberte **nasadit do Azure** spustíte šablonu pro rychlý start na webu Azure portal.
1. Vyplňte požadovaná pole Konfigurace ILB a vytvořit naslouchací proces skupiny dostupnosti. Volitelná pole může být ponecháno prázdné. 

    Následující tabulka zobrazuje hodnoty potřebné pro šablony: 

   | **Pole** | Hodnota |
   | --- | --- |
   |**Skupina prostředků** | Skupina prostředků, kde existují virtuální počítače s SQL serverem a skupiny dostupnosti. | 
   |**Stávající uzel clusteru převzetí služeb při selhání** | Název clusteru, ke které je připojený virtuální počítače s SQL serverem. |
   | **Existující skupina dostupnosti Sql**| Název, který vaše virtuální počítače s SQL serverem jsou součástí skupiny dostupnosti. |
   | **Existující seznam virtuálních počítačů** | Názvy virtuálních počítačů SQL Server, které jsou součástí skupiny dostupnosti výše uvedené. Názvy musí být odděleny čárkou a mezerou (ex: SQLVM1, SQLVM2). |
   | **Existující plně kvalifikovaný název domény** | Stávající plně kvalifikovaný název domény pro doménu, ve kterém jsou umístěny virtuální počítače s SQL serverem. |
   | **Naslouchací proces** | Název DNS, který chcete přiřadit k naslouchacímu procesu. Ve výchozím nastavení tato šablona určuje název aglistener, ale lze jej změnit. |
   | **Port naslouchacího procesu** | Port, který chcete používat naslouchací proces. Obvykle tento port by měl být výchozí port 1433, a v důsledku toho jde v této šabloně zadané číslo portu. Ale pokud došlo ke změně výchozího portu, potom na port naslouchacího procesu vhodnější použít tuto hodnotu. | 
   | **Existující virtuální síť** | Název sítě vNet, kde jsou umístěné vaše virtuální počítače s SQL serverem a ILB. |
   | **Existující podsítě** | *Název* interní podsítě virtuálních počítačů s SQL serverem (ex: výchozí). Tato hodnota se dá určit tak, že přejdete na vaše **skupiny prostředků**, kde vyberou vaše **virtuální sítě**, kde vyberou **podsítě** pod **nastavení**podokně a zkopírování hodnoty v rámci **název**. |
   | **Existující interního nástroje Load Balancer** | Název ILB, kterou jste vytvořili v kroku 3. |
   | **Port testu** | Port testu, který chcete, aby nástroje pro vyrovnávání zatížení používat. Šablona používá 59999 ve výchozím nastavení, ale tato hodnota může být změněna. |
   | &nbsp; | &nbsp; |

1. Pokud souhlasíte s podmínkami a ujednáními, zaškrtněte políčko vedle položky **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře** a vyberte **nákupní** pro dokončení nasazení šablony rychlý start. 
1. K monitorování vašeho nasazení, vyberte nasazení **oznámení** sek ikony v horním navigačním panelu nápisu nebo přejděte na vaše **skupiny prostředků** na webu Azure Portal, vyberte  **Nasazení** v **nastavení** pole a možnost nasazení "Microsoft.Template". 

  >[!NOTE]
  >Pokud se nasazení nezdaří poloviční způsob, jak prostřednictvím, budete muset ručně [odstranit nově vytvořený naslouchací proces](#remove-availability-group-listener) před opětovného nasazení pomocí prostředí PowerShell **101--vm-aglistener – instalační program systému sql** šablonu pro rychlý start. 

## <a name="remove-availability-group-listener"></a>Odebrání naslouchacího procesu skupiny dostupnosti
Pokud později potřebujete odebrat naslouchacího procesu skupiny dostupnosti nakonfigurováno pomocí šablony, musíte projít přes poskytovatele prostředků virtuálního počítače s SQL. Protože naslouchací proces je zaregistrované prostřednictvím poskytovatele prostředků virtuálního počítače s SQL, pouhým odstraněním přes SQL Server Management Studio není dostatečná. Je ve skutečnosti je potřeba odstranit prostřednictvím poskytovatele prostředků virtuálního počítače SQL pomocí Powershellu. To odebere metadata naslouchacího procesu AG od zprostředkovatele prostředků virtuálního počítače s SQL a fyzicky odstraní naslouchací proces skupiny dostupnosti. 

Následující fragment kódu odstraní naslouchacího procesu skupiny dostupnosti SQL, od obou tohoto poskytovatele prostředků SQL a ze skupiny dostupnosti: 

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="known-issues-and-errors"></a>Známé problémy a chyby
Tato část popisuje některé známé problémy a jejich možná řešení. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Naslouchací proces skupiny dostupnosti pro skupinu dostupnosti "\<AG-Name >' již existuje.
Vybraná skupina dostupnosti skupiny už používá v naslouchacím procesu AG šablona Azure Quickstart obsahuje naslouchací proces, fyzicky v rámci skupiny dostupnosti, nebo jako metadata v rámci poskytovatele prostředků virtuálního počítače s SQL.  Odebrání naslouchacího procesu pomocí [Powershellu](#remove-availability-group-listener) před znovu se nasazuje **101--vm-aglistener – instalační program systému sql** šablonu pro rychlý start. 

### <a name="connection-only-works-from-primary-replica"></a>Připojení funguje jenom z primární repliky
Toto chování je pravděpodobně z nezdařené **101--vm-aglistener – instalační program systému sql** nasazení šablony byste museli opustit ILB konfigurace v nekonzistentním stavu. Ověřte, že back-endový fond obsahuje skupinu dostupnosti a existují pravidla pro sondu stavu a pro pravidla Vyrovnávání zatížení. Pokud něco chybí, je konfigurace ILB nekonzistentním stavu. 

Chcete-li tento problém vyřešit, odeberte naslouchací proces, pomocí [PowerShell](#remove-availability-group-listener), odstraňte interního nástroje Load Balancer přes Azure portal a znovu začít rychlým [kroku 3](#step-3---manually-create-the-internal-load-balanced-ilb). 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>Chybného požadavku – je možné aktualizovat pouze seznam virtuálních počítačů SQL
K této chybě může dojít při nasazování **101--vm-aglistener – instalační program systému sql** šablony, pokud naslouchacího procesu se odstranila přes SQL Server Management Studio (SSMS), nebyl ale odstraněn z poskytovatele prostředků virtuálního počítače s SQL. Odstraňuje se naslouchací proces prostřednictvím aplikace SSMS neodebere metadata naslouchacího procesu od zprostředkovatele prostředků virtuálního počítače SQL; naslouchací proces je nutné odstranit z poskytovatele prostředků pomocí [Powershellu](#remove-availability-group-listener). 


## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled virtuálního počítače s SQL serverem](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k virtuálnímu počítači SQL serveru](virtual-machines-windows-sql-server-iaas-faq.md)
* [Doprovodné materiály k cenám virtuálního počítače s SQL serverem](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Zpráva k vydání verze virtuálního počítače s SQL serverem](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Přepínání licenční modely pro virtuální počítač s SQL serverem](virtual-machines-windows-sql-ahb.md)



