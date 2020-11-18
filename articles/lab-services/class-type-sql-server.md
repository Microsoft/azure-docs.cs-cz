---
title: Nastavení testovacího prostředí pro správu a vývoj pomocí Azure SQL Database | Azure Lab Services
description: Naučte se, jak nastavit testovací prostředí pro správu a vývoj pomocí Azure SQL Database.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 50f71ee1ce59f5809fe8905c58f0399cf484f11a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659705"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Nastavení testovacího prostředí pro správu a vývoj pomocí SQL Server

Tento článek popisuje, jak nastavit testovací prostředí pro základní třídu SQL Server Management a vývoj v Azure Lab Services.  Koncepty databází jsou jedním z úvodních kurzů, které jsou v rámci vysokoškolského ministerstva v oblasti počítačové vědy. Jazyk SQL (Structured Query Language) (SQL) je mezinárodní standard.  SQL je standardním jazykem pro relaci správy databází, včetně přidávání, přístupu a správy obsahu v databázi.  Je nejužitečnější pro své rychlé zpracování, prověřenou spolehlivost, jednoduchost a flexibilitu použití.

V tomto článku si ukážeme, jak nastavit šablonu virtuálního počítače v testovacím prostředí pomocí sady [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)a [Azure Data Studio](https://github.com/microsoft/azuredatastudio).  V tomto testovacím prostředí budeme používat jednu sdílenou [SQL Server databázi](../azure-sql/database/sql-database-paas-overview.md) pro celé testovací prostředí. [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) je databázový stroj PaaS (Platform as a Service) z Azure.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K nastavení tohoto testovacího prostředí potřebujete předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/), ještě než začnete. Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí najdete v [kurzu nastavení účtu testovacího prostředí](./tutorial-setup-lab-account.md). Můžete použít i existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí

Pro účet testovacího prostředí povolte nastavení popsaná v následující tabulce. Další informace o tom, jak povolit image Marketplace, najdete v tématu [určení imagí z Marketplace dostupných pro tvůrce testovacích prostředí](specify-marketplace-images.md).

| Nastavení účtu testovacího prostředí | Pokyny |
| ------------------- | ------------ |
| Image z Marketplace | Povolte pro použití v rámci vašeho účtu testovacího prostředí komunitní image sady Visual Studio 2019 (nejnovější verze) na Windows 10 Enterprise N (x64). |

### <a name="shared-resource-configuration"></a>Konfigurace sdíleného prostředku

Chcete-li použít sdílený prostředek v testovacích službách, musíte nejprve vytvořit virtuální síť a prostředky samotné.  Pokud chcete vytvořit virtuální síť a připojit ji k testovacímu prostředí, postupujte podle pokynů [v tématu Vytvoření testovacího prostředí se sdíleným prostředkem v Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Pamatujte si, že všechny prostředky externí se službami testovacího prostředí se budou účtovat zvlášť a nebudou se zahrnovat do odhadu nákladů testovacího prostředí.

>[!WARNING]
>Sdílené prostředky pro testovací prostředí by měly být nastavené před vytvořením testovacího prostředí.  Pokud virtuální síť není v [partnerském vztahu k účtu testovacího](how-to-connect-peer-virtual-network.md) prostředí *před* vytvořením testovacího prostředí, testovací prostředí nebude mít přístup ke sdílenému prostředku.

Teď, když je strana věcí v síti zpracována, umožňuje vytvořit databázi SQL Server.  Vytvoříme izolovanou [databázi](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) , protože se jedná o možnost nejrychlejší nasazení Azure SQL Database.  Pro další možnosti nasazení vytvořte [elastický fond](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), [spravovanou instanci](../azure-sql/managed-instance/instance-create-quickstart.md)nebo [virtuální počítač SQL](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md).

1. V nabídce Azure Portal vyberte **vytvořit nový prostředek**.
2. Vyberte **SQL Database** a klikněte na tlačítko **vytvořit** .
3. Na kartě **základy** ve formuláři **vytvořit databázi SQL** vyberte skupinu prostředků pro databázi.  Budeme používat *SQLDB-RG*.
4. Jako **název databáze** zadejte *classlabdb*.
5. V části nastavení **serveru** klikněte na **vytvořit novou** a vytvořte nový server pro uložení databáze.
6. V informačním rámečku **Nový server** zadejte název serveru.  Budeme používat *classlabdbserver*.  Název serveru musí být globálně jedinečný.
7. Jako *azureuser* zadejte **přihlašovací jméno správce serveru**.
8. Zadejte heslo, které jste si zapamatovat.  Heslo musí mít délku alespoň osm znaků a musí obsahovat speciální znaky.
9. Jako **umístění** vyberte oblast.  Pokud je to možné, zadejte stejné umístění jako účet testovacího prostředí a virtuální síť s partnerským vztahem, abyste minimalizovali latenci.
10. Kliknutím na tlačítko **OK** se vraťte do formuláře **vytvořit SQL Database** .
11. V nastavení **výpočty + úložiště** klikněte na odkaz **Konfigurovat databázi** .
12. Upravte nastavení databáze podle potřeby pro třídu.  Můžete si vybrat mezi zřizováním a možnostmi bez serveru.  V tomto příkladu použijeme možnost bez serveru s autoškálou s maximálním počtem virtuální jádra 4, min virtuální jádra z 1. Nastavíme nastavení Autopause minimálně na 1 hodinu. Klikněte na **Použít**.
13. Klikněte na tlačítko **Další: síťové** tlačítko.
14. Na kartě sítě vyberte pro **metodu připojení** privátní koncový bod.
15. V části **privátní koncové body** klikněte na **Přidat privátní koncový bod**.
16. V rozevíracím seznamu **vytvořit privátní koncový bod** vyberte stejnou skupinu prostředků jako vaše virtuální síť v partnerském vztahu k účtu testovacího prostředí.
17. V poli **umístění** vyberte stejné umístění jako virtuální síť.
18. Jako **název** zadejte *labsql-Endpt*.
19. Ponechte cílový subzdroj nastaven na SqlServer.
20. V poli **virtuální síť** vyberte stejnou virtuální síť jako partnerský vztah k účtu testovacího prostředí.
21. V poli **podsíť** vyberte podsíť, ve které chcete koncový bod hostovat.  IP adresa přiřazená ke koncovému bodu bude z rozsahu přiřazeného k této podsíti.
22. Nastavte **integraci s privátním DNS** na **ne**. Pro zjednodušení použijeme Azure DNS přes vlastní privátní zónu DNS nebo vlastní servery DNS.
23. Klikněte na **OK**.
24. Klikněte na **Další: Další nastavení**.
25. V nastavení **použít existující data** vyberte možnost **Ukázka**.  Data z databáze AdventureWorksLT budou použita při vytvoření databáze.
26. Klikněte na **Zkontrolovat a vytvořit**.
27. Klikněte na **Vytvořit**.

Po úspěšném dokončení nasazení SQL Database můžeme vytvořit testovací prostředí a nainstalovat software do počítače šablony testovacího prostředí.

### <a name="lab-settings"></a>Nastavení testovacího prostředí

Při nastavování testovacího prostředí učebny použijte nastavení v následující tabulce. Další informace o tom, jak vytvořit prostředí učebny, najdete v tématu [Nastavení kurzu pro prostředí učebny](tutorial-setup-classroom-lab.md).

| Nastavení testovacího prostředí | Hodnota/pokyny |
| ------------ | ------------------ |
| Velikost virtuálního počítače | Střední. Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy. |
| Image virtuálního počítače | Visual Studio 2019 Community (nejnovější verze) ve Windows 10 Enterprise N (x64) |

Teď, když je naše laboratoř vytvořená, můžeme upravit počítač šablony se softwarem, který potřebujeme.

## <a name="visual-studio"></a>Visual Studio

Výše zvolená image zahrnuje [Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/).  Všechny úlohy a sady nástrojů jsou už v imagi nainstalované.  Použijte Instalační program pro Visual Studio k [instalaci všech volitelných nástrojů](/visualstudio/install/modify-visual-studio?view=vs-2019) , které byste chtěli potřebovat.  [Přihlaste se k aplikaci Visual Studio](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio) , abyste mohli odemknout edici Community.

Sada Visual Studio obsahuje sadu nástrojů pro **ukládání a zpracování dat** , která zahrnuje nástroje SQL Server Data Tools (SSDT).  Další informace o schopnostech SSDT najdete v tématu [Přehled nástrojů SQL Server Data Tools](/sql/ssdt/sql-server-data-tools?view=sql-server-ver15).  Chcete-li ověřit připojení ke sdíleným SQL Server pro třídu, přečtěte si téma [připojení k databázi a procházení existujících objektů](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15). Pokud se zobrazí výzva, přidejte do [seznamu povolených počítačů](../azure-sql/database/firewall-configure.md) , které se můžou připojit k vaší instanci SQL Server, IP adresa počítače šablony.

Visual Studio podporuje několik úloh, včetně **webových & cloudových** a **desktopových &ch** úloh.  Oba tyto úlohy podporují SQL Server jako zdroj dat. Další informace o použití ASP.NET Core k SQL Server najdete [v tématu vytvoření ASP.NET Core a SQL Database aplikace v Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md) kurzu.  Pomocí knihovny [System. data. SqlClient](/dotnet/api/system.data.sqlclient) se můžete připojit k SQL Database z aplikace [Xamarin](/xamarin) .

## <a name="install-azure-data-studio"></a>Nainstalovat Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) je více databází, desktopové prostředí pro různé platformy pro odborníky na data s využitím řady místních a cloudových datových platforem v systémech Windows, MacOS a Linux.

1. Stáhněte [instalační program Azure Data Studio *systému* pro Windows](https://go.microsoft.com/fwlink/?linkid=2127432). Instalační programy pro jiné podporované operační systémy zjistíte tak, že přejdete na stránku pro stažení [Azure Data Studio](/sql/azure-data-studio/download) .
2. Na stránce **Licenční smlouva** vyberte možnost Souhlasím **se smlouvou**. Klikněte na **Next** (Další).
3. Na stránce **Select Destination Location** (Vyberte cílové umístění) klikněte na tlačítko **Next** (Další).
4. Na stránce **Select Start Menu Folder** (Vyberte složku nabídky Start) klikněte na tlačítko **Next** (Další).
5. Na stránce **Vybrat další úlohy** zaškrtněte políčko **vytvořit ikonu plochy** , pokud chcete ikonu plochy.  Klikněte na **Next** (Další).
6. Na stránce **připraveno k instalaci** klikněte na tlačítko **Další**.
7. Počkejte, než se instalační program spustí.  Klikněte na **Finish** (Dokončit).

Teď, když je Azure Data Studio nainstalovaná, nastavili jsme připojení k Azure SQL Database.

1. Na **úvodní** stránce pro Azure Data Studio klikněte na odkaz **nové připojení** .
2. V poli **Podrobnosti připojení** vyplňte potřebné informace.
    - Nastavit **Server** na *classlabdbserver.Database.Windows.NET*
    - Nastavení **uživatelského** jména na *azureuser*
    - Nastavte **heslo** na heslo použité k vytvoření databáze.
    - Ověřte **si heslo**.
    - V případě **databáze** vyberte *classlabdb*.
3. Klikněte na **Připojit**.

## <a name="install-sql-server-management-studio"></a>Nainstalovat SQL Server Management Studio

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) je integrované prostředí pro správu jakékoli infrastruktury SQL.  SSMS je nástroj používaný správci databáze k nasazení, monitorování a upgradu infrastruktury dat.

1. [Stáhněte si Management Studio SQL serveru](https://aka.ms/ssmsfullsetup). Po stažení spusťte instalační program.
2. Na **úvodní** stránce klikněte na **nainstalovat**.
3. Na stránce **Instalace byla dokončena** klikněte na tlačítko **Zavřít**.
4. Spusťte systém SQL Server Management Studio.  
5. Na stránce **proces konfigurace závislostí** klikněte na **Zavřít**.

Není-li nainstalován SSMS, můžete se [připojit a zadat dotaz na SQL Server](/sql/ssms/tutorials/connect-query-sql-server). Při nastavování připojení použijte následující hodnoty:

- Typ serveru: databázový stroj
- Název serveru: *classlabdbserver.Database.Windows.NET*
- Ověřování: SQL Server ověřování
- Přihlášení: *azureuser*
- Heslo: heslo použité k vytvoření databáze.

## <a name="cost-estimate"></a>Odhad nákladů

Pojďme pro tuto třídu pokrýt možné náklady. Odhad nezahrnuje náklady na spuštění SQL Server.  Aktuální podrobnosti o cenách databáze najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database) .

Použijeme třídu 25 studentů. Naplánovaný čas třídy je 20 hodin. Každý student navíc získá kvótu 10 hodin pro domácí nebo přiřazení mimo plánovanou dobu třídy. Velikost virtuálního počítače, kterou jsme zvolili, byla střední, což je 42 jednotek testovacího prostředí.

Zde je příklad možného odhadu nákladů pro tuto třídu:

25 studentů \* (20 naplánovaných hodin \+ 10 hodin kvót) × 0,42 USD za hodinu = 315,00 USD

>[!IMPORTANT]
>Odhad nákladů slouží pouze jako příklad pro účely. Aktuální podrobnosti o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření, Správa a publikování šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)