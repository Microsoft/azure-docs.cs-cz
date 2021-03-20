---
title: O integraci Splunk
titleSuffix: Azure Defender for IoT
description: Za účelem řešení nedostatečného přehledu o zabezpečení a odolnosti sítě typu "společnost Defender pro IoT vyvinula aplikaci pro monitorování hrozeb pro IoT, IIoT a ICS pro Splunk, nativní integraci mezi Defendery pro IoT a Splunk, která umožňuje jednotný přístup k IT a zabezpečení.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98557586"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Defender pro aplikaci monitorování hrozeb pro IoT a ICS pro Splunk

Defender pro IoT snižuje riziko IIoT, ICS a SCADA s použitím patentových modulů pro samoobslužné zpracování ICS, které poskytují okamžité přehledy o zařízeních, chybách zabezpečení a hrozbách ICS za méně než hodinu a nezávisle na agentech, pravidlech nebo podpisech, specializovaných dovednostech a jejich předchozích znalostech.

Za účelem řešení nedostatečného přehledu o zabezpečení a odolnosti sítě typu "společnost Defender pro IoT vyvinula aplikaci pro monitorování hrozeb pro IoT, IIoT a ICS pro Splunk, nativní integraci mezi Defendery pro IoT a Splunk, která umožňuje jednotný přístup k IT a zabezpečení.

> [!Note]
> Odkazy na CyberX odkazují na Azure Defender pro IoT.

## <a name="about-the-splunk-application"></a>O aplikaci Splunk

Aplikace poskytuje analytikům SOC multidimenzionální přehled o specializovaných protokolech a zařízeních IIoT nasazených v průmyslových prostředích, a to spolu s analýzou chování s funkcí ICS pro rychlé detekci podezřelého nebo neobvyklého chování. Aplikace taky v rámci jednoho podnikového SOCu umožňuje také reakci na incidenty IT i OT. Jedná se o důležitý vývoj s ohledem na probíhající sbližování IT a k podpoře nových iniciativ IIoT, jako jsou například inteligentní počítače a analytické informace v reálném čase.

Aplikace Splunk se dá nainstalovat místně nebo spustit v cloudu. Integrace s Defenderem pro IoT podporuje obě nasazení.

## <a name="about-the-integration"></a>O integraci

Integrace programu Defender for IoT a Splunk prostřednictvím nativní aplikace umožňuje uživatelům:

- Zkraťte dobu potřebnou k tomu, aby organizace v průmyslu a kritické infrastruktuře zjistily, prozkoumaly a jednaly o počítačové hrozby.

- Získejte informace o rizicích za v reálném čase.

- Korelace Defenderu pro výstrahy IoT s úložištěm Splunk Enterprise Security Threat Intelligence

- Monitorování a reakce z jednoho podokna na sklo.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Hlavní stránka nástroje Splunk":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Stránka s výstrahami v Splunk.":::

Aplikace umožňuje správcům Splunk analyzovat výstrahy, které Defender pro IoT odesílá, a monitorovat celé nasazení zabezpečení, včetně podrobností:

- Který z pěti analytických modulů zjistil výstrahu.

- Který protokol vygeneroval výstrahu.

- Který Defender pro IoT snímač vygeneroval výstrahu.

- Úroveň závažnosti výstrahy.

- Zdroj a cíl komunikace.

## <a name="requirements"></a>Požadavky

### <a name="version-requirements"></a>Požadavky na verzi

Následující verze jsou požadavky.

- Defender pro IoT verze 2,4 a vyšší.

- Splunkbase verze 11 a vyšší.

- Splunk Enterprise verze 7,2 a vyšší.
  
## <a name="download-the-application"></a>Stažení aplikace

Stáhněte si z [Splunkbase](https://splunkbase.splunk.com/app/4313/) *aplikaci CyberX ICS pro monitorování hrozeb pro Splunk* .

## <a name="splunk-permission-requirements"></a>Požadavky na oprávnění Splunk

Vyžaduje se následující oprávnění Splunk:

- Každý uživatel s oprávněními role uživatele *správce* .

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Odeslání programu Defender pro výstrahy IoT do Splunk

Defender pro výstrahy IoT nabízí informace o rozsáhlém rozsahu událostí zabezpečení, včetně:

- Odchylky od zjištěné aktivity základní sítě.

- Zjištění malwaru.

- Detekce na základě podezřelých provozních změn.

- Anomálie sítě.

- Odchylky protokolu ze specifikací protokolu.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="Obrazovka detekce.":::

Defender pro IoT můžete nakonfigurovat tak, aby odesílal výstrahy na server Splunk, kde se zobrazují informace o výstrahách na řídicím panelu Splunk Enterprise.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Zobrazit všechna upozornění a jejich podrobnosti.":::

Na server Splunk se odešlou následující informace o výstraze.

- Datum a čas výstrahy.

- Defender pro modul IoT, který zjistil událost: porušení protokolu, porušení zásad, malware, anomálie nebo provozní modul.

- Název výstrahy.

- Zpráva upozornění

- Závažnost výstrahy: upozornění, podverze, hlavní nebo kritická.

- Název zdrojového zařízení

- IP adresa zdrojového zařízení.

- Název cílového zařízení

- IP adresa cílového zařízení.

- V programu Defender pro IP adresu platformy IoT (hostitel).

- Název zařízení s platformou IoT pro IoT (typ zdroje).

Vzorový výstup je zobrazený níže:

| Čas | Událost |
|--|--|
| 7/23/15<br />9:28:31.000 ODP. | **Výstraha pro platformu IoT for IoT**: zařízení se zastavilo pomocí příkazu PLC.<br /><br />**Typ**: porušení funkčnosti <br /><br />**Závažnost**: hlavní <br /><br />**Název zdroje**: my_device1 <br /><br />**Zdrojová IP adresa**: 192.168.110.131 <br /><br />**Název cíle**: my_device2<br /><br /> **Cílová IP adresa**: 10.140.33.238 <br /><br />**Zpráva**: síťové zařízení se zastavilo pomocí příkazu stop PLC. Toto zařízení bude fungovat až po odeslání spouštěcího příkazu. 192.168.110.131 zastavil 10.140.33.238 (zařízení Siemens S7) pomocí příkazu pro zastavení PLC.<br /><br />**Hostitel**: 192.168.90.43 <br /><br />**SourceType**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Definování pravidel předávání výstrah

Pro posílání informací o výstrahách na servery Splunk použijte Defender pro *pravidla předávání* IoT.

K dispozici jsou možnosti pro přizpůsobení pravidel výstrahy na základě:

- Byly zjištěny konkrétní protokoly.

- Závažnost události

- Defender pro modul IoT, který detekuje události

Vytvoření pravidla předávání:

1. V levém podokně senzoru nebo místní konzoly pro správu vyberte **předat dál.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Vyberte modré tlačítko vytvořit předávání výstrah.":::

1. Vyberte **vytvořit pravidla předávání**. V okně **vytvořit pravidlo předávání** definujte parametry pravidla.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Vytvořte pravidla pro předávací pravidlo.":::

    | Parametr | Popis |
    |--|--|
    | **Název** | Název pravidla předávání. |
    | **Výběr závažnosti** | Minimální incident úrovně zabezpečení k přeposílání. Pokud je například vybraná možnost podverze, budou se předávány méně závažné výstrahy a veškerá výstraha nad touto úrovní závažnosti. |
    | **Protokoly** | Ve výchozím nastavení jsou vybrány všechny protokoly. Pokud chcete vybrat konkrétní protokol, vyberte **konkrétní** a vyberte protokol, pro který se toto pravidlo použije. |
    | **Motoru** | Ve výchozím nastavení jsou zapojeny všechny bezpečnostní moduly. Pokud chcete vybrat konkrétní modul zabezpečení, pro který se toto pravidlo používá, vyberte **konkrétní** a vyberte modul. |
    | **Systémová oznámení** | Online nebo offline stav předávaného senzoru Tato možnost je k dispozici pouze v případě, že jste přihlášeni do centrálního správce. |                                            |

1. Pokud chcete, aby aplikace Defender pro IoT odesílala informace o prostředcích do Splunk, vyberte **akci** a pak vyberte **Odeslat do Splunk serveru**.

1. Zadejte následující parametry Splunk.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Parametry Splunk, které by měly být zadány na této obrazovce.":::

    | Parametr | Popis |
    |--|--|
    | **Hostitel** | Adresa serveru Splunk |
    | **Port** | 8089 |
    | **Uživatelské jméno** | Uživatelské jméno serveru Splunk |
    | **Heslo** | Heslo serveru Splunk |

1. Vybrat **Odeslat**

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [přeslat informace o výstrahách](how-to-forward-alert-information-to-partners.md).
