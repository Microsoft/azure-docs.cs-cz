---
title: Použití spravované identity k ověření úlohy Azure Stream Analytics pro Azure Data Lake Storage Gen1 výstup (Preview)
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2018
ms.openlocfilehash: 41b3dcc03f7cfbfee11798738a3b2daaf5e96741
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365284"
---
# <a name="use-managed-identities-to-authenticate-azure-stream-analytics-jobs-to-azure-data-lake-storage-gen1-output-preview"></a>Použití spravované identity k ověření úlohy Azure Stream Analytics pro Azure Data Lake Storage Gen1 výstup (Preview)

Azure Stream Analytics podporuje spravovanou identitu ověřování pomocí Azure Data Lake Storage (ADLS) Gen1 výstup. Identita je spravovaná aplikace v Azure Active Directory, který představuje dané úlohy Stream Analytics a slouží k ověřování na cílový prostředek. Spravované identity eliminovat omezení metody ověřování založené na uživatelích, jako by bylo nutné donutit z důvodu změny hesla nebo uživatel token platnosti, ke kterým dochází vždy po 90 dnech. Kromě toho spravované identity usnadňující automatizaci nasazení úlohy Stream Analytics, která výstup do Azure Data Lake Storage Gen1.

Přejděte [osm nových funkcí ve službě Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) příspěvek na blogu k registraci pro tuto verzi preview a přečtěte si více o nových funkcích.

Tento článek ukazuje dva způsoby, jak povolit spravovanou identitu pro úlohy Azure Stream Analytics, jejichž výstupem jsou do Azure Data Lake Storage Gen1: na webu Azure portal a až po nasazení šablony Azure Resource Manageru.

## <a name="enable-managed-identity-with-azure-portal"></a>Povolit identitu spravované pomocí webu Azure portal

1. Začněte tím, že vytvoříte novou úlohu Stream Analytics nebo tak, že otevřete existující projekt na webu Azure portal. V panelu nabídky na levé straně obrazovky vyberte **spravovaná identita (preview)** umístěna ve složce **konfigurovat**.

   ![Konfigurace spravovaných identit Stream Analytics ve verzi preview](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Vyberte **použijte systém přiřadil Identity spravované (verze preview)** z okna, která se zobrazí na pravé straně. Klikněte na tlačítko **Uložit** k vytvoření instančního objektu pro identifikaci úlohy Stream Analytics v Azure Active Directory. Azure bude spravovat životní cyklus nově vytvořený identity. Při odstranění úlohy Stream Analytics přidružený identity (to znamená, instanční objekt) Azure automaticky odstraní.

   Při uložení konfigurace ID objektu (OID) objektu služby, které je uvedené jako ID objektu zabezpečení, jak je znázorněno níže:

   ![Stream Analytics hlavní ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Instanční objekt služby má stejný název jako úloha Stream Analytics. Například, pokud je název vaší úlohy **MyASAJob**, název se vytvořil objekt služby je také **MyASAJob**.

3. V okně výstupu vlastnosti výstupní jímky ADLS Gen1, klikněte na rozevírací seznam a vyberte režim ověřování **spravovaná identita (preview)**.

4. Vyplňte zbývající vlastnosti nástroje. Další informace o vytváření výstup ADLS najdete v tématu [vytvořit výstupní Data lake Store pomocí stream analytics](../data-lake-store/data-lake-store-stream-analytics.md). Až budete hotovi, klikněte na tlačítko **Uložit**.

   ![Konfigurace úložiště Azure Data Lake](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Přejděte na stránku přehled vaší ADLS Gen1 a klikněte na **Průzkumník dat**.

   ![Přehled služby Data Lake Storage konfigurace](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. V podokně Průzkumník dat vyberte **přístup** a klikněte na tlačítko **přidat** v podokně přístup.

   ![Konfigurace přístupu k Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Do textového pole na **vyberte uživatele nebo skupinu** podokně, zadejte název instančního objektu. Mějte na paměti, že název instančního objektu je také název odpovídající úlohy Stream Analytics. Během zadávání hlavní název se zobrazí pod textovým polem. Vyberte hlavní název požadované služby a klikněte na tlačítko **vyberte**.

   ![Vyberte hlavní název služby](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. V **oprávnění** podokně zaškrtněte **zápisu** a **Execute** oprávnění a přiřadit ji ke **tuto složku a všechny podřízené objekty**. Pak klikněte na tlačítko **Ok**.

   ![Vyberte oprávnění](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Instanční objekt služby je uvedený v části **přiřazena oprávnění** na **přístup** podokně, jak je znázorněno níže. Teď můžete přejít zpět a spuštění vaší úlohy Stream Analytics.

   ![Přístup k seznamu](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Další informace o oprávnění systému souborů Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="resource-manager-template-deployment"></a>Nasazení šablony Resource Manageru

1. Můžete vytvořit *Microsoft.StreamAnalytics/streamingjobs* prostředků s využitím spravované identity zahrnutím následující vlastnosti v oddíle prostředků šablony Resource Manageru:

   ```json
   "Identity": {
   "Type": "SystemAssigned",
   },
   ```

   Tato vlastnost říká Azure Resource Manageru můžete vytvořit a spravovat identitu pro vaši úlohu Azure Stream Analytics.

   **Ukázkové úlohy**

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
                 "accountName": “myDataLakeAccountName",
                 "filePathPrefix": “cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
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
   ```

   Poznamenejte si ID objektu zabezpečení z odpovědi úlohy udělit přístup k požadované prostředků ADLS.

   **ID Tenanta** je ID tenanta Azure Active Directory, kde se vytvoří instanční objekt služby. Instanční objekt se vytvoří v Azure, která je důvěryhodná pro předplatné.

   **Typ** označuje typ spravovanou identitu, jak je vysvětleno v typech spravovaných identit. Je podporován pouze typ systém přiřadil.

2. Poskytnout přístup k objektu zabezpečení pomocí prostředí PowerShell. Udělit přístup ke službě instančního objektu pomocí Powershellu, spusťte následující příkaz:

   ```powershell
   Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** je ID objektu instančního objektu a je uvedené na portálu obrazovka po vytvoření instančního objektu. Pokud jste vytvořili úlohu pomocí nasazení šablony Resource Manageru, ID objektu je uvedeno ve vlastnosti identita třídy odpověď úlohy.

   **Příklad**

   ```powershell
   PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Další informace o výše uvedený příkaz Powershellu, přečtěte si [Set-AzureRmDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters) dokumentaci.

## <a name="next-steps"></a>Další postup

* [Vytvořit výstupní Data lake Store pomocí stream analytics](../data-lake-store/data-lake-store-stream-analytics.md)
