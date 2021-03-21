---
title: Nastavení automatického škálování pro aplikace mikroslužeb
description: Tento článek popisuje, jak nastavit nastavení automatického škálování pro vaše aplikace pomocí portál Microsoft Azure nebo rozhraní příkazového řádku Azure CLI.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: eaa4a219a1020639c930f8a07bfcaa1c45d0cef8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219008"
---
# <a name="set-up-autoscale-for-microservice-applications"></a>Nastavení automatického škálování pro aplikace mikroslužeb

**Tento článek se týká:** ✔️ Java ✔️ C #

Automatické škálování je integrovaná funkce Azure jarního cloudu, která pomáhá aplikacím mikroslužeb při změnách na vyžádání vykonat jejich nejlepší výkon. To zahrnuje úpravu počtu virtuálních procesorů, paměti a instancí aplikací. Tento článek popisuje, jak nastavit nastavení automatického škálování pro vaše aplikace pomocí portál Microsoft Azure nebo rozhraní příkazového řádku Azure CLI.

## <a name="prerequisites"></a>Předpoklady

Chcete-li provést tyto postupy, potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nasazená instance služby jarní cloudová služba Azure Pokud chcete začít, postupujte podle pokynů k [rychlému zprovoznění nasazení aplikace přes Azure CLI](./spring-cloud-quickstart.md) .
* V instanci služby už je vytvořená aspoň jedna aplikace.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Přejít na stránku automatického škálování v Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Přejít na stránku **přehledu** jarního cloudu Azure.
3. Vyberte skupinu prostředků, která obsahuje vaši službu.
4. V nabídce **Nastavení** v levém navigačním podokně vyberte kartu **aplikace** .
5. Vyberte aplikaci, pro kterou chcete nastavit automatické škálování. V tomto příkladu vyberte aplikaci s názvem **demo**. Měla by se zobrazit Stránka s **přehledem** aplikace.
6. V nabídce **Nastavení** v levém navigačním podokně přejděte na kartu horizontální navýšení **kapacity** .
7. Vyberte nasazení, pro které chcete nastavit automatické škálování. Měli byste vidět možnosti automatického škálování zobrazené v následující části.


![Nabídka automatického škálování](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Nastavte nastavení automatického škálování pro vaši aplikaci v Azure Portal

K dispozici jsou dvě možnosti pro správu požadavků na automatické škálování:

* Ruční škálování: udržuje pevně daný počet instancí. Úroveň Standard umožňuje horizontální navýšení kapacity na maximálně 500 instancí. Tato hodnota mění počet samostatných spuštěných instancí aplikace mikroslužeb.
* Vlastní automatické škálování: Škálujte podle libovolného plánu na základě jakékoli metriky.

V Azure Portal vyberte, jak chcete škálovat.  Na následujícím obrázku je znázorněno vlastní nastavení možností a režimu **automatického škálování** .

![Vlastní automatické škálování](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Nastavení automatického škálování pro vaši aplikaci v Azure CLI
Můžete také nastavit režimy automatického škálování pomocí Azure CLI.  Následující příkazy vytvoří nastavení automatického škálování a pravidlo automatického škálování.

* Vytvořit nastavení automatického škálování
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Vytvořit pravidlo automatického škálování
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Upgrade na úroveň Standard

Pokud se nacházíte na úrovni Basic a omezujete je na jednu nebo více těchto omezení, můžete upgradovat na úroveň Standard. Pokud to chcete provést, přejděte nejprve do nabídky **Cenová** úroveň a vyberte sloupec *standardní* úroveň a klikněte na tlačítko **upgradovat** .

## <a name="next-steps"></a>Další kroky

* [Přehled automatického škálování v Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md)
* [Automatické škálování monitorování Azure CLI](/cli/azure/monitor/autoscale)
