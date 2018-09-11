---
title: Konfigurace serveru Azure MFA pro zajištění vysoké dostupnosti | Dokumentace Microsoftu
description: Nasazení více instancí Azure Multi-Factor Authentication Server v konfiguracích, které zajišťují vysokou dostupnost.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 5d3833d3218a4b6252c9591bb67686ddc1c3cdf9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298570"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurovat ověřování Azure Multi-Factor Authentication Server pro zajištění vysoké dostupnosti

K dosažení vysoké dostupnosti s nasazením Azure Server MFA, budete muset nasadit víc serverů MFA. Tato část obsahuje informace o návrhu s vyrovnáváním zatížení pro dosažení cílů vaší vysokou dostupnost v vám nasazení serverů MFS Azure.

## <a name="mfa-server-overview"></a>MFA serveru – přehled

Architektura služby Azure MFA serveru zahrnuje několik komponent, jak je znázorněno v následujícím diagramu:

 ![Architektura serveru MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

MFA Server je Windows Server, který je nainstalován software ověřování Azure Multi-Factor Authentication. Instance serveru MFA musí být aktivovaná služba vícefaktorového ověřování v Azure do funkce. Více než jeden Server MFA může být nainstalovaný místní.

První MFA serveru, který je nainstalován je hlavní MFA Server při aktivaci pomocí služby Azure MFA ve výchozím nastavení. Hlavní MFA server má kopii databáze PhoneFactor.pfdata zapisovatelné. Následné instalace instancí systému MFA Server jsou označovány jako podřízené uzly. Podřízené uzly MFA kopie replikovaných jen pro čtení PhoneFactor.pfdata databáze. MFA servery replikují informace pomocí vzdáleného volání procedur (RPC). Přeruší všechny MFA musí souhrnně být buď připojené k doméně nebo samostatnou replikaci informací.

Hlavní server MFA a MFA serverů za podřízené komunikovat se službou vícefaktorové ověřování při vyžádáním dvoufaktorového ověřování. Například když se uživatel pokusí získat přístup k aplikaci, která vyžaduje dvojúrovňové ověřování, uživatel se nejdřív ověřit pomocí zprostředkovatele identity, jako je Active Directory (AD).

Po úspěšném ověření službou AD MFA Server bude komunikovat se službou vícefaktorové ověřování. MFA Server čeká na oznámení ze služby MFA umožňuje povolit nebo odepřít přístup uživatelů k aplikaci.

Pokud hlavní MFA server přejde do režimu offline, stále zpracovávat ověřování, ale operace, které vyžadují změny v databázi MFA nelze zpracovat. (Příklady: Přidání uživatelů samoobslužných služeb změny kódu PIN a změně informací o uživateli)

## <a name="deployment"></a>Nasazení

Vezměte v úvahu následující důležité skutečnosti pro vyrovnávání zatížení Azure MFA serverem a jeho souvisejících součástí.

* **K dosažení vysoké dostupnosti pomocí protokolu RADIUS standard**. Pokud používáte Azure MFA serverů jako servery RADIUS, můžete potenciálně nakonfigurovat jeden Server MFA jako primární cíl ověřování protokolu RADIUS a dalších serverů Azure MFA jako cíle sekundární ověřování. Tuto metodu za účelem dosažení vysoké dostupnosti však nemusí být praktické, protože je nutné počkat časový limit, dojde k selhání ověření na cílové primární ověřování předtím, než můžete ověřovány proti cíli sekundární ověřování. Je mnohem efektivnější k vyrovnávání zatížení provozu pomocí protokolu RADIUS mezi klienta protokolu RADIUS a servery RADIUS (v tomto případě Azure MFA Server fungující jako servery RADIUS) tak, aby klienti RADIUS můžete nakonfigurovat jednu adresu URL, která může odkazovat na.
* **Muset manuálně povýšit MFA podřízené**. Pokud hlavní Azure MFA server přejde do režimu offline, sekundární Azure MFA servery dál zpracovávat žádosti vícefaktorové ověřování. Ale až do hlavního serveru MFA je k dispozici, nelze přidat uživatele nebo změnit nastavení MFA správci a uživatelé nemohou provádět změny pomocí portálu user portal. Zvyšuje se úroveň MFA podřízený na role hlavního operačního serveru je vždy ruční.
* **Separability komponent**. Azure MFA Server se skládá z několika součástí, které mohou být nainstalovány ve stejné instanci Windows serveru nebo v různých instancích. Tyto součásti zahrnují adaptéru služby AD FS (agent), portál User Portal a webové služby mobilní aplikace. Tato separability umožňuje použít Proxy webových aplikací portálu User Portal a Mobile App Web Server publikovat z hraniční sítě. Taková konfigurace se přidá do celkové zabezpečení návrhu, jak je znázorněno v následujícím diagramu. MFA User Portal a mobilní aplikace Webový Server může také nasadit v konfiguracích s vysokou DOSTUPNOSTÍ s vyrovnáváním zatížení.

   ![MFA Server s hraniční síti](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Jednorázovým heslem (OTP) přes SMS (označuje se také jako jednosměrná zpráva SMS) potřebuje použít rychlé relace, pokud je provoz s vyrovnáváním zatížení**. Jednosměrné služby SMS je možnost ověřování, která způsobí, že se serveru MFA uživatelům poslat textovou zprávu jednorázovým HESLEM. Uživatel zadá kombinací jednorázového HESLA v okně výzvy k dokončení ověřovacím testem MFA. Pokud načítáte zůstatek Azure MFA servery, musí být stejný server, který obsluhuje požadavek na počáteční ověření serveru, který obdrží zprávu ověřování jednorázovým HESLEM od uživatele; Pokud jiný MFA Server obdrží odpověď ověřování jednorázovým HESLEM, výzvu ověřování se nezdaří. Další informace najdete v tématu [jedno heslo čas přes SMS přidán do Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Nasazení portálu User Portal a webová služba mobilní aplikace s vyrovnáváním zatížení vyžaduje rychlé relace**. Pokud jste se vyrovnávání zatížení MFA User Portal a webová služba mobilní aplikace, musí každá relace zůstat na stejném serveru.

## <a name="high-availability-deployment"></a>Nasazení vysoké dostupnosti

Následující diagram ukazuje kompletní vysokou dostupnost s vyrovnáváním zatížení implementaci vícefaktorovým Ověřováním Azure a jeho komponenty, spolu s AD FS pro referenci.

 ![Azure MFA Server HA implementace](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Mějte na paměti následující položky pro odpovídajícím způsobem očíslované oblasti na předchozím obrázku.

1. Dva servery Azure MFA (MFA1 a MFA2) jsou s vyrovnáváním zatížení (mfaapp.contoso.com) a jsou nakonfigurovány pro použití statického portu (4443) k replikaci databáze PhoneFactor.pfdata. Sada SDK webové služby je nainstalovanými na každém z MFA serveru k umožnění komunikace přes port TCP 443 se servery služby AD FS. MFA servery jsou nasazené v konfiguraci bezstavové s vyrovnáváním zatížení. Ale pokud byste chtěli použít ověřování jednorázovým HESLEM prostřednictvím serveru SMS, musíte použít stavová zátěže.
   ![Azure MFA serveru – aplikační server HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Vzhledem k tomu RPC používá dynamické porty, se nedoporučuje k otevření brány firewall až rozsah dynamické porty, které mohou potenciálně používat RPC. Pokud máte bránu firewall **mezi** aplikačních serverů MFA, měli byste nakonfigurovat MFA serveru komunikaci na statický port pro přenosy replikace mezi hlavní a podřízené servery a otevřít tento port v bráně firewall. Statický port můžete vynutit tím, že vytvoříte hodnotu DWORD registru na ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` volá ```Pfsvc_ncan_ip_tcp_port``` a nastavením této hodnoty na dostupné statický port. Připojení jsou vždycky iniciováno podřízené servery vícefaktorové ověřování k hlavnímu serveru, statický port je potřeba jenom na hlavním serveru, ale protože podřízená položka bude hlavní kdykoli můžete zvýšit úroveň, byste měli nastavit statický port ve všech serverech vícefaktorové ověřování.

2. Dva servery mobilní aplikace uživatele portál/MFA (vícefaktorové ověřování. až MAS1 a vícefaktorové ověřování. až MAS2) jsou v skupinu s vyrovnáváním zatížení **stavová** konfigurace (mfa.contoso.com). Připomínáme, že rychlé relace jsou nutné pro vyrovnávání zatížení portálu User Portal pro MFA a služby mobilní aplikace.
   ![Azure MFA serveru – portál User Portal a mobilní služby App Service HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Farmě služby AD FS je load balanced a publikovat na Internetu prostřednictvím vyrovnáváním zatížení služby AD FS proxy serverů v hraniční síti. Každý Server služby AD FS používá ke komunikaci s Azure MFA servery pomocí jednoho s vyrovnáváním zatížení adresy URL (mfaapp.contoso.com) přes TCP port 443 agent služby AD FS.

## <a name="next-steps"></a>Další postup

* [Instalace a konfigurace Azure MFA serveru](howto-mfaserver-deploy.md)
