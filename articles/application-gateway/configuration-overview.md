---
title: Přehled konfigurace Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat komponenty služby Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651998"
---
# <a name="application-gateway-configuration-overview"></a>Přehled konfigurace Application Gateway

Azure Application Gateway se skládá z několika komponent, které můžete nakonfigurovat různými způsoby pro různé scénáře. V tomto článku se dozvíte, jak nakonfigurovat jednotlivé komponenty.

![Graf Flow komponent Application Gateway](./media/configuration-overview/configuration-overview1.png)

Tento obrázek znázorňuje aplikaci, která má tři naslouchací procesy. První dva jsou naslouchací procesy pro více webů pro `http://acme.com/*` a v `http://fabrikam.com/*` uvedeném pořadí. Naslouchat na portu 80. Třetí je základní naslouchací proces, který má komplexní ukončení služby TLS (Transport Layer Security), dříve označované jako ukončení SSL (Secure Sockets Layer) (SSL).

## <a name="infrastructure"></a>Infrastruktura

Infrastruktura Application Gateway zahrnuje virtuální síť, podsítě, skupiny zabezpečení sítě a uživatelsky definované trasy.

Další informace najdete v tématu [Konfigurace infrastruktury Application Gateway](configuration-infrastructure.md).



## <a name="front-end-ip-address"></a>Front-endová IP adresa

Aplikační bránu můžete nakonfigurovat tak, aby měla veřejnou IP adresu, soukromou IP adresu nebo obojí. Veřejná IP adresa se vyžaduje při hostování back-endu, ke kterému musí klienti přistupovat přes Internet prostřednictvím internetové virtuální IP adresy (VIP).

Další informace najdete v tématu [Application Gateway konfigurace front-ENDOVÉ IP adresy](configuration-front-end-ip.md).

## <a name="listeners"></a>Naslouchací procesy

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení pomocí portu, protokolu, hostitele a IP adresy. Při konfiguraci naslouchacího procesu je nutné zadat hodnoty, které odpovídají odpovídajícím hodnotám v příchozím požadavku na bráně.

Další informace najdete v tématu [Konfigurace naslouchacího procesu Application Gateway](configuration-listeners.md).

## <a name="request-routing-rules"></a>Vyžádání pravidel směrování

Když vytváříte Aplikační bránu pomocí Azure Portal, vytvoříte výchozí pravidlo (*rule1*). Toto pravidlo váže výchozí naslouchací proces (*appGatewayHttpListener*) s výchozím fondem back-end (*appGatewayBackendPool*) a výchozím nastavením back-endu http (*appGatewayBackendHttpSettings*). Po vytvoření brány můžete upravit nastavení výchozího pravidla nebo vytvořit nová pravidla.

Další informace najdete v tématu [Application Gateway pravidla směrování žádostí](configuration-request-routing-rules.md).

## <a name="http-settings"></a>Nastavení HTTP

Služba Application Gateway směruje provoz na back-endové servery pomocí konfigurace, kterou tady zadáte. Po vytvoření nastavení HTTP je potřeba ho přidružit k jednomu nebo více pravidlům směrování požadavků.

Další informace najdete v tématu [Konfigurace nastavení protokolu HTTP Application Gateway](configuration-http-settings.md).

## <a name="back-end-pool"></a>Back-endový fond

Back-end fond můžete nasměrovat na čtyři typy členů back-endu: konkrétní virtuální počítač, sada škálování virtuálního počítače, IP adresa nebo plně kvalifikovaný název domény nebo služba App Service. 

Až vytvoříte fond back-end, musíte ho přidružit k jednomu nebo více pravidlům směrování požadavků. Pro každý fond back-end v bráně Application Gateway musíte taky nakonfigurovat testy stavu. Pokud je splněna podmínka pravidla směrování požadavků, služba Application Gateway přesměruje provoz na servery, které jsou v pořádku (podle určení sond stavu), do odpovídajícího fondu back-end.

## <a name="health-probes"></a>Sondy stavu

Služba Application Gateway ve výchozím nastavení monitoruje stav všech prostředků ve svém back-endu. Důrazně doporučujeme vytvořit vlastní test pro každé nastavení back-endu HTTP a získat tak větší kontrolu nad monitorováním stavu. Informace o tom, jak nakonfigurovat vlastní test paměti, najdete v tématu [vlastní nastavení sondy stavu](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Po vytvoření vlastního sondy stavu je potřeba ho přidružit k nastavení back-endu HTTP. Vlastní test nemonitoruje stav fondu back-end, pokud není odpovídající nastavení HTTP explicitně přidruženo k naslouchacímu procesu pomocí pravidla.

## <a name="next-steps"></a>Další kroky

Teď, když víte o Application Gateway komponent, můžete:

- [Vytvoření aplikační brány v Azure Portal](quick-create-portal.md)
- [Vytvoření aplikační brány pomocí PowerShellu](quick-create-powershell.md)
- [Vytvoření aplikační brány pomocí Azure CLI](quick-create-cli.md)
