---
title: Globální distribuce služby Azure Cosmos DB
description: Zjistěte, jak globálně replikovat data pomocí služby Azure Cosmos DB na webu Azure Portal.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 03/26/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: fb9418d47d2888467e1c1c40862833029111b75b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38747527"
---
Informace o globální distribuci služby Azure Cosmos DB najdete v následujícím videu, kde Andrew Liu, manažer programu Azure Cosmos DB, prochází funkce globální distribuce.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]

Další informace o fungování globální replikace databází ve službě Azure Cosmos DB najdete v tématu [Globální distribuce dat pomocí služby Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).

## <a id="addregion"></a>Přidání globálních oblastí databáze pomocí webu Azure Portal
Služba Azure Cosmos DB je dostupná ve všech [oblastech Azure][azureregions] po celém světe. Po výběru výchozí úrovně konzistence účtu databáze k němu můžete přidružit jednu nebo několik oblastí (v závislosti na výběru výchozí úrovně konzistence a potřebách globální distribuce).

1. V levém panelu na webu [Azure Portal](https://portal.azure.com/) klikněte na **Azure Cosmos DB**.
2. Na stránce **Azure Cosmos DB** vyberte účet databáze, který chcete upravit.
3. V nabídce na stránce účtu klikněte na **Globální replikace dat**.
4. Na stránce **Globální replikace dat** vyberte kliknutím na oblasti na mapě oblasti, které chcete přidat nebo odebrat, a pak klikněte na **Uložit**. Za přidání oblastí se neúčtují žádné poplatky. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) nebo v článku [Globální distribuce dat pomocí služby Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).
   
    ![Přidání nebo odebrání oblastí kliknutím na oblasti na mapě][1]
    
Po přidání druhé oblasti se na stránce portálu **Globální replikace dat** povolí možnost **Ruční převzetí služeb při selhání**. Pomocí této možnosti můžete otestovat proces převzetí služeb při selhání nebo změnit primární oblast pro zápis. Po přidání třetí oblasti se na stejné stránce povolí možnost **Priority převzetí služeb při selhání**, abyste mohli změnit pořadí převzetí služeb při selhání pro čtení.  

### <a name="selecting-global-database-regions"></a>Výběr globálních oblastí databáze
Pro konfiguraci dvou nebo více oblastí existují dva běžné scénáře:

1. Poskytování přístupu k datům s nízkou latencí koncovým uživatelům po celém světe bez ohledu na to, kde se nacházejí.
2. Přidání oblastní odolnosti pro zajištění provozní kontinuity a zotavení po havárii (BCDR).

Pro zajištění nízké latence pro koncové uživatele se doporučuje nasadit aplikaci i službu Azure Cosmos DB v oblastech, které odpovídají umístění uživatelů aplikace.

Pro zajištění BCDR se doporučuje přidat oblasti podle spárovaných oblastí popsaných v článku [Provozní kontinuita a zotavení po havárii (BCDR): Spárované oblasti Azure][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
