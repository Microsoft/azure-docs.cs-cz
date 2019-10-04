---
title: Jak monitorovat Azure Data Share Preview
description: Jak monitorovat Azure Data Share Preview
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: fbc6847dd86b7687d477b2bae0deab1389dc8491
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827067"
---
# <a name="monitor-azure-data-share-preview"></a>Monitorovat Azure Data Share Preview 

Tento článek vysvětluje, jak můžete monitorovat sdílené složky dat pomocí Azure Data Share Preview. Jako poskytovatel dat můžete monitorovat různé aspekty vašich relací sdílení dat. Podrobnosti o tom, jestli vaši příjemci dat přijali vaše pozvání ke sdílení dat, a taky to, jestli si vytvořili předplatné pro sdílení a začali používat vaše data, jsou k dispozici pro monitorování. 

Jako příjemce dat můžete monitorovat snímky, které se aktivovaly v rámci vašeho předplatného Azure. 

## <a name="monitor-invitation-status"></a>Stav pozvánky monitorování

Přejděte na odeslané sdílené složky – > pozvánky, zobrazte stav pozvání ke sdílení dat. 

(./media/invitation-status.png "Stav pozvánky") na ![stav pozvánky] 

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

(./media/sent-shares.png "Historie snímků") ![Historie snímků] 

Další podrobnosti o každém spuštění snímku můžete zobrazit kliknutím na datum zahájení spuštění. 

Ve výchozím nastavení se zobrazí historie snímků po dobu až 30 dnů. Pokud potřebujete zobrazit historii více než 30 dnů, přejděte do části monitorování-> diagnostické nastavení a vyberte **Přidat nastavení diagnostiky**. Budete muset vybrat účet úložiště, do kterého se mají ukládat tyto protokoly. 

(./media/diagnostic-settings.png "Nastavení diagnostiky") ![Historie snímků] 

## <a name="next-steps"></a>Další kroky 

Další informace o [terminologii Azure Data Share](terminology.md)