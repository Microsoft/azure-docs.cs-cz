---
title: 'Rychlý Start: povolení Azure Security Center pro službu IoT v IoT Hub'
description: V tomto rychlém startu se dozvíte, jak ve IoT Hub povolit Azure Security Center pro službu IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 641ba4c8d3b0e54132c19a493e1e4bf17bb28e13
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904119"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Rychlý Start: zprovoznění Azure Security Center pro službu IoT v IoT Hub

Tento článek popisuje, jak povolit Azure Security Center pro službu IoT v existujícím IoT Hub. Pokud v tuto chvíli nemáte IoT Hub, přečtěte si téma [vytvoření IoT Hub pomocí Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) pro začátek. 

> [!NOTE]
> Azure Security Center pro IoT v současné době podporuje pouze centra IoT úrovně Standard.


## <a name="prerequisites-for-enabling-the-service"></a>Předpoklady pro povolení služby

- Pracovní prostor Log Analytics
  - Ve výchozím nastavení se ve vašem Log Analytics pracovním prostoru ukládají dva typy informací Azure Security Center pro IoT. **výstrahy** a **doporučení**zabezpečení. 
  - Můžete zvolit, že chcete přidat úložiště pro další typ informací, **nezpracované události**. Pamatujte, že ukládání **nezpracovaných událostí** v Log Analytics přináší další náklady na úložiště. 
- IoT Hub (úroveň Standard)
- Splnění všech [požadavků služby](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Povolení Azure Security Center pro IoT na IoT Hub 

Postup při povolení zabezpečení na IoT Hub: 

1. Otevřete **IoT Hub** v Azure Portal. 
1. V nabídce **zabezpečení** klikněte na **zabezpečení vašeho řešení IoT**.    


Blahopřejeme! Dokončili jste povolování Azure Security Center pro IoT na IoT Hub. 

### <a name="geolocation-and-ip-address-handling"></a>Geografická poloha a zpracování IP adres

V rámci zabezpečení řešení IoT se shromažďují a ukládají IP adresy příchozích a odchozích připojení do a ze zařízení IoT, IoT Edge a IoT Hub, a to ve výchozím nastavení. Tyto informace jsou nezbytné ke zjištění neobvyklého připojení z podezřelých zdrojů IP adres. Například při pokusech o navázání připojení ze zdroje IP adresy známého botnetu nebo ze zdroje IP adresy mimo vaši geografickou polohu. Azure Security Center pro službu IoT nabízí flexibilitu, která umožňuje kdykoli povolit a zakázat shromažďování dat IP adres. 

Povolení nebo zakázání shromažďování dat IP adres: 

1. Otevřete IoT Hub a v nabídce **zabezpečení** vyberte **Přehled** . 
2. Klikněte na obrazovku **Nastavení** a podle potřeby upravte nastavení geografického umístění nebo manipulace s IP adresami.

### <a name="log-analytics-creation"></a>Vytváření Log Analytics

Když je zapnutá Azure Security Center pro IoT, vytvoří se výchozí pracovní prostor Azure Log Analytics, který ukládá nezpracované události zabezpečení, výstrahy a doporučení pro vaše zařízení IoT, IoT Edge a IoT Hub. Každý měsíc je prvních pět (5) GB dat přijatých na zákazníky do služby Azure Log Analytics zdarma. Každý GB dat přijatý v pracovním prostoru Azure Log Analytics se prvních 31 dnů uchovává zdarma. Přečtěte si další informace o cenách [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) .

Změna konfigurace pracovního prostoru Log Analytics:

1. Otevřete IoT Hub a v nabídce **zabezpečení** vyberte **Přehled** . 
2. Klikněte na obrazovku **Nastavení** a upravte konfiguraci pracovního prostoru Log Analytics nastavení podle vašich přání.

### <a name="customize-your-iot-security-solution"></a>Přizpůsobení řešení zabezpečení IoT
Ve výchozím nastavení zapnutí Azure Security Center pro řešení IoT automaticky zabezpečuje všechna centra IoT v rámci vašeho předplatného Azure. 

Postup zapnutí nebo vypnutí Azure Security Center služby IoT na konkrétní IoT Hub: 

1. Otevřete IoT Hub a v nabídce **zabezpečení** vyberte **Přehled** . 
2. V rámci vašeho předplatného Azure klikněte na obrazovku **Nastavení** a upravte nastavení zabezpečení libovolného centra IoT.


## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a nakonfigurujte vaše řešení...

> [!div class="nextstepaction"]
> [Konfigurace řešení](quickstart-configure-your-solution.md)
