---
title: Nastavit Micro fokus CICS BankDemo Micro fokus Enterprise Developer 4.0 ve službě Azure Virtual Machines
description: Spusťte aplikaci BankDemo Micro fokus na Azure Virtual Machines (VMs) se naučíte, jak používat Micro fokus Enterprise Server a podnikový vývojář.
author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7afe29cb98a294b2a30020ad48f8b27264386746
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304756"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Nastavit Micro fokus CICS BankDemo Micro fokus Enterprise Developer 4.0 v Azure

Při nastavování Micro fokus Enterprise Server 4.0 a 4.0 Developer Enterprise v Azure můžete otestovat nasazení úloh IBM z/OS. Tento článek ukazuje, jak nastavit CICS BankDemo, ukázkovou aplikaci, která je součástí Enterprise Developer.

CICs jsou zahrnovaného systém řízení informací o zákaznících, transakce platformy používané mnoha online mainframových aplikací. Aplikace BankDemo se skvěle hodí pro učení, jak Enterprise Server a podnikový vývojář pracovat a jak spravovat a nasazovat aplikace skutečný s terminály zelené obrazovky.

## <a name="prerequisites"></a>Požadavky

- Virtuální počítač s [podnikový vývojář](set-up-micro-focus-azure.md). Uvědomte si, že podnikový vývojář má úplnou instanci Enterprise serveru na něm pro účely vývoje a testování. Tato instance je instance serveru Enterprise pro tuto ukázku.

- [Edice systému SQL Server 2017 Express](https://www.microsoft.com/sql-server/sql-server-editions-express). Stáhněte a nainstalujte na virtuální počítač Enterprise Developer. Enterprise Server vyžaduje databázi pro správu CICS oblastí a BankDemo aplikace také používá databázi systému SQL Server s názvem BANKDEMO. V této ukázce se předpokládá, že používáte systém SQL Server Express pro databázi i databázi. Při instalaci, vyberte základní instalaci.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). Aplikace SSMS se používá pro správu databází a spuštění skriptu T-SQL. Stáhněte a nainstalujte na virtuální počítač Enterprise Developer.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) s nejnovější aktualizací service pack nebo [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), kterou si můžete stáhnout zdarma.

- Rumba plochy nebo jiného 3270 emulátoru.

## <a name="configure-the-windows-environment"></a>Konfigurace prostředí Windows

Po instalaci služby Enterprise Developer 4.0 na virtuálním počítači, musíte nakonfigurovat instanci Enterprise serveru, která se dodává s ním. K tomu, musíte nainstalovat několik dalších funkcí Windows následujícím způsobem.

1. Přihlaste se k virtuálnímu počítači Enterprise Server 4.0 jste vytvořili pomocí protokolu RDP.

2. Klikněte na tlačítko **hledání** ikonu vedle **Start** tlačítko a typ **funkce Windows**. Otevře se správce serveru přidat role a funkce průvodce.

3. Vyberte **roli webového serveru (IIS)** a potom zkontrolujte následující možnosti:

    - Nástroje webové správy
    - Kompatibilita správy služby IIS 6 (vybrat všechny dostupné funkce)
    - Konzola pro správu služby IIS
    - Služba IIS skripty a nástroje správy
    - Služba správy služby IIS

4. Vyberte **webové služby**a zkontrolujte následující možnosti:

     Funkce pro vývoj aplikací:
    - Rozšiřitelnost rozhraní .NET
    - ASP.NET
    - Společné funkce protokolu HTTP: Přidat všechny dostupné funkce
    - Stav a Diagnostika: Přidat všechny dostupné funkce
    - Zabezpečení:
        - základního ověřování
        - Ověřování systému Windows

5. Vyberte **služby Aktivace procesu Windows** a všechny jeho podřízené položky.

6. Pro **funkce**, zkontrolujte **rozhraní Microsoft .NET framework 3.5.1**a zkontrolujte následující možnosti:

    - Windows Communication Foundation HTTP Activation
    - Aktivace jiným protokolem než HTTP Windows Communication Foundation

7. Pro **funkce**, zkontrolujte **rozhraní Microsoft .NET framework 4.6**a zkontrolujte následující možnosti:

   - Aktivace pojmenovaného kanálu
   - Aktivace protokolem TCP
   - Sdílení portů TCP

     ![Průvodce přidáním rolí a funkcí: Služby role](media/01-demo-roles.png)

8. Když vyberete všechny možnosti, klikněte na tlačítko **Další** k instalaci.

9. Po funkcí Windows přejděte na **ovládací panely \> systém a zabezpečení \> nástroje pro správu**a vyberte **služby**. Posuňte se dolů a ujistěte se, že tyto služby běží a nastavte na **automatické**:

    - **NetTcpPortSharing**
    - **Adaptér naslouchání Net.Pipe**
    - **Adaptér naslouchání NET.TCP**

10. Konfigurace podpory služby IIS a WAS, vyhledejte v nabídce **Micro fokus Enterprise Developer Command Prompt (64bitová verze)** a spuštění jako **správce**.

11. Typ **wassetup – i** a stiskněte klávesu **Enter**.

12. Po spuštění skriptu můžete okno zavřít.

## <a name="configure-the-local-system-account-for-sql-server"></a>Konfigurace místního systémového účtu pro SQL Server

Některé organizace serverové procesy musí mít možnost do přihlášení serveru SQL Server a vytvoření databáze a dalších objektů. Tyto procesy použít místní systémový účet, takže je potřeba předat autority sysadmin tomuto účtu.

1. Spusťte **SSMS** a klikněte na tlačítko **připojit** pro připojení k místním SQLEXPRESS serveru pomocí ověřování Windows. Měla by být dostupná v **název serveru** seznamu.

2. Na levé straně, rozbalte **zabezpečení** a pak zvolte položku **přihlášení**.

3. Vyberte **NT AUTHORITY\\systému** a vyberte **vlastnosti**.

4. Vyberte **role serveru** a zkontrolujte **sysadmin**.

     ![Okno Průzkumníka objektů SSMS: Vlastnosti přihlášení](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Vytvořit databázi BankDemo a všechny objekty

1. Otevřít **Windows Explorer** a přejděte do **C:\\uživatelé\\veřejné\\dokumenty\\Micro fokus\\podnikový vývojář\\ Ukázky\\Mainframových\\CICS\\DotNet\\BankDemo\\SQL**.

2. Zkopírujte obsah **BankDemoCreateAll.SQL** souboru do schránky.

3. Otevřít **SSMS**. Na pravé straně, klikněte na tlačítko **Server** a vyberte **nový dotaz**.

4. Umožňuje vložit obsah schránky do **nový dotaz** pole.

5. Provedení SQL kliknutím **Execute** z **příkaz** kartu výše dotaz.

Dotaz by měl spustit bez chyb. Po dokončení mít ukázkovou databázi pro aplikaci BankDemo.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Ověřte, zda byly vytvořeny databáze tabulek a objektů

1. Klikněte pravým tlačítkem myši **BANKDEMO** databáze a vyberte **aktualizovat**.

2. Rozbalte **databáze** a vyberte **tabulky**. By měl vypadat přibližně jako následující.

     ![Tabulka BANKDEMO rozbalení v Průzkumníku objektů](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Vytvoření aplikace v podnikové vývojáře

1. Otevřít Visual Studio a přihlaste se.

2. V části **souboru** možnost nabídky, vyberte **otevřít projekt či řešení**, přejděte na **C:\\uživatelé\\veřejné\\dokumenty\\Micro Fokus\\podnikový vývojář\\ukázky\\Mainframových\\CICS\\DotNet\\BankDemo**a vyberte **sln**souboru.

3. Prozkoumání objektů nějakou dobu trvat. COBOL programy jsou zobrazeny v Průzkumníku řešení s příponou CBL spolu s dat (CPY) a JCL.

4. Klikněte pravým tlačítkem myši **BankDemo2** projektu a vyberte **nastavit jako spouštěný projekt**.

    > [!NOTE]
    > Projekt BankDemo využívá HCOSS (hostitele možnost kompatibility pro SQL Server), který se nepoužívá pro tuto ukázku.

5. V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **BankDemo2** projektu a vyberte **sestavení**.

    > [!NOTE]
    > Sestavení na úrovni řešení za následek chyby, jako HCOSS není nakonfigurovaná.

6. Pokud je projekt vytvořen, podívejte se **výstup** okna. By měl vypadat jako na obrázku níže.

     ![Okno výstup zobrazuje úspěšné sestavení](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Nasazení aplikace BankDemo do oblasti databáze

1. Jako správce otevřete příkazový řádek Enterprise Developer (64 bitů).

2. Přejděte **veřejné %\\dokumenty\\Micro fokus\\pro podnikové vývojáře\\ukázky\\Mainframových\\CICS\\DotNet\\ BankDemo**.

3. Na příkazovém řádku spusťte **bankdemodbdeploy** a přidejte parametr pro databáze, kterou chcete nasadit, například:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Nezapomeňte použít lomítkem (/) není zpětné lomítko (\\). Tento skript se spustí nějakou dobu.

![Správa: Okno Enterprise Developer Command Prompt](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Vytvořit oblast BankDemo v správce podnikové sítě pro .NET

1. Otevřít **podnikový Server pro správu .NET** uživatelského rozhraní.

2. Spuštění modulu snap-in konzoly MMC z Windows **Start** nabídce zvolte **Micro fokus Enterprise Developer \> konfigurace \> podnikový Server pro správu .NET**. (Pro systém Windows Server, zvolte **Micro fokus Enterprise Developer \> podnikový Server pro správu .NET**).

3. Rozbalte **oblastech** kontejneru v levém podokně a kliknutím pravým tlačítkem **CICS**.

4. Vyberte **definovat oblasti** vytvořit novou oblast CICS volá **BANKDEMO**hostovaná v databázi (místní).

5. Zadat instanci databázového serveru, klikněte na tlačítko **Další**a pak zadejte název oblasti **BANKDEMO**.

     ![Definujte oblasti, dialogové okno](media/07-demo-cics.png)

6. Vyhledejte a vyberte soubor definice oblasti pro databázi mezi různými oblastmi, **oblasti\_bankdemo\_db.config** v **C:\\uživatelé\\veřejné\\ Dokumenty\\Micro fokus\\podnikový vývojář\\ukázky\\Mainframových\\CICS\\DotNet\\BankDemo**.

     ![Definujte oblasti – název oblasti: BANKDEMO](media/08-demo-cics.png)

7. Klikněte na **Dokončit**.

## <a name="create-xa-resource-definitions"></a>Vytvoření definic prostředků XA

1. V levém podokně **podnikový Server pro správu .NET** uživatelského rozhraní, rozbalte **systému**a potom **definice prostředků XA**. Toto nastavení definuje, jak se oblast spolupracuje s Enterprise serveru a databázím aplikace.

2. Klikněte pravým tlačítkem na **definice prostředků XA** a vyberte **přidání Instance serveru**.

3. V rozevíracím seznamu vyberte **instanci databáze služby**. Bude místní počítač SQLEXPRESS.

4. Vyberte instanci v rámci **XA definice prostředků (machinename\\sqlexpress)** kontejneru a klikněte na **přidat**.

5. Vyberte **definice prostředků XA databáze** a pak zadejte **BANKDEMO** pro **název** a **oblasti**.

     ![Nová obrazovka definice prostředků XA databáze](media/09-demo-xa.png)

6. Klikněte na symbol tří teček ( **...** ) a zobrazte si průvodce připojovací řetězec. Pro **název serveru**, typ **(místní)\\SQLEXPRESS**. Pro **přihlášení**vyberte **ověřování Windows**. Název databáze, zadejte **BANKDEMO**

     ![Připojovací řetězec obrazovka pro úpravy](media/10-demo-string.png)

7. Otestujte připojení.

## <a name="start-the-bankdemo-region"></a>Spustit BANKDEMO oblasti

> [!NOTE]
> Prvním krokem je důležité: Je nutné nastavit oblasti, kterou chcete použít definici prostředků XA jste právě vytvořili.

1. Přejděte na **BANDEMO CICS oblasti** pod **oblastech kontejneru**a pak vyberte **upravit oblast spouštěcí soubor** z **akce** podokno. Posuňte se dolů vlastnosti SQL a zadejte **bankdemo** pro **název prostředek XA**, nebo použijte na tři tečky a vyberte ji.

2. Klikněte na tlačítko **Uložit** ikonu uložte provedené změny.

3. Klikněte pravým tlačítkem na **BANKDEMO CICS oblasti** v **konzoly** podokně a vyberte **operací spustit/zastavit oblasti**.

4. V dolní části **operací spustit/zastavit oblasti** pole, které se zobrazí v prostředním podokně vyberte **Start**. Za několik sekund spustí se oblast.

     ![Pole SQL spustit/zastavit](media/11-demo-sql.png)

     ![Oblast CICS BANKDEMO – Začínáme obrazovky](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Vytvořit naslouchací proces

Vytvořte naslouchací proces pro TN3270 relací, které přístup k aplikaci BankDemo.

1. V levém podokně rozbalte **konfigurační editory** a vyberte **naslouchací proces**.

2. Klikněte na tlačítko **otevřít soubor** ikonu a vyberte **seelistener.exe.config** souboru. Tento soubor bude možné upravovat a načten při každém spuštění Enterprise serveru.

3. Všimněte si, že tyto dvě oblasti dříve definovaných (ESDEMO a JCLDEMO).

4. Chcete-li vytvořit novou oblast pro BANKDEMO, klikněte pravým tlačítkem na **oblastech**a vyberte **přidat oblast**.

5. Vyberte **BANKDEMO oblasti**.

6. Přidejte kanál TN3270 kliknutím pravým tlačítkem myši **BANKDEMO oblasti** a vyberete **kanálu přidat**.

7. Pro **název**, zadejte **TN3270**. Pro **Port**, zadejte **9024**. Aplikace ESDEMO používá port 9230, takže budete muset použít jiný port.

8. Pokud chcete soubor uložit, klikněte na tlačítko **Uložit** ikonu nebo zvolte **souboru** \> **Uložit**.

9. Chcete-li spustit naslouchací proces, klikněte na tlačítko **spustit naslouchací proces** ikonu nebo zvolte **možnosti** \> **spustit naslouchací proces**.

     ![Editor konfigurace naslouchacího procesu windows](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurace programu pro přístup k aplikaci BankDemo Rumba

Poslední věcí, kterou musíte udělat, je nakonfigurovat 3270 relaci pomocí programu Rumba 3270 emulátoru. Tento krok umožňuje přístup k aplikaci BankDemo přes naslouchací proces, který jste vytvořili.

1. Z Windows **Start** nabídce spuštění programu Rumba Desktop.

2. V části **připojení** položku nabídky, vyberte **TN3270**.

3. Klikněte na tlačítko **vložit** a typ **127.0.0.1** pro IP adresu a **9024** pro uživatelem definovaný port.

4. V dolní části dialogového okna, klikněte na tlačítko **připojit**. Černé CICS obrazovka se zobrazí.

5. Typ **bank** k zobrazení úvodní obrazovka 3270 BankDemo aplikace.

6. ID uživatele, zadejte **B0001** a heslo, zadejte cokoli. Na první obrazovce BANK20 otevře.

![Zobrazení úvodní obrazovka mainframových](media/14-demo.png)
![obrazovky zobrazení Mainframových - programu Rumba - subsystému ukázku](media/15-demo.png)

Blahopřejeme! Je teď spuštěná CICS aplikace v Azure pomocí Micro fokus Enterprise Server.

## <a name="next-steps"></a>Další postup

- [Spuštění serveru organizace v kontejnerech Docker v Azure](run-enterprise-server-container.md)
- [Migrace mainframů – portál](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Odstraňování potíží](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Uvedení mainframových migrace do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
