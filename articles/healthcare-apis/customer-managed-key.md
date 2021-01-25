---
title: Konfigurace klíčů spravovaných zákazníkem pro Azure API pro FHIR
description: Využijte vlastní klíčovou funkci podporovanou v rozhraní Azure API pro FHIR prostřednictvím Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 3879280f56a4b99d8e6e08a9c9ed852ef2cafa68
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747319"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Konfigurace klíčů spravovaných zákazníkem v klidovém rozmezí

Když vytvoříte nový účet Azure API pro FHIR, vaše data se ve výchozím nastavení šifrují pomocí klíčů spravovaných Microsoftem. Nyní můžete přidat druhou vrstvu šifrování dat pomocí vlastního klíče, který vyberete a budete spravovat sami.

V Azure se to obvykle provádí pomocí šifrovacího klíče v Azure Key Vault zákazníka. Azure SQL, Azure Storage a Cosmos DB jsou příklady, které tuto funkci poskytují ještě dnes. Rozhraní Azure API pro FHIR využívá tuto podporu od Cosmos DB. Při vytváření účtu budete mít možnost zadat identifikátor URI Azure Key Vault klíče. Tento klíč se předává do Cosmos DB, když se zřídí účet databáze. Když se vytvoří požadavek FHIR, Cosmos DB načte klíč a použije ho k zašifrování/dešifrování dat. Chcete-li začít, můžete se podívat na následující odkazy:

- [Registrace poskytovatele prostředků Azure Cosmos DB pro předplatné Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Konfigurace instance Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Přidání zásady přístupu do instance Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Vygenerovat klíč v Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Zadejte Azure Key Vault klíč

Při vytváření služby Azure API pro účet FHIR na Azure Portal se na kartě Další nastavení zobrazí možnost konfigurace šifrování dat v části nastavení databáze. Ve výchozím nastavení bude zvolena možnost klíč spravovaný službou. 

Klíč můžete zvolit z modulu pro výběr klíče:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Výběr":::

Můžete také zadat svůj Azure Key Vault klíč tak, že vyberete možnost klíč spravovaný zákazníkem. Identifikátor URI klíče můžete zadat tady:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Vytvoření rozhraní API Azure pro FHIR":::

U stávajících účtů FHIR můžete v okně "databáze" zobrazit možnost šifrování klíče (služba) (klíč spravovaný zákazníkem), a to jak je uvedeno níže. Možnost konfigurace se po zvolení nedá změnit. Svůj klíč ale můžete upravit a aktualizovat.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Databáze":::

Kromě toho můžete vytvořit novou verzi zadaného klíče, po jejímž uplynutí budou data zašifrovaná s novou verzí bez přerušení služby. Přístup k tomuto klíči můžete odebrat také tak, že odeberete přístup k datům. Pokud je klíč zakázán, dotazy budou mít za následek chybu. Pokud je klíč znovu povolený, budou dotazy znovu úspěšné.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak konfigurovat klíče spravované zákazníkem v klidovém umístění. V dalším kroku se můžete podívat na část Nejčastější dotazy k Azure Cosmos DB: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: jak nastavit CMK](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
