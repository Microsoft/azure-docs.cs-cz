---
title: Nastavení předvoleb portálu Azure | Dokumenty společnosti Microsoft
description: Výchozí nastavení portálu Azure můžete změnit tak, aby vyhovovalo vašim vlastním preferencím. Nastavení zahrnují neaktivní časový limit relace, výchozí zobrazení, režim nabídky, kontrast, motiv, oznámení a jazykové a regionální formáty.
services: azure-portal
keywords: nastavení, časový čas, jazyk, regionální
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310675"
---
# <a name="set-your-azure-portal-preferences"></a>Nastavení předvoleb pro Azure Portal

Můžete změnit výchozí nastavení portálu Azure tak, aby vyhovovaly vašim vlastním preferencím. Každé z níže uvedených nastavení lze změnit:

* [Časový oposa neaktivní relace](#change-the-idle-duration-for-inactive-sign-out)
* [Výchozí zobrazení](#choose-your-default-view)
* [Režim nabídky Portál](#choose-a-portal-menu-mode)
* [Barevný a vysoký kontrastní motiv](#choose-a-theme)
* [Automaticky otevíraná oznámení](#enable-or-disable-pop-up-notifications)
* [Jazyk a místní formát](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Změna obecného nastavení portálu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V záhlaví globální stránky vyberte **Nastavení.**

    ![Snímek obrazovky se zvýrazněnými ikonami záhlaví globální stránky](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Změna doby trvání nečinnosti pro neaktivní odhlášení

Nastavení časového omezení nečinnosti pomáhá chránit prostředky před neoprávněným přístupem, pokud zapomenete zabezpečit pracovní stanici. Po chvíli nečinnosti se automaticky odhlásila z relace portálu Azure.

Vyberte rozevírací **políčko Odhlásit mě, když jsem neaktivní**. Zvolte dobu, po jejímž uplynutí se vaše relace na portálu Azure odhlásila, pokud jste nečinní.

   ![Snímek obrazovky s nastavením portálu se zvýrazněným neaktivním nastavením časového času](./media/set-preferences/inactive-signout-user.png)

Změna se uloží automaticky. Pokud jste nečinní, vaše relace portálu Azure se po nastavené době odhlásí.

Toto nastavení může také provést správce na úrovni adresáře k vynucení maximální doby nečinnosti. Pokud správce provedl nastavení časového času na úrovni adresáře, můžete stále nastavit vlastní dobu trvání neaktivního odhlášení. Zvolte nastavení času, které je menší než nastavení na úrovni adresáře.

Pokud správce povolil zásadu časového času nečinnosti, zaškrtněte políčko **Přepsat zásady časového času nečinnosti adresáře.** Nastavte časový interval, který je menší než nastavení zásad.

   ![Snímek obrazovky se zvýrazněným nastavením časového času nečinnosti adresáře](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Pokud jste správce a chcete vynutit neaktivní nastavení časového času pro všechny uživatele webu Azure Portal, přečtěte si část [Nastavení časového režimu nečinnosti na úrovni adresáře pro uživatele portálu Azure Portal.](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Volba výchozího zobrazení 

Stránku, která se otevře ve výchozím nastavení při přihlášení k portálu Azure, můžete změnit.

   ![Snímek obrazovky se zvýrazněným nastavením portálu Azure se zvýrazněným výchozím zobrazením](./media/set-preferences/default-view.png)

Výchozí nastavení zobrazení řídí, které zobrazení portálu Azure se zobrazí při přihlášení. Azure Home můžete otevřít ve výchozím nastavení nebo zobrazení řídicího panelu.

* **Domov** nelze přizpůsobit.  Zobrazuje zástupce oblíbených služeb Azure a uvádí zdroje, které jste použili naposledy. Poskytujeme také užitečné odkazy na prostředky, jako je Microsoft Learn a plán Azure.
* Řídicí panely lze přizpůsobit tak, aby vytvořily pracovní prostor navržený právě pro vás. Můžete například vytvořit řídicí panel zaměřený na projekt, úkol nebo roli. Pokud vyberete **řídicí panel**, vaše výchozí zobrazení přejde na naposledy použitý řídicí panel.

### <a name="choose-a-portal-menu-mode"></a>Volba režimu nabídky portálu

Výchozí režim nabídky portálu určuje, kolik místa nabídka portálu zabírá na stránce.

* Když je nabídka portálu v režimu **informačního rámečku,** je skrytá, dokud ji nepotřebujete. Vyberte ikonu nabídky, kterou chcete otevřít nebo zavřít.
* Pokud pro nabídku portálu zvolíte **ukotvený** režim, bude vždy viditelný. Můžete sbalit nabídku a poskytnout tak více pracovního prostoru. 

### <a name="choose-a-theme"></a>Zvolit motiv

Motiv, který zvolíte, ovlivní barvy pozadí a písma, které se zobrazí na webu Azure Portal. Můžete si vybrat z jedné ze čtyř přednastavených barevných motivů. Vyberte jednotlivé miniatury a najděte motiv, který vám nejlépe vyhovuje.

   ![Snímek obrazovky se zvýrazněným nastavením portálu Azure se zvýrazněnými motivy](./media/set-preferences/theme.png)

Místo toho můžete zvolit jeden z vysoce kontrastních motivů. Nastavení vysokého kontrastu usnadňují čtení portálu Azure pro uživatele se zrakovým postižením a přepisují všechny ostatní výběry motivů. Další informace naleznete v [tématu Zapnutí vysokého kontrastu nebo změna motivu](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Povolení nebo zakázání automaticky otevíraných oznámení

Oznámení jsou systémové zprávy související s aktuální relací. Poskytují informace, jako je aktuální zůstatek kreditu, kdy jsou například k dispozici zdroje, které jste právě vytvořili, nebo potvrďte poslední akci. Když jsou zapnutá automaticky otevíraná oznámení, zprávy se krátce zobrazí v horním rohu obrazovky. 

Chcete-li povolit nebo zakázat automaticky otevíraná oznámení, zaškrtněte nebo zrušte zaškrtnutí políčka **Povolit automaticky otevíraná oznámení.**

   ![Snímek obrazovky se zvýrazněným nastavením portálu Azure se zvýrazněnými automaticky otevíranými oznámeními](./media/set-preferences/popup-notifications.png)

Pokud chcete přečíst všechna oznámení přijatá během aktuální relace, vyberte **Oznámení** z globálního záhlaví.

   ![Snímek obrazovky se zvýrazněným záhlavím portálu Azure Portal se zvýrazněnými oznámeními](./media/set-preferences/read-notifications.png)

Pokud chcete číst oznámení z předchozích relací, vyhledejte události v protokolu aktivit. Další informace najdete v zobrazení [a načtení událostí protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Nastavení pod užitečnými odkazy

Pokud jste provedli změny v nastavení portálu Azure a chcete je zahodit, vyberte **Obnovit výchozí nastavení**. Všechny změny provedené v nastavení portálu budou ztraceny. Tato možnost nemá vliv na vlastní nastavení řídicího panelu.

Další informace o **exportu všech nastavení** nebo **odstranění všech nastavení a soukromých řídicích panelů**naleznete v [tématu Export nebo odstranění uživatelských nastavení](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Změna jazyka a místního nastavení

Existují dvě nastavení, která řídí, jak se bude text na webu Azure Portal zjevovat. Nastavení **jazyk** řídí jazyk, který se zobrazí pro text na webu Azure Portal. **Místní formát** řídí způsob zobrazení kalendářních dat, času, čísel a měny.

Pokud chcete změnit jazyk, který se používá na webu Azure Portal, vyberte pomocí rozevíracího seznamu ze seznamu dostupných jazyků.

Výběr regionálního formátu se změní tak, aby zobrazoval místní možnosti pouze pro vybraný jazyk. Chcete-li tento automatický výběr změnit, vyberte pomocí rozevíracího seznamu požadovaný regionální formát.

Pokud například jako jazyk vyberete angličtinu a potom jako regionální formát vyberete Spojené státy, zobrazí se měna v amerických dolarech. Pokud jako jazyk vyberete angličtinu a pak jako regionální formát vyberete Evropu, zobrazí se měna v eurech.

Vyberte **Použít,** chcete-li aktualizovat nastavení jazyka a místního formátu.

   ![Snímek obrazovky s nastavením jazyka a regionálního formátu](./media/set-preferences/language.png)

>[!NOTE]
>Tato jazyková a místní nastavení ovlivní jenom portál Azure. Odkazy na dokumentaci, které se otevřou na nové kartě nebo v okně, budou používat k určení jazyka, který se má zobrazit, nastavení jazyka prohlížeče.
>

## <a name="next-steps"></a>Další kroky

* [Vytváření a sdílení vlastních řídicích panelů](azure-portal-dashboards.md)
* [Série videí s postupy pro Azure Portal](azure-portal-video-series.md)
