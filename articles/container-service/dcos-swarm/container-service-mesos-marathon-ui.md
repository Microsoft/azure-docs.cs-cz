---
title: ZASTARALÉ Správa clusteru Azure DC/OS pomocí uživatelského rozhraní Marathon
description: Využijte webového uživatelského rozhraní Marathon k nasazení kontejnerů do clusteru Azure Container Service.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b251096915506c3c7a4eebf45b6a03e24779a3d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277809"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>ZASTARALÉ Správa Azure Container Service clusteru DC/OS prostřednictvím webového uživatelského rozhraní Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS poskytuje prostředí pro nasazování a škálování clusterových úloh a zároveň poskytuje abstrakci používaného hardwaru. Nad DC/OS je rozhraní, které spravuje plánování a provádění výpočetních úloh.

I když jsou pro mnoho oblíbených úloh dostupné architektury, tento dokument popisuje, jak začít s nasazováním kontejnerů pomocí Marathon. 


## <a name="prerequisites"></a>Požadavky
Než si projdete tyto příklady, budete potřebovat cluster DC/OS nakonfigurovaný v Azure Container Service. Kromě toho je nutné mít možnost se k tomuto clusteru připojit vzdáleně. Další informace k těmto záležitostem najdete v těchto článcích:

* [Nasazení clusteru Azure Container Service](container-service-deployment.md)
* [Připojení ke clusteru Azure Container Service](../container-service-connect.md)

> [!NOTE]
> V tomto článku se předpokládá, že vytváříte tunelování do clusteru DC/OS přes místní port 80.
>

## <a name="explore-the-dcos-ui"></a>Zkoumáme uživatelské rozhraní DC/OS
Po [navázání](../container-service-connect.md)tunelu Secure Shell (SSH) přejděte na http\/:/localhost/.. Načte se webové uživatelské rozhraní DC/OS a zobrazí se informace o clusteru, například využité prostředky, aktivní agenti a spuštěné služby.

![Uživatelské rozhraní DC/OS](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Zkoumáme uživatelské rozhraní Marathon
Pokud chcete zobrazit uživatelské rozhraní Marathon, přejděte na adresu\/http:/localhost/Marathon.. Prostřednictvím této obrazovky můžete spustit nový kontejner nebo jinou aplikaci z clusteru Azure Container Service na bázi DC/OS. Taky uvidíte informace o spuštěných kontejnerech a aplikacích.  

![Uživatelské rozhraní Marathon](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Nasazení kontejneru formátovaného Dockerem
Pokud chcete pomocí Marathonu nasadit nový kontejner, klikněte na **Create Application** (Vytvořit aplikaci) a zadejte na kartách formuláře následující informace:

| Pole | Hodnota |
| --- | --- |
| ID |nginx |
| Memory (Paměť) | 32 |
| Image |nginx |
| Síť |Bridged (Zapojeno do mostu) |
| Host Port (Port hostitele) |80 |
| Protocol (Protokol) |TCP |

![Nová aplikace uživatelského rozhraní – obecné](./media/container-service-mesos-marathon-ui/dcos4.png)

![Nová aplikace uživatelského rozhraní – kontejner Docker](./media/container-service-mesos-marathon-ui/dcos5.png)

![Nová aplikace uživatelského rozhraní – porty a zjišťování služby](./media/container-service-mesos-marathon-ui/dcos6.png)

Pokud chcete staticky namapovat port kontejneru na port agenta, budete muset použít režim JSON. Provedete to tak, že pomocí přepínače přepnete průvodce novou aplikací na **JSON Mode** (Režim JSON). Potom v části `portMappings` definice aplikace zadejte následující nastavení. Tento příklad namapuje port číslo 80 kontejneru na port číslo 80 agenta DC/OS. Po provedení této změny můžete režim JSON v průvodci opět vypnout.

```none
"hostPort": 80,
```

![Nová aplikace uživatelského rozhraní – příklad u portu číslo 80](./media/container-service-mesos-marathon-ui/dcos13.png)

Pokud chcete povolit kontroly stavu, nastavte cestu na kartě **Health Checks** (Kontroly stavu).

![Nová aplikace uživatelského rozhraní – kontroly stavu](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

Cluster DC/OS se nasazuje se sadou privátních a veřejných agentů. Pokud chcete, aby měl cluster přístup k aplikacím z internetu, musíte aplikace nasadit s použitím veřejného agenta. Uděláte to následovně: V průvodci novou aplikací vyberete kartu **Optional** (Volitelné) a do **Accepted Resource Roles** (Povolené role prostředků) zadáte **slave_public**.

Pak klikněte na **Create Application** (Vytvořit aplikaci).

![Nová aplikace uživatelského rozhraní – nastavení veřejného agenta](./media/container-service-mesos-marathon-ui/dcos14.png)

Když se vrátíte na hlavní stránku Marathonu, uvidíte stav nasazení daného kontejneru. Zpočátku uvidíte stav **Deploying** (Nasazování). Po úspěšném nasazení se stav změní na **Running** (spuštěno).

![Hlavní stránka uživatelského rozhraní Marathon – stav nasazení kontejneru](./media/container-service-mesos-marathon-ui/dcos7.png)

Když přepnete zpět do webového uživatelského rozhraní DC/OS (http:\//localhost/), uvidíte, že úloha (v tomto případě kontejner ve formátu Docker) je spuštěná na clusteru DC/OS.

![Webové uživatelské rozhraní DC/OS – úloha spuštěná na clusteru](./media/container-service-mesos-marathon-ui/dcos8.png)

Pokud chcete zobrazit uzel clusteru, na kterém je úloha spuštěná, klikněte na kartu **Nodes** (Uzly).

![Webové uživatelské rozhraní DC/OS – uzel clusteru úlohy](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Dosažení kontejneru

V tomto příkladu je aplikace spuštěna v uzlu veřejného agenta. K aplikaci budete mít přístup z Internetu, a to tak, že přejdete na plně `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`kvalifikovaný název domény agenta clusteru:, kde:

* **DNSPREFIX** je předpona DNS zadaná ve chvíli, kdy jste nasadili cluster.
* **REGION** je oblast, ve které je umístěna skupina prostředků.

    ![Nginx z Internetu](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Další kroky
* [Práce s DC/OS a rozhraním API Marathonu](container-service-mesos-marathon-rest.md)

* Podrobná prohlídka služby Azure Container Service s Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
