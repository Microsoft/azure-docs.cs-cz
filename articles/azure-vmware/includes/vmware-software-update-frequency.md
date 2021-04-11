---
title: Frekvence aktualizace softwaru VMware
description: Podporovaná frekvence aktualizace softwaru VMware pro řešení Azure VMware.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869858"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Společnost Microsoft zodpovídá za správu životního cyklu softwaru VMware (ESXi, vCenter, PSC a NXS) v privátním cloudu řešení Azure VMware.

Software privátního cloudu se upgraduje podle plánu, který sleduje vydání softwarového balíčku od VMware. Váš privátní cloud nevyžaduje pro upgrady výpadky.

Upgrady sady prostředků privátního cloudu udržují software v rámci jedné verze nejnovější verze softwarového balíčku od VMware. Verze privátního cloudového softwaru se mohou lišit od nejaktuálnější verze jednotlivých softwarových komponent (ESXi, NSX-T, vCenter, síti vSAN).

Mezi aktualizace softwaru patří:

- **Opravy** – opravy zabezpečení nebo opravy chyb vydané VMware
- **Aktualizace** – změna dílčí verze komponenty zásobníku VMware
- **Upgrady** – Změna hlavní verze komponenty zásobníku VMware

Společnost Microsoft testuje kritickou opravu zabezpečení, jakmile bude dostupná z VMware.

Zdokumentováná alternativní řešení VMware se implementují místo instalace odpovídající opravy, dokud nebudou nasazené další plánované aktualizace. 
