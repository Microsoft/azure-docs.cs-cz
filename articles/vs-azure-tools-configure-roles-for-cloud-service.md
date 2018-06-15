---
title: Konfigurace role pro cloudové služby Azure pomocí sady Visual Studio | Microsoft Docs
description: Zjistěte, jak nastavit a konfigurovat role pro cloudové služby Azure pomocí sady Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 09e6c3a9c27342ef27d49674d62ccf74d70d2e0f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798722"
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Konfigurace role Azure cloud service pomocí sady Visual Studio
Cloudové služby Azure může mít jeden nebo více pracovních nebo webové role. Pro každou roli musíte definovat nastavení dané role a taky nakonfigurovat, jak tato role běží. Další informace o rolích v cloudové služby najdete v tématu video [Úvod do Azure Cloud Services](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

Informace pro cloudové služby jsou uloženy v následujících souborech:

- **ServiceDefinition.csdef** -definiční soubor služby definuje nastavení běhového prostředí pro cloudové služby, včetně toho, jaké role jsou požadovány, koncových bodů a velikost virtuálního počítače. Žádná data uložená v `ServiceDefinition.csdef` jde změnit, pokud vaše role běží.
- **Souboru ServiceConfiguration.cscfg** – konfigurační soubor služby konfiguruje, jak velký počet instancí role spouštějí a hodnoty nastavení definované pro roli. Data uložená v `ServiceConfiguration.cscfg` lze změnit, když vaše role běží.

Pokud chcete uložit různé hodnoty pro nastavení, které řídí, jak role běží, můžete definovat více konfigurace služby. Konfigurace různé služby můžete použít pro jednotlivá prostředí nasazení. Například můžete nastavit připojovací řetězec účet úložiště můžete použít emulátor místního úložiště Azure v místní službě konfigurace a vytvořte jinou konfiguraci služby pro použití úložiště Azure v cloudu.

Při vytváření cloudové služby Azure v sadě Visual Studio, jsou automaticky vytvořen a přidán do projektu Azure dvě konfigurace služby:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Konfigurace Azure cloud service
Cloudové služby Azure v Průzkumníku řešení v sadě Visual Studio, můžete nakonfigurovat, jak je znázorněno v následujícím postupu:

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **vlastnosti**.
   
    ![Místní nabídku projektu Průzkumník řešení](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. Na stránce vlastností projektu, vyberte **vývoj** kartě. 

    ![Stránka vlastností projektu - karta vývoj](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. V **konfigurace služby** seznamu, vyberte název konfigurace služby, který chcete upravit. (Pokud chcete změnit konfiguraci služby pro tuto roli, vyberte **všechny konfigurace**.)
   
    > [!IMPORTANT]
    > Pokud si zvolíte specifické služby konfigurace, některé vlastnosti jsou zakázané, protože jejich lze nastavit pouze pro všechny konfigurace. Chcete-li upravit tyto vlastnosti, je nutné vybrat **všechny konfigurace**.
    > 
    > 
   
    ![Seznam konfigurace služby pro cloudové služby Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>Změnit počet instancí role
Pokud chcete zlepšit výkon cloudové služby, můžete změnit počet instancí role, které běží na základě počtu uživatelů nebo zatížení pro konkrétní roli. Samostatný virtuální počítač se vytvoří pro každou instanci role, při spuštění cloudové služby v Azure. Tato akce ovlivní fakturace pro nasazení pro tuto cloudovou službu. Další informace o fakturaci, viz [porozumět vaší faktuře pro Microsoft Azure](billing/billing-understand-your-bill.md).

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku**, rozbalte uzel projektu. V části **role** uzel, klikněte pravým tlačítkem na roli, kterou chcete aktualizovat a v místní nabídce vyberte **vlastnosti**.

    ![Řešení Explorer Azure role kontextové nabídky](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Vyberte **konfigurace** kartě.

    ![Na kartě Konfigurace](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. V **konfigurace služby** vyberte konfiguraci služby, který chcete aktualizovat.
   
    ![Konfigurace služby seznamu](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. V **Instance počet** textové pole, zadejte počet instancí, které chcete spustit pro tuto roli. Každá instance běží na samostatný virtuální počítač při publikování cloudové služby Azure.

    ![Aktualizuje se počet instancí](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. Ze sady Visual Studio panelu nástrojů vyberte **Uložit**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Spravovat připojovací řetězce pro účty úložiště
Můžete přidat, odebrat nebo upravit připojovací řetězce pro vaše konfigurace služby. Například můžete místní připojovací řetězec pro konfiguraci místní služby, který má hodnotu `UseDevelopmentStorage=true`. Můžete také nakonfigurovat služby konfigurace cloudu, který používá účet úložiště v Azure.

> [!WARNING]
> Když zadáte údaje klíče účtu úložiště Azure pro připojovací řetězce k účtu úložiště, tyto informace jsou uloženy místně v konfiguračním souboru služby. Tyto informace nejsou aktuálně uloženy jako šifrovaný text.
> 
> 

Pomocí jinou hodnotu pro každou konfiguraci služby není muset použít jiné připojovací řetězce v rámci cloudové služby nebo upravit kód při publikování cloudové služby Azure. Můžete použít stejný název pro připojovací řetězec v kódu a hodnota se liší v závislosti na konfiguraci služby, kterou vyberete při sestavování cloudové služby, nebo při jeho publikování.

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku**, rozbalte uzel projektu. V části **role** uzel, klikněte pravým tlačítkem na roli, kterou chcete aktualizovat a v místní nabídce vyberte **vlastnosti**.

    ![Řešení Explorer Azure role kontextové nabídky](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Vyberte **nastavení** kartě.

    ![Karta nastavení](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. V **konfigurace služby** vyberte konfiguraci služby, který chcete aktualizovat.

    ![Konfigurace služby](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Chcete-li přidat připojovací řetězec, vyberte **přidat nastavení**.

    ![Přidat připojovací řetězec](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Jakmile se nové nastavení byl přidán do seznamu, aktualizujte řádek v seznamu nezbytné informace.

    ![Nový připojovací řetězec](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Název** – zadejte název, který chcete použít pro připojovací řetězec.
    - **Typ** – vyberte **připojovací řetězec** z rozevíracího seznamu.
    - **Hodnota** – můžete buď zadat připojovací řetězec přímo do **hodnotu** buňky, nebo vyberte se třemi tečkami (...) pro práci v **vytvoření připojovacího řetězce úložiště** dialogové okno.  

1. V **vytvoření připojovacího řetězce úložiště** dialogovém okně, vyberte možnost pro **připojit pomocí**. Postupujte podle pokynů pro možnosti, kterou jste vybrali:

    - **Emulátor úložiště Microsoft Azure** – Pokud vyberete tuto možnost, zbývající nastavení v dialogovém okně jsou zakázány, protože se vztahují pouze na Azure. Vyberte **OK**.
    - **Vaše předplatné** – Pokud vyberete tuto možnost, pomocí rozevíracího seznamu vyberte a přihlaste se k účtu Microsoft nebo přidání účtu Microsoft. Vyberte účet předplatného a úložiště Azure. Vyberte **OK**.
    - **Ručně zadali přihlašovací údaje** – zadejte název účtu úložiště a druhý nebo primární klíč. Vyberte možnost pro **připojení** (HTTPS se doporučuje pro většinu scénářů). Vyberte **OK**.

1. Chcete-li odstranit připojovací řetězec, vyberte připojovací řetězec a pak vyberte **odebrat nastavení**.

1. Ze sady Visual Studio panelu nástrojů vyberte **Uložit**.

## <a name="programmatically-access-a-connection-string"></a>Programový přístup připojovací řetězec

Následující kroky ukazují, jak k programovému přístupu ke připojovací řetězec pomocí jazyka C#.

1. Přidejte následující direktivy using do souboru C# kam používá nastavení:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Následující kód ukazuje příklad přístup připojovací řetězec. Nahraďte &lt;ConnectionStringName > zástupný symbol na odpovídající hodnotu. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Přidat vlastní nastavení pro použití v cloudové služby Azure
Vlastní nastavení v konfiguračním souboru služby můžete přidat název a hodnotu řetězce pro konfiguraci konkrétní služby. Můžete zvolit pomocí tohoto nastavení můžete nakonfigurovat funkce v rámci cloudové služby tak, že čtení hodnoty nastavení a použití této hodnoty pro řízení logiku v kódu. Tyto hodnoty konfigurace služby můžete změnit bez nutnosti znovu vytvořit balíček vaší služby nebo při spuštění cloudové služby. Kódu můžete zkontrolovat pro oznámení, když se změní nastavení. V tématu [RoleEnvironment.Changing událostí](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Můžete přidat, odebrat nebo upravit vlastní nastavení pro vaše konfigurace služby. Můžete chtít různé hodnoty pro tyto řetězce pro různé služby konfigurace.

Pomocí jinou hodnotu pro každou konfiguraci služby není muset použít jiné řetězce v rámci cloudové služby nebo upravit kód při publikování cloudové služby Azure. Můžete použít stejný název pro řetězec v kódu a hodnota se liší v závislosti na konfiguraci služby, kterou vyberete při sestavování cloudové služby, nebo při jeho publikování.

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku**, rozbalte uzel projektu. V části **role** uzel, klikněte pravým tlačítkem na roli, kterou chcete aktualizovat a v místní nabídce vyberte **vlastnosti**.

    ![Řešení Explorer Azure role kontextové nabídky](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Vyberte **nastavení** kartě.

    ![Karta nastavení](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. V **konfigurace služby** vyberte konfiguraci služby, který chcete aktualizovat.

    ![Konfigurace služby seznamu](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Chcete-li přidat vlastní nastavení, vyberte **přidat nastavení**.

    ![Přidat vlastní nastavení](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Jakmile se nové nastavení byl přidán do seznamu, aktualizujte řádek v seznamu nezbytné informace.

    ![Nové vlastní nastavení](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Název** – zadejte název nastavení.
    - **Typ** – vyberte **řetězec** z rozevíracího seznamu.
    - **Hodnota** – zadejte hodnotu nastavení. Můžete buď zadat hodnotu přímo do **hodnotu** buňky, nebo vyberte se třemi tečkami (...) zadejte hodnotu v **Upravit řetězec** dialogové okno.  

1. Chcete-li odstranit vlastní nastavení, vyberte nastavení a pak vyberte **odebrat nastavení**.

1. Ze sady Visual Studio panelu nástrojů vyberte **Uložit**.

## <a name="programmatically-access-a-custom-settings-value"></a>Programový přístup hodnoty vlastní nastavení
 
Následující kroky ukazují, jak k programovému přístupu ke vlastního nastavení pomocí jazyka C#.

1. Přidejte následující direktivy using do souboru C# kam používá nastavení:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Následující kód ukazuje příklad toho, jak pro přístup k vlastní nastavení. Nahraďte &lt;SettingName > zástupný symbol na odpovídající hodnotu. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Spravovat místní úložiště pro každou instanci role
Můžete přidat úložiště systému místní soubor pro každou instanci role. Data uložená na tomto úložiště není přístupné ostatní instance role jsou data uložena, nebo jiné role.  

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku**, rozbalte uzel projektu. V části **role** uzel, klikněte pravým tlačítkem na roli, kterou chcete aktualizovat a v místní nabídce vyberte **vlastnosti**.

    ![Řešení Explorer Azure role kontextové nabídky](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Vyberte **místní úložiště** kartě.

    ![Karta místní úložiště](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. V **konfigurace služby** seznamu, ujistěte se, že **všechny konfigurace** je vybrán jako místní úložiště nastavení se použije pro všechny konfigurace služby. Jakákoli jiná hodnota výsledkem všech vstupních polí na stránce bude zakázán. 

    ![Konfigurace služby seznamu](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Přidání položky místní úložiště, vyberte **přidat místní úložiště**.

    ![Přidejte místní úložiště](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. Jakmile nový záznam místní úložiště byl přidán do seznamu, aktualizujte řádek v seznamu nezbytné informace.

    ![Nová položka místní úložiště](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Název** – zadejte název, který chcete použít pro nové místní úložiště.
    - **Velikost (MB)** – zadejte velikost v MB, kterou potřebujete pro nové místní úložiště.
    - **Čištění na role recyklaci** – vyberte tuto možnost, chcete-li odstranit data v nové místní úložiště po recyklaci virtuálního počítače pro roli.

1. Chcete-li odstranit položku místní úložiště, vyberte položku a pak vyberte **odebrat místní úložiště**.

1. Ze sady Visual Studio panelu nástrojů vyberte **Uložit**.

## <a name="programmatically-accessing-local-storage"></a>Prostřednictvím kódu programu přístup k místnímu úložišti

V této části ukazuje, jak se prostřednictvím kódu programu přístup k místnímu úložišti pomocí jazyka C# vytvořením textového souboru testovací `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Zápis do místního úložiště do textového souboru

Následující kód ukazuje příklad zápis do textového souboru do místního úložiště. Nahraďte &lt;LocalStorageName > zástupný symbol na odpovídající hodnotu. 

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

### <a name="find-a-file-written-to-local-storage"></a>Vyhledat soubor zapisovat do místního úložiště

Chcete-li zobrazit soubor vytvořený pomocí kódu v předchozí části, postupujte takto:
    
1.  V oznamovací oblasti systému Windows klikněte pravým tlačítkem na ikonu Azure a, v místní nabídce vyberte **zobrazit uživatelské prostředí emulátoru výpočtů**. 

    ![Zobrazit emulátoru služby výpočty Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Vyberte webovou roli.

    ![Emulátoru služby výpočty Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. Na **Microsoft Azure výpočetní emulátor** nabídce vyberte možnost **nástroje** > **otevřete místní úložiště**.

    ![Položky nabídky otevřete místního úložiště.](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Když se otevře okno Průzkumníka Windows, zadejte "MyLocalStorageTest.txt'' do **vyhledávání** textového pole a vyberte **Enter** má začít prohledávání. 

## <a name="next-steps"></a>Další postup
Další informace o Azure projekty v sadě Visual Studio načtením [konfigurace projektu Azure](vs-azure-tools-configuring-an-azure-project.md). Další informace o schématu cloudové služby načtením [– odkaz schématu](https://msdn.microsoft.com/library/azure/dd179398).

