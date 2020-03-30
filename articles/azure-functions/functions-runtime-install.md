---
title: Instalace runtime funkce Azure
description: Jak nainstalovat Azure Functions Runtime preview 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 7ad748aa9a5b45af10121648a668344548484cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226726"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instalace azure functions runtime preview 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Pokud chcete nainstalovat Azure Functions Runtime Preview 2, postupujte takto:

1. Ujistěte se, že váš stroj splňuje minimální požadavky.
1. Stáhněte si [instalační službu Azure Functions Runtime Preview .](https://aka.ms/azafrv2)
1. Odinstalujte náhled runtime Azure Functions 1.
1. Nainstalujte Azure Functions Runtime preview 2.
1. Dokončete konfiguraci Azure Functions Runtime Preview 2.
1. Vytvoření první funkce ve verzi Azure Functions Runtime Preview

## <a name="prerequisites"></a>Požadavky

Před instalací náhledu runtime Funkce Azure musíte mít k dispozici následující prostředky:

1. Počítač se systémem Microsoft Windows Server 2016 nebo Microsoft Windows 10 Creators Update (Professional nebo Enterprise Edition).
1. Instance serveru SQL Server spuštěná v síti.  Minimální požadovaná edice je SQL Server Express.

## <a name="uninstall-previous-version"></a>Odinstalace předchozí verze

Pokud jste dříve nainstalovali náhled Runtime funkce Azure, musíte odinstalovat před instalací nejnovější verze.  Odinstalujte náhled runtime Azure Functions tak, že odeberete program v části Přidat nebo odebrat programy v systému Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalace náhledu runtime funkcí Azure

Instalační služba Azure Functions Runtime Preview vás provede instalací rolí Azure Functions Runtime preview Management a Worker.  Je možné nainstalovat roli Management a Worker na stejném počítači.  Při přidávání dalších aplikací funkcí je však nutné nasadit více rolí pracovních sil na dalších počítačích, abyste mohli škálovat funkce na více pracovníků.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalace role správy a pracovního procesu do stejného počítače

1. Spusťte instalační program Náhled runtime Azure Functions.

    ![Instalační služba preview runtime Azure Functions][1]

1. Klikněte na **Další**.
1. Jakmile si přečtete podmínky **eula**, **zaškrtněte políčko** pro přijetí podmínek a klikněte na **tlačítko Další** předem.
1. Vyberte role, které chcete nainstalovat do tohoto počítače **Role správy funkcí** nebo role **pracovního procesu funkce** a klepněte na tlačítko **Další**.

    ![Instalační služba náhledu Azure Functions Runtime – výběr rolí][3]

    > [!NOTE]
    > **Roli pracovního procesu funkce** můžete nainstalovat do mnoha dalších počítačů. Postupujte podle těchto pokynů a v instalačním programu vyberte pouze **role pracovního procesu funkce.**

1. Chcete-li, aby **Průvodce instalací runtime Azure pro** spuštění procesu instalace v počítači. **Next**
1. Po dokončení průvodce nastavením spustí nástroj konfigurace **Azure Functions Runtime.**

    ![Instalační služba náhledu runtime Azure Functions byla dokončena][6]

    > [!NOTE]
    > Pokud instalujete do **Windows 10** a funkce **Kontejner** nebyla dříve povolena, **instalační program prostředí Azure Functions Runtime** vás vyzve k restartování počítače k dokončení instalace.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurace běhu funkcí Azure

Chcete-li dokončit instalaci azure functions runtime, musíte dokončit konfiguraci.

1. Nástroj konfigurace **Azure Functions Runtime** ukazuje, které role jsou nainstalované ve vašem počítači.

    ![Nástroj pro konfiguraci náhledu runtime Azure Functions][7]

1. Klepněte na kartu **Databáze,** zadejte podrobnosti o připojení pro instanci serveru SQL Server, včetně zadání [hlavního klíče databáze](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), a klepněte na **tlačítko Použít**.  Připojení k instanci SQL Serveru je nutné, aby modul Azure Functions Runtime vytvořil databázi pro podporu modulu Runtime.

    ![Konfigurace databáze Azure Functions Runtime Preview][8]

1. Klikněte na kartu **Přihlašovací údaje.**  Zde musíte vytvořit dvě nová pověření pro použití se sdílenou složkou pro hostování všech aplikací funkcí.  Zadejte kombinace **uživatelského jména** a **hesla** pro **vlastníka sdílené složky** a pro **uživatele sdílené složky**a klepněte na tlačítko **Použít**.

    ![Přihlašovací údaje náhledu runtime Azure Functions][9]

1. Klikněte na kartu **Sdílení souborů.**  Zde je nutné zadat podrobnosti umístění sdílené složky.  Sdílenou složku lze vytvořit za vás nebo můžete použít existující sdílenou složku a klepnout na tlačítko **Použít**.  Pokud vyberete nové umístění sdílení souborů, musíte zadat adresář pro použití v Azure Functions Runtime.

    ![Azure Functions Runtime preview sdílení souborů][10]

1. Klikněte na kartu **IIS.**  Tato karta zobrazuje podrobnosti o webech ve službě IIS, které vytvoří konfigurační nástroj Azure Functions Runtime.  Zde můžete zadat vlastní název DNS pro portál náhledu Azure Functions Runtime.  Chcete-li provést, klepněte na tlačítko **Použít.**

    ![Azure Functions Runtime preview IIS][11]

1. Klikněte na kartu **Služby.**  Tato karta zobrazuje stav služeb v konfiguračním nástroji Azure Functions Runtime.  Pokud **služba aktivace hostitele funkcí Azure** není po počáteční konfiguraci spuštěna, klikněte na tlačítko Spustit **službu**.

    ![Konfigurace náhledu runtime Azure Functions byla dokončena][12]

1. Přejděte na **portál Runtime Azure Functions jako** `https://<machinename>.<domain>/`.

    ![Portál preview runtime azure functions][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Vytvoření první funkce ve verzi Azure Functions Runtime preview

Vytvoření první funkce ve verzi Preview runtime Azure Functions

1. Přejděte na **portál Runtime Azure Functions,** jako `https://<machinename>.<domain>` je například `https://mycomputer.mydomain.com`.

1. Pokud jste nasazeni **v**doméně, budete vyzváni k přihlášení pomocí uživatelského jména a hesla účtu domény, jinak se k portálu přihlaste pomocí uživatelského jména a hesla místního účtu.

    ![Přihlášení k portálu Azure Functions Runtime Preview][14]

1. Chcete-li vytvořit aplikace funkcí, musíte vytvořit předplatné.  V levém horním rohu portálu klikněte **+** na možnost vedle předplatných.

    ![Předplatná portálu Azure Functions Runtime preview][15]

1. Zvolte **DefaultPlan**, zadejte název předplatného a klepněte na **vytvořit**.

    ![Plán a název předplatného portálu Azure Functions Runtime preview][16]

1. Všechny aplikace funkcí jsou uvedeny v levém podokně portálu.  Chcete-li vytvořit novou aplikaci funkce, vyberte záhlaví **Aplikace funkcí** a klepněte na **+** tuto možnost.

1. Zadejte název aplikace funkce, vyberte správné předplatné, zvolte, kterou verzi runtime Azure Functions chcete naprogramovat, a klikněte na **Vytvořit.**

    ![Nová aplikace pro nové funkce portálu Azure Functions Runtime Preview][17]

1. Nová aplikace funkcí je uvedena v levém podokně portálu.  Vyberte Funkce a v horní části prostředním podokně portálu klikněte na **Nová funkce.**

    ![Šablony náhledu Runtime Azure Functions][18]

1. Vyberte funkci Časovač aktivační událost, v pravém informačním rámečku `*/5 * * * * *` název funkce a změňte plán na (tento cron výraz způsobí, že vaše funkce časovače spustit každých pět sekund), a klepněte na tlačítko **Vytvořit**

    ![Azure Functions Runtime náhled nové konfigurace funkce časovače][19]

1. Vaše funkce byla nyní vytvořena.  Protokol spuštění aplikace Function můžete zobrazit rozbalením podokna **protokolu** v dolní části portálu.

    ![Spuštění funkce Runtime Azure Functions][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
