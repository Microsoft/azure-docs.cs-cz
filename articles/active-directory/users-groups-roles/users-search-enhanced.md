---
title: Vylepšení správy uživatelů (Preview) – Azure Active Directory | Microsoft Docs
description: Popisuje, jak Azure Active Directory umožňuje uživatelům vyhledávání, filtrování a další informace o vašich uživatelích.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 10/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: be0d428120f53a4edb9763199a78b0e50409b19a
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708729"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Vylepšení správy uživatelů (Preview) v Azure Active Directory

Tento článek popisuje, jak používat vylepšení správy uživatelů ve verzi Preview na portálu Azure Active Directory (Azure AD). Stránky **Všichni uživatelé** a **odstraně uživatelé** byly aktualizovány, aby poskytovaly Další informace a usnadnily hledání uživatelů. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Změny ve verzi Preview zahrnují:

- Více viditelných uživatelských vlastností včetně ID objektu, stavu synchronizace adresáře, typu vytvoření a vystavitele identity
- Hledání teď umožňuje kombinované hledání názvů, e-mailů a ID objektů.
- Rozšířené filtrování podle typu uživatele (člen, Host, žádný), stav synchronizace adresářů, typ vytvoření, název společnosti a název domény
- Nové možnosti řazení u vlastností, jako je název a hlavní název uživatele
- Nový celkový počet uživatelů, kteří se aktualizují pomocí hledání nebo filtrů

> [!NOTE]
> Tato verze Preview není v současnosti dostupná pro Azure AD B2C klienty.

## <a name="find-the-preview"></a>Najít náhled

Ve výchozím nastavení je verze Preview zapnutá, takže ji můžete použít hned. Nejnovější funkce a vylepšení můžete zjistit výběrem **funkcí verze Preview** na stránce **Všichni uživatelé** . Všechny stránky, které byly aktualizovány v rámci této verze Preview, zobrazí značku Preview. Pokud máte nějaké problémy, můžete přejít zpátky do starší verze prostředí:

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) a vyberte **Uživatelé**.
1. Na stránce **Uživatelé – všichni uživatelé** vyberte banner v horní části stránky.
1. V podokně **funkce verze Preview** zapněte **vylepšenou správu uživatelů** .

   ![Jak a kde zapnout a vypnout vylepšenou správu uživatelů](./media/users-search-enhanced/enable-preview.png)

Vážíme si vašich názorů, abychom mohli vylepšit naše prostředí.

## <a name="more-user-properties"></a>Další vlastnosti uživatele

Provedli jsme některé změny ve sloupcích, které jsou k dispozici na stránkách **Všichni uživatelé** a **Odstranění uživatelé** . Kromě stávajících sloupců, které poskytujeme pro správu seznamu uživatelů, jsme přidali několik dalších sloupců.

### <a name="all-users-page"></a>Stránka všichni uživatelé

Na stránce **Všichni uživatelé** jsou zobrazeni následující vlastnosti uživatele:

- Název: zobrazovaný název uživatele.
- Hlavní název uživatele: hlavní název uživatele (UPN).
- Typ uživatele: typ uživatele, buď člen, nebo host.
- Adresář synchronizovaný: označuje, jestli je uživatel synchronizovaný z místního adresáře.
- Vystavitel identity: Vystavitelé identity, která se používá k přihlášení k uživatelskému účtu.
- ID objektu: ID objektu uživatele.
- Typ vytvoření: udává, jak byl uživatelský účet vytvořen.
- Název společnosti: název společnosti, ke kterému je uživatel přidružen.
- Stav pozvánky: stav pozvánky pro uživatele typu Host.
- E-mail: e-mail uživatele.

![nové vlastnosti uživatele zobrazené na stránkách všichni uživatelé a odstranění uživatelé](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Stránka Odstraněná uživatelé

Stránka **odstraněné uživatele** obsahuje všechny sloupce, které jsou k dispozici na stránce **Všichni uživatelé** , a několik dalších sloupců, konkrétně:

- Datum odstranění: datum, kdy se uživatel poprvé odstranil z organizace (uživatel je obnovitelné).
- Datum trvalého odstranění: datum, kdy byl uživatel trvale odstraněn z organizace.

Ve výchozím nastavení se zobrazují některé sloupce. Chcete-li přidat další sloupce, vyberte **sloupce** na stránce, vyberte názvy sloupců, které chcete přidat, a výběrem **OK** uložte předvolby.

### <a name="identity-issuers"></a>Vystavitelé identity

Vyberte položku ve sloupci **vystavitele identity** , pro libovolného uživatele zobrazíte další podrobnosti o vystaviteli, včetně typu přihlášení a ID přiřazeného vystavitele. Položky ve sloupci **vystavitele identity** můžou být vícehodnotové. Pokud je k dispozici více vystavitelů identity uživatele, zobrazí se ve sloupci **vystavitele identity** na **všech uživatelích** a **odstraněných uživatelích** slovo více a v podokně podrobností se zobrazí seznam všech vystavitelů.

> [!NOTE]
> **Zdrojový** sloupec je nahrazen více sloupci včetně **typu vytvoření**, **synchronizace adresáře**a **vystavitele identity** pro podrobnější filtrování.

## <a name="user-list-search"></a>Hledání v seznamu uživatelů

Když zadáte hledaný řetězec, používá hledání "začíná na", které se nyní může shodovat s názvy, e-maily nebo ID objektů v jednom hledání. Do vyhledávacího pole můžete zadat libovolný z těchto atributů a hledání se automaticky vyhledá ve všech těchto vlastnostech a vrátí všechny vyhovující výsledky. Stejné hledání můžete provádět na stránkách **Všichni uživatelé** i **odstraně uživatelé** .

## <a name="user-list-filtering"></a>Filtrování seznamu uživatelů

Byly vylepšeny možnosti filtrování, které poskytují více možností filtrování pro stránky **Všichni uživatelé** a **odstraně uživatelé** . Nyní můžete filtrovat podle více vlastností současně a filtrovat je podle dalších vlastností.

### <a name="filtering-all-users-list"></a>Filtrování seznamu všech uživatelů

Níže jsou uvedené vlastnosti, které lze filtrovat na stránce **Všichni uživatelé** :

- Typ uživatele: člen, Host, žádný
- Stav synchronizace adresářů: Ano, ne
- Typ vytvoření: Pozvánka, E-mail ověřeno, místní účet
- Stav pozvánky – čeká se na přijetí, přijato
- Název domény: zadejte název domény.
- Název společnosti: zadejte název společnosti.
- Jednotka pro správu: tuto možnost vyberte, pokud chcete omezit rozsah zobrazených uživatelů na jednu jednotku správy. Další informace najdete v tématu [Správa jednotek správy ve verzi Preview](directory-administrative-units.md).

### <a name="filtering-deleted-users-list"></a>Filtrování seznamu odstraněných uživatelů

Stránka **odstraněné uživatelé** obsahuje další filtry, které nejsou na stránce **Všichni uživatelé** . Níže jsou uvedené vlastnosti, které lze filtrovat na stránce **odstraněné uživatele** :

- Typ uživatele: člen, Host, žádný
- Stav synchronizace adresářů: Ano, ne
- Typ vytvoření: Pozvánka, E-mail ověřeno, místní účet
- Stav pozvánky: čeká na přijetí, přijato
- Datum odstranění: posledních 7, 14 nebo 30 dní
- Název domény: zadejte název domény.
- Název společnosti: zadejte název společnosti.
- Datum trvalého odstranění: posledních 7, 14 nebo 30 dní

## <a name="user-list-sorting"></a>Řazení seznamu uživatelů

Nyní můžete řadit podle názvu a hlavního názvu uživatele na stránce **Všichni uživatelé** a **odstraně uživatelé** . V seznamu **odstraněné uživatele** můžete také řadit podle data odstranění.

## <a name="user-list-counts"></a>Počty seznamů uživatelů

Celkový počet uživatelů můžete zobrazit na stránkách **Všichni uživatelé** a **odstraně uživatelé** . Při hledání nebo filtrování seznamů se počet aktualizuje tak, aby odrážel celkový počet nalezených uživatelů.

![Obrázek počtů seznamů uživatelů na stránce všichni uživatelé](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Časté otázky

Otázka | Odpověď
-------- | ------
Co se stane s hromadnou schopností pro uživatele a hosty? | Hromadné operace jsou pro uživatele a hosty stále k dispozici, včetně hromadného vytvoření, hromadného pozvání, hromadného odstranění a stažení uživatelů. Právě jsme je sloučili do nabídky s názvem **hromadné operace**. Možnosti **hromadných operací** najdete v horní části stránky **Všichni uživatelé** .
Co se stalo se zdrojovým sloupcem? | **Zdrojový** sloupec byl nahrazen dalšími sloupci, které obsahují podobné informace, a umožňuje vám tyto hodnoty nezávisle filtrovat. Mezi příklady patří **typ vytvoření**, **Synchronizovaný adresář** a **Vystavitel identity**.
Co se stalo se sloupcem s uživatelským jménem? | Sloupec **uživatelské jméno** je stále v seznamu, ale je přejmenován na **hlavní název uživatele**. Tím lépe odráží informace obsažené v tomto sloupci. Také si všimnete, že pro hosty B2B se teď zobrazí celý hlavní název uživatele (UPN). To odpovídá tomu, co byste získali v MS graphu.  
Proč mohu provést hledání "začíná na", a ne "obsahuje" hledání? | Existují určitá omezení, která nám brání v tom, aby vám umožnila provádět hledání "obsahuje". Dostali jsme zpětnou vazbu, takže můžete zůstat vyladěné.

## <a name="next-steps"></a>Další kroky

Uživatelské operace

- [Přidat nebo změnit informace o profilu](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Přidání nebo odstranění uživatelů](../fundamentals/add-users-azure-active-directory.md)

Hromadné operace

- [Stáhnout seznam uživatelů](users-bulk-download.md)
- [Hromadné přidání uživatelů](users-bulk-add.md)
- [Hromadné odstraňování uživatelů](users-bulk-delete.md)
- [Hromadné obnovování uživatelů](users-bulk-restore.md)
