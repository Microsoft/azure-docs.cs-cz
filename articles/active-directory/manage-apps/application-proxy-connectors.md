---
title: Pochopení konektory proxy aplikace služby Azure AD | Microsoft Docs
description: Popisuje základní informace o Azure AD Application Proxy konektory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 74e6428cf0536a7c8016be6cdf29071128bf4a3b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025685"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Pochopení konektory proxy aplikace služby Azure AD

Konektory jsou co umožňují proxy aplikace služby Azure AD. Jsou jednoduchá, snadno nasadit a udržovat a super výkonné. Tento článek popisuje, jaké konektory jsou, jak fungují a některé návrhy, jak k optimalizaci vašeho nasazení. 

## <a name="what-is-an-application-proxy-connector"></a>Co je konektor Proxy aplikace?

Konektory jsou lightweight agenti, kteří se nacházejí na místě a usnadnit tak odchozí připojení ke službě Proxy aplikace. Konektory musí být nainstalován v systému Windows Server, který má přístup k back-end aplikace. Konektory můžete uspořádat do skupin konektoru se všemi skupinami zpracování přenosů pro konkrétní aplikace. Vyrovnávání zatížení konektory automaticky a může pomoct za účelem optimalizace struktury vaší sítě. 

## <a name="requirements-and-deployment"></a>Požadavky a nasazení

Pokud chcete úspěšně nasadit Proxy aplikace, budete potřebovat minimálně jeden konektor, ale doporučujeme dvou nebo více větší odolnost. Konektor nainstalujte na počítač 2016 nebo Windows Server 2012 R2. Konektor musí být schopen komunikovat s Proxy aplikace služby a také místní aplikace, které budete publikovat. 

Další informace o síťové požadavky u serveru konektoru najdete v tématu [začít pracovat s Proxy aplikace a nainstalujte konektor](application-proxy-enable.md).

## <a name="maintenance"></a>Údržba
Konektory a služba postará o všech úloh vysoké dostupnosti. Dají se přidat nebo odebrat dynamicky. Pokaždé, když dorazí novou žádost o je směrována na jednu z konektory, které aktuálně nejsou k dispozici. Pokud konektor není dočasně k dispozici, není reagovat na tento provoz.

Konektory jsou bezstavové a mít žádné konfigurační data v počítači. Pouze data, která ukládají se nastavení připojení služby a její ověřovací certifikát. Při připojování ke službě, všechna požadovaná konfigurační data pro vyžádání obsahu a jej aktualizovat každých několik minut.

Konektory také dotazovat server a zjistěte, zda existuje novější verze konektoru. Pokud ho najde, konektory aktualizovat sami.

Vaše konektory z počítače, na kterém běží, můžete monitorovat pomocí protokolu událostí a čítače výkonu. Nebo můžete zobrazit jejich stav ze stránky Proxy aplikací na portálu Azure:

 ![Konektory Proxy aplikace AzureAD](./media/application-proxy-connectors/app-proxy-connectors.png)

Nemáte ručně odstranit konektory, které se nepoužívá. Konektor je spuštěna, zůstává aktivní jako připojení ke službě. Nepoužívané konektory jsou označené jako _neaktivní_ a jsou odebrány po 10 dnů nečinnosti. Pokud chcete odinstalovat konektor, ale odinstalujte službu konektoru i aktualizační službu ze serveru. Restartujte počítač, aby se službě úplně odebrala.

## <a name="automatic-updates"></a>Automatické aktualizace

Azure AD poskytuje funkce Automatické aktualizace pro všechny konektory, které nasadíte. Tak dlouho, dokud se službou Application Proxy Connector Updater, vaše konektory aktualizovat automaticky. Pokud nevidíte konektor aktualizační službu na serveru, budete muset [přeinstalování vašeho konektoru](application-proxy-enable.md) získat žádné aktualizace. 

Pokud nechcete čekat na automatické aktualizace se do vašeho konektoru, můžete provést ruční upgrade. Přejděte na [stránky pro stažení konektoru](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serveru, kde je vaše konektor najít a vyberte **Stáhnout**. Tento proces se spustí upgrade pro místní konektor. 

U klientů s více konektorů automatické aktualizace cíle jeden konektor v době v každé skupině, abyste zabránili výpadkům ve vašem prostředí. 

Výpadek se můžete setkat, když vaše konektor aktualizuje, pokud:  
- Můžete mít pouze jeden konektor. Pokud chcete tento výpadky a zvýšit vysokou dostupnost, doporučujeme nainstalovat druhý konektor a [vytvořte skupinu konektor](application-proxy-connector-groups.md).  
- Konektor se uprostřed transakce, když zahájil aktualizace. I když dojde ke ztrátě počáteční transakce, prohlížeč by měl automaticky opakujte operaci, nebo můžete aktualizovat stránku. Když je nutno žádost, provoz se směruje na zálohování konektor.

## <a name="creating-connector-groups"></a>Vytváření skupin konektoru

Konektor skupiny umožňují přiřadit konkrétní konektory poskytovat konkrétní aplikace. Můžete seskupit několik konektorů a potom přiřadit ke skupině pro každou aplikaci. 

Konektor skupiny bylo snazší spravovat nasazení ve velkých organizacích. Také zvyšují latence pro klienty, kteří mají aplikace hostované v různých oblastech, protože můžete vytvořit skupiny založené na umístění konektoru k obsluze pouze místní aplikace. 

Další informace o skupinách konektoru najdete v tématu [publikování aplikací na samostatných sítí a umístění pomocí konektoru skupiny](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Plánování kapacit 

Zatímco spojnic automatické vyvážení v rámci skupiny pro konektor zatížení, je také důležité se ujistit, že jste naplánovali dostatečnou kapacitu mezi konektory pro zpracování provozu očekávané. Obecně platí, tím víc uživatelů, které máte, větší a počítači, na kterém budete potřebovat. Níže je, že dokáže zpracovat tabulku poskytnutí přehledu ze svazku různé počítače. Poznámka: všechny pracuje na očekávané transakce za druhé (TPS) místo uživatelem od využití vzory lišit a nelze jej použít k předvídání zatížení.  Všimněte si, že budou některé rozdíly, na základě velikosti odpovědi a doba odezvy back-end aplikace – větší velikosti odpovědi a odezvy pomalejší, bude výsledkem nižší TPS Max.

|Jádra|Paměť RAM|Očekávaný latence (MS)-P99|Maximální počet TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|
\* Tento počítač měl připojení limit 800. Pro všechny ostatní počítače jsme použili výchozí limit 200 připojení.
 
>[!NOTE]
>Není k dispozici mnoho rozdíl v maximální TPS 4, 8 až 16 jádra počítače. Hlavní rozdíl mezi ty je očekávané latence.  

## <a name="security-and-networking"></a>Zabezpečení a sítě

Konektory lze nainstalovat kdekoli v síti, která vám umožní k odesílání požadavků na službu Proxy aplikace. Co je důležité je, že počítač také spuštěným konektorem má přístup k aplikacím. Konektory můžete nainstalovat v rámci vaší podnikové síti nebo na virtuální počítač, který běží v cloudu. Konektory lze spustit v rámci demilitarizovaná zóna (DMZ), ale není nutné, protože všechny přenosy je odchozí, takže zůstává zabezpečené sítě.

Konektory odeslat pouze odchozí požadavky. Odchozí přenosy se odesílají se službou Proxy aplikace a k publikovaným aplikacím. Nemáte otevřít příchozí porty, protože přenosy obou směrech po vytvoření relace. Nemáte nastavení vyrovnávání zátěže mezi konektory nebo nakonfigurujte příchozí přístup přes vaší brány firewall. 

Další informace o konfiguraci odchozí pravidla brány firewall najdete v tématu [pracují se stávající místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md).


## <a name="performance-and-scalability"></a>Výkon a škálovatelnost

Měřítko pro službu Proxy aplikace je transparentní, ale Škálováním je faktor pro konektory. Je potřeba mít dostatek konektory pro zpracování provozu mezi ve špičce. Ale nemusíte konfiguraci Vyrovnávání zatížení, protože všechny konektory v rámci skupiny pro konektor automatické vyvážení zatížení.

Vzhledem k tomu, že konektory jsou bezstavové, nejsou ovlivněny počet uživatelů a relací. Místo toho budou reagovat na počet požadavků a jejich velikost datové části. S standardní webových přenosů průměrná počítače může zpracovávat několik tisíců požadavků za sekundu. Konkrétní kapacitu, závisí na vlastnosti přesný počítače. 

Výkon konektoru je svázaná s procesoru a sítě. Výkon procesorů je potřeba pro SSL šifrování a dešifrování, zatímco sítě je důležité získat rychlého připojení k aplikacím a online služby v Azure.

Naproti tomu paměti je menší problém pro konektory. Velká část zpracování a veškerý provoz neověřené postará online služby. Vše, co můžete udělat v cloudu se provádí v cloudu. 

Vyrovnávání zatížení se stane mezi konektory skupiny daný konektor. Provedeme varianta kruhové dotazování k určení, které konektor ve skupině slouží určité žádosti. Pokud z nějakého důvodu nedostupný konektor nebo počítač, provoz se začne přejdete na jiný konektor ve skupině. Tato odolnosti je také proč doporučujeme mít více konektorů.

Dalším faktorem, který ovlivňuje výkon je kvality sítě mezi konektorů, včetně: 

* **Online služby**: pomalá nebo vysokou latencí připojení k Proxy aplikace služby v Azure vliv na výkon konektoru. Nejlepšího výkonu dosáhnete připojení k Azure s Express Route vaší organizace. Jinak máte tým sítí, ujistěte se, že jsou co možná nejefektivnější zajistit připojení k Azure. 
* **Back-end aplikace**: V některých případech jsou další servery proxy mezi konektor a back-end aplikace, které může zpomalit nebo zabránění připojení. Tento scénář lze vyřešit, otevřete prohlížeč ze serveru konektoru a pokuste se získat přístup k aplikaci. Pokud spustíte konektory v Azure, ale aplikace jsou na místě, nemusí být prostředí očekávat vaši uživatelé.
* **Řadiče domény**: Pokud konektory provést jednotné přihlašování pomocí omezené delegování Kerberos, se před odesláním požadavku na back-end kontaktovat řadiče domény. Konektory mají mezipaměť lístky protokolu Kerberos, ale v prostředí zaneprázdněn odezvy řadičů domény může ovlivnit výkon. Tento problém je dnes běžné pro konektory, které spustit v Azure, ale komunikovat s řadiči domény, které jsou na místě. 

Další informace o optimalizaci sítě najdete v tématu [aspekty topologie sítě, při použití aplikace Proxy Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Připojení k doméně

Konektory lze spustit na počítači, který není připojený k doméně. Pokud chcete jednotné přihlašování (SSO) k aplikacím, které používají integrované ověřování systému Windows (IWA), ale musíte na počítači připojeném k doméně. V takovém případě musí být konektor počítače připojené k doméně, která může provádět [Kerberos](https://web.mit.edu/kerberos) omezené delegování jménem uživatelů pro publikování aplikace.

Konektory může být taky připojen do domény nebo doménové struktury, které mají částečný vztah důvěryhodnosti, nebo řadiče domény jen pro čtení.

## <a name="connector-deployments-on-hardened-environments"></a>Nasazení konektoru na posílené prostředí

Obvykle nasazení konektoru je jednoduchý a nepotřebuje žádnou zvláštní konfiguraci. Existují však některé jedinečné podmínky, které musí vzít v úvahu:

* Organizace, které omezit odchozí přenosy musí [otevřete požadované porty](application-proxy-enable.md#open-your-ports).
* Počítače kompatibilní se standardem FIPS, bude pravděpodobně nutné změnit jejich konfigurace povolit konektor procesy generovat a ukládat certifikát.
* Organizace, které zamknout jejich prostředí na základě procesů, které vydávají požadavky sítě mají a ujistěte se, že jsou povoleny obě služby konektoru pro přístup k všechny požadované porty a adresy IP.
* V některých případech může odchozí dopředného proxy přerušení ověřování obousměrný certifikátu a způsobit selhání komunikace.

## <a name="connector-authentication"></a>Konektor ověřování

K poskytování zabezpečeného služby, konektory mít k ověření na službu a službu má k ověření směrem k konektor. Toto ověřování se provádí pomocí certifikátů klienta a serveru při konektory inicializuje připojení. Tímto způsobem uživatelské jméno a heslo správce nejsou uložené na počítači konektor.

Certifikáty jsou specifické pro službu Proxy aplikace. Budou vytvořeny při počáteční registraci a jsou automaticky obnovena konektory každých několik měsíců. 

Pokud konektor není připojený ke službě několik měsíců, může být zastaralý svoje certifikáty. V takovém případě odinstalovat a znovu nainstalujte konektor aktivace registrace. Můžete spustit následující příkazy prostředí PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Pod pokličkou

Konektory jsou založené na Windows Server služby Proxy webových aplikací, takže mají většinu stejné nástroje pro správu včetně protokoly událostí systému Windows

 ![Správa protokolů událostí se v prohlížeči událostí](./media/application-proxy-connectors/event-view-window.png)

a čítačů výkonu systému Windows. 

 ![Přidání čítačů do konektor s nástrojem Sledování výkonu](./media/application-proxy-connectors/performance-monitor.png)

Konektory jste správce a relace protokoly. Správce protokoly obsahovat klíče události a jejich chyby. Protokoly relace obsahovat všechny transakce a jejich zpracování podrobnosti. 

Pokud chcete zobrazit protokoly, přejděte do prohlížeče událostí, otevřete **zobrazení** nabídky a povolit **ukazují analytické a ladicí protokoly**. Pak je spustit shromažďování událostí povolte. Tyto protokoly se nezobrazí v Proxy webových aplikací ve Windows serveru 2012 R2 jako konektory jsou založené na novější verzi.

Můžete zkontrolovat stav služby v okně služby. Tento konektor zahrnuje dvě služby systému Windows: skutečný konektor a aktualizačního programu. Oba dva musí běžet vždy.

 ![Místní služby AzureAD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Další postup


* [Publikování aplikací na samostatných sítí a umístění pomocí konektoru skupin](application-proxy-connector-groups.md)
* [Práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
* [Řešení potíží s Proxy aplikace a konektor chyby](application-proxy-troubleshoot.md)
* [Postup při bezobslužné instalaci konektoru Proxy aplikace Azure AD](application-proxy-register-connector-powershell.md)

