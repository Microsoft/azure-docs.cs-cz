---
title: Jak migrovat obsah kolekce pracovních prostorů Power BI do Power BI Embedded | Dokumentace Microsoftu
description: Zjistěte, jak migrovat z kolekce pracovních prostorů Power BI do Power BI Embedded a využijte pokročilé funkce pro vkládání do aplikací.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: de20d532112ca73f34f7cb603d043579c28179d6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071228"
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Jak migrovat obsah kolekce pracovních prostorů Power BI do Power BI Embedded

Zjistěte, jak migrovat z kolekce pracovních prostorů Power BI do Power BI Embedded. Tento článek obsahuje pokyny pro migraci z kolekce pracovních prostorů Azure Power BI do Power BI Embedded. Budeme se také podívat na co můžete očekávat změny aplikace.

Prostředek kolekce pracovních prostorů Power BI i nadále k dispozici po omezenou dobu, po vydání obecnou dostupnost služby Power BI Premium. Zákazníci se smlouvou Enterprise mají přístup k jejich stávající kolekce pracovních prostorů až do vypršení platnosti jejich stávajících smluv. Zákazníci, kteří si pořídili kolekce pracovních prostorů Power BI prostřednictvím kanálů Direct nebo CSP využívat přístup po dobu jednoho roku od obecné dostupnosti Power BI Premium.

> [!IMPORTANT]
> Během migrace je závislá na službě Power BI, není závislost na službě Power BI pro uživatelům vaší aplikace při používání **token pro vložení**. Není nutné se zaregistrovat do služby Power BI a zobrazit vložený obsah ve vaší aplikaci. To může být použito vkládání přístup vložení Power BI pro vaše zákazníky.

![Tok Power BI Embedded](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Příprava na migraci

Existuje několik věcí, které musíte udělat při přípravě na migraci z kolekce pracovních prostorů Power BI service přes do Power BI Embedded. Budete potřebovat tenanta k dispozici, spolu se jako uživatel, který má licenci Power BI Pro.

1. Ujistěte se, že máte přístup do tenanta služby Azure Active Directory (Azure AD).

    Kterého tenanta použít?

    * Použijte váš stávající podnikové tenant Power BI?
    * Používat samostatného tenanta pro vaši aplikaci?
    * Používat samostatného tenanta pro každého zákazníka?

    Pokud se rozhodnete vytvořit nového tenanta pro vaši aplikaci, nebo pro každého zákazníka, najdete v jednom z následujících akcí:

    * [Vytvoření tenanta Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Získání tenanta služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Vytvořte uživatele v rámci tohoto nového tenanta, který funguje jako "hlavní" účet vaší aplikace. Že účet potřebuje k registraci pro Power BI a musí mít přiřazenou licenci Power BI Pro.

## <a name="accounts-within-azure-ad"></a>Účty v Azure AD

Tyto účty musí existovat v rámci vašeho tenanta.

> [!NOTE]
> Tyto účty musí mít licence Power BI Pro, chcete-li použít pracovní prostory aplikací.

1. Správce tenanta.

    Doporučuje se, že vkládání pracovního prostoru aplikace měl správce tenanta uvedená jako člena.

2. Účty pro analytiky, kteří vytvářet obsah.

    Tito uživatelé měla být přiřazena k pracovním prostorům aplikace podle potřeby.

3. Aplikace *hlavní* uživatelský účet nebo účet služby.

    Back-endu aplikace ukládá přihlašovací údaje pro tento účet. Použít *hlavní* účet pro získání tokenu Azure AD pomocí rozhraní REST API Power BI. Tento účet slouží ke generování token pro vložení pro aplikaci. *Hlavní* účet musí být správcem pracovních prostorů aplikace vytvářených pro účely vkládání.

    **Tento účet je právě běžný uživatelský účet ve vaší organizaci, který se používá pro účely vkládání.**

## <a name="app-registration-and-permissions"></a>Registrace aplikace a oprávnění

Pro volání rozhraní REST API registrace aplikace v Azure AD. Přidání konfigurace se použije v rámci portálu Microsoft Azure kromě registrační stránku aplikace Power BI. Další informace najdete v tématu [registrace aplikace Azure AD pro vložení obsahu Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Doporučuje se registrace aplikace pomocí aplikace **hlavní** účtu.

## <a name="create-app-workspaces-required"></a>Vytvoření pracovních prostorů aplikace (povinné)

Pokud vaše aplikace obsluhuje více zákazníků, můžete využívat pracovní prostory aplikací a zajištění lepší izolace. Řídicí panely a sestavy pak budou izolované mezi zákazníky. Můžete pak použít účet Power BI za pracovní prostor aplikace dále izolovat aplikace prostředí mezi zákazníky, ale jeden účet můžete použít jenom pro jednoduchost.

> [!IMPORTANT]
> Nelze použít osobní pracovní prostor ("Můj pracovní prostor") pro vkládání pro vaše zákazníky.

Je nutné jako uživatel, který má licenci k vytvoření pracovního prostoru aplikace Power BI. Uživatel Power BI, který vytvoří pracovní prostor aplikace je správcem tohoto pracovního prostoru ve výchozím nastavení. **Aplikace *hlavní* účet musí být správcem pracovního prostoru.**

## <a name="content-migration"></a>Migrace obsahu

Migraci obsahu z kolekce pracovních prostorů do Power BI Embedded můžete provést paralelně s aktuálním řešením a nevyžaduje žádné prostoje.

A **nástroj pro migraci** je pro vás může pomoct s kopírováním obsahu z kolekce pracovních prostorů Power BI do Power BI Embedded k dispozici. Zejména v případě, že máte spoustu sestav. Další informace najdete v tématu [nástroj pro migraci Power BI Embedded](migrate-tool.md).

Migraci obsahu se využívají hlavně dvě rozhraní API.

1. Download PBIX: Toto rozhraní API může stáhnout soubory PBIX, které byly do Power BI nahráli po říjnu 2016.
2. Import PBIX: Toto rozhraní API nahrát libovolný soubor PBIX do Power BI.

Některé související fragmenty kódu, najdete v článku [fragmenty kódu pro migraci obsahu z Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Typy sestav

Existuje několik typů sestav, každý jiný postup migrace vyžaduje.

#### <a name="cached-dataset-and-report"></a>Datové sady v mezipaměti a sestavy

V mezipaměti datové sady odkazují na soubory PBIX obsahující importovaná data a nevyužívají živé připojení nebo připojení DirectQuery.

**Tok**

1. Volání rozhraní API Download PBIX z pracovního prostoru kolekce pracovních prostorů Power BI.
2. Uložte soubor PBIX.
3. Volejte rozhraní Import PBIX pro váš pracovní prostor Power BI Embedded.

#### <a name="directquery-dataset-and-report"></a>Datové sady DirectQuery a sestavy

**Tok**

1. Volejte GET `https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources` a uložte přijatý připojovací řetězec.
2. Volání rozhraní API Download PBIX z pracovního prostoru kolekce pracovních prostorů Power BI.
3. Uložte soubor PBIX.
4. Volejte rozhraní Import PBIX pro váš pracovní prostor Power BI Embedded.
5. Aktualizujte připojovací řetězec voláním – POST  `https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections`
6. Získejte ID GW ID a zdroj dat voláním – GET `https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources`
7. Aktualizujte přihlašovací údaje uživatele voláním – PATCH `https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}`

#### <a name="old-dataset-and-reports"></a>Původní datové sady a sestavy

Sestavy nahraje před říjnem 2016 nepodporují funkci stáhnout soubor PBIX. 

**Tok**

1. Získejte soubor PBIX ze svého vývojového prostředí (interního zdroje řízení).
2. Volejte rozhraní Import PBIX pro váš pracovní prostor Power BI Embedded.

#### <a name="push-dataset-and-report"></a>Datové sady nabízených oznámení a sestavy

Stáhněte si soubor PBIX nepodporuje *rozhraní Push API* datové sady. Zápis dat datové sady rozhraní API nejde přenést z kolekce pracovních prostorů Power BI do Power BI Embedded.

**Tok**

1. Volání "Vytvoření datové sady" rozhraní API s datovou sadou Json pro vytvoření datové sady pro váš pracovní prostor Power BI Embedded.
2. Sestavte znovu sestavu pro vytvořenou datovou sadu *.

Je možné pomocí některé řešení migrace nasdílení změn rozhraní api sestavu z kolekce pracovních prostorů Power BI do Power BI Embedded vyzkoušejte následující:

1. Nahrává se nějaký zástupný soubor PBIX do pracovního prostoru kolekce pracovních prostorů Power BI.
2. Klonovat nasdílení změn rozhraní api sestavy a připnout ho ke zástupný soubor PBIX z kroku 1.
3. Stáhněte si push API sestavy s použitím zástupný soubor PBIX.
4. Nahrajte zástupný soubor PBIX do pracovního prostoru Power BI Embedded.
5. Vytvoření datové sady nabízených oznámení v pracovním prostoru Power BI Embedded.
6. Obnovení vazby sestav k rozhraní api datové sady nabízených oznámení.

## <a name="create-and-upload-new-reports"></a>Vytváření a nahrávání nových sestav

Kromě obsahu migrovaného z kolekce pracovních prostorů Power BI můžete vytvořit sestavy a datové sady pomocí Power BI Desktopu a tyto sestavy publikovat do pracovního prostoru aplikace. Koncový uživatel, který bude sestavy publikovat, musí mít licenci Power BI Pro, chcete-li publikovat do pracovního prostoru aplikace.

## <a name="rebuild-your-application"></a>Opětovné sestavení aplikace

1. Upravte svou aplikaci pomocí rozhraní REST API Power BI a umístění sestavy na webu powerbi.com.

2. Znovu sestavte ověřování AuthN/AuthZ pomocí *hlavní* účtu pro vaši aplikaci. Můžete využít výhod používání [token pro vložení](https://msdn.microsoft.com/library/mt784614.aspx) tento uživatel jednat jménem jiných uživatelů.

3. Vložte své sestavy z Power BI Embedded do vaší aplikace. Další informace najdete v tématu [jak vložit vaše Power BI řídicí panely, sestavy a dlaždice](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Namapování vašich uživatelů na uživatele Power BI

V rámci vaší aplikace, můžete mapování uživatelů, která spravujete v rámci aplikace *hlavní* přihlašovací údaje Power BI pro účely vaší aplikace. Přihlašovací údaje pro tento Power BI *hlavní* účet jsou uložené v rámci vaší aplikace a použít k vytváření tokenů pro vložení.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Co dělat, když jste připraveni pro produkční prostředí

Až budete připravení přejít do produkčního prostředí, musíte provést následující kroky:

- Pokud používáte samostatného tenanta pro vývoj, musíte zajistit, aby že vaše pracovní prostory aplikací společně s řídicí panely a sestavy, jsou dostupné v produkčním prostředí. Ujistěte se, že aplikace vytvořené ve službě Azure AD vašeho produkčního tenanta a přiřadit správná oprávnění pro aplikaci, jak je uvedeno v kroku 1.

- Zakupte si kapacitu, která nejlépe vyhovuje vašim potřebám. Můžete použít [dokument White Paper o plánování kapacity u vkládaného analytického](https://aka.ms/pbiewhitepaper) pro lepší porozumění tomu, co budete potřebovat. Až budete připravení k nákupu, si můžete koupit prostředek Power BI Embedded na webu Azure portal.

- Upravit pracovní prostor aplikace a přiřaďte ho ke kapacitě v části Upřesnit.

    ![Přiřadit pracovní prostor aplikace ke kapacitě v rámci powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Nasaďte aktualizovanou aplikaci do produkčního prostředí a začněte vkládat sestavy z Power BI Embedded.

## <a name="after-migration"></a>Po dokončení migrace

V rámci kolekce pracovních prostorů Power BI je potřeba trochu pořádek.

- Odeberte všechny pracovní prostory z nasazeného řešení v rámci služby Azure z kolekce pracovních prostorů Power BI.
- Odstraňte všechny kolekce pracovního prostorů, které existovaly v Azure.

## <a name="next-steps"></a>Další postup

Blahopřejeme. Vaše aplikace se migroval do Power BI Embedded. Informace o tom, jak vkládat řídicí panely Power BI, sestavy a datové sady, naleznete v tématu [jak vložit vaše Power BI řídicí panely, sestavy a dlaždice](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Chcete se ještě na něco zeptat? [Zkuste se zeptat komunity Power BI](http://community.powerbi.com/)