---
title: Integrace s azure zásadami – Azure Batch | Dokumenty společnosti Microsoft
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618370"
---
# <a name="integration-with-azure-policy"></a>Integrace s azure policy

Azure Policy je služba v Azure, kterou používáte k vytváření, přiřazování a správě zásad, které vynucují pravidla nad prostředky, aby zajistily, že tyto prostředky zůstanou v souladu s vašimi podnikovými standardy a smlouvami o úrovni služeb. Azure Policy vyhodnocuje vaše prostředky z důvodu nedodržování zásad, které přiřadíte. 

Azure Batch má dvě předdefinovaná rozšíření, která vám pomůžou spravovat dodržování zásad. 

|**Jméno**...|   **Popis**|**Efekty**|  **Verze**|    **Zdroj**
|----------------|----------|----------|----------------|---------------|
|Diagnostické protokoly v dávkových účtech by měly být povoleny.|   Auditování povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity pro účely šetření; dojde k incidentu zabezpečení nebo v případě ohrožení sítě|AuditIfNotExists, zakázáno|  2.0.0|  GitHub|
|Pravidla upozornění metriky by měla být konfigurována u dávkových účtů.| Auditovat konfiguraci pravidel upozornění metriky pro dávkový účet, aby byla povolena požadovaná metrika|   AuditIfNotExists, zakázáno| 1.0.0|  GitHub|

Definice zásad popisují podmínky, které je třeba splnit. Podmínka porovná vlastnost prostředku s požadovanou hodnotou. K polím vlastností prostředku se přistupuje pomocí předdefinovaných aliasů. Aliasy vlastností se používají k přístupu k určitým vlastnostem pro typ prostředku. Aliasy umožňují omezit, jaké hodnoty nebo podmínky jsou povoleny pro vlastnost prostředku. Každý alias se mapuje na cesty v různých verzích rozhraní API pro daný typ prostředku. Během vyhodnocení zásad modul získá cestu vlastnosti pro tuto verzi rozhraní API.

Mezi prostředky vyžadované batchem patří: účet, výpočetní uzel, fond, úloha a úloha. Takže byste použít aliasy vlastností pro přístup k určité vlastnosti pro tyto prostředky. Další informace o [aliasech](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Chcete-li se ujistit, že znáte aktuální aliasy a zkontrolujte své prostředky a zásady, použijte rozšíření zásad Azure pro kód Visual Studia. Může být nainstalován na všech platformách, které jsou podporovány Visual Studio Code. Tato podpora zahrnuje Windows, Linux a macOS. Viz [pokyny pro instalaci](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



