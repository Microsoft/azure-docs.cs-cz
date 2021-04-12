---
title: Použití Azure dosah studia
description: Tento článek popisuje, jak používat Azure dosah Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 04/02/2021
ms.openlocfilehash: ba22c322d47d8738b1d607597d6f93b8b8456616
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283865"
---
# <a name="use-purview-studio"></a>Použití sady Purview Studio

Tento článek obsahuje přehled některých hlavních funkcí služby Azure dosah.

## <a name="prerequisites"></a>Požadavky

* Aktivní účet dosah je už vytvořený v Azure Portal a uživatel má oprávnění pro přístup k dosah studiu.

## <a name="launch-purview-account"></a>Spustit účet dosah

* Pokud chcete účet dosah spustit, v části Azure Portal klikněte na účty dosah, vyberte účet, který chcete spustit, a účet spusťte.

  :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Snímek obrazovky výběru pro spuštění katalogu účtů Azure dosah":::

* Jiný způsob, jak spustit účet dosah, je přejít na `https://web.purview.azure.com` , vybrat **Azure Active Directory** a název účtu pro spuštění účtu.

## <a name="home-page"></a>Domovská stránka

**Domovskou** stránkou je úvodní stránka klienta Azure dosah.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Snímek obrazovky domovské stránky":::

Následující seznam shrnuje hlavní funkce **domovské stránky**. Každé číslo v seznamu odpovídá zvýrazněnému číslu na předchozím snímku obrazovky.

1. Popisný název katalogu Název katalogu můžete nastavit v   >  **informacích o účtu** centra pro správu.

2. Katalog Analytics zobrazuje počet:

   * Uživatelé, skupiny a aplikace
   * Zdroje dat
   * Prostředky
   * Termíny glosáře

3. Vyhledávací pole umožňuje vyhledávat datové assety v rámci katalogu Data Catalog.

4. Tlačítka rychlý přístup poskytují přístup k často používaným funkcím aplikace. Zobrazená tlačítka závisí na roli přiřazené k vašemu uživatelskému účtu.

   * Pro *data kurátor* jsou tato tlačítka **centrem znalostí**, **procházejí assety**, **spravují Glosář** a **zobrazují přehledy**.
   * V případě *čtecího zařízení* jsou doporučenými tlačítky **znalostní báze**, **Procházet prostředky**, **zobrazovat Glosář** a **Zobrazovat přehledy**.
   * V případě kurátor dat *správců zdrojů dat*  +  jsou doporučenými tlačítky **centra Knowledge Center**, **Registrovat zdroje dat**, **Procházet prostředky** a **Spravovat Glosář**.
   * V případě  +  *čtečky dat* správce zdroje dat jsou doporučenými tlačítky **centra Knowledge Center**, **Registrovat zdroje dat**, **Procházet prostředky** a **zobrazovat Glosář**.

5. Levý navigační panel vám pomůže najít hlavní stránky aplikace. Zobrazená tlačítka závisí na roli přiřazené k vašemu uživatelskému účtu.

   * Pro *data kurátor* jsou tato tlačítka **Domovská**, **glosářová**, **přehledová** a **centra pro správu**.
   * Pro *čtečku dat* jsou tato tlačítka **Domovská**, **glosářová**, **přehledová** a **centra pro správu**.
   * Pro *správce zdroje dat* nebo *data kurátor/Reader* jsou k dispozici tlačítka **Domů**, **zdroje**, **Glosář**, **Přehled** a **Centrum pro správu**.
  
6. Na kartě **nedávno použité** se zobrazuje seznam nedávno použitých datových assetů. Informace o přístupu k assetům najdete v tématu [hledání Data Catalog](how-to-search-catalog.md) a [procházení podle typu assetu](how-to-browse-catalog.md#browse-experience).  Karta **Moje položky** je seznam datových assetů vlastněných přihlášeným uživatelem.
7. **Užitečné odkazy** obsahují odkazy na stav oblasti, dokumentaci, ceny, přehled a stav dosah.
8. Horní navigační panel obsahuje informace o poznámkách k verzi a o aktualizacích, změnách dosah účtů, oznámení, nápovědu a zpětná vazba.

## <a name="knowledge-center"></a>Centrum znalostí

Centrum znalostí vám umožní najít všechna videa a kurzy týkající se dosah.

## <a name="guided-tours"></a>Řízená prohlídky

Každé uživatelské prostředí ve službě Azure dosah Studio bude mít k dispozici prohlídky, které vám pomohou s přehledem stránky. Pokud chcete spustit prohlídku s průvodcem, vyberte v horním panelu **nápovědu** a vyberte **prohlídky s asistencí**.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Snímek obrazovky s průvodcem":::

> [!Important]
> Role *správce zdroje dat* sama o sobě nemá přístup k dosah studiu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidat objekt zabezpečení](tutorial-scan-data.md)
