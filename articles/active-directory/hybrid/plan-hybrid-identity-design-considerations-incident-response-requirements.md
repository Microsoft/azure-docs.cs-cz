---
title: Návrh hybridní identity – požadavky na reakci na incidenty Azure | Microsoft Docs
description: Určete možnosti monitorování a vytváření sestav pro řešení hybridní identity, které může využívat IT k tomu, aby mohly provádět akce pro identifikaci a zmírnění potenciálních hrozeb.
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
ms.openlocfilehash: 8c68be4e46693621f77cb25dd8f34f8e0b7d75dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89278407"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na reakci na incidenty pro vaše řešení hybridní identity
U velkých nebo středních organizací pravděpodobně dojde k [reakci na incidenty zabezpečení](/previous-versions/tn-archive/cc700825(v=technet.10)) , která bude pomáhat při provádění akcí podle úrovně incidentu. Systém správy identit je důležitou součástí procesu reakce na incidenty, protože je možné ho použít k identifikaci toho, kdo provedl určitou akci s cílem. Řešení hybridní identity musí být schopné poskytovat funkce monitorování a vytváření sestav, které může využít IT k tomu, aby provedla akce k identifikaci a zmírnění potenciální hrozby. V typickém plánu odpovědí na incidenty budete mít jako součást plánu tyto fáze:

1. Počáteční posouzení.
2. Komunikace incidentu.
3. Řízení škod a snížení rizik.
4. Identifikace toho, co bylo zabezpečení a závažnost.
5. Uchování legitimace.
6. Oznámení příslušným stranám.
7. Obnovení systému.
8. Nápovědě.
9. Vyhodnocování škod a nákladů.
10. Zpracuje a naplánuje revizi.

Při identifikaci toho, co bylo ohroženo a závažnosti-fáze, bude nutné identifikovat ohrožené systémy, soubory, které byly použity, a určit citlivost těchto souborů. Systém hybridní identity by měl být schopný splnit tyto požadavky, aby bylo možné identifikovat uživatele, který tyto změny provedl. 

## <a name="monitoring-and-reporting"></a>Monitorování a vytváření sestav
Systém identit může také mnohokrát pomáhat v prvotní fázi hodnocení, hlavně v případě, že systém obsahuje integrované funkce auditování a vytváření sestav. Během prvotního posouzení musí být správce IT schopný identifikovat podezřelou aktivitu, jinak by systém měl být schopný ho aktivovat automaticky na základě předem nakonfigurované úlohy. Řada aktivit může naznačovat možný útok, ale v jiných případech může nesprávně nakonfigurovaný systém vést k řadě falešně pozitivních hodnot v systému zjišťování neoprávněných vniknutí. 

Systém správy identit by měl správcům IT pomáhat při identifikaci a hlášení těchto podezřelých aktivit. Tyto technické požadavky lze obvykle splnit monitorováním všech systémů a vyplněním možností vytváření sestav, které mohou zvýrazňovat potenciální hrozby. Níže uvedené otázky vám pomůžou při návrhu řešení hybridní identity a přitom vzít v úvahu požadavky na reakci na incidenty:

* Má vaše společnost na svém místě reakci na incidenty zabezpečení?
  * Pokud ano, je aktuální systém pro správu identit, který se používá jako součást procesu?
* Potřebuje vaše společnost identifikovat podezřelé pokusy uživatelů v různých zařízeních?
* Potřebuje vaše společnost zjistit možné přihlašovací údaje uživatele?
* Potřebuje vaše společnost Auditovat přístup a akci uživatele?
* Potřebuje vaše společnost znát, kdy si uživatel resetuje heslo?

## <a name="policy-enforcement"></a>Vynucování zásad
Během řízení škod a snižování rizik je důležité rychle snížit skutečné a potenciální důsledky útoku. Tato akce, kterou v tomto okamžiku provedete, může probíhat rozdíl mezi podmnožinou a hlavní verzí. Přesná odpověď bude záviset na vaší organizaci a povaze útoku, který čelíte. Pokud počáteční posouzení dospělo k narušení účtu, budete muset vynutilit zásady pro blokování tohoto účtu. To je jenom jeden příklad, ve kterém se bude využívat systém správy identit. Níže uvedené otázky vám pomůžou navrhovat řešení hybridní identity a přitom vzít v úvahu, jak se budou zásady vyhovět, aby reagovaly na probíhající incident:

* Má vaše společnost zavedeny zásady, které zablokují uživatelům přístup k síti v případě potřeby?
  * Pokud ano, aktuální řešení se integruje se systémem správy hybridních identit, který budete chtít přijmout?
* Potřebuje vaše společnost vymáhat podmíněný přístup pro uživatele, kteří jsou v karanténě? 

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) překročí dostupné možnosti a výhody a nevýhody jednotlivých možností.  Po zodpovězení těchto otázek si vyberete, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Definovat strategii ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)