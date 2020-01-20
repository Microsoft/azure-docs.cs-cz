---
title: 'Kurz: škálování aplikace v Azure jaře cloudu | Microsoft Docs'
description: V tomto kurzu se naučíte škálovat aplikaci pomocí jarního cloudu Azure v Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277479"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Škálování aplikace ve jarním cloudu Azure

Tento kurz ukazuje, jak škálovat libovolnou aplikaci mikroslužeb pomocí řídicího panelu Azure jarního cloudu v Azure Portal.

Škálujte svou aplikaci nahoru a dolů úpravou počtu virtuálních procesorů (vCPU) a velikosti paměti. Škálujte svoji aplikaci tak, že upravíte počet instancí aplikace a naškálujte je.

Po dokončení budete potřebovat, abyste v každé aplikaci v rámci služby provedli rychlé ruční změny. Změna měřítka se projeví během několika sekund a nevyžaduje žádné změny kódu nebo opětovné nasazení.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
* Nasazená instance služby jarní cloudová služba Azure  Pokud chcete začít, postupujte podle našeho [rychlého startu na webu nasazení aplikace prostřednictvím rozhraní příkazového řádku Azure CLI](spring-cloud-quickstart-launch-app-cli.md) .
* V instanci služby už je vytvořená aspoň jedna aplikace.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Přejít na stránku škálování v Azure Portal

1. Přihlaste se k [Portálu Azure](https://portal.azure.com).

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak ručně škálovat vaše aplikace pro cloudovou službu Azure na jaře. Pokud se chcete dozvědět, jak aplikaci monitorovat, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Informace o tom, jak monitorovat aplikaci](spring-cloud-tutorial-distributed-tracing.md)
