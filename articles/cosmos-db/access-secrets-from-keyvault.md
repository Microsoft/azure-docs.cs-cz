---
title: Použití služby Key Vault k ukládání a přístupové klíče služby Azure Cosmos DB | Dokumentace Microsoftu
description: Použití Azure Key Vault k ukládání a přístup ke službě Azure Cosmos DB připojovacího řetězce, klíče, identifikátor URI.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: rafats
ms.openlocfilehash: b090c1593b49bec4f51fea8d498860e8af8b2f4b
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053080"
---
# <a name="use-key-vault-to-store-and-access-azure-cosmos-db-keys"></a>Použití služby Key Vault k ukládání a přístupové klíče služby Azure Cosmos DB

Při použití služby Azure Cosmos DB pro aplikace, můžete přistupovat databází, kolekcí, dokumentů s použitím identifikátor URI koncového bodu a klíče v konfiguračním souboru aplikace.  Však není bezpečné umístění klíče a adresy URL přímo v kódu aplikace, protože jsou k dispozici ve formátu prostého textu pro všechny uživatele. Budete chtít ověřit, že identifikátor URI a klíče jsou k dispozici, ale zabezpečené mechanismem. Je to, kde Azure Key Vault umožňuje bezpečně ukládat a spravovat tajné kódy aplikace.

Následující kroky jsou nutné pro ukládání a čtení přístupové klíče služby Azure Cosmos DB ze služby Key Vault:

* Vytvoření trezoru klíčů  
* Přidání služby Azure Cosmos DB přístupové klíče ke službě Key Vault  
* Vytvoření webové aplikace Azure  
* Zaregistrovat aplikaci & udělit oprávnění ke čtení služby Key Vault  


## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

1. Přihlaste se k [webu Azure Portal](https://portal.azure.com/).  
2. Vyberte **vytvořit prostředek > zabezpečení > služby Key Vault**.  
3. V části **Vytvořit trezor klíčů** zadejte následující informace:  
   * **Název:** zadejte jedinečný název pro Key Vault.  
   * **Předplatné:** zvolte předplatné, které budete používat.  
   * V části **Skupina prostředků** zvolte **Vytvořit novou** a zadejte název skupiny prostředků.  
   * V rozevírací nabídce umístění zvolte umístění.  
   * Další možnosti ponechte jejich výchozí hodnoty.  
4. Po zadání výše uvedených informací vyberte **Vytvořit**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Přidání služby Azure Cosmos DB přístupové klíče ke službě Key Vault.
1. Přejděte do služby Key Vault, kterou jste vytvořili v předchozím kroku, otevřete **tajných kódů** kartu.  
2. Vyberte **+ vygenerovat/importovat**, 

   * Vyberte **ruční** pro **možnosti nahrání**.
   * Zadejte **název** pro váš tajný klíč
   * Zadat připojovací řetězec účtu služby Cosmos DB do **hodnotu** pole. A pak vyberte **vytvořit**.

   ![Vytvoření tajného klíče](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Po vytvoření tajného klíče, otevřete ho a zkopírujte ** identifikátor tajný klíč, který je v následujícím formátu. Tento identifikátor použijete v další části. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Vytvoření webové aplikace Azure

1. Vytvoření webové aplikace Azure nebo si můžete stáhnout aplikaci [úložiště GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Je to jednoduché aplikace MVC.  

2. Stažených aplikací rozzipují a otevřou **HomeController.cs** souboru. Aktualizace tajného kódu ID v následující řádek:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Uložit** souboru **sestavení** řešení.  
4. Potom nasaďte aplikaci do Azure. Klikněte pravým tlačítkem na projekt a zvolte **publikovat**. Vytvoření nového profilu služby aplikace (app WebAppKeyVault1 jméno) a vyberte **publikovat**.   

5. Jakmile je aplikace nasazená. Na webu Azure Portal, přejděte do webové aplikace, kterou jste nasadili a zapnout **se identita spravované služby** této aplikace.  

   ![Identita spravované služby](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Pokud nyní aplikaci spustíte, zobrazí se následující chyba, protože nebyly zadané žádné oprávnění k této aplikaci ve službě Key Vault.

![Aplikace nasazení bez přístupu](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Zaregistrovat aplikaci & udělit oprávnění ke čtení služby Key Vault

V této části zaregistrovat aplikaci v Azure Active Directory a udělení oprávnění pro aplikace pro čtení služby Key Vault. 

1. Přejděte na portál Azure Portal, otevřete **služby Key Vault** jste vytvořili v předchozí části.  

2. Otevřít **zásady přístupu**vyberte **+ přidat nový** najít jste nasadili webovou aplikaci, vyberte oprávnění a vyberte **OK**.  

   ![Přidat zásady přístupů](./media/access-secrets-from-keyvault/add-access-policy.png)

Nyní Pokud aplikaci spouštíte, si můžete přečíst tajného klíče ze služby Key Vault.

![Aplikace nasazená s tajným klíčem](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Podobně můžete přidat uživatele pro přístup ke klíči trezoru. Budete muset přidat sebe do služby Key Vault tak, že vyberete **zásady přístupu** a potom jim udělit všechna oprávnění, která budete potřebovat ke spuštění aplikace ze sady Visual studio. Pokud tato aplikace běží na ploše, bude zpracování trvat vaši identitu.

## <a name="next-steps"></a>Další postup

* Konfigurace brány firewall služby Azure Cosmos DB najdete v tématu [podpora brány firewall](firewall-support.md) článku.
* Pokud chcete nakonfigurovat koncový bod služby virtuální sítě, naleznete v tématu [zabezpečení přístupu pomocí koncového bodu služby virtuální sítě](vnet-service-endpoint.md) článku.
