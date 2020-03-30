---
title: Principy konektorů proxy aplikací Azure AD | Dokumenty společnosti Microsoft
description: Popisuje základy konektorů proxy aplikací Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: b097ce3781a77a8c5e8a94b9c2bf0977f3efcfd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481326"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Principy konektorů proxy aplikací Azure AD

Konektory jsou co umožňují proxy aplikace Azure AD. Jsou jednoduché, snadno se nasazují a udržují a jsou super výkonné. Tento článek popisuje, co jsou konektory, jak fungují a některé návrhy, jak optimalizovat nasazení.

## <a name="what-is-an-application-proxy-connector"></a>Co je konektor proxy aplikace?

Konektory jsou zjednodušené agenty, které jsou místní a usnadňují odchozí připojení ke službě Proxy aplikace. Konektory musí být nainstalovány na systému Windows Server, který má přístup k back-endové aplikaci. Můžete uspořádat konektory do skupin konektorů, přičemž každá skupina zpracovává provoz do konkrétních aplikací.

## <a name="requirements-and-deployment"></a>Požadavky a nasazení

Chcete-li úspěšně nasadit proxy aplikace, potřebujete alespoň jeden konektor, ale doporučujeme dva nebo více pro větší odolnost proti chybám. Nainstalujte konektor do počítače se systémem Windows Server 2012 R2 nebo novějším. Konektor potřebuje komunikovat se službou Proxy aplikace a místními aplikacemi, které publikujete.

### <a name="windows-server"></a>Server Windows
Potřebujete server se systémem Windows Server 2012 R2 nebo novějším, na kterém můžete nainstalovat konektor Proxy aplikace. Server se musí připojit ke službám proxy aplikací v Azure a k místním aplikacím, které publikujete.

Před instalací konektoru Proxy aplikace musí mít systém Windows Server povolenou technologii TLS 1.2. Povolení tls 1.2 na serveru:

1. Nastavte následující klíče registru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restartování serveru

Další informace o požadavcích na síť pro konektorový server naleznete v [tématu Začínáme s proxy aplikacemi a nainstalujte konektor](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Údržba

Konektory a služba se starají o všechny úkoly s vysokou dostupností. Mohou být přidány nebo odstraněny dynamicky. Pokaždé, když přijde nový požadavek je směrován do jedné z konektorů, která je aktuálně k dispozici. Pokud konektor dočasně není k dispozici, nereaguje na tento provoz.

Konektory jsou bezstavové a nemají v počítači žádná konfigurační data. Jediná data, která ukládají, jsou nastavení pro připojení služby a jejího ověřovacího certifikátu. Když se připojí ke službě, vytáhnou všechna požadovaná konfigurační data a aktualizují je každých pár minut.

Konektory také dotazování serveru zjistit, zda je novější verze konektoru. Pokud je nalezen, konektory aktualizovat sami.

Konektory můžete sledovat ze zařízení, na které jsou spuštěny, a to buď pomocí protokolu událostí, tak čítačů výkonu. Nebo můžete zobrazit jejich stav na stránce Proxy aplikace na webu Azure Portal:

![Příklad: Konektory proxy aplikací Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Není nutné ručně odstranit konektory, které jsou nepoužívané. Když je konektor spuštěn, zůstane aktivní, protože se připojuje ke službě. Nepoužívané konektory jsou označeny jako _neaktivní_ a jsou odebrány po 10 dnech nečinnosti. Pokud však chcete odinstalovat konektor, odinstalujte službu Konektor i službu Updater ze serveru. Restartujte počítač, aby se službě úplně odebrala.

## <a name="automatic-updates"></a>Automatické aktualizace

Azure AD poskytuje automatické aktualizace pro všechny konektory, které nasadíte. Dokud je spuštěna služba Aktualizace konektoru proxy aplikace, vaše konektory se aktualizují automaticky. Pokud na serveru nevidíte službu Aktualizace konektoru, je třeba [přeinstalovat konektor,](application-proxy-add-on-premises-application.md) abyste získali všechny aktualizace.

Pokud nechcete čekat na automatickou aktualizaci, která přijde na váš konektor, můžete provést ruční upgrade. Přejděte na [stránku pro stažení konektoru](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serveru, kde je konektor umístěn, a vyberte **možnost Stáhnout**. Tento proces spustí upgrade pro místní konektor.

Pro klienty s více konektory, automatické aktualizace cíl jeden konektor najednou v každé skupině, aby se zabránilo prostojům ve vašem prostředí.

K prostojání může dojít při aktualizaci konektoru, pokud:
  
- Máte pouze jeden konektor doporučujeme nainstalovat druhý konektor a [vytvořit skupinu konektorů](application-proxy-connector-groups.md). Tím se zabrání prostojům a zajistíte vyšší dostupnost.  
- Spojnice byla uprostřed transakce při zahájení aktualizace. Přestože dojde ke ztrátě počáteční transakce, prohlížeč by měl operaci automaticky opakovat nebo stránku aktualizovat. Při požadavku je znovu odeslána, provoz je směrován do konektoru zálohování.

Informace o dříve vydaných verzích a o jejich změnách naleznete v [tématu Historie verzí aplikace](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Vytváření skupin konektorů

Skupiny spojnic umožňují přiřadit konkrétní konektory pro konkrétní aplikace. Můžete seskupit několik spojnic a pak přiřadit každou aplikaci ke skupině.

Skupiny konektorů usnadňují správu velkých nasazení. Zlepšují také latenci pro klienty, kteří mají aplikace hostované v různých oblastech, protože můžete vytvořit skupiny konektorů založených na umístění, které budou sloužit pouze místním aplikacím.

Další informace o skupinách konektorů najdete [v tématu Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Plánování kapacity

Je důležité se ujistit, že jste naplánovali dostatečnou kapacitu mezi konektory pro zpracování očekávaného objemu provozu. Doporučujeme, aby každá skupina konektorů má alespoň dva konektory pro zajištění vysoké dostupnosti a škálování. Mít tři konektory je optimální v případě, že budete potřebovat servis stroje v libovolném bodě.

Obecně platí, že čím více uživatelů máte, tím větší je počítač, který budete potřebovat. Níže je tabulka poskytující přehled objemu a očekávanou latenci, kterou mohou zpracovat různé počítače. Všimněte si, že je vše založeno na očekávaných transakcích za sekundu (TPS) spíše než podle uživatele, protože vzorce použití se liší a nelze je použít k předvídání zatížení. Budou také existovat určité rozdíly založené na velikosti odpovědí a době odezvy back-endové aplikace - větší velikosti odpovědí a pomalejší doby odezvy budou mít za následek nižší Max TPS. Doporučujeme také mít další stroje tak, aby distribuované zatížení napříč počítači vždy poskytuje dostatek vyrovnávací paměti. Extra kapacita zajistí vysokou dostupnost a odolnost proti chybám.

|Cores|Paměť RAM|Očekávaná latence (MS)-P99|Maximální TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\*Tento počítač použil vlastní nastavení ke zvýšení některých výchozích limitů připojení nad rámec nastavení doporučených rozhraní .NET. Doporučujeme spustit test s výchozím nastavením před kontaktováním podpory, aby se tento limit změnil pro vašeho tenanta.

> [!NOTE]
> Není velký rozdíl v maximální TPS mezi 4, 8 a 16 jádrových strojů. Hlavní rozdíl mezi těmi je v očekávané latence.
>
> Tato tabulka se také zaměřuje na očekávaný výkon konektoru na základě typu počítače, na který je nainstalován. Toto je oddělené od omezení omezení služby Proxy aplikace, viz [Omezení a omezení služby](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Zabezpečení a vytváření sítí

Konektory lze nainstalovat kdekoli v síti, což jim umožňuje odesílat požadavky na službu Proxy aplikace. Důležité je, že počítač s konektorem má také přístup k vašim aplikacím. Konektory můžete nainstalovat uvnitř podnikové sítě nebo na virtuální počítač, který běží v cloudu. Konektory lze spustit v rámci hraniční sítě, označované také jako demilitarizovaná zóna (DMZ), ale není to nutné, protože veškerý provoz je odchozí, takže vaše síť zůstane zabezpečená.

Konektory odesílají pouze odchozí požadavky. Odchozí provoz je odeslán službě Proxy aplikace a publikovaným aplikacím. Není třeba otevírat příchozí porty, protože provoz toky oběma směry po vytvoření relace. Také není nutné konfigurovat příchozí přístup přes brány firewall.

Další informace o konfiguraci odchozích pravidel brány firewall naleznete v [tématu Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Výkon a škálovatelnost

Škálování pro službu Proxy aplikace je transparentní, ale škálování je faktorem pro konektory. Musíte mít dostatek konektorů pro zpracování provozu ve špičce. Vzhledem k tomu, že konektory jsou bezstavové, nejsou ovlivněny počtem uživatelů nebo relací. Místo toho reagují na počet požadavků a jejich velikost datové části. Se standardním webovým provozem dokáže průměrný stroj zpracovat několik tisíc požadavků za sekundu. Konkrétní kapacita závisí na přesných vlastnostech stroje.

Výkon konektoru je vázán procesorem a sítí. Výkon procesoru je potřeba pro šifrování a dešifrování TLS, zatímco sítě je důležité získat rychlé připojení k aplikacím a online službě v Azure.

Naproti tomu paměť je menší problém pro konektory. Online služba se postará o většinu zpracování a veškerý neověřený provoz. Všechno, co se dá dělat v cloudu, se provádí v cloudu.

Pokud z nějakého důvodu, že konektor nebo počítač přestane být k dispozici, provoz začne přecovákovat do jiného konektoru ve skupině. Tato odolnost je také důvod, proč doporučujeme mít více konektorů.

Dalším faktorem, který ovlivňuje výkon, je kvalita sítě mezi konektory, včetně:

- **Online služba**: Pomalé připojení nebo připojení s vysokou latencí ke službě Proxy aplikace v Azure ovlivňují výkon konektoru. Chcete-li dosáhnout nejlepšího výkonu, připojte svou organizaci k Azure pomocí express route. V opačném případě se váš síťový tým ujistěte, že připojení k Azure jsou zpracovány co nejefektivněji.
- **Back-endové aplikace**: V některých případech existují další proxy servery mezi konektorem a back-endové aplikace, které mohou zpomalit nebo zabránit připojení. Chcete-li tento scénář vyřešit, otevřete prohlížeč z konektorového serveru a pokuste se o přístup k aplikaci. Pokud spustíte konektory v Azure, ale aplikace jsou místní, prostředí nemusí být to, co vaši uživatelé očekávají.
- **Řadiče domény**: Pokud konektory provádět jednotné přihlašování (Jednotné přihlašování) pomocí kerberos omezené delegování, kontaktujte řadiče domény před odesláním požadavku do back-endu. Konektory mají mezipaměť lístků protokolu Kerberos, ale v rušném prostředí může odezva řadičů domény ovlivnit výkon. Tento problém je častější pro konektory, které běží v Azure, ale komunikovat s řadiči domény, které jsou místní.

Další informace o optimalizaci sítě najdete v tématu [Důležité informace o topologii sítě při použití proxy aplikace služby Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Připojení k doméně

Konektory lze spustit v počítači, který není připojen k doméně. Pokud však chcete jednotné přihlašování (SSO) k aplikacím, které používají integrované ověřování systému Windows (IWA), potřebujete počítač spojený s doménou. V takovém případě musí být počítače konektoru připojeny k doméně, která může provádět delegování s omezeným protokolem [Kerberos](https://web.mit.edu/kerberos) jménem uživatelů pro publikované aplikace.

Konektory lze také připojit k doménám nebo doménovým strukturám, které mají částečný vztah důvěryhodnosti, nebo k řadičům domény jen pro čtení.

## <a name="connector-deployments-on-hardened-environments"></a>Nasazení konektorů v kalených prostředích

Nasazení konektoru je obvykle jednoduché a nevyžaduje žádnou speciální konfiguraci. Existují však některé jedinečné podmínky, které by měly být zváženy:

- Organizace, které omezují odchozí provoz, musí [otevřít požadované porty](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Počítače kompatibilní s FIPS může být nutné změnit jejich konfiguraci, aby procesy konektoru generovat a ukládat certifikát.
- Organizace, které uzamknou své prostředí na základě procesů, které vydávají síťové požadavky, musí zajistit, aby obě služby konektoru byly povoleny pro přístup ke všem požadovaným portům a IP adresy.
- V některých případech odchozí dopředu proxy může přerušit obousměrné ověřování certifikátu a způsobit selhání komunikace.

## <a name="connector-authentication"></a>Ověřování konektoru

Chcete-li poskytnout zabezpečenou službu, konektory musí ověřit směrem ke službě a služba musí ověřit směrem ke konektoru. Toto ověřování se provádí pomocí klientských a serverových certifikátů při zahájení připojení konektory. Tímto způsobem uživatelské jméno a heslo správce nejsou uloženy v počítači konektoru.

Použité certifikáty jsou specifické pro službu Proxy aplikace. Jsou vytvořeny během počáteční registrace a jsou automaticky obnovovány konektory každých pár měsíců.

Pokud konektor není připojen ke službě po dobu několika měsíců, jeho certifikáty mohou být zastaralé. V takovém případě odinstalujte a znovu nainstalujte konektor pro aktivaci registrace. Můžete spustit následující příkazy Prostředí PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Pod pokličkou

Konektory jsou založeny na serveru Proxy webových aplikací systému Windows Server, takže mají většinu stejných nástrojů pro správu, včetně protokolů událostí systému Windows

![Správa protokolů událostí pomocí Prohlížeče událostí](./media/application-proxy-connectors/event-view-window.png)

a čítače výkonu systému Windows.

![Přidání čítačů ke konektoru pomocí monitoru výkonu](./media/application-proxy-connectors/performance-monitor.png)

Konektory mají protokoly správce i relace. Protokoly správce obsahují klíčové události a jejich chyby. Protokoly relace obsahují všechny transakce a jejich podrobnosti o zpracování.

Chcete-li zobrazit protokoly, přejděte do Prohlížeče událostí, otevřete nabídku **Zobrazení** a povolte **zobrazit analytické a ladicí protokoly**. Potom jim umožněte začít shromažďovat události. Tyto protokoly se nezobrazují v proxy webové aplikaci v systému Windows Server 2012 R2, protože konektory jsou založeny na novější verzi.

Můžete zkontrolovat stav služby v okně Služby. Spojnice se skládá ze dvou služeb systému Windows: skutečný konektor a updater. Oba musí pořád utíkat.

 ![Příklad: Okno služby zobrazující místní služby Azure AD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Další kroky

- [Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md)
- [Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
- [Poradce při potížích s chybami proxy aplikace a konektoru](application-proxy-troubleshoot.md)
- [Jak tiše nainstalovat konektor proxy aplikací Azure AD](application-proxy-register-connector-powershell.md)
