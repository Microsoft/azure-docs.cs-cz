---
title: Obnovení přiřazení rolí prostředků Azure v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak rozšířit nebo obnovit přiřazení rolí prostředků Azure v privilegované správě identit Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022917"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Rozšíření nebo obnovení přiřazení rolí prostředků Azure v privilegované správě identit

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) poskytuje ovládací prvky pro správu životního cyklu přístupu a přiřazení pro prostředky Azure. Správci mohou přiřadit role pomocí vlastností počátečního a koncového data a času. Když se blíží ukončení přiřazení, správa privilegovaných identit odešle e-mailová oznámení postiženým uživatelům nebo skupinám. Také odesílá e-mailová oznámení správcům prostředku, aby bylo zajištěno, že bude zachován odpovídající přístup. Přiřazení mohou být obnovena a zůstat viditelná ve stavu s ukončenou platností po dobu až 30 dnů, i když přístup není prodloužen.

## <a name="who-can-extend-and-renew"></a>Kdo může rozšířit a obnovit?

Přiřazení rolí mohou rozšířit nebo obnovit pouze správci prostředku. Ovlivněný uživatel nebo skupina může požádat o rozšíření rolí, jejichž platnost brzy vyprší, a požádat o obnovení rolí, jejichž platnost již vypršela.

## <a name="when-are-notifications-sent"></a>Kdy jsou odesílána oznámení?

Správa privilegovaných identit odesílá e-mailová oznámení správcům a ovlivněným uživatelům nebo skupinám rolí, jejichž platnost vyprší do 14 dnů a jednoho dne před vypršením platnosti. Odešle další e-mail, když oficiálně vyprší platnost přiřazení.

Správci obdrží oznámení, když uživateli nebo skupině přidělily žádosti o prodloužení nebo obnovení role s vypršením platnosti nebo s ukončenou platností. Pokud konkrétní správce žádost vyřeší, všichni ostatní správci budou o rozhodnutí o řešení informováni (schváleno nebo zamítnuto). Poté je žádající uživatel nebo skupina informována o rozhodnutí.

## <a name="extend-role-assignments"></a>Rozšířit přiřazení rolí

Následující kroky popisují proces vyžádání, řešení nebo správy rozšíření nebo obnovení přiřazení role.

### <a name="self-extend-expiring-assignments"></a>Vlastní prodloužení přiřazení s končící platností

Uživatelé nebo skupiny přiřazené k roli mohou prodloužit vypršení platnosti přiřazení rolí přímo z karty **Způsobilé** nebo **Aktivní** na stránce **Moje role** prostředku a na stránce Na nejvyšší úrovni Moje **role** na portálu Správy privilegovaných identit. Uživatelé nebo skupiny mohou požádat o rozšíření způsobilých a aktivních (přiřazených) rolí, jejichž platnost vyprší v příštích 14 dnech.

![Prostředky Azure – stránka Moje role se seznamem způsobilých rolí se sloupcem Akce](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Pokud je datum a čas ukončení přiřazení do 14 dnů, stane se tlačítko **Rozšířit** aktivním odkazem v uživatelském rozhraní. V následujícím příkladu předpokládejme, že aktuální datum je 27.

![Sloupec akce s odkazy na aktivovat nebo rozšířit](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Chcete-li požádat o rozšíření tohoto přiřazení role, vyberte **možnost Rozšířit** a otevřete formulář žádosti.

![Rozšíření podokna přiřazení role pomocí pole Důvod](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Chcete-li zobrazit informace o původním přiřazení, rozbalte **položku Podrobnosti přiřazení**. Zadejte důvod žádosti o rozšíření a pak vyberte **rozšířit**.

>[!NOTE]
>Doporučujeme zahrnout podrobnosti o tom, proč je prodloužení nezbytné a jak dlouho by mělo být rozšíření uděleno (pokud máte tyto informace).

![Rozšířit podokno přiřazení role s rozbalenými podrobnostmi přiřazení](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Během několika okamžiků obdrží správci prostředků e-mailové oznámení s žádostí o kontrolu žádosti o rozšíření. Pokud už byla odeslána žádost o prodloužení, zobrazí se na portálu oznámení Azure.

![Oznámení vysvětlující, že již existuje rozšíření o přiřazení role čekající na vyřízení.](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Přejděte na stránku **Nevyřízené žádosti** a zobrazte stav žádosti nebo ji zrušte.

![Prostředky Azure – stránka Čekající požadavky se seznamem všech nevyřízených požadavků a odkazu na zrušit](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Rozšíření schválené správcem

Když uživatel nebo skupina odešle žádost o rozšíření přiřazení role, správci prostředků obdrží e-mailové oznámení, které obsahuje podrobnosti o původním přiřazení a důvod žádosti. Oznámení obsahuje přímý odkaz na žádost správce o schválení nebo zamítnutí.

Kromě použití následujícího odkazu z e-mailu mohou správci schvalovat nebo zamítat žádosti tak, že přejdou na portál správy správy privilegované identity a v levém podokně vyberou **možnost Schválit žádosti.**

![Prostředky Azure – schválení žádostí o výpis žádostí a odkazů na schválení nebo zamítnutí](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Pokud správce vybere **schválit** nebo **odepřít**, zobrazí se podrobnosti žádosti spolu s polem pro poskytnutí obchodního odůvodnění protokolů auditu.

![Schválit žádost o přiřazení role s důvodem žádosti, typem přiřazení, počátečním časem, časem ukončení a důvodem](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Při schvalování žádosti o rozšíření přiřazení role mohou správci zdrojů zvolit nové počáteční datum, koncové datum a typ přiřazení. Změna typu přiřazení může být nezbytná, pokud správce chce poskytnout omezený přístup k dokončení určitého úkolu (například jeden den). V tomto příkladu může správce změnit přiřazení z **Způsobilé** na **Aktivní**. To znamená, že mohou poskytnout přístup k žadateli bez nutnosti jejich aktivace.

### <a name="admin-initiated-extension"></a>Rozšíření iniciované správcem

Pokud uživatel přiřazený k roli nepožádá o rozšíření pro přiřazení role, může správce rozšířit přiřazení jménem uživatele. Rozšíření pro správu přiřazení role nevyžadují schválení, ale oznámení jsou odesílána všem ostatním správcům po rozšíření role.

Chcete-li rozšířit přiřazení role, přejděte k roli zdroje nebo zobrazení přiřazení v části Správa privilegovaných identit. Najděte přiřazení, které vyžaduje rozšíření. Pak ve sloupci akce vyberte **Rozšířit.**

![Zdroje Azure – stránka přiřazení se seznamem způsobilých rolí s odkazy na rozšíření](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Obnovení přiřazení rolí

Zatímco koncepčně podobné procesu pro vyžádání rozšíření, proces obnovení přiřazení role vypršela, se liší. Pomocí následujících kroků mohou přiřazení a správci v případě potřeby obnovit přístup k rolím, jejichž platnost vypršela.

### <a name="self-renew"></a>Vlastní obnova

Uživatelé, kteří již nemají přístup k prostředkům, mají přístup k historii přiřazení s ukončenou platností až 30 dní. Chcete-li to provést, přejdou do **moje role** v levém podokně a pak vyberte kartu **Vypršela platnost role** v části role prostředků Azure.

![Stránka Moje role – karta Role s ukončenou platností](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Seznam rolí zobrazených ve výchozím nastavení **pro způsobilé role**. Pomocí rozevírací nabídky můžete přepínat mezi přiřazenými rolemi Způsobilé a Aktivní.

Chcete-li požádat o obnovení pro libovolné přiřazení rolí v seznamu, vyberte akci **Obnovit.** Pak zadejte důvod žádosti. Je užitečné poskytnout dobu trvání kromě jakékoli další kontext nebo obchodní odůvodnění, které může pomoci správce prostředku rozhodnout o schválení nebo zamítnutí.

![Podokno Obnovení přiřazení role zobrazující okno Důvod](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Po odeslání žádosti jsou správci prostředků upozorněni na nevyřízený požadavek na obnovení přiřazení role.

### <a name="admin-approves"></a>Správce schvaluje

Správci prostředků mají přístup k žádosti o obnovení z odkazu v e-mailovém oznámení nebo přístupem ke správě privilegovaných identit z portálu Azure a výběrem **možnosti Schválit požadavky** z levého podokna.

![Prostředky Azure – schválení žádostí o výpis žádostí a odkazů na schválení nebo zamítnutí](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Pokud správce vybere **schválit** nebo **odepřít**, zobrazí se podrobnosti žádosti spolu s polem, které poskytuje obchodní odůvodnění protokolů auditu.

![Schválit žádost o přiřazení role s důvodem žádosti, typem přiřazení, počátečním časem, časem ukončení a důvodem](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Při schvalování žádosti o obnovení přiřazení role musí správci zdrojů zadat nové počáteční datum, koncové datum a typ přiřazení.

### <a name="admin-renew"></a>Obnovení správce

Správci prostředků mohou obnovit přiřazení rolí s ukončenou platností z karty **Členové** v levé navigační nabídce zdroje. Mohou také obnovit přiřazení rolí s ukončenou platností z karty **Role s ukončenou platností** v roli prostředku.

Chcete-li zobrazit seznam všech přiřazení rolí, jejichž platnost vypršela, vyberte na obrazovce **Členové** **možnost Počet rolí vypršela**.

![Prostředky Azure – stránka členů obsahuje funkce s prošlou platností s odkazy na obnovení](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Další kroky

- [Schválení nebo zamítnutí požadavků na role prostředků Azure v privilegované správě identit](pim-resource-roles-approval-workflow.md)
- [Konfigurace nastavení role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-configure-role-settings.md)
