---
title: Nastavení Micro Focus CICS BankDemo pro Micro Enterprise Developer 4,0 on Azure Virtual Machines
description: Využijte BankDemo aplikaci Micro Focus na Azure Virtual Machines (virtuálních počítačů), abyste se seznámili s používáním Enterprise serveru a podnikového vývojáře pro vývoj na Micro.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: b2cac7a8310482305467a1cb3ed3127eb18ae416
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915772"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Nastavení Micro Focus CICS BankDemo pro Micro Enterprise Developer 4,0 v Azure

Když nastavíte Micro Enterprise Server 4,0 a Enterprise Developer 4,0 v Azure, můžete otestovat nasazení úloh IBM z/OS. V tomto článku se dozvíte, jak nastavit CICS BankDemo, ukázkovou aplikaci, která se dodává s podnikovým vývojářem.

CICs představuje systém pro řízení informací o zákaznících, platformu transakce, kterou používá mnoho online sálových aplikací. Aplikace BankDemo je skvělým způsobem, jak se naučit, jak Enterprise Server a Enterprise Developer pracují a jak spravovat a nasazovat vlastní aplikace s koncovými body zelené obrazovky.

> [!NOTE]
> Už brzy: pokyny pro nastavení [Micro Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) na virtuálních počítačích Azure.

## <a name="prerequisites"></a>Požadavky

- Virtuální počítač s [podnikovým vývojářem](set-up-micro-focus-azure.md). Mějte na paměti, že Enterprise Developer má pro účely vývoje a testování kompletní instanci podnikového serveru. Tato instance je instancí podnikového serveru, který se používá pro ukázku.

- [Edice SQL Server 2017 Express](https://www.microsoft.com/sql-server/sql-server-editions-express). Stáhněte ho a nainstalujte na podnikovém virtuálním počítači pro vývojáře. Enterprise Server vyžaduje databázi pro správu CICSch oblastí a aplikace BankDemo používá také databázi SQL Server s názvem BANKDEMO. Tato ukázka předpokládá, že používáte SQL Server Express pro obě databáze. Po instalaci vyberte základní instalaci.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017&preserve-view=true) (SSMS). SSMS se používá ke správě databází a spuštění skriptu T-SQL. Stáhněte ho a nainstalujte na podnikovém virtuálním počítači pro vývojáře.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) s nejnovější aktualizací Service Pack nebo [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), kterou si můžete zdarma stáhnout.

- Rumba Desktop nebo jiný emulátor 3270.

## <a name="configure-the-windows-environment"></a>Konfigurace prostředí systému Windows

Po instalaci Enterprise Developer 4,0 na virtuální počítač musíte nakonfigurovat instanci podnikového serveru, který je součástí tohoto virtuálního počítače. K tomu je potřeba nainstalovat několik dalších funkcí Windows následujícím způsobem.

1. Pomocí protokolu RDP se přihlaste k virtuálnímu počítači Enterprise Server 4,0, který jste vytvořili.

2. Klikněte na ikonu **hledání** vedle tlačítka **Start** a zadejte **funkce systému Windows**. Otevře se Průvodce Správce serveru přidání rolí a funkcí.

3. Vyberte **role Webový server (IIS)** a pak zkontrolujte následující možnosti:

    - Nástroje webové správy
    - Kompatibilita správy služby IIS 6 (výběr všech dostupných funkcí)
    - Konzola pro správu služby IIS
    - Skripty a nástroje správy služby IIS
    - Služba správy služby IIS

4. Vyberte **webové služby** a podívejte se na tyto možnosti:

     Funkce pro vývoj aplikací:
    - Rozšiřitelnost rozhraní .NET
    - ASP.NET
    - Společné funkce protokolu HTTP: přidání všech dostupných funkcí
    - Stav a diagnostika: přidat všechny dostupné funkce
    - Zabezpečení:
        - Základní ověření
        - Ověřování systému Windows

5. Vyberte **Aktivační službu procesů systému Windows** a všechny její podřízené položky.

6. V případě **funkcí** ověřte **rozhraní Microsoft .NET Framework 3.5.1** a podívejte se na následující možnosti:

    - Windows Communication Foundation aktivace protokolem HTTP
    - Windows Communication Foundation aktivace jiným protokolem než HTTP

7. V případě **funkcí** ověřte **rozhraní Microsoft .NET Framework 4,6** a podívejte se na následující možnosti:

   - Aktivace pojmenovaného kanálu
   - Aktivace protokolem TCP
   - Sdílení portů TCP

     ![Průvodce přidáním rolí a funkcí: služby rolí](media/01-demo-roles.png)

8. Když vyberete všechny možnosti, nainstaluje se kliknutím na **Další** .

9. Po použití funkcí Windows použijte **Ovládací panely \> \> Nástroje pro správu systému a zabezpečení** a vyberte **služby**. Posuňte se dolů a ujistěte se, že jsou spuštěné následující služby a že jsou nastavené na **Automatické**:

    - **NetTcpPortSharing**
    - **Adaptér naslouchání NET. pipe**
    - **Adaptér naslouchání NET. TCP**

10. Pokud chcete nakonfigurovat službu IIS a byla podporovaná, najděte z nabídky **Micro Enterprise Developer Command Prompt (64)** a spusťte jako **správce**.

11. Zadejte **wassetup – i** a stiskněte klávesu **ENTER**.

12. Po spuštění skriptu můžete okno zavřít.

## <a name="configure-the-local-system-account-for-sql-server"></a>Nakonfigurujte účet Local System pro SQL Server

Některé procesy podnikového serveru musí být schopné přihlašovat SQL Server a vytvářet databáze a další objekty. Tyto procesy používají účet místního systému, takže musíte dát autoritu sysadmin k tomuto účtu.

1. Spusťte **SSMS** a kliknutím na **připojit** se připojte k místnímu serveru SQLEXPRESS pomocí ověřování systému Windows. Měl by být k dispozici v seznamu **název serveru** .

2. Na levé straně rozbalte složku **zabezpečení** a vyberte **přihlašovací jména**.

3. Vyberte **NT Authority \\ System** a vyberte **vlastnosti**.

4. Vyberte **role serveru** a ověřte **sysadmin**.

     ![SSMS Průzkumník objektů okno: vlastnosti přihlášení](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Vytvoření databáze BankDemo a všech jejích objektů

1. Otevřete **Průzkumníka Windows** a přejděte na **oddíl C: \\ Uživatelé \\ Public \\ Documents \\ Micro \\ Enterprise Developer \\ Samples \\ sálový sálový \\ CICS \\ dotnet \\ BankDemo \\ SQL**.

2. Zkopírujte obsah souboru **BankDemoCreateAll. SQL** do schránky.

3. Otevřete **SSMS**. Na pravé straně klikněte na **Server** a vyberte **Nový dotaz**.

4. Vloží obsah schránky do **nového pole dotazu** .

5. Spusťte SQL kliknutím na tlačítko **Spustit** z karty **příkaz** nad dotazem.

Dotaz by měl běžet bez chyb. Po dokončení budete mít ukázkovou databázi pro aplikaci BankDemo.

![Výstup SQLQuery1. SQL](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Ověřte, že byly vytvořeny tabulky a objekty databáze.

1. Klikněte pravým tlačítkem na databázi **BANKDEMO** a vyberte **aktualizovat**.

2. Rozbalte položku **databáze** a vyberte možnost **tabulky**. Měl by se zobrazit něco podobného jako následující.

     ![Tabulka BANKDEMO se rozšířila v Průzkumník objektů](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Sestavení aplikace v podnikovém vývojáři

1. Otevřete Visual Studio a přihlaste se.

2. V nabídce **soubor** vyberte možnost **Otevřít projekt nebo řešení**, přejděte na jazyk **C: \\ Uživatelé \\ Public \\ Documents \\ Micro \\ Enterprise Developer \\ Samples \\ sálové \\ CICS \\ dotnet \\ BankDemo** a vyberte soubor **sln** .

3. Vyhledá objekty nějakou dobu. COBOL programy se zobrazují v Průzkumník řešení s rozšířením CBL společně s CopyBooks (CPY) a JCL.

4. Klikněte pravým tlačítkem na projekt **BankDemo2** a vyberte **nastavit jako spouštěný projekt**.

    > [!NOTE]
    > Projekt BankDemo využívá HCOSS (možnost kompatibility hostitelů pro SQL Server), která se pro tuto ukázku nepoužívá.

5. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt **BankDemo2** a vyberte možnost **sestavit**.

    > [!NOTE]
    > Při sestavování na úrovni řešení dojde k chybám, protože HCOSS není nakonfigurovaný.

6. Po sestavení projektu si Projděte okno **výstup** . Měla by vypadat jako na obrázku níže.

     ![Okno výstup zobrazující úspěšné sestavení](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Nasazení aplikace BankDemo do databáze oblasti

1. Otevřete příkazový řádek (64 bitů) podnikového vývojáře jako správce.

2. Přejděte na: **% Public% \\ Documents \\ Micro \\ \\ Samples Micro ukázky pro Enterprise \\ sálový sálový sálový \\ CICS \\ dotnet \\ BankDemo**.

3. Na příkazovém řádku spusťte **bankdemodbdeploy** a uveďte parametr pro databázi, do které chcete nasadit, například:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Ujistěte se, že používáte lomítko (/), nikoli zpětné lomítko ( \\ ). Tento skript se spouští po chvíli.

![Správa: okno podnikového Developer Command Prompt](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Vytvoření oblasti BankDemo v podnikovém správci pro .NET

1. Otevřete uživatelské rozhraní **pro správu podnikového serveru pro .NET** .

2. Modul snap-in konzoly MMC spustíte tak, že v nabídce **Start** systému Windows zvolíte možnost **Micro the Enterprise Developer \> Configuration \> Enterprise Server for .NET Admins**. (Pro Windows Server vyberte **Micro Enterprise Developer \> Enterprise Server for .NET admin**).

3. Rozbalte kontejner **oblasti** v levém podokně a potom klikněte pravým tlačítkem na **CICS**.

4. Vyberte **definovat oblast** pro vytvoření nové oblasti CICS s názvem **BANKDEMO**, která je hostovaná v databázi (místní).

5. Zadejte instanci databázového serveru, klikněte na tlačítko **Další** a zadejte název oblasti **BANKDEMO**.

     ![Dialog definovat oblast – dialogové okno](media/07-demo-cics.png)

6. Pokud chcete vybrat soubor definice oblasti pro databázi mezi oblastmi, najděte **oblast \_ bankdemo \_db.config** v **C: \\ Uživatelé \\ Public \\ Documents \\ Micro \\ Enterprise Developer \\ Samples \\ sálový sálový \\ CICS \\ dotnet \\ bankdemo**.

     ![Definování oblasti – název oblasti: BANKDEMO](media/08-demo-cics.png)

7. Klikněte na **Finish** (Dokončit).

## <a name="create-xa-resource-definitions"></a>Vytvoření definic prostředků XA

1. V levém podokně uživatelského rozhraní **pro správu .NET** rozbalte **systém** a pak proveďte **definice prostředků XA**. Toto nastavení definuje, jak oblast spolupracuje s podnikovým serverem a aplikačními databázemi.

2. Klikněte pravým tlačítkem na **definice prostředků XA** a vyberte **přidat instanci serveru**.

3. V rozevíracím seznamu vyberte možnost **instance služby databáze**. Bude to místní počítač SQLEXPRESS.

4. Vyberte instanci z kontejneru s **definicemi prostředků XA (nazev_pocitace \\ SQLEXPRESS)** a klikněte na **Přidat**.

5. Vyberte **položku definice prostředků XA databáze** a pak jako **název** a **oblast** zadejte **BANKDEMO** .

     ![Obrazovka definice nového prostředku XA databáze](media/09-demo-xa.png)

6. Kliknutím na tři tečky (**...**) zobrazte Průvodce připojovacím řetězcem. Jako **název serveru** zadejte **(Local) \\ SQLEXPRESS**. V případě **přihlášení** vyberte **ověřování systému Windows**. Jako název databáze zadejte **BANKDEMO** .

     ![Obrazovka upravit připojovací řetězec](media/10-demo-string.png)

7. Otestujte připojení.

## <a name="start-the-bankdemo-region"></a>Začátek oblasti BANKDEMO

> [!NOTE]
> První krok je důležitý: v oblasti musíte nastavit, aby se použila definice prostředků XA, kterou jste právě vytvořili.

1. V **kontejneru oblasti** přejděte do **oblasti BANDEMO CICS** a v podokně **Akce** vyberte **Upravit spouštěcí soubor oblasti** . Posuňte se dolů k vlastnostem SQL a zadejte **bankdemo** pro **název prostředku XA** nebo použijte tři tečky k jejímu výběru.

2. Kliknutím na ikonu **Uložit** uložte změny.

3. V podokně **konzoly** klikněte pravým tlačítkem na **oblast BANKDEMO CICS** a vyberte **Spustit/zastavit oblast**.

4. V dolní části pole **Spustit/zastavit** , které se zobrazí v prostředním podokně vyberte možnost **Spustit**. Po několika sekundách se oblast spustí.

     ![Okno pro spuštění/zastavení SQL](media/11-demo-sql.png)

     ![Úvodní obrazovka CICS oblasti BANKDEMO – Začínáme](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Vytvoření naslouchacího procesu

Vytvořte naslouchací proces pro relace TN3270, které přistupují k aplikaci BankDemo.

1. V levém podokně rozbalte **editory konfigurace** a vyberte **naslouchací proces**.

2. Klikněte na ikonu **otevřít soubor** a vyberte soubor **seelistener.exe.config** . Tento soubor se upraví a načte se při každém spuštění podnikového serveru.

3. Všimněte si výše definovaných dvou oblastí (ESDEMO a JCLDEMO).

4. Pokud chcete vytvořit novou oblast pro BANKDEMO, klikněte pravým tlačítkem na **oblasti** a vyberte **Přidat oblast**.

5. Vyberte **oblast BANKDEMO**.

6. Přidejte kanál TN3270 kliknutím pravým tlačítkem myši na **oblast BANKDEMO** a výběrem možnosti **Přidat kanál**.

7. Jako **název** zadejte **TN3270**. Jako **port** zadejte **9024**. Aplikace ESDEMO používá port 9230, takže potřebujete použít jiný port.

8. Soubor uložíte tak, že kliknete na ikonu **Uložit** nebo  zvolíte \> **Uložit** soubor.

9. Pokud chcete spustit naslouchací proces, klikněte na ikonu **Spustit naslouchací proces** nebo vyberte **Možnosti** \> **Spustit naslouchací proces**.

     ![Okna editoru konfigurace naslouchacího procesu](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurace Rumba pro přístup k aplikaci BankDemo

Poslední věcí, kterou musíte udělat, je konfigurace relace 3270 pomocí Rumba, emulátoru 3270. Tento krok umožňuje přístup k aplikaci BankDemo pomocí naslouchacího procesu, který jste vytvořili.

1. V nabídce **Start** systému Windows spusťte Rumba Desktop.

2. V nabídce **připojení** vyberte položku **TN3270**.

3. Klikněte na **Vložit** a zadejte **127.0.0.1** pro IP adresu a **9024** pro port definovaný uživatelem.

4. V dolní části dialogového okna klikněte na **připojit**. Zobrazí se černá CICS obrazovka.

5. Typ **bank** zobrazí úvodní obrazovku 3270 pro aplikaci BankDemo.

6. Jako ID uživatele zadejte **B0001** a heslo a zadejte cokoli. Otevře se první obrazovka BANK20.

![Úvodní obrazovka zobrazení sálového počítače v sálovém sálovém zobrazení ](media/14-demo.png)
 ![ -Rumba-subsystému](media/15-demo.png)

Blahopřejeme! Nyní máte v Azure spuštěnou aplikaci CICS s využitím podnikového serveru Micro.

## <a name="next-steps"></a>Další kroky

- [Provozování podnikového serveru v kontejnerech Docker v Azure](run-enterprise-server-container.md)
- [Migrace sálového počítače – portál](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Virtual Machines](../../../linux/overview.md)
- [Řešení potíží](../../../troubleshooting/index.yml)
- [Migrace Demystifying z sálového počítače do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
