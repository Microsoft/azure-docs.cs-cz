---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 20397339c1675686f2a670dc60899bfd4bd4c3ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050273"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime

V této části vytvoříte místní prostředí Integration Runtime a přidružíte ho k místnímu počítači s databází SQL Serveru. Místní prostředí Integration runtime je komponenta, která kopíruje data z SQL Server ve vašem počítači do Azure SQL Database. 

1. Vytvořte proměnnou pro název prostředí Integration Runtime. Použijte jedinečný název a poznamenejte si ho. Použijete ho později v tomto kurzu. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Vytvořte místní prostředí Integration Runtime. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Zde je ukázkový výstup:

   ```json
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. Pokud chcete načíst stav vytvořeného prostředí Integration Runtime, spusťte následující příkaz. Potvrďte, že hodnota vlastnosti **State** je nastavena na **NeedRegistration**. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Zde je ukázkový výstup:

   ```json  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. Spuštěním následujícího příkazu načtěte ověřovací klíče pro registraci místního prostředí Integration Runtime ve službě Azure Data Factory v cloudu: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Zde je ukázkový výstup:

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Pro registraci místního prostředí Integration Runtime, které nainstalujete na počítači v dalších krocích, zkopírujte jeden z klíčů (bez uvozovek).  

## <a name="install-the-integration-runtime-tool"></a>Instalace nástroje Integration runtime

1. Pokud již na počítači máte prostředí Integration Runtime, odinstalujte ho pomocí panelu **Přidat nebo odebrat programy**. 

2. Na místním počítači s Windows [stáhněte](https://www.microsoft.com/download/details.aspx?id=39717) místní prostředí Integration Runtime. Spusťte instalaci.

3. Na stránce **Vítá vás instalace prostředí Microsoft Integration Runtime** vyberte **Další**.

4. Na stránce **Licenční smlouva s koncovým uživatelem (EULA)** přijměte podmínky a licenční smlouvu a vyberte **Další**.

5. Na stránce **Cílová složka** vyberte **Další**.

6. Na stránce **Připraveno k instalaci prostředí Microsoft Integration Runtime** vyberte **Nainstalovat**.

7. Na stránce **Dokončení instalace prostředí Microsoft Integration Runtime** vyberte **Dokončit**.

8. Na stránce **Registrace prostředí Integration Runtime (v místním prostředí)** vložte klíč, který jste uložili v předchozí části, a vyberte **Zaregistrovat**. 

    ![Registrace prostředí Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. Na stránce **nový uzel Integration runtime (v místním prostředí)** vyberte **Dokončit**. 

10. Po úspěšném dokončení registrace místního prostředí Integration Runtime se zobrazí následující zpráva:

    ![Úspěšně zaregistrováno](media/data-factory-create-install-integration-runtime/registered-successfully.png)

14. Na stránce **Registrace prostředí Integration Runtime (v místním prostředí)** vyberte **Spustit Správce konfigurace**.

15. Jakmile se uzel připojí ke cloudové službě, zobrazí se následující stránka:

    ![Stránka Uzel je připojen](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Teď otestujte připojení k databázi SQL Serveru.

    ![Karta Diagnostika](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. Na stránce **Správce konfigurace** přejděte na kartu **Diagnostika**.

    b. Jako typ zdroje dat vyberte **SqlServer**.

    c. Zadejte název serveru.

    d. Zadejte název databáze.

    e. Vyberte režim ověřování.

    f. Zadejte uživatelské jméno.

    například Zadejte heslo, které je přidruženo k pro uživatelské jméno.

    h. Pokud chcete potvrdit, že se prostředí Integration Runtime může připojit k SQL Serveru, vyberte **Test**. Pokud je připojení úspěšné, zobrazí se zelená značka zaškrtnutí. Jestliže připojení není úspěšné, zobrazí se chybová zpráva. Opravte všechny problémy a ověřte, že se prostředí Integration Runtime může připojit k SQL Serveru.    

    > [!NOTE]
    > Poznamenejte si hodnoty pro typ ověřování, server, databázi, uživatele a heslo. Použijete je později v tomto kurzu.
