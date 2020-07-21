---
title: Spouštění balíčků SSIS z SSDT
description: Naučte se spouštět balíčky SSIS v Azure z SSDT.
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
ms.openlocfilehash: c3163d414e940d843489a34f319996b1b8ed6f4a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497370"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Spouštění balíčků SSIS v Azure z SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje funkci projektů služba SSIS (SQL Server Integration Services) (SSIS) s podporou Azure na SQL Server Data Tools (SSDT), která umožňuje spouštět balíčky v Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF).  Tuto funkci můžete použít k otestování stávajících balíčků SSIS ještě před tím, než je převedete & Shift nebo migrujete do Azure, nebo pokud chcete vyvíjet nové balíčky SSIS pro spuštění v Azure.

Pomocí této funkce můžete vytvořit novou Azure-SSIS IR nebo připojit existující SSIS k projektům a pak na něm spustit balíčky.  Podporujeme spouštění balíčků pro nasazení do katalogu SSIS (SSISDB) v modelu nasazení projektu a ty, které se mají nasadit do systémů souborů/sdílených složek/souborů Azure v modelu nasazení balíčku. 

## <a name="prerequisites"></a>Předpoklady
Pokud chcete tuto funkci použít, Stáhněte si a nainstalujte nejnovější SSDT s rozšířením projektů SSIS pro Visual Studio z [tohoto místa](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) nebo jako samostatný instalační program z [tohoto místa](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure – povolení projektů SSIS
### <a name="create-new-azure-enabled-ssis-projects"></a>Vytváření nových projektů SSIS s podporou Azure
V SSDT můžete pomocí šablony **integračních služeb (Azure-Enabled)** vytvořit nové projekty SSIS s podporou Azure.

   ![Nový projekt SSIS s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Po vytvoření projektu s podporou Azure se zobrazí výzva, abyste se připojili k SSIS v Azure Data Factory.

   ![Připojit Azure-SSIS IR výzvy](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Pokud se chcete k vašemu Azure-SSIS IR připojit hned, podrobnější informace najdete v tématu o [připojení k Azure-SSIS IR](#irconnect) . Můžete se také připojit později tak, že kliknete pravým tlačítkem myši na uzel projektu na panelu Průzkumník řešení SSDT, který se otevře v nabídce, a potom v podnabídce **SSIS Azure Data Factory v** nabídce Azure Data Factory vyberete položku **připojit k SSIS** .

### <a name="azure-enable-existing-ssis-projects"></a>Azure – povolení stávajících projektů SSIS
U stávajících projektů SSIS můžete Azure povolit pomocí následujících kroků:

1. Kliknutím pravým tlačítkem myši na uzel projektu na panelu Průzkumník řešení SSDT otevřete nabídku a potom výběrem položky nabídky projekt s **podporou Azure** v podnabídce **SSIS v Azure Data Factory** spusťte **Průvodce projektem s podporou Azure**.

   ![Azure – povolit existující projekt SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Na stránce **Vybrat konfiguraci sady Visual Studio** vyberte konfiguraci sady Visual Studio, která použije nastavení spuštění balíčku v Azure. Dobrým postupem je vytvořit novou konfiguraci sady Visual Studio pro Cloud a Azure – povolit projekt proti konfiguraci cloudu. V případě více konfigurací můžete vašim parametrům přiřadit různé hodnoty na základě různých prostředí (místně nebo v Azure). Další podrobnosti najdete v [tomto příkladu](#example) .

   ![Vybrat konfiguraci sady Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure – povolení vašich stávajících projektů SSIS vyžaduje, abyste jako verzi cílového serveru nastavili nejnovější verzi, kterou podporuje Azure-SSIS IR, která je aktuálně **SQL Server 2017**. Takže pokud jste to ještě neudělali, musíte ověřit, jestli balíček obsahuje další součásti, které nejsou podporované v SQL Server 2017, a klikněte na tlačítko Další, abyste mohli pokračovat, i když nebudete mít obavy.

   ![Přepnout verzi cílového serveru](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Chcete-li dokončit připojení k Azure-SSIS IR, přečtěte si téma [připojení k Azure-SSIS IR](#irconnect) .

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="irconnect"></a>Připojení projektů s podporou Azure k SSIS v Azure Data Factory

Připojením projektů s podporou Azure tak, aby se SSIS v ADF, můžete nahrát balíčky do souborů Azure a spustit je na Azure-SSIS IR. Provedete to následujícím postupem:

1. Na **úvodní stránce SSIS na ADF** se podívejte na Úvod a pokračujte kliknutím na tlačítko **Další** .

   ![SSIS v úvodu ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Na stránce **Vyberte SSIS IR v ADF** vyberte existující ADF a Azure-SSIS IR, jestli chcete spustit balíčky, nebo vytvořte nové, pokud žádné nemáte.
   - Pokud chcete vybrat existující Azure-SSIS IR, vyberte nejdřív příslušné předplatné Azure a ADF.
   - Pokud vyberete svůj existující ADF, který nemá žádné Azure-SSIS IR, klikněte na tlačítko **vytvořit SSIS IR** a vytvořte nový na portálu ADF nebo v aplikaci.
   - Pokud vyberete stávající předplatné Azure, které nemá žádný ADF, klikněte na tlačítko **vytvořit SSIS IR** a spusťte **průvodce vytvořením Integration runtime**, kde můžete zadat umístění a předponu, abychom mohli automaticky vytvořit novou skupinu prostředků Azure, Data Factory a SSIS IR vaším jménem, a to s názvem v následujícím vzoru: **YourPrefix-RG/DF/IR-YourCreationTime**.

   ![Výběr SSIS IR v ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Na stránce **vybrat Azure Storage** vyberte svůj existující účet Azure Storage, abyste do souborů Azure nahráli balíčky, nebo vytvořte novou, pokud žádné nemáte.
   - Pokud chcete vybrat svůj existující účet Azure Storage, vyberte nejdřív příslušné předplatné Azure.
   - Pokud zaškrtnete stejné předplatné Azure jako Azure-SSIS IR, která nemá žádný účet Azure Storage, klikněte na tlačítko **vytvořit Azure Storage** pro nás pro automatické vytvoření nového nového uživatele ve stejném umístění, ve kterém se používá, a na stejné místo jako vaše Azure-SSIS IR s názvem kombinováním předpony svého Azure-SSIS IRho názvu a jeho data vytvoření.
   - Pokud vyberete jiné předplatné Azure, které nemá žádný účet Azure Storage, klikněte na tlačítko **vytvořit Azure Storage** a vytvořte nový na Azure Portal.

   ![Výběr služby Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Kliknutím na tlačítko **připojit** dokončete připojení.  Vybraný Azure-SSIS IR a Azure Storage účet zobrazíme v uzlu **propojených prostředků Azure** na panelu Průzkumník řešení na panelu SSDT.  Aktualizujeme taky stav vašeho Azure-SSIS IR, ale můžete ho spravovat tak, že pravým tlačítkem myši kliknete na jeho uzel a pak vyberete položku nabídky **Start\Stop\Manage** , která vás přesměruje na portál a aplikaci ADF.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Vyhodnocení SSIS project\packages pro provádění v Azure
### <a name="assess-ssis-project-or-package"></a>Posouzení projektu nebo balíčku SSIS
Než zahájíte balíčky v Azure, můžete posoudit svůj balíček a vyhodnotit, jestli existují nějaké potenciální blokující migrace nebo informace, o kterých potřebujete vědět. 
-  Můžete buď vyhodnotit všechny balíčky v rámci projektu nebo jednoho balíčku.

   ![Vyhodnotit ](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)
    ![ balíček vyhodnocení projektu](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

-  Můžete získat zprávu o posouzení pro kontrolu každého problému s posouzením a každý problém bude mít podrobný popis a doporučení. Sestavu posouzení můžete také exportovat jako soubor CSV. 

   ![Vyhodnotit výsledek projektu](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppress-assessment-rule"></a>Potlačit pravidlo vyhodnocení
Pokud jste si jisti, že pro váš balíček není použité nějaké pravidlo hodnocení, můžete ho potlačit. 
-  Můžete přímo kliknout na odkaz **Konfigurovat potlačení pravidla posouzení** v sestavě posouzení.

   ![Nastavení potlačení pravidla posouzení](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Můžete ji také nakonfigurovat prostřednictvím **Nastavení Azure**.

   ![Nastavení s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Nastavení potlačení pravidel posouzení prostřednictvím nastavení s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Spouštění balíčků SSIS v Azure
### <a name="azure-enabled-setting"></a>Nastavení s povolenou službou Azure
Před spuštěním balíčků v Azure se můžete rozhodnout nakonfigurovat nastavení spuštění. Pokud chcete povolit ověřování systému Windows pro balíčky SSIS, postupujte prosím podle následujících kroků:

1. Kliknutím pravým tlačítkem myši na uzel projektu na panelu Průzkumník řešení SSDT se zobrazí nabídka a potom v podnabídce **SSIS v Azure Data Factory** vyberte položku nabídky **Nastavení Azure s podporou** .

   ![Nastavení s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Klikněte na rozevírací seznam **Povolit ověřování systému Windows** a vyberte **true (pravda**). Pak klikněte na tlačítko Upravit pro **přihlašovací údaje ověřování systému Windows** a zadejte přihlašovací údaje.

   ![Povolit ověřování systému Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Zadejte přihlašovací údaje v editoru **přihlašovacích údajů pro ověřování systému Windows** .

   ![Přihlašovací údaje pro ověřování systému Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Spouštění spouštění balíčků
Po připojení projektů k SSIS v ADF můžete spouštět balíčky na Azure-SSIS IR.  Spuštění balíčku můžete spustit dvěma způsoby:
-  Kliknutím na tlačítko **Start** v panelu nástrojů SSDT vyřaďte nabídku a vyberte položku nabídky **Spustit v Azure** . 

   ![Provést v Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Kliknutím pravým tlačítkem na uzel balíčku v Průzkumník řešení panelu SSDT se otevře nabídka a v položce nabídky Azure vyberte možnost **spustit balíček** .

   ![Spustit balíček v Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Spouštění balíčků v Azure vyžaduje, abyste měli spuštěný Azure-SSIS IR, takže pokud je Azure-SSIS IR zastavený, zobrazí se dialogové okno, aby se spouštělo.  S výjimkou času vlastní instalace by tento proces měl být dokončen během 5 minut, ale může trvat přibližně 20-30 minut, než se Azure-SSIS IR připojuje k virtuální síti.  Po spuštění balíčků v Azure můžete zastavit Azure-SSIS IR a spravovat tak jejich spuštěné náklady tak, že pravým tlačítkem myši kliknete na svůj uzel v Průzkumník řešením panelu SSDT a pak vyberete položku nabídky **Start\Stop\Manage** , která vás přesměruje na portál ADF nebo na aplikaci.

### <a name="checking-package-execution-logs"></a>Kontrola protokolů spuštění balíčku
Při spuštění balíčku naformátujeme a zobrazíme protokol v okně průběhu SSDT.  V případě dlouhotrvajícího balíčku budeme průběžně aktualizovat protokol o minuty.  Spuštění balíčku můžete zastavit kliknutím na tlačítko **zastavit** na panelu nástrojů SSDT, který ho okamžitě zruší.  Nezpracované data protokolu můžete také dočasně najít v cestě UNC (Universal Naming Convention): `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , ale po jednom dni ji vyčistíme.

### <a name="switching-package-protection-level"></a>Přepínání úrovně ochrany balíčků
Spouštění balíčků SSIS v Azure nepodporuje **EncryptSensitiveWithUserKey** / úrovně ochrany**EncryptAllWithUserKey** EncryptSensitiveWithUserKey.  V důsledku toho budou v případě, že jsou balíčky nakonfigurovány s těmito balíčky, dočasně je přepnete do **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**s náhodně generovanými hesly při nahrávání balíčků do souborů Azure za účelem provedení na vašem Azure-SSIS IR.

> [!NOTE]
> Pokud vaše balíčky obsahují úlohy spouštěné balíčku, které odkazují na další balíčky nakonfigurované s úrovněmi ochrany **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** , musíte tyto ostatní balíčky před spuštěním balíčků ručně znovu nakonfigurovat tak, aby používaly **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**.

Pokud jsou vaše balíčky už nakonfigurované **EncryptSensitiveWithPassword**s / úrovněmi ochrany EncryptSensitiveWithPassword**EncryptAllWithPassword** , nebudeme je uchovávat beze změny, ale při nahrání balíčků do souborů Azure budou pořád používat náhodně generovaná hesla, aby je bylo možné vykonání na vaše Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Použití konfiguračního souboru balíčku
Pokud v modelu nasazení balíčku použijete konfigurační soubory balíčku pro změnu hodnot proměnných v době běhu, budeme tyto soubory automaticky nahrát do souborů Azure, aby je bylo možné vykonání na vašem Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Pomocí úlohy spustit balíček
Pokud vaše balíčky obsahují úlohy vykonání balíčku, které odkazují na jiné balíčky uložené v místních systémech souborů, musíte provést následující další nastavení:

1. Ostatní balíčky nahrajte do souborů Azure pod stejným Azure Storage účet, který jste připojili k vašim projektům, a získáte novou cestu UNC, např.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Nahraďte cestu k souborům pro tyto ostatní balíčky ve Správci připojení k souboru pro provádění úloh balíčku s novou cestou UNC.
   - Pokud Váš počítač se systémem SSDT nemá přístup k nové cestě UNC, můžete změnit cestu k souboru na panelu Vlastnosti v souboru Správce připojení.
   - Alternativně můžete použít proměnnou pro cestu k souboru pro přiřazení správné hodnoty v době běhu.

Pokud vaše balíčky obsahují úlohy vykonání balíčku, které odkazují na jiné balíčky ve stejném projektu, není nutné žádné další nastavení.

## <a name="switching-package-execution-environments-with-azure-enabled-projects"></a><a name="example"></a>Přepínání prostředí provádění balíčků pomocí projektů s podporou Azure

Chcete-li přepnout prostředí pro spouštění balíčků s projekty s podporou Azure, můžete vytvořit více konfigurací sady Visual Studio a použít pro ně různé hodnoty pro parametry specifické pro konkrétní prostředí. Například je k dispozici jednoduchý balíček SSIS s **úlohou systému souborů** , která nastavuje atributy zadaného souboru, a můžete ho jednoduše migrovat do cloudu pomocí následujících kroků:

1. Definujte parametr **FilePath** typu řetězce, což je cesta k souboru cílového souboru.

   ![Definovat parametr balíčku](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Přidružte **zdrojové připojení** k tomuto parametru. 

   ![Aktualizovat zdrojové připojení](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Vytvořte novou konfiguraci sady Visual Studio pro Cloud v aplikaci Visual Studio Configuration Manager.

4. Definujte hodnoty pro tento parametr pro každou konfiguraci sady Visual Studio.

   ![Přepsat hodnoty parametrů](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure – tento projekt SSIS s podporou konfigurace sady Visual Studio pro Cloud.

6. Spusťte tento balíček v Azure. Prostředí můžete snadno přepnout zpátky na místní a přepnout aktuální konfiguraci sady Visual Studio.

   ![Přepnout konfiguraci sady Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

## <a name="current-limitations"></a>Aktuální omezení
-  Tato funkce SSDT v současné době nepodporuje národní Cloud.

## <a name="next-steps"></a>Další kroky
Až budete s používáním balíčků v Azure spokojeni z SSDT, můžete je nasadit a spustit jako aktivity balíčku SSIS v kanálech ADF. Další informace najdete v tématu [spouštění balíčků SSIS jako spouštění aktivit balíčku SSIS v kanálech ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
