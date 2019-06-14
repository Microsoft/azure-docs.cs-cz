---
title: Zřizování Příručka pro virtuální počítače s Windows SQL serverem na webu Azure Portal | Dokumentace Microsoftu
description: Tato příručka popisuje možnosti pro vytváření virtuálních počítačů s Windows a SQL serverem 2017 na webu Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6723adb3fb8987a127eee419c9ac188c7a33d50b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076044"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Přidělení virtuálního počítače s Windows SQL serverem na webu Azure Portal

Tato příručka obsahuje podrobné informace o různých možnostech, které jsou k dispozici při vytváření virtuálního počítače s Windows serverem SQL na webu Azure Portal. V tomto článku najdete další možnosti konfigurace, než [rychlý start virtuálního počítače s SQL serverem](quickstart-sql-vm-create-portal.md), které jde další až jeden možný zřizování úloh. 

Tento průvodce vám vytvořit vlastní virtuální počítač s SQL serverem. Případně ho můžete použít jako referenci naleznete možnosti dostupné na webu Azure Portal.

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a id="select"></a> Image z Galerie virtuálních počítačů SQL serveru

Při vytváření virtuálního počítače s SQL serverem, vyberte jednu z několika předem nakonfigurované Image z Galerie virtuálních počítačů. Následující kroky ukazují, jak vybrat některou k imagí SQL serveru 2017.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí svého účtu.

1. Na webu Azure Portal klikněte na **Vytvořit prostředek**. Na Portálu se otevře okno **Nový**.

1. V okně **Nový** klikněte na **Compute** a pak klikněte na **Zobrazit všechno**.

1. Do pole hledání zadejte **SQL Server 2017** a stiskněte ENTER.

1. Ve filtru rozevíracích, vyberte _Windows serveru 2016_ pro **operační systém** a vyberte _Microsoft_ jako **vydavatele**. 

     ![Okno Nová služba Compute](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Projděte si dostupné image SQL Serveru. U každé image je označena příslušná verze SQL Serveru a operační systém.

1. Vyberte image s názvem **bezplatná licence SQL serveru: SQL Server 2017 Developer ve Windows serveru 2016**.

   > [!TIP]
   > U verze Developer edition je použít v tomto návodu, protože je plně funkční, bezplatnou edici systému SQL Server pro vývoj testování. Platíte jenom náklady na provozování virtuálního počítače. Ale budete moci vybrat kteroukoli z Image na použití v tomto názorném postupu. Popis dostupných imagí najdete v tématu [SQL Server Windows Virtual Machines – přehled](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Náklady na licencování pro SQL Server jsou začleněny do ceny za sekundu virtuální počítač vytvořit a liší se podle edice a počet jader. SQL Server Developer edition je však zdarma pro vývoj a testování (ne produkci) a SQL Express je zdarma pro nenáročné úlohy (méně než 1 GB paměti, méně než 10 GB úložiště). Můžete také přinést si – vlastní licence (BYOL) a platíte jenom za virtuální počítač. Tyto názvy bitových kopií mají předponu {BYOL}. 
   >
   > Další informace o těchto možnostech najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. V části **Vybrat model nasazení** ověřte, že je vybraný **Resource Manager**. U nových virtuálních počítačů se doporučuje používat model nasazení Resource Manageru. 

1. Vyberte **Vytvořit**.


## <a id="configure"></a> Možnosti konfigurace

Existuje několik karet pro konfiguraci virtuálního počítače s SQL serverem. Pro účely tohoto průvodce se zaměříme na následující: 

| Krok | Popis |
| --- | --- |
| **Základy** |[Konfigurace základního nastavení](#1-configure-basic-settings) |
| **Volitelné funkce** |[Konfigurace volitelných funkcí](#2-configure-optional-features) |
| **Nastavení SQL Serveru** |[Konfigurace nastavení SQL Serveru](#3-configure-sql-server-settings) |
| **Zkontrolovat a vytvořit** | [Kontrola souhrnných informací](#4-review--create) |

## <a name="1-configure-basic-settings"></a>1. Konfigurace základního nastavení


Na **Základy** kartu, zadejte následující informace:

* V části **Project Details**, ujistěte se, že je vybrané správné předplatné. 
*  V **skupiny prostředků** části, vyberte buď existující prostředek skupiny ze seznamu nebo zvolte **vytvořit nový** vytvořit novou skupinu prostředků. Skupina prostředků je kolekce souvisejících prostředků v Azure (virtuální počítače, účty úložiště, virtuální sítě atd.). 

    ![Předplatné](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Použití nové skupinu prostředků je užitečné, pokud testujete nasazení SQL Serveru v Azure nebo se snažíte o něm dozvědět více. Až s testováním skončíte, odstraňte skupinu prostředků. Automaticky se tím odstraní virtuální počítač se všemi prostředky spojenými s danou skupinu prostředků. Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../../../azure-resource-manager/resource-group-overview.md).


* V části **Instance podrobnosti**:
    1. Zadejte jedinečný **název virtuálního počítače**.  
    1. Vyberte umístění pro vaše **oblasti**. 
    1. Pro účely tohoto průvodce, ponechte **možností dostupnosti** nastavena na _žádné redundance infrastruktury požadované_. Další informace o možnosti dostupnosti najdete v tématu [oblastí Azure a dostupnost](../../windows/regions-and-availability.md). 
    1. V **Image** seznamu vyberte _bezplatná licence SQL serveru: SQL Server 2017 Developer ve Windows serveru 2016_.  
    1. Zvolit **změnit velikost** pro **velikost** virtuálního počítače a vyberte **A2 základní** nabídky. Ujistěte se, že vyčistit prostředky, až budete hotovi s nimi, abyste zabránili neočekávaným poplatkům. Doporučené velikosti a konfiguraci počítačů pro produkční úlohy najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

    ![Podrobnosti o instanci](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Odhadované měsíční náklady zobrazené v okně **Zvolit velikost** nezahrnují náklady na licencování SQL Serveru. Tento odhad jsou náklady pouze na virtuální počítač. Pro edice Express a Developer systému SQL Server je tento odhad o celkové odhadované náklady. Pro ostatní edice se podívejte na [stránku s cenami pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a vyberte cílovou edici vašeho SQL Serveru. Viz také [doprovodné materiály k pro virtuální počítače Azure s SQL serverem cenám](virtual-machines-windows-sql-server-pricing-guidance.md) a [velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* V části **účet správce**, zadejte uživatelské jméno a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Účet správce](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* V části **příchozí pravidla portů**, zvolte **povolit vybrané porty** a pak vyberte **protokolu RDP (3389)** z rozevíracího seznamu. 

   ![Pravidla portů pro příchozí provoz](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Konfigurace volitelných funkcí

### <a name="disks"></a>Disky

Na **disky** kartu, nakonfigurujte možnosti disku. 

* V části **typ disku operačního systému**, vyberte typ disku, které chcete použít pro váš operační systém z rozevíracího seznamu. Premium se doporučuje pro produkční systémy, ale není k dispozici pro základní virtuální počítač. Chcete-li využívají Premium SSD, změňte velikost virtuálního počítače. 
* V části **Upřesnit**vyberte **Ano** během použití **Managed Disks**.

   > [!NOTE]
   > Microsoft pro SQL Server doporučuje Spravované disky. Spravované disky se starají o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se Spravovanými disky ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v článku [Azure Přehled služby Managed Disks] [.. / spravovaných overview.md disků). Podrobnosti o spravovaných discích ve skupině dostupnosti, najdete v článku [použít spravované disky pro virtuální počítače ve skupině dostupnosti] (.. /Manage-Availability.MD.

![Nastavení disku virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Sítě

Na **sítě** kartu, nakonfigurujte možnosti vaší sítě. 

* Vytvořte nový **virtuální sítě**, nebo použít existující virtuální sítě pro virtuální počítač s SQL serverem. Určení **podsítě** také. 

* V části **skupiny zabezpečení NIC**, vyberte skupinu zabezpečení základní nebo pokročilé zabezpečení skupiny. Základní možnosti vám umožní vybrat příchozí porty pro SQL Server VM (stejné hodnoty, které byly nakonfigurované na **základní** kartu). Výběr možnosti Upřesnit umožňuje vyberte existující skupinu zabezpečení sítě, nebo vytvořte novou. 

* Můžete udělat jiné změny nastavení sítě, nebo ponechte výchozí hodnoty.

![Nastavení sítě virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorování

Na **monitorování** kartu, nakonfigurujte sledování a autoshutdown. 

* Azure umožňuje **spouštění monitorování** ve výchozím nastavení pomocí stejného účtu úložiště určený pro virtuální počítač. Můžete změnit tato nastavení zde, jakož i povolení **operačního systému hosta diagnostiky**. 
* Můžete povolit **systém přiřadil spravovanou identitu** a **autoshutdown** na této kartě také. 

![Správa nastavení virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurace nastavení SQL serveru

Na **nastavení systému SQL Server** kartu, nakonfigurujte konkrétní nastavení a optimalizace pro SQL Server. Nastavení, která můžete nakonfigurovat pro SQL Server, patří:



| Nastavení |
| --- |
| [Připojení](#connectivity) |
| [Ověřování](#authentication) |
| [Integrace se službou Azure Key Vault](#azure-key-vault-integration) |
| [Konfigurace úložiště](#storage-configuration) |
| [Automatizované opravy](#automated-patching) |
| [Automatizované zálohování](#automated-backup) |
| [R Services (Advanced Analytics)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Připojení

V části **Připojení SQL** zadejte typ přístupu, který chcete mít k instanci SQL Serveru na tomto virtuálním počítači. Pro účely tohoto názorného postupu, vyberte **veřejné (internet)** umožňující připojení k SQL serveru z počítačů nebo služeb na Internetu. Tato možnost aktivní Azure automaticky nakonfiguruje bránu firewall a skupinu zabezpečení sítě umožňující provoz na portu vybrané.

> [!TIP]
> Ve výchozím nastavení SQL Server naslouchá na dobře známém portu **1433**. Pokud chcete zvýšit zabezpečení, změňte port v předchozím dialogovém okně tak, aby SQL Server naslouchal na jiném než výchozím portu, například 1401. Pokud změníte port, musíte se připojit pomocí tohoto portu ze všech nástrojů klienta, jako je SSMS.

![Zabezpečení virtuálních počítačů SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Aby bylo možné se k SQL Serveru připojovat prostřednictvím internetu, musíte také povolit ověřování SQL Serveru, které je popsané v následující části.

Pokud raději nechcete povolovat připojení k databázovému stroji prostřednictvím internetu, zvolte jednu z následujících možností:

* **Místní (jen uvnitř virtuálního počítače):** Umožňuje připojení k SQL Serveru pouze v rámci virtuálního počítače.
* **Privátní (uvnitř virtuální sítě):** Umožňuje připojení k SQL Serveru z počítačů nebo služeb ve stejné virtuální síti.

Obecně se doporučuje zvýšit zabezpečení výběrem nejvíce omezujícího připojení, které váš scénář umožňuje. Všechny možnosti je ale možné zabezpečit prostřednictvím pravidel skupin zabezpečení sítě a ověřování SQL Serveru a Windows. Skupinu zabezpečení sítě můžete upravit po vytvoření virtuálního počítače. Další informace najdete v tématu [Informace o zabezpečení pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Authentication

Pokud budete vyžadovat ověřování SQL serveru, klikněte na tlačítko **povolit** pod **ověřování SQL** na **nastavení systému SQL Server** kartu.

![Ověřování SQL Serveru](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Pokud budete chtít přístup k SQL serveru prostřednictvím Internetu (nastavením veřejného připojení), které musíte tu povolit ověřování SQL. Veřejný přístup k SQL Serveru vyžaduje použití ověřování SQL Serveru.

Pokud povolíte ověřování SQL Serveru, zadejte **přihlašovací jméno** a **heslo**. Tato přihlašovací jméno je nakonfigurovaný jako účet ověřování systému SQL Server a členem **sysadmin** pevné role serveru. Další informace o režimech ověřování najdete v tématu [Volba režimu ověřování](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Pokud ověřování SQL Serveru nepovolíte, můžete pro připojení k instanci SQL Serveru používat účet místního správce ve virtuálním počítači.


### <a name="azure-key-vault-integration"></a>Integrace se službou Azure Key Vault

Chcete-li ukládat tajné klíče zabezpečení v Azure pro šifrování, vyberte **nastavení systému SQL Server**a přejděte dolů k položce **integrace Azure key vaultu**. Vyberte **povolit** a vyplňte požadované informace. 

![Integrace se službou Azure Key Vault](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

V následující tabulce jsou uvedeny parametry, které jsou nezbytné pro konfiguraci Integrace se službou Azure Key Vault.

| PARAMETR | POPIS | PŘÍKLAD |
| --- | --- | --- |
| **Adresa URL služby Key Vault** |Umístění služby Key Vault |https:\//contosokeyvault.vault.azure.net/ |
| **Název objektu zabezpečení** |Hlavní název služby Azure Active Directory. Tento název se také označuje jako ID klienta. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Tajný kód objektu zabezpečení** |Tajný klíč objektu zabezpečení služby Azure Active Directory. Tento tajný klíč se také označuje jako Tajný klíč klienta. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Název přihlašovacího údaje** |**Název přihlašovacího údaje**: Integrace se službou AZURE vytvoří přihlašovací údaje v rámci SQL serveru, díky čemuž mají přístup k trezoru klíčů virtuální počítače. Zvolte název pro tyto přihlašovací údaje. |moje_přihlaš1 |

Další informace najdete v tématu [Konfigurace Integrace se službou Azure Key Vault pro virtuální počítače Azure](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Konfigurace úložiště

Na **nastavení systému SQL Server** ve skupině **konfiguraci úložiště**vyberte **změna konfigurace** k určení požadavků na úložiště.


> [!NOTE]
> Pokud jste virtuální počítač ručně nakonfigurovali tak, aby používal Storage úrovně Standard, tato možnost není dostupná. Automatická optimalizace úložiště je k dispozici pouze pro Premium Storage.

> [!TIP]
> Počet zastavení a horní omezení každého posuvníku závisí na velikosti vybraného virtuálního počítače. Větší a výkonnější virtuální počítač umožňuje větší vertikální navýšení kapacity.

Požadavky můžete zadat jako vstupně-výstupní operace za sekundu (IOPs), propustnost v MB/s a celkovou velikost úložiště. Tyto hodnoty nakonfigurujte pomocí posuvníků. Tato nastavení úložiště můžete podle náročnosti zpracovávaných úloh změnit. Portál na základě těchto požadavků automaticky vypočítá počet disků, které se mají připojit a nakonfigurovat.

V části **Optimalizace úložiště** vyberte jednu z následujících možností:

* **Obecné:** Výchozí nastavení a podporuje většinu úloh.
* **Transakční:** Toto zpracování optimalizuje úložiště pro standardní úlohy databází OLTP.
* **Datové sklady:** Optimalizuje úložiště pro úlohy analýz a generování sestav.

![Konfigurace úložiště virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-storage-configuration.png)

### <a name="sql-server-license"></a>SQL Server License
Pokud jste zákazníky programu Software Assurance, můžete využít [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) na používání vlastní licence SQL serveru a ušetřit na prostředky. 

![Licence virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatizované opravy

**Automatizované opravy** jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Zadejte den v týdnu, čas a dobu trvání intervalu údržby. V té době pak Azure nainstaluje potřebné opravy. V rámci plánování intervalu údržby se pro čas používá národní prostředí virtuálních počítačů. Pokud nechcete, aby se v rámci Azure automaticky opravoval SQL Server a operační systém, klikněte na **Zakázat**.  

![Virtuální počítač SQL automatizované opravy](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Další informace najdete v tématu [Automatizované opravy pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatizované zálohování

Automatické zálohování databází můžete pro všechny databáze povolit v části **Automatizované zálohování**. Automatizované zálohování je ve výchozím nastavení zakázané.

Když povolíte automatizované zálohování SQL, můžete nakonfigurovat následující nastavení:

* Doba uchování dat (dny) pro zálohování
* Účet úložiště, který se má používat pro zálohování
* Možnost šifrování a heslo pro zálohování
* Zálohování systémových databází
* Konfigurování plánu zálohování

Pokud chcete zálohy šifrovat, klikněte na **Povolit**. Pak zadejte **heslo**. Azure vytvoří certifikát pro šifrování záloh a používá zadané heslo k ochraně tohoto certifikátu. Ve výchozím nastavení plánu je nastavena automaticky, ale můžete vytvořit ruční plán tak, že vyberete **ruční**. 

![Automatické zálohování virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Další informace najdete v tématu [Automatizované zálohování pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).


### <a name="r-services-advanced-analytics"></a>R Services (Advanced Analytics)

Máte možnost Povolit [SQL Server R Services (Advanced Analytics)](/sql/advanced-analytics/r/sql-server-r-services/). Tato možnost umožňuje používat pokročilé analýzy s SQL serverem 2017. Vyberte **povolit** na **nastavení systému SQL Server** okna.


## <a name="4-review--create"></a>4. Zkontrolovat a vytvořit

Na **zkontrolujte + vytvořit** kartu, zkontrolujte souhrn a vyberte **vytvořit** k vytvoření systému SQL Server, skupinu prostředků a prostředky zadané pro tento virtuální počítač.

Nasazení můžete monitorovat z webu Azure Portal. Tlačítko **Oznámení** v horní části obrazovky zobrazuje základní stav nasazení.

> [!NOTE]
> Abyste si udělali představu o tom, jak dlouho nasazování trvá, nasadil jsem virtuální počítač s SQL Serverem pro oblast Východní USA s výchozím nastavením. Dokončení tohoto testovacího nasazení trvalo přibližně 12 minut. Na základě vaší oblasti a vybraného nastavení ale můžete zaznamenat kratší nebo delší čas nasazení.

## <a id="remotedesktop"></a>Otevření virtuálního počítače pomocí Vzdálené plochy

Podle následujícího postupu se připojte k virtuálnímu počítači s SQL Serverem pomocí Vzdálené plochy:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po připojení k virtuálnímu počítači s SQL Serverem můžete spustit SQL Server Management Studio a připojit se pomocí ověřování systému Windows se svými přihlašovacími údaji místního správce. Pokud jste povolili ověřování SQL Serveru, můžete se také připojit pomocí ověřování SQL Serveru a použít k tomu přihlašovací jméno a heslo SQL Serveru, které jste nakonfigurovali během zřizování.

Přístup k počítači vám umožňuje podle potřeb přímo měnit nastavení počítače a SQL Serveru. Můžete například nakonfigurovat nastavení brány firewall nebo změnit nastavení konfigurace SQL Serveru.

## <a id="connect"></a>Vzdálené připojení k SQL Serveru

V tomto návodu jste vybrali **veřejné** přístup pro virtuální počítač a **ověřování systému SQL Server**. Tato nastavení automaticky nakonfigurovala virtuální počítač tak, aby povoloval připojení k SQL Serveru z libovolného klienta přes internet (za předpokladu, že má správné přihlašovací údaje SQL Serveru).

> [!NOTE]
> Pokud jste nevybrali veřejný přístup během zřizování, můžete prostřednictvím portálu změnit nastavení připojení SQL po zřízení. Další informace najdete v tématu popisujícím [změnu nastavení připojení SQL](virtual-machines-windows-sql-connect.md#change).

Následující části vysvětlují, jak se připojit přes internet k vaší instanci virtuálního počítače s SQL serverem.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Tento příklad používá běžný port 1433. Tato hodnota bude však nutné upravit, pokud byl zadán jiný port (například 1401) během nasazení virtuálního počítače SQL serveru. 


## <a name="next-steps"></a>Další postup

Další informace o používání SQL Serveru v Azure najdete v tématu [SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) a [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).