---
title: Spravované místní role RBAC místních RBAC – Azure Key Vault | Microsoft Docs
description: Přehled spravovaných předdefinovaných rolí HSM, které je možné přiřadit uživatelům, instančním objektům, skupinám a spravovaným identitám
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 01e96922d9c0c47eaf4d430e92eafcd9d0964e13
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557220"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Předdefinované role RBAC místního spravovaného HSM

Spravovaná místní RBAC v modulu HSM má několik předdefinovaných rolí. Tyto role můžete přiřadit uživatelům, objektům služby, skupinám a spravovaným identitám. Pokud chcete, aby objekt zabezpečení mohl provést operaci, musíte jim přiřadit roli, která jim uděluje oprávnění k provádění těchto operací. Všechny tyto role a operace umožňují spravovat pouze oprávnění k operacím roviny dat. Pokud chcete spravovat oprávnění řídicí plochy pro spravovaný prostředek HSM, musíte použít [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/overview.md). Některé příklady operací řízení roviny představují vytvoření nového spravovaného HSM nebo aktualizace, přesunutí a odstranění.

## <a name="built-in-roles"></a>Vestavěné role

|Název role|Popis|ID|
|---|---|---|
|Spravovaný správce HSM| Uděluje oprávnění k provádění všech operací souvisejících s doménou zabezpečení, úplným zálohováním a obnovením a správou rolí. Nepovoluje se provádět žádné operace správy klíčů.|a290e904-7015-4bba-90c8-60543313cdb4|
|Spravovaný pracovník kryptografických modulů HSM|Uděluje oprávnění provádět všechny úlohy správy rolí, vyprázdnit nebo obnovit odstraněné klíče a exportovat klíče. Nepovoluje se provádět žádné jiné operace správy klíčů.|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Spravovaný kryptografický uživatel HSM|Uděluje oprávnění k provádění všech operací správy klíčů s výjimkou vymazání nebo obnovení odstraněných klíčů a exportu klíčů.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Správce zásad spravovaného HSM| Uděluje oprávnění k vytváření a odstraňování přiřazení rolí.|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Spravovaný auditor kryptografie HSM|Uděluje oprávnění ke čtení (ale nepoužívá) atributům klíče.|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Spravované šifrování šifrovací služby HSM| Uděluje oprávnění používat klíč pro šifrování služby. |33413926-3206-4cdd-b39a-83574fe37a17|
|Spravované zálohování HSM| Uděluje oprávnění k provádění jediného klíče nebo úplného zálohování HSM.|7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Povolené operace
> [!NOTE]  
> - Znak X označuje, že role může provádět akce s daty. Prázdná buňka označuje, že role nemá pemission k provedení těchto dat.
> - Všechny názvy akcí dat mají předponu Microsoft. zkrácení trezor/managedHsm, která je vynechána v tabulkách níže pro.
> - Všechny názvy rolí mají předponu "spravovaný modul HSM", který se v níže uvedené tabulce pro zkrácení vynechá.

|Akce s daty | Správce | Kryptografický pracovník | Kryptografický uživatel | Správce zásad | Šifrování šifrovací služby | Backup | Auditor kryptografie|
|---|---|---|---|---|---|---|---|
|**Správa domén zabezpečení**|
/securitydomain/download/action|<center>Znak</center>||||||
/securitydomain/upload/action|<center>Znak</center>||||||
/securitydomain/upload/read|<center>Znak</center>||||||
/securitydomain/transferkey/read|<center>Znak</center>||||||
|**Správa klíčů**|
|/keys/read/action|||<center>Znak</center>||<center>Znak</center>||<center>Znak</center>|
|/keys/write/action|||<center>Znak</center>||||
|/keys/create|||<center>Znak</center>||||
|/keys/delete|||<center>Znak</center>||||
|/keys/deletedKeys/read/action||<center>Znak</center>|||||
|/keys/deletedKeys/recover/action||<center>Znak</center>|||||
|/keys/deletedKeys/delete||<center>Znak</center>|||||<center>Znak</center>|
|/keys/backup/action|||<center>Znak</center>|||<center>Znak</center>|
|/keys/restore/action|||<center>Znak</center>||||
|/keys/export/action||<center>Znak</center>|||||
|/keys/release/action|||<center>Znak</center>||||
|/keys/import/action|||<center>Znak</center>||||
|**Klíčové operace šifrování**|
|/keys/encrypt/action|||<center>Znak</center>||||
|/keys/decrypt/action|||<center>Znak</center>||||
|/keys/wrap/action|||<center>Znak</center>||<center>Znak</center>||
|/keys/unwrap/action|||<center>Znak</center>||<center>Znak</center>||
|/keys/sign/action|||<center>Znak</center>||||
|/keys/verify/action|||<center>Znak</center>||||
|**Správa rolí**|
|/roleAssignments/read/action|<center>Znak</center>|<center>Znak</center>|<center>Znak</center>|<center>Znak</center>|||<center>Znak</center>
|/roleAssignments/write/action|<center>Znak</center>|<center>Znak</center>||<center>Znak</center>|||
|/roleAssignments/delete/action|<center>Znak</center>|<center>Znak</center>||<center>Znak</center>|||
|/roleDefinitions/read/action|<center>Znak</center>|<center>Znak</center>|<center>Znak</center>|<center>Znak</center>|||<center>Znak</center>
|/roleDefinitions/write/action|<center>Znak</center>|<center>Znak</center>||<center>Znak</center>|||
|/roleDefinitions/delete/action|<center>Znak</center>|<center>Znak</center>||<center>Znak</center>|||
|**Správa zálohování a obnovení**|
|/backup/start/action|<center>Znak</center>|||||<center>Znak</center>|
|/backup/status/action|<center>Znak</center>|||||<center>Znak</center>|
|/restore/start/action|<center>Znak</center>||||||
|/restore/status/action|<center>Znak</center>||||||
||||||||

## <a name="next-steps"></a>Další kroky

- Podívejte se na přehled [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md).
- Projděte si kurz [spravované správy rolí HSM](role-management.md) .
