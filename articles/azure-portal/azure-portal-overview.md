---
title: Přehled webu Azure Portal | Dokumentace Microsoftu
description: Naučte se, jak procházet Azure Portal a použít ji ke správě služeb.
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 6e176a8b16129cd35fc011e14fcb36038f7c0144
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000340"
---
# <a name="azure-portal-overview"></a>Přehled webu Azure Portal

Tento článek zavádí Azure Portal, identifikuje prvky stránky portálu a pomůže vám seznámit se s prostředím pro Azure Portal správu.

## <a name="what-is-the-azure-portal"></a>Co je Azure Portal?

Azure Portal je webová jednotná konzola, která poskytuje alternativu k nástrojům příkazového řádku. Pomocí Azure Portal můžete spravovat své předplatné Azure pomocí grafického uživatelského rozhraní. Můžete sestavovat, spravovat a monitorovat všechno z jednoduchých webových aplikací až po složitá cloudová nasazení, vytvářet vlastní řídicí panely pro organizované zobrazení prostředků a konfigurovat možnosti usnadnění pro dosažení optimálního prostředí.

Azure Portal je navržená tak, aby byla odolná a nepřetržitá dostupnost. Má přítomnost v každém datovém centru Azure a díky tomu je odolný proti selhání jednotlivých datových center a zároveň brání zpomalování sítě tím, že se blíží uživatelům. Azure Portal průběžně aktualizuje a nevyžaduje pro aktivity údržby žádné výpadky.

## <a name="azure-home"></a>Domovská stránka Azure

Jako noví předplatitelé služeb Azure se první věc, kterou vidíte po [přihlášení k portálu](https://portal.azure.com) , nachází na **domovské stránce Azure**. Tato stránka kompiluje prostředky, které vám pomůžou získat maximum z předplatného Azure. Zahrnuli jsme odkazy na bezplatné online kurzy, dokumentaci, základní služby a užitečné weby, které vám pozůstávají aktuální a spravovat změny vaší organizace. Pro rychlý a snadný přístup k probíhající práci se zobrazí také seznam naposledy navštívených prostředků. Tuto stránku nemůžete přizpůsobit, ale můžete zvolit, jestli se má jako výchozí zobrazení zobrazovat **řídicí panel** **Azure Home** nebo Azure. Při prvním přihlášení se zobrazí výzva v horní části stránky, kde můžete uložit preference.

![Snímek obrazovky znázorňující selektor výchozích zobrazení](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Řídicí panel Azure

Řídicí panely poskytují přehled o prostředcích v předplatném, které záleží na vás. Dostali jsme vám výchozí řídicí panel, který vám umožní začít. Tento řídicí panel můžete přizpůsobit tak, aby se prostředky, které použijete nejčastěji, zobrazily v jednom zobrazení. Všechny změny, které provedete ve výchozím zobrazení, budou mít vliv pouze na vaše prostředí. Můžete ale vytvořit další řídicí panely pro vlastní použití nebo publikovat vlastní řídicí panely a sdílet je s ostatními uživateli ve vaší organizaci. Další informace najdete v tématu [Vytvoření a sdílení řídicích panelů v Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Seznámení s portálem

Je užitečné porozumět základnímu rozložení portálu a způsobu, jak s ním pracovat. Tady zavedeme komponenty uživatelského rozhraní a některá z terminologie, kterou použijeme k poskytnutí pokynů. Podrobnější přehled portálu najdete v lekci kurzu [Navigace na portálu](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Azure Portal boční panel a záhlaví stránky jsou globální prvky, které jsou vždy přítomny. Tyto trvalé funkce jsou "prostředí" pro uživatelské rozhraní přidružené ke každé jednotlivé službě nebo funkci a záhlaví poskytuje přístup k globálním ovládacím prvkům. Stránka konfigurace (někdy označovaná jako "okno") pro určitý prostředek může mít také k dispozici v levém podokně, které vám umožní přesouvat se mezi funkcemi.

Obrázek níže popisuje základní prvky Azure Portal, z nichž každá je popsána v následující tabulce.

![Snímek obrazovky znázorňující zobrazení portálu na celé obrazovce a klíč k prvkům uživatelského rozhraní](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Klíč|Popis
|:---:|---|
|1|Záhlaví stránky Zobrazuje se v horní části každé stránky portálu a obsahuje globální prvky.|
|2| Globální vyhledávání. Panel hledání slouží k rychlému vyhledání konkrétního prostředku, služby nebo dokumentace.|
|3|Globální ovládací prvky. Podobně jako u všech globálních prvků jsou tyto funkce na portálu trvalé a zahrnují: Cloud Shell, filtr předplatných, oznámení, nastavení portálu, pomoc a podpora a poslat nám svůj názor.|
|4|Váš účet. Zobrazte si informace o vašem účtu, přepněte si adresáře, odhlaste se nebo se přihlaste pomocí jiného účtu.|
|5|Stín. Postranní panel je globální prvek, který vám pomůže s navigací mezi službami. Postranní panel lze sbalit, aby bylo možné se soustředit na pracovní podokno.|
|6|Hlavní ovládací prvek pro vytvoření nového prostředku v aktuálním předplatném. Vyhledejte nebo vyhledejte Azure Marketplace pro typ prostředku, který chcete vytvořit.|
|7|Seznam oblíbených položek. Přidejte nebo odeberte oblíbené položky na stránce **všechny služby** .|
|8|Levé podokno. Mnohé služby obsahují nabídku s levým podoknem, která vám může službu spravovat.|
|9|Panel příkazů. Ovládací prvky na panelu příkazů jsou kontextové na váš aktuální fokus.|
|10|S popisem cesty. Pomocí odkazů s popisem cesty můžete v pracovním postupu přejít zpět na úroveň.|
|11|Pracovní podokno.  Zobrazí podrobnosti o prostředku, který je aktuálně aktivní.|

## <a name="get-started-with-services"></a>Začínáme se službami

Pokud jste novým předplatitelem, budete muset vytvořit prostředek ještě předtím, než budete moct něco spravovat. Výběrem **+ vytvořit prostředek** zobrazíte služby, které jsou k dispozici v Azure Marketplace. Zde najdete aplikace a služby ze stovek poskytovatelů, které jsou certifikované pro spuštění v Azure.

Do bočního panelu jsme předem naplnili vaše oblíbené položky s odkazy na běžně používané služby.  Pokud chcete zobrazit všechny dostupné služby, vyberte **všechny služby** z tohoto bočního panelu.

> [!TIP]
> Nejrychlejší způsob, jak najít prostředek, službu nebo dokumentaci, je použít *vyhledávání* v globálním záhlaví. Pomocí odkazů s popisem cesty se vraťte na předchozí stránky.
>
Podívejte se na toto video, kde najdete ukázku použití globálního vyhledávání v Azure Portal.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Jak používat globální vyhledávání v Azure Portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Další postup

* Další informace o tom, kde spouštět Azure Portal v [podporovaných prohlížečích a zařízeních](../azure-portal/azure-portal-supported-browsers-devices.md)

* Zůstaňte připojení na cestách pomocí [mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
