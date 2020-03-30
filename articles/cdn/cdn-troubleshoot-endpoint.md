---
title: Poradce při potížích s koncovými body Azure CDN – stavový kód 404
description: Poradce při potížích 404 kódy odpovědí s koncovými body Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c332c6712cdf057491e3039854aa1a29bd54196f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083116"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Poradce při potížích s koncovými body Azure CDN, které vracejí stavový kód 404
Tento článek umožňuje řešit problémy s koncovými body sítě Azure Content Delivery Network (CDN), které vracejí stavové kódy 404 HTTP.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [MSDN Azure a zásobníku přetečení fóra](https://azure.microsoft.com/support/forums/). Případně můžete také soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.

## <a name="symptom"></a>Příznak
Vytvořili jste profil CDN a koncový bod, ale váš obsah se nezdá být k dispozici na CDN. Uživatelé, kteří se pokusí o přístup k vašemu obsahu prostřednictvím adresy URL CDN, obdrží stavový kód HTTP 404. 

## <a name="cause"></a>Příčina
Existuje několik možných příčin, včetně:

* Původ souboru není viditelný pro CDN.
* Koncový bod je nesprávně nakonfigurován, což způsobí, že CDN hledat na nesprávném místě.
* Hostitel odmítá hlavičku hostitele z cdn.
* Koncový bod neměl čas k šíření v celé CDN.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
> [!IMPORTANT]
> Po vytvoření koncového bodu CDN nebude okamžitě k dispozici pro použití, protože trvá, než se registrace rozšíří prostřednictvím CDN:
> - V případě profilů **Azure CDN Standard od Microsoftu** je šíření obvykle hotové během deseti minut. 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 90 minut. 
> 
> Pokud dokončíte kroky v tomto dokumentu a stále dostáváte odpovědi 404, zvažte, zda nepočkáte několik hodin, než se znovu zkontrolujete před otevřením lístku podpory.
> 
> 

### <a name="check-the-origin-file"></a>Kontrola původního souboru
Nejprve ověřte, zda je soubor do mezipaměti k dispozici na zdrojovém serveru a zda je veřejně přístupný na internetu. Nejrychlejší způsob, jak to udělat, je otevřít prohlížeč v soukromé nebo anonymní relaci a procházet přímo do souboru. Zadejte nebo vložte adresu URL do pole adresy a ověřte, zda je výsledkem souboru, který očekáváte. Předpokládejme například, že máte soubor v účtu Azure\/Storage, přístupný na adrese https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Pokud můžete úspěšně načíst obsah tohoto souboru, projde testem.

![Výborně!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> I když se jedná o nejrychlejší a nejjednodušší způsob, jak ověřit, že je soubor veřejně dostupný, některé konfigurace sítě ve vaší organizaci by mohly způsobit, že soubor je veřejně dostupný, pokud je ve skutečnosti viditelný pouze pro uživatele sítě (i když je hostován v Azure). Chcete-li zajistit, že tomu tak není, otestujte soubor pomocí externího prohlížeče, jako je například mobilní zařízení, které není připojené k síti vaší organizace, nebo virtuální počítač v Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Kontrola nastavení původu
Po ověření, že je soubor veřejně dostupný na internetu, ověřte nastavení původu. Na [webu Azure Portal](https://portal.azure.com)vyhledejte profil CDN a vyberte koncový bod, který řešíte. Na výsledné stránce **Koncového bodu** vyberte počátek.  

![Stránka koncového bodu se zvýrazněným původem](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Zobrazí se stránka **Origin.** 

![Stránka původu](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ původu a název hostitele
Ověřte, zda jsou hodnoty **typu Origin** a názvu **hostitele Originsprávné.** V tomto příkladu\/https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, část názvu hostitele adresy URL je *cdndocdemo.blob.core.windows.net*, což je správné. Vzhledem k tomu, že původ Azure Storage, Web App a Cloud Service používá hodnotu rozevíracího seznamu pro pole **Název hostitele Originu,** nesprávný pravopis není problém. Pokud však používáte vlastní původ, ujistěte se, že vaše název hostitele je napsán správně.

#### <a name="http-and-https-ports"></a>Porty HTTP a HTTPS
Zkontrolujte **porty** **HTTP** a HTTPS . Ve většině případů jsou 80 a 443 správné a nebudete vyžadovat žádné změny.  Pokud však zdrojový server naslouchá na jiném portu, bude zde nutné ji reprezentovat. Pokud si nejste jisti, zobrazte adresu URL původního souboru. Specifikace HTTP a HTTPS používají jako výchozí porty 80 a 443. V příkladu URL\/https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt není zadán port, takže výchozí hodnota 443 se předpokládá a nastavení jsou správná.  

Předpokládejme však, že adresa URL původního souboru, který jste testovali dříve, je http:\//www.contoso.com:8080/file.txt. Všimněte si části *:8080* na konci segmentu název hostitele. Toto číslo instruuje prohlížeč, aby používal port 8080 pro připojení k webovému serveru na adrese www\.contoso.com, proto budete muset zadat *8080* do pole **portu HTTP.** Je důležité si uvědomit, že tato nastavení portu ovlivnit pouze to, co port koncový bod používá k načtení informací z počátku.

> [!NOTE]
> **Azure CDN Standard z koncových bodů Akamai** neumožňují úplný rozsah portů TCP pro počátky.  Seznam nepovolených portů původu najdete v tématu [Povolené porty původu Azure CDN společnosti Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Kontrola nastavení koncového bodu
Na stránce **Koncový bod** vyberte tlačítko **Konfigurovat.**

![Stránka koncového bodu se zvýrazněným tlačítkem Konfigurace](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Zobrazí se stránka **Konfigurace** koncového bodu CDN.

![Konfigurace stránky](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoly
V **případě protokolů**ověřte, zda je vybrán protokol používaný klienty. Vzhledem k tomu, že stejný protokol používaný klientem je ten, který se používá pro přístup k původu, je důležité mít porty původu správně nakonfigurované v předchozí části. Koncový bod CDN naslouchá pouze na výchozích portech HTTP a HTTPS (80 a 443) bez ohledu na počáteční porty.

Vraťme se k našemu hypotetickému příkladu s http:\//www.contoso.com:8080/file.txt.  Jak si budete pamatovat, Contoso zadal *8080* jako svůj http port, ale předpokládejme také, že zadali *44300* jako port HTTPS.  Pokud by vytvořili koncový bod s názvem *contoso*, jejich název hostitele koncového bodu CDN by *byl contoso.azureedge.net*.  Požadavek na\/http: /contoso.azureedge.net/file.txt je požadavek HTTP, takže koncový bod by použít HTTP na portu 8080 načíst z původu.  Zabezpečený požadavek přes\/HTTPS, https: /contoso.azureedge.net/file.txt, by způsobit koncový bod použít HTTPS na portu 44300 při načítání souboru z počátku.

#### <a name="origin-host-header"></a>Hlavička počátečního hostitele
**Hlavička hostitele Originu** je hodnota hlavičky hostitele odeslaná na počátek s každým požadavkem.  Ve většině případů by to mělo být stejné jako **název hostitele Originu,** který jsme ověřili dříve.  Nesprávná hodnota v tomto poli obvykle nezpůsobí stavy 404, ale pravděpodobně způsobí další stavy 4xx v závislosti na tom, co očekává původ.

#### <a name="origin-path"></a>Cesta ke zdroji
A konečně bychom měli ověřit naši **cestu k Originu**.  Ve výchozím nastavení je to prázdné.  Toto pole byste měli použít pouze v případě, že chcete zúžit rozsah zdrojů hostovaných v původu, které chcete zpřístupnit v cdn.  

V příkladu koncového bodu jsme chtěli, aby všechny prostředky na účtu úložiště byly k dispozici, takže **cesta Originu** zůstala prázdná.  To znamená, že požadavek na https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt má za následek připojení z koncového bodu do cdndocdemo.core.windows.net, který požaduje */publicblob/lorem.txt*.  Podobně požadavek na https:\//cdndocdemo.azureedge.net/donotcache/status.png má za následek koncový bod požadující */donotcache/status.png* z počátku.

Ale co když nechcete použít CDN pro každou cestu na vašem původu?  Řekněme, že jste jen chtěli odhalit *publicblob* cestu.  Pokud zadáme */publicblob* v poli **cesta původu,** to způsobí, že koncový bod vložit */publicblob* před každý požadavek na původ.  To znamená, že požadavek na https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt bude nyní ve skutečnosti trvat požadavek část URL, */publicblob/lorem.txt*a připojit */publicblob* na začátek. Výsledkem je požadavek na */publicblob/publicblob/lorem.txt* z původu.  Pokud se tato cesta nevyřeší do skutečného souboru, původ vrátí stav 404.  Správná adresa URL pro načtení lorem.txt\/v tomto příkladu by ve skutečnosti bylo https: /cdndocdemo.azureedge.net/lorem.txt.  Všimněte si, že nezahrnujeme */publicblob* cestu vůbec, protože požadavek část URL je */lorem.txt* a koncový bod přidá */publicblob*, výsledkem */publicblob/lorem.txt* je požadavek předán původu.

