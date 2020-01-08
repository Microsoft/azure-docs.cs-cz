---
title: Profil a ID uživatele pro použití s Azure Notebooks Preview
description: Jak vytvořit a spravovat profil uživatele a ID uživatele pomocí Azure Notebooks, které se stávají součástí adresy URL sdílených poznámkových bloků.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646275"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Váš profil a ID uživatele pro Azure Notebooks Preview

Profil uživatele v prostoru výkonné a spolupráci poznámkových bloků Azure představuje veřejné image ostatním uživatelům:

[![stránku profilu Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Vaše ID uživatele je součástí adresy URL můžete použít ke sdílení projektů a poznámkové bloky. Následující seznam popisuje různé vzory adres URL:

- `https://notebooks.azure.com/<user_id>`: Stránka vašeho profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Vaše projekty. Zobrazí všechny projekty; ostatní uživatelé uvidí jenom veřejné projekty.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Soubory projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klony konkrétních projektů.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Náhled HTML konkrétní Poznámkový blok nebo souboru.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>Vaše ID uživatele

Při přihlašování do poznámkových bloků Azure poprvé, váš účet se automaticky přiřadí dočasné uživatelské ID, jako je například "anonymně idr3ca". Za předpokladu, máte ID uživatele, který začíná "anonymně-", poznámkových bloků Azure vás vyzve, abyste ho změnit pokaždé, když se přihlásíte:

![Zobrazit výzvu k vytvoření ID uživatele při přihlašování do poznámkových bloků Azure](media/accounts/create-user-id.png)

A **nakonfigurovat ID uživatele** příkaz také zobrazí vedle názvu dočasného uživatele:

![Příkaz ID uživatele, který se zobrazí, když používáte dočasné ID konfigurace](media/accounts/configure-user-id-command.png)

Vaše ID uživatele můžete kdykoli změnit také na stránce svého profilu.

ID uživatele musí být tvořeno čtyřmi a šestnáct písmeny, číslicemi a spojovníky. Žádná ostatní znaky jsou povolené a ID uživatele nesmí začínat nebo končit pomlčkou ani použít víc spojovníků za sebou. Vzhledem k tomu, že ID uživatelů jsou v rámci všech účtů Azure Notebooks jedinečné, může se zobrazit zpráva "ID uživatele se již používá". (Tato zpráva se zobrazí také v případě, že se pokusíte použít ochrannou známku společnosti Microsoft jako ID uživatele.) V těchto případech vyberte jiné ID uživatele.

> [!Important]
> Změna ID vašeho zruší platnost žádné adresy URL možná jste sdíleli pomocí předchozí ID. Můžete změnit ID zpět k předchozí ID na znovu ověřit odkazy. Nicméně je možné pro jiného uživatele, pokud chcete uplatnit nárok nepoužité ID do té doby.

## <a name="your-profile"></a>Váš profil

Váš profil se skládá z veřejně zobrazitelné informace na adrese URL, `https://notebooks.azure.com/<user_id>`. Stránku s vaším profilem také ukazuje naposledy použitých projekty i jakékoli označených hvězdičkou.

Chcete-li upravit svůj profil, použijte **úprava informací profilu** příkaz na stránce svého profilu. V částech vašeho profilu jsou následující:

| Sekce | Obsah |
| --- | --- |
| Profilové fotky | Obrázek, který se zobrazí na stránce svého profilu. |
| Informace o účtu | Zobrazované jméno, ID uživatele a veřejné e-mailový účet. E-mailový účet poskytuje ostatním uživatelům mean se vás kontaktovat a může se lišit od [účet](azure-notebooks-user-account.md) používáte k přihlášení do Azure poznámkových bloků, samotného. |
| Informace o profilu | Vaše poloha, společnosti, pracovní pozice, web a krátký popis sami. |
| Sociální profily | GItHub, Twitter a Facebook ID, pokud chcete sdílet. |
| Nastavení ochrany osobních údajů | Poskytuje dva příkazy:<ul><li>**Exportovat profil**: vytvoří a stáhne *ZIP* soubor, který obsahuje všechny informace, které se uloží poznámkových bloků Azure ve vašem profilu, včetně fotografie, informace o profilu a protokoly zabezpečení.</li><li>**Odstranit tento účet**: trvale odstraní všechny vaše osobní údaje uložené v poznámkových bloků Azure.</li></ul> |
| Povolení funkce webu | Umožňuje ovládat chování poznámkových bloků Azure:<ul><li>**Sjednocené front-endu pro poznámkové bloky**: umožňuje rychlejší spuštění poznámkového bloku a lepší trvalosti.</li><li>**Ve výchozím nastavení spouští v JupyterLab**: ve výchozím nastavení, poznámkových bloků Azure poskytuje jednoduché uživatelské rozhraní, který je vhodný pro většinu uživatelů. JupyterLab poskytuje širší ale složitější rozhraní pro zkušené uživatele.</li><li>**Web VNext**: Povolí rozložení modernizované webové stránky zobrazené v této dokumentaci.</li></ul> |

## <a name="next-steps"></a>Další kroky  

> [!div class="nextstepaction"]
> [Kurz: vytvoření spustit Poznámkový blok Jupyter provedete lineární regrese](tutorial-create-run-jupyter-notebook.md)
