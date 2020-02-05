---
title: Řešení Azure VMware (AVS) – zvýšení oprávnění služby AVS
description: Popisuje, jak eskalovat oprávnění funkce AVS k provádění funkcí správy v rámci nástroje pro správu privátního cloudu s funkcí AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025329"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>Eskalace oprávnění ke všem funkcím pro správu v nesdílené privátní cloudové vCenter v systému AVS

Přístup k oprávněním k funkci AVS je navržený tak, aby uživatelům vCenter poskytovala oprávnění, která potřebují k provádění běžných operací. V některých případech může uživatel vyžadovat další oprávnění k provedení konkrétního úkolu. Po omezené období můžete zvýšit oprávnění uživatele pro jednotné přihlašování (SSO) vCenter.

Důvody pro eskalaci oprávnění mohou zahrnovat následující:

* Konfigurace zdrojů identity
* Správa uživatelů
* Odstranění skupiny distribuovaných portů
* Instalace řešení vCenter (například zálohovací aplikace)
* Vytváření účtů služeb

> [!WARNING]
> Akce provedené ve zvýšení privilegovaného privilegovaného stavu můžou negativně ovlivnit váš systém a můžou způsobit, že váš systém nebude dostupný. Provedení pouze nezbytných akcí během období eskalace.

Na portálu služby AVS zapněte [oprávnění](escalate-private-cloud-privileges.md) pro místního uživatele CloudOwner na serveru vCenter SSO. Oprávnění vzdáleného uživatele můžete eskalovat jenom v případě, že je na vCenter nakonfigurovaný další zprostředkovatel identity. Eskalace oprávnění zahrnuje přidání vybraného uživatele do předdefinované skupiny Administrators vSphere. Pouze jeden uživatel může mít povýšené oprávnění. Pokud potřebujete povýšit oprávnění jiného uživatele, nejprve zrušte eskalaci oprávnění aktuálních uživatelů.

Uživatele z dalších zdrojů identity musí být přidáni jako členové skupiny CloudOwner.

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze *ke skupině*Cloud-Owner-Group *-Global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin* -Group nebo, *Cloud-Global-VM-admin-Group*.  Uživatelé přidaní do skupiny *správců* se automaticky odeberou.  Pouze účty služeb musí být přidány do skupiny *Administrators* a účty služeb nesmí být použity pro přihlášení k WEBOVÉmu uživatelskému rozhraní vSphere.
Během období eskalace používá aplikace AVS automatizované monitorování s přidruženými oznámeními výstrah k identifikaci neúmyslných změn v prostředí.
