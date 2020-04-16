---
title: Spouštět balíčky SSIS z SSDT
description: Zjistěte, jak spouštět balíčky SSIS v Azure z SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399432"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Spouštění balíčků SSIS v Azure z SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje funkci projektů Azure-Enabled SQL Server Integration Services (SSIS) na SQL Server Data Tools (SSDT), který umožňuje spouštět balíčky na Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF).  Pomocí této funkce můžete otestovat existující balíčky SSIS před výtahem & přesunout/migrovat je do Azure nebo vyvinout nové balíčky SSIS pro spuštění v Azure.

Pomocí této funkce můžete vytvořit novou azure-SSIS IR nebo připojit existující k projektům SSIS a pak na něm spouštět balíčky.  Podporujeme spuštěné balíčky, které mají být nasazeny do katalogu SSIS (SSISDB) v modelu nasazení projektu a ty, které mají být nasazeny do souborových systémů / sdílených složek souborů /souborů Azure v modelu nasazení balíčku. 

## <a name="prerequisites"></a>Požadavky
Chcete-li tuto funkci použít, stáhněte a nainstalujte nejnovější ssdt s rozšířením SSIS Projects pro Visual Studio [odtud](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) nebo jako samostatný instalační program [odtud](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Projekty SSIS povolit Azure
Na SSDT můžete vytvořit nové projekty Azure SSIS pomocí šablony **Integration Services Project (Azure-Enabled).**

![Nový projekt Azure s podporou SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Případně můžete Azure povolit vaše stávající projekty SSIS kliknutím pravým tlačítkem myši na uzel projektu v panelu Průzkumník řešení sSDT a potom vybrat položku nabídky **s podporou Azure.**

![Azure povolit existující projekt SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure povolení vaše stávající projekty SSIS vyžaduje, abyste nastavili jejich verzi cílového serveru jako nejnovější verzi podporovanou Azure-SSIS IR, což je aktuálně **SQL Server 2017**, takže pokud jste tak již neučinili, zobrazí se dialogové okno, které to provede.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Připojení projektů s podporou Azure k SSIS v Azure Data Factory
Připojením projektů s podporou Azure k SSIS v adf můžete nahrát balíčky do souborů Azure a spustit je na Azure-SSIS IR.  Chcete-li tak učinit, postupujte takto:

1. Kliknutím pravým tlačítkem myši na projekt nebo uzel **Propojené prostředky Azure** v panelu Průzkumník řešení ssdt zobrazíte nabídku a vyberte položku nabídky Připojit k **SSIS v Azure Data Factory** a spusťte Průvodce **připojením SSIS v ADF .**

   ![Připojení k SSIS v adf](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Na stránce **SSIS v adf úvod,** zkontrolujte úvod a klikněte na tlačítko **Další** pokračovat.

   ![SSIS v Úvod ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Na stránce **Select SSIS IR v adf** vyberte existující služby ADF a Azure-SSIS IR, chcete-li spouštět balíčky nebo vytvářet nové, pokud žádné nemáte.
   - Pokud chcete vybrat stávající infračervený přenos Azure-SSIS, vyberte nejdřív příslušné předplatné Azure a ADF.
   - Pokud vyberete existující podavač ADF, který nemá žádné Azure-SSIS IR, klikněte na **tlačítko Vytvořit SSIS IR** pro vytvoření nového na portálu ADF/aplikaci.
   - Pokud vyberete své stávající předplatné Azure, které nemá žádné podavač ADF, klikněte na tlačítko **Vytvořit infračervený přenos SSIS** a spusťte **Průvodce vytvořením prostředí Integration Runtime**, kde můžete zadat umístění a předponu pro automatické vytvoření nové skupiny prostředků Azure, data factory a infračerveného úřadu SSIS vaším jménem, pojmenovaném v následujícím vzoru: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Vybrat ssis infračervený přenos v podavaču ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Na stránce **Select Azure Storage** vyberte svůj stávající účet Azure Storage, chcete-li nahrát balíčky do souborů Azure, nebo vytvořte nový, pokud žádné nemáte.
   - Pokud chcete vybrat svůj stávající účet Azure Storage, vyberte nejdřív příslušné předplatné Azure.
   - Pokud vyberete stejné předplatné Azure jako vaše Azure-SSIS IR, která nemá žádný účet Azure Storage, klikněte na tlačítko **Vytvořit úložiště Azure,** abychom automaticky vytvořili nové předplatné vaším jménem ve stejném umístění jako vaše zařízení Azure-SSIS IR s názvem kombinací předpony vašeho názvu infračerveného zařízení Azure-SSIS a data jeho vytvoření.
   - Pokud vyberete jiné předplatné Azure, které nemá žádný účet Azure Storage, klikněte na tlačítko **Vytvořit úložiště Azure** a vytvořte nový na webu Azure Portal.
   
   ![Výběr služby Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Kliknutím na tlačítko **Připojit** dokončete připojení.  Vybrané infračervené zařízení Azure-SSIS a účet úložiště Azure zobrazíme v panelu **Azure-SSDT** v panelu Propojené prostředky Azure.  Aktualizujeme také stav zařízení Azure-SSIS IR, zatímco můžete ji spravovat kliknutím pravým tlačítkem myši na jeho uzel a pak vyberete položku nabídky **Start\Stop\Manage,** která vás přenese na portál/aplikaci ADF.

## <a name="execute-ssis-packages-in-azure"></a>Spouštění balíčků SSIS v Azure
### <a name="starting-package-executions"></a>Spuštění spuštění spuštění balíčku
Po připojení projektů k SSIS v ADF, můžete spouštět balíčky na Azure-SSIS IR.  Máte dvě možnosti spuštění spuštění balíčku:
-  Kliknutím na tlačítko **Start** v panelu nástrojů SSDT rozbalíte nabídku a vyberete položku nabídky **Spustit v Azure.** 

   ![Spouštění v Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Klikněte pravým tlačítkem myši na uzel balíčku v panelu Průzkumník řešení ssdt, abyste rozbalili nabídku a vyberte **položku nabídky Spustit balíček v Azure.**

   ![Spouštět balíček v Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Provádění balíčků v Azure vyžaduje, abyste měli spuštěnou infračervené nebo infračervené zařízení Azure-SSIS, takže pokud je infračervená infračervená zařízení Azure-SSIS zastavená, objeví se dialogové okno, které ji spustí.  S výjimkou vlastní čas nastavení, tento proces by měl být dokončen do 5 minut, ale může trvat přibližně 20 - 30 minut pro Azure-SSIS IR připojení k virtuální síti.  Po spuštění balíčků v Azure můžete zastavit zařízení Azure-SSIS IR a spravovat jeho průběžné náklady kliknutím pravým tlačítkem myši na jeho uzel v panelu Průzkumník řešení s ssdt a pak výběrem položky nabídky **Start\Stop\Manage,** která vás přenese na portál/aplikaci ADF.

### <a name="checking-package-execution-logs"></a>Kontrola protokolů spuštění balíčku
Při spuštění balíčku naformátujeme a zobrazíme jeho protokol v okně Průběh SSDT.  U dlouho běžícího balíčku budeme pravidelně aktualizovat jeho protokol podle minut.  Spuštění balíčku můžete zastavit kliknutím na tlačítko **Stop** v panelu nástrojů SSDT, které jej okamžitě zruší.  Nezpracovaná data protokolu můžete také dočasně najít v `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`cestě unc (Universal Naming Convention): , ale po jednom dni je vyčistíme.

### <a name="switching-package-protection-level"></a>Přepínání úrovně ochrany balíčků
Provádění balíčků SSIS v Azure nepodporuje úrovně ochrany **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey.**  V důsledku toho pokud vaše balíčky jsou nakonfigurovány s těmi, budeme dočasně přepnout je do **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, respektive s náhodně generovanými hesly při nahrávání balíčků do souborů Azure pro spuštění na Azure-SSIS IR.

> [!NOTE]
> Pokud vaše balíčky obsahují úlohy spouštění balíčků, které odkazují na jiné balíčky nakonfigurované pomocí úrovně ochrany **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey,** je třeba ručně překonfigurovat tyto ostatní balíčky tak, aby **používaly EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, před spuštěním balíčků.

Pokud jsou vaše balíčky již nakonfigurovány s úrovněmi ochrany **EncryptSensitiveWithPassword**/**EncryptAllWithPassword,** ponecháme je beze změny, ale při nahrávání balíčků do souborů Azure na vašem zařízení Azure-SSIS IR budeme i nadále používat náhodně vygenerovaná hesla.

### <a name="using-package-configuration-file"></a>Použití konfiguračního souboru balíčku
Pokud použijete konfigurační soubory balíčků v modelu nasazení balíčku ke změně hodnot proměnných za běhu, automaticky tyto soubory nahrajeme s vašimi balíčky do souborů Azure pro spuštění v zařízení Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Použití úlohy spustit balíček
Pokud vaše balíčky obsahují úlohy spouštění balíčků, které odkazují na jiné balíčky uložené v místních systémech souborů, je třeba provést následující další nastavení:

1. Nahrajte ostatní balíčky do souborů Azure pod stejným účtem Azure Storage připojeným k vašim projektům a získejte novou cestu UNC, například.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Nahraďte cestu k souborům těchto ostatních balíčků ve Správci připojení k souborům úloh y Execute Package Tasks novou cestou UNC
   - Pokud váš počítač se systémem SSDT nemá přístup k nové cestě UNC, můžete změnit cestu k souboru v panelu Vlastnosti Správce připojení k souborům
   - Alternativně můžete použít proměnnou pro cestu k souboru přiřadit správnou hodnotu za běhu

Pokud vaše balíčky obsahují úlohy spouštění balíčků, které odkazují na jiné balíčky ve stejném projektu, není nutné žádné další nastavení.

## <a name="next-steps"></a>Další kroky
Jakmile budete spokojeni se spuštěním balíčků v Azure z SSDT, můžete je nasadit a spustit jako spustit aktivity balíčku SSIS v kanálech ADF, viz [Spuštění balíčků SSIS jako Spuštění aktivit balíčků SSIS v kanálech ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
