---
title: Ověření Azure Stream Analytics pro Azure Data Lake Storage Gen1
description: Tento článek popisuje způsob použití spravované identity k ověření vaší úlohy Azure Stream Analytics k Azure Data Lake Storage Gen1 výstup.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379192"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Ověření Stream Analytics pro Azure Data Lake Storage Gen1 pomocí spravovaných identit

Azure Stream Analytics podporuje spravovanou identitu ověřování pomocí Azure Data Lake Storage (ADLS) Gen1 výstup. Identita je spravovaná aplikace v Azure Active Directory, který představuje dané úlohy Stream Analytics a slouží k ověřování na cílový prostředek. Spravované identity eliminovat omezení metody ověřování založené na uživatelích, jako by bylo nutné donutit z důvodu změny hesla nebo uživatel token platnosti, ke kterým dochází vždy po 90 dnech. Kromě toho spravované identity usnadňující automatizaci nasazení úlohy Stream Analytics, která výstup do Azure Data Lake Storage Gen1.

Tento článek ukazuje tři způsoby, jak povolit spravovanou identitu pro úlohu Azure Stream Analytics, která se zapisuje do Azure Data Lake Storage Gen1 prostřednictvím Azure Portal, nasazování šablon Azure Resource Manager a Azure Stream Analytics nástrojů pro Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Začněte tím, že vytvoříte novou úlohu Stream Analytics nebo tak, že otevřete existující projekt na webu Azure portal. Z řádku nabídek umístěného na levé straně obrazovky vyberte **spravovaná identita** umístěná v části **Konfigurovat**.

   ![Konfigurace Stream Analytics spravované identity](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. V okně, které se zobrazí na pravé straně, vyberte **použít spravovanou identitu přiřazenou systémem** . Klikněte na **Uložit** k instančnímu objektu pro identifikaci úlohy Stream Analytics v Azure Active Directory. Azure bude spravovat životní cyklus nově vytvořený identity. Při odstranění úlohy Stream Analytics přidružený identity (to znamená, instanční objekt) Azure automaticky odstraní.

   Při uložení konfigurace ID objektu (OID) objektu služby, které je uvedené jako ID objektu zabezpečení, jak je znázorněno níže:

   ![ID instančního objektu Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Instanční objekt služby má stejný název jako úloha Stream Analytics. Pokud je název úlohy například **MyASAJob**, název vytvořeného objektu služby je také **MyASAJob**.

3. V okně Vlastnosti výstupu jímky výstupního ADLS Gen1 klikněte na rozevírací seznam režim ověřování a vyberte * * spravovaná identita * *.

4. Vyplňte zbývající vlastnosti nástroje. Další informace o vytváření výstupu ADLS najdete v tématu [Vytvoření výstupu Data Lake Store s využitím Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Po dokončení klikněte na **Uložit**.

   ![Konfigurace úložiště Azure Data Lake](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Přejděte na stránku Přehled ADLS Gen1 a klikněte na **Průzkumník dat**.

   ![Přehled služby Data Lake Storage konfigurace](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. V podokně Průzkumník dat vyberte **přístup** a v podokně přístupu klikněte na **Přidat** .

   ![Konfigurace přístupu k Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Do textového pole v podokně **Vybrat uživatele nebo skupinu** zadejte název instančního objektu. Mějte na paměti, že název instančního objektu je také název odpovídající úlohy Stream Analytics. Během zadávání hlavní název se zobrazí pod textovým polem. Zvolte požadovaný hlavní název služby a klikněte na **Vybrat**.

   ![Vyberte hlavní název služby](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. V podokně **oprávnění** Ověřte oprávnění **zapisovat** a **Spustit** a přiřaďte je k **této složce a všem podřízeným**položkám. Pak klikněte na **OK**.

   ![Vyberte zápisu a spouštěcích oprávnění](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Instanční objekt je uveden v části **přiřazená oprávnění** v podokně **přístupu** , jak je uvedeno níže. Teď můžete přejít zpět a spuštění vaší úlohy Stream Analytics.

   ![Stream Analytics přístup k seznamu v portálu](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Další informace o Data Lake Storage Gen1 oprávnění systému souborů najdete v tématu [Access Control v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics nástroje pro Visual Studio

1. V souboru JobConfig. JSON nastavte **použít identitu přiřazenou systémem** na **hodnotu true**.

   ![Stream Analytics spravované identity konfigurace úlohy](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. V okně Vlastnosti výstupu jímky výstupního ADLS Gen1 klikněte na rozevírací seznam režim ověřování a vyberte * * spravovaná identita * *.

   ![ADLS výstup spravovaných identit](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Vyplňte zbytek vlastností a klikněte na **Uložit**.

4. V editoru dotazů klikněte na **Odeslat do Azure** .

   Po odeslání úlohy nástroje dělají dvě věci:

   * Automaticky vytvoří instanční objekt pro identitu úlohy Stream Analytics v Azure Active Directory. Azure bude spravovat životní cyklus nově vytvořený identity. Při odstranění úlohy Stream Analytics přidružený identity (to znamená, instanční objekt) Azure automaticky odstraní.

   * Automaticky nastavit oprávnění **zapisovat** a **spustit** pro cestu adls Gen1 předponu použitou v úloze a přiřadit ji k této složce a všem podřízeným položkám.

5. Pomocí Stream Analytics CI můžete vygenerovat šablony Správce prostředků s následující vlastností [. Balíček NuGet CD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) verze 1.5.0 nebo novější na sestavovacím počítači (mimo Visual Studio). Použijte postup nasazení šablony Správce prostředků v následující části k získání instančního objektu a udělení přístupu k instančnímu objektu prostřednictvím PowerShellu.

## <a name="resource-manager-template-deployment"></a>Nasazení šablony Resource Manageru

1. Pomocí spravované identity můžete vytvořit prostředek *Microsoft. StreamAnalytics/streamingjobs* , a to tak, že do oddílu prostředků v šabloně správce prostředků zadáte následující vlastnost:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Tato vlastnost říká Azure Resource Manageru můžete vytvořit a spravovat identitu pro vaši úlohu Azure Stream Analytics.

   **Ukázková úloha**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Ukázková odpověď úlohy**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Poznamenejte si ID objektu zabezpečení z odpovědi úlohy udělit přístup k požadované prostředků ADLS.

   **ID tenanta** je id klienta Azure Active Directory, ve kterém je objekt služby vytvořený. Instanční objekt se vytvoří v Azure, která je důvěryhodná pro předplatné.

   **Typ** určuje typ spravované identity, jak je vysvětleno v typech spravovaných identit. Je podporován pouze typ systém přiřadil.

2. Poskytnout přístup k objektu zabezpečení pomocí prostředí PowerShell. Udělit přístup ke službě instančního objektu pomocí Powershellu, spusťte následující příkaz:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** je ID objektu instančního objektu a je uveden na obrazovce portálu po vytvoření instančního objektu. Pokud jste vytvořili úlohu pomocí nasazení šablony Resource Manageru, ID objektu je uvedeno ve vlastnosti identita třídy odpověď úlohy.

   **Příklad**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Další informace o výše uvedeném příkazu PowerShellu najdete v dokumentaci k rutině [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) .

## <a name="limitations"></a>Omezení
Tato funkce nepodporuje následující:

1. **Přístup s více klienty**: instanční objekt vytvořený pro danou Stream Analytics úlohu se bude nacházet v klientovi Azure Active Directory, na kterém byla úloha vytvořena, a nelze ho použít pro prostředek, který se nachází v jiném Azure Active Directory tenantovi. Proto můžete použít jenom MSI na ADLSch prostředcích 1. generace, které jsou v rámci stejného Azure Active Directory tenanta jako vaše úloha Azure Stream Analytics. 

2. **[Identita přiřazená uživatelem](../active-directory/managed-identities-azure-resources/overview.md)** : není podporována. To znamená, že uživatel nemůže zadat vlastní instanční objekt, který bude používat jejich Stream Analytics úlohy. Instanční objekt je generovaný Azure Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Vytvoření výstupu Data Lake Store s využitím Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testování Stream Analytics dotazů v místním prostředí se sadou Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Místní testování živých dat pomocí Azure Stream Analyticsch nástrojů pro Visual Studio](stream-analytics-live-data-local-testing.md) 
