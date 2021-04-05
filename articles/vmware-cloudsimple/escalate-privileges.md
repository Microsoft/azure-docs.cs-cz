---
title: Řešení Azure VMware podle CloudSimple – zvýšení oprávnění CloudSimple
description: Popisuje, jak eskalovat CloudSimple oprávnění k provádění funkcí správy v privátním cloudu vCenter.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895723"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Eskalace CloudSimple oprávnění k provádění funkcí správy v privátním cloudu vCenter

Přístup k oprávněním CloudSimple je navržený tak, aby uživatelům vCenter poskytovala oprávnění, která potřebují k provádění běžných operací. V některých případech může uživatel vyžadovat další oprávnění k provedení konkrétního úkolu.  Po omezené období můžete zvýšit oprávnění uživatele pro jednotné přihlašování (SSO) vCenter.

Důvody pro eskalaci oprávnění mohou zahrnovat následující:

* Konfigurace zdrojů identity
* Správa uživatelů
* Odstranění skupiny distribuovaných portů
* Instalace řešení vCenter (například zálohovací aplikace)
* Vytváření účtů služeb

> [!WARNING]
> Akce provedené ve zvýšení privilegovaného privilegovaného stavu můžou negativně ovlivnit váš systém a můžou způsobit, že váš systém nebude dostupný. Provedení pouze nezbytných akcí během období eskalace.

Z portálu CloudSimple můžete zvýšit [oprávnění](escalate-private-cloud-privileges.md) pro místního uživatele CloudOwner na serveru vCenter SSO.  Oprávnění vzdáleného uživatele můžete eskalovat jenom v případě, že je na vCenter nakonfigurovaný další zprostředkovatel identity.  Eskalace oprávnění zahrnuje přidání vybraného uživatele do předdefinované skupiny Administrators vSphere.  Pouze jeden uživatel může mít povýšené oprávnění.  Pokud potřebujete povýšit oprávnění jiného uživatele, nejprve zrušte eskalaci oprávnění aktuálních uživatelů.

Uživatele z dalších zdrojů identity musí být přidáni jako členové skupiny CloudOwner.

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze *ke skupině* Cloud-Owner-Group *-Global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin* -Group nebo, *Cloud-Global-VM-admin-Group*.  Uživatelé přidaní do skupiny *správců* se automaticky odeberou.  Pouze účty služeb musí být přidány do skupiny *Administrators* a účty služeb nesmí být použity pro přihlášení k WEBOVÉmu uživatelskému rozhraní vSphere.

Během období eskalace CloudSimple používá automatizované monitorování s přidruženými oznámeními výstrah k identifikaci neúmyslných změn v prostředí.
