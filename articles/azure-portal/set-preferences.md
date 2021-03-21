---
title: Správa nastavení a předvoleb na webu Azure Portal
description: Výchozí nastavení Azure Portal můžete změnit tak, aby splňovalo vaše vlastní předvolby. Nastavení zahrnují časový limit neaktivních relací, výchozí zobrazení, režim nabídky, kontrast, motiv, oznámení a jazyky a místní formáty.
keywords: nastavení, časový limit, jazyk, oblast
ms.date: 08/05/2020
ms.topic: how-to
ms.openlocfilehash: 591b1ce6c2c069f9c0266588bf32f7303bf49708
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588808"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Správa nastavení a předvoleb na webu Azure Portal

Výchozí nastavení Azure Portal můžete změnit tak, aby splňovalo vaše vlastní předvolby. Většina nastavení je k dispozici v nabídce **Nastavení** v záhlaví globální stránky.

![Snímek obrazovky zobrazující ikony záhlaví globálních stránek s zvýrazněným nastavením](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Zvolit výchozí předplatné

Když se přihlašujete k Azure Portal, můžete změnit odběr, který se ve výchozím nastavení otevře. To je užitečné v případě, že máte primární předplatné, se kterým pracujete, ale budete s nimi občas používat jiné. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Filtrovat seznam prostředků podle předplatného":::

1. V záhlaví globální stránky vyberte ikonu filtru adresářů a předplatných.

1. Po spuštění portálu vyberte předplatné, které chcete jako Výchozí předplatná. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="Po spuštění portálu vyberte předplatné, které chcete jako Výchozí předplatná."::: 


## <a name="choose-your-default-view"></a>Zvolit výchozí zobrazení 

Když se přihlásíte k Azure Portal, můžete změnit stránku, která se otevře ve výchozím nastavení.

![Snímek obrazovky zobrazující Azure Portal nastavení s zvýrazněným výchozím zobrazením](./media/set-preferences/default-view.png)

- **Domovskou stránku** nejde přizpůsobit.  Zobrazuje zástupce oblíbených služeb Azure a zobrazuje seznam aktuálně používaných prostředků. Poskytujeme vám také užitečné odkazy na prostředky, jako je Microsoft Learn a plán Azure.

- Řídicí panely můžete přizpůsobit tak, aby vytvořily pracovní prostor určený jen pro vás. Můžete například vytvořit řídicí panel, který je zaměřený na projekt, úlohu nebo roli. Pokud vyberete **řídicí panel**, vaše výchozí zobrazení přejde na naposledy použitý řídicí panel. Další informace najdete v tématu [Vytvoření a sdílení řídicích panelů v Azure Portal](azure-portal-dashboards.md).

## <a name="choose-a-portal-menu-mode"></a>Zvolit režim nabídky portálu

Výchozí režim pro nabídku portálu určuje, kolik místa se v nabídce portálu zabírá na stránce.

![Snímek obrazovky, který ukazuje, jak nastavit výchozí režim pro nabídku portálu.](./media/set-preferences/menu-mode.png)

- Když je nabídka portálu v režimu **plovoucí** , je skrytá, dokud ji nepotřebujete. Výběrem ikony nabídky otevřete nebo zavřete nabídku.

- Pokud zvolíte možnost **ukotvený režim** pro nabídku portálu, je vždy zobrazená. Nabídku můžete sbalit tak, aby poskytovala více pracovních prostorů.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Zvolit motiv nebo povolit vysoký kontrast

Zvolený motiv má vliv na barvy pozadí a písma, které se zobrazí v Azure Portal. Můžete vybrat jeden ze čtyř přednastavených barevných motivů. Vyberte jednotlivé miniatury, abyste našli motiv, který vám nejlépe vyhovuje.

Případně můžete zvolit jeden z motivů s vysokým kontrastem. Motiv vysokého kontrastu usnadňuje čtení Azure Portal uživatelům, kteří mají vizuální postižení. přepíší všechny ostatní výběry motivů.

![Snímek obrazovky zobrazující Azure Portal nastavení se zvýrazněnými motivy](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Povolit nebo zakázat místní oznámení

Oznámení jsou systémové zprávy týkající se vaší aktuální relace. Poskytují informace, jako je aktuální zůstatek kreditu, kdy se právě vytvořené prostředky stanou dostupnými, nebo potvrďte poslední akci, například. Po zapnutí místních oznámení se zprávy krátce zobrazí v horním rohu obrazovky. 

Pokud chcete povolit nebo zakázat místní oznámení, zaškrtněte nebo zrušte zaškrtnutí políčka **Povolit místní oznámení**.

![Snímek obrazovky zobrazující nastavení Azure Portal s zvýrazněnými automaticky vydanými oznámeními](./media/set-preferences/popup-notifications.png)

Pokud chcete číst všechna oznámení přijatá během vaší aktuální relace, vyberte **oznámení** z globálního záhlaví.

![Snímek obrazovky zobrazující Azure Portal globální záhlaví s zvýrazněnými oznámeními](./media/set-preferences/read-notifications.png)

Pokud chcete číst oznámení z předchozích relací, vyhledejte události v protokolu aktivit. Další informace najdete v tématu [zobrazení protokolu aktivit](../azure-monitor/essentials/activity-log.md#view-the-activity-log). 

## <a name="change-the-inactivity-timeout-setting"></a>Změna nastavení časového limitu nečinnosti

Nastavení časového limitu nečinnosti pomáhá chránit prostředky před neoprávněným přístupem, pokud zapomenete zabezpečit svoji pracovní stanici. Jakmile budete chvíli nečinné, automaticky se odhlásíte z Azure Portal relace. Jako jednotlivec můžete změnit nastavení časového limitu pro sebe. Pokud jste správce, můžete ho nastavit na úrovni adresáře pro všechny uživatele v adresáři.

### <a name="change-your-individual-timeout-setting-user"></a>Změna nastavení individuálního časového limitu (uživatel)

Vyberte rozevírací nabídku s možností **Odhlásit** se, pokud je neaktivní. Vyberte dobu, po jejímž uplynutí bude relace Azure Portal odhlášena, pokud nepracujete v nečinnosti.

![Snímek obrazovky s zvýrazněným nastavením neaktivního časového limitu](./media/set-preferences/inactive-signout-user.png)

Změna se automaticky uloží. Pokud nepracujete, vaše relace Azure Portal se odhlásí po uplynutí doby, kterou jste nastavili.

Pokud správce povolil zásady časových limitů nečinnosti, můžete nastavit vlastní, pokud je to méně, než nastavení na úrovni adresáře. Vyberte **přepsat zásady časového limitu nečinnosti adresáře** a pak nastavte časový interval.

![Snímek obrazovky s nastavením portálu s přepsáním nastavení zásad časového limitu nečinnosti adresáře, který se zvýrazní](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>Změna nastavení časového limitu adresáře (správce)

Správci v [roli globálního správce](../active-directory/roles/permissions-reference.md#global-administrator) můžou vymáhat maximální dobu nečinnosti před odhlášením relace. Nastavení časového limitu nečinnosti se vztahuje na úrovni adresáře. Nastavení se projeví pro nové relace. Nebude okamžitě platit pro všechny uživatele, kteří jsou už přihlášení. Další informace o adresářích najdete v tématu [přehled Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Pokud jste globální správce a chcete vyhodnotit nastavení časového limitu nečinnosti pro všechny uživatele Azure Portal, postupujte podle následujících kroků:

1. Vyberte text odkazu **konfigurace časového limitu na úrovni adresáře**.

    ![Snímek obrazovky s nastavením portálu s zvýrazněným textem odkazu](./media/set-preferences/settings-admin.png)

1. Na stránce **Konfigurovat časový limit nečinnosti na úrovni adresáře** vyberte **Povolit časový limit nečinnosti na úrovni adresáře, Azure Portal** zapnout nastavení.

1. V dalším kroku zadejte **hodiny** a **minuty** pro maximální dobu, po kterou může být uživatel nečinný, než se automaticky odhlásí jeho relace.

1. Vyberte **Použít**.

    ![Snímek obrazovky zobrazující stránku pro nastavení časového limitu nečinnosti na úrovni adresáře](./media/set-preferences/configure.png)

Pokud chcete potvrdit, že se správně nastavily zásady časového limitu nečinnosti, vyberte **oznámení** z hlavičky globální stránky. Ověřte, že je v seznamu uvedeno oznámení o úspěšném dokončení.

![Snímek obrazovky zobrazující zprávu o úspěšném oznámení pro časový limit nečinnosti na úrovni adresáře](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Obnovit výchozí nastavení

Pokud jste provedli změny nastavení Azure Portal a chcete je zahodit, vyberte **Obnovit výchozí nastavení**. Všechny změny, které jste provedli v nastavení portálu, budou ztraceny. Tato možnost nemá vliv na přizpůsobení řídicího panelu.

![Snímek obrazovky, který ukazuje obnovení výchozích nastavení](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Exportovat uživatelská nastavení

Informace o vlastním nastavení jsou uložené v Azure. Můžete exportovat následující uživatelská data:

* Soukromé řídicí panely v Azure Portal
* Uživatelská nastavení, jako jsou oblíbená předplatná nebo adresáře a poslední přihlášený adresář
* Motivy a další vlastní nastavení portálu

Pokud je chcete odstranit, je vhodné toto nastavení exportovat a zkontrolovat. Opětovné sestavení řídicích panelů nebo obnovení nastavení může být časově náročné.

Pokud chcete exportovat nastavení portálu, vyberte **exportovat všechna nastavení**.

![Snímek obrazovky znázorňující Export nastavení](./media/set-preferences/useful-links-export-settings.png)

Při exportu nastavení se vytvoří soubor *. JSON* , který obsahuje vaše uživatelská nastavení, jako je váš barevný motiv, oblíbené a privátní řídicí panely. Z důvodu dynamické povahy uživatelských nastavení a rizika poškození dat nemůžete importovat nastavení ze souboru *. JSON* .

## <a name="delete-user-settings-and-dashboards"></a>Odstranění uživatelských nastavení a řídicích panelů

Informace o vlastním nastavení jsou uložené v Azure. Můžete odstranit následující uživatelská data:

* Soukromé řídicí panely v Azure Portal
* Uživatelská nastavení, jako jsou oblíbená předplatná nebo adresáře a poslední přihlášený adresář
* Motivy a další vlastní nastavení portálu

Před odstraněním je vhodné nastavení exportovat a zkontrolovat. Opětovné sestavení řídicích panelů nebo opakované provádění vlastních nastavení může být časově náročné.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Pokud chcete odstranit nastavení portálu, vyberte **Odstranit všechna nastavení a privátní řídicí panely**.

![Snímek obrazovky, který ukazuje odstranění nastavení](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Změnit jazyk a místní nastavení

Existují dvě nastavení, která určují, jak se text v Azure Portal zobrazí: 
- Nastavení **jazyka** určuje jazyk, který vidíte pro text v Azure Portal. 

- **Oblastní formát** řídí způsob, jakým jsou zobrazena data, čas, čísla a měna.

Pokud chcete změnit jazyk, který se používá v Azure Portal, použijte rozevírací nabídku a vyberte ze seznamu dostupných jazyků.

Místní formát výběr se změní tak, aby zobrazoval místní možnosti jenom pro vybraný jazyk. Pokud chcete tento automatický výběr změnit, použijte rozevírací nabídku a vyberte požadovaný místní formát.

Pokud například jako jazyk vyberete angličtinu a pak jako místní formát vyberete možnost USA, bude se v této měně zobrazovat v amerických dolarech. Pokud jako jazyk vyberete angličtinu a pak jako místní formát vyberete možnost Evropa, v euru se zobrazí měna.

Pokud chcete aktualizovat nastavení jazyka a místního formátu, vyberte **použít** .

   ![Snímek obrazovky zobrazující nastavení jazyka a místního formátu](./media/set-preferences/language.png)

>[!NOTE]
>Tato jazyková a místní nastavení mají vliv pouze na Azure Portal. Odkazy na dokumentaci, které se otevřou na nové kartě nebo v okně, pomocí nastavení jazyka v prohlížeči určíte jazyk, který se má zobrazit.
>

## <a name="next-steps"></a>Další kroky

- [Klávesové zkratky v Azure Portal](azure-portal-keyboard-shortcuts.md)
- [Podporované prohlížeče a zařízení](azure-portal-supported-browsers-devices.md)
- [Přidání, odebrání a změna uspořádání oblíbených položek](azure-portal-add-remove-sort-favorites.md)
- [Vytváření a sdílení vlastních řídicích panelů](azure-portal-dashboards.md)
- [Série videí s postupy pro Azure Portal](azure-portal-video-series.md)
