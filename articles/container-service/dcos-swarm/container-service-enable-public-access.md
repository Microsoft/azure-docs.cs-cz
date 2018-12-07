---
title: (NEPOUŽÍVANÉ) Povolit přístup k aplikaci kontejneru Azure DC/OS
description: Postup povolení veřejného přístupu DC/OS kontejnerům v Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996598"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(NEPOUŽÍVANÉ) Povolení veřejného přístupu k aplikaci Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Žádný kontejner DC/OS ve ACS [veřejný agent fondu](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) je automaticky přístup k Internetu. Ve výchozím nastavení, porty **80**, **443**, **8080** jsou otevřené, a jsou k dispozici žádné (veřejného) kontejneru naslouchání na těchto portech. Tento článek ukazuje, jak otevřít další porty pro vaše aplikace ve službě Azure Container Service.

## <a name="open-a-port-portal"></a>Otevření portu (portál)
Nejdřív potřebujeme pro otevření portu, jaké chceme.

1. Přihlaste se k portálu.
2. Najdete skupinu prostředků, kterou jste nasadili do Azure Container Service.
3. Vyberte nástroje pro vyrovnávání zatížení agenta (který se nazývá podobný **XXXX--agent-lb XXXX**).
   
    ![Nástroj pro vyrovnávání zatížení Azure container service](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klikněte na tlačítko **sondy** a potom **přidat**.
   
    ![Sondy nástroje pro vyrovnávání zatížení Azure container service](./media/container-service-enable-public-access/add-probe.png)
5. Vyplňte formulář test a klikněte na tlačítko **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Název |Popisný název testu. |
   | Port |Port kontejneru pro testování. |
   | Cesta |(Pokud v režimu HTTP) Cesta relativní webu do testu. HTTPS se nepodporuje. |
   | Interval |Množství času mezi test pokusí v řádu sekund. |
   | Prahová hodnota špatného stavu |Počet po sobě jdoucích test pokusí předtím, než kontejneru není v pořádku. |
6. Zpět na vlastnosti nástroje pro vyrovnávání zatížení agenta, klikněte na tlačítko **pravidla Vyrovnávání zatížení** a potom **přidat**.
   
    ![Pravidla nástroje pro vyrovnávání zatížení Azure container service](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Vyplňte formulář nástroje pro vyrovnávání zatížení a klikněte na tlačítko **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Název |Popisný název nástroje pro vyrovnávání zatížení. |
   | Port |Veřejné příchozí port. |
   | Back-endový port |Interní veřejný port kontejneru pro směrování provozu do. |
   | Back-endový fond |Kontejnery v tomto fondu budou cílem pro tento nástroj pro vyrovnávání zatížení. |
   | Test paměti |Používá k určení, zda cíl v testu **back-endový fond** je v pořádku. |
   | Trvalost relace |Určuje, jak by měl zpracovávat přenosy z klienta po dobu trvání relace.<br><br>**Žádný**: po sobě jdoucí požadavky ze stejného klienta může být zpracována kontejneru.<br>**Klient IP**: po sobě jdoucí požadavky ze stejné IP adresa klienta jsou zpracovávány stejného kontejneru.<br>**Klient IP, protokol**: po sobě jdoucí požadavky ze stejné kombinaci IP adresy a protokol klienta jsou zpracovávány stejného kontejneru. |
   | Časový limit nečinnosti |(Pouze TCP) Během několika minut, dobu k aktualizacím klienta TCP/HTTP otevřené bez nutnosti spoléhat se na *keep-alive* zprávy. |

## <a name="add-a-security-rule-portal"></a>Přidat pravidlo zabezpečení (portál)
Teď potřebujeme přidat pravidlo zabezpečení, která směruje provoz z našich otevřený port přes bránu firewall.

1. Přihlaste se k portálu.
2. Najdete skupinu prostředků, kterou jste nasadili do Azure Container Service.
3. Vyberte **veřejné** agenta skupiny zabezpečení sítě (který se nazývá podobný **XXXX-agent-public-nsg-XXXX**).
   
    ![Skupina zabezpečení sítě Azure container service](./media/container-service-enable-public-access/agent-nsg.png)
4. Vyberte **příchozí pravidla zabezpečení** a potom **přidat**.
   
    ![Pravidla skupiny zabezpečení sítě Azure container service](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Vyplňte pravidla brány firewall pro povolení veřejného portu a klikněte na tlačítko **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Název |Popisný název pravidla brány firewall. |
   | Priorita |Pořadí priority pro pravidlo. Čím nižší číslo vyšší je priorita. |
   | Zdroj |Omezte příchozí rozsah IP adres má být povolen nebo odepřen tímto pravidlem. Použití **jakékoli** pro nezadání omezení. |
   | Služba |Vyberte sadu předdefinovaných služby, které je pro toto pravidlo zabezpečení. Jinak použijte **vlastní** vytvořit vlastní. |
   | Protocol (Protokol) |Omezení provozu na základě **TCP** nebo **UDP**. Použití **jakékoli** pro nezadání omezení. |
   | Rozsah portů |Když **služby** je **vlastní**, určuje rozsah portů, na která toto pravidlo vztahuje. Jediný port, můžete použít jako **80**, nebo jako rozsah **1024 1500**. |
   | Akce |Povolí nebo zakážou provoz, který splňuje kritéria. |

## <a name="next-steps"></a>Další postup
Další informace o rozdílech mezi [veřejných a privátních agentů DC/OS](container-service-dcos-agents.md).

Přečtěte si další informace o [Správa kontejnerů systému DC/OS](container-service-mesos-marathon-ui.md).

