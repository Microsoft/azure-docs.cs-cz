---
title: Azure Functions instalace modulu Runtime | Microsoft Docs
description: Postup instalace modulu Runtime funkce Azure preview 2
services: functions
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: f8ce27bf28f73818932f2ac9056d4fdd573679e8
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2017
ms.locfileid: "26290653"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instalace modulu Runtime funkce Azure preview 2

Pokud chcete nainstalovat Azure Functions Runtime preview 2, postupujte podle těchto kroků:

1. Zkontrolujte, že váš počítač úspěšně projde minimální požadavky.
1. Stažení [Azure Functions instalačního programu Preview Runtime](https://aka.ms/azafrv2).
1. Odinstalujte funkce Runtime Azure preview 1.
1. Instalace modulu Runtime funkce Azure preview 2.
1. Dokončete konfiguraci Azure Functions Runtime Preview 2.
1. Vytvoření první funkce ve verzi Preview Runtime funkce Azure

## <a name="prerequisites"></a>Požadavky

Než nainstalujete Azure Functions Runtime preview, musíte mít k dispozici následující prostředky:

1. Počítač se systémem Microsoft Windows Server 2016 nebo Microsoft Windows 10 Creators Update (Professional nebo Enterprise Edition).
1. Instance SQL serveru spuštěna v rámci vaší sítě.  Minimální verze vyžaduje je SQL Server Express.

## <a name="uninstall-previous-version"></a>Odinstalujte předchozí verze

Pokud jste dříve nainstalovali verzi preview Azure Functions Runtime, je nutné odinstalovat před instalací nejnovější verze.  Odinstalujte preview Azure Functions Runtime odebráním programu na panelu Přidat nebo odebrat programy v systému Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Nainstalujte Preview Runtime Azure Functions

Instalační program Azure funkce Runtime Preview vás provede instalaci preview Azure Functions Runtime správy a rolí pracovního procesu.  Je možné nainstalovat roli správy a pracovního procesu na stejném počítači.  Však jako přidáte další funkce aplikací, je nutné nasadit více rolí pracovního procesu na další počítače. abyste mohli škálovat funkcí do více pracovníků.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalace správy a roli pracovního procesu na stejném počítači

1. Spusťte instalační program Preview Runtime Azure Functions.

    ![Azure instalační program modulu Runtime funkce preview][1]

1. Klikněte na **Další**.
1. Jakmile budete mít přečtěte si podmínky **smlouvy EULA**, **zaškrtněte políčko** přijmout podmínky a klikněte na tlačítko **Další** posunut.
1. Vyberte role, kterou chcete nainstalovat na tomto počítači **Role správy funkce** nebo **Role pracovního procesu funkce** a klikněte na tlačítko **Další**.

    ![Azure Functions Runtime preview instalační - výběr role][3]

    > [!NOTE]
    > Můžete nainstalovat **Role pracovního procesu funkce** na mnoho dalších počítačů. Uděláte to tak, postupujte podle těchto pokynů a vybrat pouze **Role pracovního procesu funkce** v instalačním programu.

1. Klikněte na tlačítko **Další** tak, aby měl **Průvodce instalací modulu Runtime Azure funkce** spustit proces instalace v počítači.
1. Po dokončení Průvodce instalací spustí **Azure Functions Runtime** nástroje Konfigurace.

    ![Azure preview Functions Runtime instalační dokončení][6]

    > [!NOTE]
    > Pokud instalujete na **Windows 10** a **kontejneru** funkce není povolená dříve, **instalace modulu Runtime funkce Azure** vás vyzve, abyste po restartování počítače pro dokončení instalace.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurace modulu Runtime Azure Functions

K dokončení instalace Azure Functions Runtime, je třeba provést konfiguraci.

1. **Azure Functions Runtime** konfigurační nástroj ukazuje, jaké role jsou nainstalovány v počítači.

    ![Nástroj konfigurace Azure Runtime funkce preview][7]

1. Klikněte **databáze** zadejte podrobnosti připojení pro instanci serveru SQL, včetně určení [hlavní klíč databáze](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)a klikněte na tlačítko **použít**.  Připojení k instanci systému SQL Server je nutná pro Runtime Azure Functions k vytvoření databáze pro podporu modulu Runtime.

    ![Konfigurace databáze Azure Runtime funkce preview][8]

1. Klikněte **pověření** kartě.  Zde je nutné vytvořit dva nové přihlašovací údaje pro použití se sdílenou složkou pro hostování všechny funkce aplikace.  Zadejte **uživatelské jméno** a **heslo** kombinací pro **vlastníka sdílené složky souboru** a **uživatele sdílené složky souboru**, pak klikněte na tlačítko **Použít**.

    ![Přihlašovací údaje Azure preview Functions Runtime][9]

1. Klikněte **sdílené složky** kartě.  V tomto poli musíte zadat podrobnosti o umístění sdílené složky souborů.  Je možné vytvořit sdílené složky pro vás nebo můžete použít existující sdílení souborů a klikněte na tlačítko **použít**.  Pokud vyberete nové umístění sdílené složky, musíte zadat adresář pro použití Azure Functions Runtime.

    ![Azure Functions Runtime preview sdílené složky][10]

1. Klikněte **IIS** kartě.  Tato karta zobrazuje podrobnosti o webů ve službě IIS, která vytvoří nástroj Konfigurace Azure Functions Runtime.  Můžete určit vlastní název DNS pro portál ve verzi preview Azure Functions Runtime.  Klikněte na tlačítko **použít** k dokončení.

    ![Azure preview Runtime funkce služby IIS][11]

1. Klikněte **služby** kartě.  Tato karta zobrazuje stav služeb v vaše konfigurace nástroje Azure Functions Runtime.  Pokud **služba Aktivace hostitele funkce Azure** je neběží po počáteční konfiguraci, klikněte na tlačítko **spustit službu**.

    ![Azure Functions Runtime preview dokončení konfigurace][12]

1. Vyhledejte **Azure Functions Runtime portál** jako `https://<machinename>.<domain>/`.

    ![Portál Azure preview Functions Runtime][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Vytvoření první funkce ve verzi preview Azure Functions Runtime

Chcete-li vytvořit svoji první funkci ve verzi preview Azure Functions Runtime

1. Vyhledejte **Azure Functions Runtime portál** jako https://<machinename>.<domain> například https://mycomputer.mydomain.com
1. Zobrazí se výzva k **přihlásit**, pokud se nasadí domény používá účet domény uživatelské jméno a heslo, jinak hodnota používat místní účet uživatelské jméno a heslo k přihlášení na portál.

![Modul Runtime funkce preview portálu přihlášení k Azure][14]

1. Vytvářet aplikace pro funkce, je nutné vytvořit odběr.  V levém horním rohu portálu, klikněte na  **+**  možnost vedle odběrů

![Předplatná Azure, která Functions Runtime preview portálu][15]

1. Zvolte **DefaultPlan**, zadejte název pro vaše předplatné a klikněte na **vytvořit**.

![Azure Functions Runtime preview portálu předplatné plánu a název][16]

1. V levém podokně portálu jsou uvedeny všechny funkce aplikací.  Chcete-li vytvořit novou aplikaci funkce, vyberte záhlaví **funkce aplikace** a klikněte na tlačítko  **+**  možnost.

1. Zadejte název pro vaši aplikaci funkce, vyberte správné předplatné, vyberte, kterou verzi modulu runtime Azure Functions chcete programu proti a klikněte na tlačítko **vytvořit**

![Modul Runtime funkce preview portálu nové funkce aplikace Azure][17]

1. Nové funkce aplikace je uvedena v levém podokně portálu.  Vyberte funkce a pak klikněte na tlačítko **novou funkci** v horní části podokna center na portálu.

![Šablony Azure preview Functions Runtime][18]

1. Vyberte funkci aktivaci časovačem, v pravém plovoucím panelem název funkce a změňte plán pro `*/5 * * * * *` (Tento výraz cron způsobí, že vaše časovače funkce Spustit každých pět sekund) a klikněte na tlačítko **vytvořit**

![Modul Runtime funkce preview nové časovače funkce Konfigurace Azure][19]

1. Funkce byla vytvořena.  Můžete zobrazit v protokolu spuštění vaší aplikace funkce rozšířením **protokolu** panelu v dolní části portálu.

![Azure provádění funkce preview Functions Runtime][20]

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
