---
title: Jak zabezpečit přístup k Azure Data Catalog
description: V tomto článku se dozvíte, jak zabezpečit data Catalog a jeho datové assety.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c6c99eb62ba628ffc8c84799a6729540b572c580
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736386"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Postup zabezpečení přístupu ke katalogu dat a datovým assetům
> [!IMPORTANT]
> Tato funkce je k dispozici pouze v edici Standard Azure Data Catalog.

Azure Data Catalog vám umožní určit, kdo má mít přístup ke katalogu Data Catalog a jaké operace (zaregistrovat, opatřit poznámkami, převzít vlastnictví), můžou provádět metadata v katalogu. 

## <a name="catalog-users-and-permissions"></a>Uživatelé a oprávnění katalogu
Chcete-li uživateli nebo skupině udělit přístup ke katalogu dat a nastavit oprávnění:

1. Na [domovské stránce katalogu Data Catalog](https://www.azuredatacatalog.com)klikněte na **Nastavení** na panelu nástrojů.

    ![Data Catalog – nastavení](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. Na stránce nastavení rozbalte část **Uživatelé katalogu** .
    ![Uživatelé katalogu – přidat](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Klikněte na **Přidat**.
4. Zadejte plně kvalifikované **uživatelské jméno** nebo název **skupiny zabezpečení** v Azure Active Directory (AAD) přidružené ke katalogu. Pokud přidáváte více než jednoho uživatele nebo skupinu, použijte čárku (,) jako oddělovač.
    ![Uživatelé katalogu – uživatelé nebo skupiny](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. V textovém poli stiskněte klávesu **ENTER** nebo **TAB** . 
6.  Ověřte, že jsou ve výchozím nastavení přiřazenavšechna oprávnění (**opatřit poznámkami**, registrací a **převzít vlastnictví**) těmto uživatelům nebo skupinám. To znamená, že uživatel nebo skupina může [Registrovat datové assety]( data-catalog-how-to-register.md), [opatřit poznámkami datové prostředky]( data-catalog-how-to-annotate.md)a [převzít vlastnictví datových assetů]( data-catalog-how-to-manage.md). 
    ![Uživatelé katalogu – výchozí oprávnění](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Pokud chcete uživateli nebo skupině udělit jenom přístup pro čtení ke katalogu, zrušte pro daného uživatele nebo skupinu možnost **opatřit poznámkami** . Když to uděláte, uživatel nebo skupina nebude moci přidávat poznámky k datovým assetům v katalogu, ale může je zobrazit. 
8.  Chcete-li odepřít uživateli nebo skupině v registraci datových prostředků, zrušte zaškrtnutí možnosti **Registrovat** pro daného uživatele nebo skupinu.
9.  Pokud chcete uživateli odepřít převzít vlastnictví datového assetu, zrušte zaškrtnutí možnosti **převzít vlastnictví** pro daného uživatele nebo skupinu. 
10. Pokud chcete uživatele nebo skupinu odstranit z katalogu uživatelů, klikněte na **x** pro uživatele nebo skupinu ve spodní části seznamu. 
    ![Uživatelé katalogu – odstranit uživatele](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Doporučujeme přidat skupiny zabezpečení do katalogu uživatelů místo toho, aby přidávali uživatele přímo a nepřiřadili oprávnění. Pak přidejte uživatele do skupin zabezpečení, které odpovídají jejich rolím, a jejich požadovaný přístup ke katalogu.

## <a name="special-considerations"></a>Zvláštní upozornění

- Oprávnění přiřazená ke skupinám zabezpečení jsou aditivní. Řekněme, že uživatel je ve dvou skupinách. Jedna skupina má oprávnění k přidávání poznámek a jiná skupina nemá oprávnění k přidávání poznámek. Pak uživatel má oprávnění k přidání poznámek. 
- Oprávnění přiřazená explicitně uživateli přepíší oprávnění přiřazená skupinám, ke kterým uživatel patří. V předchozím příkladu řekněme, že jste explicitně přidali uživatele do katalogu a nepřiřadíte oprávnění k přidávání poznámek. Uživatel nemůže přidávat poznámky k datovým assetům i v případě, že je uživatel členem skupiny, která má oprávnění k přidání poznámek.

## <a name="next-steps"></a>Další postup
- [Začínáme s Azure Data Catalogem](data-catalog-get-started.md)

