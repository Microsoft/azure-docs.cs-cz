---
title: Příručka pro zřizování pro virtuální počítače s Windows SQL Server v Azure Portal | Microsoft Docs
description: Tato příručka popisuje vaše možnosti vytváření virtuálních počítačů s Windows SQL Server 2017 v Azure Portal.
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
ms.openlocfilehash: c316e55700fc8a28ed39bea960a9fe2b2bbd4df1
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900375"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Jak zřídit virtuální počítač s Windows SQL Server v Azure Portal

Tato příručka poskytuje podrobné informace o různých možnostech, které jsou k dispozici při vytváření virtuálního počítače s Windows SQL Server v Azure Portal. Tento článek se zabývá více možnostmi konfigurace než [rychlý Start pro SQL Server virtuálních počítačů](quickstart-sql-vm-create-portal.md), který je více než jednou možnou úlohou zřizování. 

Pomocí tohoto průvodce můžete vytvořit vlastní virtuální počítač SQL Server. Případně ho použijte jako referenci pro dostupné možnosti v Azure Portal.

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a id="select"></a>SQL Server imagí Galerie virtuálních počítačů

Když vytváříte virtuální počítač s SQL Server, můžete z Galerie virtuálních počítačů vybrat jednu z několika předem nakonfigurovaných imagí. Následující kroky ukazují, jak vybrat jednu z imagí SQL Server 2017.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu.

1. Na webu Azure Portal klikněte na **Vytvořit prostředek**. Na Portálu se otevře okno **Nový**.

1. V okně **Nový** klikněte na **Compute** a pak klikněte na **Zobrazit všechno**.

1. Do pole hledání zadejte **SQL Server 2017** a stiskněte ENTER.

1. V rozevíracích seznamech filtru vyberte pro **operační systém** možnost _Windows Server 2016_ a jako **vydavatele**vyberte _Microsoft_ . 

     ![Okno Nová služba Compute](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Projděte si dostupné image SQL Serveru. U každé image je označena příslušná verze SQL Serveru a operační systém.

1. Vyberte image s názvem **Free SQL Server License: SQL Server 2017 Developer v systému Windows Server**2016.

   > [!TIP]
   > V tomto návodu se používá vývojářská edice, protože se jedná o plně funkční, bezplatnou edici SQL Server pro testování vývoje. Platíte jenom náklady na provozování virtuálního počítače. V tomto návodu ale zvolíte libovolné image, které chcete použít. Popis dostupných imagí najdete v [přehledu SQL Server Windows Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Náklady na licencování pro SQL Server jsou začleněny do cen za sekundu virtuálního počítače, který vytvoříte a liší se podle edice a jader. SQL Server Developer Edition je ale zdarma pro vývoj/testování (ne pro produkci) a SQL Express je zdarma pro odlehčené úlohy (méně než 1 GB paměti, méně než 10 GB úložiště). Můžete také využít vlastní licenci (BYOL) a Plaťte jenom za virtuální počítač. Tyto názvy bitových kopií mají předponu {BYOL}. 
   >
   > Další informace o těchto možnostech najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. V části **Vybrat model nasazení** ověřte, že je vybraný **Resource Manager**. U nových virtuálních počítačů se doporučuje používat model nasazení Resource Manageru. 

1. Vyberte **Vytvořit**.


## <a id="configure"></a>Možnosti konfigurace

Pro konfiguraci virtuálního počítače s SQL Server je k dispozici několik karet. Pro účely tohoto průvodce se zaměříme na následující: 

| Krok | Popis |
| --- | --- |
| **Základy** |[Konfigurace základního nastavení](#1-configure-basic-settings) |
| **Volitelné funkce** |[Konfigurace volitelných funkcí](#2-configure-optional-features) |
| **Nastavení SQL Serveru** |[Konfigurace nastavení SQL Serveru](#3-configure-sql-server-settings) |
| **Zkontrolovat a vytvořit** | [Kontrola souhrnných informací](#4-review--create) |

## <a name="1-configure-basic-settings"></a>1. Konfigurace základních nastavení


Na kartě **základy** zadejte následující informace:

* V části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné. 
*  V části **Skupina prostředků** vyberte ze seznamu existující skupinu prostředků nebo vytvořte novou skupinu prostředků výběrem možnosti **vytvořit nový** . Skupina prostředků je kolekce souvisejících prostředků v Azure (virtuální počítače, účty úložiště, virtuální sítě atd.). 

    ![Subscription](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Použití nové skupinu prostředků je užitečné, pokud testujete nasazení SQL Serveru v Azure nebo se snažíte o něm dozvědět více. Až s testováním skončíte, odstraňte skupinu prostředků. Automaticky se tím odstraní virtuální počítač se všemi prostředky spojenými s danou skupinu prostředků. Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../../../azure-resource-manager/resource-group-overview.md).


* V části **Podrobnosti instance**:
    1. Zadejte jedinečný **název virtuálního počítače**.  
    1. Vyberte umístění pro vaši **oblast**. 
    1. Pro účely tohoto průvodce nechte **Možnosti dostupnosti** nastavené na nepotřebnou _redundanci infrastruktury_. Další informace o možnostech dostupnosti najdete v tématu [dostupnost](../../windows/availability.md). 
    1. V seznamu **Obrázek** vyberte _bezplatný SQL Server licence: SQL Server 2017 Developer v systému Windows Server_2016.  
    1. Zvolte, chcete-li **změnit velikost** pro **Velikost** virtuálního počítače, a vyberte položku **základní nabídka a2** . Abyste zabránili neočekávaným poplatkům, nezapomeňte prostředky vyčistit. Doporučené velikosti a konfiguraci počítačů pro produkční úlohy najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

    ![Podrobnosti o instancích](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Odhadované měsíční náklady zobrazené v okně **Zvolit velikost** nezahrnují náklady na licencování SQL Serveru. Tento odhad je náklady na samotný virtuální počítač. V edicích Express a Developer pro SQL Server je toto odhadované celkové odhadované náklady. Pro ostatní edice se podívejte na [stránku s cenami pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a vyberte cílovou edici vašeho SQL Serveru. Podívejte se také na [doprovodné materiály k cenám pro](virtual-machines-windows-sql-server-pricing-guidance.md) virtuální počítače s SQL Server a [velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)Azure.

* V části **účet správce**zadejte uživatelské jméno a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Účet správce](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* V části **pravidla portů pro příchozí spojení**zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **RDP (3389)** . 

   ![Pravidla portů pro příchozí spojení](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Nakonfigurujte volitelné funkce

### <a name="disks"></a>Disky

Na kartě **disky** nakonfigurujte možnosti disku. 

* V části **typ disku s operačním systémem**vyberte v rozevíracím seznamu typ disku, který chcete pro operační systém. Premium se doporučuje pro produkční systémy, ale pro základní virtuální počítač není k dispozici. Pokud chcete využít SSD úrovně Premium, změňte velikost virtuálního počítače. 
* V části **Upřesnit**vyberte v části použít **Managed disks** **Ano** .

   > [!NOTE]
   > Microsoft pro SQL Server doporučuje Spravované disky. Spravované disky se starají o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se Spravovanými disky ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v tématu [Přehled Azure Managed Disks] [.. /managed-disks-overview.md). Podrobnosti o spravovaných discích ve skupině dostupnosti najdete v tématu [Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti](../manage-availability.md).

![Nastavení disku pro virtuální počítač SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Sítě

Na kartě **síť** nakonfigurujte možnosti sítě. 

* Vytvořte novou **virtuální síť**nebo pro virtuální počítač SQL Server použijte existující virtuální síť. Určete také **podsíť** . 

* V části **Skupina zabezpečení sítě síťové karty**vyberte buď základní skupinu zabezpečení, nebo skupinu rozšířených zabezpečení. Výběrem možnosti základní můžete vybrat vstupní porty pro virtuální počítač SQL Server (stejné hodnoty, které byly nakonfigurované na kartě **základní** ). Výběrem možnosti Upřesnit můžete vybrat existující skupinu zabezpečení sítě nebo vytvořit novou. 

* Můžete provést další změny v nastavení sítě nebo ponechat výchozí hodnoty.

![Nastavení sítě virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorování

Na kartě **monitorování** nakonfigurujte monitorování a automatické vypnutí. 

* Azure povolí **diagnostiku spouštění** ve výchozím nastavení se stejným účtem úložiště, který je určený pro virtuální počítač. Tato nastavení můžete změnit tady a zároveň povolit **diagnostiku hosta operačního systému**. 
* Na této kartě můžete také povolit **spravovanou identitu přiřazenou systémem** a automatické vypínání. 

![Nastavení správy virtuálních počítačů SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurace nastavení SQL Server

Na kartě **nastavení SQL Server** nakonfigurujte konkrétní nastavení a optimalizace pro SQL Server. Nastavení, která můžete nakonfigurovat pro SQL Server, zahrnují následující:



| Nastavení |
| --- |
| [Připojení](#connectivity) |
| [Autentizace](#authentication) |
| [Integrace se službou Azure Key Vault](#azure-key-vault-integration) |
| [Konfigurace úložiště](#storage-configuration) |
| [Automatizované opravy](#automated-patching) |
| [Automatizované zálohování](#automated-backup) |
| [Služby R (pokročilé analýzy)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Připojení

V části **Připojení SQL** zadejte typ přístupu, který chcete mít k instanci SQL Serveru na tomto virtuálním počítači. Pro účely tohoto návodu vyberte možnost **veřejné (Internet)** , která umožňuje připojení k SQL Server z počítačů nebo služeb na internetu. Když je vybraná tato možnost, Azure automaticky nakonfiguruje bránu firewall a skupinu zabezpečení sítě tak, aby na vybraném portu povolovala provoz.

> [!TIP]
> Ve výchozím nastavení SQL Server naslouchá na dobře známém portu **1433**. Pokud chcete zvýšit zabezpečení, změňte port v předchozím dialogovém okně tak, aby SQL Server naslouchal na jiném než výchozím portu, například 1401. Změníte-li port, je nutné se připojit pomocí tohoto portu z libovolných klientských nástrojů, například SSMS.

![Zabezpečení virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Aby bylo možné se k SQL Serveru připojovat prostřednictvím internetu, musíte také povolit ověřování SQL Serveru, které je popsané v následující části.

Pokud raději nechcete povolovat připojení k databázovému stroji prostřednictvím internetu, zvolte jednu z následujících možností:

* **Místní (jen uvnitř virtuálního počítače):** Umožňuje připojení k SQL Serveru pouze v rámci virtuálního počítače.
* **Privátní (uvnitř virtuální sítě):** Umožňuje připojení k SQL Serveru z počítačů nebo služeb ve stejné virtuální síti.

Obecně se doporučuje zvýšit zabezpečení výběrem nejvíce omezujícího připojení, které váš scénář umožňuje. Všechny možnosti je ale možné zabezpečit prostřednictvím pravidel skupin zabezpečení sítě a ověřování SQL Serveru a Windows. Skupinu zabezpečení sítě můžete upravit po vytvoření virtuálního počítače. Další informace najdete v tématu [Informace o zabezpečení pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Ověřování

Pokud požadujete SQL Server ověřování, klikněte na **Povolit** pod **ověřováním SQL** na kartě **nastavení SQL Server** .

![Ověřování SQL Serveru](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Pokud máte v úmyslu získat přístup k SQL Server přes Internet (možnost veřejného připojení), musíte tady povolit ověřování SQL. Veřejný přístup k SQL Serveru vyžaduje použití ověřování SQL Serveru.

Pokud povolíte ověřování SQL Serveru, zadejte **přihlašovací jméno** a **heslo**. Toto přihlašovací jméno je nakonfigurované jako přihlašovací jméno ověřování SQL Server a člen pevné role serveru **sysadmin** . Další informace o režimech ověřování najdete v tématu [Volba režimu ověřování](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Pokud ověřování SQL Serveru nepovolíte, můžete pro připojení k instanci SQL Serveru používat účet místního správce ve virtuálním počítači.


### <a name="azure-key-vault-integration"></a>Integrace se službou Azure Key Vault

Pokud chcete pro šifrování ukládat tajné klíče zabezpečení v Azure, vyberte **SQL Server nastavení**a přejděte dolů k **integraci Azure Key trezoru**. Vyberte **Povolit** a vyplňte požadované informace. 

![Integrace se službou Azure Key Vault](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

V následující tabulce jsou uvedeny parametry, které jsou nezbytné pro konfiguraci Integrace se službou Azure Key Vault.

| PARAMETR | POPIS | PŘÍKLAD |
| --- | --- | --- |
| **Adresa URL služby Key Vault** |Umístění služby Key Vault |https:\//contosokeyvault.Vault.Azure.NET/ |
| **Název objektu zabezpečení** |Hlavní název služby Azure Active Directory. Tento název se také označuje jako ID klienta. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Tajný kód objektu zabezpečení** |Tajný klíč objektu zabezpečení služby Azure Active Directory. Tento tajný klíč se také označuje jako Tajný klíč klienta. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Název přihlašovacího údaje** |**Název přihlašovacích údajů**: INTEGRACE Integration vytvoří v rámci SQL Server přihlašovací údaje, aby měl virtuální počítač přístup k trezoru klíčů. Zvolte název pro tyto přihlašovací údaje. |moje_přihlaš1 |

Další informace najdete v tématu [Konfigurace Integrace se službou Azure Key Vault pro virtuální počítače Azure](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Konfigurace úložiště

Na kartě **nastavení SQL Server** v části **Konfigurace úložiště**vyberte **změnit konfiguraci** a určete požadavky na úložiště.


> [!NOTE]
> Pokud jste virtuální počítač ručně nakonfigurovali tak, aby používal Storage úrovně Standard, tato možnost není dostupná. Automatická optimalizace úložiště je k dispozici pouze pro Premium Storage.

> [!TIP]
> Počet zastavení a horní omezení každého posuvníku závisí na velikosti vybraného virtuálního počítače. Větší a výkonnější virtuální počítač umožňuje větší vertikální navýšení kapacity.

Požadavky můžete zadat jako vstupně-výstupní operace za sekundu (IOPs), propustnost v MB/s a celkovou velikost úložiště. Tyto hodnoty nakonfigurujte pomocí posuvníků. Tato nastavení úložiště můžete podle náročnosti zpracovávaných úloh změnit. Portál na základě těchto požadavků automaticky vypočítá počet disků, které se mají připojit a nakonfigurovat.

V části **Optimalizace úložiště** vyberte jednu z následujících možností:

* **Obecné:** Výchozí nastavení a podporuje většinu úloh.
* **Transakční:** Toto zpracování optimalizuje úložiště pro standardní úlohy databází OLTP.
* **Datové sklady:** Optimalizuje úložiště pro úlohy analýz a generování sestav.

![Konfigurace úložiště virtuálních počítačů SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-storage-configuration.png)

### <a name="sql-server-license"></a>Licence SQL Serveru
Pokud jste zákazníkem programu Software Assurance, můžete využít [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) k převedení vlastní SQL Server licence a uložení na prostředky. 

![Licence k virtuálnímu počítači SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatizované opravy

**Automatizované opravy** jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Zadejte den v týdnu, čas a dobu trvání intervalu údržby. V té době pak Azure nainstaluje potřebné opravy. V rámci plánování intervalu údržby se pro čas používá národní prostředí virtuálních počítačů. Pokud nechcete, aby se v rámci Azure automaticky opravoval SQL Server a operační systém, klikněte na **Zakázat**.  

![Automatizované opravy SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Další informace najdete v tématu [Automatizované opravy pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatizované zálohování

Automatické zálohování databází můžete pro všechny databáze povolit v části **Automatizované zálohování**. Automatizované zálohování je ve výchozím nastavení zakázané.

Když povolíte automatizované zálohování SQL, můžete nakonfigurovat následující nastavení:

* Doba uchování dat (dny) pro zálohování
* Účet úložiště, který se má používat pro zálohování
* Možnost šifrování a heslo pro zálohování
* Zálohování systémových databází
* Konfigurování plánu zálohování

Pokud chcete zálohy šifrovat, klikněte na **Povolit**. Pak zadejte **heslo**. Azure vytvoří certifikát pro šifrování záloh a používá zadané heslo k ochraně tohoto certifikátu. Ve výchozím nastavení je plán nastavený na automaticky, ale ruční plán můžete vytvořit tak, že vyberete **ručně**. 

![Automatizované zálohování virtuálních počítačů SQL](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Další informace najdete v tématu [Automatizované zálohování pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).


### <a name="r-services-advanced-analytics"></a>Služby R (pokročilé analýzy)

Máte možnost povolit [SQL Server R Services (Pokročilá analýza)](/sql/advanced-analytics/r/sql-server-r-services/). Tato možnost umožňuje používat pokročilou analýzu s SQL Server 2017. V okně **nastavení SQL Server** vyberte **Povolit** .


## <a name="4-review--create"></a>4. Zkontrolovat a vytvořit

Na kartě **Revize + vytvořit** zkontrolujte souhrn a vyberte **vytvořit** a vytvořte SQL Server, skupinu prostředků a prostředky zadané pro tento virtuální počítač.

Nasazení můžete monitorovat z webu Azure Portal. Tlačítko **Oznámení** v horní části obrazovky zobrazuje základní stav nasazení.

> [!NOTE]
> Abyste si udělali představu o tom, jak dlouho nasazování trvá, nasadil jsem virtuální počítač s SQL Serverem pro oblast Východní USA s výchozím nastavením. Dokončení tohoto testovacího nasazení trvalo přibližně 12 minut. Na základě vaší oblasti a vybraného nastavení ale můžete zaznamenat kratší nebo delší čas nasazení.

## <a id="remotedesktop"></a>Otevření virtuálního počítače pomocí Vzdálené plochy

Podle následujícího postupu se připojte k virtuálnímu počítači s SQL Serverem pomocí Vzdálené plochy:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po připojení k virtuálnímu počítači s SQL Serverem můžete spustit SQL Server Management Studio a připojit se pomocí ověřování systému Windows se svými přihlašovacími údaji místního správce. Pokud jste povolili ověřování SQL Serveru, můžete se také připojit pomocí ověřování SQL Serveru a použít k tomu přihlašovací jméno a heslo SQL Serveru, které jste nakonfigurovali během zřizování.

Přístup k počítači vám umožňuje podle potřeb přímo měnit nastavení počítače a SQL Serveru. Můžete například nakonfigurovat nastavení brány firewall nebo změnit nastavení konfigurace SQL Serveru.

## <a id="connect"></a>Vzdálené připojení k SQL Serveru

V tomto návodu jste vybrali **veřejný** přístup k virtuálnímu počítači a **SQL Server ověřování**. Tato nastavení automaticky nakonfigurovala virtuální počítač tak, aby povoloval připojení k SQL Serveru z libovolného klienta přes internet (za předpokladu, že má správné přihlašovací údaje SQL Serveru).

> [!NOTE]
> Pokud jste nevybrali veřejný přístup během zřizování, můžete prostřednictvím portálu změnit nastavení připojení SQL po zřízení. Další informace najdete v tématu popisujícím [změnu nastavení připojení SQL](virtual-machines-windows-sql-connect.md#change).

Následující části ukazují, jak se připojit přes Internet k instanci virtuálního počítače s SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > V tomto příkladu se používá běžný port 1433. Tato hodnota se ale bude muset upravit, pokud se během nasazování SQL Server virtuálního počítače zadal jiný port (například 1401). 


## <a name="next-steps"></a>Další postup

Další informace o používání SQL Serveru v Azure najdete v tématu [SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) a [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).
