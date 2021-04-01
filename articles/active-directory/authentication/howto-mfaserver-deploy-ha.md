---
title: Vysoká dostupnost pro Azure MFA Server – Azure Active Directory
description: Nasaďte více instancí Azure Multi-Factor Authentication Server v konfiguracích, které poskytují vysokou dostupnost.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1853a4784e3098ecbcefa94d5512b4877ac4dc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584472"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurace Azure Multi-Factor Authentication Server pro vysokou dostupnost

Abyste dosáhli vysoké dostupnosti nasazení MFA Azure serveru, musíte nasadit víc serverů MFA. V této části najdete informace o návrhu s vyrovnáváním zatížení, abyste dosáhli vašich cílů vysoké dostupnosti v nasazení Azure MFS serveru.

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování (MFA) během přihlašovacích událostí, by měli používat cloudovou Multi-Factor Authentication Azure AD.
>
> Pokud chcete začít s cloudovým ověřováním MFA, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Stávající zákazníci, kteří si vyaktivovali MFA Server dřív než 1. července 2019, můžou stáhnout nejnovější verzi, budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci obvyklým způsobem.

## <a name="mfa-server-overview"></a>Přehled serveru MFA

Architektura služby Azure MFA Server se skládá z několika součástí, jak je znázorněno v následujícím diagramu:

 ![Komponenty architektury MFA serveru](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

MFA Server je Windows Server, na kterém je nainstalovaný software Azure Multi-Factor Authentication. Instance MFA serveru musí být aktivovaná službou MFA v Azure, aby fungovala. Do místního prostředí se dá nainstalovat víc než jeden MFA Server.

První server MFA, který je nainstalovaný, je primární MFA Server, který se ve výchozím nastavení aktivuje službou Azure MFA. Primární MFA Server má zapisovatelnou kopii databáze PhoneFactor. pfdata. Další instalace instancí MFA serveru se označují jako podřízené. Podřízené položky MFA mají replikovanou kopii databáze PhoneFactor. pfdata, která je jen pro čtení. VÍCEFAKTOROVÉ servery replikují informace pomocí vzdáleného volání procedur (RPC). Všechny servery MFA musí být společně připojené k doméně nebo samostatné k replikaci informací.

Primární i podřízený server MFA komunikuje se službou MFA při vyžadování dvojúrovňového ověřování. Když se například uživatel pokusí získat přístup k aplikaci, která vyžaduje dvojúrovňové ověřování, bude uživatel nejdřív ověřený poskytovatelem identity, jako je třeba služba Active Directory (AD).

Po úspěšném ověření se službou AD bude MFA server komunikovat se službou MFA. Server MFA čeká na oznámení od služby MFA, aby umožnil uživateli přístup k aplikaci nebo mu odepřít přístup.

Pokud primární server MFA přejde do režimu offline, můžou se ověřování dál zpracovávat, ale nedají se zpracovat operace vyžadující změny v databázi MFA. (Příklady zahrnují: Přidání uživatelů, samoobslužné změny PIN kódu, změna informací o uživateli nebo přístup k portálu User Portal).

## <a name="deployment"></a>Nasazení

Vezměte v úvahu následující důležité body pro vyrovnávání zatížení Azure MFA serveru a jeho souvisejících součástí.

* **Zajištění vysoké dostupnosti pomocí standardu RADIUS** Pokud používáte Azure MFA servery jako servery RADIUS, můžete nakonfigurovat jeden MFA Server jako primární cíl ověřování RADIUS a jiné servery Azure MFA jako cíle sekundárního ověřování. Tato metoda, která by dosáhla vysoké dostupnosti, ale nemusí být praktická, protože je nutné počkat na vypršení časového limitu, když se ověřování na primárním cíli ověřování nezdařilo, než bude možné provést ověření proti cílovému sekundárnímu ověřování. Je efektivnější vyrovnávat zatížení protokolu RADIUS mezi klientem RADIUS a servery RADIUS (v tomto případě servery Azure MFA fungující jako servery RADIUS), abyste mohli nakonfigurovat klienty RADIUS s jedinou adresou URL, na kterou můžou odkazovat.
* **Je potřeba ručně propagovat podřízené uzly MFA**. Pokud primární Azure MFA server přejde do režimu offline, sekundární servery Azure MFA budou dál zpracovávat požadavky MFA. Dokud však nebude k dispozici primární MFA Server, správci nebudou moct přidávat uživatele ani měnit nastavení vícefaktorového ověřování a uživatelé nemůžou provádět změny pomocí portálu User Portal. Zvýšení úrovně MFA, která je podřízená primární roli, je vždycky ruční proces.
* **Separability komponent**. Azure MFA Server se skládá z několika komponent, které se dají nainstalovat na stejnou instanci Windows serveru nebo na různé instance. Mezi tyto součásti patří portál User Portal, Webová služba mobilní aplikace a adaptér ADFS (Agent). Tento separability umožňuje použít proxy webových aplikací k publikování portálu User Portal a webového serveru mobilní aplikace z hraniční sítě. Taková konfigurace přičítá k celkovému zabezpečení návrhu, jak je znázorněno v následujícím diagramu. Portál MFA User Portal a webový server mobilní aplikace můžou být nasazené i v konfiguracích s vyrovnáváním zatížení HA.

   ![MFA Server s hraniční sítí](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* Jednorázové **heslo (jednorázové heslo) prostřednictvím SMS (označované také jako jednosměrné SMS) vyžaduje použití rychlé relace v případě vyrovnávání zatížení**. Jednosměrná zpráva SMS je možnost ověřování, která způsobí, že server MFA pošle uživatelům textovou zprávu obsahující jednorázové heslo. Uživatel zadá do okna příkazového řádku jednorázové heslo, aby se mohl dokončí ověřování MFA. Pokud vyrovnáváte zatížení serverů Azure MFA, musí být stejný server, který sloužil jako počáteční požadavek na ověření, serverem, který uživateli obdrží zprávu pro jednorázové heslo; Pokud jiný server MFA obdrží odpověď na heslo, výzva k ověření se nezdařila. Další informace najdete v tématu [jednorázové heslo při přidání hesla přes SMS do Azure MFA serveru](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Nasazení s vyrovnáváním zatížení portálu User Portal a webové služby mobilní aplikace vyžadují rychlé relace**. Pokud vyrovnáváte zatížení portálu User Portal a webové služby mobilní aplikace, musí každá relace zůstat na stejném serveru.

## <a name="high-availability-deployment"></a>Nasazení s vysokou dostupností

Následující diagram znázorňuje kompletní implementaci Azure MFA s vyrovnáváním zatížení, společně se službou ADFS pro referenci.

 ![Implementace HA Azure MFA serveru](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Všimněte si následujících položek pro odpovídající očíslované oblasti předchozího diagramu.

1. Tyto dva servery Azure MFA (MFA1 a MFA2) mají vyrovnávání zatížení (mfaapp.contoso.com) a jsou nakonfigurované tak, aby k replikaci databáze PhoneFactor. pfdata používaly statický port (4443). Sada SDK webové služby je nainstalovaná na každém serveru MFA, aby umožňovala komunikaci přes port TCP 443 se servery ADFS. Servery MFA jsou nasazené v konfiguraci s vyrovnáváním zatížení bez stavů. Pokud jste ale chtěli použít jednorázové heslo přes SMS, musíte použít stav vyrovnávání zatížení.
   ![Azure MFA Server – HA aplikačního serveru](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Vzhledem k tomu, že služba RPC používá dynamické porty, nedoporučuje se otevírat brány firewall až do rozsahu dynamických portů, které může RPC potenciálně použít. Pokud máte bránu firewall **mezi** aplikačními servery MFA, měli byste Server MFA nakonfigurovat tak, aby komunikoval na statickém portu pro přenos replikace mezi podřízenými a primárními servery a aby se tento port otevřel v bráně firewall. Statický port můžete vynutit tak, že vytvoříte hodnotu registru DWORD s ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` názvem ```Pfsvc_ncan_ip_tcp_port``` a nastavíte hodnotu na dostupný statický port. Připojení jsou vždy iniciována podřízenými servery MFA na primární. statický port je vyžadován pouze na primárním serveru, ale vzhledem k tomu, že můžete kdykoli zvýšit úroveň podřízeného na primární server, měli byste nastavit statický port na všech serverech MFA.

2. Dva uživatelské portály/servery mobilních aplikací pro MFA (vícefaktorové ověřování-MAS1 a MFA-UP-MAS2) jsou vyrovnávány zatížením **stavové** konfigurace (MFA.contoso.com). Navrácení těchto relací s rychlým voláním je požadavek na Vyrovnávání zatížení portálu pro uživatele MFA a mobilní App Service.
   ![Azure MFA Server – User Portal a Mobile App Service HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Serverová farma ADFS je vyrovnává zatížení a publikuje se na internetu prostřednictvím proxy serverů služby AD FS s vyrovnáváním zatížení v hraniční síti. Každý server ADFS používá ke komunikaci s Azure MFA servery službu AD FS pomocí jedné adresy URL s vyrovnáváním zatížení (mfaapp.contoso.com) přes port TCP 443.

## <a name="next-steps"></a>Další kroky

* [Instalace a konfigurace Azure MFA serveru](howto-mfaserver-deploy.md)
