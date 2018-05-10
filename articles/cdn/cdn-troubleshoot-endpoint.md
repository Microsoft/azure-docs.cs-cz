---
title: Řešení potíží s koncovými body Azure CDN, které vracejí stav 404 kódu | Microsoft Docs
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
ms.openlocfilehash: 1cffef5bbda475032ee7ff07188ab0d9d52846ea
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Řešení potíží s koncovými body Azure CDN, které vracejí stav 404 kódu
Tento článek vám umožňuje řešení problémů s koncovými body Azure Content Delivery Network (CDN), které vracejí 404 kódy stavu odpovědi HTTP.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [MSDN Azure a fóra Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="symptom"></a>Příznaky
Vytvoření profilu CDN a koncového bodu, ale asi není k dispozici na CDN svůj obsah. Uživatelé, kteří se pokusí o přístup k vašemu obsahu prostřednictvím adresy URL CDN zobrazí kód stavu HTTP 404. 

## <a name="cause"></a>Příčina
Existuje několik možných příčin, včetně:

* Původ souboru není viditelná pro CDN.
* Koncový bod není správně nakonfigurované, způsobuje od CDN k podívejte se na nesprávné místo.
* Hostitel zamítá hlavičku hostitele z CDN.
* Koncový bod neproběhla čas potřebný k šíření v rámci CDN.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
> [!IMPORTANT]
> Po vytvoření koncového bodu CDN, nebude okamžitě k dispozici pro použití, jak trvá, než se registrace rozšíří v rámci CDN:
> - Pro **Azure CDN Standard od společnosti Microsoft** profily, šíření obvykle dokončení dobu 10 minut. 
> - Pro **Azure CDN Standard od společnosti Akamai** profily, šíření obvykle dokončení během jedné minuty. 
> - Pro **Azure CDN Standard od společnosti Verizon** a **Azure CDN Premium od společnosti Verizon** profily, šíření obvykle dokončení během 90 minut. 
> 
> Pokud je provést kroky v tomto dokumentu a stále vám kód odpovědi 404, zvažte čekání na pár hodin zkontrolovat znovu před otevřením lístku podpory.
> 
> 

### <a name="check-the-origin-file"></a>Zkontrolujte k původnímu souboru
Nejprve ověří, že soubor do mezipaměti je k dispozici na původním serveru a je veřejně přístupné z Internetu. Otevřete prohlížeč v privátní nebo incognito relace a přejděte přímo k souboru je nejrychlejší způsob, jak to udělat. Zadejte nebo vložte adresu URL do pole Adresa a ověřte, že výsledky v souboru, které očekáváte. Předpokládejme například, máte souboru v účtu Azure Storage, přístupná na https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Pokud se můžete úspěšně načíst obsah tohoto souboru, projde testem.

![Výborně!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> To je nejrychlejší a nejjednodušší způsob ověření souboru je veřejně dostupné, některé konfigurace sítě ve vaší organizaci může způsobit, že se zobrazí, který soubor je veřejně dostupné, pokud je, ve skutečnosti jenom viditelné uživatelům sítě (i když je hostován v Azure). Aby se zajistilo, že to není případě, test soubor s externího prohlížeče, jako je mobilní zařízení, který není připojen k síti vaší organizace, nebo virtuálního počítače v Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Zkontrolujte nastavení pro počátek
Jakmile ověříte, že je soubor veřejně dostupné v síti internet, ověřte nastavení původu. V [portálu Azure](https://portal.azure.com), přejděte na svůj profil CDN a vybrat koncový bod se řešení potíží. Ve výsledném **koncový bod** Vyberte počátek.  

![Stránka koncový bod se zvýrazněnou počátek](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**Původu** se zobrazí stránka. 

![Stránka počátek](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ původu a název hostitele
Ověřte, že hodnoty **typ původu** a **název počátečního hostitele** jsou správné. V tomto příkladu https:\/cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, část názvu hostitele adresy URL je *cdndocdemo.blob.core.windows.net*, který je správný. Protože Azure Storage, webové aplikace a cloudové služby zdroje používají hodnotu rozevíracího seznamu **název počátečního hostitele** pole, nesprávná slova nejsou problém. Pokud používáte vlastní původ, zkontrolujte však pravopisné vašeho názvu hostitele.

#### <a name="http-and-https-ports"></a>Porty HTTP a HTTPS
Zkontrolujte vaše **HTTP** a **HTTPS porty**. Ve většině případů 80 a 443 jsou správné, a bude vyžadovat žádné změny.  Ale pokud je zdrojový server naslouchá na jiný port, který potřebovat a nelze je zde. Pokud si nejste jistí, zobrazte adresu URL pro zdrojový soubor. Specifikace protokolu HTTP a HTTPS používání portů 80 a 443 jako výchozí hodnoty. V adrese URL příklad https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, není port určen, tak, aby se předpokládá výchozí hodnota 443 a správnost nastavení.  

Předpokládejme však, že adresa URL pro zdrojový soubor, který jste dříve neotestovali je http:\//www.contoso.com:8080/file.txt. Poznámka: *: 8080* část na konci tohoto segmentu název hostitele. Že číslo dostane pokyn, aby port 8080 slouží k připojení k webovému serveru na www.contoso.com, proto je potřeba zadat *8080* v **HTTP port** pole. Je důležité si uvědomit, že tato nastavení portu ovlivňují jenom jaké port koncového bodu se používá k načtení informací z tohoto počátku.

> [!NOTE]
> **Azure CDN Standard od Akamai** koncové body zakázat u původu plný rozsah portu TCP.  Seznam nepovolených portů původu najdete v tématu [Povolené porty původu Azure CDN společnosti Akamai](https://msdn.microsoft.com/library/mt757337.aspx).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Zkontrolujte nastavení koncového bodu
Na **koncový bod** vyberte **konfigurace** tlačítko.

![Stránka koncový bod se zvýrazněnou tlačítka Konfigurovat](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Koncový bod CDN **konfigurace** se zobrazí stránka.

![Konfigurace stránky](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoly
Pro **protokoly**, ověřte, zda je vybrán protokol používá klienty. Stejný protokol používaná klientem je používaný pro přístup k počátku, a proto je důležité mít portů původu správně nakonfigurovaná v předchozí části. Koncový bod CDN naslouchá jenom na výchozí porty HTTP a HTTPS (80 a 443), bez ohledu na původ porty.

Umožňuje vrátit do našich hypotetický příklad s protokolem http:\//www.contoso.com:8080/file.txt.  Jak budete si pamatovat, zadaný Contoso *8080* jako jejich HTTP port, ale také Předpokládejme, že zadaný *44300* jako jejich port HTTPS.  Pokud vytvořené koncový bod s názvem *contoso*, jejich hostitele koncového bodu CDN by *contoso.azureedge.net*.  Požadavek http:\//contoso.azureedge.net/file.txt je požadavek HTTP, takže koncový bod by prostřednictvím protokolu HTTP na portu 8080 načíst z tohoto počátku.  Žádost o zabezpečené pomocí protokolu HTTPS, https: \/ /contoso.azureedge.net/file.txt, by způsobilo koncový bod pro použití protokolu HTTPS na portu 44300 při načítání souboru z tohoto počátku.

#### <a name="origin-host-header"></a>Hlavička počátečního hostitele
**Hlavičky hostitele počátku** je hodnota hlavičky hostitele odesílaná na počátek s každou žádostí.  Ve většině případů to by měl být stejný jako **název počátečního hostitele** jsme ověřit dříve.  Nesprávná hodnota v tomto poli obvykle nezpůsobí stav 404, ale může způsobit další 4xx stavy, v závislosti na tom, co počátek očekává.

#### <a name="origin-path"></a>Cesta k počátku
A konečně, bylo by měl ověřit naše **cesty ke zdroji**.  Ve výchozím nastavení je toto pole prázdné.  Toto pole byste měli používat jenom, pokud chcete zúžit rozsah hostované počátek prostředky, které chcete zpřístupnit v CDN.  

V příkladu koncový bod, jsme chtěli všechny prostředky v účtu úložiště k dispozici, takže **cesty ke zdroji** byl ponecháno prázdné.  To znamená, že požadavek na protokol https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt výsledkem připojení z koncového bodu k cdndocdemo.core.windows.net, které vyžadují */publicblob/lorem.txt*.  Podobně požadavku pro protokol https:\//cdndocdemo.azureedge.net/donotcache/status.png výsledkem požadavku koncového bodu */donotcache/status.png* z tohoto počátku.

Ale co když nechcete používat CDN pro každou cestu na do zdrojového umístění?  Řekněme chcete vystavit *publicblob* cesta.  Pokud jsme zadejte */publicblob* v **cesty ke zdroji** pole, které způsobí, že koncový bod vložení */publicblob* před každým požadavkem určené k počátku.  To znamená, že žádost pro protokol https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt bude nyní ve skutečnosti trvat žádost část adresy URL, */publicblob/lorem.txt*a připojte */publicblob* na začátku. Výsledkem je žádost o */publicblob/publicblob/lorem.txt* z tohoto počátku.  Pokud tato cesta se nepřekládá na skutečný soubor, počátek vrátí stav 404.  Správnou adresu URL pro načtení lorem.txt v tomto příkladu by ve skutečnosti se https:\//cdndocdemo.azureedge.net/lorem.txt.  Všimněte si, že jsme neobsahují */publicblob* cestu na všechny, protože požadavek část adresy URL je */lorem.txt* a přidá koncový bod */publicblob*, což */publicblob/lorem.txt* předávány žádosti k počátku.

