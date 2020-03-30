---
title: Jak monitorovat Azure Data Share
description: Zjistěte, jak ve službě Azure Data Share sledovat stav pozvánky, sdílet předplatná a historii snímků.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490514"
---
# <a name="monitor-azure-data-share"></a>Monitorování služby Azure Data Share  

Tento článek vysvětluje, jak můžete sledovat sdílené datové složky pomocí Služby Azure Data Share. Jako poskytovatel dat můžete sledovat různé aspekty vztahů sdílení dat. Podrobnosti, například zda vaši data spotřebitelé přijali vaši pozvánku ke sdílené správě dat, a také, zda vytvořili předplatné sdílení a začali používat vaše data jsou k dispozici ke sledování. 

Jako příjemce dat můžete sledovat snímky, které se aktivovali do vašeho předplatného Azure. 

## <a name="monitor-invitation-status"></a>Sledování stavu pozvánky

Zobrazení stavu pozvánek ke sdílení dat přejdete na Odeslané sdílené složky -> pozvánky. 

![Stav pozvánky](./media/invitation-status.png "Stav pozvánky") 

Existují tři stavy, ve kterých může být vaše pozvánka:

* Čeká na vyřízení – příjemce sdílené složky dat pozvánku ještě nepřijal.
* Přijato - Příjemce sdílené složky data přijal pozvánku.
* Odmítnuto - Příjemce sdílené složky dat pozvánku odmítl.

> [!IMPORTANT]
> Pokud pozvánku odstraníte poté, co již byla přijata, není ekvivalentní zrušení přístupu. Pokud chcete zabránit zkopírování budoucích snímků do vašeho účtu úložiště pro spotřebitele dat, musíte odvolat přístup prostřednictvím karty *Sdílení předplatných.* 

## <a name="monitor-share-subscriptions"></a>Sledování předplatných sdílení

Stav předplatných sdílení zobrazíte tak, že přejdete na Předplatná sdílených akcií -> odesláno do sdílených složek. To vám poskytne podrobnosti o aktivních předplatných vytvořených vašimi datovými spotřebiteli po přijetí pozvánky. Budoucí aktualizace pro příjemce dat můžete zastavit výběrem předplatného sdílení a výběrem *možnosti Odvolat*. 

## <a name="snapshot-history"></a>Historie snímků 

Na kartě historie můžete zobrazit snímky, které byly zkopírovány do klienta příjemce dat. Můžete sledovat frekvenci a dobu trvání každého intervalu snímku. 

![Historie snímků](./media/sent-shares.png "Historie snímků") 

Kliknutím na počáteční datum spuštění můžete zobrazit další podrobnosti o každém snímku. 

Ve výchozím nastavení se zobrazí až 30 denní historie snímků. Pokud potřebujete zobrazit historii za více než 30 dní, přejděte na nastavení monitorování > diagnostiky a vyberte **přidat diagnostické nastavení**. Budete muset vybrat účet úložiště pro uložení těchto protokolů do. 

![Historie snímků](./media/diagnostic-settings.png "Nastavení diagnostiky") 

## <a name="next-steps"></a>Další kroky 

Další informace o [terminologii Azure Data Share](terminology.md)