---
title: Povolit přístup k aplikaci kontejner Azure DC/OS
description: Postup povolení veřejný přístup k DC/OS kontejnerů v Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: aedc97335a0b9ad00cf653477b62bf530b556900
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2017
ms.locfileid: "26332275"
---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>Povolit veřejný přístup k aplikaci Azure Container Service

Všechny DC/OS kontejneru služby ACS [veřejného agenta fondu](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) je automaticky přístup k Internetu. Ve výchozím nastavení, porty **80**, **443**, **8080** jsou otevřené, a jsou dostupné všechny (veřejné) kontejneru naslouchá na těchto portech. Tento článek ukazuje, jak otevřít další porty pro vaše aplikace v Azure Container Service.

## <a name="open-a-port-portal"></a>Otevřete port (portál)
Nejprve musíme otevřít port, který má být.

1. Přihlaste se k portálu.
2. Najít skupinu prostředků, které jste nasadili Azure Container Service na.
3. Vyberte pro vyrovnávání zatížení agenta (která je s názvem podobná **XXXX--agent-lb XXXX**).
   
    ![Nástroj pro vyrovnávání zatížení Azure container service](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klikněte na tlačítko **sondy** a potom **přidat**.
   
    ![Nástroj pro vyrovnávání zatížení Azure container service sondy](./media/container-service-enable-public-access/add-probe.png)
5. Vyplňte formulář kontroly a klikněte na tlačítko **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name (Název) |Popisný název kontroly. |
   | Port |Port kontejneru pro testování. |
   | Cesta |(Pokud v režimu HTTP) Web relativní cesta k testu. Protokol HTTPS není podporována. |
   | Interval |Množství času mezi testu pokusí v sekundách. |
   | Prahová hodnota špatného stavu |Počet po sobě jdoucích testu pokusy o zadání před zvažování kontejneru není v pořádku. |
6. Zpět na vlastnosti služby Vyrovnávání zatížení agenta, klikněte na tlačítko **pravidla Vyrovnávání zatížení** a potom **přidat**.
   
    ![Pravidla nástroje pro vyrovnávání zatížení Azure container service](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Vyplňte formulář, nástroje pro vyrovnávání zatížení a klikněte na tlačítko **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name (Název) |Popisný název služby Vyrovnávání zatížení. |
   | Port |Veřejný příchozí port. |
   | Back-endový port |Interní veřejný port kontejneru směrovat provoz. |
   | Fond back-end |Kontejnery v tomto fondu budou cílem pro tento nástroj pro vyrovnávání zatížení. |
   | Test |Kontroly používané k určení, zda cíl v **fond back-end** je v pořádku. |
   | Trvalost relace |Určuje způsob zpracování provoz z klienta po dobu trvání relace.<br><br>**Žádný**: po sobě jdoucí požadavky ze stejného klienta může zpracovávat všechny kontejneru.<br>**Klient IP**: po sobě jdoucí požadavky ze stejné IP adresa klienta jsou zpracovávány ve stejném kontejneru.<br>**Klient IP a protokol**: po sobě jdoucí požadavky ze stejné kombinaci IP adresy a protokol klienta jsou zpracovávány ve stejném kontejneru. |
   | Časový limit nečinnosti |(Pouze TCP) V minutách, otevřete doba uchování TCP nebo HTTP klienta bez spoléhání na *udržování* zprávy. |

## <a name="add-a-security-rule-portal"></a>Přidat pravidlo zabezpečení (portál)
Dále je potřeba přidat pravidlo zabezpečení, který směruje provoz z našich otevřen port přes bránu firewall.

1. Přihlaste se k portálu.
2. Najít skupinu prostředků, které jste nasadili Azure Container Service na.
3. Vyberte **veřejné** agenta skupinu zabezpečení sítě (která je s názvem podobná **XXXX-agent veřejný nsg-XXXX**).
   
    ![Skupina zabezpečení sítě Azure container service](./media/container-service-enable-public-access/agent-nsg.png)
4. Vyberte **příchozí pravidla zabezpečení** a potom **přidat**.
   
    ![Pravidla skupiny zabezpečení sítě Azure container service](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Vyplňte pravidlo brány firewall povolit veřejný port a klikněte na tlačítko **OK**.
   
   | Pole | Popis |
   | --- | --- |
   | Name (Název) |Popisný název pravidla brány firewall. |
   | Priorita |Pořadí priority pro pravidlo. Nižší počet tím vyšší je priorita. |
   | Zdroj |Omezte příchozí rozsah IP adres má být povolené nebo zakázané tímto pravidlem. Použití **žádné** nezadat omezení. |
   | Služba |Vyberte sadu předdefinovaných služeb, ke kterému se toto pravidlo zabezpečení. Jinak použijte **vlastní** a vytvořte vlastní. |
   | Protocol (Protokol) |Omezit přenos na základě **TCP** nebo **UDP**. Použití **žádné** nezadat omezení. |
   | Rozsah portů |Když **služby** je **vlastní**, určuje rozsah portů, která toto pravidlo vztahuje. Můžete používat jediný port, jako třeba **80**, nebo jako rozsah **1024-1 500**. |
   | Akce |Povolí nebo zakážou provoz, která splňuje kritéria. |

## <a name="next-steps"></a>Další kroky
Další informace o rozdílu mezi [veřejné a privátní agentů DC/OS](container-service-dcos-agents.md).

Přečtěte si další informace o [Správa kontejnerů Váš DC/OS](container-service-mesos-marathon-ui.md).

