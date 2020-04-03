---
title: Použití trezoru klíčů k ukládání klíčů Azure Cosmos DB a přístup u jeho přístupu
description: Azure Key Vault slouží k ukládání a přístupu k připojovacímu řetězci, klíčům, koncovým bodům Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 225221635f978e3d70cec4ce7e9d78d6b100b4fd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618755"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Zabezpečení klíčů Azure Cosmos s využitím služby Azure Key Vault 

>[!IMPORTANT]
> Doporučené řešení pro přístup ke klíčům Azure Cosmos DB je použití [spravované identity přiřazené k systému](managed-identity-based-authentication.md). Pokud vaše služba nemůže využívat spravované identity, použijte [řešení založené na certifikátu](certificate-based-authentication.md). Pokud řešení spravované identity i řešení založené na certifikátu nesplňují vaše potřeby, použijte níže uvedené řešení trezoru klíčů.

Při použití Azure Cosmos DB pro vaše aplikace, můžete přistupovat k databázi, kolekce, dokumenty pomocí koncového bodu a klíč v rámci konfiguračního souboru aplikace.  Není však bezpečné umístit klíče a adresu URL přímo do kódu aplikace, protože jsou k dispozici ve formátu prostého textu všem uživatelům. Měli byste zajistit, aby koncový bod a klíče byly dostupné bezpečným způsobem. Se zabezpečeným ukládáním a správou tajných klíčů aplikací vám může pomoct služba Azure Key Vault.

Následující kroky jsou nutné k uložení a čtení přístupových klíčů Azure Cosmos DB z trezoru klíčů:

* Vytvoření trezoru klíčů  
* Přidání přístupových klíčů Azure Cosmos DB do trezoru klíčů  
* Vytvoření webové aplikace Azure  
* Registrace aplikace & udělovat oprávnění ke čtení trezoru klíčů  


## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).  
2. Vyberte **možnost Vytvořit prostředek > trezoru klíčů zabezpečení >**.  
3. V části **Vytvořit trezor klíčů** zadejte následující informace:  
   * **Název:** Zadejte jedinečný název trezoru klíčů.  
   * **Předplatné:** Vyberte předplatné, které budete používat.  
   * V části **Skupina prostředků** zvolte **Vytvořit novou** a zadejte název skupiny prostředků.  
   * V rozevírací nabídce Umístění zvolte umístění.  
   * Ostatní možnosti ponechejte ve výchozím nastavení.  
4. Po zadání výše uvedených informací vyberte **Vytvořit**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Přidejte přístupové klíče Azure Cosmos DB do trezoru klíčů.
1. Přejděte do trezoru klíčů, který jste vytvořili v předchozím kroku, a otevřete kartu **Tajné klíče.**  
2. Vyberte **možnost +Generovat/Importovat**, 

   * Vyberte **možnosti Ruční** **pro nahrávání**.
   * Zadejte **název** svého tajného klíče
   * Zadejte připojovací řetězec účtu Cosmos DB do pole **Hodnota.** A pak vyberte **Vytvořit**.

   ![Vytvoření tajného klíče](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Po vytvoření tajného klíče jej otevřete a zkopírujte **Tajný identifikátor, který je v následujícím formátu. Tento identifikátor použijete v další části. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Vytvoření webové aplikace Azure

1. Vytvořte webovou aplikaci Azure nebo si ji můžete stáhnout z [úložiště GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Jedná se o jednoduchou aplikaci MVC.  

2. Rozbalte staženou aplikaci a otevřete **soubor HomeController.cs.** Aktualizujte ID tajného klíče na následujícím řádku:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Uložte** soubor, **Vytvořte** řešení.  
4. Další nasazení aplikace do Azure. Klikněte pravým tlačítkem myši na projekt a zvolte **publikovat**. Vytvořte si nový profil služby app service (aplikaci můžete pojmenovat WebAppKeyVault1) a vyberte **Publikovat**.   

5. Po nasazení aplikace. Na webu Azure navigujte do webové aplikace, kterou jste nasadili, a zapněte **identitu spravované služby** této aplikace.  

   ![Identita spravované služby](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Pokud aplikaci spustíte nyní, zobrazí se následující chyba, protože jste této aplikaci v trezoru klíčů neudělili žádné oprávnění.

![Aplikace nasazená bez přístupu](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrace aplikace & udělovat oprávnění ke čtení trezoru klíčů

V této části zaregistrujete aplikaci pomocí služby Azure Active Directory a udělte aplikaci oprávnění ke čtení trezoru klíčů. 

1. Přejděte na portál Azure a otevřete **trezor klíčů,** který jste vytvořili v předchozí části.  

2. Otevřete **zásady Přístupu**, vyberte **+Přidat nové** najít webovou aplikaci, kterou jste nasadili, vyberte oprávnění a vyberte **OK**.  

   ![Přidat zásady přístupu](./media/access-secrets-from-keyvault/add-access-policy.png)

Nyní, pokud spustíte aplikaci, můžete si přečíst tajný klíč z trezoru klíčů.

![Aplikace nasazená s tajnými](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Podobně můžete přidat uživatele pro přístup k trezoru klíčů. Do trezoru klíčů se musíte přidat výběrem **zásad přístupu** a pak udělit všechna oprávnění, která potřebujete ke spuštění aplikace z aplikace Visual Studio. Když je tato aplikace spuštěna z plochy, převezme vaši identitu.

## <a name="next-steps"></a>Další kroky

* Postup konfigurace brány firewall pro Azure Cosmos DB najdete v článku [o podpoře brány firewall.](firewall-support.md)
* Pokud chcete nakonfigurovat koncový bod služby virtuální sítě, podívejte se na [článek o zabezpečeném přístupu pomocí článku koncového bodu služby Virtuální sítě.](vnet-service-endpoint.md)
