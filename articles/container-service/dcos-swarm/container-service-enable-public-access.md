---
title: ZASTARALÉ Povolit přístup k aplikaci kontejneru DC/OS v Azure
description: Jak povolit veřejný přístup k kontejnerům DC/OS v Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "61457322"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>ZASTARALÉ Povolit veřejný přístup k aplikaci Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Jakýkoli kontejner DC/OS ve [fondu veřejných agentů](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) služby ACS je automaticky zveřejněn pro Internet. Ve výchozím nastavení jsou porty **80**, **443**, **8080** otevřené a všechny (veřejné) kontejnery, které na těchto portech naslouchají, jsou přístupné. V tomto článku se dozvíte, jak otevřít další porty pro aplikace v Azure Container Service.

## <a name="open-a-port-portal"></a>Otevření portu (portál)
Nejdřív musíme otevřít port, který chceme.

1. Přihlaste se k portálu.
2. Vyhledejte skupinu prostředků, do které jste Azure Container Service nasadili.
3. Vyberte nástroj pro vyrovnávání zatížení, který je pojmenovaný podobně jako **xxxx-agent-9,1-xxxx**.
   
    ![Nástroj pro vyrovnávání zatížení Azure Container Service](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klikněte na **sondy** a pak na **Přidat**.
   
    ![Sondy nástroje pro vyrovnávání zatížení Azure Container Service](./media/container-service-enable-public-access/add-probe.png)
5. Vyplňte formulář sondy a klikněte na **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name |Popisný název sondy. |
   | Port |Port kontejneru, který se má testovat. |
   | Cesta |(V režimu HTTP) Relativní cesta k webovému serveru pro test. Protokol HTTPS není podporován. |
   | Interval |Doba mezi pokusy o sondu (v sekundách). |
   | Prahová hodnota pro poškozený stav |Počet po sobě jdoucích pokusů o sondu před zvážením chybného kontejneru. |
6. Zpět ve vlastnostech nástroje pro vyrovnávání zatížení agenta klikněte na tlačítko **pravidla vyrovnávání** zatížení a potom na **Přidat**.
   
    ![Pravidla nástroje pro vyrovnávání zatížení Azure Container Service](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Vyplňte formulář nástroje pro vyrovnávání zatížení a klikněte na **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name |Popisný název nástroje pro vyrovnávání zatížení. |
   | Port |Veřejný příchozí port. |
   | Port back-endu |Interní veřejný port kontejneru pro směrování provozu. |
   | Back-endový fond |Kontejnery v tomto fondu budou cílem pro tento nástroj pro vyrovnávání zatížení. |
   | Sonda |Sonda sloužící k určení, jestli je cíl ve **fondu back-endu** v dobrém stavu. |
   | Trvalost relace |Určuje, jakým způsobem má být provoz z klienta zpracován po dobu trvání relace.<br><br>**Žádné**: po sobě jdoucí požadavky ze stejného klienta mohou být zpracovány jakýmkoli kontejnerem.<br>**IP adresa klienta**: po sobě jdoucí požadavky ze stejné IP adresy klienta jsou zpracovávány stejným kontejnerem.<br>**IP adresa klienta a protokol**: po sobě jdoucí požadavky ze stejné kombinace IP adresy klienta a protokolu jsou zpracovávány stejným kontejnerem. |
   | Časový limit nečinnosti |(Jenom TCP) V minutách je čas, kdy se klient TCP/HTTP otevře, aniž by se musel spoléhat na zprávy *Keep-Alive* . |

## <a name="add-a-security-rule-portal"></a>Přidat pravidlo zabezpečení (portál)
Dál je potřeba přidat pravidlo zabezpečení, které směruje provoz z našeho otevřeného portu přes bránu firewall.

1. Přihlaste se k portálu.
2. Vyhledejte skupinu prostředků, do které jste Azure Container Service nasadili.
3. Vyberte skupinu zabezpečení sítě **veřejného** agenta (která je pojmenována podobně jako **xxxx-agent-Public-NSG-xxxx**).
   
    ![Skupina zabezpečení sítě Azure Container Service](./media/container-service-enable-public-access/agent-nsg.png)
4. Vyberte **příchozí pravidla zabezpečení** a pak **Přidat**.
   
    ![Pravidla skupiny zabezpečení sítě Azure Container Service](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Vyplňte pravidlo brány firewall, abyste povolili veřejný port, a klikněte na **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name |Popisný název pravidla brány firewall. |
   | Priorita |Pořadí priority pro pravidlo Čím nižší bude číslo vyšší Priorita. |
   | Zdroj |Omezte povolený nebo zakázaný rozsah příchozích IP adres na toto pravidlo. Použijte **,** Pokud nechcete určit omezení. |
   | Služba |Vyberte sadu předdefinovaných služeb, pro které toto pravidlo zabezpečení platí. Jinak použijte **vlastní** a vytvořte si vlastní. |
   | Protocol (Protokol) |Omezte provoz na základě **TCP** nebo **UDP**. Použijte **,** Pokud nechcete určit omezení. |
   | Rozsah portů |Když **Service** je služba **vlastní**, určuje rozsah portů, na které má toto pravidlo vliv. Můžete použít jeden port, například **80**, nebo rozsah, například **1024-1500**. |
   | Akce |Povolí nebo zakáže provoz, který splňuje kritéria. |

## <a name="next-steps"></a>Další kroky
Přečtěte si o rozdílu mezi [veřejnými a soukromými agenty DC/OS](container-service-dcos-agents.md).

Přečtěte si další informace o [správě kontejnerů DC/OS](container-service-mesos-marathon-ui.md).

