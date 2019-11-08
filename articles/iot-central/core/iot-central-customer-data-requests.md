---
title: Funkce pro žádosti o zákaznických datech v Azure IoT Central | Microsoft Docs
description: Tento článek popisuje identifikaci, odstraňování a export zákaznických dat v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 27cd2e58eabcd96414391cac1a517ea242a65c21
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809808"
---
# <a name="summary-of-customer-data-request-features"></a>Shrnutí funkcí žádosti o data zákazníků

Azure IoT Central je plně spravované řešení pro Internet věcí (IoT) typu software jako služba, které usnadňuje připojení, sledování a správu vašich prostředků IoT ve velkém měřítku, vytváření podrobných přehledů z vašich dat IoT a zajištění informování.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifikace zákaznických dat

Azure Active Directory – identifikátory objektů se používají k identifikaci uživatelů a přiřazování rolí. Portál Azure IoT Central zobrazuje e-mailové adresy uživatelů pro přiřazení rolí, ale ukládá se jenom ID objektu Azure Active Directory, e-mailová adresa se dynamicky dotazuje z Azure Active Directory. Správci Azure IoT Central můžou uživatelé aplikace zobrazit, exportovat a odstranit v části Správa uživatelů aplikace Azure IoT Central.

V rámci aplikace je možné nakonfigurovat e-mailové adresy, aby přijímaly výstrahy. V tomto případě jsou e-mailové adresy uložené v rámci IoT Central a musí se spravovat ze stránky pro správu účtu na aplikaci.

V případě zařízení společnost Microsoft neudržuje žádné informace a nemá přístup k datům, která umožňují provádět korelaci uživatelů na zařízení. Mnohé ze zařízení, která jsou spravovaná v Azure IoT Central, nejsou osobní zařízení, například prodejní počítač nebo kavárny. Zákazníci si ale můžou všimnout, že některá zařízení mají identifikovatelné osobní údaje a na základě jejich uvážení můžou udržovat své vlastní prostředky a systémy pro sledování inventáře, které se zařízení propojují jednotlivcům. Azure IoT Central spravuje a ukládá všechna data, která jsou přidružená k zařízením, jako by šlo o osobní údaje.

Pokud používáte Microsoft Enterprise Services, společnost Microsoft vygeneruje některé informace, které se nazývají systémem generované protokoly. Tyto protokoly představují věcné akce prováděné v rámci služby a diagnostická data související s jednotlivými zařízeními a nesouvisejí s aktivitou uživatelů. Protokoly generované systémem Azure IoT Central nejsou k dispozici nebo je lze exportovat pomocí Správce aplikací.

## <a name="deleting-customer-data"></a>Odstraňují se zákaznická data

Možnost odstranění uživatelských dat je zajištěna pouze prostřednictvím stránky pro správu IoT Central. Správci aplikací můžou vybrat uživatele, který se má odstranit, a vybrat **Odstranit** v pravém horním rohu aplikace a záznam odstranit. Správci aplikací můžou také odebrat jednotlivé účty, které už nejsou přidružené k dané aplikaci.

Po odstranění uživatele nebudou do nich zasílány žádné další výstrahy. Jejich e-mailová adresa se ale musí jednotlivě odebrat z každé nakonfigurované výstrahy.

Další informace najdete v tématu [Konfigurace pravidel a akcí pro vaše zařízení](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Export zákaznických dat

Možnost exportu dat je poskytována pouze prostřednictvím stránky pro správu IoT Central. Zákaznická data, včetně přiřazených rolí, můžou být vybraná, zkopírovaná a vložená správcem aplikace.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Další informace o správě účtů, včetně definic rolí, najdete v tématu [Správa aplikace](howto-administer.md).
