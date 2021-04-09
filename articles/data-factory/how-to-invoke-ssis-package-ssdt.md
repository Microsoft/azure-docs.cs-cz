---
title: Spouštění balíčků SSIS z SSDT
description: Naučte se spouštět balíčky SSIS v Azure z SSDT.
ms.service: data-factory
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: f11a3845e8644f3f60425538b2ef32cff668d88d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384924"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Spouštění balíčků SSIS v Azure z SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje funkci projektů služba SSIS (SQL Server Integration Services) (SSIS) s podporou Azure na SQL Server Data Tools (SSDT). Umožňuje vám posoudit kompatibilitu cloudu pro vaše balíčky SSIS a spouštět je na Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF). Tuto funkci můžete použít k otestování stávajících balíčků předtím, než &ete Shift nebo migrujete do Azure, nebo budete vyvíjet nové balíčky pro spuštění v Azure.

Pomocí této funkce můžete připojit nově vytvořenou nebo existující Azure-SSIS IR k projektům SSIS a pak na něm spustit balíčky.  Podporujeme spouštění balíčků, které se mají nasadit do katalogu SSIS (SSISDB), který je hostovaný vaším Azure SQL Database serverem nebo spravovanou instancí v modelu nasazení projektu. Podporujeme taky spouštění balíčků, které se nasadí do systému souborů/Azure Files/SQL Server Database (MSDB) hostované vaší spravovanou instancí Azure SQL v modelu nasazení balíčku. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete tuto funkci použít, Stáhněte si a nainstalujte nejnovější SSDT s SSIS projekty rozšíření pro Visual Studio (VS) z [tohoto místa](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects). Alternativně můžete také stáhnout a nainstalovat nejnovější SSDT jako samostatný instalační [program.](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure – povolení projektů SSIS

### <a name="creating-new-azure-enabled-ssis-projects"></a>Vytváření nových projektů SSIS s podporou Azure

V SSDT můžete pomocí šablony **integračních služeb (Azure-Enabled)** vytvořit nové projekty SSIS s podporou Azure.

   ![Nový projekt SSIS s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Po vytvoření projektu s podporou Azure se zobrazí výzva, abyste se připojili k SSIS v Azure Data Factory.

   ![Připojit Azure-SSIS IR výzvy](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Pokud se chcete hned připojit k Azure-SSIS IR, přečtěte si další informace v tématu [připojení k Azure-SSIS IR](#connectssisir) . Můžete se také připojit později tak, že kliknete pravým tlačítkem myši na uzel projektu v Průzkumník řešením okně SSDT k místní nabídce. Potom v podnabídce **Azure Data Factory v** podnabídce vyberte položku **připojit k SSIS v položce Azure Data Factory** .

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure – povolení stávajících projektů SSIS

U stávajících projektů SSIS můžete Azure povolit pomocí následujících kroků:

1. Kliknutím pravým tlačítkem myši na uzel projektu v Průzkumník řešení okně SSDT zobrazíte nabídku. V dalším kroku vyberte položku **projektu s podporou Azure** v **SSIS v** podnabídce Azure Data Factory pro spuštění **Průvodce projektem s podporou Azure**.

   ![Azure – povolit existující projekt SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Na stránce **Vybrat konfiguraci sady Visual Studio** vyberte existující konfiguraci vs pro použití nastavení spuštění balíčku v Azure. Můžete také vytvořit nové, pokud jste to ještě neudělali, přečtěte si téma [Vytvoření nové konfigurace vs](/visualstudio/ide/how-to-create-and-edit-configurations). Pro spouštění balíčků v místních a cloudových prostředích doporučujeme mít aspoň dvě různé konfigurace VS. díky tomu můžete Azure – povolit projekt proti konfiguraci cloudu. Tímto způsobem, pokud jste nastavili parametrizovaný projekt nebo balíčky, můžete přiřadit různé hodnoty k projektu nebo parametrům balíčku za běhu na základě různých prostředí pro spuštění (buď na vašem místním počítači, nebo v Azure). Podívejte se například na téma [přepínání prostředí pro spouštění balíčků](#switchenvironment).

   ![Vybrat konfiguraci sady Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure – povolení vašich stávajících projektů SSIS vyžaduje, abyste jako verzi cílového serveru nastavili nejnovější verzi, kterou podporuje Azure-SSIS IR. Azure-SSIS IR je aktuálně založená na **SQL Server 2017**. Zajistěte, aby balíčky neobsahovaly další součásti, které nejsou v SQL Server 2017 podporovány. Zajistěte prosím, aby byly v Azure-SSIS IR také nainstalovány všechny kompatibilní další součásti prostřednictvím vlastních nastavení. Další informace najdete v tématu [přizpůsobení Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md). Pokračujte kliknutím na tlačítko **Další** .

   ![Přepnout verzi cílového serveru](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Pokud chcete dokončit připojení projektu k Azure-SSIS IR, přečtěte si téma [připojení k Azure-SSIS IR](#connectssisir) .

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Připojení projektů s podporou Azure k SSIS v Azure Data Factory

Připojením projektů s podporou Azure tak, aby se SSIS v ADF, můžete nahrát balíčky do souborů Azure a spustit je na Azure-SSIS IR. Můžete to udělat pomocí následujících kroků:

1. Na **úvodní stránce SSIS na ADF** se podívejte na Úvod a kliknutím na tlačítko **Další** pokračujte.

   ![SSIS v úvodu ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Na stránce **Vyberte SSIS IR v ADF** vyberte existující ADF a Azure-SSIS IR pro spouštění balíčků. Pokud žádné nemáte, můžete také vytvořit nové.
   - Pokud chcete vybrat existující Azure-SSIS IR, vyberte nejdřív příslušné předplatné Azure a ADF.
   - Pokud vyberete svůj existující ADF, který nemá žádné Azure-SSIS IR, vyberte na portálu ADF tlačítko **vytvořit SSIS IR** a vytvořte nový. Po vytvoření se můžete na tuto stránku vrátit a vybrat novou Azure-SSIS IR.
   - Pokud vyberete stávající předplatné Azure, které nemá žádné ADF, vyberte tlačítko **vytvořit SSIS IR** a spusťte **průvodce vytvořením Integration runtime**. V průvodci můžete zadat určené umístění a předponu, abychom mohli automaticky vytvořit novou skupinu prostředků Azure, Data Factory a SSIS IR vaším jménem, a to s názvem v následujícím vzoru: **YourPrefix-RG/DF/IR-YourCreationTime**. Po vytvoření se můžete na tuto stránku vrátit a vybrat svoje nové ADF a Azure-SSIS IR.

   ![Výběr SSIS IR v ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Na stránce **vybrat Azure Storage** vyberte svůj existující účet Azure Storage a nahrajte balíčky do souborů Azure. Novou možnost můžete vytvořit také v případě, že nemáte žádné z nich.
   - Pokud chcete vybrat svůj existující účet Azure Storage, vyberte nejdřív příslušné předplatné Azure.
   - Pokud vyberete stejné předplatné Azure jako Azure-SSIS IR, který nemá žádný účet Azure Storage, vyberte tlačítko **vytvořit Azure Storage** . Za vás automaticky vytvoříme nové místo ve stejném umístění, ve kterém je Azure-SSIS IR s názvem, a to tak, že zkombinujete předponu názvu Azure-SSIS IR a datum vytvoření. Po vytvoření se můžete na tuto stránku vrátit a vybrat nový účet Azure Storage.
   - Pokud vyberete jiné předplatné Azure, které nemá žádný účet Azure Storage, vyberte na Azure Portal tlačítko **vytvořit Azure Storage** a vytvořte nové. Po vytvoření se můžete na tuto stránku vrátit a vybrat nový účet Azure Storage.

   ![Výběr služby Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Kliknutím na tlačítko **připojit** dokončete připojení projektu k Azure-SSIS IR. V **propojeném uzlu prostředky Azure** v Průzkumník řešení okně SSDT zobrazíme váš vybraný Azure-SSIS IR a Azure Storage účet. Také pravidelně aktualizujeme a zobrazíme stav vašich Azure-SSIS IR. Azure-SSIS IR můžete spravovat tak, že pravým tlačítkem myši kliknete na jeho uzel a pak vyberete položku **Start\Stop\Manage** , která vás zavede na portál ADF.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Vyhodnocení SSIS project\packages pro provádění v Azure

### <a name="assessing-single-or-multiple-packages"></a>Vyhodnocování jednoho nebo více balíčků

Než zahájíte balíčky v Azure, můžete je vyhodnotit tak, aby se vyhodnotily případné problémy s kompatibilitou cloudu. Mezi ně patří bloky migrace a další informace, o kterých byste měli vědět. 
-  Máte možnost vyhodnotit jednotlivé balíčky jeden po druhém nebo všechny balíčky současně v rámci projektu.

   ![Vyhodnotit balíček](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Vyhodnotit projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  V okně **sestavy posouzení** SSDT můžete najít všechny potenciální problémy s kompatibilitou cloudu, které jsou Surface, z nichž každá má vlastní podrobný popis a doporučení. Sestavu posouzení můžete také exportovat do souboru CSV, který můžete sdílet s kýmkoli, kdo by tyto problémy měli zmírnit. 

   ![Sestava posouzení](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Potlačení pravidel hodnocení

Jakmile si zajistěte, že některé potenciální problémy s kompatibilitou cloudu nejsou použitelné nebo byly ve vašich balíčcích správně zmírňované, můžete potlačit relevantní pravidla hodnocení, která jsou Surface. Tím se sníží šum v dalších sestavách posouzení.
-  V okně **sestavy posouzení** pro SSDT vyberte odkaz pro **potlačení pravidla posouzení** , abyste mohli zobrazit okno **Nastavení potlačení pravidel hodnocení** , kde můžete vybrat pravidla pro posouzení, která chcete potlačit.

   ![Nastavení potlačení pravidla posouzení](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Případně můžete kliknout pravým tlačítkem myši na uzel projektu v Průzkumník řešením okně SSDT, aby se místní nabídka zobrazovala. Vyberte položku **Nastavení Azure s podporou Azure** v **SSIS v** podnabídce Azure Data Factory pro otevření okna obsahujícího stránky vlastností projektu. V části **nastavení s povoleným Azure** vyberte vlastnost **potlačit ID pravidel vyhodnocení** . Nakonec kliknutím na tlačítko se třemi tečkami (**...**) zobrazíte okno **Nastavení potlačení pravidel hodnocení** , kde můžete vybrat pravidla pro posouzení, která chcete potlačit.

   ![Nastavení s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Nastavení potlačení pravidel posouzení prostřednictvím nastavení s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Spouštění balíčků SSIS v Azure

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Konfigurace nastavení povolených pro Azure

Než začnete balíčky v Azure spouštět, můžete pro ně nakonfigurovat nastavení s podporou Azure. Můžete například povolit ověřování systému Windows ve vašem Azure-SSIS IR pro přístup k místním nebo cloudovým úložištím dat pomocí následujících kroků:

1. Kliknutím pravým tlačítkem myši na uzel projektu v Průzkumník řešení okně SSDT zobrazíte nabídku. V dalším kroku vyberte položku **Nastavení Azure s podporou Azure** v **SSIS v** podnabídce Azure Data Factory pro otevření okna obsahujícího stránky vlastností projektu.

   ![Nastavení s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. V části **nastavení povolená pro Azure** vyberte vlastnost **Povolit ověřování systému Windows** a v rozevírací nabídce vyberte **true (pravda** ). Potom vyberte vlastnost **pověření ověřování systému Windows** a potom kliknutím na tlačítko se třemi tečkami (**...**) Přistavte okno **přihlašovací údaje pro ověřování systému Windows** .

   ![Povolit ověřování systému Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Zadejte přihlašovací údaje pro ověřování systému Windows. Například pro přístup k souborům Azure můžete zadat `Azure` , `YourStorageAccountName` a `YourStorageAccountKey` pro **doménu**, **uživatelské jméno** a **heslo**, v uvedeném pořadí.

   ![Přihlašovací údaje pro ověřování systému Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Spouštění spouštění balíčků

Po připojení projektů s podporou Azure za účelem SSISí v ADF, vyhodnocení jejich kompatibility v cloudu a zmírnění potenciálních problémů můžete na Azure-SSIS IR spustit nebo otestovat balíčky.
-  Kliknutím na tlačítko **Start** v panelu nástrojů SSDT rozevírací nabídku rozbalte. V dalším kroku vyberte položku **Spustit v Azure** .

   ![Provést v Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Případně můžete kliknout pravým tlačítkem myši na uzel balíčku v Průzkumník řešením okně SSDT, aby se místní nabídka zobrazovala. V dalším kroku vyberte **spustit balíček v položce Azure** .

   ![Spustit balíček v Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Spouštění balíčků v Azure vyžaduje, abyste měli spuštěný Azure-SSIS IR, takže pokud je Azure-SSIS IR zastavený, zobrazí se dialogové okno, aby se spouštělo. S výjimkou času vlastní instalace by tento proces měl být dokončen během 5 minut, ale může trvat přibližně 20-30 minut, než se Azure-SSIS IR připojuje k virtuální síti. Po spuštění balíčků v Azure můžete zastavit Azure-SSIS IR, abyste mohli spravovat jeho spuštěné náklady tím, že v Průzkumník řešení okně SSDT kliknete pravým tlačítkem na jeho uzel a pak vyberete položku **Start\Stop\Manage** , kterou vás přesměruje na portál ADF.

### <a name="using-execute-package-task"></a>Pomocí úlohy spustit balíček

Pokud vaše balíčky obsahují úlohy vykonání balíčku, které odkazují na podřízené balíčky uložené v místních systémech souborů, postupujte podle těchto dalších kroků:

1. Nahrajte podřízené balíčky do souborů Azure v rámci stejného Azure Storage účtu připojeného k vašim projektům a získejte novou cestu UNC (Universal Naming Convention), např. `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. Nahraďte cestu k souboru těchto podřízených balíčků ve Správci připojení k souboru pro provádění úloh balíčku s novou cestou UNC.
   - Pokud Váš místní počítač se systémem SSDT nemá přístup k nové cestě UNC, můžete ho zadat na panelu Vlastnosti v souboru Správce připojení.
   - Alternativně můžete použít proměnnou pro cestu k souboru pro přiřazení správné hodnoty v době běhu.

Pokud vaše balíčky obsahují úlohy vykonání balíčku, které odkazují na podřízené balíčky ve stejném projektu, není nutné provádět žádné další kroky.

### <a name="switching-package-protection-level"></a>Přepínání úrovně ochrany balíčků

Spouštění balíčků SSIS v Azure nepodporuje  / úrovně ochrany **EncryptAllWithUserKey** EncryptSensitiveWithUserKey. V důsledku toho jsou v případě, že jsou vaše balíčky nakonfigurovány k použití, je dočasně převede na použití / **EncryptAllWithPassword** úrovní ochrany EncryptSensitiveWithPassword. Při nahrávání vašich balíčků do souborů Azure budeme také náhodně generovat šifrovací hesla, která vám na vaše Azure-SSIS IR.

> [!NOTE]
> Pokud vaše balíčky obsahují úlohy spouštěné balíčku, které odkazují na podřízené balíčky nakonfigurované tak, aby používaly úrovně ochrany **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** , musíte tyto podřízené balíčky před spuštěním balíčků ručně znovu nakonfigurovat tak, aby používaly úrovně ochrany **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** .

Pokud jsou vaše balíčky už nakonfigurované tak, aby používaly úrovně ochrany **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** , nebudeme je uchovávat beze změny. I když nahrajeme vaše balíčky do služby soubory Azure ke spuštění na vašem Azure-SSIS IR, budeme pořád vygenerovat šifrovací hesla.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Přepínání prostředí provádění balíčků

Pokud parametrizovatte projekt nebo balíčky v modelu nasazení projektu, můžete vytvořit více konfigurací VS pro přepínání prostředí provádění balíčků. Tímto způsobem můžete přiřadit hodnoty specifické pro prostředí k parametrům projektu nebo balíčku za běhu. Pro spouštění balíčků v místních a cloudových prostředích doporučujeme, abyste měli k dispozici minimálně dvě různé konfigurace VS. díky tomu můžete Azure – povolit vaše projekty v konfiguraci cloudu. Tady je podrobný příklad přepínání prostředí provádění balíčků mezi místním počítačem a Azure:

1. Řekněme, že balíček obsahuje úlohu systému souborů, která nastavuje atributy souboru. Když ho spustíte na místním počítači, nastaví atributy souboru uloženého v místním systému souborů. Když ho spustíte na svém Azure-SSIS IR, přejete si, aby nastavil atributy souboru uloženého ve službě soubory Azure. Nejdřív vytvořte parametr balíčku typu String a pojmenujte ho **FilePath** , aby se mohla uchovávat hodnota cesty k cílovému souboru.

   ![Vytvořit parametr balíčku](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Potom na stránce **Obecné** v okně **Editor úloh systému souborů** parametrizovat vlastnost **SourceVariable** v části **zdrojové připojení** s parametrem balíčku **FilePath** . 

   ![Parametrizovat zdrojové připojení](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Ve výchozím nastavení máte existující konfiguraci VS pro spouštění balíčků v místním prostředí s názvem **vývoj**. Vytvořte novou konfiguraci VS pro spouštění balíčků v cloudovém prostředí s názvem **Azure**. Další informace najdete v tématu [Vytvoření nové konfigurace vs](/visualstudio/ide/how-to-create-and-edit-configurations), pokud jste to ještě neudělali.

4. Při prohlížení parametrů balíčku vyberte tlačítko **přidat parametry do konfigurací** a otevřete okno **Spravovat hodnoty parametrů** pro váš balíček. V dalším kroku přiřaďte jiné hodnoty cílového souboru k parametru balíčku **FilePath** v části **vývoj** a konfigurace **Azure** .

   ![Přiřazení hodnot parametrů](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure – povolení projektu v konfiguraci cloudu, další informace najdete v tématu [Azure – povolení stávajících projektů SSIS](#azureenableproject), pokud jste to ještě neudělali. Dále nakonfigurujte nastavení s podporou Azure, aby bylo možné povolit ověřování Windows pro Azure-SSIS IR přístup k souborům Azure. Pokud jste tak ještě neučinili, přečtěte si téma [Konfigurace nastavení s podporou Azure](#azureenabledsettings).

6. Spusťte balíček v Azure. Prostředí pro spuštění balíčku můžete přepnout zpátky na svůj místní počítač tak, že vyberete konfiguraci **vývoje** .

   ![Přepnout konfiguraci sady Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Použití konfiguračního souboru balíčku

Pokud v modelu nasazení balíčku použijete konfigurační soubory balíčku, můžete v době běhu přiřadit hodnoty specifické pro prostředí k vlastnostem balíčku. Tyto soubory automaticky nahrajeme do souborů Azure do souborů Azure pro provedení ve vašem Azure-SSIS IR.

### <a name="checking-package-execution-logs"></a>Kontrola protokolů spuštění balíčku

Po spuštění balíčku naformátujeme a zobrazíme protokoly v okně **průběhu** SSDT. Pro dlouhotrvající balíček budeme průběžně aktualizovat protokoly o minuty. Spuštění balíčku můžete okamžitě zrušit tak, že na panelu nástrojů SSDT vyberete tlačítko **zastavit** . Nezpracovaná data protokolů můžete dočasně najít také v následující cestě UNC: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , ale po jednom dni ji vyčistíme.

## <a name="current-limitations"></a>Aktuální omezení

-  SSDT s podporou Azure podporuje jenom obchodní a globální cloudové oblasti a v současné době nepodporuje veřejné a národní cloudové oblasti.

## <a name="next-steps"></a>Další kroky

Až budete s spuštěním balíčků v Azure spokojeni z SSDT, můžete je nasadit a spustit jako aktivity balíčku SSIS v kanálech ADF. Další informace najdete v tématu [spouštění balíčků SSIS jako spouštění aktivit balíčku SSIS v kanálech ADF](./how-to-invoke-ssis-package-ssis-activity.md).