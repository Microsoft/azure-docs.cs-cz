---
title: Spouštět služba SSIS (SQL Server Integration Services) (SSIS) balíčky s dtexec nástrojem povoleným pro Azure | Microsoft Docs
description: Naučte se spouštět balíčky služba SSIS (SQL Server Integration Services) (SSIS) s dtexec nástrojem povoleným pro Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 740e53728356755bcc42e1e0aafb64992b30e113
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249019"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>Spouštění balíčků služba SSIS (SQL Server Integration Services) (SSIS) pomocí nástroje DTExec s povoleným Azure
Tento článek popisuje nástroj příkazového řádku **dtexec** (**AzureDTExec**) s podporou Azure.  Používá se ke spouštění balíčků SSIS na Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF).

Tradiční nástroj **dtexec** se dodává s SQL Server, další informace najdete v dokumentaci k [nástroji dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) .  Často ji vyvolají Orchestrace a plánovače třetích stran, jako je například aktivní dávka, ovládání-M atd., aby bylo možné spouštět balíčky SSIS místně.  Moderní nástroj **AzureDTExec** se dodává s nástrojem SQL Server Management Studio (SSMS).  Můžou je taky vyvolat Orchestrace a plánovače třetích stran pro spouštění balíčků SSIS v Azure.  Usnadňuje zdvihání & přesunu a migraci balíčků SSIS do cloudu.  Pokud po migraci chcete dál používat orchestraci a plánovače třetích stran v každodenních operacích, teď můžou místo **dtexec**volat **AzureDTExec** .

**AzureDTExec** spustí vaše balíčky jako aktivity balíčku SSIS v kanálech ADF. Další informace najdete v článku [spuštění balíčků SSIS jako aktivity ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) .  Dá se nakonfigurovat přes SSMS, aby se používala aplikace Azure Active Directory (AAD), která v ADF generuje kanály.  Dá se taky nakonfigurovat tak, aby měl přístup k systémům souborů/sdíleným složkám nebo k souborům Azure, do kterých ukládáte vaše balíčky.  V závislosti na hodnotách, které udělíte pro své možnosti volání, **AzureDTExec** vygeneruje a spustí jedinečný kanál ADF s aktivitou balíčku Execute SSIS.  Vyvoláním **AzureDTExec** se stejnými hodnotami pro jeho možnosti se znovu spustí existující kanál.

## <a name="prerequisites"></a>Požadované součásti
Pokud chcete používat **AzureDTExec**, Stáhněte si a nainstalujte nejnovější verzi SSMS (verze 18,3 nebo novější [).](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)

## <a name="configure-azuredtexec-utility"></a>Konfigurace nástroje AzureDTExec
Instalace SSMS na místním počítači nainstaluje také **AzureDTExec**.  Pokud chcete nakonfigurovat jeho nastavení, spusťte SSMS s možností **Spustit jako správce** a vyberte položku s možnostmi na sebe **– > migrovat do Azure – > nakonfigurujte dtexec s podporou Azure**.

![Konfigurace nabídky dtexec s povoleným Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Tato akce otevře okno **AzureDTExecConfig** , které je třeba otevřít s oprávněními správce, aby bylo možné zapisovat do souboru **AzureDTExec. Settings** .  Pokud jste SSMS jako správce, zobrazí se okno Řízení uživatelských účtů (UAC), kde můžete zadat heslo správce, abyste mohli zvýšit vaše oprávnění.

![Konfigurace nastavení dtexec s povoleným Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

V okně **AzureDTExecConfig** můžete zadat nastavení konfigurace následujícím způsobem:

- **ApplicationId**: Zadejte jedinečný identifikátor aplikace AAD, kterou vytvoříte se správnými oprávněními pro generování kanálů v ADF. Další informace najdete v tématu [Vytvoření aplikace AAD a instančního objektu prostřednictvím Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) článku.

- **AuthenticationKey**: zadejte ověřovací klíč pro vaši aplikaci AAD.

- **TenantId**: Zadejte jedinečný identifikátor tenanta AAD, pod kterým je vytvořená vaše aplikace AAD.

- **SubscriptionId**: Zadejte jedinečný identifikátor předplatného Azure, ve kterém se vytvořil ADF.

- Skupina **prostředků: zadejte**název skupiny prostředků Azure, ve které se vytvořil ADF.

- **DataFactory**: zadejte název svého ADF, ve kterém se generují jedinečné kanály se spuštěnou aktivitou balíčku SSIS v nich založené na hodnotách možností, které jsou k dispozici při vyvolání **AzureDTExec**.

- **IRName**: zadejte název Azure-SSIS IR ve vašem ADF, na kterém se při vyvolání **AzureDTExec** spustí balíčky zadané v cestě UNC (Universal Naming Convention).

- **PackageAccessDomain**: zadejte přihlašovací údaje domény pro přístup k balíčkům v cestě UNC zadané při vyvolání **AzureDTExec**.

- **PackageAccessUserName**: zadejte přihlašovací údaje uživatelského jména pro přístup k balíčkům v cestě UNC zadané při vyvolání **AzureDTExec**.

- **PackageAccessPassword**: zadejte přihlašovací údaje hesla pro přístup k balíčkům v cestě UNC zadané při vyvolání **AzureDTExec**.

- **LogPath**: zadejte cestu UNC ke složce protokolu, do které se budou zapisovat soubory protokolu z spuštění balíčku na Azure-SSIS IR.

- **LogLevel**: do Azure-SSIS IR zadejte vybraný rozsah protokolování z **předdefinovaných @no__t**-2**Basic**/ @no__t**verbose**– možnosti**výkonu** pro spuštění balíčku.

- **LogAccessDomain**: zadejte přihlašovací údaje domény pro přístup do složky protokolu v cestě UNC při zápisu souborů protokolu, které jsou požadovány, když je zadán parametr **logPath** a možnost **LogLevel** není **null**.

- **LogAccessUserName**: zadejte přihlašovací údaje uživatelského jména pro přístup ke složce protokolů v cestě UNC při zápisu souborů protokolu, které jsou požadovány, když je zadán parametr **logPath** a možnost **LogLevel** není **null**.

- **LogAccessPassword**: zadejte přihlašovací údaje pro přístup k složce protokolů v cestě UNC při zápisu souborů protokolu, které jsou povinné při zadání **logPath** a **LogLevel** není **null**.

- **PipelineNameHashStrLen**: Zadejte délku řetězců hash, které mají být generovány z hodnot možností, které zadáte při volání **AzureDTExec**.  Řetězce se použijí k vytvoření jedinečných názvů pro kanály ADF, na kterých běží vaše balíčky na Azure-SSIS IR.  Obvykle je délka 32 znaků dostačující.

Pokud máte v úmyslu ukládat balíčky a soubory protokolů v místních systémech souborů/sdílených složkách souborů místně, měli byste se připojit k Azure-SSIS IR k virtuální síti připojené k vaší místní síti, aby mohla načíst vaše balíčky a zapisovat soubory protokolu. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) síti. Další informace najdete v článku.

Aby nedocházelo k zobrazování citlivých hodnot zapsaných v souboru **AzureDTExec. Settings** v prostém textu, zakódujeme je do řetězců kódování Base64.  Když vyvoláte **AzureDTExec**, všechny řetězce kódované ve formátu base64 budou dekódovat zpátky do jejich původních hodnot.  Soubor **AzureDTExec. Settings** můžete dále zabezpečit omezením účtů, které k němu mají přístup.

## <a name="invoke-azuredtexec-utility"></a>Vyvolat nástroj AzureDTExec
**AzureDTExec** můžete vyvolat na příkazovém řádku a zadat relevantní hodnoty pro konkrétní možnosti ve scénáři použití.

Nástroj se instaluje na `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`. Můžete přidat cestu k proměnné prostředí PATH, aby ji bylo možné vyvolat odkudkoli.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Vyvolání **AzureDTExec** nabízí podobné možnosti jako vyvolání **dtexec**, další informace najdete v dokumentaci k [nástroji dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) .  Tady jsou aktuálně podporované možnosti:

- **/F [soubor]** : načte balíček, který je uložený v systému souborů/sdílení souborů nebo souborech Azure.  Jako hodnotu této možnosti můžete zadat cestu UNC pro soubor balíčku v systému souborů/sdílení souborů/soubory Azure s jeho rozšířením dtsx.  Pokud zadaná cesta UNC obsahuje nějaké místo, musíte umístit uvozovky kolem celé cesty.

- **/Conf [igFile]** : Určuje konfigurační soubor, ze kterého mají být extrahovány hodnoty.  Pomocí této možnosti můžete nastavit konfiguraci za běhu pro balíček, který se liší od toho, který je zadaný v době návrhu.  Můžete uložit různá nastavení do konfiguračního souboru XML a pak je načíst před spuštěním balíčku.  Další informace najdete v článku [konfigurace balíčků SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) .  Jako hodnotu této možnosti můžete zadat cestu UNC pro konfigurační soubor v systému souborů/sdílení souborů/soubory Azure s jeho rozšířením dtsConfig.  Pokud zadaná cesta UNC obsahuje nějaké místo, musíte umístit uvozovky kolem celé cesty.

- **/Conn [ection]** : Určuje připojovací řetězce pro existující Správce připojení ve vašem balíčku.  Pomocí této možnosti můžete nastavit připojovací řetězce pro modul runtime pro existující Správce připojení v balíčku, které se liší od těch, které jsou určeny v době návrhu.  Jako hodnotu této možnosti můžete zadat následující: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.

- **/Set**: přepíše konfiguraci parametru, proměnné, vlastnosti, kontejneru, zprostředkovatele protokolů, enumerátoru foreach nebo připojení v balíčku.  Tuto možnost lze zadat vícekrát.  Jako hodnotu této možnosti můžete zadat následující: `property_path;value`, například `\package.variables[counter].Value;1` přepíše hodnotu `counter` proměnné jako 1.  Průvodce konfigurací balíčku můžete použít k vyhledání, zkopírování a vložení hodnoty `property_path` pro položky v balíčku, jejichž hodnota se má přepsat. Další informace najdete v dokumentaci [Průvodce konfigurací balíčku](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) .

- **/De [crypt]** : nastaví dešifrovací heslo balíčku, který je nakonfigurovaný s úrovní ochrany**EncryptSensitiveWithPassword** **EncryptAllWithPassword**/.

> [!NOTE]
> Vyvoláním **AzureDTExec** s novými hodnotami pro své možnosti se vygeneruje nový kanál s výjimkou možnosti **/de [pt]** .

## <a name="next-steps"></a>Další kroky

Po vygenerování a spuštění jedinečných kanálů s SSIS aktivitou balíčku v nich, které se spustí po vyvolání **AzureDTExec**, je můžete monitorovat na portálu ADF. Další informace najdete v článku [spuštění balíčků SSIS jako aktivit ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) .

> [!WARNING]
> Očekává se, že vygenerovaný kanál bude používat jenom **AzureDTExec**. Jeho vlastnosti/parametry se můžou v budoucnu změnit, takže byste je neměli upravovat ani znovu používat pro žádné jiné účely, které by mohly přerušit **AzureDTExec**. V případě, že k tomu dojde, můžete kanál vždycky odstranit a **AzureDTExec** při příštím volání vygeneruje nový kanál.
