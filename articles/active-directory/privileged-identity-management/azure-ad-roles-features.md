---
title: Správa rolí Azure AD v privilegované správě identit (PIM) | Dokumenty společnosti Microsoft
description: Jak spravovat role Azure AD pro přiřazení privilegované správy identit (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245977"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Možnosti správy pro role Azure AD v privilegované správě identit

Prostředí pro správu rolí Azure AD ve správě privilegovaných identit bylo aktualizováno, aby se sjednocovalzpůsob správy rolí Azure AD a rolí prostředků Azure. Dříve privilegované identity správy pro role prostředků Azure má několik klíčových funkcí, které nebyly k dispozici pro role Azure AD.

S aktualizací, která je aktuálně zavedena, slučujeme je do jednoho prostředí pro správu a v něm získáte stejné funkce pro role Azure AD jako pro role prostředků Azure. Tento článek vás informuje o aktualizovaných funkcích a všech požadavcích.


## <a name="time-bound-assignments"></a>Časově ohraničená přiřazení

Dříve v privileged identity management pro role Azure AD, jste byli obeznámeni s přiřazení rolí se dvěma možnými stavy – *způsobilé* a *trvalé*. Nyní můžete nastavit počáteční a koncový čas pro každý typ přiřazení. Toto přidání poskytuje čtyři možné stavy, ve kterých můžete umístit přiřazení:

- Způsobilé trvale
- Aktivní trvale
- Způsobilé, se zadanými počátečními a koncovými daty pro přiřazení
- Aktivní, se zadanými počátečními a koncovými daty pro přiřazení

V mnoha případech i v případě, že nechcete, aby uživatelé měli způsobilé přiřazení a aktivovat role pokaždé, můžete stále chránit svou organizaci Azure AD nastavením doby vypršení platnosti přiřazení. Pokud máte například některé dočasné uživatele, kteří mají nárok, zvažte nastavení vypršení platnosti tak, aby byly automaticky odebrány z přiřazení role po dokončení jejich práce.

## <a name="new-role-settings"></a>Nové nastavení role

Přidáváme také nové nastavení pro role Azure AD. Dříve bylo možné konfigurovat nastavení aktivace pouze pro na základě rolí. To znamená, že nastavení aktivace, jako jsou požadavky na vícefaktorové ověřování a požadavky na lístek incidentů/požadavků na požadavky na incidenty a požadavky na lístek požadavku, byly použity pro všechny uživatele způsobilé pro určitou roli. Nyní můžete nakonfigurovat, zda je třeba, aby jednotliví uživatelé před aktivací role provedli vícefaktorové ověřování. Můžete mít také rozšířenou kontrolu nad e-maily správy privilegovaných identit souvisejících s konkrétními rolemi.

## <a name="extend-and-renew-assignments"></a>Rozšíření a obnovení přiřazení

Jakmile zjistíte časově vázaný úkol, první otázka, kterou si můžete položit, je, co se stane, když vyprší platnost role? V této nové verzi poskytujeme dvě možnosti pro tento scénář:

- Rozšířit – když se přiřazení role blíží vypršení platnosti, může uživatel pomocí správy privilegovaných identit požádat o rozšíření pro toto přiřazení role.
- Obnovit – po vypršení platnosti přiřazení role může uživatel pomocí správy privilegovaných identit požádat o obnovení pro toto přiřazení role.

Obě akce iniciované uživatelem vyžadují schválení od globálního správce nebo správce privilegovaných rolí. Správci již nebudou muset spravovat tyto vypršení platnosti. Stačí počkat na žádosti o prodloužení nebo obnovení a schválit je, pokud je žádost platná.

## <a name="api-changes"></a>Změny rozhraní API

Když zákazníci mají aktualizovanou verzi zavedenou do své organizace Azure AD, existující rozhraní API grafu přestane fungovat. Chcete-li použít [rozhraní GRAPH API pro role prostředků Azure](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta), musíte přejít. Chcete-li spravovat role Azure AD `/aadroles` pomocí tohoto rozhraní API, swap `resourceId` `/azureResources` s v podpisu a použít ID adresáře pro .

Snažili jsme se co nejlépe oslovit všechny zákazníky, kteří používají předchozí rozhraní API, aby chomáč o této změně předem. Pokud vaše organizace Azure AD byla přesunuta na novou verzi a stále závisí pim_preview@microsoft.comna staré rozhraní API, oslovit tým na .

## <a name="powershell-change"></a>Změna prostředí PowerShell

Pro zákazníky, kteří používají modul PowerShell správy privilegovaných identit pro role Azure AD, PowerShell přestane pracovat s aktualizací. Místo předchozích rutin je nutné použít rutiny správy privilegovaných identit v modulu PowerShell Azure AD Preview. Nainstalujte modul Azure AD PowerShell z [Galerie PowerShellu](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Nyní si můžete [přečíst dokumentaci a ukázky pro operace PIM v tomto modulu Prostředí PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Další kroky

- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
- [Definice rolí ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
