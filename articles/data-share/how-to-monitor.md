---
title: Jak monitorovat Azure Data Share
description: Naučte se monitorovat stav pozvánky, sdílet odběry a historii snímků ve službě Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "73490514"
---
# <a name="monitor-azure-data-share"></a>Monitorování služby Azure Data Share  

Tento článek vysvětluje, jak můžete monitorovat sdílené složky dat pomocí Azure Data Share. Jako poskytovatel dat můžete monitorovat různé aspekty vašich relací sdílení dat. Podrobnosti o tom, jestli vaši příjemci dat přijali vaše pozvání ke sdílení dat, a taky to, jestli si vytvořili předplatné pro sdílení a začali používat vaše data, jsou k dispozici pro monitorování. 

Jako příjemce dat můžete monitorovat snímky, které se aktivovaly v rámci vašeho předplatného Azure. 

## <a name="monitor-invitation-status"></a>Stav pozvánky monitorování

Přejděte na odeslané sdílené složky – > pozvánky, zobrazte stav pozvání ke sdílení dat. 

![Stav pozvánky](./media/invitation-status.png "Stav pozvánky") 

Existují tři stavy, ve kterých může být vaše Pozvánka:

* Čeká na dokončení – příjemce sdílené složky tuto pozvánku ještě nepřijal.
* Přijatý – příjemce sdílení dat přijal pozvánku.
* Odmítnuto – příjemce sdílené složky odmítl pozvání.

> [!IMPORTANT]
> Pokud pozvánku odstraníte poté, co byla přijata, není ekvivalentem odvolat přístup. Pokud chcete zastavit budoucí snímky z kopírování do účtu úložiště dat pro uživatele, musíte odvolat přístup prostřednictvím karty *sdílení předplatných* . 

## <a name="monitor-share-subscriptions"></a>Monitorování předplatných sdílení

Projděte si stav vašich předplatných sdílení tak, že přejdete na odeslané sdílené složky – > sdílet předplatná. Tím získáte podrobnosti o aktivních předplatných vytvořených vašimi příjemci dat po přijetí pozvánky. Budoucí aktualizace svého příjemce dat můžete zastavit výběrem položky sdílet předplatné a výběrem možnosti *odvolat*. 

## <a name="snapshot-history"></a>Historie snímků 

Na kartě Historie můžete zobrazit snímky, které byly zkopírovány do tenanta data Consumer. Můžete monitorovat četnost a dobu trvání každého intervalu snímků. 

![Historie snímků](./media/sent-shares.png "Historie snímků") 

Další podrobnosti o každém spuštění snímku můžete zobrazit kliknutím na datum zahájení spuštění. 

Ve výchozím nastavení se zobrazí historie snímků po dobu až 30 dnů. Pokud potřebujete zobrazit historii více než 30 dnů, přejděte do části monitorování-> diagnostické nastavení a vyberte **Přidat nastavení diagnostiky**. Budete muset vybrat účet úložiště, do kterého se mají ukládat tyto protokoly. 

![Historie snímků](./media/diagnostic-settings.png "Nastavení diagnostiky") 

## <a name="next-steps"></a>Další kroky 

Další informace o [terminologii Azure Data Share](terminology.md)