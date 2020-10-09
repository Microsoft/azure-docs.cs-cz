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
ms.openlocfilehash: 535bb5d21beafaabb50769a6c03478dbd1f942d4
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844298"
---
# <a name="configure-customer-managed-keys"></a>Konfigurace klíčů spravovaných zákazníkem

Když vytvoříte nový účet Azure API pro FHIR, vaše data se ve výchozím nastavení šifrují pomocí klíčů spravovaných Microsoftem. Nyní můžete přidat druhou vrstvu šifrování dat pomocí vlastního klíče, který vyberete a budete spravovat sami.

V Azure se to obvykle provádí pomocí šifrovacího klíče v Azure Key Vault zákazníka (integrace). Azure SQL, Azure Storage a Cosmos DB jsou příklady, které tuto funkci poskytují ještě dnes. Rozhraní Azure API pro FHIR využívá tuto podporu od Cosmos DB. Při vytváření účtu budete mít možnost zadat identifikátor URI integrace klíče. Po zřízení účtu databáze budeme tento klíč předat Cosmos DB. Když se vytvoří požadavek FHIR, Cosmos DB načte klíč a použije ho k zašifrování/dešifrování dat. Chcete-li začít, můžete se podívat na následující odkazy:

- [Registrace poskytovatele prostředků Azure Cosmos DB pro předplatné Azure](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#register-resource-provider) 
- [Konfigurace instance integrace](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#configure-your-azure-key-vault-instance)
-  [Přidání zásad přístupu do instance integrace](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#add-an-access-policy-to-your-azure-key-vault-instance)
- [Vygenerování klíče v integrace](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#generate-a-key-in-azure-key-vault)

Po vytvoření rozhraní API Azure pro účet FHIR na Azure Portal se na kartě Další nastavení zobrazí možnost konfigurace šifrování dat v části nastavení databáze. Ve výchozím nastavení bude zvolena možnost klíč spravovaný službou. Svůj klíč integrace můžete zadat tak, že vyberete možnost klíč spravovaný zákazníkem. Sem můžete zadat identifikátor URI zkopírovaného klíče.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Vytvoření rozhraní API Azure pro FHIR":::

Nebo můžete zvolit klíč ze služby pro výběr klíče:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Vytvoření rozhraní API Azure pro FHIR" zobrazit možnost šifrování klíče (služba) (klíč spravovaný zákazníkem), a to jak je uvedeno níže. Možnost konfigurace se po zvolení nedá změnit. Svůj klíč ale můžete upravit a aktualizovat.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Vytvoření rozhraní API Azure pro FHIR":::

Kromě toho můžete vytvořit novou verzi zadaného klíče, po jejímž uplynutí budou data zašifrovaná s novou verzí bez přerušení služby. Přístup k tomuto klíči můžete odebrat také tak, že odeberete přístup k datům.
