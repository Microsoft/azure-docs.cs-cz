---
title: Ověřování Windows a Azure MFA Server | Dokumentace Microsoftu
description: Toto je stránka Azure Multi-Factor Authentication, která vám pomůže při nasazení ověření Windows a serveru Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 19a4043f-c4ce-43c0-80e7-2548ee92cb74
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: d7d0536c5504d559e8623083bfcd8c49832a8e48
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Ověření Windows a server Azure Multi-Factor Authentication
Část ověření Windows serveru Azure Multi-Factor Authentication umožňuje povolit a konfigurovat ověřování Windows pro aplikace. Před nastavením ověřování Windows mějte na paměti následující seznam:

* Po nastavení je nutné provést aktivaci restartováním Azure Multi-Factor Authentication pro Terminálové služby.
* Pokud je zaškrtnuto políčko „Vyžadovat shodu uživatele Azure Multi-Factor Authentication“ a nejste v seznamu uživatelů, nebudete se po restartu moci přihlásit do počítače.
* Důvěryhodné IP adresy jsou závislé na tom, zda aplikace může zajistit IP adresu klienta s ověřením. Momentálně jsou podporovány pouze terminálové služby.  

> [!NOTE]
> Tato funkce není podporována pro zabezpečené terminálové služby v systému Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Chcete-li zabezpečit aplikaci pomocí ověřování systému Windows, použijte následující postup.
1. Na serveru Azure Multi-Factor Authentication klikněte na ikonu ověřování systému Windows.
   ![Ověřování systému Windows](./media/howto-mfaserver-windows/windowsauth.png)
2. Zaškrtněte políčko **Povolit ověřování systému Windows**. Ve výchozím nastavení je toto políčko zaškrtnuté.
3. Karta aplikace umožňuje správci konfigurovat jednu nebo více aplikací pro ověřování systému Windows.
4. Vyberte server nebo aplikaci – určete, zda je povolen server/aplikace. Klikněte na **OK**.
5. Klikněte na **Přidat...**
6. Karta Důvěryhodné IP umožňuje přeskočit ověřování Azure Multi-Factor Authentication pro relace systému Windows pocházející z konkrétních IP adres. Například pokud zaměstnanci používají aplikace z kanceláře a z domova, můžete rozhodnout, že nechcete, aby jejich telefony vyzváněly pro ověřování Azure Multi-Factor Authentication, zatímco jsou v kanceláři. V takovém případě zadáte podsíť kanceláře jako položku důvěryhodných IP adres.
7. Klikněte na **Přidat...**
8. Vyberte **Jedna IP adresa**, pokud chcete přeskočit jednu IP adresu.
9. Vyberte **Rozsah IP adres**, pokud chcete přeskočit celý rozsah IP adres. Příklad 10.63.193.1–10.63.193.100.
10. Pokud chcete zadat rozsahu IP adres pomocí zápisu podsítě, vyberte **Podsíť**. Zadejte počáteční IP adresu podsítě a vyberte příslušnou síťovou masku z rozevíracího seznamu.
11. Klikněte na **OK**.

## <a name="next-steps"></a>Další kroky

- [Konfigurace zařízení sítě VPN třetích stran pro Azure MFA Server](howto-mfaserver-nps-vpn.md)

- [Posílení vaší stávající infrastruktury pro ověřování pomocí rozšíření NPS pro Azure MFA](howto-mfa-nps-extension.md)
