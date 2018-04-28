---
title: Začínáme s Microsoft Power BI Embedded | Dokumentace Microsoftu
description: Power BI Embedded do aplikace business intelligence
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: maghan
ms.openlocfilehash: 7b604f9a26fc4c9a2c76a28ca01d066fe1640718
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Začínáme s Microsoft Power BI Embedded

**Power BI Embedded** nabízí nezávislým výrobcům softwaru (ISV) a vývojářům způsob, jak do svých aplikací rychle přidat ohromující vizuály, sestavy a řídicí panely prostřednictvím modelu založeného na kapacitě a měřeného po hodinách.

![Diagram toku vkládání](media/get-started/introduction.png)

Power BI Embedded přináší výhody nezávislým výrobcům softwaru, jejich vývojářům i zákazníkům. Například nezávislý výrobce softwaru může začít vytvářet vizuály zdarma pomocí Power BI Desktop. Nezávislí výrobci softwaru můžou minimalizací úsilí souvisejícího s vývojem vizuálních analýz dosáhnout rychlejšího uvedení na trh a odlišit se tak od konkurence diferencovanými prostředími pro práci s daty. Nezávislí výrobci softwaru se také můžou rozhodnout účtovat více za přidanou hodnotu vytvořenou pomocí vložených analýz.

Vývojáři můžou svůj čas soustředit na vytváření základních dovedností aplikace a nemusí trávit čas vývojem vizuálů a analýz. Vývojáři můžou rychle plnit požadavky zákazníků na sestavy a řídicí panely a jednoduše je vkládat pomocí plně zdokumentovaných rozhraní API a sad SDK. Nezávislí výrobci softwaru můžou nakonec povolením prozkoumávání dat, která lze snadno procházet, ve svých aplikacích umožnit zákazníkům s jakýmkoli zařízením provádět rychlé rozhodování na základě dat s ohledem na kontext a bez obav.

## <a name="register-an-application-within-azure-active-directory"></a>Registrace aplikace v Azure Active Directory

Registrovaná aplikace v Azure Active Directory (AAD) je potřeba k vložení do vlastní aplikace. Registrovaná aplikace vyžaduje, aby váš tenant byl tenantem Power BI. Tenant Power BI znamená, že se alespoň jeden uživatel v organizaci zaregistroval k Power BI. Registrace uživatele k Power BI umožní zobrazení rozhraní API Power BI v registrované aplikaci.

Další informace o postupu registrace aplikace v AAD najdete v tématu [Registrace aplikace Azure AD pro vkládání obsahu Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

## <a name="embed-content-in-your-application"></a>Vložení obsahu do aplikace

Po registraci aplikace v AAD můžete ve své aplikaci vkládat obsah Power BI. Obsah můžete vložit pomocí rozhraní REST API společně s rozhraními JavaScript API.

Nabízíme ukázky, které vám pomůžou začít. Názorného průvodce ukázkou najdete v tématu [Integrace řídicího panelu, dlaždice nebo sestavy do aplikace](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

## <a name="get-capacity-and-move-to-production"></a>Získání kapacity a přesun do produkčního prostředí

Vytvořte v rámci Microsoft Azure kapacitu Power BI Embedded pro přesun aplikace do produkčního prostředí. Informace o postupu vytvoření kapacity najdete v tématu [Vytvoření kapacity Power BI Embedded na webu Azure Portal](create-capacity.md).

> [!IMPORTANT]
> Vzhledem k tomu, že vložené tokeny jsou určené jenom pro účely vývoje a testování, omezili jsme počet vložených tokenů, které může vytvořit hlavní účet Power BI. Pro scénáře vkládání v produkčním prostředí je [potřeba zakoupit kapacitu](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical). Pokud je kapacita zakoupena, není generování vložených tokenů nijak omezené.

Kapacitu můžete spravovat na portálu pro správu Power BI. Přiřaďte pověřující osobu pracovního prostoru, která vám pomůže s pracovními prostory aplikace. Další informace najdete v tématu [Správa kapacit v Power BI Premium a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

## <a name="next-steps"></a>Další kroky

Pokud jste připraveni vytvořit kapacitu Power BI Embedded, přečtěte si téma [Vytvoření kapacity Power BI Embedded na webu Azure Portal](create-capacity.md).

Pokud hledáte názorného průvodce ukázkou, přečtěte si téma [Integrace řídicího panelu, dlaždice nebo sestavy do aplikace](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)
