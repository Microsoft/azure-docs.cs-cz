---
title: O integraci ServiceNow
description: Program Defender for IoT ICS pro ServiceNow poskytuje analytikům SOC, kteří mají multidimenzionální přehled o specializovaných protokolech a zařízeních IoT nasazených v průmyslových prostředích, spolu s analýzou chování s funkcí ICS pro rychlé odhalení podezřelého nebo neobvyklého chování.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786001"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>Defender pro aplikaci správy služby ICS pro IoT pro ServiceNow

Program Defender for IoT ICS pro ServiceNow poskytuje analytikům SOC, kteří mají multidimenzionální přehled o specializovaných protokolech a zařízeních IoT nasazených v průmyslových prostředích, spolu s analýzou chování s funkcí ICS pro rychlé odhalení podezřelého nebo neobvyklého chování. Jedná se o důležitý vývoj s ohledem na probíhající sbližování IT a k podpoře nových iniciativ IoT, jako jsou například inteligentní počítače a analytické informace v reálném čase.

Aplikace taky v rámci jednoho podnikového SOCu umožňuje také reakci na incidenty IT i OT.

## <a name="about-defender-for-iot"></a>O programu Defender pro IoT

Defender pro IoT přináší jedinou platformu ICS a IoT kyberbezpečnosti vytvořenou odborníky z oblasti Blue-tým se záznamem sledování, který chrání kritickou národní infrastrukturu a jedinou platformou s patentovou analýzou ochrany před internetovými útoky a strojovým učením. Defender pro IoT poskytuje:

- Okamžité přehledy o ICS zařízení na šířku s rozsáhlým rozsahem podrobností o atributech.

- Bohaté informace o protokolech, zařízeních, aplikacích a jejich chování s obsáhlou integrovanou službou ICS

- Okamžité přehledy o chybách zabezpečení a známých hrozeb.

- Automatizovaná technologie ICS pro vytváření hrozeb pro předpověď nejpravděpodobnějších cest k cíleným útokům ICS prostřednictvím proprietárních analýz.

> [!Note]
> Odkazy na CyberX odkazují na Azure Defender pro IoT.

## <a name="about-the-integration"></a>O integraci

Integrace programu Defender for IoT s ServiceNow poskytuje novou úroveň centralizované viditelnosti, monitorování a řízení pro IoT a na šířku. Tyto přemostění platforem umožňují automatizovanou viditelnost zařízení a správu hrozeb předtím, než je ICS & zařízení IoT.

### <a name="threat-management"></a>Řízení rizik

Aplikace Defender pro správu služby ICS pro IoT pomáhá:

- Zkraťte dobu potřebnou k tomu, aby organizace v průmyslu a kritické infrastruktuře zjistily, prozkoumaly a jednaly o počítačové hrozby.

- Získejte informace o rizicích za v reálném čase.

- Korelace Defenderu pro výstrahy IoT s pracovními postupy pro monitorování hrozeb ServiceNow a řešení incidentů.

- Aktivujte ServiceNow lístky a pracovní postupy s dalšími službami a aplikacemi na platformě ServiceNow.

Bezpečnostní hrozby ICS a SCADA se identifikují v programu Defender pro bezpečnostní moduly IoT, které poskytují okamžitou reakci na výstrahy týkající se útoků na malware, sítě a zásad zabezpečení a také provozní a bezpečnostní anomálie. Podrobnosti o podrobnostech o výstrahách odeslaných na ServiceNow najdete v tématu [vytváření sestav výstrah](#alert-reporting).

### <a name="device-visibility-and-management"></a>Viditelnost a Správa zařízení

Databáze správy konfigurace ServiceNow (CMDB) je obohacena a doplněna bohatou sadou atributů zařízení, kterou odeslal Defender pro platformu IoT. Díky tomu je zajištěná komplexní a nepřetržitá viditelnost zařízení na šířku a umožňuje monitorovat a reagovat z jednoho podokna. Podrobnosti o atributech zařízení odeslaných do ServiceNowSee najdete [v tématu zobrazení Defenderu pro zjišťování IoT v ServiceNow](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Požadavky na systém a architektura

Tento článek popisuje:

- **Požadavky na software**  
- **Architektura**

## <a name="software-requirements"></a>Požadavky na software

- Správa služeb ServiceNow verze 3.0.2

- Defender pro IoT patch 2.8.11.1 nebo novější

> [!Note]
> Pokud už pracujete s Defenderem pro integraci IoT a ServiceNow a upgradujete pomocí místní konzoly pro správu, měli byste z ServiceNow vymazat data zkontrolují přijatá z Defenderu pro senzory IoT.

## <a name="architecture"></a>Architektura

### <a name="on-premises-management-console-architecture"></a>Architektura místní konzoly pro správu

Místní Konzola pro správu poskytuje jednotný zdroj pro všechny informace o zařízení a výstrahy odesílané do ServiceNow.

Můžete nastavit místní konzolu pro správu, která bude komunikovat s jednou instancí ServiceNow. Místní Konzola pro správu odesílá data ze senzorů do aplikace Defender for IoT pomocí REST API.

Pokud nastavujete systém pro práci s místní konzolou pro správu, zakažte ServiceNow synchronizaci, pravidla předávání a konfigurace proxy serveru v senzorech, pokud byly nastaveny.

Tyto konfigurace by se měly nastavit pro místní konzolu pro správu. Pokyny ke konfiguraci jsou popsány v tomto článku.

### <a name="sensor-architecture"></a>Architektura senzorů

Pokud chcete nastavit prostředí tak, aby zahrnovalo přímou komunikaci mezi snímači a ServiceNow, můžete pro každý senzor definovat ServiceNow synchronizaci, pravidla předávání a konfiguraci proxy serveru (Pokud je potřeba proxy).

Doporučuje se nastavit integraci pomocí místní konzoly pro správu, aby komunikovala s ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Vytváření přístupových tokenů v ServiceNow

Tento článek popisuje, jak v ServiceNow vytvořit přístupový token. Token je nutný ke komunikaci s Defenderem pro IoT.

Při vytváření Defenderu pro pravidla předávání pro IoT budete potřebovat **ID klienta** a **tajný klíč klienta** , které předají informace o výstraze do ServiceNow, a když se nakonfiguruje Defender pro IoT, aby zadával atributy zařízení do tabulek ServiceNow.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Nastavení programu Defender pro IoT ke komunikaci s ServiceNow

Tento článek popisuje, jak nastavit Defender pro IoT ke komunikaci s ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Odeslat Defender pro informace o výstrahách IoT

Tento článek popisuje, jak nakonfigurovat Defender pro IoT, aby nabízel informace o výstrahách do tabulek ServiceNow. Informace o odeslaných datech výstrah najdete v tématu [vytváření sestav výstrah](#alert-reporting).

V ServiceNow se jako incidenty zabezpečení zobrazí v.

Definujte pravidlo pro *předávání* v programu Defender pro IoT, které odesílá informace o výstrahách do ServiceNow.

Postup při definování pravidla:

1. V levém podokně Defender pro IoT vyberte **předat dál**.  

1. Vyberte :::image type="content" source="media/integration-servicenow/plus.png" alt-text="tlačítko s ikonou plus."::: hamburgeru. Otevře se dialogové okno vytvořit pravidlo předávání.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Vytvořit předávací pravidlo":::

1. Přidejte název pravidla.

1. Definujte kritéria, podle kterých bude Defender pro IoT aktivovat pravidlo předávání. Práce s kritérii pravidla předávání pomáhá identifikovat a spravovat objem informací odeslaných z programu Defender pro IoT do ServiceNow. Dostupné jsou tyto možnosti:

    - **Úrovně závažnosti:** Toto je incident nejnižší úrovně zabezpečení pro přeposlání. Pokud je například vybraná možnost **podverze** , podvýstrahy a veškerá výstraha nad touto úrovní závažnosti se předají. Úrovně jsou předem definovány v programu Defender pro IoT.

    - **Protokoly:** Pravidlo předávání aktivujte jenom v případě, že se zjistilo, že provoz běžel přes konkrétní protokoly. V rozevíracím seznamu vyberte požadované protokoly nebo je vyberte vše.

    - **Moduly:** Vyberte požadované moduly nebo je vyberte vše. Budou odesílány výstrahy z vybraných modulů.

1. Ověřte, že je vybraná možnost **oznámení výstrah sestav** .

1. V části Akce vyberte **Přidat** a pak vyberte **ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Z možností rozevíracího seznamu vyberte ServiceNow.":::

1. Zadejte parametry akce ServiceNow:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Vyplňte parametry akce ServiceNow.":::

1. V podokně **Akce** nastavte následující parametry:

  | Parametr | Popis |
  |--|--|
  | Doména | Zadejte IP adresu serveru ServiceNow. |
  | Uživatelské jméno | Zadejte uživatelské jméno ServiceNow serveru. |
  | Heslo | Zadejte heslo serveru ServiceNow. |
  | ID klienta | Zadejte ID klienta, které jste dostali pro Defender pro IoT, na stránce **Registry aplikací** v ServiceNow. |
  | Tajný klíč klienta | Zadejte řetězec tajného klíče klienta, který jste vytvořili pro Defender pro IoT, na stránce **Registry aplikací** v ServiceNow. |
  | Typ sestavy | **Incidenty**: předejte seznam výstrah, které jsou prezentovány v SERVICENOW s ID incidentu a stručným popisem jednotlivých výstrah.<br /><br />**Defender for IoT Application**: předejte úplné informace o výstrahách, včetně podrobností snímačů, modulu, zdroje a cílových adres. Informace se předávají do programu Defender for IoT v aplikaci ServiceNow. |

1. Vyberte **Uložit**. V ServiceNow se zobrazí jako incidenty služby pro ochranu IoT.

### <a name="send-defender-for-iot-device-attributes"></a>Poslat Defender pro atributy zařízení IoT

Tento článek popisuje, jak nakonfigurovat program Defender pro IoT tak, aby do tabulek ServiceNow nanabízel velký rozsah atributů zařízení. Podrobnosti o druhu nabízených informací do ServiceNow najdete v ***informacích o inventáři*** .

K odeslání atributů do ServiceNow je nutné mapovat místní konzolu pro správu na instanci ServiceNow. Tím zajistíte, aby platforma Defender pro IoT mohla komunikovat s instancí a ověřit ji.

Přidání instance ServiceNow:

1. Přihlaste se k programu Defender pro místní konzolu pro správu IoT.

1. Vyberte **nastavení systému** a potom **ServiceNow** z části věnované integraci konzoly pro správu.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Vyberte tlačítko ServiceNow.":::

1. V dialogovém okně synchronizace ServiceNow zadejte následující parametry synchronizace.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="Dialogové okno ServiceNow Sync.":::

     Parametr | Popis |
    |--|--|
    | Povolit synchronizaci | Po definování parametrů povolte a zakažte synchronizaci. |
    | Frekvence synchronizace (minuty) | Ve výchozím nastavení se informace odesílají do ServiceNow každých 60 minut. Minimum je 5 minut. |
    | Instance ServiceNow | Zadejte adresu URL instance ServiceNow. |
    | ID klienta | Zadejte ID klienta, které jste dostali pro Defender pro IoT, na stránce **Registry aplikací** v ServiceNow. |
    | Tajný klíč klienta | Zadejte řetězec tajného klíče klienta, který jste vytvořili pro Defender pro IoT, na stránce **Registry aplikací** v ServiceNow. |
    | Uživatelské jméno | Zadejte uživatelské jméno pro tuto instanci. |
    | Heslo | Zadejte heslo pro tuto instanci. |

1. Vyberte **Uložit**.

## <a name="verify-communication"></a>Ověřit komunikaci

Zkontrolujte, jestli je místní Konzola pro správu připojená k instanci ServiceNow, kontrolou data *Poslední synchronizace* .

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Prohlédněte si poslední synchronizaci, abyste ověřili, že komunikace proběhla.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Nastavení integrace pomocí proxy serveru HTTPS

Při nastavování Defenderu pro integraci IoT a ServiceNow, místní konzole pro správu a server ServiceNow komunikují pomocí portu 443. Pokud je server ServiceNow za proxy serverem, nelze použít výchozí port.

Defender for IoT podporuje proxy HTTPS v integraci ServiceNow tím, že umožňuje změnu výchozího portu používaného pro integraci.

Konfigurace proxy serveru:

1. Upravit globální vlastnosti v místní konzole pro správu:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Přidejte následující parametry:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Uložte a ukončete.

4. Spusťte následující příkaz: `sudo monit restart all`

Po dokončení konfigurace se všechna data ServiceNow předají pomocí nakonfigurovaného proxy serveru.

## <a name="download-the-defender-for-iot-application"></a>Stažení aplikace Defender for IoT

Tento článek popisuje, jak stáhnout aplikaci.

Přístup k aplikaci Defender for IoT:

1. Přejděte na adresu <https://store.servicenow.com/>.

2. Vyhledejte `Defender for IoT` nebo `CyberX IoT/ICS Management` .

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Na panelu hledání vyhledejte CyberX.":::

3. Vyberte aplikaci.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Vyberte aplikaci ze seznamu.":::

4. Vyberte **aplikace žádosti.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Přihlaste se k aplikaci pomocí svých přihlašovacích údajů.":::

5. Přihlaste se a stáhněte aplikaci.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Zobrazení Defenderu pro zjišťování IoT v ServiceNow

Tento článek popisuje atributy zařízení a informace o výstrahách prezentovaných v ServiceNow.

Zobrazení atributů zařízení:

1. Přihlaste se k ServiceNow.

2. Přejděte na **platformu CyberX**.

3. Přejděte do **inventáře** nebo **výstrahy**.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Inventář nebo výstraha":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Informace o inventáři

Databáze správy konfigurace (CMDB) je obohacena a doplněna daty odesílanými programem Defender pro IoT do ServiceNow. Přidáním nebo aktualizací atributů zařízení v tabulkách položek konfigurace CMDB v ServiceNow může Defender pro IoT aktivovat pracovní postupy ServiceNow a obchodní pravidla.

K dispozici jsou následující informace:

- Byly zjištěny atributy zařízení, například adresa MAC zařízení, operační systém, výrobce nebo protokol.

- Informace o firmwaru, například verze firmwaru a sériové číslo.

- Informace o připojeném zařízení, například směr přenosu dat mezi zdrojem a cílem.

### <a name="devices-attributes"></a>Atributy zařízení

Tento článek popisuje atributy zařízení nabízené do ServiceNow.

| Položka | Popis |
|--|--|
| Náplně | Název senzoru, který zjistil provoz. |
| ID | ID zařízení přiřazené programem Defender pro IoT |
| Name | Název zařízení |
| IP adresa | IP adresa nebo adresa zařízení. |
| Typ | Typ zařízení, například přepínač, PLC, historian nebo řadič domény. |
| Adresa MAC | Adresa MAC zařízení nebo adresy. |
| Operační systém | Operační systém zařízení. |
| Dodavatel | Dodavatel zařízení. |
| Protokoly | Protokoly zjištěné v provozu vygenerovaném zařízením. |
| Vlastník | Zadejte jméno vlastníka zařízení. |
| Umístění | Zadejte fyzické umístění zařízení. |

Umožňuje zobrazit zařízení připojená k zařízení v tomto zobrazení.

Zobrazení připojených zařízení:

1. Vyberte zařízení a pak **Vyberte zařízení uvedené v části pro** toto zařízení.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Vyberte požadované zařízení ze seznamu.":::

1. V dialogovém okně **Podrobnosti o zařízení** vyberte **připojená zařízení**.

### <a name="firmware-details"></a>Podrobnosti firmwaru

Tento článek popisuje informace o firmwaru zařízení nabízené do ServiceNow.

| Položka | Popis |
|--|--|
| Náplně | Název senzoru, který zjistil provoz. |
| Zařízení | Název zařízení |
| Adresa | IP adresa zařízení. |
| Adresa modulu | Model zařízení a číslo slotu nebo ID. |
| Sér | Sériové číslo zařízení. |
| Modelování | Číslo modelu zařízení. |
| Verze | Číslo verze firmwaru. |
| Další data | Další data o firmwaru definovaném dodavatelem, například typ zařízení. |

### <a name="connection-details"></a>Podrobnosti připojení

Tento článek popisuje informace o připojení zařízení nabízené do ServiceNow.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="Informace o připojení zařízení":::

| Položka | Popis |
|--|--|
| Náplně | Název senzoru, který zjistil provoz. |
| Směr | Směr provozu. <br /> <br /> - **Jednosměrně** znamená, že cílem je server a zdroj je klient. <br /> <br /> - **Dva způsoby** značí, že zdroj i cíl jsou servery nebo že je klient neznámý. |
| ID zdrojového zařízení | IP adresa zařízení, které komunikuje s připojeným zařízením. |
| Název zdrojového zařízení | Název zařízení, které komunikuje s připojeným zařízením. |
| ID cílového zařízení | IP adresa připojeného zařízení. |
| Název cílového zařízení | Název připojeného zařízení. |

## <a name="alert-reporting"></a>Vytváření sestav výstrah

Výstrahy se aktivují, když se ke službě IoT Engines detekuje změny v síťovém provozu a chování, které vyžadují vaši pozornost. Podrobnosti o typech výstrah generovaných každým modulem najdete v tématu [o](#about-alert-engines)modulech výstrah.

Tento článek popisuje informace o výstrahách zařízení nabízených do ServiceNow.

| Položka | Popis |
|--|--|
| Vytvořeno | Čas a datum, kdy byla výstraha vygenerována. |
| Modul | Modul, který zjistil událost. |
| Nadpis | Název výstrahy. |
| Popis | Popis výstrahy |
| Protokol | Protokol zjištěný v provozu. |
| Závažnost | Závažnost výstrahy definovaná v programu Defender pro IoT |
| Náplně | Název senzoru, který zjistil provoz. |
| Název zdroje | Název zdroje |
| Zdrojová IP adresa| Zdrojová IP adresa. |
| Název cíle | Název cíle |
| Cílová IP adresa | Cílová IP adresa. |
| Zmocněn | Zadejte jméno jednotlivce přiřazené k lístku. |

### <a name="updating-alert-information"></a>Aktualizace informací o výstrahách

Vyberte položku ve sloupci vytvořeno pro zobrazení informací o výstrahách ve formuláři. Můžete aktualizovat podrobnosti výstrahy a přiřadit upozornění osobě pro kontrolu a zpracování.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Zobrazit informace o výstraze.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>O modulech upozornění

Tento článek popisuje typ výstrah, které jednotlivé moduly aktivují.

| Typ upozornění | Popis |
|--|--|
| Upozornění na porušení zásad | Aktivováno, když modul porušení zásad detekuje odchylku od dříve zjištěného provozu. Například: <br /><br />– Zjistilo se nové zařízení. <br /><br />– V zařízení se zjistila nová konfigurace. <br /><br />– Zařízení, které není definované jako programové zařízení, provede změnu v programování. <br /><br />– Změnila se verze firmwaru. |
| Výstrahy porušení protokolu | Aktivováno, když modul porušení protokolu detekuje struktury paketů nebo hodnoty polí, které nevyhovují specifikaci protokolu. |
| Provozní výstrahy | Aktivuje se, když provozní modul detekuje provozní incidenty sítě nebo zařízení nefunguje. Například síťové zařízení bylo zastaveno pomocí příkazu zastavit PLC nebo rozhraní na senzoru zastavilo monitorování provozu. |
| Výstrahy malwaru | Aktivováno, když malware detekuje aktivitu škodlivou v síti, například známé útoky, jako je například Conficker. |
| Výstrahy anomálií | Aktivováno, když modul anomálií detekuje odchylku. Zařízení například provádí kontrolu sítě, ale není definováno jako skenovací zařízení. |

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [přeslat informace o výstrahách](how-to-forward-alert-information-to-partners.md).
