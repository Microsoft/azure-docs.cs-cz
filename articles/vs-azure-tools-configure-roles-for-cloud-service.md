---
title: Konfigurace role pro cloudové služby Azure pomocí sady Visual Studio | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nakonfigurovat role pro Azure cloud services pomocí sady Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 4405476a2f006ca8e4e565a8cdd2f6c12e2ed684
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969472"
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Konfigurace role služby Azure cloud pomocí sady Visual Studio
Cloudové služby Azure může mít jednu nebo více pracovních procesů nebo webové role. Pro každou roli budete muset definovat, jak nastavit tuto roli a také nakonfigurovat tak, jak tuto roli běží. Další informace o rolích v cloudových službách, podívejte se na video [Úvod do služby Azure Cloud Services](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

Informace pro cloudové služby jsou uloženy v následující soubory:

- **Soubor ServiceDefinition.csdef** – definiční soubor služby definuje nastavení běhového prostředí pro cloudové služby, včetně toho, jaké role jsou povinné, koncové body a velikost virtuálního počítače. Žádná z dat uložených v `ServiceDefinition.csdef` lze změnit, pokud vaše role běží.
- **Soubor ServiceConfiguration.cscfg** – konfigurační soubor služby nakonfiguruje, kolik instancí role jsou spuštěny a hodnoty nastavení definované pro roli. Data uložená v `ServiceConfiguration.cscfg` lze změnit, když vaše role běží.

Pokud chcete uchovávat různé hodnoty pro nastavení, které řídí vykonávání roli, můžete definovat více konfigurací služby. Pro jednotlivá prostředí nasazení můžete použít konfiguraci jinou službu. Můžete třeba nastavit připojovací řetězec účtu úložiště pomocí emulátoru lokálního úložiště Azure v místní službě konfigurace a vytvořit další konfigurace služby pro použití služby Azure storage v cloudu.

Při vytváření cloudové služby Azure v sadě Visual Studio dvou konfiguracích služby se automaticky vytvořen a přidán do projektu Azure:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Konfigurace cloudové služby Azure
Cloudové služby Azure z Průzkumníka řešení v sadě Visual Studio, můžete nakonfigurovat, jak je znázorněno v následujícím postupu:

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **vlastnosti**.
   
    ![Místní nabídky projektu Průzkumníka řešení](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. Na stránce vlastností projektu, vyberte **vývoj** kartu. 

    ![Stránka Vlastnosti projektu - karta vývoj](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. V **konfigurace služby** seznamu, vyberte název, který chcete upravit konfiguraci služby. (Pokud chcete provést změny na všech konfiguracích služby pro tuto roli, vyberte **všechny konfigurace**.)
   
    > [!IMPORTANT]
    > Pokud se rozhodnete specifické konfigurace služby, některé vlastnosti jsou zakázané, protože jejich lze nastavit pouze u všech konfigurací. Pokud chcete upravit tyto vlastnosti, musíte vybrat **všechny konfigurace**.
    > 
    > 
   
    ![Seznam konfiguraci služby pro cloudové služby Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>Změňte počet instancí role
Pro zlepšení výkonu cloudové služby, můžete změnit počet instancí role, které jsou spuštěny na základě počtu uživatelů nebo zatížení, byl očekáván pro určité role. Samostatný virtuální počítač se vytvoří pro každou instanci role při spuštění cloudové služby v Azure. To ovlivní fakturaci pro nasazení této cloudové služby. Další informace o fakturaci najdete v tématu [vysvětlení vašeho vyúčtování služeb Microsoft Azure](billing/billing-understand-your-bill.md).

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, rozbalte uzel projektu. V části **role** uzel, klikněte pravým tlačítkem na roli, kterou chcete aktualizovat a v místní nabídce vyberte **vlastnosti**.

    ![Řešení Explorer Azure role kontextové nabídky](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Vyberte **konfigurace** kartu.

    ![Karta Konfigurace](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. V **konfigurace služby** vyberte konfiguraci služby, které chcete aktualizovat.
   
    ![Konfigurace služby seznamu](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. V **počet instancí** textové pole, zadejte počet instancí, které chcete spustit pro tuto roli. Každá instance běží na samostatném virtuálním počítači, při publikování cloudové služby v Azure.

    ![Aktualizuje se počet instancí](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. Ze sady Visual Studio panel nástrojů, vyberte **Uložit**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Správa připojovací řetězce pro účty úložiště
Můžete přidat, odebrat nebo změnit připojovací řetězce pro konfiguraci služby. Například může být vhodné místní připojovací řetězec pro místní služba konfigurace, která má hodnotu `UseDevelopmentStorage=true`. Můžete také nakonfigurovat konfigurací cloudové služby, který používá účet úložiště v Azure.

> [!WARNING]
> Po zadání klíče údaje účtu úložiště Azure pro připojovací řetězec účtu úložiště se tyto informace se ukládají místně v konfiguračním souboru služby. Tyto informace není aktuálně uložený jako šifrovaný text.
> 
> 

Když použijete jinou hodnotu pro každou konfiguraci služby, není nutné používat jinými řetězci připojení v cloudové službě nebo upravit kód při publikování vaší cloudové služby v Azure. Můžete použít stejný název pro připojovací řetězec v kódu a hodnota se liší v závislosti na konfiguraci služby, který vyberete při vytváření cloudové služby nebo při jejím publikování.

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, rozbalte uzel projektu. V části **role** uzel, klikněte pravým tlačítkem na roli, kterou chcete aktualizovat a v místní nabídce vyberte **vlastnosti**.

    ![Řešení Explorer Azure role kontextové nabídky](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Vyberte **nastavení** kartu.

    ![Karta nastavení](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. V **konfigurace služby** vyberte konfiguraci služby, které chcete aktualizovat.

    ![Konfigurace služby](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Chcete-li přidat připojovací řetězec, **přidat nastavení**.

    ![Přidat připojovací řetězec](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Jakmile toto nové nastavení je přidaný do seznamu, aktualizujte řádek v seznamu potřebné informace.

    ![Nový připojovací řetězec](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Název** – zadejte název, který chcete použít pro připojovací řetězec.
    - **Typ** – vyberte **připojovací řetězec** z rozevíracího seznamu.
    - **Hodnota** – můžete zadat připojovací řetězec přímo do **hodnotu** buňky, nebo vyberte tři tečky (...) pro práci **vytvořit připojovací řetězec úložiště** dialogového okna.  

1. V **vytvořit připojovací řetězec úložiště** dialogového okna, vyberte možnost pro **připojit pomocí**. Postupujte podle pokynů pro zvolené možnosti:

    - **Emulátor úložiště Microsoft Azure** – Pokud vyberete tuto možnost, zbývající nastavení v dialogovém okně je zakázaná, protože se vztahují pouze na Azure. Vyberte **OK**.
    - **Vaše předplatné** – Pokud vyberete tuto možnost použijte v rozevíracím seznamu vyberte a přihlaste se do účtu Microsoft, nebo přidat účet Microsoft. Vyberte předplatné a úložiště účtu Azure. Vyberte **OK**.
    - **Ručně zadali přihlašovací údaje** – zadejte název účtu úložiště a primární a druhý klíč. Vyberte možnost pro **připojení** (protokol HTTPS se doporučuje pro většinu scénářů). Vyberte **OK**.

1. Pokud chcete odstranit připojovací řetězec, vyberte připojovací řetězec a pak vyberte **nastavení odebrat**.

1. Ze sady Visual Studio panel nástrojů, vyberte **Uložit**.

## <a name="programmatically-access-a-connection-string"></a>Programový přístup k připojovací řetězec

Následující kroky ukazují, jak programově přistupovat k připojovací řetězec pomocí jazyka C#.

1. Přidejte následující direktivy using do souboru C# ve kterém chcete použít nastavení:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Následující kód ukazuje příklad toho, jak přistupovat k připojovací řetězec. Nahradit &lt;ConnectionStringName > zástupný symbol s odpovídající hodnotou. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Přidat vlastní nastavení pro použití v Azure cloudové služby
Vlastní nastavení v konfiguračním souboru služby umožňují přidat název a hodnotu řetězce pro specifické konfigurace služby. Můžete pomocí tohoto nastavení můžete konfigurovat funkce v cloudové službě čtení hodnoty nastavení a použití této hodnoty pro řízení logiku z vašeho kódu. Tyto hodnoty konfigurace služby můžete změnit bez nutnosti znovu sestavovat služby balíčku nebo při spuštění cloudové služby. Kód můžete zkontrolovat oznámení při změně nastavení. Zobrazit [RoleEnvironment.Changing události](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Můžete přidat, odebrat nebo upravit vlastní nastavení pro konfiguraci služby. Jiné hodnoty může být vhodné pro tyto řetězce pro jiné služby konfigurace.

Když použijete jinou hodnotu pro každou konfiguraci služby, není nutné používat různé řetězce v cloudové službě nebo upravit kód při publikování vaší cloudové služby v Azure. Můžete použít stejný název pro řetězec v kódu a hodnota se liší v závislosti na konfiguraci služby, který vyberete při vytváření cloudové služby nebo při jejím publikování.

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, rozbalte uzel projektu. V části **role** uzel, klikněte pravým tlačítkem na roli, kterou chcete aktualizovat a v místní nabídce vyberte **vlastnosti**.

    ![Řešení Explorer Azure role kontextové nabídky](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Vyberte **nastavení** kartu.

    ![Karta nastavení](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. V **konfigurace služby** vyberte konfiguraci služby, které chcete aktualizovat.

    ![Konfigurace služby seznamu](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Chcete-li přidat vlastní nastavení, **přidat nastavení**.

    ![Přidat vlastní nastavení](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Jakmile toto nové nastavení je přidaný do seznamu, aktualizujte řádek v seznamu potřebné informace.

    ![Nové vlastní nastavení](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Název** – zadejte název nastavení.
    - **Typ** – vyberte **řetězec** z rozevíracího seznamu.
    - **Hodnota** – zadejte hodnotu daného nastavení. Můžete buď zadat hodnotu přímo do **hodnotu** buňky, nebo vyberte tři tečky (...) zadejte hodnotu v **Upravit řetězec** dialogového okna.  

1. Pokud chcete odstranit vlastní nastavení, vyberte nastavení a pak vyberte **nastavení odebrat**.

1. Ze sady Visual Studio panel nástrojů, vyberte **Uložit**.

## <a name="programmatically-access-a-custom-settings-value"></a>Programový přístup k nastavení vlastní hodnoty
 
Následující kroky ukazují, jak získat programově přístup k vlastní nastavení pomocí jazyka C#.

1. Přidejte následující direktivy using do souboru C# ve kterém chcete použít nastavení:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Následující kód ukazuje příklad toho, jak přistupovat k vlastní nastavení. Nahradit &lt;Názevnastavení > zástupný symbol s odpovídající hodnotou. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Spravovat místní úložiště pro každou instanci role
Můžete přidat místní soubor úložiště v systému pro každou instanci role. Data uložená v tom, že úložiště není přístupný ostatních instancí role, pro který jsou data uložená, nebo jiné role.  

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, rozbalte uzel projektu. V části **role** uzel, klikněte pravým tlačítkem na roli, kterou chcete aktualizovat a v místní nabídce vyberte **vlastnosti**.

    ![Řešení Explorer Azure role kontextové nabídky](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Vyberte **místní úložiště** kartu.

    ![Karta místní úložiště](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. V **konfigurace služby** seznamu, ujistěte se, že **všechny konfigurace** je vybrán jako místní úložiště nastavení platí pro všechny služby konfigurace. Jakákoli jiná hodnota výsledkem všech vstupních polí na stránce, protože se zakázalo. 

    ![Konfigurace služby seznamu](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Chcete-li přidat položku místní úložiště, **přidat místní úložiště**.

    ![Přidat místní úložiště](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. Po přidání nové položky místního úložiště do seznamu aktualizujte řádek v seznamu potřebné informace.

    ![Nová položka místní úložiště](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Název** – zadejte název, který chcete použít pro nové místní úložiště.
    - **Velikost (MB)** – zadejte velikost v MB, které potřebujete pro nové místní úložiště.
    - **Vyčištění při recyklaci role** – tuto možnost použijte k odebrání dat v nové místní úložiště při recykluje virtuální počítač pro roli.

1. Odstranit položku místní úložiště, vyberte položku a pak vyberte **odebrat místní úložiště**.

1. Ze sady Visual Studio panel nástrojů, vyberte **Uložit**.

## <a name="programmatically-accessing-local-storage"></a>Programově přístup k místnímu úložišti

Tato část ukazuje, jak programově přístup k místní úložiště pomocí jazyka C# napsáním textový soubor testu `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Zápis do textového souboru do místního úložiště

Následující kód ukazuje příklad toho, jak zápis do textového souboru do místního úložiště. Nahradit &lt;LocalStorageName > zástupný symbol s odpovídající hodnotou. 

    ```csharp
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("<LocalStorageName>");
    
    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyLocalStorageTest.txt" };
    String filePath = Path.Combine(paths);
    
    using (FileStream writeStream = File.Create(filePath))
    {
        Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
        writeStream.Write(textToWrite, 0, textToWrite.Length);
    }

    ```

### <a name="find-a-file-written-to-local-storage"></a>Najít soubor se zapisují do místního úložiště

Chcete-li zobrazit soubor vytvořený kódem v předchozí části, postupujte podle těchto kroků:
    
1.  V oznamovací oblasti Windows, klikněte pravým tlačítkem na ikonu Azure a, v místní nabídce vyberte **zobrazit Uživatelském prostředí emulátoru výpočtů**. 

    ![Zobrazit emulátoru služby výpočty v Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Vyberte webovou roli.

    ![Emulátor výpočtů v Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. Na **emulátoru Microsoft Azure Compute** nabídce vyberte možnost **nástroje** > **otevřít místní úložiště**.

    ![Položka nabídky otevřít místní úložiště](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Když se otevře okno Průzkumníka Windows, zadejte "MyLocalStorageTest.txt" do **hledání** textového pole a vyberte **Enter** má začít prohledávání. 

## <a name="next-steps"></a>Další postup
Další informace o Azure projekty v sadě Visual Studio načtením [konfigurace projektu aplikace Azure](vs-azure-tools-configuring-an-azure-project.md). Další informace o schématu služby cloudových čtení [referenční dokumentace schématu](https://msdn.microsoft.com/library/azure/dd179398).

