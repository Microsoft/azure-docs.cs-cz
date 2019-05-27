---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0068bd151c3d7d243b05c326ec73a201f4131296
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146814"
---
Pokud chcete vytvořit připojení P2S z jiného klientského počítače, než který jste použili k vytvoření klientských certifikátů, budete muset klientský certifikát nainstalovat. Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při jeho exportu.

1. Vyhledejte a zkopírujte soubor *.pfx* na klientský počítač. Na klientském počítači spusťte instalaci dvojím kliknutím na soubor *.pfx*. Ponechte **Umístění úložiště** jako **Aktuální uživatel** a pak klikněte na **Další**.
2. Na stránce **Importovaný soubor** neprovádějte žádné změny. Klikněte na **Další**.
3. Na **ochrana privátního klíče** stránky, zadejte heslo pro certifikát nebo ověřte správnost objektu zabezpečení a potom klikněte na tlačítko **Další**.
4. Na stránce **Úložiště certifikátů** ponechte výchozí umístění a pak klikněte na **Další**.
5. Klikněte na **Dokončit**. V okně **Upozornění zabezpečení** pro instalaci certifikátu klikněte na **Ano**. Na tlačítko Ano můžete s klidem kliknout, protože jste certifikát generovali vy. Certifikát se teď úspěšně naimportoval.