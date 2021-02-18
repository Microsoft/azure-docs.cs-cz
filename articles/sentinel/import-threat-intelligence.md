---
title: Import analýzy hrozeb do služby Azure Sentinel | Microsoft Docs
description: Pomocí kanálů analýzy hrozeb v Azure Sentinel můžete analyzovat data, zjišťovat hrozby a generovat upozornění a incidenty. Vizualizujte informace analýzy hrozeb pomocí sešitů.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 0b2a6088effc735076d56ba83dd85135392147b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574985"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Import analýzy hrozeb do Azure Sentinelu

## <a name="introduction-to-threat-intelligence"></a>Seznámení s analýzou hrozeb

Internet Threat Intelligence (CTI) je informace popisující známé existující nebo potenciální hrozby pro systémy a uživatele. Tento typ informací má mnoho forem, od písemných sestav, které podrobně popisují podněty, infrastrukturu a techniky určitého objektu actor, pro konkrétní pozorování IP adres, domén a hodnot hash souborů souvisejících s internetovými hrozbami. Společnost CTI používá organizace k poskytování základního kontextu neobvyklých aktivit, aby pracovníci zabezpečení mohli rychle provést opatření k ochraně svých lidí a prostředků. CTI se dá nacházet z mnoha míst, jako jsou Open Source datové kanály, komunity pro sdílení hrozeb, kanály komerčních kurzů a místní informace shromážděné v rámci šetření zabezpečení v organizaci.

V rámci řešení SIEM (Security Information and Event Management), jako je Azure Sentinel, je nejužitečnější forma CTI indikátorem hrozeb, která se často označuje jako indikátory kompromisů nebo IoCs. Indikátory hrozeb jsou data, která přidružuje pozorování, jako jsou adresy URL, hodnoty hash souborů nebo IP adresy se známou aktivitou hrozby, jako je phishing, botnety nebo malware. Tato forma analýzy hrozeb se často označuje jako taktické Threat Intelligence, protože se dá použít pro zabezpečovací produkty a automatizaci ve velkém měřítku pro ochranu a detekci potenciálních hrozeb v organizaci. V Azure Sentinel můžete použít indikátory hrozeb, které vám pomůžou odhalit škodlivou aktivitu zjištěnou ve vašem prostředí a poskytnout kontext vyšetřovacích zkoušejících, aby mohli pomáhat s rozhodováním o reakci.

Do služby Azure Sentinel můžete integrovat analýzu hrozeb (ČŘ) prostřednictvím následujících činností:

- Pomocí **datových konektorů** k různým platformám ČŘ importujte do Azure Sentinel [Intelligence](./connect-threat-intelligence.md) .
- Zobrazení a Správa importovaných informací o hrozbách v **protokolech** a v oblasti nová **Analýza hrozeb** v rámci služby Azure Sentinel.
- Pomocí integrovaných šablon pravidel **analýzy** můžete vygenerovat výstrahy zabezpečení a incidenty pomocí importované analýzy hrozeb.
- Pomocí **sešitu analýzy hrozeb** Vizualizujte klíčové informace o vaší analýze hrozeb v Azure Sentinel.

Analýza hrozeb taky nabízí užitečný kontext v rámci jiných prostředí s Sentinelou v Azure, jako jsou například **lovecké** a **poznámkové bloky**, a i když tento článek není popsaný v tomto článku, řeší se tato prostředí v [tomto skvělém příspěvku na blogu Ian Hellen v Jupyterch poznámkových blocích v Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239), který pokrývá použití cti v

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Datové konektory Azure Sentinel pro analýzu hrozeb

Stejně jako u všech ostatních dat událostí ve službě Azure Sentinel se indikátory hrozeb importují pomocí datových konektorů. Ve službě Azure Sentinel existují dva datové konektory, které jsou určené konkrétně pro indikátory hrozeb, pro **TAXII** a **analytické platformy** pro analýzy hrozeb. V závislosti na tom, kde vaše organizace má indikátory ohrožení zdrojů, můžete použít buď samostatné konektory dat, nebo oba konektory současně. Pojďme se na každou z datových konektorů spojit.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Přidání indikátorů hrozeb do Azure Sentinel s datovým konektorem pro datové platformy pro analýzu hrozeb

Mnoho organizací využívá řešení pro analýzu hrozeb (Threat Intelligence Platform) k agregaci kanálů indikátorů hrozeb z nejrůznějších zdrojů, ke využívání dat v rámci platformy a k výběru, které indikátory hrozeb se mají použít u různých řešení zabezpečení, jako jsou síťová zařízení, rozšířená řešení ochrany před internetovými útoky nebo systémů Siem, jako je například Azure Sentinel. Pokud vaše organizace využívá integrované řešení TIP, jako je MISP, anomálie ThreatStream, ThreatConnect, EclecticIQ Platform, ThreatQ Intelligence Intelligence Platform nebo Palo Alto Networks, umožňuje **konektor data Intelligence** pro analýzy hrozeb použít k importu indikátorů hrozeb do služby Azure Sentinel. Vzhledem k tomu, že konektor spolupracuje s [rozhraním api Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator) , konektor se dá použít i v jakékoli vlastní platformě pro analýzu hrozeb, pomocí které můžete využít rozhraní tiIndicators API k posílání ukazatelů do Azure Sentinel (a dalších řešení Microsoftu, jako je třeba ATP v Defenderu).

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Cesta pro import Intelligence Threat":::

Pomocí těchto kroků importujte indikátory hrozeb do Azure Sentinel z integrovaného řešení TIP nebo vlastního řešení pro analýzu hrozeb:

1. Získat ID aplikace a tajný klíč klienta z Azure Active Directory

1. Zadejte tyto informace do svého řešení nebo vlastní aplikace.

1. Povolení konektoru dat pro datové platformy pro rozhraní Threat Intelligence v Azure Sentinel

Tady je podrobný pohled na každý z těchto kroků.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Získat ID aplikace a tajný klíč klienta z Azure Active Directory

Bez ohledu na to, jestli pracujete s tipem nebo s vlastním řešením, vyžaduje rozhraní tiIndicators API základní informace pro připojení a odesílání indikátorů hrozeb. Tyto tři části informací, které budete potřebovat, jsou:
- ID aplikace (klienta)
- ID adresáře (tenanta)
- Tajný klíč klienta

Tyto informace se vždycky dodávají z vašeho Azure Active Directory pomocí procesu nazývaného **Registrace aplikace** , který obsahuje následující tři kroky:
- Registrace aplikace pomocí Azure Active Directory
- Určete oprávnění, která aplikace požaduje pro připojení k Microsoft Graph rozhraní API tiIndicators a odesílání indikátorů hrozeb.
- Získejte souhlas od vaší organizace, aby tato oprávnění udělila této aplikaci.  

**Registrujte aplikaci s Azure Active Directory**

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte ke službě **Azure Active Directory** .

1. V nabídce vyberte **Registrace aplikací** a vyberte **Nová registrace**.

1. Zvolte název registrace vaší aplikace, vyberte přepínač **jednoho tenanta** a vyberte **Registrovat**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Registrace aplikace":::

1. Z výsledné obrazovky zkopírujte hodnoty ID **aplikace (klienta)** a **ID adresáře (tenant)** . Toto jsou první dvě informace, které později budete potřebovat ke konfiguraci tipu nebo vlastního řešení pro odesílání indikátorů hrozeb do Azure Sentinel.

**Zadejte oprávnění požadovaná aplikací.**

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte ke službě **Azure Active Directory** .

1. V nabídce vyberte **Registrace aplikací** a vyberte svou nově registrovanou aplikaci.

1. V nabídce vyberte **oprávnění rozhraní API** a klikněte na tlačítko **Přidat oprávnění** .

1. Na stránce **Vybrat rozhraní API** vyberte **Microsoft Graph** a vyberte ze seznamu oprávnění Microsoft Graph.

1. Když se zobrazí dotaz **, jaký typ oprávnění vaše aplikace požaduje?** vyberte **oprávnění aplikace**. Toto je typ oprávnění používaných aplikacemi, které se ověřují pomocí ID aplikace a tajného klíče aplikace (klíče rozhraní API).

1. Vyberte **ThreatIndicators. OwnedBy.** Pokud chcete přidat toto oprávnění do seznamu oprávnění vaší aplikace, vyberte **Přidat oprávnění** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Určení oprávnění":::

**Získat souhlas od vaší organizace, aby udělil tato oprávnění**

1. Pokud chcete udělit souhlas, budete potřebovat globálního správce Azure Active Directory pro výběr tlačítka **udělit souhlas správce pro vašeho tenanta** na stránce oprávnění rozhraní API vaší aplikace. Pokud ve svém účtu nemáte roli globálního správce, toto tlačítko nebude k dispozici a vy budete muset požádat globálního správce z vaší organizace, aby tento krok provedl.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Udělit souhlas":::

1. Po udělení souhlasu vaší aplikaci by se měla zobrazit zelená značka zaškrtnutí pod položkou **stav**.
 
Teď, když je vaše aplikace registrovaná a máte udělená oprávnění, můžete získat poslední položku v seznamu – tajný klíč klienta pro vaši aplikaci.

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte ke službě **Azure Active Directory** .

1. V nabídce vyberte **Registrace aplikací** a vyberte svou nově registrovanou aplikaci.

1. V nabídce vyberte **certifikáty & tajné klíče** a kliknutím na tlačítko **nový tajný klíč klienta** Získejte tajný klíč (klíč rozhraní API) pro vaši aplikaci.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Získat tajný klíč klienta":::

1. Klikněte na tlačítko **Přidat** a **Nezapomeňte zkopírovat tajný klíč klienta**, protože tento tajný klíč nemůžete znovu načíst, když opustíte tuto stránku. Tuto hodnotu budete potřebovat při konfiguraci vašeho tipu nebo vlastního řešení.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Zadejte tyto informace do svého řešení nebo vlastní aplikace.

Teď máte k dispozici všechny tři informace, které potřebujete ke konfiguraci tipu nebo vlastního řešení pro odesílání indikátorů hrozeb do Azure Sentinel. 
- ID aplikace (klienta)
- ID adresáře (tenanta)
- Tajný klíč klienta

V případě potřeby zadejte tyto hodnoty v konfiguraci integrovaného tipu nebo vlastního řešení a indikátory hrozeb se budou posílat prostřednictvím rozhraní Microsoft Graph API tiIndicators zaměřeného na službu Azure Sentinel.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Povolení konektoru dat pro datové platformy pro rozhraní Threat Intelligence v Azure Sentinel

Posledním krokem v procesu integrace je povolit konektor dat pro **platformy pro analýzu hrozeb** v Azure Sentinel. Toto je krok, který importuje indikátory hrozeb odeslané z vašeho tipu nebo vlastního řešení prostřednictvím rozhraní API Microsoft Graph tiIndicators do Azure Sentinel. Tyto indikátory budou k dispozici pro všechny pracovní prostory Azure Sentinel pro vaši organizaci. Pomocí těchto kroků můžete pro každý pracovní prostor povolit datové konektory pro platformy Threat Intelligence:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte do služby **Azure Sentinel** .

1. Vyberte **pracovní prostor** , do kterého chcete importovat ukazatele hrozeb odesílané z vašeho tipu nebo vlastního řešení.

1. V nabídce vyberte **datové konektory** , z Galerie konektorů vyberte **platformy Threat Intelligence** a klikněte na tlačítko **otevřít stránku konektoru** .

1. Jak jste už dokončili registraci aplikace a nakonfigurovali jste TIP nebo vlastní řešení pro odesílání indikátorů hrozeb, jediným krokem je kliknutí na tlačítko **připojit** .

Během několika minut by se měly indikátory hrozby začít přesměrovat do tohoto pracovního prostoru služby Azure Sentinel.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Přidání indikátorů hrozeb do služby Azure Sentinel pomocí konektoru pro analýzu hrozeb – TAXII data Connector

Nejpoužívanějším oborovým standardem pro přenos analýzy hrozeb je [kombinace formátu dat Stix a protokolu TAXII](https://oasis-open.github.io/cti-documentation/). Pokud vaše organizace získá indikátory hrozeb z řešení, která podporují aktuální verzi STIX/TAXII (2,0 nebo 2,1), můžete pomocí konektoru data **Intelligence – TAXII** data Connector přenést své indikátory hrozeb do Azure Sentinel. TAXII data Connector umožňuje integrovanému TAXII klientovi ve službě Azure Sentinel pro import analýzy hrozeb ze serverů TAXII 2. x.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Cesta pro import TAXII":::
 
Pomocí těchto kroků importujte STIX naformátované indikátory hrozeb do Azure Sentinel ze serveru TAXII:

1. Získání ID kořene a ID kolekce rozhraní API serveru TAXII

1. Povolení konektoru pro analýzu hrozeb – TAXII data Connector v Azure Sentinel

Teď se podíváme na každý z těchto kroků.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>Získání ID kořene a ID kolekce rozhraní API serveru TAXII

TAXII 2. x servery inzerují kořeny rozhraní API, což jsou adresy URL, které hostují kolekce analýz hrozeb. Nejčastěji se kořen rozhraní API dá získat prostřednictvím stránky dokumentace poskytovatele analýzy hrozeb, který hostuje server TAXII. V některých případech jsou však jedinou inzerovanou informací adresa URL známá jako koncový bod zjišťování. Pokud se jedná o tento případ, je snadné najít kořenový adresář rozhraní API pomocí koncového bodu zjišťování. Pokud už znáte kořen rozhraní API serveru TAXII a ID kolekcí, se kterými chcete pracovat, můžete přejít k další části a **Povolit konektor dat Analýza hrozeb – TAXII data Connector v Azure Sentinel**.

Pojďme se podívat na skutečný příklad použití jednoduchého nástroje příkazového řádku s názvem [kudrlinkou](https://en.wikipedia.org/wiki/CURL), který je k dispozici v systému Windows a většině distribucí pro Linux, ke zjištění KOŘENU rozhraní API a procházení kolekcí serveru TAXII, který začíná jenom z koncového bodu zjišťování. V tomto příkladu použijeme koncový bod zjišťování serveru TAXII 2,0 se [anomálií Limo](https://www.anomali.com/community/limo) ThreatStream.

1.  V prohlížeči přejděte na [koncový bod zjišťování serveru THREATSTREAM TAXII 2,0](https://limo.anomali.com/taxii) a načtěte kořenový adresář rozhraní API. Ověřte s uživatelem a heslem `guest` .

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Pomocí nástroje složeného a kořene rozhraní API ( https://limo.anomali.com/api/v1/taxii2/feeds/) z předchozího kroku můžete procházet seznam ID kolekcí hostovaných v kořenovém adresáři rozhraní API).

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Nyní máte všechny informace, které potřebujete k připojení Azure Sentinel k jedné nebo více kolekcím serveru TAXII, které nabízí anomálie Limo.

| **Kořen rozhraní API** (https://limo.anomali.com/api/v1/taxii2/feeds/) | ID kolekce |
| ------------------------------------------------------------ | ------------: |
| **Nephishingová nádrž**                                               | 107           |
| **Abuse.ch ransomwarem IP adresy**                                  | 135           |
| **Abuse.ch ransomwarem domény**                              | 136           |
| **DShield kontroly IP adres**                                     | 150           |
| **Seznam domén malwaru – hotlist**                            | 200           |
| **Blutmagie uzly pro mandát**                                      | 209           |
| **Vznikající hrozby C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **Kybernetická**                                               |  41           |
| **Vznikající ohrožené hrozby**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Povolení konektoru pro analýzu hrozeb – TAXII data Connector v Azure Sentinel

Pokud chcete naimportovat indikátory hrozeb do Azure Sentinel ze serveru TAXII, použijte následující postup:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte do služby **Azure Sentinel** .

1. Vyberte **pracovní prostor** , do kterého chcete naimportovat indikátory hrozeb ze serveru TAXII.

1. V nabídce vyberte **datové konektory** , z Galerie konektorů vyberte **Threat Intelligence-TAXII** a klikněte na tlačítko **otevřít stránku konektoru** .

1. Zadejte **název** pro tuto kolekci serveru TAXII, **adresu URL kořenového adresáře rozhraní API**, **ID kolekce**, **uživatelské jméno** (Pokud se vyžaduje) a **heslo** (Pokud se vyžaduje) a klikněte na tlačítko **Přidat** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="Konfigurace serverů TAXII":::
 
Měli byste obdržet potvrzení, že připojení k TAXII serveru bylo úspěšně navázáno, a můžete opakovat krok (4) výše, kolikrát je potřeba se připojit k více kolekcím ze stejných nebo různých serverů TAXII.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Zobrazit indikátory hrozeb v Azure Sentinel

Teď, když jste úspěšně naimportovali indikátory hrozeb do Azure Sentinel pomocí platforem pro analýzy **hrozeb** a/nebo datového konektoru **Threat Intelligence-TAXII** , je můžete zobrazit v tabulce **ThreatIntelligenceIndicator** v **protokolech** , kde jsou uložená všechna vaše data události Sentinel Azure. Tato tabulka je základem pro dotazy prováděné jinými funkcemi Sentinel Azure, jako jsou analytické a sešity. Tady je postup, jak najít a zobrazit indikátory hrozeb v tabulce **ThreatIntelligenceIndicator** .

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte do služby **Azure Sentinel** .

1. Vyberte **pracovní prostor** , do kterého jste naimportovali ukazatele hrozeb pomocí datového konektoru pro analýzu hrozeb.

1. V části **Obecné** v nabídce Sentinel Azure vyberte **protokoly** .

1. Tabulka **ThreatIntelligenceIndicator** se nachází ve skupině **Sentinel Azure** .

1. Vyberte ikonu **náhledu dat** (oka) vedle názvu tabulky a výběrem tlačítka **Zobrazit v editoru dotazů** spusťte dotaz, který zobrazí záznamy z této tabulky.

Výsledky by měly vypadat podobně jako na indikátoru ukázkové hrozby uvedené níže:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Ukázková data dotazů":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Správa indikátorů hrozeb v oblasti nová Analýza hrozeb v Azure Sentinel

Díky nové oblasti analýzy **hrozeb** dostupné v nabídce Sentinel Azure můžete také zobrazit, třídit, filtrovat a hledat importované indikátory hrozeb bez nutnosti psát dotaz na **protokoly** . Tato nová oblast také umožňuje vytvářet indikátory hrozeb přímo v rozhraní Azure Sentinel a provádět běžné úlohy správy pro službu Threat Intelligence, jako je označení indikátoru a vytváření nových ukazatelů souvisejících s vyšetřováním zabezpečení.
Pojďme se podívat na dva nejběžnější úkoly a vytvořit nové indikátory hrozeb a indikátory označení pro snadné seskupování a odkazy.

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte do služby **Azure Sentinel** .

1. Vyberte **pracovní prostor** , do kterého jste naimportovali ukazatele hrozeb pomocí datového konektoru pro analýzu hrozeb.

1. V části Správa hrozeb v nabídce Sentinel Azure vyberte **Intelligence Threat** .

1. V horní nabídce stránky vyberte tlačítko **Přidat nový** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Přidat nový indikátor hrozby" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Zvolte typ indikátoru a potom vyplňte požadovaná pole označená červenou hvězdičkou (*) na **novém panelu indikátoru** .

1. Vyberte **Použít**. Indikátor se přidá do mřížky ukazatelů a pošle se taky do tabulky ThreatIntelligenceIndicator v **protokolech**.

Označování indikátorů hrozeb je jednoduchý způsob, jak je seskupit dohromady, aby bylo snazší je najít. Obvykle můžete použít značku na indikátory týkající se konkrétního incidentu nebo indikátory reprezentující hrozby z konkrétního známého objektu actor nebo známé kampaně se známým útokem. Můžete označit indikátory hrozeb jednotlivě nebo indikátory vícenásobného výběru a označit je všechny najednou. Níže je uveden příklad označování více indikátorů s ID incidentu. Vzhledem k tomu, že označení je zdarma, doporučuje se vytvořit standardní konvence pojmenování pro značky indikátoru hrozeb. U každého indikátoru můžete použít více značek.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Použít značky na indikátory hrozeb" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Analýza přináší indikátory hrozeb pro práci s detekcí potenciálních hrozeb.

Máte indikátory hrozeb, které jsou zadávány do Azure Sentinel; Viděli jste, jak je zobrazit a spravovat. Teď uvidíte, co můžou dělat za vás. Nejdůležitější případ použití pro indikátory hrozeb v řešeních SIEM, jako je Azure Sentinel, je pravidla Power Analytics.  Tato pravidla založené na ukazateli porovnávají nezpracované události ze zdrojů dat se svými indikátory hrozeb a zjišťují bezpečnostní hrozby ve vaší organizaci. V Azure Sentinel **Analytics** vytvoříte analytická pravidla, která se spouštějí na základě plánu a generují výstrahy zabezpečení. Tato pravidla jsou založená na dotazech spolu s konfiguracemi, které určují, jak často se má pravidlo spouštět, jaký druh výsledků dotazu by měl generovat výstrahy zabezpečení a všechny automatizované odezvy, které se aktivují, když se generují výstrahy.

I když můžete vždy vytvořit nová analytická pravidla od začátku, Azure Sentinel poskytuje sadu předdefinovaných šablon pravidla vytvořených inženýry zabezpečení společnosti Microsoft, které můžete použít tak, jak jsou, nebo upravit tak, aby vyhovovaly vašim potřebám. Můžete snadno identifikovat šablony pravidel, které používají indikátory hrozeb, protože jsou všechny s názvem od "**ČŘ map**...". Všechny tyto šablony pravidel fungují podobně, přičemž jediným rozdílem je, že se používá typ indikátorů hrozeb (doména, e-mail, hodnota hash souboru, IP adresa nebo adresa URL) a odpovídající typ události. Každá šablona obsahuje seznam požadovaných zdrojů dat potřebných k tomu, aby pravidlo fungovalo, takže se můžete podívat na první pohled v případě, že už máte naimportované nezbytné události v rámci Azure Sentinel.

Pojďme se podívat na jednu z těchto šablon pravidel a podíváme se, jak povolit a nakonfigurovat pravidlo pro generování výstrah zabezpečení pomocí indikátorů hrozeb, které jste naimportovali do Azure Sentinel. V tomto příkladu použijeme šablonu pravidla nazvanou **entity IP adresy ČŘ na AzureActivity**. Toto pravidlo bude odpovídat jakémukoli indikátoru hrozby typu IP adresa a všem událostem aktivity Azure. Pokud je nalezena shoda, bude vygenerována **Výstraha** a také odpovídající **incident** pro šetření v rámci vašeho týmu zabezpečení provozu. Toto pravidlo analýzy bude fungovat úspěšně jenom v případě, že jste povolili jeden nebo oba datové konektory pro **analýzu hrozeb** (pro import indikátorů hrozeb) a konektor dat **aktivit Azure** (pro import událostí na úrovni předplatného Azure).

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte do služby **Azure Sentinel** .

1. Pomocí konektoru dat o **aktivitách Azure** vyberte **pracovní prostor** , do kterého jste importovali indikátory hrozeb pomocí datových konektorů a **dat o** aktivitách Azure.

1. V části **Konfigurace** nabídky Sentinel Azure vyberte **Analytics** .

1. Vyberte kartu **šablony pravidel** a zobrazte seznam dostupných šablon pravidel Analytics.

1. Přejděte na pravidlo s názvem **entita mapování IP adresy ČŘ na AzureActivity** a ujistěte se, že jste provedli propojení všech požadovaných zdrojů dat, jak je uvedeno níže.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Požadované zdroje dat":::

1. Vyberte toto pravidlo a klikněte na tlačítko **vytvořit pravidlo** . Tím se otevře Průvodce pro konfiguraci pravidla. Dokončete nastavení a vyberte tlačítko **Další: nastavit >logiky pravidla** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Vytvořit analytické pravidlo":::

1. Část s logikou pravidla v průvodci obsahuje:
    - Dotaz, který se použije v pravidle.

    - Mapování entit, která dávají službě Azure Sentinel informace o tom, jak rozpoznat entity, jako jsou účty, IP adresy a adresy URL, aby **incidenty** a **šetření** porozuměly tomu, jak pracovat s daty v jakékoli výstrahy zabezpečení generované tímto pravidlem.

    - Plán, ve kterém má být toto pravidlo spuštěno.

    - Počet výsledků dotazu potřebných před vygenerováním výstrahy zabezpečení.

    Výchozí nastavení v šabloně:
    - Spustí se jednou za hodinu.

    - Porovnává všechny indikátory hrozeb IP adres z tabulky **ThreatIntelligenceIndicator** s jakoukoli IP adresou nalezenou během poslední hodiny událostí z tabulky **AzureActivity** .

    - Vygenerovat výstrahu zabezpečení, pokud jsou výsledky dotazu větší než nula, což znamená, že se najde nějaké shody.

Můžete ponechat výchozí nastavení nebo změnit některý z nich tak, aby splňoval vaše požadavky. Po dokončení vyberte tlačítko **Další: automatizovaná odpověď >.**

1. Tento krok průvodce vám umožní nakonfigurovat jakoukoli automatizaci, kterou byste chtěli aktivovat při vygenerování výstrahy zabezpečení z tohoto pravidla analýzy. Automatizace ve službě Azure Sentinel se provádí pomocí **playbooky**, která využívá Azure Logic Apps. Další informace najdete v tomto [kurzu: nastavení automatických odpovědí na hrozby v Azure Sentinel](./tutorial-respond-threats-playbook.md). V tomto příkladu vybereme tlačítko **Další: >zkontrolovat** , aby bylo možné pokračovat.

1. Tento poslední krok ověří nastavení v pravidle. Až budete připraveni pravidlo Povolit, vyberte tlačítko **vytvořit** a Vy jste hotovi.

Teď, když jste povolili pravidlo analýzy, najdete na kartě **aktivní pravidla** v části analýzy v části **Analýza** v tématu Azure Sentinel (Povolit) vaše povolené pravidlo. Aktivní pravidlo můžete v něm upravovat, povolit, zakázat, duplikovat nebo odstranit. Nové pravidlo se spustí hned po aktivaci a od potom se spustí podle definovaného plánu.

Vzhledem k výchozímu nastavení pokaždé, když se pravidlo spustí podle plánu, všechny nalezené výsledky budou generovat výstrahu zabezpečení. Výstrahy zabezpečení ve službě Azure Sentinel můžete zobrazit v části **protokoly** v tématu Azure Sentinel v tabulce **SecurityAlert** ve skupině **Sentinel Azure** .

Výstrahy generované z pravidel analýz ve službě Azure Sentinel také generují incidenty zabezpečení, které najdete v **incidentech** v části **Správa hrozeb** v nabídce Sentinel Azure. Incidenty jsou informace o tom, jaké týmy vašich operací zabezpečení se budou rozhodovat a prozkoumat, aby určily příslušné akce reakce. Podrobné informace najdete v tomto [kurzu: Prozkoumejte incidenty pomocí služby Azure Sentinel](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Pracovní sešity poskytují přehled o vaší analýze hrozeb.

Nakonec můžete použít pracovní sešit Azure Sentinel k vizualizaci klíčových informací o vaší analýze hrozeb v Azure Sentinel a sešity můžete snadno přizpůsobit podle potřeb vaší firmy.
Podívejme se, jak najít sešit s přehledem hrozeb, který je k dispozici v Azure Sentinel, a my také ukážeme, jak v sešitu provádět úpravy a přizpůsobit ho.

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte do služby **Azure Sentinel** .

1. Vyberte **pracovní prostor** , do kterého jste naimportovali ukazatele hrozeb pomocí datového konektoru pro analýzu hrozeb.

1. V části **Správa hrozeb** v nabídce Sentinel Azure vyberte **sešity** .

1. Přejděte do sešitu s názvem **Threat Intelligence** a ověřte, že máte data v tabulce **ThreatIntelligenceIndicator** , jak je znázorněno níže.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Ověření dat":::
 
1. Vyberte tlačítko **Uložit** a zvolte umístění Azure pro uložení sešitu. Tento krok je nutný, pokud budete sešit upravovat jakýmkoli způsobem a uložíte změny.

1. Nyní vyberte tlačítko **Zobrazit uložený sešit** a otevřete sešit pro zobrazení a úpravy.

1. Nyní byste měli vidět výchozí grafy poskytované šablonou. Teď provedeme nějaké změny v jednom z grafů. V horní části stránky vyberte tlačítko **Upravit** a zadejte pro sešit režim úprav.

1. Pojďme přidat nový graf indikátorů hrozeb podle typu hrozby. Posuňte se do dolní části stránky a vyberte Přidat dotaz.

1. Do textového pole **dotaz do protokolu log Analyticsho pracovního prostoru** přidejte následující text:
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. V rozevíracím seznamu **vizualizace** vyberte **pruhový graf**.

1. Vyberte tlačítko **Hotovo pro úpravy** . Vytvořili jste nový graf pro sešit.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Pruhový graf":::

Pracovní sešity poskytují výkonné interaktivní řídicí panely, které vám poskytnou přehled o všech aspektech Azure Sentinel. Existuje celá řada, kterou můžete dělat se sešity, a zatímco poskytnuté šablony jsou skvělým výchozím bodem, pravděpodobně budete chtít podrobně a přizpůsobit tyto šablony, nebo můžete vytvořit nové řídicí panely, které kombinují mnoho různých zdrojů dat, abyste data mohli vizualizovat jedinečnými způsoby. Vzhledem k tomu, že jsou sešity Sentinel v Azure založené na Azure Monitor sešitech, už je dostupná rozsáhlá dokumentace a mnoho dalších šablon. Dobrým místem, kde začít, je tento článek o [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../azure-monitor/visualize/workbooks-overview.md). 

K dispozici je také bohatá komunita [Azure Monitorch sešitů na GitHubu](https://github.com/microsoft/Application-Insights-Workbooks) , kde si můžete stáhnout další šablony a přispět k vlastním šablonám.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli o možnostech analýzy hrozeb v Azure Sentinel a novém okně Analýza hrozeb. Praktické pokyny k používání možností analýzy hrozeb v rámci služby Azure Sentinel najdete v následujících článcích:

- [Připojte data analýzy hrozeb](./connect-threat-intelligence.md) ke službě Azure Sentinel.
- Vytvářejte [předdefinované](./tutorial-detect-threats-built-in.md) nebo [vlastní](./tutorial-detect-threats-custom.md) výstrahy a [Prozkoumejte](./tutorial-investigate-cases.md) incidenty v Azure Sentinel.