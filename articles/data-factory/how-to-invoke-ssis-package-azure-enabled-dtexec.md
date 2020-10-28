---
title: Spouštění balíčků služba SSIS (SQL Server Integration Services) (SSIS) pomocí nástroje DTExec s povoleným Azure
description: Naučte se spouštět balíčky služba SSIS (SQL Server Integration Services) (SSIS) s dtexec nástrojem povoleným pro Azure.
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
ms.openlocfilehash: c72a2916eeb0a200fe006651b60cbe1b53ef3015
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637764"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Spouštění balíčků služba SSIS (SQL Server Integration Services) pomocí nástroje DTExec s povoleným Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje nástroj příkazového řádku dtexec (AzureDTExec) s podporou Azure. Používá se ke spouštění balíčků služba SSIS (SQL Server Integration Services) (SSIS) na Azure-SSIS Integration Runtime (IR) v Azure Data Factory.

Tradiční nástroj dtexec se dodává s SQL Server. Další informace najdete v tématu [dtexec Utility](/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Je často vyvolána orchestrací nebo plánovači třetích stran, jako jsou ActiveBatch a Control-M, ke spouštění balíčků SSIS v místním prostředí. 

Moderní nástroj AzureDTExec se dodává s nástrojem pro SQL Server Management Studio (SSMS). Můžou je taky vyvolat Orchestrace a plánovače třetích stran pro spouštění balíčků SSIS v Azure. Usnadňuje zdvihání a přesunování nebo migraci balíčků SSIS do cloudu. Pokud po migraci chcete dál používat orchestrace nebo plánovače třetích stran v každodenních operacích, teď můžou místo dtexec volat AzureDTExec.

AzureDTExec spustí vaše balíčky jako aktivity balíčku SSIS ve Data Factorych kanálech. Další informace najdete v tématu [spouštění balíčků SSIS jako aktivit Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md). 

AzureDTExec je možné nakonfigurovat prostřednictvím SSMS tak, aby používala aplikaci Azure Active Directory (Azure AD), která generuje kanály ve vaší datové továrně. Dá se taky nakonfigurovat tak, aby měl přístup k systémům souborů, sdíleným složkám nebo souborům Azure, do kterých ukládáte balíčky. V závislosti na hodnotách, které udělíte pro své možnosti volání, AzureDTExec vygeneruje a spustí jedinečný Data Factory kanál s aktivitou balíčku Execute SSIS. Vyvolání AzureDTExec se stejnými hodnotami pro příslušné možnosti znovu spustí existující kanál.

## <a name="prerequisites"></a>Předpoklady
Chcete-li použít AzureDTExec, Stáhněte a nainstalujte nejnovější verzi nástroje SSMS, která je verze 18,3 nebo novější. Stáhněte si ho z [tohoto webu](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Konfigurace nástroje AzureDTExec
Instalace SSMS na místním počítači nainstaluje taky AzureDTExec. Pokud chcete nakonfigurovat jeho nastavení, spusťte SSMS pomocí možnosti **Spustit jako správce** . Pak vyberte **nástroje**  >  **migrace do Azure**  >  **Konfigurace dtexec s povoleným Azure** .

![Konfigurace nabídky dtexec s povoleným Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Tato akce otevře okno **AzureDTExecConfig** , které je třeba otevřít s oprávněními správce, aby bylo možné zapisovat do souboru *AzureDTExec. Settings* . Pokud jste nespouštěli SSMS jako správce, otevře se okno Řízení uživatelských účtů (UAC). Pokud chcete zvýšit svoje oprávnění, zadejte heslo správce.

![Konfigurace nastavení dtexec s povoleným Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

V okně **AzureDTExecConfig** zadejte nastavení konfigurace následujícím způsobem:

- **ApplicationId** : Zadejte jedinečný identifikátor aplikace Azure AD, kterou vytvoříte se správnými oprávněními pro generování kanálů ve vaší datové továrně. Další informace najdete v tématu [Vytvoření aplikace a instančního objektu služby Azure AD prostřednictvím Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).
- **AuthenticationKey** : zadejte ověřovací klíč pro vaši aplikaci Azure AD.
- **TenantId** : Zadejte jedinečný identifikátor TENANTA Azure AD, na kterém je vytvořená vaše aplikace Azure AD.
- **DataFactory: zadejte** název objektu pro vytváření dat, ve kterém se generují jedinečné kanály se spuštěnou aktivitou balíčku SSIS v nich založené na hodnotách možností, které jsou k dispozici při vyvolání AzureDTExec.
- **IRName** : zadejte název Azure-SSIS IR ve vaší datové továrně, na kterém se při vyvolání AzureDTExec spustí balíčky zadané v cestě UNC (Universal Naming Convention).
- **PipelineNameHashStrLen** : Zadejte délku řetězců hash, které mají být generovány z hodnot možností, které poskytnete při volání AzureDTExec. Řetězce slouží k vytvoření jedinečných názvů Data Factorych kanálů, které spouštějí balíčky na Azure-SSIS IR. Obvykle je délka 32 znaků dostačující.
- Skupina **prostředků: zadejte** název skupiny prostředků Azure, ve které jste vytvořili datovou továrnu.
- **SubscriptionId** : Zadejte jedinečný identifikátor předplatného Azure, v rámci kterého byl vytvořen objekt pro vytváření dat.
- **LogAccessDomain** : zadejte přihlašovací údaje domény pro přístup do složky protokolu v cestě UNC při zápisu souborů protokolu, které se vyžadují, když se zadá **logPath** a **LogLevel** není **null** .
- **LogAccessPassword** : zadejte přihlašovací údaje pro přístup ke složce protokolů v cestě UNC při zápisu souborů protokolu, které jsou požadovány, když je zadán parametr **logPath** a možnost **LogLevel** není **null** .
- **LogAccessUserName** : zadejte přihlašovací údaje uživatelského jména pro přístup ke složce protokolů v cestě UNC při zápisu souborů protokolu, které jsou požadovány, když je zadán parametr **logPath** a možnost **LogLevel** není **null** .
- **LogLevel** : zadejte vybraný rozsah protokolování z předdefinovaných možností **null** , **Basic** , **verbose** nebo **Performance** pro spuštění balíčku na Azure-SSIS IR.
- **LogPath** : zadejte cestu UNC ke složce protokolu, do které se zapisují soubory protokolu z spuštění balíčku na Azure-SSIS IR.
- **PackageAccessDomain** : zadejte přihlašovací údaje domény pro přístup k balíčkům v cestě UNC, která je zadána při vyvolání AzureDTExec.
- **PackageAccessPassword** : zadejte přihlašovací údaje hesla pro přístup k balíčkům v cestě UNC, která je zadána při vyvolání AzureDTExec.
- **PackageAccessUserName** : zadejte přihlašovací údaje uživatelského jména pro přístup k balíčkům v cestě UNC, která je zadána při vyvolání AzureDTExec.

Pokud chcete své balíčky a soubory protokolů ukládat do systémů souborů nebo sdílených složek místně, připojte se Azure-SSIS IR k virtuální síti připojené k vaší místní síti, aby mohla načíst vaše balíčky a zapsat soubory protokolu. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md).

Aby nedocházelo k zobrazování citlivých hodnot zapsaných v souboru *AzureDTExec. Settings* v prostém textu, zakódujeme je do řetězců kódování Base64. Když vyvoláte AzureDTExec, všechny řetězce kódované pomocí Base64 se dekóduje zpátky do jejich původních hodnot. Soubor *AzureDTExec. Settings* můžete dále zabezpečit tím, že omezíte účty, které k němu mají přístup.

## <a name="invoke-the-azuredtexec-utility"></a>Vyvolat nástroj AzureDTExec
AzureDTExec můžete vyvolat na příkazovém řádku a zadat relevantní hodnoty pro konkrétní možnosti ve scénáři použití.

Nástroj je nainstalován na adrese `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` . Můžete přidat cestu k proměnné prostředí PATH, aby ji bylo možné vyvolat odkudkoli.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Vyvolání AzureDTExec nabízí podobné možnosti jako vyvolání DTExec. Další informace najdete v tématu [dtexec Utility](/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Tady jsou aktuálně podporované možnosti:

- **/F [soubor]** : načte balíček, který je uložený v systému souborů, v souborové sdílené složce nebo v souborech Azure. Jako hodnotu této možnosti můžete zadat cestu UNC pro soubor balíčku v systému souborů, sdílenou složku nebo soubory Azure s příponou. dtsx. Pokud zadaná cesta UNC obsahuje mezery, vložte kolem celé cesty uvozovky.
- **/Conf [igFile]** : Určuje konfigurační soubor, ze kterého mají být extrahovány hodnoty. Pomocí této možnosti můžete nastavit konfiguraci za běhu pro balíček, který se liší od toho, který je zadaný v době návrhu. Můžete uložit různá nastavení do konfiguračního souboru XML a pak je načíst před spuštěním balíčku. Další informace najdete v tématu [konfigurace balíčků SSIS](/sql/integration-services/packages/package-configurations?view=sql-server-2017). Pokud chcete zadat hodnotu pro tuto možnost, použijte cestu UNC pro konfigurační soubor v systému souborů, sdílenou složku nebo soubory Azure s jeho rozšířením dtsConfig. Pokud zadaná cesta UNC obsahuje mezery, vložte kolem celé cesty uvozovky.
- **/Conn [ection]** : Určuje připojovací řetězce pro existující Správce připojení ve vašem balíčku. Pomocí této možnosti můžete nastavit připojovací řetězce pro modul runtime pro existující Správce připojení v balíčku, které se liší od těch, které jsou určeny v době návrhu. Zadejte hodnotu pro tuto možnost následujícím způsobem: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` .
- **/Set** : přepíše konfiguraci parametru, proměnné, vlastnosti, kontejneru, zprostředkovatele protokolů, enumerátoru foreach nebo připojení v balíčku. Tuto možnost lze zadat vícekrát. Zadejte hodnotu pro tuto možnost následujícím způsobem: `property_path;value` . Například `\package.variables[counter].Value;1` přepíše hodnotu `counter` proměnné jako 1. Průvodce **konfigurací balíčku** můžete použít k vyhledání, zkopírování a vložení hodnoty `property_path` pro položky v balíčku, jejichž hodnota má být popsána. Další informace najdete v tématu [Průvodce konfigurací balíčku](/sql/integration-services/packages/legacy-package-deployment-ssis).
- **/De [crypt]** : nastaví dešifrovací heslo balíčku, který je nakonfigurovaný s **EncryptAllWithPassword** / úrovní ochrany **EncryptSensitiveWithPassword** EncryptAllWithPassword.

> [!NOTE]
> Volání AzureDTExec s novými hodnotami pro své možnosti generuje nový kanál s výjimkou možnosti **/de [pt]** .

## <a name="next-steps"></a>Další kroky

Po vygenerování a spuštění jedinečných kanálů s aktivitou spustit balíček SSIS v nich, které se vyvolají AzureDTExec, je můžete monitorovat na portálu Data Factory. Data Factory triggery můžete přiřadit také v případě, že je chcete orchestrovat nebo naplánovat pomocí Data Factory. Další informace najdete v tématu [spouštění balíčků SSIS jako aktivit Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

> [!WARNING]
> Očekává se, že vygenerovaný kanál bude používat jenom AzureDTExec. Vlastnosti nebo parametry se mohou v budoucnu změnit, takže je neupravujte ani nepoužívejte pro žádné jiné účely. Úpravy mohou přerušit AzureDTExec. Pokud k tomu dojde, kanál odstraňte. AzureDTExec vygeneruje nový kanál při příštím vyvolání.