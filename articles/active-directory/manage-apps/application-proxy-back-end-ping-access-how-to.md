---
title: Jak nakonfigurovat aplikaci proxy aplikací tak, aby používala PingAccess
description: Naučte se používat PingAccess k rozšiřování výhod aplikačního proxy serveru na aplikace pomocí ověřování založeného na hlavičkách.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cfbca2e7542315b59d741a9e8f6f9eb92af6ebf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84760214"
---
# <a name="how-to-configure-an-application-proxy-application-to-use-pingaccess"></a>Jak nakonfigurovat aplikaci proxy aplikací pro použití PingAccess

Naše spolupráce s PingAccess teď umožňuje rozšiřování výhod aplikačního proxy serveru do aplikací pomocí ověřování založeného na hlavičkách. Pokud vaše aplikace nepoužívají hlavičky, další informace o dalších možnostech najdete v naší [dokumentaci k jednomu Sign-On](application-proxy-configure-single-sign-on-with-kcd.md) .

## <a name="overview-of-steps-and-recommended-documents"></a>Přehled kroků a doporučených dokumentů

Ke konfiguraci aplikace pomocí PingAccess existují čtyři kroky:

1.  Konfigurace konektorů proxy aplikací

2.  Vytvoření aplikace Azure Proxy aplikací služby AD

3.  Stažení & konfigurace PingAccess

4.  Konfigurace aplikací v PingAccess

Podrobnosti o každém z těchto kroků najdete v naší [Sign-On dokumentaci k hlavičkám](application-proxy-configure-single-sign-on-with-ping-access.md).
