---
title: Nastavte předvolby Azure Portal | Microsoft Docs
description: Výchozí nastavení Azure Portal můžete změnit tak, aby splňovalo vaše vlastní předvolby. Nastavení zahrnují časový limit neaktivních relací, výchozí zobrazení, režim nabídky, kontrast, motiv, oznámení a jazyky a místní formáty.
services: azure-portal
keywords: nastavení, časový limit, jazyk, oblast
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: d4c675ab18a7a9231229ce0c7de9a7450dc1baa8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763954"
---
# <a name="set-your-azure-portal-preferences"></a>Nastavení předvoleb pro Azure Portal

Výchozí nastavení Azure Portal můžete změnit tak, aby splňovalo vaše vlastní předvolby. Každé z následujících nastavení lze změnit:

* [Časový limit neaktivních relací](#change-the-idle-duration-for-inactive-sign-out)
* [Výchozí zobrazení](#choose-your-default-view)
* [Režim nabídky portálu](#choose-a-portal-menu-mode)
* [Motiv barvy a vysokého kontrastu](#choose-a-theme)
* [Místní oznámení](#enable-or-disable-pop-up-notifications)
* [Jazyk a místní formát](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Změnit obecné nastavení portálu

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. V záhlaví globální stránky vyberte **Nastavení** .

    ![Snímek obrazovky zobrazující ikony záhlaví globálních stránek s zvýrazněným nastavením](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Změna doby nečinnosti pro neaktivní odhlašování

Nastavení časového limitu nečinnosti pomáhá chránit prostředky před neoprávněným přístupem, pokud zapomenete zabezpečit svoji pracovní stanici. Jakmile budete chvíli nečinné, automaticky se odhlásíte z Azure Portal relace.

Vyberte rozevírací nabídku s možností **Odhlásit**se, pokud je neaktivní. Vyberte dobu, po jejímž uplynutí bude relace Azure Portal odhlášena, pokud nepracujete v nečinnosti.

   ![Snímek obrazovky s zvýrazněným nastavením neaktivního časového limitu](./media/set-preferences/inactive-signout-user.png)

Změna se automaticky uloží. Pokud nepracujete, vaše relace Azure Portal se odhlásí po uplynutí doby, kterou jste nastavili.

Toto nastavení může nastavit také správce na úrovni adresáře, aby se vynutila maximální doba nečinnosti. Pokud správce nastavil časový limit na úrovni adresáře, můžete si pořád nastavit vlastní dobu neaktivního přihlášení. Vyberte nastavení času, které je menší než nastavení na úrovni adresáře.

Pokud váš správce povolil zásady časového limitu nečinnosti, zaškrtněte políčko **přepsat zásady časového limitu nečinnosti adresáře** . Nastavte časový interval, který je menší než nastavení zásad.

   ![Snímek obrazovky s nastavením portálu s přepsáním nastavení zásad časového limitu nečinnosti adresáře, který se zvýrazní](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Pokud jste správce a chcete vymáhat nastavení neaktivního časového limitu pro všechny uživatele Azure Portal, přečtěte si téma [nastavení časového limitu nečinnosti na úrovni adresáře pro uživatele Azure Portal](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Zvolit výchozí zobrazení 

Když se přihlásíte k Azure Portal, můžete změnit stránku, která se ve výchozím nastavení otevře.

   ![Snímek obrazovky zobrazující Azure Portal nastavení s zvýrazněným výchozím zobrazením](./media/set-preferences/default-view.png)

Výchozí nastavení zobrazení určuje, které Azure Portal zobrazení se zobrazí při přihlášení. Můžete se rozhodnout, že ve výchozím nastavení otevřete Azure Home nebo zobrazení řídicího panelu.

* **Domovskou stránku** nejde přizpůsobit.  Zobrazuje zástupce oblíbených služeb Azure a zobrazuje seznam aktuálně používaných prostředků. Poskytujeme vám také užitečné odkazy na prostředky, jako je Microsoft Learn a plán Azure.
* Řídicí panely můžete přizpůsobit tak, aby vytvořily pracovní prostor určený jen pro vás. Můžete například vytvořit řídicí panel, který je zaměřený na projekt, úlohu nebo roli. Pokud vyberete **řídicí panel**, vaše výchozí zobrazení přejde na naposledy použitý řídicí panel.

### <a name="choose-a-portal-menu-mode"></a>Zvolit režim nabídky portálu

Výchozí režim pro nabídku portálu určuje, kolik místa se v nabídce portálu zabírá na stránce.

* Když je nabídka portálu v režimu **plovoucí** , je skrytá, dokud ji nepotřebujete. Výběrem ikony nabídky otevřete nebo zavřete nabídku.
* Pokud zvolíte možnost **ukotvený** režim pro nabídku portálu, je vždy zobrazená. Nabídku můžete sbalit tak, aby poskytovala více pracovních prostorů. 

### <a name="choose-a-theme"></a>Zvolit motiv

Zvolený motiv má vliv na barvy pozadí a písma, které se zobrazí v Azure Portal. Můžete vybrat jeden ze čtyř přednastavených barevných motivů. Vyberte jednotlivé miniatury, abyste našli motiv, který vám nejlépe vyhovuje.

   ![Snímek obrazovky zobrazující Azure Portal nastavení se zvýrazněnými motivy](./media/set-preferences/theme.png)

Místo toho můžete zvolit jeden z motivů s vysokým kontrastem. Nastavení vysokého kontrastu usnadňuje čtení Azure Portal pro lidi, kteří mají vizuální narušení a přepisují všechny ostatní výběry motivů. Další informace najdete v tématu [Zapnutí vysokého kontrastu nebo změna motivu](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Povolit nebo zakázat místní oznámení

Oznámení jsou systémové zprávy týkající se vaší aktuální relace. Poskytují informace, jako je aktuální zůstatek kreditu, kdy se právě vytvořené prostředky stanou dostupnými, nebo potvrďte poslední akci, například. Po zapnutí místních oznámení se zprávy krátce zobrazí v horním rohu obrazovky. 

Pokud chcete povolit nebo zakázat místní oznámení, zaškrtněte políčko Povolit místní oznámení nebo zrušte zaškrtnutí políčka **Povolit oznámení** .

   ![Snímek obrazovky zobrazující nastavení Azure Portal s zvýrazněnými automaticky vydanými oznámeními](./media/set-preferences/popup-notifications.png)

Pokud chcete číst všechna oznámení přijatá během vaší aktuální relace, vyberte **oznámení** z globálního záhlaví.

   ![Snímek obrazovky zobrazující Azure Portal globální záhlaví s zvýrazněnými oznámeními](./media/set-preferences/read-notifications.png)

Pokud chcete číst oznámení z předchozích relací, vyhledejte události v protokolu aktivit. Pokud se chcete dozvědět víc, přečtěte si téma [zobrazení a načtení událostí protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Nastavení v rámci užitečných odkazů

Pokud jste provedli změny nastavení Azure Portal a chcete je zahodit, vyberte **Obnovit výchozí nastavení**. Všechny změny, které jste provedli v nastavení portálu, budou ztraceny. Tato možnost nemá vliv na přizpůsobení řídicího panelu.

Další informace o **exportu všech nastavení** nebo **odstranění všech nastavení a privátních řídicích panelů**najdete v tématu [Export nebo odstranění uživatelských nastavení](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Změnit jazyk a místní nastavení

Existují dvě nastavení, která určují, jak se zobrazí text v Azure Portal. Nastavení **jazyka** určuje jazyk, který vidíte pro text v Azure Portal. **Oblastní formát** řídí způsob, jakým jsou zobrazena data, čas, čísla a měna.

Pokud chcete změnit jazyk, který se používá v Azure Portal, použijte rozevírací nabídku a vyberte ze seznamu dostupných jazyků.

Místní formát výběr se změní tak, aby zobrazoval místní možnosti jenom pro vybraný jazyk. Pokud chcete tento automatický výběr změnit, použijte rozevírací nabídku a vyberte požadovaný místní formát.

Pokud například jako jazyk vyberete angličtinu a pak jako místní formát vyberete možnost USA, bude se v této měně zobrazovat v amerických dolarech. Pokud jako jazyk vyberete angličtinu a pak jako místní formát vyberete možnost Evropa, v euru se zobrazí měna.

Pokud chcete aktualizovat nastavení jazyka a místního formátu, vyberte **použít** .

   ![Snímek obrazovky zobrazující nastavení jazyka a místního formátu](./media/set-preferences/language.png)

>[!NOTE]
>Tato jazyková a místní nastavení mají vliv pouze na Azure Portal. Odkazy na dokumentaci, které se otevřou na nové kartě nebo v okně, budou pomocí nastavení jazyka v prohlížeči určovat jazyk, který se má zobrazit.
>

## <a name="next-steps"></a>Další kroky

* [Vytváření a sdílení vlastních řídicích panelů](azure-portal-dashboards.md)
* [Série videí s postupy pro Azure Portal](azure-portal-video-series.md)
