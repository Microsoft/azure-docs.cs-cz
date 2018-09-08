---
title: Azure Functions instalace modulu Runtime | Dokumentace Microsoftu
description: Postup instalace modulu Runtime služby Azure Functions ve verzi preview 2
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 1ad1d2c74be97afcb62f3f8e8161111f4938f645
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093664"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Nainstalovat modul Runtime služby Azure Functions ve verzi preview 2

Pokud chcete nainstalovat modul Runtime služby Azure Functions ve verzi preview 2, postupujte podle těchto kroků:

1. Ujistěte se, že váš počítač úspěšně projde minimální požadavky.
1. Stáhněte si [Azure Functions instalační program modulu Runtime ve verzi Preview](https://aka.ms/azafrv2).
1. Odinstalujte modul Runtime služby Azure Functions ve verzi preview 1.
1. Nainstalujte modul Runtime služby Azure Functions ve verzi preview 2.
1. Dokončete konfiguraci modulu Runtime služby Azure Functions ve verzi preview 2.
1. Vytvoření první funkce Azure Functions Runtime – Předběžná verze

## <a name="prerequisites"></a>Požadavky

Než nainstalujete modul Runtime služby Azure Functions ve verzi preview, musíte mít k dispozici následující prostředky:

1. Počítače se systémem Microsoft Windows Server 2016 nebo Microsoft Windows 10 Creators Update (Professional nebo Enterprise Edition).
1. Instance SQL serveru v rámci vaší sítě.  Minimální požadovaná edice je SQL Server Express.

## <a name="uninstall-previous-version"></a>Odinstalovat předchozí verze

Pokud jste dříve nainstalovali modul Runtime služby Azure Functions ve verzi preview, je nutné odinstalovat před instalací nejnovější verze.  Odinstalujte modul Runtime služby Azure Functions ve verzi preview tak, že odeberete program v Windows přidat nebo odebrat programy.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalace Preview modulu Runtime Azure Functions

Instalační služby Azure Functions modulu Runtime ve verzi Preview vám pomůže nainstalovat modul Runtime služby Azure Functions ve verzi preview Management a role pracovního procesu.  Je možné nainstalovat roli správy a pracovní proces ve stejném počítači.  Jak budete přidávat další aplikace function App, však musíte nasadit více rolemi pracovních procesů u dalších počítačů bude škálovat vaše funkce do několika pracovních procesů.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Nainstalujte Management a Role pracovního procesu ve stejném počítači

1. Spusťte instalační program ve verzi Preview modulu Runtime Azure Functions.

    ![Azure ve verzi preview instalační modul Runtime služby Functions][1]

1. Klikněte na **Další**.
1. Jakmile jste si přečetli podmínky **smlouva EULA**, **zaškrtněte políčko** přijmout podmínky a klikněte na tlačítko **Další** k přechodu.
1. Vyberte role, kterou chcete nainstalovat na tomto počítači **rolí správy funkce** a/nebo **Role pracovního procesu funkce** a klikněte na tlačítko **Další**.

    ![Azure modul Runtime služby Functions ve verzi preview instalačním programem – výběr role][3]

    > [!NOTE]
    > Můžete nainstalovat **Role pracovního procesu funkce** na mnoho dalších počítačů. Uděláte to tak, postupujte podle těchto pokynů a vybrat jenom **Role pracovního procesu funkce** v instalačním programu.

1. Klikněte na tlačítko **Další** mít **Průvodce instalací modulu Runtime Azure Functions** zahájení procesu instalace na svém počítači.
1. Jakmile budete hotovi, spustí se Průvodce instalací **modul Runtime služby Azure Functions** konfigurační nástroj.

    ![Dokončete instalační služby systému ve verzi preview Azure modul Runtime služby Functions][6]

    > [!NOTE]
    > Pokud chcete nainstalovat **Windows 10** a **kontejneru** funkce není povolená dříve, **instalace modulu Runtime Azure Functions** vás vyzve k restartování počítače pro dokončení instalace.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurace modulu Runtime Azure Functions

K dokončení instalace modulu Runtime služby Azure Functions, je nutné provést konfiguraci.

1. **Modul Runtime služby Azure Functions** konfigurační nástroj ukazuje, jaké role jsou nainstalovány na vašem počítači.

    ![Nástroj konfigurace Azure modul Runtime služby Functions ve verzi preview][7]

1. Klikněte na tlačítko **databáze** kartu, zadejte podrobnosti o připojení pro vaši instanci SQL serveru, včetně zadání [hlavní klíč databáze](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)a klikněte na tlačítko **použít**.  Připojení k instanci systému SQL Server je nutná pro modul Runtime Azure Functions k vytvoření databáze pro podporu modulu Runtime.

    ![Konfigurace databáze ve verzi preview Azure modul Runtime služby Functions][8]

1. Klikněte na tlačítko **pověření** kartu.  Tady je nutné vytvořit dva nové přihlašovací údaje pro použití s sdílené složky pro hostování všechny vaše aplikace function App.  Zadejte **uživatelské jméno** a **heslo** kombinace, abyste **vlastníka sdílené složky** a **uživatele sdílené složky**, pak klikněte na tlačítko **Použít**.

    ![Přihlašovací údaje ve verzi preview Azure modul Runtime služby Functions][9]

1. Klikněte na tlačítko **sdílené** kartu.  Tady zadejte podrobnosti o umístění sdílené složky.  Sdílené složky můžete vytvořit za vás, nebo můžete použít existující sdílení souborů a klikněte na tlačítko **použít**.  Pokud vyberete nové sdílené umístění, je nutné zadat adresář používají modul Runtime služby Azure Functions.

    ![Modul Runtime služby Functions ve verzi preview sdílená složka Azure][10]

1. Klikněte na tlačítko **IIS** kartu.  Tato karta zobrazuje podrobnosti o weby ve službě IIS, která vytvoří nástroj pro konfiguraci modulu Runtime služby Azure Functions.  Můžete zadat vlastní název DNS pro modul Runtime služby Azure Functions ve verzi preview portálu.  Klikněte na tlačítko **použít** dokončete.

    ![Azure Functions Runtime – Předběžná verze služby IIS][11]

1. Klikněte na tlačítko **služby** kartu.  Tato karta zobrazuje stav služeb v nástroji pro konfiguraci modulu Runtime služby Azure Functions.  Pokud **služba Azure Functions hostitele aktivace** není spuštěno po počáteční konfiguraci, klikněte na tlačítko **spustit službu**.

    ![Azure ve verzi preview modul Runtime služby Functions konfigurace byla dokončena][12]

1. Přejděte **Azure Functions Runtime portál** jako `https://<machinename>.<domain>/`.

    ![Portál Azure preview modul Runtime služby Functions][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Vytvoření první funkce v modulu Runtime služby Azure Functions ve verzi preview

Chcete-li vytvořit svoji první funkci ve verzi preview modul Runtime služby Azure Functions

1. Přejděte **Azure Functions Runtime portál** jako https://<machinename>.<domain> Například https://mycomputer.mydomain.com
1. Zobrazí se výzva k **přihlášení**, pokud nasazení používá domény, uživatelské jméno účtu domény a heslo, jinak použít místní účet uživatelské jméno a heslo pro přihlášení k portálu.

![Modul Runtime služby Functions ve verzi preview portálu přihlášení k Azure][14]

1. Vytvoření aplikace function App, je nutné vytvořit předplatné.  V levém horním rohu portálu, klikněte **+** možnost vedle předplatná

![Azure modul Runtime služby Functions ve verzi preview portálu předplatných][15]

1. Zvolte **DefaultPlan**, zadejte název vašeho předplatného a klikněte na tlačítko **vytvořit**.

![Plán portálu předplatného Azure modul Runtime služby Functions ve verzi preview a název][16]

1. V levém podokně portálu jsou uvedeny všechny vaše aplikace function App.  Pokud chcete vytvořit novou aplikaci Function App, vyberte záhlaví **aplikace Function App** a klikněte na tlačítko **+** možnost.

1. Zadejte název pro vaši aplikaci function app, vyberte správné předplatné, která verze modulu runtime Azure Functions, které chcete programovat proti a klikněte na tlačítko **Create**

![Azure modul Runtime služby Functions ve verzi preview portálu novou aplikaci function app][17]

1. V levém podokně portálu je uvedena nová aplikace function app.  Vyberte funkce a pak klikněte na tlačítko **novou funkci** nahoře v prostředním podokně na portálu.

![Šablony Azure ve verzi preview modul Runtime služby Functions][18]

1. Vyberte funkci Trigger časovače, v pravém informační rámeček pojmenujte svoji funkci a změnit plán `*/5 * * * * *` (Tento výraz cron způsobí, že vaše funkce časovače pro spuštění každých pět sekund) a klikněte na tlačítko **Create**

![Modul Runtime služby Functions ve verzi preview nového časovače funkce Konfigurace Azure][19]

1. Byla vytvořena vaše funkce.  Prováděcí protokol aplikace Function App můžete zobrazit rozbalením **protokolu** panelu v dolní části portálu.

![Azure provádí funkce ve verzi preview modul Runtime služby Functions][20]

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
