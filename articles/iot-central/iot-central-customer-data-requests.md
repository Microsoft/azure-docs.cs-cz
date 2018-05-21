---
title: Zákaznická data žádosti o funkce v centrální Azure IoT
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: iot-central
ms.openlocfilehash: 0e348ca9ca85da7d4734a57afac4e5bb27217eae
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavek data zákazníků

Azure IoT střed je plně spravovaná řešení Internetu věcí (IoT) softwaru jako služby, které usnadňuje připojení, monitorovat a spravovat vaše prostředky IoT ve velkém rozsahu, vytvářet hloubkové přehledy o vašich datech IoT a provést informované akci.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifikace data zákazníků

Azure Active Directory-ID objektů se používají k identifikaci uživatelů a přiřazování rolí. Je uložený Azure IoT centrální portálu zobrazí uživatele e-mailové adresy pro přiřazení rolí, ale jenom Azure Active Directory-ID objektu, e-mailová adresa je dynamicky získaných z Azure Active Directory. Azure IoT střed správci můžete zobrazit, exportovat a aplikace uživatele v části Správa uživatele Azure IoT centrální aplikace odstranit.

V aplikaci můžete nakonfigurovat e-mailových adres přijímat výstrahy. V takovém případě e-mailové adresy jsou uloženy v rámci střed IoT a ze stránky správy účtu v aplikaci se musí spravovat.

O zařízení společnost Microsoft udržuje žádné informace a nemá přístup k datům, která povoluje zařízení uživatele korelace. Mnoho zařízení spravovaných v Azure IoT centrální nejsou osobní zařízení, třeba prodejních počítače nebo maker v. Zákazníci, však zvažte některá zařízení jako osobní identifikovatelné a podle svého uvážení mohou zachovat své vlastní prostředek nebo systémy, které tie zařízení jednotlivcům pro sledování inventáře. Azure IoT střed spravuje a ukládá všechna data přidružená k zařízení, jako by šlo osobní data.

Při použití služby Microsoft enterprise Microsoft generuje některé informace, které jsou známé jako protokoly generované systémem. Tyto protokoly tvoří konkrétní akce prováděné v rámci služby a diagnostická data související s jednotlivých zařízení a nesouvisí se aktivity uživatelů. Azure IoT střed generována protokoly nejsou přístupné nebo exportovatelný správci aplikace.

## <a name="deleting-customer-data"></a>Odstraňování dat zákazníka

Možnost odstranit data uživatele je k dispozici pouze prostřednictvím stránky IoT centrální správy. Správci aplikace můžete vybrat uživatele odstranit, a klikněte na tlačítko **odstranit** v pravém horním rohu aplikace záznamu. Správci aplikací rovněž můžete odebrat jednotlivé účty, které jsou už přidružené dané žádosti.

Po odstranění uživatele se žádné další výstrahy e-mailem na ně. Ale e-mailové adresy musí jednotlivě odeberou z každé nakonfigurované výstrahy.

Další informace najdete v tématu [nakonfigurovat pravidla a akce pro vaše zařízení](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Export dat zákazníka

Možnost exportu dat je k dispozici pouze prostřednictvím stránky IoT centrální správy. Data zákazníků, včetně přiřazených rolí můžete vybrané, zkopírovat a vložit správcem aplikace.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Další informace o správě účtu, včetně definice rolí, najdete v části [postupy při správě aplikace](howto-administer.md).
