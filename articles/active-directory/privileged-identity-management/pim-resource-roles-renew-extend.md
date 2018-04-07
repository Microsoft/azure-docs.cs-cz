---
title: Privileged Identity Management pro prostředky Azure - rozšíření a obnovení role | Microsoft Docs
description: Tento dokument popisuje, jak rozšířit a obnovit role prostředků Azure pro prostředky PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Správa privilegovaných identit - role prostředků - rozšířit nebo obnovení

Pro prostředky Azure PIM zavádí nové ovládací prvky pro správu životního cyklu přístup a přiřazení k prostředkům Azure. Správci mohou přiřadit členství pomocí vlastnosti počáteční a koncové datum a čas. Když se blíží konec přiřazování, PIM odešle e-mailová oznámení ovlivněných členů (to může být uživatel nebo skupina) a správcům prostředku zajistit odpovídající přístup je možný. V případě, že přístup není rozšířeno z důvodu nečinnosti, přiřazení může být obnovena a zůstanou viditelné v stav vypršené platnosti po dobu 30 dnů.

## <a name="who-can-extend-and-renew"></a>Kdo může rozšířit a obnovit?

Pouze správci prostředku mohou rozšířit nebo obnovit přiřazení rolí. Ovlivněné člen můžete požádat o prodloužení role vyprší a žádost o obnovení role již vypršela platnost.

## <a name="when-are-notifications-sent"></a>Když se odešlou oznámení?

PIM odešle e-mailová oznámení pro správce a ovlivněné členy rolí vyprší během 14 dnů a jeden den před vypršením platnosti. Další e-mail je odeslán, při přiřazování oficiálně vypršela. 

Správci přijímat upozornění, když členem role kterým vyprší platnost nebo vypršela její platnost žádosti o na rozšířit nebo obnovení. Po vyřešení určitého správce požadavku ostatními správci oznámeno rozhodnutí řešení (schváleny nebo odmítnuty) a žádajícího člen je upozornění rozhodnutí. 

## <a name="extend-role-assignments"></a>Rozšíření přiřazení rolí

Následující kroky popisují kroky a uživatelské rozhraní účastnící se požaduje, řešení nebo jejich správě rozšíření nebo obnovení přiřazení role. 

### <a name="member-extend"></a>Člen rozšíření

Členové přiřazení role můžete požádat o prodloužení kterým vyprší platnost přiřazení rolí přímo z "Způsobilých" nebo "Aktivní" kartě na "Role" stránka Moje role portálu PIM prostředku a z nejvyšší úrovně. Členy můžete požádat o prodloužení oprávněné a aktivní (přiřazené) role, jejichž platnost skončí za další 14 dnů.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Po přiřazení koncové datum a čas do 14 dnů se na tlačítko "Rozšíření" stane aktivní odkaz v uživatelském rozhraní. V následujícím příkladu předpokládejme aktuální datum následuje dne 27.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Chcete-li požádat o prodloužení této role přiřazení, klikněte na tlačítko "Rozšíření" otevřete formulář požadavku.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Rozbalte položku "podrobnosti o přiřazení" Chcete-li zobrazit informace o původní přiřazení. Zadejte důvod pro žádost o rozšíření a klepněte na možnost "Rozšířit".

>[!Note]
>Doporučujeme vám, včetně podrobnosti, proč je potřebná rozšíření a jak dlouho rozšíření by měl být (Pokud je znám).

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

Správci prostředků v několika situacích, obdrží e-mail s oznámením požadování že jejich zkontrolujte žádost rozšíření. Pokud již byla odeslána žádost o rozšíření, zobrazí se v horní části portálu Azure vysvětlením chyba oznámení s informační zprávou.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Přejděte na kartu "čekající požadavky" v levé navigační nabídce zobrazení stav nebo zrušte žádost.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Schválení správce

Když členem odešle požadavek na rozšíření přiřazení role, zobrazí se správci prostředků e-mailové oznámení obsahující podrobnosti o původní přiřazení a z důvodu poskytne žadatel. Oznámení obsahuje přímý odkaz na žádost o správce schválí nebo zamítne. 

Správci kromě následujících odkaz z e-mailu, můžete schválit nebo odmítnout požadavky, přejdete na portálu pro správu PIM a výběrem "Schválit požadavky" v levé navigační nabídce.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Pokud správce vybere možnost schválit nebo odepřít jsou uvedeny podrobnosti požadavku společně s pole zadávat odůvodnění pro protokoly auditu.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Při schválení požadavek na rozšíření přiřazení role, správci prostředků můžete zvolit nové počáteční a koncové datum a čas a přiřazení typu. Změna přiřazení typu může být nutné v případě, že správce chce omezený přístup k dokončení konkrétních úkolů (například jeden den). V tomto příkladu správce může změnit přiřazení z nárok na aktivní, poskytuje přístup žadatele bez nutnosti jejich aktivovat.

### <a name="admin-extend"></a>Správce rozšíření

V případě člena role zapomene nebo nemůže požádat o rozšíření členství role Správce může rozšířit přiřazení jménem člena. Správce rozšíření členství role nevyžadují schválení ale oznámení se odesílají na všechny ostatní správce po dokončení rozšíření role.

K rozšíření role členství přejděte do zobrazení prostředků role nebo člen v PIM. Find – člen, které vyžadují rozšíření a klikněte na tlačítko "Rozšíření" ve sloupci Akce.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Obnovit přiřazení rolí

Při koncepčně podobá procesu obnovení přiřazení role s vypršenou platností se liší od požaduje rozšíření pro členy a správce. Pomocí těchto kroků členy a správci můžou obnovit přístup k vypršela platnost role, pokud je to nezbytné.

### <a name="member-renew"></a>Člen obnovení

Členové, kteří už přístup k prostředkům, můžete přejít až 30 dnů od historie vypršela platnost přiřazení pomocí přejdete na Moje role v levé navigaci PIM a výběrem karty "Prošlé role" v části role prostředků Azure.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

Seznam zobrazí výchozí hodnota je vhodné přiřazení role. Pomocí rozevíracího seznamu k přepnutí mezi způsobilých a aktivní přiřazené role.

Požádá o obnovení pro všechny role přiřazení v seznamu vyberte akci, která "Obnovení" a uveďte důvod pro požadavek. Je vhodné zajistit dobu trvání kromě žádné další kontext, který vám pomůže rozhodnout, ke schvalování nebo zamítání správce prostředků.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Po odeslání žádosti o Správci prostředků oznámí čekající žádosti o obnovení přiřazení role.

### <a name="admin-approves"></a>Schválí správce

Správci prostředků můžete otevřít žádost o obnovení z odkazu v e-mailových oznámení nebo z portálu Auzre přístup k PIM a výběrem "Schválit požadavky" v levé navigační nabídce.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Pokud správce vybere možnost schválit nebo odepřít jsou uvedeny podrobnosti požadavku společně s pole zadávat odůvodnění pro protokoly auditu.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Po schválení žádosti o obnovení přiřazení role, musí správce prostředků zadejte počáteční a koncové datum a čas a přiřazení typu new. 

### <a name="admin-renew"></a>Správce obnovení

Správci prostředků můžete obnovit přiřazení rolí vypršela platnost z karty členy v levé navigační nabídce prostředku nebo v kartě platnost vypršela rolí z role prostředků.

Na obrazovce členy vyberte role platnost vypršela, chcete-li zobrazit seznam všech přiřazení rolí vypršela platnost.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Další postup

[Vyžadovat schválení aktivovat](pim-resource-roles-approval-workflow.md)

[Aktivovat roli](pim-resource-roles-use-the-audit-log.md)


