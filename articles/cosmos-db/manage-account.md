---
title: Správa účtu Azure Cosmos DB na webu Azure Portal | Dokumentace Microsoftu
description: Další informace o správě vašeho účtu služby Azure Cosmos DB na webu Azure Portal. Najdete Průvodce věnované využití webu Azure Portal k zobrazení, kopírování, odstranění a přístup k účtům.
keywords: Azure Portal, azure, Microsoft azure
services: cosmos-db
author: kirillg
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: abcf51c6bd196c2ffb0bb35e2df161531a53972d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229386"
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Jak spravovat účet služby Azure Cosmos DB
Zjistěte, jak nastavit globální soudržnosti, práce s klíči a odstranění účtu služby Azure Cosmos DB na webu Azure Portal.

## <a id="consistency"></a>Spravovat nastavení konzistence s Azure Cosmos DB
Výběr úrovně konzistence správné závisí na sémantiku vaší aplikace. Seznamte se s úrovní konzistence k dispozici ve službě Azure Cosmos DB načtením [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu ve službě Azure Cosmos DB][consistency]. Azure Cosmos DB poskytuje konzistencí, dostupností a záruky týkající se výkonu, na všech úrovních konzistence účtu databáze k dispozici. Konfigurace účtu databáze s úrovní konzistence silná vyžaduje, aby vaše data uzavřeného do jedné oblasti Azure a globálně dostupná. Na druhou stranu, úrovně mírnější konzistencí – omezená neaktuálnost, relace nebo konečné povolit k přidružení libovolného počtu oblastí Azure se svým databázovým účtem. Následující jednoduché kroky ukazují, jak k výběru výchozí úrovně konzistence účtu databáze.

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Chcete-li určit výchozí konzistence účtu služby Azure Cosmos DB
1. V [webu Azure portal](https://portal.azure.com/), přístup k vašemu účtu Azure Cosmos DB.
2. Na stránce účtu klikněte na **výchozí konzistence**.
3. V **výchozí konzistence** stránky, vyberte novou úroveň konzistence a klikněte na tlačítko **Uložit**.
    ![Výchozí konzistence relace][5]

## <a id="keys"></a>Zobrazení, kopírování a opětovné vygenerování přístupových klíčů a hesel
Při vytváření účtu služby Azure Cosmos DB, generuje tato služba dva hlavní přístupové klíče (nebo dvě hesla pro účty rozhraní API MongoDB), který slouží k ověřování při přístupu k účtu Azure Cosmos DB. Poskytnutím dvou přístupových klíčů služby Azure Cosmos DB umožňuje znovu vygenerovat klíče bez přerušení ke svému účtu Azure Cosmos DB. 

V [webu Azure portal](https://portal.azure.com/), přístup **klíče** na stránce v nabídce prostředků **účtu služby Azure Cosmos DB** stránky k zobrazení, kopírování a opětovné vygenerování přístupových klíčů, které se používají pro přístup k vašemu účtu Azure Cosmos DB. Účty rozhraní API MongoDB, přístup **připojovací řetězec** stránky v nabídce prostředků k zobrazení, kopírování a opětovné vygenerování hesla, které se používají pro přístup k účtu.

![Azure portal – snímek obrazovky, stránce klíče](./media/manage-account/keys.png)

> [!NOTE]
> **Klíče** stránka také obsahuje primární a sekundární připojovací řetězce, které je možné se připojit ke svému účtu z [nástroj pro migraci dat](import-data.md).
> 
> 

Klíče jen pro čtení jsou také k dispozici na této stránce. Dotazy jsou jen pro čtení operace, při vytvoří, odstraní a nahradí nejsou a čtení.

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>Zkopírování přístupového klíče nebo hesla na webu Azure Portal
Na **klíče** stránky (nebo **připojovací řetězec** stránku pro účty rozhraní API MongoDB), klikněte na tlačítko **kopírování** tlačítko vpravo od klíč nebo heslo, které chcete kopírovat.

![Zobrazení a zkopírování přístupového klíče na portálu Azure na stránce Klíče](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>Znovu vygenerovat přístupové klíče a hesla
Měli byste změnit přístupové klávesy (a hesla pro účty rozhraní API MongoDB) ke svému účtu Azure Cosmos DB pravidelně abychom zvýšili bezpečnost vašeho připojení. Dva klíče a hesla přístupu jsou přiřazeny vám umožní spravovat připojení k účtu Azure Cosmos DB používat jeden přístupový klíč, zatímco znovu vygenerujete druhý přístupový klíč.

> [!WARNING]
> Opětovné generování přístupových klíčů má vliv na všechny aplikace, které jsou závislé na aktuální klíč. Všichni klienti, kteří používají přístupový klíč pro přístup k účtu Azure Cosmos DB musí aktualizovat na používání nového klíče.
> 
> 

Pokud máte aplikace nebo cloudové služby pomocí účtu Azure Cosmos DB, ztratíte připojení je-li znovu vygenerovat klíče, klíče nezaregistrujete. Následující kroky popisují proces účastnící se zajištěním provozu klíče a hesla.

1. Aktualizujte přístupový klíč v kódu aplikace tak, aby odkazovaly sekundární přístupový klíč účtu služby Azure Cosmos DB.
2. Znovu vygenerovat primární přístupový klíč účtu služby Azure Cosmos DB. V [webu Azure portal](https://portal.azure.com/), přístup k vašemu účtu Azure Cosmos DB.
3. V **účet služby Azure Cosmos DB** klikněte na **klíče** (nebo **připojovací řetězec** pro MongoDB účty **).
4. Na **klíče**/**připojovací řetězec** stránky, klikněte na tlačítko znovu vygenerovat a pak klikněte na tlačítko **Ok** potvrďte, že chcete vygenerovat nový klíč.
    ![Opětovné vygenerování přístupových klíčů](./media/manage-account/regenerate-keys.png)
5. Jakmile si ověříte, že nový klíč je k dispozici pro použití (přibližně 5 minut po obnovení), aktualizujte přístupový klíč v kódu aplikace tak, aby odkazovaly nový primární přístupový klíč.
6. Stejným způsobem pak opětovně vygenerujte sekundární přístupový klíč.
   
    ![Opětovné vygenerování přístupových klíčů](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Může trvat několik minut, než je možné nově vygenerovaný klíč pro přístup k účtu služby Azure Cosmos DB.
> 
> 

## <a name="get-the-connection-string"></a>Získání připojovacího řetězce
Pokud chcete načíst váš připojovací řetězec, postupujte takto: 

1. V [webu Azure portal](https://portal.azure.com), přístup k vašemu účtu Azure Cosmos DB.
2. V nabídce prostředku klikněte na tlačítko **klíče** (nebo **připojovací řetězec** pro účty rozhraní API MongoDB).
3. Klikněte na tlačítko **kopírování** vedle **primární připojovací řetězec** nebo **sekundární připojovací řetězec** pole. 

Pokud používáte připojovací řetězec [nástroj pro migraci databáze Azure Cosmos DB](import-data.md), název databáze přidat na konec připojovací řetězec. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Odstranění účtu služby Azure Cosmos DB
Odebrat účet služby Azure Cosmos DB na webu Azure Portal, který už nepoužíváte, klikněte pravým tlačítkem na název účtu a klikněte na **odstranit účet**.

![Postup odstranění účtu služby Azure Cosmos DB na webu Azure Portal](./media/manage-account/deleteaccount.png)

1. V [webu Azure portal](https://portal.azure.com/), přístup k účtu Azure Cosmos DB, který chcete odstranit.
2. Na **účtu služby Azure Cosmos DB** stránky, klikněte pravým tlačítkem na účet a potom klikněte na tlačítko **odstranit účet**. 
3. Na stránce výsledný potvrzení zadejte název účtu služby Azure Cosmos DB k potvrzení, že chcete odstranit účet.
4. Klikněte na tlačítko **odstranit** tlačítko.

![Postup odstranění účtu služby Azure Cosmos DB na webu Azure Portal](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Další kroky
Zjistěte, jak [začít pracovat s vaším účtem Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
