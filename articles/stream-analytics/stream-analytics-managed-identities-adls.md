---
title: Ověření Azure Stream Analytics pro Azure Data Lake Storage Gen1
description: Tento článek popisuje, jak pomocí spravovaných identit ověřit vaši Azure Stream Analyticsovou úlohu pro Azure Data Lake Storage Gen1 výstupu.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: d7e9b1ecef9cfda804b89f0ba1beeb54d7d48b98
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020345"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities-preview"></a>Ověření Stream Analytics pro Azure Data Lake Storage Gen1 používání spravovaných identit (Preview)

Azure Stream Analytics podporuje ověřování pomocí spravované identity pomocí Azure Data Lake Storage (ADLS) Gen1 výstup. Identita je spravovaná aplikace zaregistrovaná v Azure Active Directory, která představuje danou Stream Analytics úlohu a dá se použít k ověření cílového prostředku. Spravované identity odstraňují omezení metod ověřování založených na uživatelích, například nutnost opětovného ověření z důvodu změny hesla nebo vypršení platnosti tokenu uživatele, ke kterému dochází každých 90 dní. Spravované identity navíc usnadňují automatizaci nasazení úloh Stream Analytics, které výstup Azure Data Lake Storage Gen1.

Tento článek ukazuje tři způsoby, jak povolit spravovanou identitu pro úlohu Azure Stream Analytics, která se zapisuje do Azure Data Lake Storage Gen1 prostřednictvím Azure Portal, nasazování šablon Azure Resource Manager a Azure Stream Analytics nástrojů pro Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>portál Azure

1. Začněte vytvořením nové Stream Analytics úlohy nebo otevřením existující úlohy v Azure Portal. Z řádku nabídek umístěného na levé straně obrazovky vyberte **spravovaná identita** umístěná v části **Konfigurovat**.

   ![Konfigurace Stream Analytics spravované identity](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. V okně, které se zobrazí na pravé straně, vyberte **použít spravovanou identitu přiřazenou systémem** . Klikněte na **Uložit** k instančnímu objektu pro identifikaci úlohy Stream Analytics v Azure Active Directory. Životní cyklus nově vytvořené identity se bude spravovat přes Azure. Po odstranění úlohy Stream Analytics se služba Azure automaticky odstraní přidružená identita (tj. instanční objekt).

   Po uložení konfigurace se ID objektu (OID) instančního objektu zobrazí jako ID objektu zabezpečení, jak je znázorněno níže:

   ![ID instančního Stream Analytics služby](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Instanční objekt má stejný název jako Stream Analytics úloha. Pokud je název úlohy například **MyASAJob**, název vytvořeného objektu služby je také **MyASAJob**.

3. V okně Vlastnosti výstupu jímky výstupního ADLS Gen1 klikněte na rozevírací seznam režim ověřování a vyberte * * spravovaná identita * *.

4. Vyplňte zbytek vlastností. Další informace o vytváření výstupu ADLS najdete v tématu [Vytvoření výstupu Data Lake Store s využitím Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Jakmile budete hotovi, klikněte na **Uložit**.

   ![Konfigurace Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Přejděte na stránku Přehled ADLS Gen1 a klikněte na **Průzkumník dat**.

   ![Konfigurace Data Lake Storage – přehled](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. V podokně Průzkumník dat vyberte **přístup** a v podokně přístupu klikněte na **Přidat** .

   ![Konfigurace přístupu Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Do textového pole v podokně **Vybrat uživatele nebo skupinu** zadejte název instančního objektu. Pamatujte, že název instančního objektu je také názvem odpovídající úlohy Stream Analytics. Když začnete psát hlavní název, zobrazí se pod textovým polem. Zvolte požadovaný hlavní název služby a klikněte na **Vybrat**.

   ![Vybrat hlavní název služby](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. V podokně **oprávnění** Ověřte oprávnění **zapisovat** a **Spustit** a přiřaďte je k **této složce a všem podřízeným** položkám. Pak klikněte na **OK**.

   ![Vyberte oprávnění zapisovat a provést.](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Instanční objekt je uveden v části **přiřazená oprávnění** v podokně **přístupu** , jak je uvedeno níže. Nyní se můžete vrátit a spustit úlohu Stream Analytics.

   ![Seznam přístupu Stream Analytics na portálu](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Další informace o Data Lake Storage Gen1 oprávnění systému souborů najdete v tématu [Access Control v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics nástroje pro Visual Studio

1. V JobConfig.jsna serveru nastavte **použít identitu přiřazenou systémem** na **hodnotu true**.

   ![Stream Analytics spravované identity konfigurace úlohy](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. V okně Vlastnosti výstupu jímky výstupního ADLS Gen1 klikněte na rozevírací seznam režim ověřování a vyberte * * spravovaná identita * *.

   ![ADLS výstup spravovaných identit](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Vyplňte zbytek vlastností a klikněte na **Uložit**.

4. V editoru dotazů klikněte na **Odeslat do Azure** .

   Po odeslání úlohy nástroje dělají dvě věci:

   * Automaticky vytvoří instanční objekt pro identitu úlohy Stream Analytics v Azure Active Directory. Životní cyklus nově vytvořené identity se bude spravovat přes Azure. Po odstranění úlohy Stream Analytics se služba Azure automaticky odstraní přidružená identita (tj. instanční objekt).

   * Automaticky nastavit oprávnění **zapisovat** a **spustit** pro cestu adls Gen1 předponu použitou v úloze a přiřadit ji k této složce a všem podřízeným položkám.

5. Šablony Správce prostředků s následující vlastností můžete vygenerovat pomocí [Stream Analytics CI.CD balíčku NuGet](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) verze 1.5.0 nebo vyšší na sestavovacím počítači (mimo Visual Studio). Použijte postup nasazení šablony Správce prostředků v následující části k získání instančního objektu a udělení přístupu k instančnímu objektu prostřednictvím PowerShellu.

## <a name="resource-manager-template-deployment"></a>Nasazení šablony Správce prostředků

1. Pomocí spravované identity můžete vytvořit prostředek *Microsoft. StreamAnalytics/streamingjobs* , a to tak, že do oddílu prostředků v šabloně správce prostředků zadáte následující vlastnost:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Tato vlastnost oznamuje Azure Resource Manager, že má vytvořit a spravovat identitu pro vaši Azure Stream Analytics úlohu.

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

   Poznamenejte si ID objektu zabezpečení z odpovědi úlohy, abyste udělili přístup k požadovanému prostředku ADLS.

   **ID tenanta** je id klienta Azure Active Directory, ve kterém je objekt služby vytvořený. Instanční objekt se vytvoří v tenantovi Azure, který je důvěryhodný pro předplatné.

   **Typ** určuje typ spravované identity, jak je vysvětleno v typech spravovaných identit. Podporován je pouze typ přiřazený systémem.

2. Poskytněte přístup k instančnímu objektu pomocí PowerShellu. K získání přístupu k instančnímu objektu prostřednictvím PowerShellu spusťte následující příkaz:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** je ID objektu instančního objektu a je uveden na obrazovce portálu po vytvoření instančního objektu. Pokud jste vytvořili úlohu pomocí nasazení šablony Správce prostředků, ID objektu je uvedeno v vlastnosti identita odpovědi úlohy.

   **Příklad**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Další informace o výše uvedeném příkazu PowerShellu najdete v dokumentaci k rutině [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) .

## <a name="remove-managed-identity"></a>Odebrat spravovanou identitu

Spravovaná identita vytvořená pro Stream Analytics úlohu se odstraní jenom v případě, že se úloha odstraní. Neexistuje způsob, jak odstranit spravovanou identitu, aniž byste úlohu odstranili. Pokud už nechcete používat spravovanou identitu, můžete pro výstup změnit metodu ověřování. Spravovaná identita bude i nadále existovat, dokud se úloha neodstraní, a použije se, pokud se rozhodnete znovu použít spravované ověřování identity.

## <a name="limitations"></a>Omezení
Tato funkce nepodporuje následující:

1. **Přístup s více klienty**: instanční objekt vytvořený pro danou Stream Analytics úlohu se bude nacházet v klientovi Azure Active Directory, na kterém byla úloha vytvořena, a nelze ho použít pro prostředek, který se nachází v jiném Azure Active Directory tenantovi. Proto můžete použít jenom MSI na ADLSch prostředcích 1. generace, které jsou v rámci stejného Azure Active Directory tenanta jako vaše úloha Azure Stream Analytics. 

2. **[Identita přiřazená uživatelem](../active-directory/managed-identities-azure-resources/overview.md)**: není podporována. To znamená, že uživatel nemůže zadat vlastní instanční objekt, který bude používat jejich Stream Analytics úlohy. Instanční objekt je generovaný Azure Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Vytvoření výstupu Data Lake Store s využitím Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testování Stream Analytics dotazů v místním prostředí se sadou Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Místní testování živých dat pomocí Azure Stream Analyticsch nástrojů pro Visual Studio](stream-analytics-live-data-local-testing.md) 
