---
title: Profil a ID uživatele pro použití s Azure Notebooks Preview
description: Jak vytvořit a spravovat profil uživatele a ID uživatele pomocí Azure Notebooks, které se stávají součástí adresy URL sdílených poznámkových bloků.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 30d70365fcc0c72df01b4dc059b6e0f4cc607bba
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109500"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Váš profil a ID uživatele pro Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

V rámci výkonného prostoru Azure Notebooks spolupráce váš uživatelský profil prezentuje vaši veřejnou image ostatním:

[![Stránka profilu Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Vaše ID uživatele je součástí adres URL, které používáte ke sdílení projektů a poznámkových bloků. Následující seznam popisuje různé vzory adres URL:

- `https://notebooks.azure.com/<user_id>`: Stránka profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Vaše projekty. Zobrazí se všechny projekty. jiní uživatelé uvidí pouze vaše veřejné projekty.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Soubory projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klony konkrétních projektů.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Náhled konkrétního poznámkového bloku nebo souboru ve formátu HTML.

## <a name="your-user-id"></a>ID uživatele

Při prvním přihlášení do Azure Notebooks se vašemu účtu automaticky přiřadí dočasné ID uživatele, například "anon-idr3ca". Pokud máte ID uživatele začínající na "anon-", Azure Notebooks vás vyzve, abyste ho změnili pokaždé, když se přihlásíte:

![Při přihlašování do Azure Notebooks vytvořit výzvu k vytvoření ID uživatele](media/accounts/create-user-id.png)

Příkaz **Konfigurovat ID uživatele** se také zobrazí vedle dočasného uživatelského jména:

![Příkaz Konfigurovat ID uživatele, který se zobrazí, když používáte dočasné ID](media/accounts/configure-user-id-command.png)

ID uživatele můžete také kdykoli změnit na stránce profilu.

ID uživatele musí být tvořeno čtyřmi a šestnáct písmeny, číslicemi a spojovníky. Nejsou povoleny žádné jiné znaky a ID uživatele nemůže začínat ani končit pomlčkou nebo použít více spojovníků v řádku. Vzhledem k tomu, že ID uživatelů jsou v rámci všech účtů Azure Notebooks jedinečné, může se zobrazit zpráva "ID uživatele se již používá". (Tato zpráva se zobrazí také v případě, že se pokusíte použít ochrannou známku společnosti Microsoft jako ID uživatele.) V těchto případech vyberte jiné ID uživatele.

> [!Important]
> Změna ID zruší platnost všech adres URL, které jste mohli sdílet pomocí předchozího ID. Můžete změnit ID zpátky na předchozí ID a znovu ověřit odkazy. Je ale možné, že jiný uživatel bude během této doby vymáhat nevyužité ID.

## <a name="your-profile"></a>Váš profil

Váš profil se skládá z veřejně viditelných informací na adrese URL `https://notebooks.azure.com/<user_id>` . Na stránce vašeho profilu se zobrazí také nedávno použité projekty a všechny projekty označených hvězdičkou.

Pokud chcete upravit svůj profil, použijte na stránce profilu příkaz **Upravit informace o profilu** . Následující části profilu:

| Sekce | Obsah |
| --- | --- |
| Profilová fotografie | Obrázek, který se zobrazí na stránce vašeho profilu. |
| Informace o účtu | Vaše zobrazované jméno, ID uživatele a veřejný e-mailový účet. Tento e-mailový účet poskytuje ostatním uživatelům střední význam pro vás a může se lišit od [účtu](azure-notebooks-user-account.md) , který používáte k přihlášení k Azure Notebooks sám. |
| Informace o profilu | Vaše poloha, společnost, název úlohy, web a stručný popis. |
| Profily sociální sítě | ID vašeho GitHubu, Twitteru a Facebooku, pokud je chcete sdílet. |
| Nastavení ochrany osobních údajů | Nabízí dva příkazy:<ul><li>**Exportovat profil**: vytvoří a stáhne soubor *. zip* obsahující všechny informace, které Azure Notebooks ukládá ve vašem profilu, včetně fotografií, informací o profilech a protokolů zabezpečení.</li><li>**Odstranit můj účet**: trvale odstraní všechny vaše osobní údaje uložené v Azure Notebooks.</li></ul> |
| Povolit funkce webu | Umožňuje řídit aspekty chování Azure Notebooks:<ul><li>**Sjednocený front-end pro poznámkové** bloky: umožňuje rychlejší spuštění poznámkového bloku a lepší trvalost.</li><li>**Spustit v JupyterLab ve výchozím** nastavení: Azure Notebooks poskytuje jednoduché uživatelské rozhraní, které je vhodné pro většinu uživatelů. JupyterLab poskytuje bohatší, ale složitější rozhraní pro zkušené uživatele.</li><li>**Web vNext**: povolí moderní webové rozložení uvedené v této dokumentaci.</li></ul> |

## <a name="next-steps"></a>Další kroky  

> [!div class="nextstepaction"]
> [Rychlý Start: Export Jupyter Notebook projektu](quickstart-export-jupyter-notebook-project.md)
