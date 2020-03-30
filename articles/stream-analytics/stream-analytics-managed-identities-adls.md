---
title: Ověření Azure Stream Analytics do Azure Data Lake Storage Gen1
description: Tento článek popisuje, jak používat spravované identity k ověření úlohy Azure Stream Analytics na výstupu Azure Data Lake Storage Gen1.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254375"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Ověření Stream Analytics do Azure Data Lake Storage Gen1 pomocí spravovaných identit

Azure Stream Analytics podporuje ověřování spravované identity s výstupem Gen1 (Azure Data Lake Storage) (ADLS). Identita je spravovaná aplikace registrovaná ve službě Azure Active Directory, která představuje danou úlohu Stream Analytics a kterou lze použít k ověření na cílový prostředek. Spravované identity eliminují omezení metod ověřování založených na uživateli, jako je nutnost opětovného ověření z důvodu změn hesla nebo vypršení platnosti tokenů uživatelů, ke kterým dochází každých 90 dní. Spravované identity navíc pomáhají s automatizací úloh Stream Analytics, které se nastoují do Azure Data Lake Storage Gen1.

Tento článek ukazuje tři způsoby, jak povolit spravovanou identitu pro úlohu Azure Stream Analytics, která se napojuje na gen1 úložiště datového jezera Azure prostřednictvím portálu Azure, nasazení šablony Azure Resource Manager a nástrojů Azure Stream Analytics pro Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>portál Azure

1. Začněte vytvořením nové úlohy Stream Analytics nebo otevřením existující úlohy na webu Azure Portal. Na řádku nabídek umístěném na levé straně obrazovky vyberte **Položku Spravovaná identita** umístěná v části **Konfigurovat**.

   ![Konfigurace spravované identity Služby Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. V okně, které se zobrazí vpravo, vyberte **Použít spravovanou identitu přiřazenou systémem.** Klikněte na **Uložit** do instančního objektu pro identitu úlohy Stream Analytics ve službě Azure Active Directory. Životní cyklus nově vytvořené identity bude spravovat Azure. Když je úloha Stream Analytics odstraněna, přidružená identita (to znamená instanční objekt) se automaticky odstraní Azure.

   Při uložení konfigurace je ID objektu (OID) instančního objektu uveden jako ID jistiny, jak je znázorněno níže:

   ![ID instančního objektu služby Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Instanční objekt má stejný název jako úloha Stream Analytics. Pokud je například název úlohy **MyASAJob**, je název vytvořeného instančního objektu také **MyASAJob**.

3. V okně výstupních vlastností jímky adls gen1 klikněte na rozevírací seznam Režim ověřování a vyberte **Spravovanou identitu **.

4. Vyplňte zbytek vlastností. Další informace o vytváření výstupu ADLS najdete v [tématu Vytvoření výstupu úložiště datového jezera pomocí analýzy datového proudu](../data-lake-store/data-lake-store-stream-analytics.md). Jakmile budete hotovi, klikněte na **Uložit**.

   ![Konfigurace úložiště datových jezer Azure](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Přejděte na stránku Přehled gen1 ADLS a klikněte na **Průzkumník dat**.

   ![Konfigurace přehledu úložiště datového jezera](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. V podokně Průzkumník dat vyberte **Access** a v podokně Access klikněte na **Přidat.**

   ![Konfigurace přístupu k úložišti datového jezera](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Do textového pole v podokně **Vybrat uživatele nebo skupiny** zadejte název instančního objektu. Nezapomeňte, že název instančního objektu je také název odpovídající úlohy Stream Analytics. Jakmile začnete psát hlavní název, zobrazí se pod textovým polem. Zvolte požadovaný název hlavního serveru služby a klepněte na tlačítko **Vybrat**.

   ![Výběr názvu hlavního povinného servisu](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. V podokně **Oprávnění** zaškrtněte oprávnění **k zápisu** a **spuštění** a přiřaďte je **této složce a všem podřízeným položkám**. Potom klepněte na tlačítko **Ok**.

   ![Výběr oprávnění k zápisu a spuštění](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Instanční objekt je uveden v části **Přiřazená oprávnění** v podokně **Access,** jak je znázorněno níže. Nyní se můžete vrátit a spustit úlohu Služby Stream Analytics.

   ![Seznam přístupu k Stream Analytics na portálu](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Další informace o oprávněních systému souborů Data Lake Storage Gen1 najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Nástroje Analýzy streamu pro Visual Studio

1. V jobconfig.json nastavte **použít systémově přiřazenou identitu** na **hodnotu True**.

   ![Spravované identity úlohy Stream Analytics](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. V okně výstupních vlastností jímky adls gen1 klikněte na rozevírací seznam Režim ověřování a vyberte **Spravovanou identitu **.

   ![Spravované identity výstupu ADLS](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Vyplňte zbývající vlastnosti a klepněte na **uložit**.

4. V editoru dotazů klikněte na **Odeslat do Azure.**

   Při odeslání úlohy nástroje dělají dvě věci:

   * Automaticky vytvoří instanční objekt pro identitu úlohy Stream Analytics ve službě Azure Active Directory. Životní cyklus nově vytvořené identity bude spravovat Azure. Když je úloha Stream Analytics odstraněna, přidružená identita (to znamená instanční objekt) se automaticky odstraní Azure.

   * Automaticky nastavte oprávnění **k zápisu** a **spouštění** pro cestu předpony ADLS Gen1 použitou v úloze a přiřaďte ji této složce a všem podřízeným položkám.

5. Pomocí nástroje Stream Analytics CI můžete generovat šablony Správce prostředků s následující [vlastností. CD Nuget balíček](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) verze 1.5.0 nebo vyšší na sestavení počítače (mimo Visual Studio). Postupujte podle kroků nasazení šablony Resource Manager v další části získat instanční objekt a udělit přístup k instančnímu objektu prostřednictvím prostředí PowerShell.

## <a name="resource-manager-template-deployment"></a>Nasazení šablony Správce prostředků

1. Prostředek *Microsoft.StreamAnalytics/streamingjobs* se spravovanou identitou můžete vytvořit tak, že do části prostředků šablony Správce prostředků zahrnuli následující vlastnosti:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Tato vlastnost říká Azure Resource Manager vytvořit a spravovat identitu pro úlohu Azure Stream Analytics.

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
  
   **Vzorová odezva úlohy**

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

   Poznamenejte si ID jistiny z odpovědi na úlohu a udělte přístup k požadovanému prostředku ADLS.

   **ID klienta** je ID klienta Služby Azure Active Directory, kde se vytvoří instanční objekt. Instanční objekt se vytvoří v tenantovi Azure, který je důvěryhodný podle předplatného.

   **Typ** označuje typ spravované identity, jak je vysvětleno v typech spravovaných identit. Podporován je pouze typ Přiřazený systém.

2. Poskytněte přístup k instančnímu objektu pomocí prostředí PowerShell. Chcete-li udělit přístup k instančnímu objektu prostřednictvím prostředí PowerShell, proveďte následující příkaz:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** je ID objektu instančního objektu a je uveden na obrazovce portálu po vytvoření instančního objektu. Pokud jste úlohu vytvořili pomocí nasazení šablony Správce prostředků, id objektu je uvedeno ve vlastnosti Identity odpovědi na úlohu.

   **Příklad**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Další informace o výše uvedeném příkazu prostředí PowerShell naleznete v dokumentaci [Set-AzDataLakeStoreItemAclEntry.](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry)

## <a name="limitations"></a>Omezení
Tato funkce nepodporuje následující:

1. **Přístup pro více klientů**: Objekt zabezpečení služby vytvořený pro danou úlohu Stream Analytics bude umístěn v tenantovi služby Azure Active Directory, ve kterém byla úloha vytvořena, a nelze jej použít proti prostředku, který je umístěn v jiném tenantovi služby Azure Active Directory. Proto můžete použít jenom MSI na prostředky ADLS Gen 1, které jsou ve stejném tenantovi Služby Azure Active Directory jako vaše úloha Azure Stream Analytics. 

2. **[Přiřazená identita uživatele](../active-directory/managed-identities-azure-resources/overview.md)**: není podporována. To znamená, že uživatel nemůže zadat vlastní instanční objekt, který má být použit jejich úlohou Stream Analytics. Instanční objekt je generován službou Azure Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Vytvoření výstupu úložiště datových jezírek s analýzou datového proudu](../data-lake-store/data-lake-store-stream-analytics.md)
* [Test Stream Analytics dotazy místně s Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testování živých dat místně pomocí nástrojů Azure Stream Analytics pro Visual Studio](stream-analytics-live-data-local-testing.md) 
