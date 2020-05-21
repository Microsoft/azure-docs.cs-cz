---
title: Integrace se zásadami Azure
description: Azure Policy je služba v Azure, kterou používáte k vytváření, přiřazování a správě zásad, které vynutily pravidla pro vaše prostředky.
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: e576c99e918410cb63743aaca32bdef78ae01d83
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726651"
---
# <a name="integration-with-azure-policy"></a>Integrace s Azure Policy

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad, které vynutila pravidla pro vaše prostředky, aby se zajistilo, že tyto prostředky budou vyhovovat vašim firemním standardům a smlouvám o úrovni služeb. Azure Policy vyhodnocuje prostředky pro nedodržování předpisů pomocí zásad, které přiřadíte. 

Azure Batch má dvě Vestavěná rozšíření, která vám pomůžou spravovat dodržování zásad. 

|**Název**...|   **Popis**|**Vliv (s)**|  **Verze**|    **Zdroj**
|----------------|----------|----------|----------------|---------------|
|V účtech Batch by měly být povolené diagnostické protokoly.|   Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě|AuditIfNotExists, zakázáno|  2.0.0|  GitHub|
|Pravidla upozornění na metriky by měly být nakonfigurovaná na účtech Batch.| Audituje konfiguraci pravidel upozornění metrik na účtu Batch, aby se aktivovala požadovaná metrika.|   AuditIfNotExists, zakázáno| 1.0.0|  GitHub|

Definice zásad popisují podmínky, které je třeba splnit. Podmínka porovnává vlastnost prostředku s požadovanou hodnotou. K polím vlastností prostředku se dostanete pomocí předem definovaných aliasů. Aliasy vlastností se používají pro přístup ke specifickým vlastnostem pro typ prostředku. Aliasy umožňují omezit, jaké hodnoty nebo podmínky jsou povoleny pro vlastnost v prostředku. Každý alias se mapuje na cesty v různých verzích rozhraní API pro daný typ prostředku. Během hodnocení zásad získá modul zásad cestu k vlastnosti pro tuto verzi rozhraní API.

Mezi prostředky, které služba Batch vyžaduje, patří: účet, výpočetní uzel, fond, úloha a úloha. Proto byste k přístupu ke konkrétním vlastnostem pro tyto prostředky použili aliasy vlastností. Další informace o [aliasech](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Abyste měli jistotu, že znáte aktuální aliasy a zkontrolovali své prostředky a zásady, použijte rozšíření Azure Policy pro Visual Studio Code. Dá se nainstalovat na všechny platformy, které Visual Studio Code podporuje. Tato podpora zahrnuje Windows, Linux a macOS. Viz [pokyny k instalaci](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



