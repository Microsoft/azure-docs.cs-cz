---
title: Zřízení virtuálního počítače pomocí portálu Azure
description: Tento návod popisuje možnosti pro vytváření virtuálních počítačů Windows SQL Server 2017 na webu Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 1fdf776570b6f10a363fb98dfe343387d86219d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249786"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Jak zřídit virtuální počítač Windows SQL Server na webu Azure Portal

Tato příručka obsahuje podrobnosti o různých možnostech, které jsou k dispozici při vytváření virtuálního počítače Windows SQL Server na webu Azure Portal. Tento článek popisuje více možností konfigurace než [rychlý start virtuálního počítače SQL Server](quickstart-sql-vm-create-portal.md), který jde více prostřednictvím jedné možné úlohy zřizování. 

Pomocí této příručky můžete vytvořit vlastní virtuální počítač SQL Server. Nebo ji použít jako odkaz na dostupné možnosti na webu Azure Portal.

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a>Obrázky galerie virtuálních počítačů serveru SQL Server

Při vytváření virtuálního počítače SQL Server můžete vybrat jednu z několika předkonfigurovaných bitových kopií z galerie virtuálních počítačů. Následující kroky ukazují, jak vybrat jednu z bitových kopií SERVERU SQL Server 2017.

1. V levé nabídce portálu Azure vyberte **Azure SQL.** Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Výběrem **možnosti + Přidat** otevřete stránku **možnosti Vybrat nasazení SQL.** Další informace můžete zobrazit výběrem **možnosti Zobrazit podrobnosti**. 
1. Zadejte `2017` vyhledávací pole serveru SQL Server na dlaždici **virtuálních počítačů SQL** a v rozevíracím sezname vyberte **bezplatnou licenci SQL Serveru: SQL Server 2017 Developer na Windows Serveru 2016.** 


   ![Výběr bitové kopie virtuálního virtuálního aplikace SQL](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > Edice Developer se používá v tomto návodu, protože se jedná o plnohodnotnou bezplatnou edici sql serveru pro testování vývoje. Platíte jenom náklady na provozování virtuálního počítače. Můžete však zvolit některý z obrázků, které chcete použít v tomto návodu. Popis dostupných bitových kopií naleznete v [přehledu virtuálních počítačů SQL Server Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Náklady na licencování pro SQL Server jsou začleněny do cen za sekundu vytvářeného virtuálního zařízení a liší se podle edice a jader. Sql Server Developer Edition je však zdarma pro vývoj nebo testování (není produkční) a SQL Express je zdarma pro lehké úlohy (méně než 1 GB paměti, méně než 10 GB úložiště). Můžete také přinést vlastní licenci (BYOL) a platit jenom za virtuální počítač. Tyto názvy bitových kopií mají předponu {BYOL}. 
   >
   > Další informace o těchto možnostech najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).


1. Vyberte **Vytvořit**.


## <a name="1-configure-basic-settings"></a>1. Konfigurace základních nastavení


Na kartě **Základy** zadejte následující informace:

* V **části Podrobnosti projektu**zkontrolujte, zda je vybráno správné předplatné. 
*  V části **Skupina prostředků** vyberte ze seznamu existující skupinu prostředků nebo zvolte **Vytvořit nový** a vytvořte novou skupinu prostředků. Skupina prostředků je kolekce souvisejících prostředků v Azure (virtuální počítače, účty úložiště, virtuální sítě atd.). 

    ![Předplatné](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Použití nové skupinu prostředků je užitečné, pokud testujete nasazení SQL Serveru v Azure nebo se snažíte o něm dozvědět více. Až s testováním skončíte, odstraňte skupinu prostředků. Automaticky se tím odstraní virtuální počítač se všemi prostředky spojenými s danou skupinu prostředků. Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../../../azure-resource-manager/management/overview.md).


* V **části Podrobnosti instance**:
    1. Zadejte jedinečný **název virtuálního počítače**.  
    1. Zvolte umístění pro vaši **oblast**. 
    1. Pro účely této příručky ponechejte **možnosti dostupnosti** nastavené na _není nutná redundance infrastruktury_. Další informace o možnostech dostupnosti naleznete v [tématu Dostupnost](../../windows/availability.md). 
    1. V seznamu **Obrázek** vyberte _bezplatnou licenci sql serveru: SQL Server 2017 Developer v systému Windows Server 2016_.  
    1. Zvolte **změnit velikost** pro **velikost** virtuálního počítače a vyberte nabídku **A2 Basic.** Nezapomeňte vyčistit své prostředky, jakmile budete hotovi s nimi, aby se zabránilo neočekávaným poplatkům. Doporučené velikosti a konfiguraci počítačů pro produkční úlohy najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

    ![Podrobnosti instance](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Odhadované měsíční náklady zobrazené v okně **Zvolit velikost** nezahrnují náklady na licencování SQL Serveru. Tento odhad jsou náklady na samotný virtuální ms. Pro expresní a vývojářské edice SQL Server je tento odhad celkové odhadované náklady. Pro ostatní edice se podívejte na [stránku s cenami pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a vyberte cílovou edici vašeho SQL Serveru. Přečtěte si taky [pokyny k cenám pro virtuální počítače](virtual-machines-windows-sql-server-pricing-guidance.md) a [velikosti](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)SQL Serveru Azure pro virtuální počítače .

* V části **Účet správce**zadejte uživatelské jméno a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Účet správce](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* V části **Pravidla příchozího portu**zvolte **Povolit vybrané porty** a v rozevíracím řádu vyberte **rdp (3389).** 

   ![Pravidla portů pro příchozí provoz](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Konfigurace volitelných funkcí

### <a name="disks"></a>Disky

Na kartě **Disky** nakonfigurujte možnosti disku. 

* V části **Typ disku operačního systému**vyberte v rozevíracím souboru typ disku, který chcete pro operační systém. Premium se doporučuje pro produkční systémy, ale není k dispozici pro základní virtuální počítače. Chcete-li využít premium SSD, změňte velikost virtuálního počítače. 
* V části **Upřesnit**vyberte **možnost Ano** v části Používat **spravované disky**.

   > [!NOTE]
   > Microsoft pro SQL Server doporučuje Spravované disky. Spravované disky se starají o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se Spravovanými disky ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v tématu [Přehled spravovaných disků Azure](../managed-disks-overview.md). Podrobnosti o spravovaných discích ve skupině dostupnosti najdete v tématu [Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti](../manage-availability.md).

![Nastavení disku virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Síťové služby

Na kartě **Síť** nakonfigurujte možnosti sítě. 

* Vytvořte novou **virtuální síť**nebo použijte existující virtuální síť pro virtuální počítač SQL Server. Určete také **podsíť.** 

* V části **Skupina zabezpečení sítě NIC**vyberte buď základní skupinu zabezpečení, nebo rozšířenou skupinu zabezpečení. Výběr základní možnosti umožňuje vybrat příchozí porty pro virtuální počítač SQL Server (stejné hodnoty, které byly nakonfigurovány na kartě **Basic).** Výběr rozšířené možnosti umožňuje zvolit existující skupinu zabezpečení sítě nebo vytvořit novou. 

* Můžete provést další změny nastavení sítě nebo zachovat výchozí hodnoty.

![Nastavení sítě virtuálních virtuálních zařízení SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorování

Na kartě **Monitorování** nakonfigurujte monitorování a automatické vypnutí. 

* Azure umožňuje **diagnostiku spouštění** ve výchozím nastavení se stejným účtem úložiště určeným pro virtuální počítač. Zde můžete změnit tato nastavení a také povolit **diagnostiku hosta operačního systému**. 
* Na této kartě můžete povolit systém **přiřazenou spravovanou identitu** a **automatické vypnutí.** 

![Nastavení správy virtuálních zařízení SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurace nastavení serveru SQL Server

Na kartě **Nastavení serveru SQL Server** nakonfigurujte konkrétní nastavení a optimalizace pro SQL Server. Nastavení, která můžete nakonfigurovat pro SQL Server, zahrnují následující:

| Nastavení |
| --- |
| [Připojení](#connectivity) |
| [Ověřování](#authentication) |
| [Integrace se službou Azure Key Vault](#azure-key-vault-integration) |
| [Konfigurace úložiště](#storage-configuration) |
| [Automatické opravy](#automated-patching) |
| [Automatické zálohování](#automated-backup) |
| [Machine Learning Services](#machine-learning-services) |


### <a name="connectivity"></a>Připojení

V části **Připojení SQL** zadejte typ přístupu, který chcete mít k instanci SQL Serveru na tomto virtuálním počítači. Pro účely tohoto návodu vyberte **možnost Veřejné (internet),** chcete-li povolit připojení k serveru SQL Server ze počítačů nebo služeb na internetu. Pokud je tato možnost vybrána, Azure automaticky nakonfiguruje bránu firewall a skupinu zabezpečení sítě tak, aby povolovaly provoz na vybraném portu.

> [!TIP]
> Ve výchozím nastavení SQL Server naslouchá na dobře známém portu **1433**. Pokud chcete zvýšit zabezpečení, změňte port v předchozím dialogovém okně tak, aby SQL Server naslouchal na jiném než výchozím portu, například 1401. Pokud změníte port, musíte se připojit pomocí tohoto portu z libovolného klientského nástroje, jako je například SSMS.

![Zabezpečení virtuálních vzato SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Aby bylo možné se k SQL Serveru připojovat prostřednictvím internetu, musíte také povolit ověřování SQL Serveru, které je popsané v následující části.

Pokud raději nechcete povolovat připojení k databázovému stroji prostřednictvím internetu, zvolte jednu z následujících možností:

* **Místní (jen uvnitř virtuálního počítače):** Umožňuje připojení k SQL Serveru pouze v rámci virtuálního počítače.
* **Privátní (uvnitř virtuální sítě):** Umožňuje připojení k SQL Serveru z počítačů nebo služeb ve stejné virtuální síti.

Obecně se doporučuje zvýšit zabezpečení výběrem nejvíce omezujícího připojení, které váš scénář umožňuje. Všechny možnosti je ale možné zabezpečit prostřednictvím pravidel skupin zabezpečení sítě a ověřování SQL Serveru a Windows. Skupinu zabezpečení sítě můžete upravit po vytvoření virtuálního počítače. Další informace najdete v tématu [Informace o zabezpečení pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Ověřování

Pokud vyžadujete ověřování serveru SQL Server, klikněte na kartě **Nastavení serveru SQL** na **povolit** v části **Ověřování SQL.**

![Ověřování SQL Serveru](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Pokud máte v plánu přístup k serveru SQL Server přes internet (možnost veřejné připojení), musíte povolit ověřování SQL zde. Veřejný přístup k SQL Serveru vyžaduje použití ověřování SQL Serveru.

Pokud povolíte ověřování SQL Serveru, zadejte **přihlašovací jméno** a **heslo**. Toto přihlašovací jméno je nakonfigurováno jako přihlášení ověřování serveru SQL Server a člen pevné role serveru **sysadmin.** Další informace o režimech ověřování najdete v tématu [Volba režimu ověřování](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Pokud ověřování SQL Serveru nepovolíte, můžete pro připojení k instanci SQL Serveru používat účet místního správce ve virtuálním počítači.


### <a name="azure-key-vault-integration"></a>Integrace se službou Azure Key Vault

Chcete-li ukládat tajné klíče zabezpečení v Azure pro šifrování, vyberte **nastavení serveru SQL Server**a přejděte dolů na integraci **trezoru klíčů Azure**. Vyberte **Povolit** a vyplňte požadované informace. 

![Integrace se službou Azure Key Vault](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

V následující tabulce jsou uvedeny parametry, které jsou nezbytné pro konfiguraci Integrace se službou Azure Key Vault.

| PARAMETR | POPIS | PŘÍKLAD |
| --- | --- | --- |
| **Adresa URL služby Key Vault** |Umístění služby Key Vault |https:\//contosokeyvault.vault.azure.net/ |
| **Název objektu zabezpečení** |Hlavní název služby Azure Active Directory. Tento název se také označuje jako ID klienta. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Tajný kód objektu zabezpečení** |Tajný klíč objektu zabezpečení služby Azure Active Directory. Tento tajný klíč se také označuje jako Tajný klíč klienta. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Název přihlašovacího údaje** |**Název přihlašovacího údaje:** Integrace se službou Azure Key Vault vytvoří přihlašovací údaje v rámci SQL Serveru, díky čemuž mají virtuální počítače přístup do trezoru klíčů. Zvolte název pro tyto přihlašovací údaje. |moje_přihlaš1 |

Další informace najdete v tématu [Konfigurace Integrace se službou Azure Key Vault pro virtuální počítače Azure](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Konfigurace úložiště

Na kartě **Nastavení serveru SQL Server** včásti Konfigurace **úložiště**vyberte **Změnit konfiguraci,** chcete-li otevřít stránku Konfigurace úložiště optimalizované pro výkon a určit požadavky na úložiště.

![Konfigurace úložiště virtuálních počítačů SQL](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

V části **Optimalizace úložiště** vyberte jednu z následujících možností:

* **Obecné:** Výchozí nastavení a podporuje většinu úloh.
* **Transakční zpracování** optimalizuje úložiště pro tradiční databázové úlohy OLTP.
* **Datové sklady:** Optimalizuje úložiště pro úlohy analýz a generování sestav.

![Konfigurace úložiště virtuálních počítačů SQL](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Můžete ponechat hodnoty ve výchozím nastavení nebo můžete ručně změnit topologii úložiště tak, aby vyhovovala vašim potřebám videa VOPS. Další informace naleznete v [tématu konfigurace úložiště](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>Licence serveru SQL Server
Pokud jste zákazníkem programu Software Assurance, můžete využít [hybridní výhodu Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) k tomu, abyste si přinesli vlastní licenci SQL Serveru a ušetřili prostředky. 

![Licence virtuálního virtuálního uživatele SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatizované opravy

**Automatizované opravy** jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Zadejte den v týdnu, čas a dobu trvání intervalu údržby. V té době pak Azure nainstaluje potřebné opravy. V rámci plánování intervalu údržby se pro čas používá národní prostředí virtuálních počítačů. Pokud nechcete, aby se v rámci Azure automaticky opravoval SQL Server a operační systém, klikněte na **Zakázat**.  

![Automatické opravy virtuálního virtuálního připojení SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Další informace najdete v tématu [Automatizované opravy pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatizované zálohování

Automatické zálohování databází můžete pro všechny databáze povolit v části **Automatizované zálohování**. Automatizované zálohování je ve výchozím nastavení zakázané.

Když povolíte automatizované zálohování SQL, můžete nakonfigurovat následující nastavení:

* Doba uchování dat (dny) pro zálohování
* Účet úložiště, který se má používat pro zálohování
* Možnost šifrování a heslo pro zálohování
* Zálohování systémových databází
* Konfigurování plánu zálohování

Pokud chcete zálohy šifrovat, klikněte na **Povolit**. Pak zadejte **heslo**. Azure vytvoří certifikát pro šifrování záloh a používá zadané heslo k ochraně tohoto certifikátu. Ve výchozím nastavení je plán nastaven automaticky, ale ruční plán můžete vytvořit výběrem **možnosti Ručně**. 

![Automatické zálohování virtuálního počítače SQL](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Další informace najdete v tématu [Automatizované zálohování pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).


### <a name="machine-learning-services"></a>Machine Learning Services

Máte možnost povolit [služby Machine Learning Services](/sql/advanced-analytics/). Tato možnost umožňuje používat strojové učení s Pythonem a R na SQL Serveru 2017. V okně **Nastavení serveru SQL Server** vyberte **Povolit.**


## <a name="4-review--create"></a>4. Recenze + vytvořit

Na kartě **Revize + vytvořit** zkontrolujte souhrn a vyberte **Vytvořit** pro vytvoření SQL Serveru, skupiny prostředků a prostředků určených pro tento virtuální počítač.

Nasazení můžete monitorovat z webu Azure Portal. Tlačítko **Oznámení** v horní části obrazovky zobrazuje základní stav nasazení.

> [!NOTE]
> Abyste si udělali představu o tom, jak dlouho nasazování trvá, nasadil jsem virtuální počítač s SQL Serverem pro oblast USA – východ s výchozím nastavením. Dokončení tohoto testovacího nasazení trvalo přibližně 12 minut. Na základě vaší oblasti a vybraného nastavení ale můžete zaznamenat kratší nebo delší čas nasazení.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a>Otevření virtuálního počítače pomocí vzdálené plochy

Podle následujícího postupu se připojte k virtuálnímu počítači s SQL Serverem pomocí Vzdálené plochy:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po připojení k virtuálnímu počítači s SQL Serverem můžete spustit SQL Server Management Studio a připojit se pomocí ověřování systému Windows se svými přihlašovacími údaji místního správce. Pokud jste povolili ověřování SQL Serveru, můžete se také připojit pomocí ověřování SQL Serveru a použít k tomu přihlašovací jméno a heslo SQL Serveru, které jste nakonfigurovali během zřizování.

Přístup k počítači vám umožňuje podle potřeb přímo měnit nastavení počítače a SQL Serveru. Můžete například nakonfigurovat nastavení brány firewall nebo změnit nastavení konfigurace SQL Serveru.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a>Vzdálené připojení k serveru SQL Server

V tomto návodu jste vybrali **veřejný** přístup pro virtuální počítač a **ověřování serveru SQL Server**. Tato nastavení automaticky nakonfigurovala virtuální počítač tak, aby povoloval připojení k SQL Serveru z libovolného klienta přes internet (za předpokladu, že má správné přihlašovací údaje SQL Serveru).

> [!NOTE]
> Pokud jste nevybrali veřejný přístup během zřizování, můžete prostřednictvím portálu změnit nastavení připojení SQL po zřízení. Další informace najdete v tématu popisujícím [změnu nastavení připojení SQL](virtual-machines-windows-sql-connect.md#change).

Následující části ukazují, jak se připojit přes internet k instanci virtuálního počítače SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Tento příklad používá společný port 1433. Tato hodnota však bude nutné upravit, pokud jiný port (například 1401) byl zadán během nasazení virtuálního počítače SQL Server. 


## <a name="next-steps"></a>Další kroky

Další informace o používání SQL Serveru v Azure najdete v tématu [SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) a [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).
