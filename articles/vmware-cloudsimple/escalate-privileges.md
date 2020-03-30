---
title: Řešení Azure VMware od CloudSimple – eskalovat oprávnění CloudSimple
description: Popisuje, jak eskalovat oprávnění CloudSimple k provádění administrativních funkcí v programu Private Cloud vCenter.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025329"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Eskalovat oprávnění CloudSimple k provádění administrativních funkcí v programu Private Cloud vCenter

Přístup oprávnění CloudSimple je navržen tak, aby uživatelům vCenter oprávnění, které potřebují k provádění běžných operací. V některých případech může uživatel vyžadovat další oprávnění k provedení určitého úkolu.  Oprávnění uživatele s přistupuje k virtuálnímu centru vcentru na omezenou dobu.

Důvody pro eskalaci oprávnění mohou zahrnovat následující:

* Konfigurace zdrojů identity
* Správa uživatelů
* Odstranění distribuované skupiny portů
* Instalace řešení vCenter (například zálohovacích aplikací)
* Vytváření účtů služeb

> [!WARNING]
> Akce provedené v eskalovaném privilegovaném stavu mohou nepříznivě ovlivnit váš systém a způsobit, že systém přestane být k dispozici. Během období eskalace proveďte pouze nezbytné akce.

Z portálu CloudSimple [eskalovat oprávnění](escalate-private-cloud-privileges.md) pro místního uživatele CloudOwner na vCenter přiřazování služeb přimicrosoft.  Oprávnění vzdáleného uživatele můžete eskalovat pouze v případě, že je v programu vCenter nakonfigurován další poskytovatel identity.  Eskalace oprávnění zahrnuje přidání vybraného uživatele do předdefinované skupiny Administrators vSphere.  Eskalovaná oprávnění může mít pouze jeden uživatel.  Pokud potřebujete eskalovat oprávnění jiného uživatele, nejprve deeskalovat oprávnění aktuálních uživatelů.

Uživatelé z dalších zdrojů identity musí být přidáni jako členové skupiny CloudOwner.

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze do *skupiny Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* *nebo, Cloud-Global-VM-Admin-Group*.  Uživatelé připřidání do *skupiny Administrators* budou automaticky odebráni.  K přihlášení k webovému uživatelskému rozhraní vSphere je nutné přidat pouze účty služeb a účty služeb *administrators* a účty služeb.

Během období eskalace CloudSimple používá automatizované monitorování s přidruženými oznámeními výstrah k identifikaci neúmyslných změn v prostředí.
