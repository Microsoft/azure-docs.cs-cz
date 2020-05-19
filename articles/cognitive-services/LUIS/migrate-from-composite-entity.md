---
title: Upgrade složené entity – LUIS
description: Upgradujte složenou entitu na počítačově získanou entitu pomocí procesu upgradu na portálu LUIS.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 9bbb03a2009bdcb6e2fa5d20aefadd4c7c99f025
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599422"
---
# <a name="upgrade-composite-entity-to-machine-learned-entity"></a>Upgrade složené entity na počítačově získanou entitu

Upgradujte složenou entitu na počítačově získanou entitu a sestavte entitu, která přijímá více úplných předpovědi s lepším detvorbou pro ladění entit.

## <a name="current-version-model-restrictions"></a>Omezení modelu aktuální verze

Proces upgradu vytvoří entity, které se naučily na stroji, na základě existujících složených entit, které se nacházejí ve vaší aplikaci, do nové verze vaší aplikace. To zahrnuje i podřízené prvky a role složených entit. Proces také přepne popisky v příkladu projevy k použití nové entity.

## <a name="upgrade-process"></a>Proces upgradu

Proces upgradu:
* Vytvoří novou entitu získanou počítačem pro každou složenou entitu.
* Podřízené entity:
    * Pokud se podřízená entita používá jenom ve složené entitě, přidají se jenom k entitě rozpoznané počítačem.
    * Pokud se podřízená entita používá ve složených _a_ jako samostatná entita (označená jako projevy), přidá se do verze jako entita a jako podentita k nové entitě získané počítačem.
    * Pokud podřízená entita používá roli, převede se každá role na subentitu se stejným názvem.
    * Pokud je podřízenou entitou entita nenáročné na počítač (regulární výraz, seznam entit nebo předem vytvořená entita), vytvoří se nová podentita se stejným názvem a nová podentita obsahuje funkci s použitím entity nenáročné na počítač s přidanou funkcí.
* Názvy jsou zachovány, ale musí být jedinečné na úrovni stejné subentity nebo na stejné úrovni. Přečtěte si [jedinečné omezení pro pojmenování](luis-boundaries.md#name-uniqueness).
* Popisky v příkladu projevy jsou přepnuty na nově získanou entitu počítač s podentitami.

Pro pochopení, jak se model mění, použijte následující graf:

|Starý objekt|Nový objekt|Poznámky|
|--|--|--|
|Složená entita|Entita získaná počítačem se strukturou|Oba objekty jsou nadřazené objekty.|
|Podřízená entita složeného objektu je **Jednoduchá entita** .|subentity|Oba objekty jsou podřízené objekty.|
|Složená podřízená entita je **předem sestavená entita** , například číslo.|podentita s názvem předem sestavené entity, jako je číslo a subentita, má _funkci_ předem připravené číselné entity s možností omezení nastavenou na _hodnotu true_.|subentity obsahuje funkci s omezením na úrovni subentity.|
|Složená podřízená entita je **předem sestavená entita** , jako je číslo a předem sestavená entita má **roli** .|podentita s názvem role a subentita má funkci předem připravené číselné entity s možností omezení nastavenou na hodnotu true.|subentity obsahuje funkci s omezením na úrovni subentity.|
|Role|subentity|Název role se bude jmenovat jako název subentity. Subentita je přímým potomkem uživatelsky rozpoznané entity.|

## <a name="begin-upgrade-process"></a>Zahájit proces upgradu

Před aktualizací se ujistěte, že:

* Změnit verze, pokud nepoužíváte správnou verzi pro upgrade


1. Zahajte proces upgradu z oznámení nebo můžete počkat do příštího naplánovaného dotazu.

    > [!div class="mx-imgBorder"]
    > ![Zahájení upgradu z oznámení](./media/update-composite-entity/notification-begin-update.png)

1. V místní nabídce vyberte **upgradovat nyní**.

1. Projděte si informace o **tom, co se stane po upgradu** , a pak vyberte **pokračovat**.

1. Vyberte ze seznamu složené entity, které chcete upgradovat, a pak vyberte **pokračovat**.

1. Můžete přesunout jakékoli provedené změny z aktuální verze do upgradované verze zaškrtnutím políčka.

1. Vyberte **pokračovat** a zahajte proces upgradu.

1. Indikátor průběhu indikuje stav procesu upgradu.

1. Po dokončení procesu se zobrazí nová poučená verze s novými entitami učenými počítačem. Pokud chcete novou entitu zobrazit, vyberte **vyzkoušet nové entity** .

    Pokud se pro novou entitu upgrade nebo školení nezdařil, poskytuje oznámení další informace.

1. Na stránce seznam entit jsou nové entity označeny jako **nové** vedle názvu typu.

## <a name="next-steps"></a>Další kroky

* [Autoři a spolupracovníci](luis-how-to-collaborate.md)