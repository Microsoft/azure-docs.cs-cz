---
title: Funkce role Azure AD v Privileged Identity Management | Microsoft Docs
description: Správa rolí Azure AD pro přiřazování Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4478c9c286c06d5d6c5593195a0e93abd286b8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371507"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Možnosti správy pro role Azure AD v Privileged Identity Management

Prostředí pro správu rolí služby Azure AD v Privileged Identity Management bylo aktualizováno, aby bylo sjednoceno, jak se spravují role Azure AD a role prostředků Azure. Dřív Privileged Identity Management pro role prostředků Azure obsahoval několik klíčových funkcí, které pro role Azure AD nebyly k dispozici.

Při současném uvedení aktualizace se tyto dva sloučí do jediného prostředí pro správu. v takovém případě získáte stejné funkce pro role Azure AD jako pro role prostředků Azure. Tento článek vás informuje o aktualizovaných funkcích a všech požadavcích.

## <a name="time-bound-assignments"></a>Přiřazení s časovou vazbou

Dříve existovaly dva možné stavy pro přiřazení rolí: *oprávněná* a *trvalá*. Nyní můžete také nastavit počáteční a koncový čas pro každý typ přiřazení. Tento dodatek vám poskytne čtyři možné stavy, do kterých můžete umístit přiřazení:

- Oprávněné trvalé
- Aktivní trvale
- Způsobilý pro přiřazení se zadaným počátečním a koncovým datem
- Aktivní se zadaným počátečním a koncovým datem pro přiřazení

V mnoha případech i v případě, že nechcete, aby uživatelé měli k dispozici oprávněné přiřazení a aktivovat role pokaždé, můžete chránit svoji organizaci Azure AD nastavením času vypršení platnosti přiřazení. Pokud máte například nějaké dočasné uživatele, kteří mají nárok, zvažte nastavení vypršení platnosti, aby se po dokončení jejich práce automaticky odebraly z přiřazení role.

## <a name="new-role-settings"></a>Nové nastavení role

Také přidáváme nová nastavení pro role Azure AD.

- **Dřív** jste mohli nakonfigurovat nastavení aktivace jenom u jednotlivých rolí. To znamená, že nastavení aktivace jako požadavky na vícefaktorové ověřování a požadavky lístku incidentu nebo žádosti byly aplikovány na všechny uživatele s nárokem na zadanou roli.
- **Nyní** můžete nakonfigurovat, zda má jednotliví uživatelé provádět službu Multi-Factor Authentication před tím, než budou moci aktivovat roli. Navíc můžete mít pokročilou kontrolu nad Privileged Identity Management e-maily, které souvisejí s konkrétními rolemi.

## <a name="extend-and-renew-assignments"></a>Prodloužit a prodloužit přiřazení

Jakmile napíšete přiřazení s časovou vazbou, bude se první otázka, kterou můžete zeptat, dělat, co se stane, když vypršela platnost role? V této nové verzi poskytujeme dvě možnosti pro tento scénář:

- **Rozšíření**: když se přiřazení role blíží k vypršení platnosti, může uživatel použít Privileged Identity Management k vyžádání rozšíření pro přiřazení role.
- **Prodloužit**: Pokud vypršela platnost přiřazení role, uživatel může použít Privileged Identity Management k vyžádání obnovení pro přiřazení role.

Akce iniciované uživatelem vyžadují schválení od správce globálního správce nebo privilegované role. Správci už nebudou muset být v podnikání v rámci správy těchto vypršení platnosti. Jenom potřebují počkat na rozšíření nebo žádosti o obnovení a schválit je, pokud je žádost platná.

## <a name="api-changes"></a>Změny rozhraní API

Když zákazníci mají aktualizovanou verzi, která je zahrnutá ve své organizaci Azure AD, existující rozhraní Graph API přestane fungovat. Abyste mohli použít [Graph API pro role prostředků Azure](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta), musíte přejít. Pokud chcete spravovat role Azure AD pomocí tohoto rozhraní API, Proměňte ho `/azureResources` `/aadroles` v signatuře a použijte ID adresáře pro `resourceId` .

Snažili jsme se co nejlépe kontaktovat všem zákazníkům, kteří používají předchozí rozhraní API, a informovat o této změně ještě před časem. Pokud se vaše organizace Azure AD přesunula na novou verzi a pořád závisí na starém rozhraní API, obraťte se na tým na adrese pim_preview@microsoft.com .

## <a name="powershell-change"></a>Změna prostředí PowerShell

Pro zákazníky, kteří používají modul Privileged Identity Management PowerShellu pro role Azure AD, přestane PowerShell pracovat s aktualizací. Místo předchozích rutin je nutné použít rutiny Privileged Identity Management v modulu Azure AD Preview PowerShell. Nainstalujte modul Azure AD PowerShell z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Teď si můžete [přečíst dokumentaci a ukázky pro operace PIM v tomto modulu PowerShellu](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Další kroky

- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
- [Definice rolí v Azure AD](../roles/permissions-reference.md)