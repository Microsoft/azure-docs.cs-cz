---
title: Jak zabezpečit přístup ke katalogu dat Azure
description: Tento článek vysvětluje, jak zabezpečit katalog dat a jeho datové prostředky v katalogu dat Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976765"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Jak zabezpečit přístup ke katalogu dat a datovým prostředkům

> [!IMPORTANT]
> Tato funkce je dostupná jenom ve standardní edici Katalogu dat Azure.

Azure Data Catalog umožňuje určit, kdo má přístup ke katalogu dat a jaké operace (registr, anotace, převzít vlastnictví) mohou provádět metadata v katalogu. 

## <a name="catalog-users-and-permissions"></a>Uživatelé katalogu a oprávnění

Jak uživateli nebo skupině udělit přístup ke katalogu dat a nastavit oprávnění:

1. Na [domovské stránce katalogu dat](https://www.azuredatacatalog.com)klikněte na panelu nástrojů na **Nastavení.**

   ![Tlačítko Nastavení domovské stránky katalogu dat Azure](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Na stránce nastavení rozbalte oddíl **Uživatelé katalogu.**

   ![Tlačítko Přidat uživatele datového katalogu Azure](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Klikněte na **Přidat**.

4. Do služby Azure Active Directory (AAD) přidružené ke katalogu zadejte plně kvalifikované **uživatelské jméno** nebo název **skupiny zabezpečení.** Pokud přidáváte více uživatelů nebo skupin, použijte čárku (''') jako oddělovač.

   ![Uživatelé katalogu dat Azure – uživatelé nebo skupiny](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Stiskněte **klávesu ENTER** nebo **TAB** mimo textové pole. 

6. Ve výchozím nastavení zkontrolujte, zda jsou těmto uživatelům nebo skupinám přiřazena všechna oprávnění **(Anotate**, **Register**a **Take Ownership**). To znamená, že uživatel nebo skupina může [registrovat datové prostředky]( data-catalog-how-to-register.md), [anotovat datové prostředky]( data-catalog-how-to-annotate.md)a [převzít vlastnictví datových prostředků]( data-catalog-how-to-manage.md). 

   ![Uživatelé katalogu dat Azure – výchozí oprávnění](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Chcete-li uživateli nebo skupině udělit přístup pro čtení pouze ke katalogu, zrušte **zaškrtnutí možnosti anotace** pro tohoto uživatele nebo skupinu. Pokud tak učiníte, uživatel nebo skupina nemůže oznamovat datové prostředky v katalogu, ale mohou je zobrazit. 

8. Chcete-li uživateli nebo skupině odepřít registraci datových prostředků, zrušte zaškrtnutí možnosti **registru** pro tohoto uživatele nebo skupinu.

9. Chcete-li uživateli odepřít vlastnictví datového prostředku, zrušte zaškrtnutí možnosti **převzetí vlastnictví** pro tohoto uživatele nebo skupinu. 

10. Chcete-li odstranit uživatele nebo skupinu z uživatelů katalogu, klepněte na tlačítko **x** pro uživatele nebo skupinu v dolní části seznamu. 

   ![Uživatelé katalogu dat Azure – ikona odstranění uživatele X](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Doporučujeme přidávat skupiny zabezpečení pro uživatele katalogu, nikoli přidávat uživatele přímo a přiřazovat oprávnění. Potom přidejte uživatele do skupin zabezpečení, které odpovídají jejich rolím a jejich požadovaný přístup ke katalogu.

## <a name="special-considerations"></a>Zvláštní aspekty

- Oprávnění přiřazená skupinám zabezpečení jsou aditivní. Řekněme, že uživatel je ve dvou skupinách. Jedna skupina má oprávnění k přidávání a notahů a jiná skupina nemá oprávnění k přidávání not. Potom má uživatel oprávnění k ohodnocení. 
- Oprávnění přiřazená explicitně uživateli přepisují oprávnění přiřazená skupinám, ke kterým uživatel patří. V předchozím příkladu řekněme výslovně jste přidali uživatele k uživatelům katalogu a nepřiřazují oprávnění k přidávání anotace. Uživatel nemůže oznamovat datové prostředky, i když je členem skupiny, která má oprávnění k opatří anotaci.

## <a name="next-steps"></a>Další kroky

- [Začínáme s Azure Data Catalogem](data-catalog-get-started.md)
