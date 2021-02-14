---
title: Získání koncových bodů pro registraci aplikace Azure AD
titleSuffix: Microsoft identity platform
description: Jak najít koncové body ověřování pro vlastní aplikaci, kterou vyvíjíte nebo zaregistrujete ve službě Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 778523869715916bf1e4c32af59ea7a0081df91b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103307"
---
# <a name="how-to-discover-endpoints"></a>Postup zjišťování koncových bodů

Koncové body ověřování pro vaši aplikaci najdete v [Azure Portal](https://portal.azure.com).

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací** a potom v horní nabídce vyberte **koncové body** .

    Zobrazí se stránka **koncové body** se zobrazením koncových bodů ověřování pro vašeho tenanta.
    
    Použijte koncový bod, který odpovídá ověřovacímu protokolu, který používáte ve spojení s **ID aplikace (klienta)** pro vytvoření žádosti o ověření specifické pro vaši aplikaci.

**Národní cloudy** (například Azure AD Čína, Německo a státní správa USA) mají vlastní portál pro registraci aplikací a koncové body ověřování Azure AD. Další informace najdete v článku [Přehled národních cloudů](authentication-national-cloud.md).

## <a name="next-steps"></a>Další kroky

Další informace o koncových bodech v různých prostředích Azure najdete v tématu [Přehled národních cloudů](authentication-national-cloud.md).
