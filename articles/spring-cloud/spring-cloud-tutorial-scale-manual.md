---
title: 'Kurz: škálování aplikace v Azure jaře cloudu | Microsoft Docs'
description: V tomto kurzu se naučíte škálovat aplikaci pomocí jarního cloudu Azure v Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: 93cefd0c71e2d51187e68c6f5f02777d158e95a4
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84792060"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Škálování aplikace ve jarním cloudu Azure

Tento kurz ukazuje, jak škálovat libovolnou aplikaci mikroslužeb pomocí řídicího panelu Azure jarního cloudu v Azure Portal.

Škálujte svou aplikaci nahoru a dolů úpravou počtu virtuálních procesorů (vCPU) a velikosti paměti. Škálujte svoji aplikaci tak, že upravíte počet instancí aplikace a naškálujte je.

Po dokončení budete potřebovat, abyste v každé aplikaci v rámci služby provedli rychlé ruční změny. Změna měřítka se projeví během několika sekund a nevyžaduje žádné změny kódu nebo opětovné nasazení.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete. 
* Nasazená instance služby jarní cloudová služba Azure  Pokud chcete začít, postupujte podle našeho [rychlého startu na webu nasazení aplikace prostřednictvím rozhraní příkazového řádku Azure CLI](spring-cloud-quickstart-launch-app-cli.md) .
* V instanci služby už je vytvořená aspoň jedna aplikace.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Přejít na stránku škálování v Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

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

Po několika sekundách se provedené změny zobrazí na stránce **Přehled** s dalšími podrobnostmi, které jsou k dispozici na kartě **instance aplikace** . škálování nevyžaduje žádné změny kódu ani nové nasazení.

## <a name="upgrade-to-the-standard-tier"></a>Upgrade na úroveň Standard
Pokud se nacházíte na úrovni Basic a omezujete je na jednu nebo více těchto [omezení](spring-cloud-quotas.md), můžete upgradovat na úroveň Standard. Pokud to chcete provést, přejděte nejprve do nabídky cenová úroveň a vyberte sloupec standardní úroveň a klikněte na tlačítko **upgradovat** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak ručně škálovat vaše aplikace pro cloudovou službu Azure na jaře. Pokud se chcete dozvědět, jak aplikaci monitorovat, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Informace o tom, jak monitorovat aplikaci](spring-cloud-tutorial-distributed-tracing.md)
