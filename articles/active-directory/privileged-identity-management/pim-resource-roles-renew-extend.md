---
title: Rozšiřování nebo obnova přiřazení rolí prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se, jak prodloužit nebo prodloužit přiřazení rolí prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eeba148945f7aa52dd32edc0fec4c45e2ab0748
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804135"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Rozšiřování nebo obnova přiřazení rolí prostředků Azure v PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) představuje nové ovládací prvky pro správu životního cyklu přístupu a přiřazení pro prostředky Azure. Správci mohou přiřadit členství pomocí vlastností počáteční a koncové datum a čas ukončení. Když se to blíží, PIM pošle e-mailová oznámení postiženým uživatelům nebo skupinám. Také pošle e-mailová oznámení správcům prostředku, aby bylo zajištěno, že bude zachován odpovídající přístup. Přiřazení se můžou prodloužit a zůstat zobrazovat v neplatném stavu po dobu až 30 dnů, a to i v případě, že se přístup nerozšíří.

## <a name="who-can-extend-and-renew"></a>Kdo se může prodloužit a prodloužit?

Pouze správci prostředku mohou prodloužit nebo obnovit přiřazení rolí. Ovlivněný člen může požádat o prodloužení platnosti rolí, jejichž platnost brzy vyprší, a požaduje obnovení rolí, jejichž platnost již vypršela.

## <a name="when-are-notifications-sent"></a>Kdy jsou oznámení odeslána?

PIM pošle e-mailová oznámení správcům a ovlivněným členům rolí, jejichž platnost vyprší do 14 dnů a jeden den před vypršením platnosti. Pošle další e-mail, když platnost přiřazení oficiálně vyprší. 

Správci dostanou oznámení v případě, že je požadavek na prodloužení nebo prodloužení žádosti role vyprší nebo vypršela její platnost. Když určitý správce tuto žádost vyřeší, všichni ostatní správci budou upozorněni na rozhodnutí o řešení (schváleno nebo zamítnuto). Žádající Člen se pak oznámí rozhodnutí. 

## <a name="extend-role-assignments"></a>Rozšíří přiřazení rolí

Následující kroky popisují proces pro vyžádání, řešení nebo správu rozšíření nebo obnovení přiřazení role. 

### <a name="member-extend"></a>Rozšíření člena

Členové přiřazení role mohou prodloužení platnosti přiřazení rolí prodlouženi přímo z karty **oprávněné** nebo **aktivní** na stránce **Moje role** v prostředku a ze stránky **role** na nejvyšší úrovni na portálu PIM. Členové můžou požádat o prodloužení oprávněných a aktivních (přiřazených) rolí, jejichž platnost vyprší během příštích 14 dnů.

![Prostředky Azure – stránka moje role se seznamem oprávněných rolí se sloupcem akce](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Když je koncové datum přiřazení do 14 dnů, tlačítko, které se **rozšiřuje** , se v uživatelském rozhraní aktivuje jako aktivní odkaz. V následujícím příkladu Předpokládejme, že aktuální datum je 27. března.

![Sloupec akce s odkazy pro aktivaci nebo rozšiřování](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Chcete-li požádat o rozšíření přiřazení této role, vyberte možnost **rozšířit** a otevřete formulář žádosti.

![Rozšíří podokno přiřazení role s polem důvod.](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Chcete-li zobrazit informace o původním přiřazení, rozbalte **Podrobnosti přiřazení**. Zadejte důvod pro požadavek na rozšíření a pak vyberte **rozšířit**.

>[!Note]
>Doporučujeme, abyste měli informace o tom, proč je rozšíření nezbytné, a jak dlouho má být rozšíření uděleno (Pokud máte tyto informace).

![Rozšířit podokno přiřazení role s rozbalenými podrobnostmi o přiřazení](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

V některých případech správci prostředků dostanou e-mailem oznámení s požadavkem na revizi žádosti o rozšíření. Pokud již byl požadavek na prodloužení odeslán, zobrazí se v horní části Azure Portal zpráva informační zprávy vysvětlující chybu.

![Oznámení s vysvětlením, že již existuje existující rozšíření přiřazení nedokončené role](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Kliknutím na stránku **nedokončené žádosti** v levém podokně zobrazte stav žádosti nebo ji zrušte.

![Prostředky Azure – stránka nevyřízené žádosti se seznamem všech nevyřízených žádostí a odkaz na zrušení](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Schválení správcem

Když člen odešle požadavek na prodloužení přiřazení role, správci prostředků obdrží e-mailové oznámení, které obsahuje podrobnosti o původním přiřazení a důvod žádosti. Oznámení obsahuje přímý odkaz na žádost, kterou správce schválí nebo zamítne. 

Kromě používání následujících odkazů z e-mailu můžou správci schvalovat nebo zamítnout požadavky na portálu pro správu PIM a vybrat **schvalovat žádosti** v levém podokně.

![Prostředky Azure – žádosti schvalovat požadavky na výpis stránek a odkazy na schválení nebo zamítnutí](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Když správce vybere **schválit** nebo **Odepřít**, zobrazí se podrobnosti o žádosti spolu s polem, které poskytne odůvodnění protokolů auditu.

![Schválit žádost o přiřazení role s důvodem žadatele, typem přiřazení, časem spuštění, časem ukončení a důvodem](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Když schválíte požadavek na prodloužení přiřazení role, můžou správci prostředků zvolit nové datum zahájení, datum ukončení a typ přiřazení. Změna typu přiřazení může být nutná v případě, že správce chce poskytnout omezený přístup k dokončení konkrétního úkolu (například jeden den). V tomto příkladu může správce změnit přiřazení z **oprávněné** na **aktivní**. To znamená, že můžou poskytnout přístup žadateli, aniž by se museli aktivovat.

### <a name="admin-extend"></a>Rozšíření správce

Pokud člen role zapomene nebo není schopen požádat o rozšíření členství v roli, může správce prodloužit přiřazení jménem člena. Rozšíření pro správu členství v rolích nevyžadují schválení, ale oznámení se odesílají všem ostatním správcům po rozšíření role.

Chcete-li zvětšit členství v roli, přejděte k roli prostředku nebo zobrazení členů v PIM. Vyhledejte člena, který vyžaduje rozšíření. Potom ve sloupci Akce vyberte **Rozšířené** .

![Prostředky Azure – stránka členů se seznamem oprávněných rolí s odkazy, které se mají zvětšit](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Obnovit přiřazení rolí

V koncepčním podobnosti s procesem pro vyžádání rozšíření se proces obnovení přiřazení role s vypršenou platností liší. Pomocí následujících kroků můžou členové a správci obnovit přístup k rolím s vypršenou platností, pokud je to potřeba.

### <a name="member-renew"></a>Obnovení člena

Členové, kteří již nemohou získat přístup k prostředkům, mohou mít k historii přiřazení vypršení platnosti až 30 dnů. Provedete to tak, že v levém podokně přejdete na **Moje role** a potom v části role prostředků Azure vyberete kartu **role, jejichž platnost vypršela** .

![Stránka Moje role – karta role s vypršenou platností](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Seznam zobrazených rolí je ve výchozím nastavení **oprávněnými rolemi**. Pomocí rozevírací nabídky můžete přepínat mezi oprávněnými a aktivními přiřazenými rolemi.

Chcete-li požádat o prodloužení přiřazení role v seznamu, vyberte akci **obnovit** . Pak zadejte důvod pro požadavek. Je vhodné zadat dobu trvání kromě dalšího kontextu, který správcům prostředků pomůže se rozhodnout, že se má schválit nebo odepřít.

![Podokno pro prodloužení přiřazení role se zobrazeným polem důvod](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Po odeslání žádosti budou správci prostředků upozorněni na nevyřízenou žádost o obnovení přiřazení role.

### <a name="admin-approves"></a>Správce schválí

Správci prostředků mají přístup k žádosti o obnovení z odkazu v e-mailovém oznámení nebo z Azure Portal k přístupu PIM z a v levém podokně vyberete **schvalovat žádosti** .

![Prostředky Azure – žádosti schvalovat požadavky na výpis stránek a odkazy na schválení nebo zamítnutí](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Když správce vybere **schválit** nebo **Odepřít**, zobrazí se podrobnosti žádosti spolu s polem, které poskytne odůvodnění protokolů auditu.

![Schválit žádost o přiřazení role s důvodem žadatele, typem přiřazení, časem spuštění, časem ukončení a důvodem](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Při schvalování žádosti o obnovení přiřazení role musí správci prostředků zadat nové datum zahájení, datum ukončení a typ přiřazení. 

### <a name="admin-renew"></a>Obnovení správce

Správci prostředků mohou prodloužit přiřazení rolí vypršení platnosti z karty **Členové** v levé navigační nabídce prostředku. Můžou také prodloužit přiřazení rolí s vypršenou platností z karty role s **vypršenou** platností v roli prostředku.

Pokud chcete zobrazit seznam všech přiřazení rolí s vypršenou platností, na obrazovce **Členové** vyberte **role, jejichž platnost vypršela**.

![Prostředky Azure – stránka členů se seznamem rolí s ukončenou platností odkazy pro obnovení](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Další postup

- [Schválení nebo zamítnutí žádostí o role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
