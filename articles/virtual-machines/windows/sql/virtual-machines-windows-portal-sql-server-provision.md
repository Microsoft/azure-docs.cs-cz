---
title: Zřizování Průvodce pro virtuální počítače Windows SQL serveru na portálu Azure | Microsoft Docs
description: Tento postup průvodce popisuje možnosti pro vytváření virtuálních počítačů Windows SQL serveru 2017 na portálu Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: jroth
ms.openlocfilehash: d2bcabf845a2178abbebe8f2998d58b462e37c78
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072313"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Postup zřízení virtuálního počítače s Windows SQL Server na portálu Azure

Tato příručka obsahuje podrobnosti o různých možnostech, které jsou k dispozici při vytváření virtuálního počítače s SQL serverem Windows na portálu Azure. Tento článek popisuje další možnosti konfigurace, než [rychlý start virtuální počítač s SQL serverem](quickstart-sql-vm-create-portal.md), které jde další přes jeden možné zřizování úloh. 

Tuto příručku použijte k vytvoření vlastní virtuální počítač SQL Server. Nebo použijte jako pomůcku možnosti dostupné na portálu Azure.

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a id="select"></a> Bitové kopie Galerie virtuálních počítačů systému SQL Server

Při vytváření virtuálního počítače s SQL serverem, vyberete jednu z několika předem nakonfigurovaná bitových kopií z Galerie virtuálního počítače. Následující kroky ukazují, jak vybrat jednu z imagí SQL Server 2017.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu.

1. Na webu Azure Portal klikněte na **Vytvořit prostředek**. Na Portálu se otevře okno **Nový**.

1. V okně **Nový** klikněte na **Compute** a pak klikněte na **Zobrazit všechno**.

   ![Okno Nová služba Compute](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Do pole hledání zadejte **SQL Server 2017** a stiskněte ENTER.

1. Pak klikněte na ikonu **Filtr**.

1. V oknech Filtr zaškrtněte podkategorii **Založené na Windows** a jako vydavatele zaškrtněte **Microsoft**. Pak kliknutím na **Hotovo** vyfiltrujte z výsledků image Windows s SQL Serverem publikované Microsoftem.

   ![Okno Azure Virtual Machines](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade2.png)

1. Projděte si dostupné image SQL Serveru. U každé image je označena příslušná verze SQL Serveru a operační systém.

1. Vyberte image s názvem **Bezplatná licence SQL Serveru: SQL Server 2017 Developer v systému Windows Server 2016**.

   > [!TIP]
   > Developer edition se používá v tomto návodu, protože je plně funkční, bezplatná edice systému SQL Server pro vývoj, testování. Platíte jenom náklady na provozování virtuálního počítače. Jste však můžete vybrat kteroukoli z obrázky, které chcete použít v tomto průvodci. Popis dostupných imagí najdete v tématu [virtuálního počítače se systémem SQL Server Windows přehled](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Náklady na licencování SQL serveru jsou součástí ceny za sekundu virtuálního počítače vytvořit a liší se edice a počet jader. SQL Server Developer edition je však zdarma pro vývoj/testování (ne produkční) a systém SQL Express je zdarma pro prosté úlohy (menší než 1 GB paměti menší než 10 GB úložiště). Můžete také uvést – vaše – vlastní – licence (BYOL) a platím pouze pro virtuální počítač. Tyto názvy bitových kopií mají předponu {BYOL}. 
   >
   > Další informace o těchto možnostech najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. V části **Vybrat model nasazení** ověřte, že je vybraný **Resource Manager**. U nových virtuálních počítačů se doporučuje používat model nasazení Resource Manageru. 

1. Klikněte na možnost **Vytvořit**.

    ![Vytvoření virtuálního počítače s SQL Serverem pomocí Resource Manageru](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a id="configure"></a> Možnosti konfigurace
Pro konfiguraci virtuálního počítače s SQL Serverem se používá pět oken.

| Krok | Popis |
| --- | --- |
| **Základy** |[Konfigurace základního nastavení](#1-configure-basic-settings) |
| **Velikost** |[Volba velikosti virtuálního počítače](#2-choose-virtual-machine-size) |
| **Nastavení** |[Konfigurace volitelných funkcí](#3-configure-optional-features) |
| **Nastavení SQL Serveru** |[Konfigurace nastavení SQL Serveru](#4-configure-sql-server-settings) |
| **Souhrn** |[Kontrola souhrnných informací](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Konfigurace základního nastavení

V okně **Základy** zadejte následující informace:

* Zadejte jedinečný **název** virtuálního počítače.

* Vyberte **SSD** jako typ disku virtuálního počítače pro zajištění optimálního výkonu.

* Zadejte **uživatelské jméno** pro účet místního správce ve virtuálním počítači. Tento účet je také přidán do pevné role serveru na serveru **sysadmin** SQL Serveru.

* Zadejte silné **heslo**.

* Pokud máte více předplatných, ověřte, že je předplatné správné pro nový virtuální počítač.

* Do pole **Skupina prostředků** zadejte název pro novou skupinu prostředků. Pokud chcete použít existující skupinu prostředků, klikněte na **Použít existující**. Skupina prostředků je kolekce souvisejících prostředků v Azure (virtuální počítače, účty úložiště, virtuální sítě atd.).

  > [!NOTE]
  > Použití nové skupinu prostředků je užitečné, pokud testujete nasazení SQL Serveru v Azure nebo se snažíte o něm dozvědět více. Až s testováním skončíte, odstraňte skupinu prostředků. Automaticky se tím odstraní virtuální počítač se všemi prostředky spojenými s danou skupinu prostředků. Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../../../azure-resource-manager/resource-group-overview.md).

* Vyberte **umístění** pro oblast Azure k hostování tohoto nasazení.

* Kliknutím na **OK** uložte nastavení.

    ![Okno Základy pro SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Volba velikosti virtuálního počítače

V kroku **Velikost** zvolte velikost virtuálního počítače v okně **Zvolit velikost**. V okně se po jeho otevření zobrazí doporučené velikosti počítačů na základě image, kterou jste vybrali.

> [!IMPORTANT]
> Odhadované měsíční náklady zobrazené v okně **Zvolit velikost** nezahrnují náklady na licencování SQL Serveru. Tento odhad jsou náklady na virtuální počítač samostatně. Tento odhad pro edice Express a vývojáře systému SQL Server je celkový odhadované náklady. Pro ostatní edice se podívejte na [stránku s cenami pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a vyberte cílovou edici vašeho SQL Serveru. Podívejte se také na [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

![Možnosti velikosti virtuálního počítače s SQL Serverem](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Doporučené velikosti a konfiguraci počítačů pro produkční úlohy najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

> [!NOTE]
> Další informace o velikostech virtuálních počítačů najdete v tématu [Velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zvolte velikost počítače a potom klikněte na **Vybrat**.

## <a name="3-configure-optional-features"></a>3. Konfigurace volitelných funkcí

V okně **Nastavení** nakonfigurujte úložiště, sítě a monitorování Azure pro virtuální počítač.

* V části **Úložiště** vyberte **Ano** u možnosti **Použít Managed Disks**.

   > [!NOTE]
   > Microsoft pro SQL Server doporučuje službu Managed Disks. Služba Managed Disks se stará o úložiště na pozadí. Navíc, pokud jsou virtuální počítače se službou Managed Disks ve stejné skupině dostupnosti, Azure distribuuje prostředky úložiště pro zajištění odpovídající redundance. Další informace najdete v tématu [Azure spravované přehled disky] [.. / spravované overview.md disky). Podrobnosti o spravovaných discích ve skupině dostupnosti najdete v tématu [Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti](../manage-availability.md).

* V části **sítě**, vyberte všechny příchozí porty, že v **vyberte veřejné příchozí porty** seznamu. Například pokud chcete vzdálenou plochu do virtuálního počítače, vyberte **protokolu RDP (3389)** portu.

   ![Příchozí porty](./media/quickstart-sql-vm-create-portal/inbound-ports.png)

   > [!NOTE]
   > Můžete vybrat **MS SQL (1433)** port pro přístup k systému SQL Server vzdáleně. Je to ale není nezbytné tady, protože **nastavení systému SQL Server** krok obsahuje také tuto možnost. Pokud vyberete port 1433 v tomto kroku, otevře se frekvence požadovaná nastavení na **nastavení systému SQL Server** krok.

   Můžete udělat jiné změny nastavení sítě, nebo ponechte výchozí hodnoty.

* Azure umožňuje **monitorování** ve výchozím nastavení se stejným účtem, jaký je nastavený pro virtuální počítač. Tato nastavení tady můžete změnit.

* V části **sadu dostupnosti**, můžete ponechat výchozí **žádné** pro účely tohoto postupu. Pokud budete chtít nastavit Skupiny dostupnosti AlwaysOn SQL, nakonfigurujte dostupnost tak, aby se předešlo opětovnému vytvoření virtuálního počítače.  Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Po dokončení konfigurace těchto nastavení klikněte na **OK**.

## <a name="4-configure-sql-server-settings"></a>4. Konfigurace nastavení SQL Serveru
V okně **Nastavení SQL Serveru** nakonfigurujte konkrétní nastavení a optimalizace pro SQL Server. Pro SQL Server můžete například nakonfigurovat následující nastavení.

| Nastavení |
| --- |
| [Připojení](#connectivity) |
| [Ověřování](#authentication) |
| [Konfigurace úložiště](#storage-configuration) |
| [Automatizované opravy](#automated-patching) |
| [Automatizované zálohování](#automated-backup) |
| [Integrace se službou Azure Key Vault](#azure-key-vault-integration) |
| [SQL Server Machine Learning Services](#sql-server-machine-learning-services) |

### <a name="connectivity"></a>Připojení

V části **Připojení SQL** zadejte typ přístupu, který chcete mít k instanci SQL Serveru na tomto virtuálním počítači. Pro účely tohoto návodu, vyberte **veřejné (internet)** umožňující připojení k systému SQL Server z počítačů nebo služeb na Internetu. Když je vybraná tato možnost, Azure automaticky nakonfiguruje bránu firewall a skupinu zabezpečení sítě tak, aby umožňovaly přenosy na portu 1433.

![Možnosti připojení SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

> [!TIP]
> Ve výchozím nastavení SQL Server naslouchá na dobře známém portu **1433**. Pokud chcete zvýšit zabezpečení, změňte port v předchozím dialogovém okně tak, aby SQL Server naslouchal na jiném než výchozím portu, například 1401. Pokud změníte port, je nutné připojit pomocí tohoto portu z žádné nástroje klienta, jako je například aplikace SSMS.

Aby bylo možné se k SQL Serveru připojovat prostřednictvím internetu, musíte také povolit ověřování SQL Serveru, které je popsané v následující části.

Pokud raději nechcete povolovat připojení k databázovému stroji prostřednictvím internetu, zvolte jednu z následujících možností:

* **Místní (jen uvnitř virtuálního počítače):** Umožňuje připojení k SQL Serveru pouze v rámci virtuálního počítače.
* **Privátní (uvnitř virtuální sítě):** Umožňuje připojení k SQL Serveru z počítačů nebo služeb ve stejné virtuální síti.

Obecně se doporučuje zvýšit zabezpečení výběrem nejvíce omezujícího připojení, které váš scénář umožňuje. Všechny možnosti je ale možné zabezpečit prostřednictvím pravidel skupin zabezpečení sítě a ověřování SQL Serveru a Windows. Skupinu zabezpečení sítě můžete upravit po vytvoření virtuálního počítače. Další informace najdete v tématu [Informace o zabezpečení pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-security.md).

### <a name="authentication"></a>Authentication

Pokud budete chtít vyžadovat ověřování SQL Serveru, klikněte v části **Ověřování SQL** na **Povolit**.

![Ověřování SQL Serveru](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Pokud máte v plánu pro přístup k systému SQL Server prostřednictvím Internetu (nastavením veřejného připojení), je nutné povolit ověřování SQL sem. Veřejný přístup k SQL Serveru vyžaduje použití ověřování SQL Serveru.

Pokud povolíte ověřování SQL Serveru, zadejte **přihlašovací jméno** a **heslo**. Toto uživatelské jméno se nakonfiguruje jako přihlašovací jméno ověřování SQL Serveru a člen pevné role serveru **sysadmin**. Další informace o režimech ověřování najdete v tématu [Volba režimu ověřování](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Pokud ověřování SQL Serveru nepovolíte, můžete pro připojení k instanci SQL Serveru používat účet místního správce ve virtuálním počítači.

### <a name="storage-configuration"></a>Konfigurace úložiště

Klikněte na **Konfigurace úložiště** a zadejte požadavky na úložiště.

![Konfigurace úložiště SQL Serveru](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Pokud jste virtuální počítač ručně nakonfigurovali tak, aby používal Storage úrovně Standard, tato možnost není dostupná. Automatická optimalizace úložiště je k dispozici pouze pro Premium Storage.

> [!TIP]
> Počet zastavení a horní omezení každého posuvníku závisí na velikosti vybraného virtuálního počítače. Větší a výkonnější virtuální počítač umožňuje větší vertikální navýšení kapacity.

Požadavky můžete zadat jako vstupně-výstupní operace za sekundu (IOPs), propustnost v MB/s a celkovou velikost úložiště. Tyto hodnoty nakonfigurujte pomocí posuvníků. Tato nastavení úložiště můžete podle náročnosti zpracovávaných úloh změnit. Portál na základě těchto požadavků automaticky vypočítá počet disků, které se mají připojit a nakonfigurovat.

V části **Optimalizace úložiště** vyberte jednu z následujících možností:

* **Obecné:** Výchozí nastavení a podporuje většinu úloh.
* **Transakční:** Toto zpracování optimalizuje úložiště pro standardní úlohy databází OLTP.
* **Datové sklady:** Optimalizuje úložiště pro úlohy analýz a generování sestav.

### <a name="automated-patching"></a>Automatizované opravy

**Automatizované opravy** jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Zadejte den v týdnu, čas a dobu trvání intervalu údržby. V té době pak Azure nainstaluje potřebné opravy. V rámci plánování intervalu údržby se pro čas používá národní prostředí virtuálních počítačů. Pokud nechcete, aby se v rámci Azure automaticky opravoval SQL Server a operační systém, klikněte na **Zakázat**.  

![Automatizované opravy pro SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Další informace najdete v tématu [Automatizované opravy pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatizované zálohování

Automatické zálohování databází můžete pro všechny databáze povolit v části **Automatizované zálohování**. Automatizované zálohování je ve výchozím nastavení zakázané.

Když povolíte automatizované zálohování SQL, můžete nakonfigurovat následující nastavení:

* Doba uchování dat (dny) pro zálohování
* Účet úložiště, který se má používat pro zálohování
* Možnost šifrování a heslo pro zálohování
* Zálohování systémových databází
* Konfigurování plánu zálohování

Pokud chcete zálohy šifrovat, klikněte na **Povolit**. Pak zadejte **heslo**. Azure vytvoří certifikát pro šifrování záloh a používá zadané heslo k ochraně tohoto certifikátu.

![Automatizované zálohování SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup2.png)

 Další informace najdete v tématu [Automatizované zálohování pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integrace se službou Azure Key Vault

Pokud budete chtít ukládat tajné klíče zabezpečení v Azure pro šifrování, klikněte na **Integrace se službou Azure Key Vault** a klikněte na **Povolit**.

![Integrace se službou Azure Key Vault pro SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

V následující tabulce jsou uvedeny parametry, které jsou nezbytné pro konfiguraci Integrace se službou Azure Key Vault.

| PARAMETR | POPIS | PŘÍKLAD |
| --- | --- | --- |
| **Adresa URL služby Key Vault** |Umístění služby Key Vault |https://contosokeyvault.vault.azure.net/ |
| **Název objektu zabezpečení** |Hlavní název služby Azure Active Directory. Tento název se také označuje jako ID klienta. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Tajný kód objektu zabezpečení** |Tajný klíč objektu zabezpečení služby Azure Active Directory. Tento tajný klíč se také označuje jako Tajný klíč klienta. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Název přihlašovacího údaje** |**Název přihlašovacího údaje:** Integrace se službou Azure Key Vault vytvoří přihlašovací údaje v rámci SQL Serveru, díky čemuž mají virtuální počítače přístup do trezoru klíčů. Zvolte název pro tyto přihlašovací údaje. |moje_přihlaš1 |

Další informace najdete v tématu [Konfigurace Integrace se službou Azure Key Vault pro virtuální počítače Azure](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services

Máte možnost povolit službu [SQL Server Machine Learning Services](https://msdn.microsoft.com/library/mt604845.aspx). Tato možnost umožňuje použít pokročilou analýzu s SQL Server 2017. V okně **Nastavení SQL Serveru** klikněte na **Povolit**.

![Povolení služby SQL Server Machine Learning Services](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

Po dokončení konfigurace nastavení SQL Serveru klikněte na **OK**.

## <a name="5-review-the-summary"></a>5. Kontrola souhrnných informací

V okně **Souhrn** zkontrolujte souhrn a pak kliknutím na **Koupit** vytvořte SQL Server, skupinu prostředků a prostředky zadané pro tento virtuální počítač.

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

Následující části vysvětlují, jak se připojit přes internet k vaší instanci virtuální počítač SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Další kroky

Další informace o používání SQL Serveru v Azure najdete v tématu [SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) a [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).