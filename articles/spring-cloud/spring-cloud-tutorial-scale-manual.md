---
title: Škálování aplikace v Azure jaře cloudu | Microsoft Docs
description: Naučte se, jak škálovat aplikaci pomocí jarního cloudu Azure v Azure Portal
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: 5632f9a6126615255306cc89425bd08a9ffa9753
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531798"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Škálování aplikace ve jarním cloudu Azure

**Tento článek se týká:** ✔️ Java ✔️ C #

Tato dokumentace ukazuje, jak škálovat libovolnou aplikaci mikroslužeb pomocí řídicího panelu Azure jarního cloudu v Azure Portal.

Škálujte svou aplikaci nahoru a dolů úpravou počtu virtuálních procesorů (vCPU) a velikosti paměti. Škálujte svoji aplikaci tak, že upravíte počet instancí aplikace a naškálujte je.

Po dokončení budete potřebovat, abyste v každé aplikaci v rámci služby provedli rychlé ruční změny. Změna měřítka se projeví během několika sekund a nevyžaduje žádné změny kódu nebo opětovné nasazení.

## <a name="prerequisites"></a>Předpoklady

Chcete-li provést tyto postupy, potřebujete:

* Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Nasazená instance služby jarní cloudová služba Azure  Pokud chcete začít, postupujte podle pokynů k [rychlému zprovoznění nasazení aplikace přes Azure CLI](spring-cloud-quickstart.md) .
* V instanci služby už je vytvořená aspoň jedna aplikace.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Přejít na stránku škálování v Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Přejít na stránku s **přehledem** jarního cloudu Azure.

1. Vyberte skupinu prostředků, která obsahuje vaši službu.

1. Vyberte kartu **aplikace** v části **Nastavení** v nabídce na levé straně stránky.

1. Vyberte aplikaci, kterou chcete škálovat. V tomto příkladu vyberte aplikaci s názvem **account-Service**. Měla by se zobrazit Stránka s **přehledem** aplikace.

1. Přejděte na kartu **škálování** v části **Nastavení** v nabídce na levé straně stránky. Měli byste vidět možnosti pro škálování atributů, které jsou uvedeny v následující části.

## <a name="scale-your-application"></a>Škálování aplikace

Pokud upravíte atributy škálování, pamatujte na tyto poznámky:

* **CPU**: maximální počet procesorů na instanci aplikace je čtyři. Celkový počet procesorů pro aplikaci je nastavená hodnota vynásobená počtem instancí aplikace.

* **Paměť/GB**: maximální velikost paměti na jednu instanci aplikace je 8 GB. Celková velikost paměti pro aplikaci je nastavená hodnota vynásobená počtem instancí aplikace.

* **Počet instancí aplikace**: na úrovni Standard můžete škálovat na maximálně 20 instancí. Tato hodnota mění počet samostatných spuštěných instancí aplikace mikroslužeb.

Nezapomeňte vybrat možnost **Uložit** a použít nastavení škálování.

![Služba škálování v Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Po několika sekundách se provedené změny zobrazí na stránce **Přehled** s dalšími podrobnostmi, které jsou k dispozici na kartě **instance aplikace** . Škálování nevyžaduje žádné změny kódu nebo opětovné nasazení.

## <a name="upgrade-to-the-standard-tier"></a>Upgrade na úroveň Standard
Pokud se nacházíte na úrovni Basic a omezujete je na jednu nebo více těchto [omezení](spring-cloud-quotas.md), můžete upgradovat na úroveň Standard. Pokud to chcete provést, přejděte nejprve do nabídky cenová úroveň a vyberte sloupec standardní úroveň a klikněte na tlačítko **upgradovat** .

## <a name="next-steps"></a>Další kroky

Tento příklad vysvětluje, jak ručně škálovat cloudovou aplikaci Azure na jaře. Informace o tom, jak monitorovat aplikaci nastavením výstrah, najdete v tématu [Nastavení automatického škálování](spring-cloud-tutorial-setup-autoscale.md).

> [!div class="nextstepaction"]
> [Přečtěte si, jak nastavit výstrahy.](spring-cloud-tutorial-alerts-action-groups.md)
