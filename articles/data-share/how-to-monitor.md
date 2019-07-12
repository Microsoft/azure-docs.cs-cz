---
title: Jak monitorovat Azure dat sdílené složky ve verzi Preview
description: Jak monitorovat Azure dat sdílené složky ve verzi Preview
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789095"
---
# <a name="monitor-azure-data-share-preview"></a>Sdílené složky Azure dat monitorování ve verzi Preview 

Tento článek vysvětluje, jak můžete monitorovat vaše data sdílených složek pomocí Azure Data sdílené složky ve verzi Preview. Jako poskytovatel dat budete moct monitorovat různé aspekty dat sdílení vztahy. Podrobnosti, jako například, jestli vaše spotřebitelé dat jste přijali vaši pozvánku ke sdílené složce dat, stejně jako Určuje, zda jste vytvořili sdílenou složku odběr a začali používat vaše data jsou všechny dostupné pro monitorování. 

Jako spotřebitel dat můžete monitorovat snímky, které jste aktivovali do vašeho předplatného Azure. 

## <a name="monitor-invitation-status"></a>Stav monitorování pozvánky

Zobrazit stav vaší sdílené složky pozvánky data tak, že přejdete do sdílených složek odeslané -> pozvánky. 

![Stav žádosti](./media/invitation-status.png "stav žádosti") 

Existují tři stavy, které vaši pozvánku lze:

* Čeká na vyřízení - příjemce dat sdílené položky ještě nepřijal e-mailové pozvánce.
* Přijato – přijal Data sdílené složky příjemce e-mailové pozvánce.
* Zamítnuto - odmítl příjemce dat sdílené složky e-mailové pozvánce.

> [!IMPORTANT]
> Pokud po již byla přijata se odstranit pozvánku, není ekvivalentní k odvolání přístupu. Pokud chcete zastavit budoucí snímky zkopírování do svého účtu úložiště spotřebitelé dat, musíte odebrat přístup prostřednictvím *sdílet předplatná* kartu. 

## <a name="monitor-share-subscriptions"></a>Monitorování sdílení předplatná

Zobrazení stavu z vašich předplatných sdílené složky tak, že přejdete do sdílených složek odeslané -> předplatná sdílet. Tím získáte podrobnosti o aktivní předplatná vytvořená spotřebiteli dat po přijetí vaši pozvánku. Budoucí aktualizace pro vaše příjemce dat můžete zastavit tak, že vyberete předplatné, sdílené složky a výběru *odvolat*. 

## <a name="snapshot-history"></a>Snímků historie 

Na kartě historie budete moct zobrazit snímky, které byly zkopírovány do příjemce dat tenanta. Budete moct sledovat četnost a dobu trvání každý interval mezi snímky. 

![Pořízení snímku historie](./media/sent-shares.png "snímků historie") 

Můžete zobrazit další podrobnosti o jednotlivých snímků, spusťte kliknutím na spustit počáteční datum. 

## <a name="next-steps"></a>Další kroky 

Další informace o [terminologie dat sdílené složky Azure](terminology.md)