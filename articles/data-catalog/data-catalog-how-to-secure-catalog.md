---
title: Jak zabezpečit přístup k Azure Data Catalog
description: Tento článek vysvětluje postup při zabezpečení katalogu dat a jeho datovým assetům.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 9d80eddffaf376b6f6f0090dce74a4884b291531
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405525"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Jak zabezpečit přístup ke katalogu dat a datových assetů
> [!IMPORTANT]
> Tato funkce je dostupná jenom v edici standard služby Azure Data Catalog.

Azure Data Catalog umožňuje určit, kdo má přístup k katalogu dat a jaké operace (zaregistrovat, opatřit poznámkami, převzít vlastnictví) můžete provádět na metadat v katalogu. 

## <a name="catalog-users-and-permissions"></a>Uživatelé katalogu a oprávnění
Zadejte uživatele nebo skupinu přístup ke katalogu dat a nastavení oprávnění:

1. Na [domovskou stránku služby data catalog](http://www.azuredatacatalog.com), klikněte na tlačítko **nastavení** na panelu nástrojů.

    ![data catalog – nastavení](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. Na stránce nastavení, rozbalte **uživatelé katalogu** oddílu.
    ![Katalog uživatelé – přidat](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Klikněte na tlačítko **Add** (Přidat).
4. Zadejte plně kvalifikovaný **uživatelské jméno** nebo název **skupiny zabezpečení** v Azure Active Directory (AAD) přidružené k katalogu. Čárkou (', ') jako oddělovač, pokud chcete přidat více než jeden uživatel nebo skupina.
    ![Uživatelé katalogu - uživatele nebo skupiny](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Stisknutím klávesy **ENTER** nebo **kartu** z textového pole. 
6.  Ujistěte se, že všechna oprávnění (**opatřit poznámkami**, **zaregistrovat**, a **převzít vlastnictví**) jsou přiřazeny k těmto uživatelům nebo skupinám ve výchozím nastavení. To znamená, že uživatel nebo skupina můžete [registrace datových assetů]( data-catalog-how-to-register.md), [opatřit poznámkami datové assety]( data-catalog-how-to-annotate.md), a [převzít vlastnictví datových assetů]( data-catalog-how-to-manage.md). 
    ![Uživatelé katalogu – výchozí oprávnění](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Zadejte uživatele nebo skupinu pouze přístup pro čtení do katalogu, zrušte **opatřit poznámkami** možnost pro uživatele nebo skupiny. Pokud tak učiníte, uživatele nebo skupinu nelze opatřit poznámkami datové assety v katalogu, ale je mohou zobrazit. 
8.  Odepřít uživatele nebo skupiny v registraci datových assetů, zrušte **zaregistrovat** možnost pro uživatele nebo skupiny.
9.  Odepřít uživatele z převzetí vlastnictví k datovému assetu, zrušte **převzít vlastnictví** možnost pro uživatele nebo skupiny. 
10. Pokud chcete odstranit uživatele nebo skupiny uživatelů katalogu, klikněte na tlačítko **x** pro uživatele nebo skupiny v dolní části seznamu. 
    ![Uživatelé katalogu – odstranit uživatele](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Doporučujeme vám, že přidáte funkci skupin zabezpečení katalogu přímo uživatelům a místo přidání uživatelů a přiřazení oprávnění. Pak přidejte uživatele do skupin zabezpečení, které odpovídají jejich rolí a jejich požadovaný přístup ke katalogu.

## <a name="special-considerations"></a>Zvláštní upozornění

- Oprávnění přiřazená skupinám zabezpečení jsou aditivní. Řekněme uživatel je ve dvou skupinách. Jedna skupina má poznámkami oprávnění a jiná skupina není mají opatřit poznámkami oprávnění. Uživatel potom má opatřit poznámkami oprávnění. 
- Oprávnění přiřazená uživateli explicitně přepsat oprávnění přiřazená do skupiny, do kterých uživatel patří. V předchozím příkladu předpokládejme, že explicitně přidáte uživateli katalogu uživatelů a provést nelze přiřadit oprávnění opatřovat je poznámkami. Uživatele nelze opatřit poznámkami datové assety, i když je uživatel členem skupiny, které mají oprávnění opatřovat je poznámkami.

## <a name="next-steps"></a>Další postup
- [Začínáme s Azure Data Catalogem](data-catalog-get-started.md)

