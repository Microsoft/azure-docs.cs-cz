---
title: Přehled konektorů aplikace logiky | Microsoft Docs
description: Přehled konektory, které lze použít v aplikaci logiky
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
ms.openlocfilehash: 6f7d8f99bfa09847c01831a06efa8b94c1c0a89a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27961648"
---
# <a name="using-connectors-in-a-logic-app"></a>Použití konektorů v aplikaci logiky
Konektorů poskytuje rychlý přístup k události, datům a akcím napříč služeb, protokoly a platformy.  Úplný seznam konektorů, které podporuje aplikace logiky můžete [je zde uveden](apis-list.md).  Konektory lze použít jako aktivační události nebo akce v aplikaci logiky a může vyžadovat nakonfigurované *připojení* použít (Příklad: autorizace účtu sítě Twitter. přístup k nebo post vaším jménem).

## <a name="basics"></a>Základy
Konektory jsou hostované služby v rámci aplikace logiky k integraci s jinými službami jako Dynamics Azure, služby Salesforce, dostanete [a další](apis-list.md).  Jsou nasazen a spravován společností Microsoft, takže můžete vytvářet pracovní postupy integrace s škálování, propustnosti a zabezpečení postaráno.  Můžete přidat konektor do aplikace logiky vyhledávání a výběrem akce konektoru nebo aktivovat pod **zobrazit Microsoft spravované rozhraní API**.

![Nabídka Akce pro výběr aktivační události][1]

Jeho sadu vlastností konfigurace bude mít každý konektor akce nebo aktivační událost.  Klikněte na tlačítka informace o další informace o akci, nebo reference jeho dokumentace [Další](apis-list.md).

Pokud chcete integrovat službu nebo rozhraní API, které není ještě konektor, můžete také rozšířit aplikace logiky prostřednictvím [vlastní konektor](../logic-apps/logic-apps-create-api-app.md) nebo stačí zavolat přímo ke službě přes protokol například HTTP.

## <a name="triggers"></a>Triggery
Některé konektory obsahovat aktivační událost, to znamená událost z tohoto konektoru se aktivovat aplikaci logiky a předejte všechna data v rámci aktivační události.  Aktivační událost je vždy první krok v aplikaci logiky.  Oblíbených aktivační procedury patří operace, jako je:

* Opakování - spouštět každou hodinu
* Kdy je přijal požadavek HTTP
* Když je položka přidána do fronty
* Při doručení e-mailu

Některé aktivačních událostí bude platit pro rychlé, které události se nakonfigurují oznámení do aplikace logiky a ostatní nutné intervalu opakování nakonfigurované na tom, jak často bude aplikace logiky zkontrolujte službu pro událost (až každých 15 sekund).  

Po přijetí události bude platit spusťte aplikaci logiky a spuštění akce v pracovním postupu.  Také bude mít přístup k žádným datům z aktivační událost v celém pracovním postupu (například aktivační událost 'na nový tweet, bude předán tweet spustit).

## <a name="actions"></a>Akce
Většina konektory mít jednu nebo více akcí, které lze provést v rámci pracovního postupu.  Akce jsou všechny kroky, které dojít po spuštění byla aktivována z aktivační události.  Chcete-li přidat klikněte na tlačítko akce **nový krok** tlačítko a vyhledejte konektor, který chcete použít.  Po začlenění (a po dokončení konfigurace žádné [připojení](#connections) , může být nutný) se zobrazí karta Akce můžete nakonfigurovat.  Můžete vybrat data z předchozích kroků kliknutím na jakékoli tokenů pro výstupy nebo zadejte jakoukoli jinou konfiguraci podle potřeby.

![Konfigurace konektoru akce][2]

## <a name="connections"></a>Připojení
Většina konektory musíte nakonfigurovat *připojení* než budete moci použít tento konektor.  A *připojení* je přihlášení nebo připojení konfigurace, která se potřebné pro přístup k konektor.  Konektory, které používají OAuth, vytvořit připojení znamená přihlášení k službě (např. Office 365, Salesforce nebo Githubu), kde můžete šifrovat a bezpečně uložené v úložišti Azure tajný přístupový token.  Ostatní konektory (např. FTP a SQL) vyžadují připojení, který obsahuje konfiguraci jako adresu serveru, uživatelské jméno a heslo.  Tyto podrobnosti konfigurace připojení jsou také šifrovaný a bezpečně uložit.  Budou moct tak dlouho, dokud služba umožňuje přístup ke službě pro připojení.  Pro Azure Active Directory OAuth připojení (např. Office 365 a Dynamics) budeme pokračovat v aktualizaci přístupový token bez omezení.  Další služby mohou uvedeny omezení na jak dlouho token můžete použít bez se právě aktualizuje.  Obecně určité akce, jako je změna hesla zrušíte platnost všech přístupových tokenů.  

Připojení lze zobrazit a spravovat v Azure kliknutím **Procházet** a výběrem **rozhraní API připojení**.  Z rozhraní API připojení prostředku můžete zobrazit, upravit, aktualizovat nebo znovu autorizovat, všechna připojení, které jste vytvořili.

## <a name="next-steps"></a>Další kroky
* [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Další běžné používá a příklady logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Začínáme s enterprise integrace triggery a akce](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
