---
title: Rozšíření a revizi rolí v prostředků Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Tento dokument popisuje, jak rozšířit a obnovit role prostředků Azure pro prostředky PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 44ce8c77a37d3a704c4f9516d942085c878307af
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234161"
---
# <a name="extend-and-review-roles-in-azure-resources-by-using-privileged-identity-management"></a>Rozšíření a revizi rolí v prostředků Azure pomocí Privileged Identity Management

Privileged Identity Management (PIM) k prostředkům Azure zavádí nové ovládací prvky pro správu životního cyklu přístup a přiřazení k prostředkům Azure. Správci mohou přiřadit členství pomocí vlastnosti počáteční a koncové datum a čas. Když se blíží konec přiřazování, PIM odešle e-mailová oznámení k ovlivnění uživatelé nebo skupiny. Rovněž odesílá e-mailová oznámení správcům prostředku zajistit, že se zachová odpovídající přístup. Přiřazení může být obnovena a zůstanou viditelné v stav vypršené platnosti po dobu 30 dnů, i když není rozšířeno přístup.

## <a name="who-can-extend-and-renew"></a>Kdo může rozšířit a obnovit?

Pouze správci prostředku mohou rozšířit nebo obnovit přiřazení rolí. Ovlivněné člen může požadavek na rozšíření role, které se chystáte vyprší a žádat o obnovení role, které jsou již vypršela platnost.

## <a name="when-are-notifications-sent"></a>Když se odešlou oznámení?

PIM odešle e-mailová oznámení pro správce a ovlivněné členy rolí, které jsou vyprší během 14 dnů a jeden den před vypršením platnosti. Přiřazení oficiálně vyprší odešle další e-mailu. 

Správci přijímat upozornění, když členem role kterým vyprší platnost nebo vypršela její platnost žádosti o na rozšířit nebo obnovení. Po vyřešení určitého správce požadavku ostatními správci oznámeno rozhodnutí řešení (schváleny nebo odmítnuty). Vyžádání člen je pak informováni o rozhodnutí. 

## <a name="extend-role-assignments"></a>Rozšíření přiřazení rolí

Následující kroky popisují proces požaduje, řešení nebo jejich správě rozšíření nebo obnovení přiřazení role. 

### <a name="member-extend"></a>Člen rozšíření

Členy přiřazení rolí můžete rozšířit, kterým vyprší platnost přiřazení rolí přímo z **vhodné** nebo **Active** na kartě **Moje role** stránky prostředku a z nejvyšší úrovně **Moje role** stránce portálu PIM. Členy můžete požádat o prodloužení oprávněné a aktivní (přiřazené) role, jejichž platnost skončí za další 14 dnů.

![Rozšíření role](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Po přiřazení koncové datum a čas během 14 dnů, na tlačítko **rozšíření** stane aktivní odkaz v uživatelském rozhraní. V následujícím příkladu se předpokládá, aktuální datum následuje dne 27.

![Tlačítko "Rozšíření"](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Chcete-li požádat o prodloužení přiřazení této role, vyberte **rozšíření** otevřete formulář požadavku.

![Otevřete formulář žádosti o](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Chcete-li zobrazit informace o původní přiřazení, rozbalte **podrobnosti o přiřazení**. Zadejte důvod pro žádost o rozšíření a potom vyberte **rozšíření**.

>[!Note]
>Doporučujeme vám, včetně podrobnosti, proč je potřebná rozšíření a jak dlouho by měla být poskytnuta rozšíření (Pokud máte tyto informace).

![Rozšíření přiřazení role](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

V několika situacích správci prostředků dostávat e-mailové oznámení, vyžaduje, že si žádost rozšíření. Pokud již byla odeslána žádost o rozšíření, zobrazí se v horní části portálu Azure vysvětlením chyba oznámení s informační zprávou.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Přejděte na **nevyřízené žádosti o** kartě v levém podokně zobrazit stav žádosti nebo ji zrušte.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Schválení správce

Když členem odešle požadavek na rozšíření přiřazení role, zobrazí se správci prostředků e-mailové oznámení, který obsahuje podrobnosti o původní přiřazení a důvod žádosti. Oznámení obsahuje přímý odkaz na žádost o správce schválí nebo zamítne. 

Kromě používání následujících odkaz z e-mailu, můžete schválit nebo odmítnout požadavky, tak, že přejdete do správy PIM portálu a vyberete správci **schvalovat žádosti o** v levém podokně.

![Snímek obrazovky chyby](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Když správce vybere **schválit** nebo **Odepřít**, jsou uvedeny podrobnosti žádosti, společně s pole do uveďte její odůvodnění protokoly auditu.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Po schválení žádosti o rozšíření přiřazení role, prostředků mohou správci nové počáteční datum, koncové datum a typ přiřazení. Změna přiřazení typu může být nutné v případě, že správce chce omezený přístup k dokončení konkrétních úkolů (jeden den, např.). V tomto příkladu může správce změnit přiřazení z **vhodné** k **Active**. To znamená, že se může poskytnout přístup k žadatel bez nutnosti jejich aktivovat.

### <a name="admin-extend"></a>Rozšíření správce

Pokud člena role zapomene nebo nemůže požádat o příponu členství role, správce můžete rozšířit přiřazení jménem člena. Správce rozšíření členství role nevyžadují schválení, ale oznámení se odesílají na všechny ostatní správce po rozšířilo roli.

Pokud chcete rozšířit členství role, přejděte do zobrazení zdrojů role nebo člen v PIM. Najít člena, který vyžaduje rozšíření. Potom vyberte **rozšíření** ve sloupci Akce.

![Rozšíření členství role](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Obnovit přiřazení rolí

Při koncepčně podobá procesu pro požaduje rozšíření, proces obnovení přiřazení role s vypršenou platností se liší. Pomocí následujících kroků, můžou členové a správci obnovit přístup k vypršela platnost role, pokud je to nezbytné.

### <a name="member-renew"></a>Obnovení člena

Členové, kteří už přístup k prostředkům, mají přístup k až 30 dnů od historie vypršela platnost přiřazení. K tomu, že vyhledejte **Moje role** v levém podokně a potom vyberte **platnost role** kartě v části role prostředků Azure.

![Na kartě "Prošlé role"](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

Seznam rolí, zobrazí se výchozí hodnota je **vhodné role**. Použijte rozevírací nabídky k přepnutí mezi způsobilých a aktivní přiřazené role.

Chcete-li požádat o obnovení pro všechny přiřazení rolí v seznamu, vyberte **obnovení** akce. Zadejte důvod pro požadavek. Je vhodné zajistit dobu trvání kromě žádné další kontext, který pomáhá správce prostředků se rozhodnete schválí nebo zamítne.

![Obnovit přiřazení role](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Po odeslání žádosti správci prostředků oznámí čekající žádosti o obnovení přiřazení role.

### <a name="admin-approves"></a>Schválí správce

Správci prostředků můžete žádost o obnovení přístup pomocí odkazu v e-mailových oznámení nebo přístup k PIM z portálu Azure a vybrat **schvalovat žádosti o** v levém podokně.

![Schválit žádosti](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Když správce vybere **schválit** nebo **Odepřít**, jsou uvedeny podrobnosti požadavku společně s pole zadávat odůvodnění pro protokoly auditu.

![Schválit přiřazení role](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Po schválení žádosti o obnovení přiřazení role, musíte zadat správci prostředků nové počáteční datum, koncové datum a typ přiřazení. 

### <a name="admin-renew"></a>Obnovení správce

Správci prostředků můžete obnovit přiřazení role s vypršenou platností ze **členy** kartě v levé navigační nabídce prostředku. Přiřazení vypršela platnost rolí v nástroji se můžete obnovit také **platnost vypršela** kartu role role prostředků.

Chcete-li zobrazit seznam všech v platnost přiřazení rolí **členy** obrazovku, vyberte **platnost role**.

![Role, kterým vypršela platnost](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Další postup

[Vyžadovat schválení aktivovat](pim-resource-roles-approval-workflow.md)

[Aktivovat roli](pim-resource-roles-use-the-audit-log.md)


