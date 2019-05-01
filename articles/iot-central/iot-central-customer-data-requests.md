---
title: Zákaznická data požadavky na funkce v Azure IoT Central | Dokumentace Microsoftu
description: Zákaznická data požadavky na funkce v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 4c63ac8d6b16b2a0476600f67c66b2ccc8dc2aab
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685561"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrnné informace o zákaznická data žádosti o funkce

Azure IoT Central je plně spravovaná řešení software jako služba Internetu věcí (IoT), které usnadňuje připojení, sledování a správu vašich IoT prostředků i ve velkém měřítku, vytvořit podrobné informace z vašich dat IoT a informovaně.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifikace zákaznická data

Azure Active Directory – ID objektů se používají k identifikaci uživatelů a přiřazení rolí. Azure IoT Central portálu zobrazí uživateli. e-mailové adresy pro přiřazení rolí, ale pouze Azure Active Directory – ID objektu je uložen, e-mailová adresa se dynamicky dotazovat ze služby Azure Active Directory. Správci služby Azure IoT Central můžete zobrazit, exportovat a odstranit uživatele aplikace v části administration uživatele aplikaci Azure IoT Central.

V rámci aplikace je možné nakonfigurovat e-mailových adres pro příjem oznámení. V tomto případě e-mailové adresy jsou uloženy v rámci IoT Central a na stránce účtu v aplikaci pro správu se musí spravovat.

Pokud jde o zařízení Microsoft udržuje žádné informace o a nemá přístup k datům, která umožňuje zařízení a uživatele korelace. Mnoho zařízení spravovaných v Azure IoT Central nejsou osobní zařízení, například Automat nebo kávy Tvůrce. Zákazníci mohou, ale vezměte v úvahu některé zařízení, která budou identifikovatelné osobní údaje a podle vlastního uvážení může udržovat vlastní prostředek nebo sledování systémy, které jsou zařízení, která jednotlivcům inventáře. Azure IoT Central spravuje a ukládá všechna data přidružená k zařízení, jako by šlo osobní údaje.

Při použití služby Microsoft enterprise Microsoft generuje určité informace, označované jako protokoly generované systémem. Tyto protokoly představují skutečné akce provedené v rámci služby a diagnostická data týkající se jednotlivých zařízeních a nesouvisí s aktivity uživatelů. Azure IoT Central systémem generované protokoly nejsou přístupné nebo exportovat pomocí aplikace správce.

## <a name="deleting-customer-data"></a>Odstraňuje se zákaznická data

Možnost odstranit data uživatele se poskytuje jenom pomocí stránky pro správu IoT Central. Správci aplikace mohou vybrat uživatele a odstranit, vyberte **odstranit** v pravém horním rohu aplikace záznam odstranit. Správci aplikace můžete také odebrat jednotlivé účty, které už nejsou přidružené dané žádosti.

Po odstranění uživatele se žádné další výstrahy e-mailem na ně. Ale e-mailová adresa musí jednotlivě odebrat z každé konfigurované výstrahy.

Další informace najdete v tématu [nakonfigurovat pravidla a akce pro vaše zařízení](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Export zákaznických dat

Možnost export dat je k dispozici pouze prostřednictvím stránky pro správu IoT Central. Zákaznická data, včetně přiřazených rolí můžete vybrané, zkopírovat a vložit microsoftem nebo správcem aplikace.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Další informace o správě účtu, včetně definice rolí, najdete v části [jak spravovat aplikace](howto-administer.md).
