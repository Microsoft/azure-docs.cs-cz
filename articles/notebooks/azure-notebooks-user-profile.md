---
title: Profil uživatele a ID pro použití s poznámkovými bloky Azure
description: Jak vytvořit a spravovat profil uživatele a ID uživatele pomocí Azure Notebooks, které se stávají součástí adresy URL sdílených poznámkových bloků.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 0874fe8223ae9c49ccfe4e8efedf2620117a0f8c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277609"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Váš profil a ID uživatele pro poznámkové bloky Azure

Profil uživatele v prostoru výkonné a spolupráci poznámkových bloků Azure představuje veřejné image ostatním uživatelům:

[![stránku profilu Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Vaše ID uživatele je součástí adresy URL můžete použít ke sdílení projektů a poznámkové bloky. Následující seznam popisuje různé vzory adres URL:

- `https://notebooks.azure.com/<user_id>`: stránka profilu.
- `https://notebooks.azure.com/<user_id>/projects`: vaše projekty. Zobrazí všechny projekty; ostatní uživatelé uvidí jenom veřejné projekty.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: soubory projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: klony konkrétních projektů.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Náhled konkrétního poznámkového bloku nebo souboru ve formátu HTML.

## <a name="your-user-id"></a>Vaše ID uživatele

Při přihlašování do poznámkových bloků Azure poprvé, váš účet se automaticky přiřadí dočasné uživatelské ID, jako je například "anonymně idr3ca". Za předpokladu, máte ID uživatele, který začíná "anonymně-", poznámkových bloků Azure vás vyzve, abyste ho změnit pokaždé, když se přihlásíte:

![Zobrazit výzvu k vytvoření ID uživatele při přihlašování do poznámkových bloků Azure](media/accounts/create-user-id.png)

Příkaz **Konfigurovat ID uživatele** se také zobrazí vedle dočasného uživatelského jména:

![Příkaz ID uživatele, který se zobrazí, když používáte dočasné ID konfigurace](media/accounts/configure-user-id-command.png)

Vaše ID uživatele můžete kdykoli změnit také na stránce svého profilu.

ID uživatele musí být tvořeno čtyřmi a šestnáct písmeny, číslicemi a spojovníky. Žádná ostatní znaky jsou povolené a ID uživatele nesmí začínat nebo končit pomlčkou ani použít víc spojovníků za sebou. Vzhledem k tomu, že ID uživatelů jsou v rámci všech účtů Azure Notebooks jedinečné, může se zobrazit zpráva "ID uživatele se již používá". (Tato zpráva se zobrazí také v případě, že se pokusíte použít ochrannou známku společnosti Microsoft jako ID uživatele.) V těchto případech vyberte jiné ID uživatele.

> [!Important]
> Změna ID vašeho zruší platnost žádné adresy URL možná jste sdíleli pomocí předchozí ID. Můžete změnit ID zpět k předchozí ID na znovu ověřit odkazy. Nicméně je možné pro jiného uživatele, pokud chcete uplatnit nárok nepoužité ID do té doby.

## <a name="your-profile"></a>Váš profil

Váš profil se skládá z veřejně viditelných informací na adrese URL `https://notebooks.azure.com/<user_id>`. Stránku s vaším profilem také ukazuje naposledy použitých projekty i jakékoli označených hvězdičkou.

Pokud chcete upravit svůj profil, použijte na stránce profilu příkaz **Upravit informace o profilu** . V částech vašeho profilu jsou následující:

| Sekce | Obsah |
| --- | --- |
| Profilové fotky | Obrázek, který se zobrazí na stránce svého profilu. |
| Informace o účtu | Zobrazované jméno, ID uživatele a veřejné e-mailový účet. Tento e-mailový účet poskytuje ostatním uživatelům střední význam pro vás a může se lišit od [účtu](azure-notebooks-user-account.md) , který používáte k přihlášení k Azure Notebooks sám. |
| Informace o profilu | Vaše poloha, společnosti, pracovní pozice, web a krátký popis sami. |
| Sociální profily | GItHub, Twitter a Facebook ID, pokud chcete sdílet. |
| Nastavení ochrany osobních údajů | Poskytuje dva příkazy:<ul><li>**Exportovat profil**: vytvoří a stáhne soubor *. zip* obsahující všechny informace, které Azure Notebooks ukládá ve vašem profilu, včetně fotografií, informací o profilech a protokolů zabezpečení.</li><li>**Odstranit můj účet**: trvale odstraní všechny vaše osobní údaje uložené v Azure Notebooks.</li></ul> |
| Povolení funkce webu | Umožňuje ovládat chování poznámkových bloků Azure:<ul><li>**Sjednocený front-end pro poznámkové**bloky: umožňuje rychlejší spuštění poznámkového bloku a lepší trvalost.</li><li>**Spustit v JupyterLab ve výchozím**nastavení: Azure Notebooks poskytuje jednoduché uživatelské rozhraní, které je vhodné pro většinu uživatelů. JupyterLab poskytuje širší ale složitější rozhraní pro zkušené uživatele.</li><li>**Web vNext**: povolí moderní webové rozložení uvedené v této dokumentaci.</li></ul> |

## <a name="next-steps"></a>Další kroky  

> [!div class="nextstepaction"]
> [Kurz: vytvoření Jupyter poznámkového bloku pro provádění lineární regrese](tutorial-create-run-jupyter-notebook.md)
