---
title: Zřízení virtuálního počítače s Windows pomocí Azure Portal
description: Tato příručka obsahuje možnosti, které jsou k dispozici pro použití Azure Portal ke zřízení SQL Server na virtuálním počítači s Windows.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a7cd15bab0b26a13f9ffb818aa29e8e262c0bd06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332912"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Jak pomocí Azure Portal zřídit virtuální počítač s Windows pomocí SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tato příručka obsahuje možnosti, které jsou k dispozici pro použití Azure Portal ke zřízení SQL Server na virtuálním počítači s Windows (VM). Tento článek se zabývá více možnostmi konfigurace než [rychlý Start pro SQL Server virtuálního počítače](sql-vm-create-portal-quickstart.md), který se podrobněji zaměřuje na jednu konfiguraci. 

Pomocí tohoto průvodce můžete vytvořit vlastní virtuální počítač SQL Server. Případně ho použijte jako referenci pro dostupné možnosti v Azure Portal.

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> SQL Server imagí Galerie virtuálních počítačů

Když vytváříte virtuální počítač s SQL Server, můžete z Galerie virtuálních počítačů vybrat jednu z několika předem nakonfigurovaných imagí. Následující kroky ukazují, jak vybrat jednu z imagí SQL Server 2017.

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte *Azure SQL* . 

   Můžete také vybrat hvězdičku vedle **Azure SQL** a uložit ji jako oblíbenou položku a přidat ji jako položku v levém navigačním panelu. 

1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Kliknutím na **Zobrazit podrobnosti**můžete zobrazit další informace. 
1. Do pole SQL Server image pro hledání na dlaždici **virtuální počítače SQL** zadejte *2017* a potom vyberte **bezplatné SQL Server licenci: SQL Server 2017 Developer v systému Windows Server 2016** z rozevíracího seznamu. 

   ![Výběr image virtuálního počítače s SQL](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > V tomto článku se používá vývojářská edice, protože se jedná o plnohodnotnou bezplatnou edici SQL Server pro testování vývoje. Platíte jenom náklady na provozování virtuálního počítače. V tomto návodu ale zvolíte libovolné image, které chcete použít. Popis dostupných imagí najdete v [přehledu SQL Server Windows Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

   > [!TIP]
   > Náklady na licencování pro SQL Server jsou začleněny do cen za sekundu virtuálního počítače, který vytvoříte a liší se podle edice a jader. Edice SQL Server Developer však je zdarma pro vývoj a testování, nikoli pro produkční prostředí. SQL Express je také zdarma pro odlehčené úlohy (méně než 1 GB paměti, méně než 10 GB úložiště). Můžete také využít vlastní licenci (BYOL) a Plaťte jenom za virtuální počítač. Tyto názvy bitových kopií mají předponu {BYOL}. 
   >
   > Další informace o těchto možnostech najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](pricing-guidance.md).


1. Vyberte **Vytvořit**.


## <a name="1-configure-basic-settings"></a>1. Konfigurace základního nastavení

Na kartě **základy** zadejte následující informace:

* V části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné. 
* V části **Skupina prostředků** vyberte ze seznamu existující skupinu prostředků nebo vytvořte novou skupinu prostředků výběrem možnosti **vytvořit nový** . Skupina prostředků je kolekce souvisejících prostředků v Azure (virtuální počítače, účty úložiště, virtuální sítě atd.). 

  ![Předplatné](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > Použití nové skupinu prostředků je užitečné, pokud testujete nasazení SQL Serveru v Azure nebo se snažíte o něm dozvědět více. Až s testováním skončíte, odstraňte skupinu prostředků. Automaticky se tím odstraní virtuální počítač se všemi prostředky spojenými s danou skupinu prostředků. Další informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../../../active-directory-b2c/overview.md).


* V části **Podrobnosti instance**:

    1. Zadejte jedinečný **název virtuálního počítače**.  
    1. Vyberte umístění pro vaši **oblast**. 
    1. Pro účely tohoto průvodce nechte **Možnosti dostupnosti** nastavené na _nepotřebnou redundanci infrastruktury_. Další informace o možnostech dostupnosti najdete v tématu [dostupnost](../../../virtual-machines/windows/availability.md). 
    1. V seznamu **Obrázek** vyberte _bezplatný SQL Server licence: SQL Server 2017 Developer v systému Windows Server 2016_.  
    1. Zvolte, chcete-li **změnit velikost** pro **Velikost** virtuálního počítače, a vyberte položku **základní nabídka a2** . Abyste zabránili neočekávaným poplatkům, nezapomeňte prostředky vyčistit. Doporučené velikosti a konfiguraci počítačů pro produkční úlohy najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](performance-guidelines-best-practices.md).

    ![Podrobnosti o instancích](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> Odhadované měsíční náklady zobrazené v okně **Zvolit velikost** nezahrnují náklady na licencování SQL Serveru. Tento odhad je náklady na samotný virtuální počítač. V edicích Express a Developer pro SQL Server je toto odhadované celkové odhadované náklady. Pro ostatní edice se podívejte na [stránku s cenami pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a vyberte cílovou edici vašeho SQL Serveru. Podívejte se také na [doprovodné materiály k cenám pro](pricing-guidance.md) virtuální počítače s SQL Server a [velikosti virtuálních počítačů](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)Azure.

* V části **účet správce**zadejte uživatelské jméno a heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Účet správce](./media/create-sql-vm-portal/basics-administrator-account.png)

* V části **pravidla portů pro příchozí spojení**zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **RDP (3389)** . 

   ![Pravidla portů pro příchozí provoz](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. konfigurace volitelných funkcí

### <a name="disks"></a>Disky

Na kartě **disky** nakonfigurujte možnosti disku. 

* V části **typ disku s operačním systémem**vyberte v rozevíracím seznamu typ disku, který chcete pro operační systém. Premium se doporučuje pro produkční systémy, ale pro základní virtuální počítač není k dispozici. Pokud chcete použít SSD úrovně Premium, změňte velikost virtuálního počítače. 
* V části **Upřesnit**vyberte v části použít **Managed disks** **Ano** .

   > [!NOTE]
   > Microsoft pro SQL Server doporučuje Spravované disky. Spravované disky se starají o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se Spravovanými disky ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v tématu [Přehled služby Azure Managed disks](../../../virtual-machines/managed-disks-overview.md). Podrobnosti o spravovaných discích ve skupině dostupnosti najdete v tématu [Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti](../../../virtual-machines/windows/manage-availability.md).

![Nastavení disku pro virtuální počítač SQL](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Sítě

Na kartě **síť** nakonfigurujte možnosti sítě. 

* Vytvořte novou **virtuální síť** nebo pro virtuální počítač SQL Server použijte existující virtuální síť. Určete také **podsíť** . 

* V části **Skupina zabezpečení sítě síťové karty**vyberte buď základní skupinu zabezpečení, nebo skupinu rozšířené zabezpečení. Výběrem možnosti základní můžete vybrat vstupní porty pro virtuální počítač SQL Server, které jsou stejné jako konfigurace na kartě **Basic** . Výběrem možnosti Upřesnit můžete vybrat existující skupinu zabezpečení sítě nebo vytvořit novou. 

* Můžete provést další změny v nastavení sítě nebo ponechat výchozí hodnoty.

![Nastavení sítě virtuálního počítače SQL](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorování

Na kartě **monitorování** nakonfigurujte monitorování a automatické vypnutí. 

* Azure povolí **diagnostiku spouštění** ve výchozím nastavení se stejným účtem úložiště, který je určený pro virtuální počítač. Na této kartě můžete změnit tato nastavení a povolit **diagnostiku hostovaného operačního systému**. 
* Na této kartě můžete také povolit **spravovanou identitu přiřazenou systémem** a **Automatické vypínání** . 

![Nastavení správy virtuálních počítačů SQL](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurace nastavení SQL Server

Na kartě **nastavení SQL Server** nakonfigurujte konkrétní nastavení a optimalizace pro SQL Server. Pro SQL Server můžete nakonfigurovat následující nastavení:

- [Připojení](#connectivity)
- [Authentication](#authentication)
- [Integrace Azure Key Vault](#azure-key-vault-integration)
- [Konfigurace úložiště](#storage-configuration)
- [Automatizované opravy](#automated-patching)
- [Automatizované zálohování](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Připojení

V části **Připojení SQL** zadejte typ přístupu, který chcete mít k instanci SQL Serveru na tomto virtuálním počítači. Pro účely tohoto návodu vyberte možnost **veřejné (Internet)** , která umožňuje připojení k SQL Server z počítačů nebo služeb na internetu. Když je vybraná tato možnost, Azure automaticky nakonfiguruje bránu firewall a skupinu zabezpečení sítě tak, aby na vybraném portu povolovala provoz.

> [!TIP]
> Ve výchozím nastavení SQL Server naslouchá na dobře známém portu **1433**. Pokud chcete zvýšit zabezpečení, změňte port v předchozím dialogovém okně tak, aby SQL Server naslouchal na jiném než výchozím portu, například 1401. Změníte-li port, je nutné se připojit pomocí tohoto portu z libovolných klientských nástrojů, například SQL Server Management Studio (SSMS).

![Zabezpečení virtuálního počítače SQL](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Aby bylo možné se k SQL Serveru připojovat prostřednictvím internetu, musíte také povolit ověřování SQL Serveru, které je popsané v následující části.

Pokud raději nechcete povolovat připojení k databázovému stroji prostřednictvím internetu, zvolte jednu z následujících možností:

* **Místní (jen uvnitř virtuálního počítače):** Umožňuje připojení k SQL Serveru pouze v rámci virtuálního počítače.
* **Privátní (uvnitř virtuální sítě):** Umožňuje připojení k SQL Serveru z počítačů nebo služeb ve stejné virtuální síti.

Obecně se doporučuje zvýšit zabezpečení výběrem nejvíce omezujícího připojení, které váš scénář umožňuje. Všechny tyto možnosti jsou ale zabezpečitelný prostřednictvím pravidel skupiny zabezpečení sítě (NSG) a ověřování SQL/Windows. Po vytvoření virtuálního počítače můžete NSG upravit. Další informace najdete v tématu [Informace o zabezpečení pro SQL Server na virtuálních počítačích Azure](security-considerations-best-practices.md).

### <a name="authentication"></a>Authentication

Pokud požadujete SQL Server ověřování, vyberte **Povolit** pod **ověřováním SQL** na kartě **nastavení SQL Server** .

![Ověřování SQL Serveru](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Pokud máte v úmyslu získat přístup k SQL Server přes Internet (možnost veřejného připojení), musíte tady povolit ověřování SQL. Veřejný přístup k SQL Server vyžaduje ověřování SQL.

Pokud povolíte ověřování SQL Serveru, zadejte **přihlašovací jméno** a **heslo**. Toto přihlašovací jméno je nakonfigurované jako přihlašovací jméno ověřování SQL Server a člen pevné role serveru **sysadmin** . Další informace o režimech ověřování najdete v tématu [Volba režimu ověřování](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Pokud nechcete povolit SQL Server ověřování, můžete se pomocí účtu místního správce na virtuálním počítači připojit k instanci SQL Server.

### <a name="azure-key-vault-integration"></a>Integrace se službou Azure Key Vault

Pokud chcete pro šifrování ukládat tajné klíče zabezpečení v Azure, vyberte **SQL Server nastavení**a přejděte dolů k  **integraci Azure Key trezoru**. Vyberte **Povolit** a vyplňte požadované informace. 

![Integrace se službou Azure Key Vault](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

V následující tabulce jsou uvedeny parametry požadované ke konfiguraci Integrace Azure Key Vault (integrace).

| PARAMETR | POPIS | PŘÍKLAD |
| --- | --- | --- |
| **Adresa URL služby Key Vault** |Umístění služby Key Vault |`https://contosokeyvault.vault.azure.net/` |
| **Název objektu zabezpečení** |Hlavní název služby Azure Active Directory. Tento název se také označuje jako ID klienta. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Tajný kód objektu zabezpečení** |Tajný klíč objektu zabezpečení služby Azure Active Directory. Tento tajný klíč se také označuje jako Tajný klíč klienta. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Název přihlašovacího údaje** |**Přihlašovací jméno**: při integraci integrace se vytvoří přihlašovací údaje v rámci služby SQL Server a povolí virtuálnímu počítači přístup k trezoru klíčů. Zvolte název pro tyto přihlašovací údaje. |`mycred1` |

Další informace najdete v tématu [Konfigurace Integrace se službou Azure Key Vault pro virtuální počítače Azure](azure-key-vault-integration-configure.md).

### <a name="storage-configuration"></a>Konfigurace úložiště

Na kartě **nastavení SQL Server** v části **Konfigurace úložiště**vyberte **změnit konfiguraci** . otevře se stránka konfigurace optimalizovaného úložiště výkonu a určí požadavky na úložiště.

![Konfigurace úložiště virtuálních počítačů SQL](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

V části **Optimalizace úložiště** vyberte jednu z následujících možností:

* **Obecné:** Výchozí nastavení a podporuje většinu úloh.
* **Transakční zpracování** optimalizuje úložiště pro tradiční databázové OLTP úlohy.
* **Datové sklady:** Optimalizuje úložiště pro úlohy analýz a generování sestav.

![Konfigurace úložiště virtuálních počítačů SQL](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

Můžete zvolit, aby se hodnoty ponechaly ve výchozím nastavení, nebo můžete topologii úložiště změnit ručně tak, aby vyhovovala vašim požadavkům IOPS. Další informace najdete v tématu [Konfigurace úložiště](storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server licence

Pokud jste zákazníkem programu Software Assurance, můžete použít [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) k převedení vlastní SQL Server licence a uložení na prostředky. 

![Licence k virtuálnímu počítači SQL](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatizované opravy

**Automatizované opravy** jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Zadejte den v týdnu, čas a dobu trvání intervalu údržby. V té době pak Azure nainstaluje potřebné opravy. Plán časového intervalu pro správu a údržbu používá národní prostředí virtuálního počítače. Pokud nechcete, aby Azure automaticky opravovat SQL Server a operační systém, vyberte **Zakázat**.  

![Automatizované opravy SQL VM](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Další informace najdete v tématu [Automatizované opravy pro SQL Server v Azure Virtual Machines](automated-patching.md).

### <a name="automated-backup"></a>Automatizované zálohování

Automatické zálohování databází můžete pro všechny databáze povolit v části **Automatizované zálohování**. Automatizované zálohování je ve výchozím nastavení zakázané.

Když povolíte automatizované zálohování SQL, můžete nakonfigurovat následující nastavení:

* Doba uchování dat (dny) pro zálohování
* Účet úložiště, který se má používat pro zálohování
* Možnost šifrování a heslo pro zálohování
* Zálohování systémových databází
* Konfigurování plánu zálohování

Chcete-li zašifrovat zálohu, vyberte možnost **Povolit**. Pak zadejte **heslo**. Azure vytvoří certifikát pro šifrování záloh a používá zadané heslo k ochraně tohoto certifikátu. Ve výchozím nastavení je plán nastavený na automaticky, ale ruční plán můžete vytvořit tak, že vyberete **ručně**. 

![Automatizované zálohování virtuálních počítačů SQL](./media/create-sql-vm-portal/automated-backup.png)

Další informace najdete v tématu [Automatizované zálohování pro SQL Server v Azure Virtual Machines](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Machine Learning Services

Máte možnost povolit [Machine Learning Services](/sql/advanced-analytics/). Tato možnost umožňuje používat Machine Learning s Pythonem a R v SQL Server 2017. V okně **nastavení SQL Server** vyberte **Povolit** .


## <a name="4-review--create"></a>4. revize a vytvoření

Na kartě **Revize + vytvořit** :
1. Zkontrolujte souhrnné informace.
1. Výběrem **vytvořit** vytvořte SQL Server, skupinu prostředků a prostředky zadané pro tento virtuální počítač.

Nasazení můžete monitorovat z webu Azure Portal. Tlačítko **Oznámení** v horní části obrazovky zobrazuje základní stav nasazení.

> [!NOTE]
> Příklad času, kdy může Azure nasadit SQL Server virtuální počítač: testovací SQL Server virtuální počítač zřízený do Východní USA oblasti s výchozími nastaveními trvá přibližně 12 minut. V závislosti na vaší oblasti a zvolených nastaveních se může vyskytnout rychlejší nebo pomalejší doba nasazení.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Otevření virtuálního počítače pomocí vzdálené plochy

Pomocí následujících kroků se připojte k SQL Server virtuálnímu počítači pomocí protokol RDP (Remote Desktop Protocol) (RDP):

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po připojení k virtuálnímu počítači s SQL Serverem můžete spustit SQL Server Management Studio a připojit se pomocí ověřování systému Windows se svými přihlašovacími údaji místního správce. Pokud jste povolili ověřování SQL Serveru, můžete se také připojit pomocí ověřování SQL Serveru a použít k tomu přihlašovací jméno a heslo SQL Serveru, které jste nakonfigurovali během zřizování.

Přístup k počítači vám umožňuje podle potřeb přímo měnit nastavení počítače a SQL Serveru. Můžete například nakonfigurovat nastavení brány firewall nebo změnit nastavení konfigurace SQL Serveru.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> Připojení k SQL Server vzdáleně

V tomto návodu jste vybrali **veřejný** přístup k virtuálnímu počítači a **SQL Server ověřování**. Tato nastavení automaticky nakonfigurovala virtuální počítač tak, aby povoloval připojení k SQL Serveru z libovolného klienta přes internet (za předpokladu, že má správné přihlašovací údaje SQL Serveru).

> [!NOTE]
> Pokud jste nevybrali veřejný přístup během zřizování, můžete prostřednictvím portálu změnit nastavení připojení SQL po zřízení. Další informace najdete v tématu popisujícím [změnu nastavení připojení SQL](ways-to-connect-to-sql.md#change).

Následující části ukazují, jak se připojit přes Internet k instanci virtuálního počítače s SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > V tomto příkladu se používá běžný port 1433. Tato hodnota se ale bude muset upravit, pokud se během nasazování SQL Server virtuálního počítače zadal jiný port (například 1401). 


## <a name="next-steps"></a>Další kroky

Další informace o používání SQL Serveru v Azure najdete v tématu [SQL Server v Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) a [Nejčastější dotazy](frequently-asked-questions-faq.md).
