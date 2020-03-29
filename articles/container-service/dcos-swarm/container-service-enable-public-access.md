---
title: (ZASTARALÉ) Povolení přístupu k aplikaci kontejneru Azure DC/OS
description: Jak povolit veřejný přístup k kontejnerům DC/OS ve službě Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61457322"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(ZASTARALÉ) Povolení veřejného přístupu k aplikaci Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Všechny kontejnery DC/OS ve [fondu veřejných agentů](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) Služby ACS jsou automaticky vystaveny internetu. Ve výchozím nastavení jsou otevřeny porty **80**, **443**, **8080** a všechny (veřejné) kontejnery naslouchající na těchto portech jsou přístupné. Tento článek ukazuje, jak otevřít další porty pro vaše aplikace ve službě Azure Container Service.

## <a name="open-a-port-portal"></a>Otevření portu (portálu)
Nejprve musíme otevřít přístav, který chceme.

1. Přihlaste se k portálu.
2. Najděte skupinu prostředků, do které jste nasadili službu Azure Container Service.
3. Vyberte agenta pro vyrovnávání zatížení (který je pojmenován podobně jako **XXXX-agent-lb-XXXX).**
   
    ![Azure kontejnerový service balancer](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klepněte na **položku Sondy** a potom **na tlačítko Přidat**.
   
    ![Sondy pro vyrovnávání zatížení služby Azure pro kontejnery](./media/container-service-enable-public-access/add-probe.png)
5. Vyplňte formulář sondy a klepněte na **tlačítko OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name (Název) |Popisný název sondy. |
   | Port |Port kontejneru k testování. |
   | Cesta |(Když jste v režimu HTTP) Relativní cesta k webu sondy. Protokol HTTPS není podporován. |
   | Interval |Doba mezi pokusy o sondu v sekundách. |
   | Prahová hodnota pro poškozený stav |Počet po sobě jdoucích pokusů o sondu před zvážením kontejneru není v pořádku. |
6. Zpět na vlastnosti agenta vyrovnávání zatížení, klepněte na **pravidla vyrovnávání zatížení** a potom **Přidat**.
   
    ![Pravidla pro vyrovnávání zatížení služby Azure pro kontejnery](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Vyplňte formulář pro vyrovnávání zatížení a klepněte na **tlačítko OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name (Název) |Popisný název provyrovnávání zatížení. |
   | Port |Veřejný příchozí port. |
   | Back-endový port |Vnitřní veřejný port kontejneru pro směrování provozu. |
   | Back-endový fond |Kontejnery v tomto fondu bude cílem pro tento vyrovnávání zatížení. |
   | Sonda |Sonda slouží k určení, pokud cíl ve **fondu back-endu** je v pořádku. |
   | Trvalost relace |Určuje, jak by měl být provoz z klienta zpracován po dobu trvání relace.<br><br>**Žádné**: Následné požadavky od stejného klienta mohou být zpracovány libovolným kontejnerem.<br>**IP klienta**: Následné požadavky ze stejné ip adresy klienta jsou zpracovány stejným kontejnerem.<br>**IP klienta a protokol**: Následné požadavky ze stejné kombinace IP a protokolu klienta jsou zpracovány stejným kontejnerem. |
   | Časový limit nečinnosti |(pouze protokol TCP) V minutách čas zachovat klienta TCP/HTTP otevřené bez spoléhání se na *zprávy keep-alive.* |

## <a name="add-a-security-rule-portal"></a>Přidání pravidla zabezpečení (portál)
Dále musíme přidat bezpečnostní pravidlo, které směruje provoz z našeho otevřeného portu přes bránu firewall.

1. Přihlaste se k portálu.
2. Najděte skupinu prostředků, do které jste nasadili službu Azure Container Service.
3. Vyberte skupinu zabezpečení sítě **veřejného** agenta (která je pojmenována podobně jako **XXXX-agent-public-nsg-XXXX).**
   
    ![Skupina zabezpečení sítě kontejnerových služeb Azure](./media/container-service-enable-public-access/agent-nsg.png)
4. Vyberte **příchozí pravidla zabezpečení** a pak **přidat**.
   
    ![Pravidla skupiny zabezpečení sítě kontejnerových služeb Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Vyplňte pravidlo brány firewall, abyste povolili veřejný port, a klepněte na tlačítko **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name (Název) |Popisný název pravidla brány firewall. |
   | Priorita |Pořadí priorit pro pravidlo. Čím nižší je číslo, tím vyšší je priorita. |
   | Zdroj |Omezte rozsah příchozích adres IP, který má být tímto pravidlem povolen nebo odepřen. Pomocí **libovolného** použití nezadejte omezení. |
   | Služba |Vyberte sadu předdefinovaných služeb, pro které je toto pravidlo zabezpečení určeno. V opačném případě použijte **vlastní** vytvořit vlastní. |
   | Protocol (Protokol) |Omezit provoz na základě **protokolu TCP** nebo **UDP**. Pomocí **libovolného** použití nezadejte omezení. |
   | Rozsah portů |Pokud je **služba** **vlastní**, určuje rozsah portů, které toto pravidlo ovlivňuje. Můžete použít jeden port, například **80**nebo rozsah jako **1024-1500**. |
   | Akce |Povolit nebo odepřít provoz, který splňuje kritéria. |

## <a name="next-steps"></a>Další kroky
Informace o rozdílu mezi [veřejnými a soukromými agenty řadiče domény/operačního sektoru](container-service-dcos-agents.md).

Přečtěte si další informace o [správě kontejnerů DC/OS](container-service-mesos-marathon-ui.md).

