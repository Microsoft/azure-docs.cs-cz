---
title: Kurz pro hromadné pozvání uživatelů pro spolupráci B2B – Azure Active Directory | Microsoft Docs
description: V tomto kurzu se dozvíte, jak použít PowerShell a soubor CSV k hromadnému rozeslání pozvánek externím uživatelům spolupráce B2B služby Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 9/19/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec1a6ea8f363f2ddd4a9568700d5bff3330443c0
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128727"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Kurz: Hromadné pozvání uživatelů pro spolupráci s B2B v Azure AD (Preview)

|     |
| --- |
| Tento článek popisuje funkci veřejné verze Preview aplikace Azure Active Directory. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |


Pokud k práci se svými externími partnery používáte spolupráci B2B služby Azure Active Directory (Azure AD), můžete do organizace pozvat více uživatelů typu host najednou. V tomto kurzu se naučíte, jak pomocí Azure Portal posílat hromadné pozvánky externím uživatelům. Konkrétně provedete následující:

> [!div class="checklist"]
> * Pomocí **hromadných pozvání uživatelů (Preview)** Připravte textový soubor s oddělovači (. csv) pomocí předvoleb pro informace o uživateli a pozvánky.
> * Nahrání souboru. CSV do Azure AD
> * Ověříte, že jsou uživatelé v adresáři přidaní.

Pokud nemáte Azure Active Directory, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

Potřebujete alespoň dva testovací e-mailové účty, na které můžete pozvánky odeslat. Účty se nesmí nacházet ve vaší organizaci. Můžete použít libovolný typ účtu, včetně sociálních účtů jako jsou adresy gmail.com nebo outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Hromadné pozvání uživatelů typu Host

1. Přihlaste se k Azure Portal pomocí účtu, který je správcem uživatele v organizaci.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat**vyberte **Uživatelé** > **hromadné pozvánky**.
4. Na stránce **hromadně pozvat uživatele (Preview)** vyberte **Stáhnout** a získejte platný soubor. csv s vlastnostmi pozvánky.

    ![Tlačítko pro stažení hromadného pozvání](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Otevřete soubor. csv a přidejte řádek pro každého uživatele typu Host. Požadované hodnoty jsou:

   * **E-mailová adresa pro pozvání** – uživatel, který dostane pozvánku

   * **Adresa URL přesměrování** – adresa URL, na kterou se po přijetí pozvánky přesměruje pozvaní uživatelé

    ![Příklad souboru CSV se zadanými uživateli typu Host](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > V **přizpůsobené zprávě pozvánky** nepoužívejte čárky, protože jim zabrání v úspěšném analyzování zprávy.

6. Uložte soubor.
7. Na stránce **hromadně pozvat uživatele (Preview)** v části **nahrát soubor CSV**přejděte k souboru. Po výběru souboru se spustí ověření souboru. csv. 
8. Když se obsah souboru ověří, zobrazí se soubor se **úspěšně nahrál**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
9. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která přidá pozvánky. 
10. Chcete-li zobrazit stav úlohy, vyberte **kliknutím sem zobrazíte stav jednotlivých operací**. Nebo můžete v části **aktivita** vybrat možnost **výsledky hromadné operace (Preview)** . Chcete-li zobrazit podrobnosti o jednotlivých položkách řádku v rámci hromadné operace, vyberte hodnoty ve sloupcích **# úspěch**, **# selhání**nebo **Celkový počet požadavků** . Pokud dojde k chybám, zobrazí se důvody selhání.

    ![Příklad výsledků hromadné operace](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Po dokončení úlohy se zobrazí oznámení, že hromadná operace byla úspěšná.

## <a name="verify-guest-users-in-the-directory"></a>Ověřit uživatele typu Host v adresáři

Zkontrolujte, že uživatelé typu Host, které jste přidali, existují v adresáři Azure Portal nebo pomocí prostředí PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Zobrazit uživatele typu Host v Azure Portal

1. Přihlaste se k Azure Portal pomocí účtu, který je správcem uživatele v organizaci.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat** vyberte **Uživatele**.
4. V části **Zobrazit**vyberte možnost **Uživatelé typu Host** a ověřte, že jsou v seznamu uvedeni uživatelé, které jste přidali.

### <a name="view-guest-users-with-powershell"></a>Zobrazení uživatelů typu host s využitím PowerShellu

Spusťte následující příkaz:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Měli byste vidět uživatele, které jste pozvali v seznamu, s hlavním názvem uživatele (UPN) ve formátu *EmailAddress*#EXT #\@*Domain*. Například *lstokes_fabrikam. com\@# contoso.onmicrosoft.com*, kde contoso.onmicrosoft.com je organizace, ze které jste pozvánky odeslali.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete testovací uživatelské účty odstranit v adresáři na stránce Azure Portal na stránce Uživatelé tak, že vyberete zaškrtávací políčko vedle uživatele typu Host a pak vyberete **Odstranit**. 

Můžete také spustit následující příkaz prostředí PowerShell a odstranit uživatelský účet:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Příklad: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste hromadně odeslali pozvánky uživatelům typu host, kteří nepatří do vaší organizace. V dalším článku se dozvíte, jak funguje uplatnění pozvánky.

> [!div class="nextstepaction"]
> [Další informace o uplatnění pozvánky ve spolupráci B2B služby Azure AD](redemption-experience.md)
