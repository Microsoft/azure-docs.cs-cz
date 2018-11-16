---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 4189d3a09625f211c6f9eddc7c1c5fe43ad31732
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571965"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime

V této části vytvoříte místní prostředí Integration Runtime a přidružíte ho k místnímu počítači s databází SQL Serveru. Místní prostředí Integration Runtime je komponenta, která zkopíruje data z SQL Serveru na vašem počítači do úložiště objektů blob v Azure. 

1. Vytvořte proměnnou pro název prostředí Integration Runtime. Použijte jedinečný název a poznamenejte si ho. Použijete ho později v tomto kurzu. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Vytvořte místní prostředí Integration Runtime. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Tady je ukázkový výstup:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 
3. Pokud chcete načíst stav vytvořeného prostředí Integration Runtime, spusťte následující příkaz. Potvrďte, že hodnota vlastnosti **State** je nastavena na **NeedRegistration**. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Tady je ukázkový výstup:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. Spuštěním následujícího příkazu načtěte ověřovací klíče pro registraci místního prostředí Integration Runtime ve službě Azure Data Factory v cloudu: 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Tady je ukázkový výstup:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Pro registraci místního prostředí Integration Runtime, které nainstalujete na počítači v dalších krocích, zkopírujte jeden z klíčů (bez uvozovek).  

## <a name="install-the-integration-runtime"></a>Instalace prostředí Integration Runtime
1. Pokud již na počítači máte prostředí Integration Runtime, odinstalujte ho pomocí panelu **Přidat nebo odebrat programy**. 

2. Na místním počítači s Windows [stáhněte](https://www.microsoft.com/download/details.aspx?id=39717) místní prostředí Integration Runtime. Spusťte instalaci.

3. Na stránce **Vítá vás instalace prostředí Microsoft Integration Runtime** vyberte **Další**.

4. Na stránce **Licenční smlouva s koncovým uživatelem (EULA)** přijměte podmínky a licenční smlouvu a vyberte **Další**.

5. Na stránce **Cílová složka** vyberte **Další**.

6. Na stránce **Připraveno k instalaci prostředí Microsoft Integration Runtime** vyberte **Nainstalovat**.

7. Pokud se zobrazí zpráva upozorňující na konfiguraci přechodu počítače do režimu spánku nebo hibernace, když se zrovna nepoužívá, vyberte **OK**.

8. Pokud se zobrazí stránka **Možnosti napájení**, zavřete ji a přejděte na stránku nastavení.

9. Na stránce **Dokončení instalace prostředí Microsoft Integration Runtime** vyberte **Dokončit**.

10. Na stránce **Registrace prostředí Integration Runtime (v místním prostředí)** vložte klíč, který jste uložili v předchozí části, a vyberte **Zaregistrovat**. 

    ![Registrace prostředí Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. Po úspěšném dokončení registrace místního prostředí Integration Runtime se zobrazí následující zpráva:

    ![Úspěšně zaregistrováno](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. Na stránce **Nový uzel prostředí Integration Runtime (v místním prostředí)** vyberte **Další**. 

    ![Stránka Nový uzel prostředí Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. Na stránce **Komunikační kanál intranetu** vyberte **Přeskočit**. V prostředí Integration Runtime s více uzly vyberte pro zabezpečení komunikace v rámci uzlů certifikaci TLS/SSL. 

    ![Stránka Komunikační kanál intranetu](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

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

    g. Zadejte heslo pro toto uživatelské jméno.

    h. Pokud chcete potvrdit, že se prostředí Integration Runtime může připojit k SQL Serveru, vyberte **Test**. Pokud je připojení úspěšné, zobrazí se zelená značka zaškrtnutí. Jestliže připojení není úspěšné, zobrazí se chybová zpráva. Opravte všechny problémy a ověřte, že se prostředí Integration Runtime může připojit k SQL Serveru.    

    > [!NOTE]
    > Poznamenejte si hodnoty pro typ ověřování, server, databázi, uživatele a heslo. Použijete je později v tomto kurzu. 
    
