---
title: Jak nakonfigurovat aplikaci proxy aplikací tak, aby používala PingAccess
description: Naučte se používat PingAccess k rozšiřování výhod aplikačního proxy serveru na aplikace pomocí ověřování založeného na hlavičkách.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c852c69b6f64b59845b551202bd1a25646a49eb6
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275660"
---
# <a name="how-to-configure-an-application-proxy-application-to-use-pingaccess"></a>Jak nakonfigurovat aplikaci proxy aplikací pro použití PingAccess

Naše spolupráce s PingAccess teď umožňuje rozšiřování výhod aplikačního proxy serveru do aplikací pomocí ověřování založeného na hlavičkách. Pokud vaše aplikace nepoužívají hlavičky, přečtěte si v [dokumentaci k jednotnému přihlašování](application-proxy-configure-single-sign-on-with-kcd.md) podrobnosti o dalších možnostech.

## <a name="overview-of-steps-and-recommended-documents"></a>Přehled kroků a doporučených dokumentů

Ke konfiguraci aplikace pomocí PingAccess existují čtyři kroky:

1.  Konfigurace konektorů proxy aplikací

2.  Vytvoření aplikace Azure Proxy aplikací služby AD

3.  Stažení & konfigurace PingAccess

4.  Konfigurace aplikací v PingAccess

Podrobnosti o každém z těchto kroků najdete v našem [jednotném přihlašování s hlavičkou](application-proxy-configure-single-sign-on-with-ping-access.md).
