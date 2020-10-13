---
title: Jak zabezpečit přístup k Azure Data Catalog
description: Tento článek vysvětluje, jak zabezpečit Katalog dat a jeho datové assety v Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 6a429c09b6f8082c95e29bcea62d27ec4fb46fd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86523379"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Postup zabezpečení přístupu ke katalogu dat a datovým assetům

> [!IMPORTANT]
> Tato funkce je k dispozici pouze v edici Standard Azure Data Catalog.

Azure Data Catalog vám umožní určit, kdo má mít přístup ke katalogu Data Catalog a jaké operace (zaregistrovat, opatřit poznámkami, převzít vlastnictví), můžou provádět metadata v katalogu. 

## <a name="catalog-users-and-permissions"></a>Uživatelé a oprávnění katalogu

Chcete-li uživateli nebo skupině udělit přístup ke katalogu dat a nastavit oprávnění:

1. Na [domovské stránce katalogu Data Catalog](https://www.azuredatacatalog.com)klikněte na **Nastavení** na panelu nástrojů.

   ![Azure Data Catalog tlačítko nastavení domovské stránky](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Na stránce nastavení rozbalte část **Uživatelé katalogu** .

   ![Tlačítko pro přidání uživatelů Azure Data Catalog](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Klikněte na **Přidat**.

4. Zadejte plně kvalifikované **uživatelské jméno** nebo název **skupiny zabezpečení** v Azure Active Directory (AAD) přidružené ke katalogu. Pokud přidáváte více než jednoho uživatele nebo skupinu, použijte čárku (,) jako oddělovač.

   ![Azure Data Catalog uživatelé – uživatelé nebo skupiny](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. V textovém poli stiskněte klávesu **ENTER** nebo **TAB** . 

6. Ověřte, že jsou ve výchozím nastavení přiřazena všechna oprávnění (**opatřit poznámkami**, **registrací**a **převzít vlastnictví**) těmto uživatelům nebo skupinám. To znamená, že uživatel nebo skupina může [Registrovat datové assety]( data-catalog-how-to-register.md), [opatřit poznámkami datové prostředky]( data-catalog-how-to-annotate.md)a [převzít vlastnictví datových assetů]( data-catalog-how-to-manage.md). 

   ![Azure Data Catalog uživatelé – výchozí oprávnění](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Pokud chcete uživateli nebo skupině udělit jenom přístup pro čtení ke katalogu, zrušte pro daného uživatele nebo skupinu možnost **opatřit poznámkami** . Když to uděláte, uživatel nebo skupina nebude moci přidávat poznámky k datovým assetům v katalogu, ale může je zobrazit. 

8. Chcete-li odepřít uživateli nebo skupině v registraci datových prostředků, zrušte zaškrtnutí možnosti **Registrovat** pro daného uživatele nebo skupinu.

9. Pokud chcete uživateli odepřít převzít vlastnictví datového assetu, zrušte zaškrtnutí možnosti **převzít vlastnictví** pro daného uživatele nebo skupinu. 

10. Pokud chcete uživatele nebo skupinu odstranit z katalogu uživatelů, klikněte na **x** pro uživatele nebo skupinu ve spodní části seznamu. 

   ![Uživatelé Azure Data Catalog katalogu – ikona odstranit uživatele X](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Doporučujeme přidat skupiny zabezpečení do katalogu uživatelů místo toho, aby přidávali uživatele přímo a nepřiřadili oprávnění. Pak přidejte uživatele do skupin zabezpečení, které odpovídají jejich rolím, a jejich požadovaný přístup ke katalogu.

## <a name="special-considerations"></a>Zvláštní požadavky

- Oprávnění přiřazená ke skupinám zabezpečení jsou aditivní. Řekněme, že uživatel je ve dvou skupinách. Jedna skupina má oprávnění k přidávání poznámek a jiná skupina nemá oprávnění k přidávání poznámek. Pak uživatel má oprávnění k přidání poznámek. 
- Oprávnění přiřazená explicitně uživateli přepíší oprávnění přiřazená skupinám, ke kterým uživatel patří. V předchozím příkladu řekněme, že jste explicitně přidali uživatele do katalogu a nepřiřadíte oprávnění k přidávání poznámek. Uživatel nemůže přidávat poznámky k datovým assetům i v případě, že je uživatel členem skupiny, která má oprávnění k přidání poznámek.

## <a name="next-steps"></a>Další kroky

- [Začínáme s Azure Data Catalogem](data-catalog-get-started.md)
