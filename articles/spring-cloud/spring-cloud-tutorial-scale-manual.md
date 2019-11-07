---
title: 'Kurz: škálování aplikace v Azure jaře cloudu | Microsoft Docs'
description: V tomto kurzu se naučíte škálovat aplikaci v Azure jaře cloudu na Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: 31b2322bdf7b7c03ae8974d57ee1b44c2f6137b9
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607514"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Kurz: škálování aplikace ve jarním cloudu Azure

Tento kurz ukazuje, jak škálovat libovolnou aplikaci mikroslužeb pomocí řídicího panelu Azure jarního cloudu v Azure Portal. Škálujte svoji aplikaci nahoru a dolů změnou počtu virtuálních procesorů (vCPU) a velikosti paměti. Škálujte svoji aplikaci tak, že upravíte počet instancí aplikace. Až budete hotovi, budete mít přehled o tom, jak provést rychlé ruční úpravy pro každou aplikaci ve vaší službě. Změna měřítka se projeví během několika sekund a nevyžaduje žádné změny kódu nebo opětovné nasazení.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
* Nasazená instance služby jarní cloudová služba Azure  Začněte podle našeho [rychlého](spring-cloud-quickstart-launch-app-cli.md) startu.
* V této instanci služby už je vytvořená aspoň jedna aplikace.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Přejít na stránku škálování v Azure Portal

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

1. Přejděte na stránku s **přehledem** jarního cloudu Azure.

1. Přejděte na kartu **aplikace** v záhlaví **Nastavení** v nabídce na levé straně.

1. Vyberte aplikaci, kterou chcete škálovat. V tomto příkladu přizpůsobíme škálování aplikace s názvem "Account-Service". To by mělo přebrat na stránku **Přehled** aplikace.

1. Přejděte na kartu **škálování** pod záhlavím **Nastavení** v nabídce na levé straně. Měl by se zobrazit formulář s řádky pro každý z atributů škálování, které jsme předtím uvedli.

## <a name="scale-your-application"></a>Škálování aplikace

Můžete upravit atributy škálování. Mějte na paměti následující poznámky.

* **CPU**: maximální povolený počet procesorů je 4 na jednu instanci aplikace. Celkový počet procesorů pro aplikaci bude nastavenou hodnotou vynásobený počtem instancí aplikace.

* **Paměť/GB**: maximální povolené množství paměti je 8 GB na jednu instanci aplikace.  Celková velikost paměti pro aplikaci bude nastavená jako hodnota vynásobená počtem instancí aplikace.

* **Počet instancí**: na úrovni Standard můžete škálovat až 20 instancí. Tato hodnota mění počet samostatných spuštěných instancí aplikace mikroslužeb.

Nezapomeňte kliknout na tlačítko **Uložit** a použít nastavení škálování.

Po několika sekundách se provedené změny zobrazí na stránce **Přehled** s dalšími podrobnostmi, které jsou k dispozici na kartě **instance aplikace** . škálování nevyžaduje žádné změny kódu ani nové nasazení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak ručně škálovat vaše aplikace pro cloudovou službu Azure na jaře.  Pokud se chcete dozvědět, jak aplikaci monitorovat, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Naučte se monitorovat svoji aplikaci.](spring-cloud-tutorial-distributed-tracing.md)
