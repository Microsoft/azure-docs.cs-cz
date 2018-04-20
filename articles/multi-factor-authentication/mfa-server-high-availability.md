---
title: "Konfigurace serveru Azure MFA pro zajištění vysoké dostupnosti | Microsoft Docs"
description: "Nasazení více instancí serveru Azure Multi-Factor Authentication Server v konfiguracích, které zajišťují vysokou dostupnost."
services: multi-factor-authentication
keywords: Azure MFA
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: c2f9a2422ea5a47edd80948073c7f7a6cb0d0945
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Nakonfigurujte Server Azure Multi-Factor Authentication pro zajištění vysoké dostupnosti

K dosažení vysoké dostupnosti s nasazením Azure MFA serveru, musíte nasadit několik serverů vícefaktorového ověřování. Tato část obsahuje informace o vyrovnávání zatížení sítě návrh k dosažení vaše cíle vysoké dostupnosti ve službě Azure MFS serveru nasazení.

## <a name="mfa-server-overview"></a>Přehled MFA serveru

Architektura služby Azure MFA serveru zahrnuje několik komponent, jak je znázorněno v následujícím diagramu:

 ![Architektura serveru MFA](./media/mfa-server-high-availability/mfa-ha-architecture.png)

MFA Server je Windows Server, který je nainstalován software Azure Multi-Factor Authentication. Instance serveru MFA musí být aktivována služba vícefaktorového ověřování v Azure funkce. Více než jeden Server MFA může být nainstalovaná na místě.

První MFA serveru, který je nainstalován je hlavní MFA Server po aktivaci pomocí služby Azure MFA ve výchozím nastavení. Hlavní server MFA má kopii databáze PhoneFactor.pfdata nelze zapisovat. Následné instalace instancí MFA serveru se označují jako slaves. MFA slaves mít replikované jen pro čtení kopie databáze PhoneFactor.pfdata. MFA servery replikují informace o používání vzdáleného volání procedur (RPC). Všechny servery vícefaktorového ověřování musí souhrnně být buď připojený k doméně nebo samostatné replikovat informace.

Hlavní server MFA a podřízené servery vícefaktorového ověřování komunikovat se službou MFA Pokud se vyžaduje dvoufaktorové ověření. Například když se uživatel pokusí získat přístup k aplikaci, která vyžaduje dvoufaktorové ověřování, uživatel bude nejprve ověřit pomocí zprostředkovatele identity, jako je Active Directory (AD).

Po úspěšném ověření službou AD MFA Server bude komunikovat se službou vícefaktorového ověřování. MFA Server čeká oznámení ze služby MFA chcete povolit nebo odepřít přístup uživatelů k aplikaci.

Pokud hlavní server MFA přejde do režimu offline, ověřování stále lze zpracovat, ale činnosti, které vyžadují změny vícefaktorového ověřování databáze nelze zpracovat. (Příklady: Přidání uživatelů samoobslužné služby změny kódu PIN a změna informace o uživateli)

## <a name="deployment"></a>Nasazení

Vezměte v úvahu následující důležité skutečnosti pro vyrovnávání zatížení Azure MFA serveru a jeho souvisejících součástí.

* **K dosažení vysoké dostupnosti pomocí protokolu RADIUS standard**. Pokud používáte Azure MFA servery jako servery RADIUS, můžete nakonfigurovat jeden Server MFA potenciálně jako primární cíl ověřování protokolu RADIUS a dalších serverů Azure MFA jako cíle sekundární ověřování. Tuto metodu za účelem dosažení vysoké dostupnosti však nemusí být praktické, protože je nutné počkat časový limit nastat, když se ověřování nezdaří na cílové primární ověřování předtím, než můžete ověřovány proti cíli sekundární ověřování. K vyrovnávání přenosů protokolu RADIUS mezi klientem RADIUS a servery RADIUS (v tomto případě servery Azure MFA funguje jako servery RADIUS) zatížení je efektivnější tak, aby klienti RADIUS lze nakonfigurovat jednu adresu URL, která může ukazovat na.
* **Muset manuálně povýšit MFA slaves**. Pokud hlavní server Azure MFA přejde do režimu offline, sekundárních serverech Azure MFA dál zpracovávat žádosti vícefaktorového ověřování. Ale dokud hlavní MFA server k dispozici, správci nelze přidat uživatele nebo změnit nastavení vícefaktorového ověřování, a uživatelé nelze provádět změny pomocí portálu user portal. Povýšení MFA podřízená role hlavního serveru je vždy ruční proces.
* **Separability součástí**. Azure MFA serveru zahrnuje několik komponent, které je možné nainstalovat na stejnou instanci serveru Windows nebo na jinou instancí. Tyto součásti zahrnují portálu User Portal, webové služby mobilní aplikace a adaptér AD FS (agent). Tato separability umožňuje publikovat portálu User Portal a mobilní aplikace Webový Server z hraniční sítě pomocí Proxy webových aplikací. Taková konfigurace se přidá do celkového zabezpečení návrhu, jak je znázorněno v následujícím diagramu. MFA User Portal a mobilní aplikace Webový Server může také možné nasadit v konfiguraci Vyrovnávání zatížení sítě HA.

   ![MFA serveru s hraniční sítí](./media/mfa-server-high-availability/mfasecurity.png)

* **Jednorázové heslo (OTP) prostřednictvím serveru SMS (neboli jednosměrné služby SMS) vyžaduje použití trvalé relace, pokud provoz s vyrovnáváním zatížení**. Jednosměrné služby SMS je možnost ověřování, která způsobí, že MFA Server k odeslání uživatele textovou zprávu jednorázovým HESLEM. Uživatel zadá jednorázového HESLA v okně výzvy k dokončení ověřovací test MFA. Při načítání vyrovnávání serverů Azure MFA, stejný server, který obsluhuje požadavek počáteční ověřování musí být server, který přijme zprávu s jednorázovým HESLEM od uživatele; Pokud jiný Server MFA obdrží odpověď ověřování jednorázovým HESLEM, výzvu ověřování se nezdaří. Další informace najdete v tématu [jedno heslo čas přes SMS přidat do Azure MFA serveru](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Vyrovnávání zatížení sítě nasazení portálu User Portal a webová služba mobilní aplikace vyžadují trvalé relace**. Pokud jste se vyrovnávání zatížení portálu User Portal MFA a webové služby mobilní aplikace, musí každé relaci Zůstaňte na stejném serveru.

## <a name="high-availability-deployment"></a>Nasazení vysoké dostupnosti

Následující diagram znázorňuje na dokončení HA Vyrovnávání zatížení sítě implementace Azure MFA a jeho komponenty, spolu s AD FS pro referenci.

 ![Azure MFA Server HA implementace](./media/mfa-server-high-availability/mfa-ha-deployment.png)

Mějte na paměti následující položky pro oblasti odpovídajícím způsobem číslem na předchozím obrázku.

1. Dva servery Azure MFA (MFA1 a MFA2) jsou skupinu s vyrovnáváním zatížení (mfaapp.contoso.com) a jsou nakonfigurovány pro použití statického portu (4443) k replikaci databáze PhoneFactor.pfdata. Sada SDK webové služby je nainstalován na každém serveru MFA k umožnění komunikace přes port TCP 443 se servery služby AD FS. Servery vícefaktorového ověřování jsou nasazeny v konfiguraci bezstavové Vyrovnávání zatížení sítě. Ale pokud jste chtěli použití jednorázového HESLA prostřednictvím serveru SMS, musíte použít vyrovnávání stavová zatížení.
   ![Azure MFA serveru - aplikační server HA](./media/mfa-server-high-availability/mfaapp.png)

   > [!NOTE]
   > Protože RPC používá dynamické porty, se nedoporučuje otevřete brány firewall až rozsah dynamické porty, které mohou potenciálně používat RPC. Pokud máte bránu firewall **mezi** MFA aplikační servery, byste měli nakonfigurovat Server MFA pro komunikaci na statický port pro přenosy replikace mezi podřízený a hlavních serverů a otevřete tento port v bráně firewall. Statický port můžete vynutit tím, že vytvoříte hodnotu DWORD registru na ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` názvem ```Pfsvc_ncan_ip_tcp_port``` a nastavte tuto hodnotu na k dispozici statický port. Připojení vždy spouští podřízený vícefaktorového ověřování serverů k hlavnímu serveru, statický port je potřeba jenom na hlavním serveru, ale vzhledem k tomu, že můžete zvýšit úroveň podřízený server jako hlavní kdykoli, byste měli nastavit statický port ve všech serverech vícefaktorového ověřování.

2. Jsou dva servery mobilní aplikace uživatele portálu nebo MFA (vícefaktorového ověřování. až MAS1 a MFA. až MAS2) ve Vyrovnávání zatížení **stateful** konfigurace (mfa.contoso.com). Odvolat, aby trvalé relace jsou nutné pro vyrovnávání zatížení portálu User Portal vícefaktorového ověřování a služby mobilní aplikace.
   ![Azure MFA serveru – portál User Portal a mobilní aplikace služby HA](./media/mfa-server-high-availability/mfaportal.png)
3. Služba AD FS serverové farmy je zatížení vyrovnáváním a publikovány na Internetu prostřednictvím Vyrovnávání zatížení sítě proxy služby AD FS v hraniční síti. Každý Server služby AD FS používá agent služby AD FS komunikovat se servery vícefaktorového ověřování Azure pomocí jednu Vyrovnávání zatížení sítě adresu URL (mfaapp.contoso.com) přes port 443 protokolu TCP.

## <a name="next-steps"></a>Další kroky

* [Instalace a konfigurace Azure MFA serveru](multi-factor-authentication-get-started-server.md)
