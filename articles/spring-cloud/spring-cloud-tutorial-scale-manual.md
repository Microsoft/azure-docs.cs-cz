---
title: 'Kurz: Škálování aplikace v Azure Spring Cloud | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak škálovat aplikaci pomocí Azure Spring Cloud na webu Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277479"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Škálování aplikace v Azure Spring Cloud

Tento kurz ukazuje, jak škálovat jakoukoli aplikaci mikroslužeb pomocí řídicího panelu Azure Spring Cloud na webu Azure Portal.

Škálujte aplikaci nahoru a dolů úpravou jejího počtu virtuálních procesorů (vCPU) a velikosti paměti. Škálujte aplikaci dovnitř a ven úpravou počtu instancí aplikace.

Po dokončení budete vědět, jak provést rychlé ruční změny jednotlivých aplikací ve vaší službě. Škálování se projeví během několika sekund a nevyžaduje žádné změny kódu ani opětovné nasazení.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 
* Naslaná instance služby Azure Spring Cloud.  Začněte s [nasazením aplikace pomocí azure cli a](spring-cloud-quickstart-launch-app-cli.md) začněte.
* Alespoň jedna aplikace již byla vytvořena v instanci služby.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Přechod na stránku škálování na webu Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Přejděte na stránku **Přehled** azure jarního cloudu.

1. Vyberte skupinu prostředků, která obsahuje vaši službu.

1. V nabídce na levé straně stránky vyberte kartu **Aplikace** v části **Nastavení.**

1. Vyberte aplikaci, kterou chcete škálovat. V tomto příkladu vyberte aplikaci s názvem **account-service**. Pak byste se měli zobrazit na stránce **Přehled** aplikace.

1. Přejděte na kartu **Změnit velikost** v části **Nastavení** v nabídce na levé straně stránky. Měli byste vidět možnosti pro změnu velikosti atributy uvedené v následující části.

## <a name="scale-your-application"></a>Škálování aplikace

Pokud změníte atributy změny měřítka, mějte na paměti následující poznámky:

* **Procesory**: Maximální počet procesorů na instanci aplikace je čtyři. Celkový počet procesorů pro aplikaci je zde nastavená hodnota vynásobená počtem instancí aplikace.

* **Paměť/GB:** Maximální velikost paměti na instanci aplikace je 8 GB. Celkové množství paměti pro aplikaci je zde nastavená hodnota vynásobená počtem instancí aplikace.

* **Počet instancí aplikace**: Na úrovni Standard můžete vertikálně navýšit kapacitu na maximálně 20 instancí. Tato hodnota změní počet samostatných spuštěných instancí aplikace mikroslužeb.

Nezapomeňte vybrat **Uložit,** chcete-li použít nastavení měřítka.

![Škálovací služba na webu Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Po několika sekundách se provedené změny zobrazí na stránce **Přehled** s dalšími podrobnostmi dostupnými na kartě **Instance aplikace.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak ručně škálovat vaše aplikace Azure Spring Cloud. Chcete-li se dozvědět, jak sledovat aplikaci, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Přečtěte si, jak sledovat aplikaci](spring-cloud-tutorial-distributed-tracing.md)
