---
title: Řešení potíží s koncovými body Azure CDN, které vrátí stavový kód 404 | Dokumentace Microsoftu
description: Řešení potíží s 404 kódy odpovědí s koncovými body Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 66ee211856bb451caad7af02103aa306d76e8f97
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916270"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Řešení potíží s koncovými body Azure CDN, které vrátí stavový kód 404
V tomto článku můžete řešení problémů s koncovými body Azure Content Delivery Network (CDN), které vracejí 404 kódy stavu odpovědi HTTP.

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="symptom"></a>Příznak
Vytvoření profilu CDN a koncový bod, ale zřejmě není váš obsah bude dostupný v síti CDN. Uživatelé, kteří se pokusí o přístup k vašemu obsahu přes adresu URL CDN obdrží stavový kód HTTP 404. 

## <a name="cause"></a>Příčina
Existuje několik možných příčin, včetně:

* Původ souboru není viditelné pro CDN.
* Koncový bod není správně nakonfigurované, způsobí CDN vás pod rouškou ve špatné místo.
* Hostitel zamítá hlavičku hostitele z CDN.
* Koncový bod neměl čas potřebný k šíření v rámci CDN.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
> [!IMPORTANT]
> Po vytvoření koncového bodu CDN, ho nebude hned dostupný pro použití, jak může zabrat určitý čas pro se registrace rozšíří v rámci CDN:
> - V případě profilů **Azure CDN Standard od Microsoftu** je šíření obvykle hotové během deseti minut. 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 90 minut. 
> 
> Pokud se vám přesto obdržíte kód odpovědi 404, proveďte kroky v tomto dokumentu zvažte, zda nevyčkat pár hodin zkontrolovat znovu před otevřením lístku podpory.
> 
> 

### <a name="check-the-origin-file"></a>Zkontrolujte zdrojový soubor
Nejprve ověřte, že soubor do mezipaměti je k dispozici na původním serveru a je veřejně přístupný na Internetu. Nejrychlejší způsob, jak to udělat, je otevřít prohlížeč v privátním nebo anonymní relaci a přejděte přímo do souboru. Zadejte nebo vložte adresu URL do pole adresy a ověřte, že výsledky v souboru, který očekáváte. Předpokládejme například, když máte soubor v účtu služby Azure Storage, přístupná na adrese https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Pokud se dají úspěšně načíst obsah tohoto souboru, projde testem.

![Výborně!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> I když je to nejrychlejší a nejjednodušší způsob, jak ověřit váš soubor je veřejně dostupný, některé konfigurace sítě ve vaší organizaci může usnadnit, který se zobrazí soubor je veřejně dostupný, když je ve skutečnosti jenom viditelné pro uživatele sítě (i v případě, že je hostován v Azure). Aby bylo zajištěno, že to není tento případ, testovací soubor s externím prohlížeči, jako jsou mobilní zařízení, které není připojené k síti vaší organizace, nebo virtuální počítač v Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Zkontrolujte nastavení počátku
Po ověření, že soubor je veřejně dostupný na Internetu, ověřte nastavení původu. V [webu Azure Portal](https://portal.azure.com), přejděte na svůj profil CDN a vyberte koncový bod řešíte potíže. Z výsledné **koncový bod** vyberte původu.  

![Koncový bod stránku původu zvýrazněnou](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**Původu** se zobrazí stránka. 

![Stránku původu](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ zdroje a název hostitele
Ověřte, že hodnoty **typ počátku** a **název počátečního hostitele** jsou správné. V tomto příkladu https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, část hostname adresy URL je *cdndocdemo.blob.core.windows.net*, která je správná. Protože zdroje služby Azure Storage, webové aplikace a cloudové služby použijte hodnotu rozevíracího seznamu **název počátečního hostitele** pole, nesprávná slova nejsou chyby. Ale pokud používáte vlastní původ, ujistěte se, že není správně napsaný název hostitele vašeho.

#### <a name="http-and-https-ports"></a>Porty HTTP a HTTPS
Zkontrolujte vaše **HTTP** a **portů HTTPS**. Ve většině případů 80 a 443 jsou správné a se nevyžadují žádné změny.  Ale pokud zdrojový server naslouchá na jiném portu, který muset uváděny zde. Pokud si nejste jistí, podívejte se na adresu URL pro zdrojový soubor. Specifikace protokolu HTTP a HTTPS použijte jako výchozí porty 80 a 443. V příkladu adresu URL https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, port není zadaný, předpokládá se výchozí hodnota 443 a správné nastavení.  

Nicméně, Předpokládejme, že adresa URL pro zdrojový soubor, který jste dříve, je http:\//www.contoso.com:8080/file.txt. Poznámka: *: 8080* část na konci segmentu názvu hostitele. Že číslo dostane pokyn, aby port 8080 slouží k připojení na webový server www\.contoso.com, proto je potřeba zadat *8080* v **HTTP port** pole. Je důležité si uvědomit, že tato nastavení portu ovlivňují jenom jaké port použitý koncovým bodem k načtení informací ze zdroje.

> [!NOTE]
> **Azure CDN Standard od Akamai** koncových bodů nejsou povoleny plný rozsah portů TCP pro zdroje.  Seznam nepovolených portů původu najdete v tématu [Povolené porty původu Azure CDN společnosti Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Zkontrolujte nastavení koncového bodu
Na **koncový bod** stránky, vyberte **konfigurovat** tlačítko.

![Stránka koncový bod se zvýrazněným tlačítkem konfigurace](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Koncový bod CDN **konfigurovat** se zobrazí stránka.

![Konfigurace stránky](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoly
Pro **protokoly**, ověřte, že je vybrán protokol používá klienti důvěřují. Vzhledem k tomu, že klient používá stejný protokol se bude používat pro přístup k počátku, je důležité mít počáteční porty správně nakonfigurovaná v předchozí části. Koncový bod CDN naslouchá jenom výchozích portech HTTP a HTTPS (80 a 443), bez ohledu na porty původu.

Vraťme naše hypotetické příkladu pomocí protokolu http:\//www.contoso.com:8080/file.txt.  Jak budete pamatovat, Contoso zadaný *8080* jako jejich protokol HTTP port, ale také Předpokládejme, že jsou zadané *44300* jako jejich port HTTPS.  Pokud se vytvořil koncový bod s názvem *contoso*, jejich název hostitele koncového bodu CDN bude *contoso.azureedge.net*.  Požadavek protokolu http:\//contoso.azureedge.net/file.txt je požadavek HTTP, takže koncový bod by prostřednictvím protokolu HTTP na portu 8080 načítat z původního zdroje.  Zabezpečení požadavku prostřednictvím protokolu HTTPS, https: \/ /contoso.azureedge.net/file.txt, by způsobilo koncový bod pro použití protokolu HTTPS na portu 44300 při načítání souboru z původního zdroje.

#### <a name="origin-host-header"></a>Hlavička počátečního hostitele
**Hlavička počátečního hostitele** je hodnota hlavičky hostitele posílaná zdroji s každou žádostí.  Ve většině případů to by měl být stejný jako **název počátečního hostitele** jsme ověřit dříve.  Nesprávná hodnota v tomto poli nezpůsobí obecně vracejí stav 404, ale by mohla způsobit, že ostatní stavy 4xx, v závislosti na tom, co se očekává původu.

#### <a name="origin-path"></a>Cesta k počátku
Nakonec jsme měli ověřit naše **cesta k počátku**.  Ve výchozím nastavení je toto pole prázdné.  Toto pole byste měli používat, jenom Pokud chcete-li zúžit rozsah původu hostované prostředky, které chcete zpřístupnit v síti CDN.  

V příkladu koncový bod, chceme, aby všechny prostředky v účtu úložiště k dispozici, takže **cesta ke zdroji** je ponecháno prázdné.  To znamená, že žádost o https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt vede z koncového bodu připojení k cdndocdemo.core.windows.net, který požaduje */publicblob/lorem.txt*.  Podobně, žádost o https:\//cdndocdemo.azureedge.net/donotcache/status.png výsledkem požadavku koncového bodu */donotcache/status.png* z původního zdroje.

Ale co když nechcete používat síť CDN pro každou cestu na váš původní název?  Řekněme, že chcete vystavit *publicblob* cestu.  Pokud jsme zadejte */publicblob* v **cesta k počátku** pole, která způsobí, že koncový bod pro vložení */publicblob* před každým požadavkem k počátku.  To znamená, že požadavku pro protokol https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt bude nyní ve skutečnosti trvat požadavek část adresy URL, */publicblob/lorem.txt*a připojit */publicblob* na začátek. Výsledkem je žádost o */publicblob/publicblob/lorem.txt* z původního zdroje.  Pokud tato cesta se nepřekládá na skutečný soubor, vrátí počátek stavem 404.  Správnou adresu URL pro načtení lorem.txt v tomto příkladu by ve skutečnosti https:\//cdndocdemo.azureedge.net/lorem.txt.  Všimněte si, že jsme nezahrnují */publicblob* cesta vůbec, protože je požadavek část adresy URL */lorem.txt* a přidá koncový bod */publicblob*výsledkem */publicblob/lorem.txt* žádost předávaný ke zdroji.

