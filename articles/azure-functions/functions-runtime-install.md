---
title: Instalace Modul runtime služby Azure Functions
description: Jak nainstalovat Modul runtime služby Azure Functions Preview 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 73b9533fa00c783e1cfb85270198f5c00c66afd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86540379"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Nainstalovat Modul runtime služby Azure Functions Preview 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Chcete-li nainstalovat Modul runtime služby Azure Functions verze Preview 2, postupujte takto:

1. Ujistěte se, že váš počítač splňuje minimální požadavky.
1. Stáhněte si [instalační program služby modul runtime služby Azure Functions Preview](https://aka.ms/azafrv2).
1. Odinstalujte Modul runtime služby Azure Functions Preview 1.
1. Nainstalujte Modul runtime služby Azure Functions Preview 2.
1. Dokončete konfiguraci Modul runtime služby Azure Functions verze Preview 2.
1. Vytvoření první funkce v Modul runtime služby Azure Functions Preview

## <a name="prerequisites"></a>Požadavky

Než nainstalujete Modul runtime služby Azure Functions Preview, musíte mít k dispozici následující prostředky:

1. Počítač se systémem Microsoft Windows Server 2016 nebo Microsoft Windows 10 Creators Update (Professional nebo Enterprise Edition).
1. Instance SQL Server spuštěná v rámci vaší sítě.  Minimální požadovaná edice je SQL Server Express.

## <a name="uninstall-previous-version"></a>Odinstalace předchozí verze

Pokud jste již dříve nainstalovali Modul runtime služby Azure Functions Preview, je nutné před instalací nejnovější verze odinstalovat.  Odinstalujte Modul runtime služby Azure Functions Preview odebráním programu v okně Přidat nebo odebrat programy v systému Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Nainstalovat Modul runtime služby Azure Functions Preview

Instalační program služby Modul runtime služby Azure Functions Preview vás provede instalací portálu Modul runtime služby Azure Functions Preview a rolí pracovních procesů.  Je možné nainstalovat roli Správa a pracovní proces do stejného počítače.  Při přidávání dalších aplikací funkcí ale musíte nasadit další role pracovního procesu na další počítače, abyste mohli škálovat vaše funkce na více pracovních procesů.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalace role správy a pracovního procesu do stejného počítače

1. Spusťte instalační program služby Modul runtime služby Azure Functions Preview.

    ![Instalační program služby Modul runtime služby Azure Functions Preview][1]

1. Klikněte na **Next** (Další).
1. Po přečtení podmínek **smlouvy EULA** **zaškrtněte políčko** , abyste mohli podmínky přijmout, a kliknutím na Další přejděte k **dalšímu kroku** .
1. Vyberte role, které chcete nainstalovat, do role **správce Functions Worker Functions** nebo **Functions Worker** functions a klikněte na **Další**.

    ![Instalační program pro náhled Modul runtime služby Azure Functions – výběr role][3]

    > [!NOTE]
    > **Roli pracovního procesu Functions** můžete nainstalovat na mnoho dalších počítačů. Pokud to chcete provést, postupujte podle těchto pokynů a v instalačním programu vyberte pouze **pracovní roli Worker Functions** .

1. Kliknutím na tlačítko **Další** zobrazíte **modul runtime služby Azure Functions Průvodce instalací** na vašem počítači instalační proces.
1. Po dokončení Průvodce instalací spustí nástroj pro konfiguraci **modul runtime služby Azure Functions** .

    ![Instalační program služby Modul runtime služby Azure Functions Preview byl dokončen.][6]

    > [!NOTE]
    > Pokud instalujete v **systému Windows 10** a funkce **kontejneru** nebyla dříve povolena, **instalační program modul runtime služby Azure Functions** vyzve k restartování počítače, aby bylo možné instalaci dokončit.

## <a name="configure-the-azure-functions-runtime"></a>Nakonfigurovat Modul runtime služby Azure Functions

Chcete-li dokončit instalaci Modul runtime služby Azure Functions, je nutné dokončit konfiguraci.

1. Nástroj pro konfiguraci **modul runtime služby Azure Functions** ukazuje, které role jsou nainstalovány na vašem počítači.

    ![Nástroj pro konfiguraci Modul runtime služby Azure Functions Preview][7]

1. Klikněte na kartu **databáze** , zadejte podrobnosti o připojení pro vaši instanci SQL Server, včetně zadání [hlavního klíče databáze](/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), a klikněte na **použít**.  Aby se Modul runtime služby Azure Functions vytvořila databáze pro podporu modulu runtime, je nutné připojení k instanci SQL Server.

    ![Konfigurace databáze Modul runtime služby Azure Functions Preview][8]

1. Klikněte na kartu **přihlašovací údaje** .  Tady musíte vytvořit dvě nové přihlašovací údaje, které se mají použít se sdílenou složkou pro hostování všech vašich aplikací Function App.  Zadejte kombinace **uživatelského jména** a **hesla** pro **vlastníka sdílené složky** a pro **uživatele sdílené složky** a potom klikněte na **použít**.

    ![Přihlašovací údaje pro Modul runtime služby Azure Functions Preview][9]

1. Klikněte na kartu **sdílení souborů** .  Tady je nutné zadat podrobnosti umístění sdílené složky.  Sdílenou složku můžete vytvořit za vás, nebo můžete použít existující sdílenou složku a kliknout na **použít**.  Pokud vyberete nové umístění sdílené složky, musíte určit adresář, který má Modul runtime služby Azure Functions použít.

    ![Modul runtime služby Azure Functions ve verzi Preview sdílení souborů][10]

1. Klikněte na kartu **IIS** .  Tato karta zobrazuje podrobnosti o webech ve službě IIS, které vytváří nástroj pro konfiguraci Modul runtime služby Azure Functions.  Tady můžete zadat vlastní název DNS pro portál Modul runtime služby Azure Functions Preview.  Klikněte na tlačítko **použít** k dokončení.

    ![Služba IIS pro Modul runtime služby Azure Functions Preview][11]

1. Klikněte na kartu **služby** .  Tato karta zobrazuje stav služeb v nástroji pro konfiguraci Modul runtime služby Azure Functions.  Pokud po počáteční konfiguraci neběží  **Aktivační služba hostitele Azure Functions** , klikněte na **Spustit službu**.

    ![Dokončení konfigurace Modul runtime služby Azure Functions Preview][12]

1. Přejděte na **portál modul runtime služby Azure Functions** jako `https://<machinename>.<domain>/` .

    ![Portál Preview Modul runtime služby Azure Functions][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Vytvoření první funkce v Modul runtime služby Azure Functions Preview

Vytvoření první funkce v Modul runtime služby Azure Functions Preview

1. Přejděte na **portál modul runtime služby Azure Functions** , například `https://<machinename>.<domain>` `https://mycomputer.mydomain.com` .

1. Zobrazí se výzva k **přihlášení**, pokud je nasazená v doméně, použijte uživatelské jméno a heslo účtu domény, jinak se přihlaste k portálu pomocí uživatelského jména a hesla místního účtu.

    ![Přihlášení k portálu Modul runtime služby Azure Functions Preview][14]

1. Pokud chcete vytvářet aplikace Function App, musíte si vytvořit předplatné.  V levém horním rohu portálu klikněte na **+** možnost vedle předplatných.

    ![Předplatná portálu Modul runtime služby Azure Functions Preview][15]

1. Vyberte **DefaultPlan**, zadejte název vašeho předplatného a klikněte na **vytvořit**.

    ![Plán a název předplatného portálu Modul runtime služby Azure Functions Preview][16]

1. Všechny vaše aplikace Function App jsou uvedené v levém podokně portálu.  Novou Function App vytvoříte tak, že vyberete nadpis **aplikace funkce** a kliknete na **+** možnost.

1. Zadejte název aplikace Function App, vyberte správné předplatné, zvolte verzi modulu runtime Azure Functions, se kterou chcete programovat, a klikněte na **vytvořit** .

    ![Nová aplikace Function App na portálu Modul runtime služby Azure Functions Preview][17]

1. Vaše nová aplikace Function App je uvedená v levém podokně portálu.  Vyberte funkce a potom v horní části prostředního podokna na portálu klikněte na **Nová funkce** .

    ![Šablony pro náhled Modul runtime služby Azure Functions][18]

1. Vyberte funkci Trigger časovače v pravém informačním rámečku funkce a změňte plán na `*/5 * * * * *` (Tento výraz cron způsobí, že se funkce časovače spustí každých pět sekund) a klikněte na **vytvořit** .

    ![Konfigurace nové funkce časovače Modul runtime služby Azure Functions Preview][19]

1. Vaše funkce se teď vytvořila.  Protokol spuštění aplikace Function App můžete zobrazit tak, že rozbalíte podokno **protokolu** v dolní části portálu.

    ![Provádění funkce ve verzi Preview Modul runtime služby Azure Functions][20]

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
