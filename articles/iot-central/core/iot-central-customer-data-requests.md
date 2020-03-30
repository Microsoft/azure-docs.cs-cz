---
title: Funkce požadavků na zákaznická data v Azure IoT Central | Dokumenty společnosti Microsoft
description: Tento článek popisuje identifikaci, odstranění a export zákaznických dat v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3cf88b4d3c4c74493235d2997db4d464bb055b81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023748"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavků na zákaznická data

Azure IoT Central je plně spravované řešení softwaru jako služby internetu věcí (IoT), které usnadňuje připojení, monitorování a správu vašich prostředků IoT ve velkém měřítku, vytváří podrobné přehledy z dat IoT a podniká informované kroky.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifikace údajů o zákaznících

ID objektů služby Azure Active Directory se používají k identifikaci uživatelů a přiřazování rolí. Portál Azure IoT Central zobrazuje e-mailové adresy uživatelů pro přiřazení rolí, ale jenom objekt Ový Identifikátor Služby Azure je uložený, e-mailová adresa se dynamicky dotazuje z Azure Active Directory. Správci Azure IoT Central můžou zobrazit, exportovat a odstraňovat uživatele aplikací v části správy uživatelů v aplikaci Azure IoT Central.

V rámci aplikace lze e-mailové adresy nakonfigurovat tak, aby přijímaté výstrahy. V takovém případě jsou e-mailové adresy uloženy v ioT central a musí být spravovány ze stránky správy účtu v aplikaci.

Pokud jde o zařízení, společnost Microsoft neudržuje žádné informace a nemá přístup k datům, která umožňuje zařízení ke korelaci uživatelů. Mnoho zařízení spravovaných v Azure IoT Central nejsou osobní zařízení, například automat nebo kávovar. Zákazníci však mohou považovat některá zařízení za osobně identifikovatelná a podle svého uvážení mohou udržovat své vlastní systémy sledování aktiv nebo inventáře, které spojují zařízení s jednotlivci. Azure IoT Central spravuje a ukládá všechna data přidružená k zařízením, jako by to byla osobní data.

Při použití služeb Microsoft enterprise, Microsoft generuje některé informace, označované jako protokoly generované systémem. Tyto protokoly představují věcné akce prováděné v rámci služby a diagnostické údaje týkající se jednotlivých zařízení a nesouvisejí s aktivitou uživatele. Azure IoT Centrální protokoly generované systémem nejsou přístupné nebo exportovatelné správci aplikací.

## <a name="deleting-customer-data"></a>Odstranění dat zákazníků

Možnost odstranění uživatelských dat je k dispozici pouze prostřednictvím stránky správy IoT Central. Správci aplikací mohou vybrat uživatele, který má být odstraněn, a vybrat **odstranit** v pravém horním rohu aplikace, chcete-li záznam odstranit. Správci aplikací mohou také odebrat jednotlivé účty, které již nejsou přidruženy k dané aplikaci.

Po odstranění uživatele se na ně nezasílá e-mailem žádná další upozornění. Jejich e-mailová adresa však musí být jednotlivě odebrána z každé nakonfigurované výstrahy.

## <a name="exporting-customer-data"></a>Export dat zákazníků

Možnost exportu dat je k dispozici pouze prostřednictvím stránky správy IoT Central. Zákaznická data, včetně přiřazených rolí, může správce aplikace vybrat, zkopírovat a vložit.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Další informace o správě účtu, včetně definic rolí, naleznete v [tématu Správa aplikace](howto-administer.md).
