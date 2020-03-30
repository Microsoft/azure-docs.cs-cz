---
title: Nastavení Micro Focus CICS BankDemo pro vývojáře Micro Focus Enterprise 4.0 na virtuálních počítačích Azure
description: Spusťte aplikaci Micro Focus BankDemo na virtuálních počítačích Azure(VM), abyste se naučili používat Micro Focus Enterprise Server a Enterprise Developer.
author: sread
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 4491fc137c2c85e2be605f5e58fde6fd422efbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67621340"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Nastavení micro focus CICS BankDemo pro micro focus enterprise developer 4.0 v Azure

Když v Azure nastavíte Micro Focus Enterprise Server 4.0 a Enterprise Developer 4.0, můžete otestovat nasazení úloh IBM z/OS. Tento článek ukazuje, jak nastavit CICS BankDemo, ukázkovou aplikaci, která je dodávána s enterprise developerem.

CS je zkratka pro Customer Information Control System, transakční platformu používanou mnoha online sálovými aplikacemi. Aplikace BankDemo je skvělá pro učení, jak fungují Enterprise Server a Enterprise Developer a jak spravovat a nasazovat skutečnou aplikaci kompletní s terminály zelené obrazovky.

## <a name="prerequisites"></a>Požadavky

- Virtuální účet s [vývojářem pro podniky](set-up-micro-focus-azure.md). Mějte na paměti, že enterprise developer má úplnou instanci Enterprise Server na něm pro účely vývoje a testování. Tato instance je instance Enterprise Server používá pro ukázku.

- [Vydání SQL Server 2017 Express](https://www.microsoft.com/sql-server/sql-server-editions-express). Stáhněte si a nainstalujte ho na virtuální m. pro vývojáře pro podniky. Enterprise Server vyžaduje databázi pro správu oblastí CICS a aplikace BankDemo také používá databázi SQL Serveru s názvem BANKDEMO. Tato ukázka předpokládá, že používáte SQL Server Express pro obě databáze. Při instalaci vyberte základní instalaci.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS se používá pro správu databází a spuštění Skriptu T-SQL. Stáhněte si a nainstalujte ho na virtuální m. pro vývojáře pro podniky.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) s nejnovější aktualizací Service Pack nebo [visual studio community](https://visualstudio.microsoft.com/vs/community/), které si můžete zdarma stáhnout.

- Rumba Desktop nebo jiný emulátor 3270.

## <a name="configure-the-windows-environment"></a>Konfigurace prostředí Windows

Po instalaci enterprise developer 4.0 na virtuální počítač je nutné nakonfigurovat instanci podnikového serveru, který je dodáván s ním. Chcete-li to provést, musíte nainstalovat několik dalších funkcí systému Windows následujícím způsobem.

1. Pomocí protokolu RDP se přihlaste k vytvořenému virtuálnímu počítači podnikového serveru 4.0.

2. Klepněte na ikonu **Hledat** vedle tlačítka **Start** a zadejte **funkce systému Windows**. Otevře se Průvodce přidáním rolí a funkcí serveru.

3. Vyberte **roli webového serveru (IIS)** a zkontrolujte následující možnosti:

    - Nástroje pro správu webu
    - Kompatibilita správy služby IIS 6 (vyberte všechny dostupné funkce)
    - Konzola pro správu služby IIS
    - Skripty a nástroje pro správu služby IIS
    - Služba správy služby IIS

4. Vyberte **služby World Wide Web Services**a zkontrolujte následující možnosti:

     Funkce vývoje aplikací:
    - Rozšiřitelnost rozhraní .NET
    - ASP.NET
    - Běžné funkce PROTOKOLU HTTP: Přidání všech dostupných funkcí
    - Stav a diagnostika: Přidání všech dostupných funkcí
    - Zabezpečení:
        - Základní ověření
        - Ověřování systému Windows

5. Vyberte **službu aktivace procesů systému Windows** a všechny její podřízené položky.

6. V **části Funkce**zkontrolujte rozhraní Microsoft **.NET Framework 3.5.1**a zkontrolujte následující možnosti:

    - Aktivace protokolu HTTP nadace Windows Communication Foundation
    - Aktivace nehttp služby Windows Communication Foundation

7. V **části Funkce**zkontrolujte rozhraní Microsoft **.NET Framework 4.6**a zkontrolujte následující možnosti:

   - Aktivace pojmenovaného kanálu
   - Aktivace protokolem TCP
   - Sdílení portů TCP

     ![Průvodce přidáním rolí a funkcí: Služby rolí](media/01-demo-roles.png)

8. Po výběru všech možností klepněte na tlačítko **Další** a nainstalujte je.

9. Po funkcích systému Windows přejděte na **ovládací panel nástroje pro správu \> systému a zabezpečení \> **a vyberte možnost **Služby**. Posuňte se dolů a ujistěte se, že jsou spuštěny následující služby a nastavte na **automaticky**:

    - **NetTcpPortSdílení**
    - **Adaptér naslouchacího procesu Net.Pipe**
    - **Adaptér naslouchacího procesu Net.tcp**

10. Chcete-li konfigurovat podporu služby IIS a was, vyhledejte v nabídce **příkazový řádek micro Focus Enterprise Developer Command Prompt (64 bit)** a spusťte jej jako **správce**.

11. Typ **wassetup –i** a stiskněte **klávesu Enter**.

12. Po spuštění skriptu můžete okno zavřít.

## <a name="configure-the-local-system-account-for-sql-server"></a>Konfigurace místního systémového účtu pro SQL Server

Některé procesy podnikového serveru musí být schopny přihlásit sql server a vytvářet databáze a další objekty. Tyto procesy používají místní systémový účet, takže je nutné dát sysadmin oprávnění k tomuto účtu.

1. Spusťte **smyk SSMS** a klepnutím na **tlačítko Připojit** se připojte k místnímu serveru SQLEXPRESS server pomocí ověřování systému Windows. Měla by být k dispozici v seznamu **Název serveru.**

2. Vlevo rozbalte složku **Zabezpečení** a vyberte **Přihlášení**.

3. Vyberte **NT\\AUTHORITY SYSTEM** a vyberte **Vlastnosti**.

4. Vyberte **Role serveru** a zkontrolujte **sysadmin**.

     ![Okno Průzkumník objektů SSMS: Vlastnosti přihlášení](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Vytvoření databáze BankDemo a všech jejích objektů

1. Otevřete **Průzkumníka Windows** a přejděte na **\\C:\\Uživatelé\\\\\\veřejné dokumenty\\Micro Focus\\Enterprise Developer\\Ukázky\\Mainframe CICS DotNet\\BankDemo\\SQL**.

2. Zkopírujte obsah souboru **BankDemoCreateAll.SQL** do schránky.

3. Otevřete **SSMS**. Vpravo klikněte na **Server** a vyberte **Nový dotaz**.

4. Vložte obsah schránky do pole **Nový dotaz.**

5. Spusťte SQL kliknutím **na Spustit** z karty **Příkaz** nad dotazem.

Dotaz by měl být spuštěn bez chyb. Po dokončení máte ukázkovou databázi pro aplikaci BankDemo.

![Výstup SQLQuery1.sql](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Ověření, zda byly vytvořeny databázové tabulky a objekty

1. Klepněte pravým tlačítkem myši na databázi **BANKDEMO** a vyberte příkaz **Aktualizovat**.

2. Rozbalte **databázi** a vyberte **tabulky**. Měli byste vidět něco jako následující.

     ![Tabulka BANKDEMO rozbalená v Průzkumníku objektů](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Vytvoření aplikace v podnikovém vývojáři

1. Otevřete Visual Studio a přihlaste se.

2. V nabídce **Soubor** vyberte **možnost Otevřít projekt/řešení**, přejděte na **c:\\Uživatelé\\veřejné\\\\dokumenty, ukázky vývojáře\\\\\\mikroaplikací , mainframCICS\\\\DotNet\\BankDemo**, a vyberte soubor **sln.**

3. Udělejte si čas na prozkoumání objektů. Cobol programy jsou zobrazeny v Průzkumníku řešení s rozšířením CBL spolu s CopyBooks (CPY) a JCL.

4. Klepněte pravým tlačítkem myši na projekt **BankDemo2** a vyberte příkaz **Nastavit jako spouštěcí projekt**.

    > [!NOTE]
    > Projekt BankDemo využívá HCOSS (Host Compatibility Option for SQL Server), který se nepoužívá pro tuto ukázku.

5. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **BankDemo2** a vyberte **příkaz Sestavit**.

    > [!NOTE]
    > Vytváření na úrovni řešení má za následek chyby, protože HCOSS nebyl nakonfigurován.

6. Při projektu je sestaven, zkontrolujte **okno Výstup.** Měla by vypadat jako na obrázku níže.

     ![Výstupní okno zobrazující úspěšné sestavení](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Nasazení aplikace BankDemo do databáze Region

1. Otevřete příkazový řádek enterprise developer (64 bit) jako správce.

2. Přejděte na **%PUBLIC%\\\\\\Documents\\Micro\\Focus\\Enterprise\\Developer\\ukázky Mainframe CICS DotNet BankDemo**.

3. Na příkazovém řádku spusťte **bankdemodbdeploy** a zahrňte parametr pro databázi, do které se má nasadit, například:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Ujistěte se, že používáte lomítko\\(/) ne zpětné lomítko( ). Tento skript běží na chvíli.

![Správa: Okno příkazového řádku pro vývojáře podniku](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Vytvoření oblasti BankDemo v podniku Správce pro rozhraní .NET

1. Otevřete **rozhraní Enterprise Server for .NET Administration.**

2. Chcete-li spustit modul snap-in konzoly MMC, zvolte z nabídky **Start** systému Windows **možnost Micro Focus Enterprise Developer \> Configuration \> Enterprise Server for .NET Admin**. (Pro systém Windows Server zvolte **Micro Focus Enterprise Developer \> Enterprise Server pro správce rozhraní .NET**).

3. Rozbalte kontejner **Oblasti** v levém podokně a klepněte pravým tlačítkem myši na **CICS**.

4. Vyberte **Definovat oblast,** chcete-li vytvořit novou oblast CICS nazvanou **BANKDEMO**, hostovanou v (místní) databázi.

5. Zadejte instanci databázového serveru, klepněte na tlačítko **Další**a zadejte název oblasti **BANKDEMO**.

     ![Dialogové okno Definovat oblast](media/07-demo-cics.png)

6. Chcete-li vybrat soubor definice oblasti pro databázi mezi oblastmi, vyhledejte **\_oblast bankdemo\_db.config** v **C:\\Uživatelé\\Veřejné\\dokumenty\\Micro Focus\\Enterprise Developer\\Ukázky\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Definovat region - název oblasti: BANKDEMO](media/08-demo-cics.png)

7. Klikněte na **Finish** (Dokončit).

## <a name="create-xa-resource-definitions"></a>Vytvořit definice prostředků XA

1. V levém podokně uživatelského rozhraní **Enterprise Server for .NET Administration** vyberte **položku System**a potom **položku Definice prostředků XA**. Toto nastavení definuje, jak region spolupracuje s podnikovým serverem a aplikačními databázemi.

2. Klikněte pravým tlačítkem myši na **definice prostředků XA** a vyberte **přidat instanci serveru**.

3. V rozevíracím poli vyberte **Instanci databázové služby**. Bude to místní počítač SQLEXPRESS.

4. Vyberte instanci z kontejneru **Definice\\prostředků XA (název stroje sqlexpress)** a klepněte na tlačítko **Přidat**.

5. Vyberte **položku Databáze XA Definice prostředku** a zadejte **příkaz BANKDEMO** pro **název** a **oblast**.

     ![Obrazovka Nový definice prostředku databáze XA](media/09-demo-xa.png)

6. Kliknutím na tři tečky (**...**) zobrazíte Průvodce připojovacím řetězcem. Do **pole Název serveru**zadejte **(místní)\\sqlexpress**. V **případě přihlášení**vyberte možnost Ověřování **systému Windows**. Pro název databáze zadejte **BANKDEMO**

     ![Obrazovka Upravit připojovací řetězec](media/10-demo-string.png)

7. Otestujte připojení.

## <a name="start-the-bankdemo-region"></a>Spuštění regionu BANKDEMO

> [!NOTE]
> První krok je důležitý: Je nutné nastavit oblast pro použití definice prostředku XA, kterou jste právě vytvořili.

1. Přejděte do **oblasti BANDEMO CICS** v **kontejneru oblastí**a v podokně **Akce** vyberte Upravit spouštěcí **soubor oblasti.** Přejděte dolů na vlastnosti SQL a zadejte **bankdemo** pro **název prostředku XA**, nebo použijte tři tečky k jeho výběru.

2. Kliknutím na ikonu **Uložit** uložte změny.

3. V podokně **konzoly** klepněte pravým tlačítkem myši na **oblast BANKDEMO CICS** a vyberte příkaz **Start/Stop .**

4. V dolní části pole **Start/Stop Region,** které se zobrazí v prostředním podokně, vyberte **Start**. Po několika sekundách začíná oblast.

     ![Pole start/stop SQL](media/11-demo-sql.png)

     ![CICS Region BANKDEMO - Spuštěno obrazovka](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Vytvoření naslouchací proces

Vytvořte naslouchací proces pro relace TN3270, které přistupují k aplikaci BankDemo.

1. V levém podokně **rozbalte položku Editory konfigurace** a vyberte **naslouchací proces**.

2. Klepněte na ikonu **Otevřít soubor** a vyberte soubor **seelistener.exe.config.** Tento soubor bude upraven a načten při každém spuštění podnikového serveru.

3. Všimněte si dvou dříve definovaných oblastí (ESDEMO a JCLDEMO).

4. Chcete-li vytvořit novou oblast pro bankdemo, klepněte pravým tlačítkem myši na **položku Oblasti**a vyberte příkaz **Přidat oblast**.

5. Vyberte **oblast BANKDEMO**.

6. Přidejte kanál TN3270 tak, že kliknete pravým tlačítkem myši na **oblast BANKDEMO** a vyberete **přidat kanál**.

7. Do **pole Název**zadejte **TN3270**. Do **su-jako port**, zadejte **9024**. Aplikace ESDEMO používá port 9230, takže musíte použít jiný port.

8. Chcete-li soubor uložit, klepněte na ikonu **Uložit** nebo zvolte **Uložit** \> **soubor**.

9. Chcete-li naslouchací proces spustit, klepněte na ikonu **Spustit naslouchací proces** nebo zvolte **Možnosti** \> **Spustit naslouchací proces**.

     ![Okna Editoru konfigurace posluchače](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurace Rumba pro přístup k aplikaci BankDemo

Poslední věc, kterou musíte udělat, je nakonfigurovat relaci 3270 pomocí Rumba, emulátoru 3270. Tento krok umožňuje přístup k aplikaci BankDemo prostřednictvím naslouchací proces, který jste vytvořili.

1. Z nabídky **Start** systému Windows spusťte plochu Rumba.

2. V položce nabídky **Připojení** vyberte **TN3270**.

3. Klikněte na **Vložit** a zadejte **127.0.0.1** pro IP adresu a **9024** pro uživatelem definovaný port.

4. V dolní části dialogového okna klepněte na **tlačítko Připojit**. Zobrazí se černá obrazovka CICS.

5. Zadejte **bank,** chcete-li zobrazit úvodní obrazovku 3270 pro aplikaci BankDemo.

6. Do id uživatele zadejte **B0001** a heslo zadejte cokoli. Otevře se první obrazovka BANK20.

![Zobrazení hlavního](media/14-demo.png)
![počítače Úvodní obrazovka Hlavní počítač – Rumba – ukázková obrazovka subsystému](media/15-demo.png)

Blahopřejeme! Nyní spouštějíte aplikaci CICS v Azure pomocí serveru Micro Focus Enterprise Server.

## <a name="next-steps"></a>Další kroky

- [Spuštění podnikového serveru v kontejnerech Dockeru v Azure](run-enterprise-server-container.md)
- [Migrace sálových počítačů – portál](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Řešení potíží](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demystifikace sálového počítače k migraci Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
