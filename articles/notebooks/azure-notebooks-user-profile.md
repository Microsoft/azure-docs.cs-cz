---
title: Profil uživatele a ID pro použití s azure notebooky Preview
description: Jak vytvořit a spravovat svůj uživatelský profil a ID uživatele pomocí poznámkových bloků Azure, které se stanou součástí adresy URL sdílených poznámkových bloků.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646275"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Váš profil a ID uživatele pro poznámkové bloky Azure Preview

V rámci výkonného prostoru pro spolupráci v poznámkových blocích Azure váš uživatelský profil představuje vaši veřejnou image ostatním:

[![Stránka profilu poznámkových bloků Azure](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Vaše ID uživatele je součástí adres URL, které používáte ke sdílení projektů a poznámkových bloků. Následující seznam popisuje různé vzory adres URL:

- `https://notebooks.azure.com/<user_id>`: Stránka vašeho profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Vaše projekty. Zobrazí se všechny projekty; ostatním uživatelům se zobrazí pouze vaše veřejné projekty.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Soubory projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klony konkrétních projektů.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Náhled HTML konkrétního poznámkového bloku nebo souboru.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>ID uživatele

Při prvním přihlášení k poznámkovým blokům Azure je vašemu účtu automaticky přiřazeno dočasné ID uživatele, například "anon-idr3ca". Pokud máte ID uživatele, které začíná na "anon-", poznámkové bloky Azure vás vyzve ke změně při každém přihlášení:

![Výzva k vytvoření ID uživatele při přihlašování k poznámkovým blokům Azure](media/accounts/create-user-id.png)

Vedle dočasného uživatelského jména se zobrazí také příkaz **Konfigurovat ID** uživatele:

![Konfigurace příkazu ID uživatele, který se zobrazí při použití dočasného ID](media/accounts/configure-user-id-command.png)

ID uživatele můžete také kdykoli změnit na stránce svého profilu.

ID uživatele se musí skládat ze čtyř až šestnácti písmen, čísel a spojovníků. Nejsou povoleny žádné další znaky a ID uživatele nemůže začínat ani končit pomlčkou ani používat více spojovníků v řádku. Vzhledem k tomu, že ID uživatelů jsou jedinečná ve všech účtech poznámkových bloků Azure, může se zobrazit zpráva "ID uživatele se už používá". (Zpráva se zobrazí také v případě, že se pokusíte použít ochrannou známku společnosti Microsoft jako ID uživatele.) V těchto případech zvolte jiné ID uživatele.

> [!Important]
> Změna id zruší platnost všech adres URL, které jste sdíleli pomocí předchozího ID. Chcete-li znovu ověřit platnost odkazů, můžete změnit své ID zpět na předchozí ID. Je však možné, aby jiný uživatel nárokovat nevyužité ID do té doby.

## <a name="your-profile"></a>Váš profil

Váš profil se skládá z veřejně zobrazitelných `https://notebooks.azure.com/<user_id>`informací na adrese URL . Na stránce profilu se také zobrazují nedávno použité projekty a všechny projekty s hvězdičkou.

Chcete-li upravit svůj profil, použijte příkaz **Upravit informace o profilu** na stránce profilu. Části vašeho profilu jsou následující:

| Sekce | Obsah |
| --- | --- |
| Profilová fotografie | Obrázek zobrazený na stránce vašeho profilu. |
| Informace o účtu | Vaše zobrazované jméno, ID uživatele a veřejný e-mailový účet. E-mailový účet zde poskytuje ostatním uživatelům způsob, jak vás kontaktovat a může se lišit od [účtu,](azure-notebooks-user-account.md) který používáte k přihlášení do samotných poznámkových bloků Azure. |
| Informace o profilu | Vaše poloha, společnost, pracovní pozice, webové stránky a krátký popis sebe sama. |
| Profily na sociálních sítích | Vaše GItHub, Twitter a Facebook ID, pokud je chcete sdílet. |
| Nastavení ochrany osobních údajů | Obsahuje dva příkazy:<ul><li>**Exportovat můj profil**: vytvoří a stáhne soubor *ZIP* obsahující všechny informace, které poznámkové bloky Azure uloží do vašeho profilu, včetně fotografie, informací o profilu a protokolů zabezpečení.</li><li>**Odstranit můj účet:** Trvale odstraní všechny vaše osobní údaje uložené v poznámkových blocích Azure.</li></ul> |
| Povolení funkcí webu | Umožňuje řídit aspekty chování poznámkových bloků Azure:<ul><li>**Unified Frontend pro poznámkové bloky**: umožňuje rychlejší spuštění notebooku a lepší trvalost.</li><li>**Ve výchozím nastavení spouštění v JupyterLabu**: Ve výchozím nastavení poskytují poznámkové bloky Azure jednoduché uživatelské rozhraní, které je vhodné pro většinu uživatelů. JupyterLab poskytuje bohatší, ale složitější rozhraní pro zkušené uživatele.</li><li>**VNext Website**: umožňuje modernizované webové rozložení uvedené v této dokumentaci.</li></ul> |

## <a name="next-steps"></a>Další kroky  

> [!div class="nextstepaction"]
> [Kurz: vytvoření spuštění notebooku Jupyter dělat lineární regrese](tutorial-create-run-jupyter-notebook.md)
