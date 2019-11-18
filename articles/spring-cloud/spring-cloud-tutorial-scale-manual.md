---
title: 'Kurz: škálování aplikace v Azure jaře cloudu | Microsoft Docs'
description: V tomto kurzu se naučíte škálovat aplikaci v Azure jaře cloudu na Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132912"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Kurz: škálování aplikace ve jarním cloudu Azure

Tento kurz ukazuje, jak škálovat libovolnou aplikaci mikroslužeb pomocí řídicího panelu Azure jarního cloudu v Azure Portal. Škálujte svoji aplikaci nahoru a dolů změnou počtu virtuálních procesorů (vCPU) a velikosti paměti. Škálujte svoji aplikaci tak, že upravíte počet instancí aplikace. Až budete hotovi, budete mít přehled o tom, jak provést rychlé ruční úpravy pro každou aplikaci ve vaší službě. Změna měřítka se projeví během několika sekund a nevyžaduje žádné změny kódu nebo opětovné nasazení.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
* Nasazená instance služby jarní cloudová služba Azure  Začněte podle našeho [rychlého](spring-cloud-quickstart-launch-app-cli.md) startu.
* V této instanci služby už je vytvořená aspoň jedna aplikace.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Přejít na stránku škálování v Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Přejděte na stránku s **přehledem** jarního cloudu Azure.

1. Vyberte skupinu prostředků, která obsahuje vaši službu.

1. Přejděte na kartu **aplikace** v záhlaví **Nastavení** v nabídce na levé straně.

1. Vyberte aplikaci, kterou chcete škálovat. V tomto příkladu přizpůsobíme škálování aplikace s názvem "Account-Service". To by mělo přebrat na stránku **Přehled** aplikace.

1. Přejděte na kartu **škálování** pod záhlavím **Nastavení** v nabídce na levé straně. Měli byste vidět možnosti pro atributy škálování zobrazené v následující části.

## <a name="scale-your-application"></a>Škálování aplikace

Můžete upravit atributy škálování. Mějte na paměti následující poznámky.

* **CPU**: maximální povolený počet procesorů je 4 na jednu instanci aplikace. Celkový počet procesorů pro aplikaci bude nastavenou hodnotou vynásobený počtem instancí aplikace.

* **Paměť/GB**: maximální povolené množství paměti je 8 GB na jednu instanci aplikace.  Celková velikost paměti pro aplikaci bude nastavená jako hodnota vynásobená počtem instancí aplikace.

* **Počet instancí aplikace**: na úrovni Standard můžete škálovat až 20 instancí. Tato hodnota mění počet samostatných spuštěných instancí aplikace mikroslužeb.

Nezapomeňte použít nastavení škálování tak, že kliknete na tlačítko **Uložit** .

![Škálování služby v Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Po několika sekundách se provedené změny zobrazí na stránce **Přehled** s dalšími podrobnostmi, které jsou k dispozici na kartě **instance aplikace** . škálování nevyžaduje žádné změny kódu ani nové nasazení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak ručně škálovat vaše aplikace pro cloudovou službu Azure na jaře.  Pokud se chcete dozvědět, jak aplikaci monitorovat, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Naučte se monitorovat svoji aplikaci.](spring-cloud-tutorial-distributed-tracing.md)
