---
title: Kurz hromadného pozvání uživatelů spolupráce B2B – Azure AD
description: V tomto kurzu se dozvíte, jak použít PowerShell a soubor CSV k hromadnému rozeslání pozvánek externím uživatelům spolupráce B2B služby Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 2/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c429648adeb0c81799bff2dca1650de965395a60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77166440"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Kurz: Hromadné pozvání uživatelů spolupráce Azure AD B2B (preview)

|     |
| --- |
| Tento článek popisuje funkci veřejného náhledu služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Od 12/22/2019 byla funkce Hromadné pozvání uživatelů (Preview) dočasně zakázána.
> V současné době není žádné známé datum, kdy bude tato funkce znovu povolena na webu Azure Portal. Pokud chcete pozvat uživatele typu Host hromadně pomocí PowerShellu, přečtěte si [kurz hromadnépozvánky B2B](bulk-invite-powershell.md) nebo [kód B2B a ukázky prostředí PowerShell](code-samples.md).

Pokud k práci se svými externími partnery používáte spolupráci B2B služby Azure Active Directory (Azure AD), můžete do organizace pozvat více uživatelů typu host najednou. V tomto kurzu se dozvíte, jak pomocí portálu Azure odesílat hromadné pozvánky externím uživatelům. Konkrétně provedete následující:

> [!div class="checklist"]
> * Použití **hromadného pozvání uživatelů (Preview)** k přípravě souboru s hodnotou oddělenou čárkami (.csv) s informacemi o uživateli a předvolbami pozvánek
> * Nahrání souboru .csv do služby Azure AD
> * Ověříte, že jsou uživatelé v adresáři přidaní.

Pokud azure active directory nemáte, vytvořte si před zahájením [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 

## <a name="prerequisites"></a>Požadavky

Potřebujete alespoň dva testovací e-mailové účty, na které můžete pozvánky odeslat. Účty se nesmí nacházet ve vaší organizaci. Můžete použít libovolný typ účtu, včetně sociálních účtů jako jsou adresy gmail.com nebo outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Hromadné pozvání uživatelů typu Host

1. Přihlaste se k portálu Azure pomocí účtu, který je správcem uživatele v organizaci.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Správa**vyberte**možnost Hromadná pozvánka** **uživatelů** > .
4. Na stránce **Hromadné pozvání uživatelů (Náhled)** vyberte **Stáhnout,** chcete-li získat platný soubor .csv s vlastnostmi pozvánky.

    ![Tlačítko hromadného pozvání ke stažení](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Otevřete soubor .csv a přidejte řádek pro každého uživatele typu Host. Požadované hodnoty jsou:

   * **E-mailová adresa pro pozvání** - uživatel, který obdrží pozvánku

   * **Adresa URL přesměrování** - adresa URL, na kterou je pozvaný uživatel po přijetí pozvánky předán

    ![Příklad souboru CSV se zadanými uživateli typu Host](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Ve **zprávě s přizpůsobenou pozvánkou** nepoužívejte čárky, protože zabrání úspěšné mu zanalýzy zprávy.

6. Uložte soubor.
7. Na stránce **Hromadné pozvání uživatelů (Preview)** v části **Nahrání souboru CSV**vyhledejte soubor. Když vyberete soubor, spustí se ověření souboru .csv. 
8. Po ověření obsahu souboru se **soubor úspěšně nahraje**. Pokud se jedná o chyby, je nutné je před odesláním úlohy opravit.
9. Když váš soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která přidá pozvánky. 
10. Chcete-li zobrazit stav úlohy, vyberte **možnost Kliknutím sem zobrazíte stav jednotlivých operací**. Nebo můžete vybrat **výsledky hromadné operace (náhled)** v části **Aktivita.** Podrobnosti o jednotlivých řádkových položkách v rámci hromadné operace vyberte hodnoty ve sloupcích **#Úspěch**, **Počet neúspěšných**nebo **Celkový počet požadavků.** Pokud došlo k chybám, budou uvedeny důvody selhání.

    ![Příklad výsledků hromadných operací](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Po dokončení úlohy se zobrazí oznámení, že hromadná operace proběhla úspěšně.

## <a name="verify-guest-users-in-the-directory"></a>Ověření uživatelů typu Host v adresáři

Zkontrolujte, jestli uživatelé typu Host, které jste přidali, existují v adresáři buď na webu Azure Portal, nebo pomocí PowerShellu.

### <a name="view-guest-users-in-the-azure-portal"></a>Zobrazení uživatelů typu Host na webu Azure Portal

1. Přihlaste se k portálu Azure pomocí účtu, který je správcem uživatele v organizaci.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat** vyberte **Uživatele**.
4. V části **Zobrazit**vyberte **pouze uživatele typu Host** a ověřte, zda jsou v seznamu uvedeni uživatelé, které jste přidali.

### <a name="view-guest-users-with-powershell"></a>Zobrazení uživatelů typu Host pomocí PowerShellu

Spusťte následující příkaz:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Měli byste vidět uživatele, které jste pozvali uvedené, s hlavním jménem uživatele\@(UPN) ve formátu *e-mailové adresy*#EXT#*domény*. Například *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, kde contoso.onmicrosoft.com je organizace, ze které jste odeslali pozvánky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete odstranit testovací uživatelské účty v adresáři v adresáři na webu Azure na stránce Uživatelé zaškrtnutím políčka vedle uživatele typu Host a výběrem možnosti **Odstranit**. 

Nebo můžete spustit následující příkaz PowerShellu a odstranit uživatelský účet:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Příklad: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste hromadně odeslali pozvánky uživatelům typu host, kteří nepatří do vaší organizace. V dalším článku se dozvíte, jak funguje uplatnění pozvánky.

> [!div class="nextstepaction"]
> [Další informace o uplatnění pozvánky ve spolupráci B2B služby Azure AD](redemption-experience.md)
