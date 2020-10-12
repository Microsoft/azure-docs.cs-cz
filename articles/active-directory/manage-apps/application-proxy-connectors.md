---
title: Vysvětlení konektorů služby Azure Proxy aplikací služby AD | Microsoft Docs
description: Přečtěte si o konektorech Azure Proxy aplikací služby AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f14c757df8bcc38bf226cb6346c400087c2d7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319822"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Vysvětlení konektorů Azure Proxy aplikací služby AD

Konektory jsou to, co umožňuje Azure Proxy aplikací služby AD. Jsou jednoduché a snadno se nasazují a udržují a velmi výkonné. Tento článek popisuje, co jsou konektory, jak fungují, a některé návrhy, jak optimalizovat nasazení.

## <a name="what-is-an-application-proxy-connector"></a>Co je konektor proxy aplikací?

Konektory jsou odlehčené agenti, kteří jsou místně a usnadňují odchozí připojení ke službě proxy aplikací. Konektory musí být nainstalované na Windows serveru, který má přístup k back-endové aplikaci. Konektory můžete uspořádat do skupin konektorů, přičemž každá skupina zpracovává provoz na konkrétní aplikace.

## <a name="requirements-and-deployment"></a>Požadavky a nasazení

K úspěšnému nasazení proxy aplikace potřebujete aspoň jeden konektor, ale pro větší odolnost doporučujeme dvě nebo víc. Nainstalujte konektor na počítač se systémem Windows Server 2012 R2 nebo novějším. Konektor musí komunikovat se službou proxy aplikací a místními aplikacemi, které publikujete.

### <a name="windows-server"></a>Windows Server
Potřebujete server se systémem Windows Server 2012 R2 nebo novějším, na kterém můžete nainstalovat konektor proxy aplikací. Server se musí připojit k aplikačním proxy službám v Azure a místním aplikacím, které publikujete.

Aby mohl Windows Server nainstalovat konektor proxy aplikací, musí mít povolený protokol TLS 1,2. Povolení TLS 1,2 na serveru:

1. Nastavte následující klíče registru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restartování serveru

Další informace o požadavcích na síť pro server konektoru najdete v tématu Začínáme [s proxy aplikací a instalace konektoru](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Údržba

Konektory a služba se budou starat o všechny úlohy s vysokou dostupností. Dají se dynamicky přidávat nebo odebírat. Pokaždé, když přijde nový požadavek, se směruje na jeden z aktuálně dostupných konektorů. Pokud je konektor dočasně nedostupný, nereaguje na tento provoz.

Konektory jsou bezstavové a nemají v počítači žádná konfigurační data. Jedinými uloženými daty jsou nastavení pro připojení služby a jejího ověřovacího certifikátu. Když se připojí ke službě, vyžádají všechna požadovaná konfigurační data a aktualizují je každých několik minut.

Konektory se také dotazují na server, aby zjistili, zda je k dispozici novější verze konektoru. Pokud je jeden nalezen, konektory se aktualizují samy.

Konektory můžete monitorovat z počítače, na kterém běží, pomocí protokolu událostí a čítačů výkonu. Můžete také zobrazit jejich stav na stránce proxy aplikace Azure Portal:

![Příklad: konektory Azure Proxy aplikací služby AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Nemusíte ručně odstraňovat nepoužité konektory. Když je konektor spuštěný, zůstane aktivní, protože se ke službě připojí. Nepoužívané konektory jsou označené jako _neaktivní_ a po 10 dnech nečinnosti se odeberou. Pokud chcete odinstalovat konektor, ale odinstalujte službu konektoru i službu aktualizace ze serveru. Restartujte počítač, aby se službě úplně odebrala.

## <a name="automatic-updates"></a>Automatické aktualizace

Azure AD poskytuje automatické aktualizace pro všechny nasazené konektory. Pokud je spuštěná služba proxy aplikací Aktualizátor konektorů, konektory se automaticky aktualizují. Pokud se služba Aktualizátor konektorů na serveru nezobrazuje, je nutné [přeinstalovat konektor](application-proxy-add-on-premises-application.md) , aby se získaly nějaké aktualizace.

Pokud nechcete čekat na připojení automatické aktualizace k vašemu konektoru, můžete provést ruční upgrade. Na serveru, kde se nachází váš konektor, na [stránce pro stažení konektoru](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) klikněte na **Stáhnout**. Tento proces zahájí upgrade místního konektoru.

Pro klienty s více konektory se automaticky aktualizují jednotlivé konektory v každé skupině, aby nedocházelo k výpadkům ve vašem prostředí.

V případě, že se konektor aktualizuje, může docházet k výpadkům:
  
- Máte jenom jeden konektor, doporučujeme nainstalovat druhý konektor a [vytvořit skupinu konektorů](application-proxy-connector-groups.md). Tím se vyhnete výpadkům a zajistíte vyšší dostupnost.  
- Konektor byl uprostřed transakce při zahájení aktualizace. I když dojde ke ztrátě počáteční transakce, bude by váš prohlížeč tuto operaci automaticky opakovat nebo můžete stránku aktualizovat. Po opětovném odeslání žádosti se přenos směruje do záložního konektoru.

Informace o dříve vydaných verzích a o tom, jaké změny zahrnuje, najdete v tématu o [verzi proxy aplikací – Historie verzí](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Vytváření skupin konektorů

Skupiny konektorů umožňují přiřadit konkrétní konektory pro poskytování konkrétních aplikací. Můžete seskupit několik konektorů a pak každou aplikaci přiřadit ke skupině.

Skupiny konektorů usnadňují správu rozsáhlých nasazení. Zlepšují také latenci pro klienty s aplikacemi hostovanými v různých oblastech, protože můžete vytvořit skupiny konektorů založené na poloze pro poskytování pouze místních aplikací.

Další informace o skupinách konektorů najdete v tématu [publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Plánování kapacity

Je důležité zajistit, aby bylo naplánováno dostatečné množství mezi konektory pro zpracování očekávaného objemu přenosů dat. Doporučujeme, aby každá skupina konektorů měla aspoň dva konektory, aby se zajistila vysoká dostupnost a škálování. Pro případ, že budete chtít mít k dispozici tři konektory, může být nutné provozovat počítač v jakémkoli bodě.

Obecně platí, že čím více uživatelů máte, tím větší je počítač, který budete potřebovat. Níže je tabulka, která poskytuje přehled o svazku a očekávané latence, které můžou zpracovat různé počítače. Všimněte si, že je vše založené na očekávaných transakcích za sekundu (TPS), a ne na uživatele, protože se vzorce používání liší a nelze je použít k předpovědi zátěže. V závislosti na velikosti odpovědí a velikosti odezvy back-end aplikace a delší dobu odezvy budou také k dispozici nižší maximální TPS. Doporučujeme také mít další počítače, aby distribuované zatížení mezi počítači vždy poskytovalo rozsáhlou vyrovnávací paměť. Kapacita navíc zajistí vysokou dostupnost a odolnost.

|Cores|Paměť RAM|Očekávaná latence (MS) – p99|Maximální počet TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200 *|

\* Tento počítač použil vlastní nastavení k vyvolání některých výchozích omezení připojení nad rámec doporučených nastavení .NET. Doporučujeme spustit test s výchozím nastavením předtím, než se obrátíte na podporu, aby se tento limit u vašeho tenanta změnil.

> [!NOTE]
> Maximální počet TPS počítačů mezi 4, 8 a 16 jádry není velký. Hlavní rozdíl mezi nimi je v očekávané latenci.
>
> Tato tabulka se zaměřuje taky na očekávaný výkon konektoru na základě typu počítače, na kterém je nainstalovaný. To je oddělené od limitů omezování služby proxy aplikací, a to v tématu omezení [a omezení služby](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Zabezpečení a sítě

Konektory lze nainstalovat kdekoli v síti, což jim umožňuje odesílat požadavky na službu proxy aplikací. Důležité je, aby počítač se spuštěným konektorem měl také přístup k vašim aplikacím. Můžete nainstalovat konektory v podnikové síti nebo ve virtuálním počítači, který běží v cloudu. Konektory se můžou spouštět v hraniční síti, označované taky jako demilitarizovaná zóna (DMZ), ale není to nutné, protože veškerý provoz je odchozí, takže vaše síť zůstane v bezpečí.

Konektory odesílají pouze odchozí požadavky. Odchozí přenosy se odesílají do služby proxy aplikací a do publikovaných aplikací. Nemusíte otevírat příchozí porty, protože přenos toků sestaví oba způsoby relace. Nemusíte také konfigurovat příchozí přístup přes brány firewall.

Další informace o konfiguraci odchozích pravidel brány firewall najdete v tématu [práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Výkon a škálovatelnost

Škálování pro službu proxy aplikací je transparentní, ale škálování je faktor pro konektory. Aby bylo možné zvládnout špičku provozu, je nutné mít dostatek konektorů. Vzhledem k tomu, že konektory jsou bezstavové, nejsou ovlivněny počtem uživatelů ani relací. Místo toho reagují na počet požadavků a jejich velikost datové části. V případě standardního webového provozu může průměrný počítač zpracovat několik tisíc požadavků za sekundu. Konkrétní kapacita závisí na přesných charakteristikách počítače.

Výkon konektoru je vázán pomocí CPU a sítě. K šifrování a dešifrování TLS se vyžaduje výkon procesoru, zatímco síť je důležité k rychlému připojení k aplikacím a online službě v Azure.

Naproti tomu paměť je menší než problém konektorů. Online služba se stará o většinu zpracování a veškerého neověřeného provozu. Všechno, co se dá udělat v cloudu, se provádí v cloudu.

Pokud z nějakého důvodu nebude konektor nebo počítač nedostupný, bude se provoz spouštět do jiného konektoru ve skupině. Tato odolnost je také důvod, proč doporučujeme mít více konektorů.

Dalším faktorem, který ovlivňuje výkon, je kvalita sítě mezi konektory, včetně:

- **Online služba**: pomalé nebo vysoké latence připojení ke službě proxy aplikací v Azure ovlivňují výkon konektoru. Pro nejlepší výkon připojte svoji organizaci k Azure pomocí expresní trasy. V opačném případě váš síťový tým zajistí, aby připojení k Azure byla zpracovávána co nejúčinnější.
- **Back-endové aplikace**: v některých případech existují další proxy servery mezi konektorem a back-end aplikacemi, které mohou zpomalit nebo zakázat připojení. Chcete-li vyřešit tento scénář, otevřete prohlížeč ze serveru konektoru a pokuste se o přístup k aplikaci. Pokud konektory spouštíte v Azure, ale aplikace jsou v místním prostředí, může to být tím, že uživatelé neočekávají.
- **Řadiče domény**: Pokud konektory provádějí jednotné přihlašování (SSO) pomocí omezeného delegování protokolu Kerberos, před odesláním žádosti do back-endu se obraťte na řadiče domény. Konektory mají mezipaměť lístků protokolu Kerberos, ale v případě zaneprázdněného prostředí může mít vliv na výkon řadiče domény rychlost odezvy. Tento problém je běžnější pro konektory, které běží v Azure, ale komunikují s řadiči domény, které jsou místně.

Další informace o optimalizaci sítě najdete v tématu [požadavky na topologii sítě při použití proxy aplikací služby Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Připojení k doméně

Konektory lze spustit na počítači, který není připojený k doméně. Pokud ale chcete jednotné přihlašování (SSO) k aplikacím, které používají integrované ověřování systému Windows (IWA), potřebujete počítač připojený k doméně. V takovém případě musí být počítače konektoru připojené k doméně, která může provést omezené delegování [protokolu Kerberos](https://web.mit.edu/kerberos) jménem uživatelů pro publikované aplikace.

Konektory je také možné připojit k doménám v doménových strukturách s částečným vztahem důvěryhodnosti nebo řadiči domény jen pro čtení.

## <a name="connector-deployments-on-hardened-environments"></a>Nasazení konektorů v zesílených prostředích

Nasazení konektoru je obvykle jednoduché a nevyžaduje žádnou zvláštní konfiguraci. Existují však některé jedinečné podmínky, které by měly být zváženy:

- Organizace, které omezují odchozí provoz, musí [otevřít požadované porty](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Aby mohly procesy konektoru generovat a ukládat certifikát, můžou se vyžadovat počítače kompatibilní se standardem FIPS.
- Organizace, které své prostředí zamkne na základě procesů, které vydávají požadavky na sítě, musí zajistit, aby obě služby konektoru měly povolený přístup ke všem požadovaným portům a IP adresám.
- V některých případech můžou odchozí předávací servery proxy poškodit obousměrné ověřování certifikátů a způsobit selhání komunikace.

## <a name="connector-authentication"></a>Ověřování konektoru

Aby bylo možné zajistit zabezpečenou službu, musí se konektory ověřovat směrem ke službě a služba musí být ověřena směrem k konektoru. Toto ověřování se provádí pomocí certifikátů klienta a serveru, když konektory iniciují připojení. Tímto způsobem se na počítači konektoru neukládají uživatelské jméno a heslo správce.

Používané certifikáty jsou specifické pro službu proxy aplikací. Vytvářejí se během počáteční registrace a automaticky se v nich obnovují konektory každých pár měsíců.

Po prvním úspěšném obnovení certifikátu nemá služba Azure Proxy aplikací služby AD Connector (síťová služba) oprávnění k odebrání starého certifikátu z úložiště místního počítače. Pokud vypršela platnost certifikátu nebo už ho služba nepoužívá, můžete ho bezpečně odstranit.

Aby se zabránilo problémům s obnovením certifikátu, zajistěte, aby byla povolena síťová komunikace z konektoru s [dokumentovanými cíli](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment) .

Pokud konektor není po několik měsíců připojený ke službě, můžou být jeho certifikáty zastaralé. V takovém případě odinstalujte a znovu nainstalujte konektor, aby se aktivovala registrace. Můžete spustit následující příkazy PowerShellu:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```
Další informace o tom, jak ověřit certifikát a řešit problémy, najdete v tématu [ověření podpory součástí počítačů a back-endu pro certifikát důvěryhodnosti proxy aplikací](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Pod pokličkou

Konektory jsou založené na proxy webových aplikací Windows serveru, takže mají většinu stejných nástrojů pro správu, včetně protokolů událostí systému Windows.

![Správa protokolů událostí pomocí Prohlížeč událostí](./media/application-proxy-connectors/event-view-window.png)

a čítače výkonu systému Windows.

![Přidání čítačů do konektoru pomocí nástroje sledování výkonu](./media/application-proxy-connectors/performance-monitor.png)

Konektory mají protokoly **správců** i **relací** . Protokol **správce** zahrnuje klíčové události a jejich chyby. Protokol **relace** zahrnuje všechny transakce a jejich podrobnosti o zpracování.

Pokud chcete zobrazit protokoly, otevřete **Prohlížeč událostí** a přejděte do části **protokoly aplikací a služeb**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector**. Chcete-li zobrazit protokol **relace** , vyberte v nabídce **zobrazení** možnost **Zobrazit protokoly o analýze a ladění**. Protokol **relace** se obvykle používá k odstraňování potíží a je ve výchozím nastavení zakázán. Umožněte, aby bylo možné zahájit shromažďování událostí a zakázat je, když už je nepotřebujete.

Stav služby můžete prostudovat v okně služby. Konektor je tvořen dvěma službami systému Windows: skutečným konektorem a aktualizačním rozhraním. Oba z nich musí běžet po celou dobu.

 ![Příklad: okno služby zobrazující místní služby Azure AD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Další kroky

- [Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md)
- [Práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
- [Řešení chyb proxy aplikací a konektorů](application-proxy-troubleshoot.md)
- [Postup při tiché instalaci konektoru služby Azure Proxy aplikací služby AD](application-proxy-register-connector-powershell.md)
