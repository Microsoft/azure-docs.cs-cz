---
title: Jak migrovat obsah kolekce pracovních prostorů Power BI pro Power BI Embedded | Microsoft Docs
description: Zjistěte, jak migrovat z kolekce pracovních prostorů Power BI pro Power BI Embedded a využívání přejde pro vložení do aplikace.
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
ms.openlocfilehash: d138b0c26ffc0a44947f79811fd586dda7df4509
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Jak migrovat obsah kolekce pracovních prostorů Power BI pro Power BI Embedded

Zjistěte, jak migrovat z kolekce pracovních prostorů Power BI pro Power BI Embedded. Tento článek obsahuje pokyny k migraci z kolekce pracovních prostorů Azure Power BI pro Power BI Embedded. Podíváme se také na co očekávat v změny aplikace.

Kolekce pracovních prostorů Power BI prostředků bude nadále k dispozici po omezenou dobu, po vydání obecné dostupnosti Power BI Premium. Zákazníci v rámci smlouvy Enterprise mít přístup k jejich existující kolekce prostoru prostřednictvím vypršení platnosti jejich existující smlouvy. Zákazníkům, kteří získaná kolekce pracovních prostorů Power BI přímo nebo poskytovatel CSP kanály získejte přístup jeden rok z obecné dostupnosti z Power BI Premium.

> [!IMPORTANT]
> Při migraci přijímá závislost na službě Power BI, není závislost na Power BI pro uživatele vaší aplikace při používání **vložení token**. Není nutné se přihlásit k Power BI zobrazíte vložený obsah ve vaší aplikaci. Můžete to použít vložení přístup vložení Power BI pro vaše zákazníky.

![Power BI Embedded toku](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Příprava na migraci

Existuje několik věcí, které je třeba provést přípravu migrace ze služby Power BI prostoru kolekce přes do Power BI Embedded. Je nutné klienta k dispozici, společně s uživatele, který má licenci Power BI Pro.

1. Zkontrolujte, zda že máte přístup klienta služby Azure Active Directory (Azure AD).

    Které klientovi k používání?

    * Vaše stávající firemní Power BI klienta použít?
    * Použít samostatné klienta pro vaši aplikaci?
    * Samostatné klienta použít pro každého zákazníka?

    Pokud se rozhodnete vytvořit nového klienta pro aplikaci nebo každého zákazníka, najdete v jednom z následujících akcí:

    * [Vytvoření klienta Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Postup získání klienta Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Vytvořte uživatele v rámci tohoto nového klienta, který funguje jako "hlavní" účet aplikace. Že účet, musí se přihlásit k Power BI a musí mít licenci Power BI Pro přiřazen.

## <a name="accounts-within-azure-ad"></a>Účty v Azure AD

V rámci vašeho klienta je nutné následující účty.

> [!NOTE]
> Tyto účty musí být Power BI Pro licence. Chcete-li používat aplikaci pracovních prostorů.

1. Uživatel správce klienta.

    Doporučuje se, že vnoření pracovní prostor aplikace má správy klienta, který je uveden jako člena.

2. Účty pro analytici, které vytvoříte obsah.

    Tito uživatelé by měla být přiřazená pracovních prostorů aplikace podle potřeby.

3. Aplikace *hlavní* uživatelský účet nebo účet služby.

    Back-end aplikace ukládá přihlašovací údaje pro tento účet. Použít *hlavní* účet pro získání tokenu Azure AD pro použití s Power BI REST API. Tento účet se používá k vygenerování tokenu vložení pro aplikace. *Hlavní* účet musí být správce pracovních prostorů aplikace vytvořené pro vložení.

    **Tento účet je právě běžného uživatelského účtu ve vaší organizaci, který se používá pro účely vložení.**

## <a name="app-registration-and-permissions"></a>Registrace aplikací a oprávnění

Chcete-li volání rozhraní REST API, zaregistrujte aplikaci s Azure AD. Přidání konfigurace se použije v rámci portálu Microsoft Azure kromě registrační stránce aplikace Power BI. Další informace najdete v tématu [zaregistrovat aplikaci Azure AD pro vložení obsah Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Doporučuje se zaregistrovat aplikaci pomocí aplikace **hlavní** účtu.

## <a name="create-app-workspaces-required"></a>Vytváření pracovních prostorů aplikace (povinné)

Pokud vaše aplikace je obsluhuje více zákazníků, můžete využít výhod pracovních prostorů aplikace k zajištění lepší izolace. Řídicí panely a sestavy by izolované mezi vašich zákazníků. Pak může použít účet Power BI na pracovní prostor aplikace další izolovat aplikace prostředí mezi vašich zákazníků, ale jeden účet můžete použít jenom pro jednoduchost.

> [!IMPORTANT]
> Nemůžete použít osobního pracovního prostoru ("Pracovního prostoru") využívat výhod vložení zákazníkům.

Je nutné, aby bylo možné vytvořit pracovní prostor služby aplikace v rámci Power BI, který má licenci Pro uživatele. Power BI uživatel, který vytváří pracovní prostor aplikace je správce tohoto pracovního prostoru ve výchozím nastavení. **Aplikace *hlavní* účet musí být správce pracovního prostoru.**

## <a name="content-migration"></a>Migrace obsahu

Migrace svůj obsah z kolekce prostoru na Power BI Embedded lze provést v paralelní do aktuální řešení a nevyžaduje žádné výpadky.

A **nástroj pro migraci** je k dispozici pro použití za účelem pomoci s kopírování obsahu z kolekce pracovních prostorů Power BI pro Power BI Embedded. Zejména v případě, že máte spoustu sestav. Další informace najdete v tématu [Power BI Embedded nástroj pro migraci](migrate-tool.md).

Obsahu migrace závisí hlavně na dvě rozhraní API.

1. Stáhnout soubor PBIX – toto rozhraní API můžete stáhnout soubor PBIX soubory, které byly odeslány na Power BI po říjnu 2016.
2. Importovat soubor PBIX – toto rozhraní API ukládání všech soubor PBIX do Power BI.

Některé související fragmenty kódu, najdete v části [Code fragmenty pro migraci obsahu z Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Typy sestav

Existuje několik typů sestav, každou nutnosti toku různé migrace.

#### <a name="cached-dataset-and-report"></a>Datové sady v mezipaměti a sestavy

V mezipaměti datové sady odkazují na soubor PBIX soubory, které měl importovali data a živé připojení nebo připojení DirectQuery.

**Tok**

1. Stáhněte si soubor PBIX rozhraní API volejte z pracovního prostoru kolekce pracovních prostorů Power BI.
2. Uložte soubor PBIX.
3. Importovat soubor PBIX volání pro Power BI Embedded pracovním prostoru.

#### <a name="directquery-dataset-and-report"></a>DirectQuery datovou sadu a sestavu

**Tok**

1. Volání GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources a uložte připojovací řetězec přijata.
2. Stáhněte si soubor PBIX rozhraní API volejte z pracovního prostoru kolekce pracovních prostorů Power BI.
3. Uložte soubor PBIX.
4. Importovat soubor PBIX volání pro Power BI Embedded pracovním prostoru.
5. Aktualizujte připojovací řetězec tak, že volání - POST  https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections
6. Získání ID GW ID a zdroj dat voláním – získání https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources
7. Aktualizujte přihlašovací údaje uživatele při volání - oprava https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}

#### <a name="old-dataset-and-reports"></a>Původní datové sady a sestavy

Sestavy než října 2016 nepodporují funkci stáhnout soubor PBIX nahrát.

**Tok**

1. Získáte soubor PBIX z vývojové prostředí (vaše interní zdrojového kódu).
2. Importovat soubor PBIX volání pro Power BI Embedded pracovním prostoru.

#### <a name="push-dataset-and-report"></a>Push datovou sadu a sestavu

Stáhněte si soubor PBIX nepodporuje *Push rozhraní API* datové sady. Doručte datovou sadu rozhraní API, které nelze přenést data z Power BI prostoru kolekcí do Power BI Embedded.

**Tok**

1. Volání API "Vytvořte datovou sadu" datové sadě Json pro vytvoření datové sady pro Power BI Embedded pracovním prostoru.
2. Znovu sestavte sestavy pro vytvořenou datovou sadu *.

Je možné, některé řešení s migrací nabízeného oznámení pomocí rozhraní api sestavy z kolekce pracovních prostorů Power BI pro Power BI Embedded tak, že zkusíte následující:

1. Některé fiktivní soubor PBIX se nahrávají do pracovního prostoru kolekce pracovních prostorů Power BI.
2. Klonování nabízeného oznámení api sestavy a navázat jej fiktivní soubor PBIX z kroku 1.
3. Stáhněte si nabízené sestava rozhraní API s fiktivní soubor PBIX.
4. Nahrajte soubor PBIX fiktivní do pracovního prostoru Power BI Embedded.
5. Vytvoření nabízené datové sady v Power BI Embedded pracovním prostoru.
6. Rebind sestavu tak, aby nabízel datovou sadu rozhraní api.

## <a name="create-and-upload-new-reports"></a>Vytvoření a odeslání nové sestavy

Kromě obsah, který jste migrovali z kolekce pracovních prostorů Power BI můžete vytvořit sestavy a datové sady pomocí Power BI Desktop a potom publikovat tyto sestavy k pracovnímu prostoru aplikace. Koncový uživatel publikování sestavy musí být licenci Power BI Pro Chcete-li publikovat k pracovnímu prostoru aplikace.

## <a name="rebuild-your-application"></a>Sestavení aplikace

1. Upravte aplikace pomocí rozhraní REST API Power BI a umístění sestavy uvnitř powerbi.com.

2. Znovu sestavte pomocí ověřovacího/AuthZ ověřování *hlavní* účet pro vaši aplikaci. Můžete využít výhod používání [vložení tokenu](https://msdn.microsoft.com/library/mt784614.aspx) umožňující tohoto uživatele tak, aby fungoval jménem jiných uživatelů.

3. Vložení sestavy z Power BI Embedded do vaší aplikace. Další informace najdete v tématu [postup vložení váš Power BI řídicí panely, sestavy a dlaždice](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Mapování vaši uživatelé uživateli Power BI

V rámci vaší aplikace mapování uživatelů, které můžete spravovat v aplikaci *hlavní* přihlašovací údaje Power BI pro účely vaší aplikace. Přihlašovací údaje pro tento Power BI *hlavní* účtu jsou uloženy v rámci vaší aplikace a použije k vytvoření tokenů pro vložení.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Co dělat, když je systém připraven pro produkční prostředí

Pokud jste připravení přejít do produkčního prostředí, je třeba provést následující akce:

- Pokud používáte samostatnou klienta pro vývoj, budete muset Ujistěte se, že vaše pracovní prostory pro aplikace, společně s řídicí panely a sestavy, jsou k dispozici v provozním prostředí. Zajistěte, aby vytvoření aplikace ve službě Azure AD pro vašeho klienta produkční a přiřadit oprávnění správné aplikace, které je uvedené v kroku 1.

- Kupte si kapacitu, která vyhovuje vašim potřebám. Můžete použít [plánování dokument White Paper vložených analýzy kapacity](https://aka.ms/pbiewhitepaper) pro lepší porozumění tomu, co budete potřebovat. Jakmile budete připraveni k nákupu, si můžete zakoupit Power BI Embedded prostředků v rámci portálu Azure.

- Upravit pracovní prostor aplikace a přiřaďte ho ke kapacitu v části Upřesnit.

    ![Pracovní prostor aplikace přiřadit kapacity v rámci powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Nasazení aktualizované aplikace do produkčního prostředí a začít vložení sestavy Power BI Embedded.

## <a name="after-migration"></a>Po dokončení migrace

Některé čištění je potřeba v rámci kolekce pracovních prostorů Power BI.

- Odeberte všechny pracovní prostory z nasazené řešení v rámci Azure služby Power BI prostoru kolekcí.
- Odstraňte všechny kolekce pracovní prostor, který neexistuje v rámci Azure.

## <a name="next-steps"></a>Další postup

Blahopřejeme. Aplikace je nyní migrovány do Power BI Embedded. Informace o tom, jak vaše řídicí panely Power BI, sestavy a datové sady pro vložení najdete v tématu [postup vložení váš Power BI řídicí panely, sestavy a dlaždice](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Chcete se ještě na něco zeptat? [Zkuste požádat komunitě Power BI](http://community.powerbi.com/)