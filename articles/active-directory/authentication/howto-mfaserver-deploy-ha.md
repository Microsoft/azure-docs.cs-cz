---
title: Vysoká dostupnost pro server Azure MFA – Azure Active Directory
description: Nasaďte více instancí serveru Azure Multi-Factor Authentication Server v konfiguracích, které poskytují vysokou dostupnost.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7b5f6bef5358acf0709f994b85215e505fa4db
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653372"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurace serveru Azure Multi-Factor Authentication Server pro vysokou dostupnost

Chcete-li dosáhnout vysoké dostupnosti s nasazením Azure Server MFA, je potřeba nasadit více serverů MFA. Tato část obsahuje informace o návrhu s vyrovnáváním zatížení k dosažení cílů vysoké dostupnosti v nasazení serveru Azure MFS Server.

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

## <a name="mfa-server-overview"></a>Přehled serveru MFA

Architektura služby Azure MFA Server obsahuje několik součástí, jak je znázorněno na následujícím diagramu:

 ![Součásti architektury serveru MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Server MFA je windows server s nainstalovaným softwarem Azure Multi-Factor Authentication. Aby fungovala instance serveru MFA Server, musí být aktivována službou MFA v Azure. Více než jeden server MFA lze nainstalovat místně.

První server MFA, který je nainstalován, je hlavní server MFA po aktivaci službou Azure MFA ve výchozím nastavení. Hlavní server MFA má zapisovatelnou kopii databáze PhoneFactor.pfdata. Následné instalace instancí serveru MFA jsou označovány jako podřízení. Podřízení mfa mají replikovanou kopii databáze PhoneFactor.pfdata jen pro čtení. Servery MFA replikují informace pomocí vzdáleného volání procedur (RPC). Všechny servery Vícefaktorové finanční ho disponibilního řízení musí být společně připojeny k doméně nebo samostatné, aby bylo možné replikovat informace.

Hlavní i podřízené servery MFA komunikují se službou MFA, pokud je vyžadováno dvoufaktorové ověřování. Pokud se například uživatel pokusí získat přístup k aplikaci, která vyžaduje dvoufaktorové ověřování, bude nejprve ověřen zprostředkovatelem identity, například službou Active Directory (AD).

Po úspěšném ověření pomocí služby AD bude server MFA komunikovat se službou MFA. Server MFA čeká na oznámení ze služby MFA povolit nebo odepřít přístup uživatele k aplikaci.

Pokud hlavní server MFA přejde do offline, ověřování lze stále zpracovávat, ale operace, které vyžadují změny databáze MFA nelze zpracovat. (Příklady zahrnují: přidání uživatelů, samoobslužné změny PIN, změnu informací o uživateli nebo přístup k uživatelskému portálu)

## <a name="deployment"></a>Nasazení

Zvažte následující důležité body pro vyrovnávání zatížení Azure MFA Server a jeho související součásti.

* **Použití standardu RADIUS k dosažení vysoké dostupnosti**. Pokud používáte servery Azure MFA jako servery RADIUS, můžete potenciálně nakonfigurovat jeden server MFA jako primární cíl ověřování RADIUS a další servery Azure MFA jako sekundární cíle ověřování. Tato metoda k dosažení vysoké dostupnosti však nemusí být praktické, protože je nutné počkat na časový čas dojít při ověřování selže na primární cíl ověřování, než můžete být ověřena proti sekundární cíl ověřování. Je efektivnější vyrovnávání zatížení přenosů RADIUS mezi klientem RADIUS a servery RADIUS (v tomto případě servery Azure MFA, které fungují jako servery RADIUS), takže můžete nakonfigurovat klienty RADIUS pomocí jediné adresy URL, na kterou mohou překážet.
* **Je třeba ručně podporovat podřízené vícefaktorové finanční finanční úřady**. Pokud hlavní server Azure MFA přejde do offline, sekundární servery Azure MFA nadále zpracovávají požadavky MFA. Dokud však není k dispozici hlavní server MFA, správci nemohou přidávat uživatele nebo upravovat nastavení vícefaktorového ověřování a uživatelé nemohou provádět změny pomocí uživatelského portálu. Povýšení vícefaktorového faktoru podřízeného hlavní roli je vždy ruční proces.
* **Oddělitelnost součástí**. Azure MFA Server obsahuje několik součástí, které lze nainstalovat na stejnou instanci Windows Serveru nebo na různých instancích. Mezi tyto součásti patří uživatelský portál, webová služba mobilních aplikací a adaptér ADFS (agent). Tato oddělitelnost umožňuje použít proxy webové aplikace k publikování uživatelského portálu a webového serveru mobilních aplikací z hraniční sítě. Tato konfigurace zvyšuje celkové zabezpečení návrhu, jak je znázorněno na následujícím diagramu. Portál pro vícefaktorové ověřování a webový server mobilních aplikací mohou být také nasazeny v konfiguracích s vyrovnáváním zatížení HA.

   ![Server MFA s hraniční sítí](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Jednorázové heslo (OTP) přes SMS (aka jednosměrné SMS) vyžaduje použití lepkavých relací, pokud je provoz vyrovnán s vyrovnáváním zatížení**. Jednosměrná sms je možnost ověřování, která způsobí, že server MFA odešle uživatelům textovou zprávu obsahující otp. Uživatel zadá OTP v okně výzvy k dokončení výzvy Vícefaktorové ověřování. Pokud vyrovnáváte zatížení serverů Azure MFA, stejný server, který obsluhoval požadavek na počáteční ověření, musí být server, který obdrží zprávu OTP od uživatele; Pokud jiný server MFA obdrží odpověď OTP, výzva ověřování se nezdaří. Další informace naleznete [v tématu One Time Password over SMS Added to Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Nasazení uživatelského portálu a webové služby mobilních aplikací s vyrovnáváním zatížení vyžadují rychlé relace**. Pokud vyrovnáváte vyrovnávání uživatelského portálu MFA a webové služby mobilních aplikací, musí každá relace zůstat na stejném serveru.

## <a name="high-availability-deployment"></a>Nasazení s vysokou dostupností

Následující diagram znázorňuje kompletní implementaci Azure MFA a jeho součástí s vyrovnáváním zatížení HA spolu s adfs pro referenci.

 ![Implementace ha serveru Azure MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Všimněte si následujících položek pro odpovídajícím způsobem očíslovaným rozsahem předchozího diagramu.

1. Dva servery Azure MFA (MFA1 a MFA2) jsou vyrovnávání zatížení (mfaapp.contoso.com) a jsou nakonfigurované tak, aby používaly statický port (4443) k replikaci databáze PhoneFactor.pfdata. Sada Web Service SDK je nainstalována na každém serveru MFA, aby byla umožněna komunikace přes port TCP 443 se servery ADFS. Servery MFA jsou nasazeny v bezstavové konfiguraci s vyrovnáváním zatížení. Pokud jste však chtěli používat OTP přes SMS, musíte použít stavové vyrovnávání zatížení.
   ![Azure MFA Server – aplikační server HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Vzhledem k tomu, že vzdálené volání procedur používá dynamické porty, nedoporučuje se otevírat brány firewall až do rozsahu dynamických portů, které může rpc potenciálně používat. Pokud máte bránu firewall **mezi** aplikačními servery MFA, měli byste nakonfigurovat server MFA tak, aby komunikoval na statickém portu pro replikační provoz mezi podřízenými a hlavním serverem a otevřel tento port v bráně firewall. Statický port můžete vynutit vytvořením hodnoty ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` ```Pfsvc_ncan_ip_tcp_port``` registru DWORD při volání a nastavením hodnoty na dostupný statický port. Připojení jsou vždy iniciována podřízenými servery MFA k hlavnímu serveru, statický port je vyžadován pouze na hlavním serveru, ale protože můžete kdykoli povýšit podřízeného jako hlavního serveru, měli byste nastavit statický port na všech serverech MFA.

2. Dva servery mobilních aplikací uživatelského portálu/MFA (MFA-UP-MAS1 a MFA-UP-MAS2) jsou vyrovnávané zatížení ve **stavové** konfiguraci (mfa.contoso.com). Připomeňme, že rychlé relace jsou požadavkem pro vyrovnávání zatížení uživatelského portálu MFA a služby mobilních aplikací.
   ![Server Azure MFA – uživatelský portál a služba mobilních aplikací HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Farma Serveru ADFS je vyvážená zatížením a publikována do Internetu prostřednictvím proxy serverů ADFS s vyrovnáváním zatížení v hraniční síti. Každý server ADFS používá agenta ADFS ke komunikaci se servery Azure MFA pomocí jediné adresy URL s vyrovnáváním zatížení (mfaapp.contoso.com) přes port TCP 443.

## <a name="next-steps"></a>Další kroky

* [Instalace a konfigurace serveru Azure MFA](howto-mfaserver-deploy.md)
