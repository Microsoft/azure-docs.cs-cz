---
title: Návrh hybridní identity – požadavky na reakci na incidenty Azure | Dokumenty společnosti Microsoft
description: Určete možnosti monitorování a vytváření sestav pro řešení hybridní identity, které mohou být využity it k provádění opatření k identifikaci a zmírnění potenciálních hrozeb
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b5e37c29e4b3df3f171f683266b5d0a3e0c95d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109280"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na reakci na incidenty pro řešení hybridní identity
Velké nebo střední organizace s největší pravděpodobností budou mít [bezpečnostní incident odpověď](https://technet.microsoft.com/library/cc700825.aspx) na místě pomoci IT přijmout opatření odpovídajícím způsobem na úroveň incidentu. Systém správy identit je důležitou součástí procesu reakce na incidenty, protože jej lze použít k identifikaci, kdo provedl konkrétní akci proti cíli. Řešení hybridní identity musí být schopno poskytovat funkce monitorování a vytváření sestav, které mohou it technologie využít k provádění akcí k identifikaci a zmírnění potenciální hrozby. V typickém plánu reakce na incidenty budete mít jako součást plánu následující fáze:

1. Počáteční posouzení.
2. Incidentová komunikace.
3. Kontrola poškození a snížení rizika.
4. Identifikace toho, co to bylo kompromisem a závažností.
5. Uchovávání důkazů.
6. Oznámení příslušným stranám.
7. Obnovení systému.
8. Dokumentace.
9. Posouzení škod a nákladů.
10. Revize procesu a plánu.

Během identifikace toho, co to bylo kompromis a závažnost- fáze, bude nutné identifikovat systémy, které byly ohroženy, soubory, které byly přístupné a určit citlivost těchto souborů. Váš hybridní systém identit by měl být schopen splnit tyto požadavky, které vám pomohou identifikovat uživatele, který tyto změny provedl. 

## <a name="monitoring-and-reporting"></a>Monitorování a vytváření sestav
Mnohokrát systém identit může také pomoci ve fázi počátečního hodnocení, zejména pokud systém má zabudované v auditování a vytváření sestav. Během počátečního hodnocení musí být správce IT schopen identifikovat podezřelou aktivitu, jinak by systém měl být schopen ji automaticky spustit na základě předem nakonfigurované úlohy. Mnoho aktivit může naznačovat možný útok, ale v jiných případech může špatně nakonfigurovaný systém vést k řadě falešných poplachů v systému detekce vniknutí. 

Systém správy identit by měl správcům IT pomoci identifikovat a hlásit tyto podezřelé aktivity. Obvykle mohou být tyto technické požadavky splněny sledováním všech systémů a schopností vykazování, která může upozornit na potenciální hrozby. Pomocí níže uvedených otázek můžete navrhnout řešení hybridní identity s přihlédnutím k požadavkům na reakci na incidenty:

* Má vaše společnost bezpečnostní incident odpověď na místě?
  * Pokud ano, používá se jako součást procesu současný systém správy identit?
* Potřebuje vaše společnost identifikovat podezřelé pokusy o přihlášení od uživatelů na různých zařízeních?
* Potřebuje vaše společnost odhalit potenciální ohrožení přihlašovacích údajů uživatele?
* Potřebuje vaše společnost auditovat přístup a akci uživatele?
* Potřebuje vaše společnost vědět, kdy uživatel resetuje své heslo?

## <a name="policy-enforcement"></a>Vynucování zásad
Během kontroly poškození a fáze snižování rizika je důležité rychle snížit skutečné a potenciální účinky útoku. Tato akce, kterou budete mít v tomto bodě může být rozdíl mezi menší a hlavní jeden. Přesná odpověď bude záviset na vaší organizaci a povaze útoku, kterému čelíte. Pokud počáteční hodnocení dospělo k závěru, že účet byl napaden, budete muset vynutit zásady pro blokování tohoto účtu. To je jen jeden příklad, kdy bude systém správy identit využit. Pomocí níže uvedených otázek můžete navrhnout řešení hybridní identity a zároveň vzít v úvahu, jak budou zásady vynuceny tak, aby reagovaly na probíhající incident:

* Má vaše společnost zavedeny zásady, které uživatelům v případě potřeby blokují přístup k síti?
  * Pokud ano, integruje se současné řešení se systémem správy hybridníidentity, který přijmete?
* Potřebuje vaše společnost vynutit podmíněný přístup pro uživatele, kteří jsou v karanténě? 

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Definovat strategii ochrany údajů](plan-hybrid-identity-design-considerations-data-protection-strategy.md) půjde přes možnosti, které jsou k dispozici a výhody / nevýhody každé možnosti.  Tím, že odpověděl na tyto otázky, budete vybírat, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Definování strategie ochrany údajů](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

