---
title: Konfigurace skupiny dostupnosti (šablona Azure pro rychlý Start)
description: Pomocí šablon Azure pro rychlý Start můžete vytvořit cluster s podporou převzetí služeb při selhání Windows, připojit SQL Server virtuální počítače ke clusteru, vytvořit naslouchací proces a nakonfigurovat interní nástroj pro vyrovnávání zatížení v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d7dfe010a3f4a1559454c49545af81eb14797bf1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359910"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Použití šablon pro rychlý Start Azure ke konfiguraci skupiny dostupnosti pro SQL Server na virtuálním počítači Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak pomocí šablon Azure pro rychlý Start částečně automatizovat nasazení konfigurace skupiny dostupnosti Always On pro SQL Server virtuálních počítačů v Azure. V tomto procesu se používají dvě šablony pro rychlý Start Azure: 

   | Template (Šablona) | Popis |
   | --- | --- |
   | [101-SQL-VM-AG-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Vytvoří cluster s podporou převzetí služeb při selhání Windows a připojí k němu SQL Server virtuální počítače. |
   | [101-SQL-VM-aglistener-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Vytvoří naslouchací proces skupiny dostupnosti a nakonfiguruje interní nástroj pro vyrovnávání zatížení. Tuto šablonu lze použít pouze v případě, že byl vytvořen cluster s podporou převzetí služeb při selhání systému Windows pomocí šablony **101-SQL-VM-AG-Setup** . |
   | &nbsp; | &nbsp; |

Ostatní části Konfigurace skupiny dostupnosti se musí provádět ručně, například vytvoření skupiny dostupnosti a vytvoření interního nástroje pro vyrovnávání zatížení. Tento článek poskytuje posloupnost automatizovaných a ručních kroků.

I když tento článek používá šablony pro rychlý Start Azure ke konfiguraci prostředí skupiny dostupnosti, je taky možné ho použít [Azure Portal](availability-group-azure-portal-configure.md), [PowerShellu nebo rozhraní příkazového řádku Azure](availability-group-az-commandline-configure.md)nebo taky [ručně](availability-group-manually-configure-tutorial.md) . 
 

## <a name="prerequisites"></a>Předpoklady 
K automatizaci nastavení skupiny dostupnosti Always On pomocí šablon pro rychlý Start musíte mít následující požadavky: 
- [Předplatné Azure](https://azure.microsoft.com/free/)
- Skupina prostředků s řadičem domény. 
- Jeden nebo víc virtuálních počítačů připojených k doméně [v Azure se spuštěným SQL Server 2016 (nebo novější) Enterprise Edition](./create-sql-vm-portal.md) , které jsou ve stejné skupině dostupnosti nebo v zóně dostupnosti a které jsou [zaregistrované v rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md).  
- Dvě dostupné (nepoužívají se v žádné entitě) IP adresy: jeden pro interní nástroj pro vyrovnávání zatížení a jeden pro naslouchací proces skupiny dostupnosti ve stejné podsíti jako skupina dostupnosti. Pokud se používá existující Nástroj pro vyrovnávání zatížení, budete potřebovat jenom jednu dostupnou IP adresu.  

## <a name="permissions"></a>Oprávnění
Následující oprávnění jsou nutná ke konfiguraci skupiny dostupnosti Always On pomocí šablon Azure pro rychlý Start: 

- Existující účet uživatele domény, který má v doméně oprávnění **vytvořit objekt počítače** .  Například účet správce domény má obvykle dostatečná oprávnění (například: account@domain.com ). _Tento účet by měl být taky součástí místní skupiny správců na každém virtuálním počítači, aby se vytvořil cluster._
- Uživatelský účet domény, který řídí SQL Server. 


## <a name="create-cluster"></a>Vytvoření clusteru
Po zaregistrování SQL Server virtuálních počítačů s rozšířením agenta SQL IaaS můžete připojit své SQL Server virtuální počítače k *SqlVirtualMachineGroups*. Tento prostředek definuje metadata clusteru s podporou převzetí služeb při selhání systému Windows. Metadata obsahují verze, edici, plně kvalifikovaný název domény, účty služby Active Directory pro správu clusteru i SQL Server a účet úložiště jako disk s kopií cloudu. 

Přidáním SQL Server virtuálních počítačů do skupiny prostředků *SqlVirtualMachineGroups* se služba Cluster s podporou převzetí služeb při selhání systému Windows pokusí vytvořit cluster a pak tyto SQL Server virtuální počítače připojí do tohoto clusteru. Tento krok je automatizovaný pomocí šablony rychlého startu **101-SQL-VM-AG-Setup** . Můžete ji implementovat pomocí následujících kroků:

1. Otevřete šablonu pro rychlý Start [**101-SQL-VM-AG-Setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) . Pak vyberte **nasadit do Azure** a otevřete šablonu pro rychlý start v Azure Portal.
1. Vyplňte požadovaná pole a nakonfigurujte metadata pro cluster s podporou převzetí služeb při selhání systému Windows. Volitelná pole můžete nechat prázdná.

   V následující tabulce jsou uvedeny nezbytné hodnoty pro šablonu: 

   | **Pole** | Hodnota |
   | --- | --- |
   | **Předplatné** |  Předplatné, ve kterém SQL Server virtuální počítače existují. |
   |**Skupina prostředků** | Skupina prostředků, ve které se nacházejí SQL Server virtuálních počítačů. | 
   |**Název clusteru převzetí služeb při selhání** | Název, který chcete pro nový cluster s podporou převzetí služeb při selhání systému Windows. |
   | **Existující seznam virtuálních počítačů** | SQL Server virtuální počítače, které chcete zapojit do skupiny dostupnosti a které jsou součástí tohoto nového clusteru. Oddělte tyto hodnoty čárkou a mezerou (například: *SQLVM1, SQLVM2*). |
   | **Verze SQL Server** | SQL Server verze vašich SQL Serverch virtuálních počítačů. Vyberte ho z rozevíracího seznamu. V současné době jsou podporovány pouze SQL Server 2016 a SQL Server 2017 bitové kopie. |
   | **Existující plně kvalifikovaný název domény** | Stávající plně kvalifikovaný název domény pro doménu, ve které se nachází vaše virtuální počítače s SQL Server. |
   | **Existující doménový účet** | Existující účet uživatele domény, který má v doméně oprávnění **vytvořit objekt počítače** jako [objekt CNO](/windows-server/failover-clustering/prestage-cluster-adds) , se vytvoří během nasazování šablony. Například účet správce domény má obvykle dostatečná oprávnění (například: account@domain.com ). *Tento účet by měl být taky součástí místní skupiny správců na každém virtuálním počítači, aby se vytvořil cluster.*| 
   | **Heslo účtu domény** | Heslo pro dříve uvedený účet uživatele domény. | 
   | **Existující účet služby SQL** | Uživatelský účet domény, který řídí [službu SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) během nasazování skupiny dostupnosti (například: account@domain.com ). |
   | **Heslo služby SQL** | Heslo, které používá účet uživatele domény, který řídí SQL Server. |
   | **Název sdílené složky v cloudu** | Vytvoří se nový účet Azure Storage, který se vytvoří a použije pro disk s kopií cloudu. Tento název můžete změnit. |
   | **\_Umístění artefaktů** | Toto pole je nastavené ve výchozím nastavení a nemělo by se měnit. |
   | **\_Token SaS umístění artefaktů** | Toto pole je úmyslně ponecháno prázdné. |
   | &nbsp; | &nbsp; |

1. Pokud souhlasíte s podmínkami a ujednáními, zaškrtněte políčko Souhlasím **s podmínkami a ujednáními uvedenými nahoře** . Pak výběrem **koupit** dokončíte nasazení šablony pro rychlý Start. 
1. Pokud chcete monitorovat vaše nasazení, buď vyberte nasazení z ikony zvonku **oznámení** v horním navigačním panelu nebo v Azure Portal přejít na **skupinu prostředků** . V části **Nastavení** vyberte **nasazení** a zvolte nasazení **Microsoft. template** . 

>[!NOTE]
> Přihlašovací údaje zadané během nasazování šablony se ukládají jenom pro délku nasazení. Po dokončení nasazení se tato hesla odeberou. Po přidání dalších SQL Server virtuálních počítačů do clusteru budete požádáni o jejich opětovné zadání. 



## <a name="validate-cluster"></a>Ověřit cluster 

Aby byl cluster s podporou převzetí služeb při selhání podporovaný společností Microsoft, musí projít ověřením clusteru. Připojte se k virtuálnímu počítači pomocí preferované metody, jako je například protokol RDP (Remote Desktop Protocol) (RDP), a ověřte, že cluster před pokračováním projde ověření. Pokud to neuděláte, zastavte cluster v nepodporovaném stavu. 

Cluster můžete ověřit pomocí Správce clusteru s podporou převzetí služeb při selhání (FCM) nebo následujícího příkazu PowerShellu:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```


## <a name="create-availability-group"></a>Vytvořit skupinu dostupnosti 
Ručně vytvořte skupinu dostupnosti obvyklým způsobem, a to pomocí [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShellu](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)nebo [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> V tuto chvíli *nevytvářejte naslouchací* proces, protože šablona pro rychlý Start **101-SQL-VM-aglistener-Setup**  je automaticky v kroku 4. 

## <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Naslouchací proces skupiny dostupnosti Always On vyžaduje interní instanci Azure Load Balancer. Interní nástroj pro vyrovnávání zatížení poskytuje "plovoucí" IP adresu pro naslouchací proces skupiny dostupnosti, který umožňuje rychlejší převzetí služeb při selhání a opětovné připojení. Pokud jsou virtuální počítače s SQL Server ve skupině dostupnosti součástí stejné sady dostupnosti, můžete použít základní nástroj pro vyrovnávání zatížení. V opačném případě je nutné použít standardní nástroj pro vyrovnávání zatížení. 

> [!IMPORTANT]
> Interní nástroj pro vyrovnávání zatížení by měl být ve stejné virtuální síti jako instance virtuálních počítačů SQL Server. 

Stačí vytvořit interní nástroj pro vyrovnávání zatížení. V kroku 4 šablona pro rychlý Start **101-SQL-VM-aglistener-Setup** zpracovává zbytek konfigurace (například fond back-endu, sondu stavu a pravidla vyrovnávání zatížení). 

1. V Azure Portal otevřete skupinu prostředků, která obsahuje SQL Server virtuálních počítačů. 
2. Ve skupině prostředků vyberte **Přidat**.
3. Vyhledejte **Nástroj pro vyrovnávání zatížení**. Ve výsledcích hledání vyberte položku **Load Balancer**, která je publikována společností **Microsoft**.
4. V okně **Load Balancer** vyberte **vytvořit**.
5. V dialogovém okně **vytvořit nástroj pro vyrovnávání zatížení** nakonfigurujte nástroj pro vyrovnávání zatížení následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Zadejte textový název, který představuje nástroj pro vyrovnávání zatížení. Zadejte například **sqlLB**. |
   | **Typ** |**Interní**: většina implementací používá interní nástroj pro vyrovnávání zatížení, který umožňuje aplikacím v rámci stejné virtuální sítě připojit se ke skupině dostupnosti.  </br> **Externí**: umožňuje aplikacím připojit se ke skupině dostupnosti prostřednictvím veřejného internetového připojení. |
   | **Virtuální síť** | Vyberte virtuální síť, ve které se nacházejí instance SQL Server. |
   | **Podsíť** | Vyberte podsíť, ve které jsou instance SQL Server. |
   | **Přiřazení IP adresy** |**staticky**. |
   | **Privátní IP adresa** | Zadejte dostupnou IP adresu z podsítě. |
   | **Předplatné** |Pokud máte více předplatných, může se toto pole zobrazit. Vyberte předplatné, které chcete k tomuto prostředku přidružit. Obvykle se jedná o stejné předplatné, jako všechny prostředky pro skupinu dostupnosti. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, ve které jsou instance SQL Server. |
   | **Umístění** |Vyberte umístění Azure, ve kterém jsou instance SQL Server. |
   | &nbsp; | &nbsp; |

6. Vyberte **Vytvořit**. 


>[!IMPORTANT]
> Prostředek veřejné IP adresy pro každý virtuální počítač SQL Server by měl mít standardní SKU, aby byl kompatibilní s nástrojem Load Balancer úrovně Standard. Pokud chcete zjistit SKU prostředku veřejné IP adresy vašeho virtuálního počítače, přejděte do **skupiny prostředků**, vyberte prostředek **veřejné IP adresy** pro virtuální počítač SQL Server a vyhledejte hodnotu v části **SKU** v podokně **Přehled** . 

## <a name="create-listener"></a>Vytvořit naslouchací proces 

Vytvořte naslouchací proces skupiny dostupnosti a automaticky nakonfigurujte interní nástroj pro vyrovnávání zatížení pomocí šablony pro rychlý Start **101-SQL-VM-aglistener-Setup**  . Šablona zřídí prostředek Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Šablona pro rychlý Start  **101-SQL-VM-aglistener-Setup** prostřednictvím rozšíření agenta SQL IaaS provádí následující akce:

- Vytvoří nový prostředek IP adresy front-endu (na základě hodnoty IP adresy uvedené během nasazení) pro naslouchací proces. 
- Konfiguruje nastavení sítě pro cluster a interní nástroj pro vyrovnávání zatížení. 
- Konfiguruje fond back-end pro interní nástroj pro vyrovnávání zatížení, sondu stavu a pravidla vyrovnávání zatížení.
- Vytvoří naslouchací proces skupiny dostupnosti s danou IP adresou a názvem.
 
>[!NOTE]
> V případě, že byl vytvořen cluster s podporou převzetí služeb při selhání systému Windows pomocí šablony **101-SQL-VM-AG-Setup** , můžete použít možnost **101-SQL-VM-aglistener-Setup** .
   
   
Pokud chcete nakonfigurovat interní nástroj pro vyrovnávání zatížení a vytvořit naslouchací proces skupiny dostupnosti, udělejte toto:
1. Otevřete šablonu pro rychlý Start [101-SQL-VM-aglistener-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) a vyberte **nasadit do Azure** a začněte šablonu pro rychlé zprovoznění v Azure Portal.
1. Vyplňte požadovaná pole pro konfiguraci interního nástroje pro vyrovnávání zatížení a vytvořte naslouchací proces skupiny dostupnosti. Volitelná pole můžete nechat prázdná. 

   V následující tabulce jsou uvedeny nezbytné hodnoty pro šablonu: 

   | **Pole** | Hodnota |
   | --- | --- |
   |**Skupina prostředků** | Skupina prostředků, ve které existuje SQL Server virtuálních počítačů a skupin dostupnosti. | 
   |**Existující název clusteru s podporou převzetí služeb při selhání** | Název clusteru, ke kterému jsou připojené vaše virtuální počítače s SQL Server. |
   | **Existující skupina dostupnosti SQL**| Název skupiny dostupnosti, do které jsou vaše virtuální počítače s SQL Server součástí. |
   | **Existující seznam virtuálních počítačů** | Názvy SQL Serverch virtuálních počítačů, které jsou součástí dříve zmíněné skupiny dostupnosti. Názvy oddělte čárkou a mezerou (například: *SQLVM1, SQLVM2*). |
   | **Naslouchací proces** | Název DNS, který chcete přiřadit k naslouchacího procesu. Ve výchozím nastavení tato šablona určuje název "aglistener", ale můžete jej změnit. Název nesmí být delší než 15 znaků. |
   | **Port naslouchacího procesu** | Port, který má naslouchací proces používat. Tento port by měl být obvykle výchozí hodnota 1433. Toto je číslo portu, které šablona určuje. Pokud se ale změnil váš výchozí port, port naslouchacího procesu by měl místo toho použít tuto hodnotu. | 
   | **IP adresa naslouchacího procesu** | IP adresa, kterou má naslouchací proces používat. Tato adresa se vytvoří během nasazování šablony, takže ji zajistěte, aby se už nepoužívala.  |
   | **Existující podsíť** | Název interní podsítě vašich SQL Server virtuálních počítačů (například: *výchozí*). Tuto hodnotu můžete zjistit tak, že do **skupiny prostředků** vyberete virtuální síť, vybíráte **podsítě** v podokně **Nastavení** a zkopírujete hodnotu pod **názvem**. |
   | **Stávající interní Load Balancer** | Název interního nástroje pro vyrovnávání zatížení, který jste vytvořili v kroku 3. |
   | **Port testu paměti** | Port testu, který má používat interní nástroj pro vyrovnávání zatížení. Šablona používá standardně 59999, ale tuto hodnotu můžete změnit. |
   | &nbsp; | &nbsp; |

1. Pokud souhlasíte s podmínkami a ujednáními, zaškrtněte políčko Souhlasím **s podmínkami a ujednáními uvedenými nahoře** . Výběrem **koupit** dokončíte nasazení šablony pro rychlý Start. 
1. Pokud chcete monitorovat vaše nasazení, buď vyberte nasazení z ikony zvonku **oznámení** v horním navigačním panelu nebo v Azure Portal přejít na **skupinu prostředků** . V části **Nastavení** vyberte **nasazení** a zvolte nasazení **Microsoft. template** . 

>[!NOTE]
>Pokud vaše nasazení neprojde uprostřed, budete muset ručně [odebrat nově vytvořený naslouchací proces](#remove-listener) pomocí prostředí PowerShell, než znovu nasadíte šablonu pro rychlý Start **101-SQL-VM-aglistener-Setup** . 

## <a name="remove-listener"></a>Odebrat naslouchací proces
Pokud budete později potřebovat odstranit naslouchací proces skupiny dostupnosti, který je konfigurován šablonou, je nutné projít rozšířením agenta SQL IaaS. Protože je naslouchací proces zaregistrován prostřednictvím rozšíření agenta SQL IaaS, stačí ho odstranit prostřednictvím SQL Server Management Studio nedostatečné. 

Nejlepším způsobem je odstranit ho pomocí rozšíření agenta SQL IaaS pomocí následujícího fragmentu kódu v PowerShellu. Tím se z rozšíření agenta SQL IaaS Odebere metadata naslouchacího procesu skupiny dostupnosti. Také fyzicky odstraní naslouchací proces ze skupiny dostupnosti. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Běžné chyby
Tato část pojednává o některých známých problémech a jejich možných řešeních. 

**Naslouchací proces skupiny dostupnosti pro skupinu dostupnosti \<AG-Name> již existuje** . Vybraná skupina dostupnosti použitá v šabloně Azure pro rychlý Start pro naslouchací proces skupiny dostupnosti již obsahuje naslouchací proces. Buď je fyzicky v rámci skupiny dostupnosti, nebo jeho metadata zůstávají v rámci rozšíření agenta SQL IaaS. Před opětovným nasazením šablony pro rychlý Start **101-SQL-VM-aglistener-Setup** odeberte naslouchací proces pomocí [prostředí PowerShell](#remove-listener) . 

**Připojení funguje jenom z primární repliky** . Toto chování je nejspíš z neúspěšného nasazení šablony **instalace 101-SQL-VM-aglistener-Setup** , které opustilo konfiguraci interního nástroje pro vyrovnávání zatížení v nekonzistentním stavu. Ověřte, že back-end fond uvádí skupinu dostupnosti a že tato pravidla existují pro sondu stavu a pro pravidla vyrovnávání zatížení. Pokud cokoli chybí, konfigurace interního nástroje pro vyrovnávání zatížení je nekonzistentní stav. 

Pokud chcete toto chování vyřešit, odeberte naslouchací proces pomocí [PowerShellu](#remove-listener), odstraňte interní nástroj pro vyrovnávání zatížení prostřednictvím Azure Portal a znovu ho spusťte v kroku 3. 

**Důvodu chybného požadavku se dá aktualizovat jenom seznam virtuálních počítačů SQL** . K této chybě může dojít, pokud nasazujete šablonu **101-SQL-VM-aglistener-Setup** , pokud byl naslouchací proces odstraněn prostřednictvím SQL Server Management Studio (SSMS), ale nebyl odstraněn z rozšíření agenta SQL IaaS. Odstraněním naslouchacího procesu prostřednictvím SSMS se neodstraní metadata naslouchacího procesu z rozšíření agenta SQL IaaS. Naslouchací proces musí být odstraněn z poskytovatele prostředků prostřednictvím [PowerShellu](#remove-listener). 

**Doménový účet neexistuje** . Tato chyba může mít dvě příčiny. Buď zadaný doménový účet neexistuje, nebo chybí data [hlavního názvu uživatele (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) . Šablona **101-SQL-VM-AG-Setup** očekává účet domény ve formátu hlavního názvu uživatele (tj user@domain.com .), ale některé účty domény ho můžou chybět. K tomu obvykle dochází, když je místní uživatel migrován jako první účet správce domény, když byl server povýšen na řadič domény, nebo když byl uživatel vytvořen prostřednictvím PowerShellu. 

Ověřte, že účet existuje. Pokud k tomu dochází, možná budete pracovat v druhé situaci. Pokud ho chcete vyřešit, udělejte toto:

1. V řadiči domény otevřete okno **Uživatelé a počítače služby Active Directory** z možnosti **nástroje** v **Správce serveru**. 
2. Pokud chcete přejít na účet, vyberte **Uživatelé** v levém podokně.
3. Klikněte pravým tlačítkem na účet a vyberte **vlastnosti**.
4. Vyberte kartu **účet** . Pokud je pole **přihlašovací jméno uživatele** prázdné, jedná se o příčinu chyby. 

    ![Prázdný uživatelský účet indikuje chybějící hlavní název uživatele (UPN).](./media/availability-group-quickstart-template-configure/account-missing-upn.png)

5. Do pole **přihlašovací jméno uživatele** zadejte název uživatele a v rozevíracím seznamu vyberte správnou doménu. 
6. Výběrem možnosti **použít** uložíte změny a zavřete dialogové okno tak, že vyberete **OK**. 

Po provedení těchto změn zkuste šablonu pro rychlý Start Azure nasadit ještě jednou. 


## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server virtuálních počítačů](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy týkající se SQL Server virtuálních počítačů](frequently-asked-questions-faq.md)
* [Doprovodné materiály k ceníkům pro SQL Server virtuální počítače](pricing-guidance.md)
* [Poznámky k verzi pro virtuální počítače s SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Přepínání modelů licencování pro SQL Server virtuální počítač](licensing-model-azure-hybrid-benefit-ahb-change.md)