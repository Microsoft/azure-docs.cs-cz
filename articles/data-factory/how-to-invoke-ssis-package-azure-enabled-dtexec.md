---
title: Spouštění balíčků služby SQL Server Integration Services (SSIS) pomocí nástroje Dtexec s podporou Azure
description: Zjistěte, jak spouštět balíčky služby SQL Server Integration Services (SSIS) pomocí nástroje Dtexec s podporou Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: dce7fb87ee49aefdedf5653243fa5729eee34519
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414327"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Spouštění balíčků služby SQL Server Integration Services pomocí nástroje Dtexec s podporou Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje nástroj příkazového řádku dtexec (AzureDTExec). Používá se ke spuštění balíčků sql server integration services (SSIS) na Azure-SSIS Integration Runtime (IR) v Azure Data Factory.

Tradiční nástroj dtexec je dodáván s SQL Server. Další informace naleznete v tématu [nástroj dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Často je vyvolána orchestrátory nebo plánovači třetích stran, jako je ActiveBatch a Control-M, ke spuštění balíčků SSIS místně. 

Moderní nástroj AzureDTExec je dodáván s nástrojem SQL Server Management Studio (SSMS). Může být také vyvolána orchestrátory nebo plánovači třetích stran ke spuštění balíčků SSIS v Azure. Usnadňuje zvedání a přesouvání nebo migraci balíčků SSIS do cloudu. Po migraci, pokud chcete nadále používat orchestrátory třetích stran nebo plánovače ve vašich každodenních operacích, mohou nyní vyvolat AzureDTExec namísto dtexec.

AzureDTExec spouští vaše balíčky jako Spouštět aktivity balíčku SSIS v kanálech Data Factory. Další informace najdete [v tématu Spuštění balíčků SSIS jako aktivity Azure Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

AzureDTExec lze nakonfigurovat prostřednictvím SSMS používat Azure Active Directory (Azure AD) aplikace, která generuje kanály ve vaší datové továrně. Můžete také nakonfigurovat pro přístup k systémům souborů, sdílených složek nebo soubory Azure, kde ukládáte balíčky. Na základě hodnot, které udáváte pro své možnosti vyvolání, AzureDTExec generuje a spouští jedinečný kanál Data Factory s aktivitou spouštění balíčku SSIS. Vyvolání AzureDTExec se stejnými hodnotami pro jeho možnosti reruns existující kanálu.

## <a name="prerequisites"></a>Požadavky
Chcete-li používat AzureDTExec, stáhněte a nainstalujte nejnovější verzi SSMS, která je verze 18.3 nebo novější. Stáhněte si ji z [této webové stránky](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Konfigurace nástroje AzureDTExec
Instalace SSMS na místním počítači také nainstaluje AzureDTExec. Chcete-li konfigurovat jeho nastavení, spusťte SSMS s možností **Spustit jako správce.** Pak vyberte **Nástroje** > **migrovat do Azure** > **Konfigurace Azure dtexec .**

![Konfigurace nabídky Dtexec s podporou Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Tato akce otevře okno **AzureDTExecConfig,** které je třeba otevřít s oprávněními správce, aby mohl zapisovat do souboru *AzureDTExec.settings.* Pokud jste nespouštěli SSMS jako správce, otevře se okno Řízení uživatelských účtů (UAC). Zadejte heslo správce, abyste zvýšili svá oprávnění.

![Konfigurace nastavení dtexec s podporou Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

V okně **AzureDTExecConfig** zadejte nastavení konfigurace následujícím způsobem:

- **ApplicationId**: Zadejte jedinečný identifikátor aplikace Azure AD, kterou vytvoříte se správnými oprávněními ke generování kanálů ve vaší datové továrně. Další informace najdete [v tématu Vytvoření aplikace Azure AD a instančního objektu služby prostřednictvím portálu Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
- **AuthenticationKey**: Zadejte ověřovací klíč pro aplikaci Azure AD.
- **TenantId**: Zadejte jedinečný identifikátor klienta Azure AD, pod kterým se vytvoří vaše aplikace Azure AD.
- **DataFactory**: Zadejte název vaší datové továrny, ve kterém jsou generovány jedinečné kanály s aktivitou sexecute SSIS Package v nich na základě hodnot možností poskytnutých při vyvolání AzureDTExec.
- **IRName**: Zadejte název Azure-SSIS Ir ve vaší datové továrně, na kterém se spustí balíčky zadané v jejich cestě UNC (Universal Naming Convention) při vyvolání AzureDTExec.
- **PipelineNameHashStrLen**: Zadejte délku řetězce hash, které mají být generovány z hodnot možností, které zadáte při vyvolání AzureDTExec. Řetězce se používají k vytvoření jedinečných názvů pro kanály data factory, které spouštějí balíčky na Azure-SSIS IR. Obvykle je dostatečná délka 32 znaků.
- **ResourceGroup:** Zadejte název skupiny prostředků Azure, ve které byla vytvořena vaše továrna na data.
- **SubscriptionId**: Zadejte jedinečný identifikátor předplatného Azure, pod kterým byla vytvořena vaše továrna na data.
- **LogAccessDomain**: Při zápisu souborů protokolu zadejte pověření domény pro přístup ke složce protokolu v cestě UNC, což je vyžadováno při zadání **logpath** a **loglevel** není **null**.
- **LogAccessPassword**: Zadejte pověření hesla pro přístup ke složce protokolu v cestě UNC při zápisu souborů protokolu, což je vyžadováno při **logpath** je zadán a **LogLevel** není **null**.
- **LogAccessUserName**: Zadejte pověření uživatelského jména pro přístup ke složce protokolu v cestě UNC při zápisu souborů protokolu, což je vyžadováno při zadání **LogPath** a **LogLevel** není **null**.
- **LogLevel**: Zadejte vybraný obor protokolování z předdefinované **null**, **Basic**, **Verbose**nebo **Performance** možnosti pro spuštění balíčku na Azure-SSIS Ir.
- **LogPath**: Zadejte cestu UNC složky protokolu, do které jsou zapsány soubory protokolu z provádění balíčků na Azure-SSIS IR.
- **PackageAccessDomain**: Zadejte pověření domény pro přístup k balíčkům v jejich cestě UNC, která je určena při vyvolání AzureDTExec.
- **PackageAccessPassword**: Zadejte pověření hesla pro přístup k balíčkům v jejich cestě UNC, která je zadána při vyvolání AzureDTExec.
- **PackageAccessUserName**: Zadejte přihlašovací údaje uživatelského jména pro přístup k balíčkům v cestě UNC, která je určena při vyvolání AzureDTExec.

Chcete-li ukládat balíčky a soubory protokolu v souborových systémech nebo sdílených sporeh souborů v místním prostředí, připojte zařízení Azure-SSIS IR k virtuální síti připojené k místní síti, aby mohla načítat balíčky a zapisovat soubory protokolu. Další informace najdete [v tématu Připojení azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Abyse zabránilo zobrazení citlivých hodnot zapsaných do souboru *AzureDTExec.settings* ve formátu prostého textu, zakódujeme je do řetězců kódování Base64. Při vyvolání AzureDTExec, všechny řetězce kódované Base64 jsou dekódovány zpět do jejich původní hodnoty. Soubor *AzureDTExec.settings* můžete dále zabezpečit omezením účtů, které k němu mají přístup.

## <a name="invoke-the-azuredtexec-utility"></a>Vyvolání nástroje AzureDTExec
AzureDTExec můžete vyvolat na příkazovém řádku řádku a poskytnout příslušné hodnoty pro konkrétní možnosti ve vašem scénáři použití případu.

Nástroj je nainstalován `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`na adrese . Můžete přidat jeho cestu do proměnné prostředí CESTA pro ji vyvolat odkudkoli.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Vyvolání AzureDTExec nabízí podobné možnosti jako vyvolání dtexec. Další informace naleznete v tématu [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Zde jsou aktuálně podporované možnosti:

- **/F[ile]**: Načte balíček, který je uložený v systému souborů, sdílené složce nebo soubory Azure. Jako hodnotu pro tuto možnost můžete určit cestu UNC pro soubor balíčku v systému souborů, sdílené složce nebo soubory Azure s příponou .dtsx. Pokud zadaná cesta UNC obsahuje mezeru, položte uvozovky kolem celé cesty.
- **/Conf[igFile]**: Určuje konfigurační soubor, ze kterýchcete extrahovat hodnoty. Pomocí této možnosti můžete nastavit konfiguraci za běhu pro váš balíček, která se liší od konfigurace zadané v době návrhu. V konfiguračním souboru XML můžete uložit různá nastavení a pak je načíst před spuštěním balíčku. Další informace naleznete v [tématu Konfigurace balíčků SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Chcete-li určit hodnotu pro tuto možnost, použijte cestu UNC pro konfigurační soubor v systému souborů, sdílené složce nebo soubory Azure s příponou dtsConfig. Pokud zadaná cesta UNC obsahuje mezeru, položte uvozovky kolem celé cesty.
- **/Conn[ection]**: Určuje připojovací řetězce pro existující správce připojení v balíčku. Pomocí této možnosti můžete nastavit připojovací řetězce za běhu pro stávající správce připojení v balíčku, které se liší od těch, které jsou zadány v době návrhu. Zadejte hodnotu pro tuto `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`volbu takto: .
- **/Set**: Přepíše konfiguraci parametru, proměnné, vlastnosti, kontejneru, zprostředkovatele protokolu, čítače výčtu Foreach nebo připojení v balíčku. Tuto možnost lze zadat vícekrát. Zadejte hodnotu pro tuto `property_path;value`volbu takto: . Například `\package.variables[counter].Value;1` přepíše hodnotu `counter` proměnné jako 1. Pomocí Průvodce **konfigurací balíčku** můžete vyhledat, zkopírovat `property_path` a vložit hodnotu položek v balíčku, jejichž hodnotu chcete přepsat. Další informace naleznete v [Průvodci konfigurací balíčku](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De[crypt]**: Nastaví heslo pro dešifrování balíčku, který je nakonfigurován s úrovní ochrany **EncryptAllWithPassword**/**EncryptSensitiveWithPassword.**

> [!NOTE]
> Vyvolání AzureDTExec s novými hodnotami pro jeho možnosti generuje nový kanál s výjimkou **možnosti /De[cript]**.

## <a name="next-steps"></a>Další kroky

Po jedinečné kanály s spustit balíček SSIS aktivity v nich jsou generovány a spustit při vyvolání AzureDTExec, mohou být sledovány na portálu Data Factory. Aktivační události data factory jim můžete také přiřadit, pokud je chcete organizovat nebo plánovat pomocí data factory. Další informace naleznete [v tématu Spuštění balíčků SSIS jako aktivit y data factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> Vygenerované kanálu se očekává, že bude použit pouze AzureDTExec. Jeho vlastnosti nebo parametry se mohou v budoucnu změnit, takže je neupravujte ani znovu nepoužívejte pro jiné účely. Změny může přerušit AzureDTExec. Pokud k tomu dojde, odstraňte kanál. AzureDTExec generuje nový kanál při příštím vyvolání.