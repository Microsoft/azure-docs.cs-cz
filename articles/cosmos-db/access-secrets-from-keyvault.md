---
title: Uložení klíčů a přístup k nim Azure Cosmos DB pomocí Key Vault
description: Pomocí Azure Key Vault můžete ukládat a přistupovat k Azure Cosmos DB připojovacímu řetězci, klíčům a koncovým bodům.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 55e6bbc338c1ac6f9ef935b4a3a05c32f2b5e9f5
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755211"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Zabezpečení klíčů Azure Cosmos pomocí Azure Key Vault 

Při použití Azure Cosmos DB pro vaše aplikace můžete získat přístup k databázi, kolekcím, dokumentům pomocí koncového bodu a klíče v konfiguračním souboru aplikace.  Není však bezpečné vkládat klíče a adresu URL přímo do kódu aplikace, protože jsou k dispozici ve formátu prostého textu pro všechny uživatele. Chcete zajistit, aby byl koncový bod a klíče k dispozici, ale prostřednictvím zabezpečeného mechanismu. Tady vám Azure Key Vault může pomáhat bezpečně ukládat a spravovat tajné klíče aplikace.

Pro ukládání a čtení Azure Cosmos DB přístupových klíčů z Key Vault je nutné použít následující postup:

* Vytvoření trezoru klíčů  
* Přidání přístupových klíčů Azure Cosmos DB k Key Vault  
* Vytvoření webové aplikace Azure  
* Zaregistrujte aplikaci & udělte oprávnění ke čtení Key Vault  


## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).  
2. Vyberte **vytvořit prostředek > > zabezpečení Key Vault**.  
3. V části **Vytvořit trezor klíčů** zadejte následující informace:  
   * **Název:** Zadejte jedinečný název Key Vault.  
   * **Předplatné:** Vyberte předplatné, které budete používat.  
   * V části **Skupina prostředků** zvolte **Vytvořit novou** a zadejte název skupiny prostředků.  
   * V rozevírací nabídce umístění vyberte umístění.  
   * U ostatních možností ponechte výchozí nastavení.  
4. Po zadání výše uvedených informací vyberte **Vytvořit**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Přidejte do Key Vault přístupové klíče Azure Cosmos DB.
1. Přejděte na Key Vault, který jste vytvořili v předchozím kroku, a otevřete kartu **tajné klíče** .  
2. Vybrat **+ Generovat/importovat**, 

   * Pro **Možnosti odeslání**vyberte možnost **ručně** .
   * Zadejte **název** tajného klíče.
   * Zadejte připojovací řetězec účtu Cosmos DB do pole **hodnota** . A pak vyberte **vytvořit**.

   ![Vytvoření tajného klíče](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Po vytvoření tajného klíče ho otevřete a zkopírujte identifikátor * * tajného klíče, který je v následujícím formátu. Tento identifikátor použijete v další části. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Vytvoření webové aplikace Azure

1. Vytvořte webovou aplikaci Azure nebo si aplikaci můžete stáhnout z [úložiště GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Jedná se o jednoduchou aplikaci MVC.  

2. Rozbalte staženou aplikaci a otevřete soubor **HomeController.cs** . Aktualizujte tajné ID na následujícím řádku:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Uložte** soubor a **Sestavte** řešení.  
4. Potom aplikaci nasaďte do Azure. Klikněte pravým tlačítkem na projekt a vyberte **publikovat**. Vytvořte nový profil služby App Service (můžete aplikaci pojmenovat WebAppKeyVault1) a vybrat **publikovat**.   

5. Po nasazení aplikace. Z Azure Portal přejděte na webovou aplikaci, kterou jste nasadili, a zapněte **identitu spravované služby** této aplikace.  

   ![Identita spravované služby](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Pokud teď aplikaci spustíte, zobrazí se následující chyba, protože jste neudělili žádné oprávnění k této aplikaci v Key Vault.

![Aplikace nasazená bez přístupu](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Zaregistrujte aplikaci & udělte oprávnění ke čtení Key Vault

V této části zaregistrujete aplikaci pomocí Azure Active Directory a udělíte oprávnění aplikaci ke čtení Key Vault. 

1. Přejděte na Azure Portal otevřete **Key Vault** , který jste vytvořili v předchozí části.  

2. Otevřete **zásady přístupu**, vyberte **+ Přidat nový** najít webovou aplikaci, kterou jste nasadili, vyberte oprávnění a vyberte **OK**.  

   ![Přidat zásady přístupu](./media/access-secrets-from-keyvault/add-access-policy.png)

Když teď aplikaci spustíte, můžete si přečíst tajný klíč z Key Vault.

![Aplikace nasazená s tajným klíčem](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Podobně můžete přidat uživatele pro přístup k trezoru klíčů. K Key Vault se musíte přidat sami tak, že vyberete **zásady přístupu** a potom udělíte všechna oprávnění, která potřebujete ke spuštění aplikace ze sady Visual Studio. Když je tato aplikace spuštěná z plochy, převezme vaši identitu.

## <a name="next-steps"></a>Další kroky

* Postup konfigurace brány firewall pro Azure Cosmos DB najdete v článku věnovaném [podpoře brány firewall](firewall-support.md) .
* Informace o konfiguraci koncového bodu služby virtuální sítě najdete v článku [zabezpečený přístup pomocí koncového bodu služby](vnet-service-endpoint.md) virtuální sítě.
