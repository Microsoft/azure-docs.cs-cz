---
title: Upgrade složené entity – LUIS
description: Upgradujte složenou entitu na entitu strojového učení s procesem upgradu na portálu LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 46e9ece70d9f980065c719ee1205eb46591b45c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025238"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>Upgrade složené entity na entitu strojového učení

Upgradujte složenou entitu na entitu strojového učení a sestavte entitu, která přijímá více úplných předpovědi s lepším detvorbou pro ladění entit.

## <a name="current-version-model-restrictions"></a>Omezení modelu aktuální verze

Proces upgradu vytvoří entity strojového učení na základě existujících složených entit, které se ve vaší aplikaci našly, do nové verze vaší aplikace. To zahrnuje i podřízené prvky a role složených entit. Proces také přepne popisky v příkladu projevy k použití nové entity.

## <a name="upgrade-process"></a>Proces upgradu

Proces upgradu:
* Vytvoří novou entitu strojového učení pro každou složenou entitu.
* Podřízené entity:
    * Pokud se podřízená entita používá jenom ve složené entitě, přidá se jenom k entitě Machine-Learning.
    * Pokud se podřízená entita používá ve složených _a_ jako samostatná entita (označená například jako projevy), přidá se do verze jako entita a jako podentita do nové entity Machine-Learning.
    * Pokud podřízená entita používá roli, převede se každá role na subentitu se stejným názvem.
    * Pokud je podřízená entita entitou bez strojového učení (regulární výraz, seznam entit nebo předem sestavená entita), vytvoří se nová podentita se stejným názvem a nová podentita obsahuje funkci s použitím entity bez strojového učení s přidanou funkcí.
* Názvy jsou zachovány, ale musí být jedinečné na úrovni stejné subentity nebo na stejné úrovni. Přečtěte si [jedinečné omezení pro pojmenování](./luis-limits.md#name-uniqueness).
* Popisky v příkladu projevy jsou přepnuty na novou entitu strojového učení s podentitami.

Pro pochopení, jak se model mění, použijte následující graf:

|Starý objekt|Nový objekt|Poznámky|
|--|--|--|
|Složená entita|entita strojového učení se strukturou|Oba objekty jsou nadřazené objekty.|
|Podřízená entita složeného objektu je **Jednoduchá entita** .|subentity|Oba objekty jsou podřízené objekty.|
|Složená podřízená entita je **předem sestavená entita** , například číslo.|podentita s názvem předem sestavené entity, jako je číslo a subentita, má _funkci_ předem připravené číselné entity s možností omezení nastavenou na _hodnotu true_.|subentity obsahuje funkci s omezením na úrovni subentity.|
|Složená podřízená entita je **předem sestavená entita** , jako je číslo a předem sestavená entita má **roli** .|podentita s názvem role a subentita má funkci předem připravené číselné entity s možností omezení nastavenou na hodnotu true.|subentity obsahuje funkci s omezením na úrovni subentity.|
|Role|subentity|Název role se bude jmenovat jako název subentity. Subentita je přímým potomkem entity strojového učení.|

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

1. Po dokončení procesu budete mít novou výukovou verzi s novými entitami strojového učení. Pokud chcete novou entitu zobrazit, vyberte **vyzkoušet nové entity** .

    Pokud se pro novou entitu upgrade nebo školení nezdařil, poskytuje oznámení další informace.

1. Na stránce seznam entit jsou nové entity označeny jako **nové** vedle názvu typu.

## <a name="next-steps"></a>Další kroky

* [Autoři a spolupracovníci](luis-how-to-collaborate.md)