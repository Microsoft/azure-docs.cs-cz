---
title: Obnoví vlastníka skupiny s vypršenou platností přiřazení členů v Privileged Identity Management – Azure AD | Microsoft Docs
description: Naučte se, jak prodloužit nebo prodloužit přiřazení skupin s přiřazením rolí v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87505988"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Prodloužit nebo prodloužit přiřazení skupin privilegovaného přístupu (Preview) v Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) poskytuje ovládací prvky pro správu životního cyklu přístupu a přiřazení pro privilegovaný přístup skupiny. Správci mohou přiřadit role pomocí vlastností počáteční a koncové datum a čas ukončení. Až se vám přiřazení dokončí, Privileged Identity Management pošle e-mailová oznámení ovlivněným uživatelům nebo skupinám. Také pošle e-mailová oznámení správcům prostředku, aby bylo zajištěno, že bude zachován odpovídající přístup. Přiřazení se můžou prodloužit a zůstat zobrazovat v neplatném stavu po dobu až 30 dnů, a to i v případě, že se přístup nerozšíří.

## <a name="who-can-extend-and-renew"></a>Kdo může prodloužit a prodloužit platnost

Pouze správci prostředku mohou prodloužit nebo obnovit přiřazení skupin privilegovaného přístupu. Ovlivněný uživatel nebo skupina mohou požádat o prodloužení přiřazení, jejichž platnost brzy vyprší, a požádat o prodloužení platnosti přiřazení, která již vypršela.

## <a name="when-notifications-are-sent"></a>Při odeslání oznámení

Privileged Identity Management odesílá e-mailová oznámení správcům a ovlivněným uživatelům privilegovaných skupin privilegovaného přístupu, jejichž platnost vyprší:

- Do 14 dnů před vypršením platnosti
- Jeden den před vypršením platnosti
- Po vypršení platnosti přiřazení

Správci obdrží oznámení, když se uživatel nebo skupina pokusí prodloužit nebo prodloužit přiřazení vypršení platnosti nebo vypršení platnosti. Když správce tuto žádost vyřeší, pošle se všem správcům a žádajícím uživatelům oznámení o schválení nebo zamítnutí.

## <a name="extend-group-assignments"></a>Rozšíří přiřazení skupin.

Následující kroky popisují proces pro vyžádání, řešení nebo správu rozšíření nebo obnovení přiřazení skupiny.

### <a name="self-extend-expiring-assignments"></a>Prodloužení platnosti přiřazení samy sebe

Uživatelé přiřazení do skupiny privilegovaných přístupu můžou na stránce **přiřazení** pro danou skupinu přesáhnout vypršení platnosti přiřazení skupin přímo na kartě **oprávněný** nebo **aktivní** . Uživatelé nebo skupiny můžou požádat o prodloužení oprávněných a aktivních přiřazení, jejichž platnost vyprší během příštích 14 dnů.

![Stránka Moje role se seznamem oprávněných assgnments se sloupcem akce](media/groups-renew-extend/self-extend-group-assignment.png)

Pokud je koncové datum konce přiřazení do 14 dnů, je k dispozici příkaz **extend** . Pokud chcete požádat o rozšíření přiřazení skupiny, vyberte **rozšířit** a otevřete formulář žádosti.

![Rozšíří podokno přiřazení skupiny s odůvodněním a podrobnostmi.](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>Doporučujeme, abyste měli informace o tom, proč je rozšíření nezbytné, a jak dlouho má být rozšíření uděleno (Pokud máte tyto informace).

V některých případech správci dostanou e-mailem oznámení s požadavkem na revizi žádosti o rozšíření. Pokud již byl požadavek na prodloužení odeslán, zobrazí se na portálu oznámení Azure.

Chcete-li zobrazit stav nebo zrušit žádost, otevřete stránku **nedokončené žádosti** pro přiřazení skupiny.

![Přiřazení skupin privilegovaný přístup – nevyřízené žádosti ukazující odkaz pro zrušení](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Rozšíření schválené správcem

Když uživatel nebo skupina odešle požadavek na prodloužení přiřazení skupiny, obdrží správci e-mailové oznámení, které obsahuje podrobnosti o původním přiřazení a důvod žádosti. Oznámení obsahuje přímý odkaz na žádost, kterou správce schválí nebo zamítne.

Kromě používání následujících odkazů z e-mailu můžou správci schvalovat nebo zamítnout požadavky na portálu pro správu Privileged Identity Management a v levém podokně vybrat **schvalovat žádosti** .

![Přiřazení privilegovaných skupin přístupu – žádosti schvalovat požadavky na výpis stránek a odkazy ke schválení nebo zamítnutí](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

Když správce vybere **schválit** nebo **Odepřít**, zobrazí se podrobnosti o žádosti spolu s polem, které poskytuje obchodní odůvodnění protokolů auditu.

![Schválení žádosti o přiřazení skupiny, důvod žadatele, typ přiřazení, čas spuštění, čas ukončení a důvod](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

Když schválíte požadavek na rozšiřování přiřazení skupiny, můžou správci prostředků zvolit nové datum zahájení, datum ukončení a typ přiřazení. Změna typu přiřazení může být nutná v případě, že správce chce poskytnout omezený přístup k dokončení konkrétního úkolu (například jeden den). V tomto příkladu může správce změnit přiřazení z **oprávněné** na **aktivní**. To znamená, že můžou poskytnout přístup žadateli, aniž by se museli aktivovat.

### <a name="admin-initiated-extension"></a>Rozšíření iniciované správcem

Pokud uživatel přiřazený k skupině nepožaduje rozšíření pro přiřazení skupiny, může správce prodloužit přiřazení jménem uživatele. Rozšíření pro správu přiřazení skupiny nevyžadují schválení, ale oznámení se odesílají všem ostatním správcům po rozšíření přiřazení.

Chcete-li roztáhnout přiřazení skupiny, přejděte k zobrazení přiřazení v Privileged Identity Management. Vyhledejte přiřazení, které vyžaduje rozšíření. Potom ve sloupci Akce vyberte **Rozšířené** .

![Stránka přiřazení – seznam oprávněných přiřazení skupin s odkazy, které se mají zvětšit](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Obnovit přiřazení skupiny

Proces obnovení přiřazení skupiny s vypršenou platností se v koncepčním režimu podobá procesu požadavku na rozšíření se liší. Pomocí následujících kroků můžou přiřazení a správci v případě potřeby obnovit přístup k vypršení platnosti.

### <a name="self-renew"></a>Samoobslužné obnovení

Uživatelé, kteří už nemohou získat přístup k prostředkům, můžou mít k historii přiřazení vypršení platnosti až 30 dnů. Provedete to tak, že v levém podokně přejdete na **Moje role** a pak vyberete kartu **neukončená přiřazení** .

![Stránka Moje role – karta přiřazení po vypršení platnosti](media/groups-renew-extend/groups-renew-from-my-roles.png)

Seznam přiřazení se zobrazí ve výchozím nastavení s **oprávněnými přiřazeními**. Pomocí rozevírací nabídky můžete přepínat mezi oprávněnými a aktivními přiřazeními.

Chcete-li požádat o obnovení některého z přiřazení skupiny v seznamu, vyberte akci **obnovit** . Pak zadejte důvod pro požadavek. Je vhodné zadat dobu trvání kromě jakýchkoli dalších souvislostí nebo obchodních odůvodnění, které mohou správci prostředků pomoct rozhodnout se schválit nebo odepřít.

![Dialogové okno pro obnovení skupiny přiřazení, které ukazuje důvod](media/groups-renew-extend/groups-renew-request-form.png)

Po odeslání žádosti budou správci prostředků upozorněni na nevyřízenou žádost o obnovení přiřazení skupiny.

### <a name="admin-approves"></a>Správce schválí

Správci prostředků mohou získat přístup k žádosti o obnovení z odkazu v e-mailovém oznámení nebo pomocí Privileged Identity Management z Azure Portal a vybrat **schvalovat žádosti** v levém podokně.

Když správce vybere **schválit** nebo **Odepřít**, zobrazí se podrobnosti o žádosti spolu s polem, které poskytuje obchodní odůvodnění protokolů auditu.

Když schválíte požadavek na obnovení přiřazení skupiny, musí správci prostředků zadat nové počáteční datum, datum ukončení a typ přiřazení.

## <a name="next-steps"></a>Další kroky

- [Schválení nebo zamítnutí žádostí o přiřazení skupin privilegovaného přístupu v Privileged Identity Management](groups-approval-workflow.md)
- [Konfigurace nastavení privilegované skupiny přístupu v Privileged Identity Management](groups-role-settings.md)
