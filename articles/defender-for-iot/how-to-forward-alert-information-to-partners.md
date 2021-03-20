---
title: Předávání informací o upozornění
description: Pomocí pravidel předávání můžete odesílat informace o výstrahách do partnerských systémů.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0b71f7ca3f812de1514612f8b0dd5915f3f81bc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100522729"
---
# <a name="forward-alert-information"></a>Předávání informací o upozornění

Můžete odesílat informace o výstrahách partnerům, kteří se integrují do Azure Defenderu pro IoT, na servery syslog, e-mailových adres a dalších. Práce s pravidly předávání umožňuje rychle doručovat informace o výstrahách zúčastněným stranám zabezpečení.  

Syslog a další výchozí akce předávání jsou dodávány s vaším systémem. Při integraci s dodavateli partnerských serverů můžou být k dispozici další akce při předávání, například Microsoft Azure Sentinel, ServiceNow nebo Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Informace o výstrahách.":::

Defender pro správce IoT má oprávnění používat pravidla pro předávání.

## <a name="about-forwarded-alert-information"></a>Informace o předaných výstrahách

Výstrahy obsahují informace o rozsáhlém rozsahu zabezpečení a provozních událostí. Například:

  - Datum a čas výstrahy

  - Modul, který zjistil událost

  - Název výstrahy a Popisná zpráva

  - Závažnost výstrahy

  - Zdrojový a cílový název a IP adresa

  - Zjištěn podezřelý provoz

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Zjistila se kontrola adresy.":::

Příslušné informace se odesílají do partnerských systémů při vytváření pravidel předávání.

## <a name="create-forwarding-rules"></a>Vytvořit pravidla předávání

Vytvoření nového pravidla předávání:

1. V postranní nabídce vyberte **předat dál** .

   :: Image Type = "content" Source = "Media/How-to-work-with-výstrahy-snímač/create-forwarding-rule-screen.png" ALT-text = "vytvoření ikony pravidla pro předávání.":::

2. Vyberte **vytvořit pravidlo předávání**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Vytvořte nové pravidlo pro předávání.":::

3. Zadejte název pravidla předávání.

### <a name="forwarding-rule-criteria"></a>Kritéria pro předávání pravidel 

Definujte kritéria, podle kterých se má aktivovat pravidlo pro předávání. Práce s kritérii pravidla předávání pomáhá určit a spravovat objem informací odesílaných ze senzoru do externích systémů. Dostupné jsou tyto možnosti:

**Protokoly**: aktivuje pravidlo předávání jenom v případě, že zjištěný provoz probíhal přes konkrétní protokoly. V rozevíracím seznamu vyberte požadované protokoly nebo je vyberte vše.

**Moduly**: vyberte požadované moduly nebo je vyberte vše. Budou odesílány výstrahy z vybraných modulů.

**Úrovně závažnosti**: Jedná se o minimální incident, který se má přeposláním, s ohledem na úroveň závažnosti. Pokud například vyberete **dílčí** a vedlejší výstrahy a veškerá výstraha nad touto úrovní závažnosti bude předána. Úrovně jsou předdefinovány.

### <a name="forwarding-rule-actions"></a>Přesměrování akcí pravidel

Akce pravidla předávání instruují senzory, aby předávali informace o výstrahách dodavatelům nebo serverům partnerů. Pro každé pravidlo předávání můžete vytvořit více akcí.

Kromě předávacích akcí dodaných s vaším systémem mohou být při integraci s dodavateli partnerů k dispozici další akce. 

#### <a name="email-address-action"></a>Akce e-mailové adresy

Odešle e-mail, který obsahuje informace o výstrahách. Můžete zadat jednu e-mailovou adresu na pravidlo.

Definování e-mailu pro pravidlo předávání:

1. Zadejte jednu e-mailovou adresu. Pokud je potřeba odeslat víc než jednu poštu, vytvořte další akci.

2. Zadejte časové pásmo pro toto časové razítko pro detekci výstrahy na SIEM.

3. Vyberte **Odeslat**.

#### <a name="syslog-server-actions"></a>Akce serveru syslog

Podporovány jsou následující formáty:

- Textové zprávy

- CEF zprávy

- LEEF zprávy

- Zprávy objektů

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Vytvořte akce pravidla předávání.":::

Zadejte následující parametry:

- Název a port hostitele syslog.

- Protokol TCP a UDP.

- Časové pásmo pro časové razítko detekce výstrah na SIEM.

- Soubor certifikátu šifrování TLS a soubor klíče pro CEF servery (volitelné).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Nakonfigurujte šifrování pro pravidlo předávání.":::

| Výstupní pole textové zprávy syslog | Description |
|--|--|
| Datum a čas | Datum a čas, kdy počítač serveru syslog tyto informace přijal. |
| Priorita | Uživatel. Alert |
| Název hostitele | IP adresa senzoru |
| Protokol | Protokol TCP nebo UDP |
| Zpráva | Senzor: název snímače.<br /> Výstraha: název výstrahy.<br /> Typ: Typ výstrahy. Může se jednat o **porušení protokolu**, **porušení zásad**, **malware**, **anomálii** nebo **provoz**.<br /> Závažnost: Závažnost výstrahy. Může být **Upozornění**, **podverze**, **hlavní** nebo **kritická**.<br /> Zdroj: název zdrojového zařízení.<br /> Zdrojová IP adresa: IP adresa zdrojového zařízení.<br /> Cíl: název cílového zařízení.<br /> Cílová IP adresa: IP adresa cílového zařízení.<br /> Zpráva: zpráva výstrahy.<br /> Skupina výstrah: Skupina výstrah přidružená k výstraze. |


| Výstup objektu syslog | Description |
|--|--|
| Datum a čas |   Datum a čas, kdy počítač serveru syslog tyto informace přijal. |  
| Priorita |    Uživatel. Alert | 
| Název hostitele |    IP adresa snímače | 
| Zpráva | Název snímače: název zařízení. <br /> Čas upozornění: čas, kdy byla výstraha zjištěna: může se lišit od času počítače serveru syslog a závisí na konfiguraci časového pásma pravidla předávání. <br /> Název výstrahy: název výstrahy. <br /> Zpráva upozornění: zpráva výstrahy. <br /> Závažnost výstrahy: Závažnost výstrahy: **Upozornění**, **podverze**, **hlavní** nebo **kritická**. <br /> Typ výstrahy: **porušení protokolu**, **porušení zásad**, **malware**, **anomálie** nebo **provoz**. <br /> Protokol: protokol výstrahy.  <br /> **Source_MAC**: IP adresa, název, dodavatel nebo operační systém zdrojového zařízení. <br /> Destination_MAC: IP adresa, název, dodavatel nebo operační systém cíle. Pokud data chybí, bude hodnota **N/a**. <br /> alert_group: Skupina výstrah přidružená k výstraze. |


| Výstupní formát syslog CEF | Description |
|--|--|
| Datum a čas | Datum a čas, kdy počítač serveru syslog tyto informace přijal. |
| Priorita | Uživatel. Alert | 
| Název hostitele | IP adresa senzoru |
| Zpráva | CEF: 0 <br />Azure Defender for IoT <br />Název snímače: název zařízení snímače. <br />Verze senzoru <br />Název výstrahy: název výstrahy. <br />Msg: zpráva výstrahy. <br />protokol: protokol výstrahy. <br />závažnost: **Upozornění**, **podverze**, **hlavní** nebo **kritická**. <br />Typ: **porušení protokolu**, **porušení zásad**, **malware**, **anomálie** nebo **provozní**. <br /> začátek: čas, kdy byla výstraha zjištěna. <br />Může se lišit od času počítače serveru syslog a závisí na konfiguraci časového pásma pravidla předávání. <br />src_ip: IP adresa zdrojového zařízení.  <br />dst_ip: IP adresa cílového zařízení.<br />Cat: Skupina výstrah přidružená k výstraze.  |

| Výstupní formát syslog LEEF | Description |
|--|--|
| Datum a čas |   Datum a čas, kdy počítač serveru syslog tyto informace přijal. |  
| Priorita |    Uživatel. Alert | 
| Název hostitele |    IP adresa snímače |
| Zpráva | Název senzoru: název zařízení Azure Defender pro IoT. <br />LEEF: 1.0 <br />Azure Defender for IoT <br />Elektrické  <br />Verze senzoru <br />Upozornění pro Azure Defender pro IoT <br />title: název výstrahy. <br />Msg: zpráva výstrahy. <br />protokol: protokol výstrahy.<br />závažnost: **Upozornění**, **podverze**, **hlavní** nebo **kritická**. <br />Typ: Typ výstrahy: **porušení protokolu**, **porušení zásad**, **malware**, **anomálie** nebo **provoz**. <br />začátek: čas výstrahy.Všimněte si, že se může lišit od času počítačového serveru syslog. (To závisí na konfiguraci časového pásma.) <br />src_ip: IP adresa zdrojového zařízení.<br />dst_ip: IP adresa cílového zařízení. <br />Cat: Skupina výstrah přidružená k výstraze. |

Po zadání všech informací vyberte **Odeslat**.

#### <a name="netwitness-action"></a>Akce NetWitness

Odesílá informace o výstrahách na server NetWitness.

Definování parametrů předávání NetWitness:

1. Zadejte **název hostitele** NetWitness a informace o **portu** .

2. Zadejte časové pásmo pro toto časové razítko pro detekci výstrahy na SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Přidejte časové pásmo do pravidla pro předávání.":::

3. Vyberte **Odeslat**.

#### <a name="integrated-vendor-actions"></a>Integrované akce dodavatele

Je možné, že jste svůj systém spojili se zabezpečením, správou zařízení nebo jiným dodavatelem v oboru. Tyto integrace umožňují:

  - Odeslat informace o výstrahách.

  - Odesílá informace o inventáři zařízení.

  - Komunikujte s bránami firewall na straně dodavatele.

Integrace pomáhají přemostění dřív vytvářených řešení zabezpečení, vylepšit viditelnost zařízení a zrychlit reakci na úrovni systému, aby bylo možné rychleji zmírnit rizika.

V části akce můžete zadat přihlašovací údaje a další informace, které jsou potřeba ke komunikaci s integrovanými dodavateli.

Podrobnosti o nastavení pravidel předávání pro integrace najdete v příslušných článcích o integraci s partnery.

### <a name="test-forwarding-rules"></a>Pravidla předávání testů

Otestujte připojení mezi senzorem a partnerským serverem, který je definovaný ve vašich pravidlech pro předávání:

1. V dialogovém okně pravidlo pro **předávání** vyberte pravidlo.

2. Zaškrtněte políčko **Další** .

3. Vyberte **Odeslat zkušební zprávu**.

4. Chcete-li ověřit, zda byly přijaty informace odesílané senzorem, přečtěte si partnerský systém.

### <a name="edit-and-delete-forwarding-rules"></a>Upravit a odstranit pravidla předávání 

Postup úpravy pravidla předávání:

- Na obrazovce **pravidlo přeposílání** vyberte **Upravit** pod rozevírací nabídkou **Další** . Proveďte požadované změny a vyberte **Odeslat**.

Odebrání pravidla předávání:

- Na obrazovce **pravidlo pro předávání** vyberte **z rozevírací nabídky** **Odebrat** . V dialogovém okně s **upozorněním** vyberte **OK**.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Pravidla předávání a pravidla vyloučení výstrah

Je možné, že Správce definoval pravidla vyloučení výstrah. Tato pravidla správcům pomůžou získat podrobnější kontrolu nad spuštěním výstrahy tím, že na senzoru budou ignorovat události výstrah založené na různých parametrech. Tyto parametry můžou zahrnovat adresy zařízení, názvy výstrah nebo konkrétní senzory.

To znamená, že pravidla pro předávání, která definujete, se můžou ignorovat na základě pravidel vyloučení, která vytvořil správce. Pravidla vyloučení jsou definována v místní konzole pro správu.

## <a name="see-also"></a>Viz také

[Urychlení pracovních postupů výstrah](how-to-accelerate-alert-incident-response.md)
