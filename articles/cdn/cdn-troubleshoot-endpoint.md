---
title: Řešení potíží s Azure CDNmi koncovými body-404 stavového kódu
description: Naučte se řešit problémy s koncovými body Azure Content Delivery Network, které vrací stavové kódy odpovědi HTTP 404.
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
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d6ad0b8b37bd4f04c22ed52d4ac6717202f22889
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192481"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Řešení potíží s Azure CDNmi koncovými body, které vracejí stavový kód 404
Tento článek vám umožní řešit problémy s koncovými body služby Azure Content Delivery Network (CDN), které vrací stavové kódy odpovědi HTTP 404.

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete se obrátit na odborníky na Azure na [webu MSDN Azure a ve Stack Overflowch fórech](https://azure.microsoft.com/support/forums/). Případně můžete také použít incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="symptom"></a>Příznak
Vytvořili jste profil CDN a koncový bod, ale váš obsah asi není dostupný na CDN. Uživatelům, kteří se pokoušejí o přístup k obsahu prostřednictvím adresy URL CDN, se zobrazí stavový kód HTTP 404. 

## <a name="cause"></a>Příčina
Existuje několik možných příčin, mezi které patří:

* Počátek souboru není viditelný pro CDN.
* Koncový bod je nesprávně nakonfigurovaný, což umožňuje, aby CDN vypadalo na špatném místě.
* Hostitel odmítá hlavičku hostitele ze sítě CDN.
* Koncový bod nemá čas na rozšíření v rámci CDN.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
> [!IMPORTANT]
> Po vytvoření koncového bodu CDN ho nebude hned dostupný k použití, protože se registrace dokončí přes CDN:
> - V případě profilů **Azure CDN Standard od Microsoftu** je šíření obvykle hotové během deseti minut. 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 90 minut. 
> 
> Pokud jste dokončili kroky v tomto dokumentu a pořád jste dostali odpovědi 404, zvažte, jestli před otevřením lístku podpory ještě pár hodin ověřit.
> 
> 

### <a name="check-the-origin-file"></a>Zkontroluje zdrojový soubor.
Nejprve ověřte, zda je soubor pro ukládání do mezipaměti k dispozici na zdrojovém serveru a je veřejně přístupný na internetu. Nejrychlejší způsob, jak to provést, je otevřít prohlížeč v privátní nebo anonymním relaci a přejít přímo k souboru. Zadejte nebo vložte adresu URL do pole Adresa a ověřte, jestli má za následek očekávaný soubor. Předpokládejme například, že máte soubor v účtu Azure Storage, který je přístupný v protokolu https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Pokud tento obsah souboru můžete úspěšně načíst, test se předá.

![Výborně!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> I když je toto nejrychlejší a nejjednodušší způsob, jak ověřit, že je soubor veřejně dostupný, některé síťové konfigurace ve vaší organizaci můžou zobrazit, že je soubor veřejně dostupný, když je ve skutečnosti, jenom uživatelům vaší sítě (i v případě, že je hostovaný v Azure). Abyste se ujistili, že se nejedná o tento případ, otestujte soubor pomocí externího prohlížeče, jako je například mobilní zařízení, které není připojené k síti vaší organizace, nebo virtuální počítač v Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Ověřit nastavení zdroje
Po ověření, že je soubor veřejně dostupný na internetu, ověřte nastavení zdroje. Na webu [Azure Portal](https://portal.azure.com)přejděte do svého profilu CDN a vyberte koncový bod, který chcete řešit. Na stránce výsledný **koncový bod** vyberte počátek.  

![Stránka koncového bodu s zvýrazněným počátekem](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Zobrazí se stránka **původ** . 

![Stránka zdroje](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ původu a název hostitele
Ověřte správnost hodnot **typu původu** a **názvu hostitele původu** . V tomto příkladu https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, část hostname adresy URL je *cdndocdemo.blob.Core.Windows.NET*, což je správné. Vzhledem k tomu, že Azure Storage, Webová aplikace a zdroje cloudových služeb používají pro pole **původní název hostitele** hodnotu rozevíracího seznamu, nesprávná kontrola pravopisu není problém. Pokud ale použijete vlastní zdroj, ujistěte se, že název hostitele je napsaný správně.

#### <a name="http-and-https-ports"></a>Porty HTTP a HTTPS
Ověřte porty **http** a **https**. Ve většině případů jsou 80 a 443 správné a nebudete muset nic měnit.  Pokud se ale na zdrojovém serveru naslouchá jiný port, bude potřeba, aby se tady reprezentuje. Pokud si nejste jistí, podívejte se na adresu URL vašeho zdrojového souboru. Specifikace HTTP a HTTPS používají jako výchozí hodnoty porty 80 a 443. V ukázkové adrese URL lorem.txt https: \/ /cdndocdemo.blob.Core.Windows.NET/publicblob/, port není zadaný, takže se předpokládá výchozí hodnota 443 a nastavení je správné.  

Předpokládejme však, že adresa URL původního souboru, který jste otestovali dříve, je http: \/ /www.contoso.com:8080/file.txt. Všimněte si, že část *: 8080* na konci segmentu názvu hostitele. Toto číslo dá prohlížeči pokyn k použití portu 8080 pro připojení k webovému serveru na webové adrese \. contoso.com, proto musíte zadat *8080* do pole **port http** . Je důležité si uvědomit, že tato nastavení portů mají vliv pouze na port, který koncový bod používá k načtení informací ze zdroje.

> [!NOTE]
> **Azure CDN Standard z** koncových bodů Akamai nepovoluje pro zdroje plný rozsah portů TCP.  Seznam nepovolených portů původu najdete v tématu [Povolené porty původu Azure CDN společnosti Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Kontrolovat nastavení koncového bodu
Na stránce **koncový bod** vyberte tlačítko **Konfigurovat** .

![Stránka koncového bodu s zvýrazněným tlačítkem konfigurovat](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Zobrazí se stránka pro **konfiguraci** koncového bodu CDN.

![Konfigurace stránky](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoly
V případě **protokolů** ověřte, zda je vybrán protokol používaný klienty. Vzhledem k tomu, že stejný protokol, který používá klient, je ten, který se používá pro přístup ke zdroji, je důležité mít v předchozí části správně nakonfigurované zdrojové porty. Koncový bod CDN naslouchá jenom na výchozích portech HTTP a HTTPS (80 a 443) bez ohledu na počáteční porty.

Pojďme se z našeho hypotetického příkladu vrátit http: \/ /www.contoso.com:8080/file.txt.  Jak budete si pamatovat, společnost Contoso zadala *8080* jako svůj port http, ale můžeme také předpokládat, že jako port HTTPS zadali *44300* .  Pokud vytvořili koncový bod s názvem *Contoso*, bude jeho název hostitele koncového bodu CDN *contoso.azureedge.NET*.  Požadavek na http: \/ /contoso.azureedge.net/file.txt je požadavek HTTP, takže koncový bod by na portu 8080 použil protokol HTTP, aby ho získal od počátku.  Zabezpečený požadavek přes HTTPS, https: \/ /contoso.azureedge.net/file.txt způsobí, že koncový bod při načítání souboru z počátku použije https na portu 44300.

#### <a name="origin-host-header"></a>Hlavička počátečního hostitele
**Počáteční hlavičkou hostitele** je hodnota hlavičky hostitele, kterou každý požadavek odeslal na počátek.  Ve většině případů by to měl být stejný jako **Zdrojový název hostitele** , který jsme předtím ověřili.  Nesprávná hodnota v tomto poli obvykle nezpůsobí 404 stavů, ale v závislosti na tom, co počátek očekává, může způsobit i další 4xx stavy.

#### <a name="origin-path"></a>Cesta ke zdroji
Nakonec byste měli ověřit naši cestu k **původnímu zdroji**.  Ve výchozím nastavení je to prázdné.  Toto pole byste měli použít pouze v případě, že chcete zúžit rozsah prostředků hostovaných počátkem, které mají být k dispozici v CDN.  

V ukázkovém koncovém bodu jsme chtěli mít k dispozici všechny prostředky v účtu úložiště, takže **původní cesta** byla ponechána prázdná.  To znamená, že požadavek na https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt vede k připojení z koncového bodu k cdndocdemo.Core.Windows.NET, který požaduje */publicblob/lorem.txt*.  Podobně požadavek na protokol https: \/ /cdndocdemo.azureedge.net/donotcache/status.png vede ke zjištění koncového bodu, který požaduje */donotcache/status.png* od počátku.

Ale co když nechcete používat CDN pro každou cestu k vašemu zdroji?  Řekněme, že jste chtěli zveřejnit cestu *publicblob* .  Pokud zadáte */publicblob* do pole **cesta zdroje** , bude koncový bod vložit */publicblob* před každou vydanou žádost do původního umístění.  To znamená, že žádost o protokol https: \/ /lorem.txt cdndocdemo.azureedge.NET/publicblob/nyní bude ve skutečnosti přebírat část adresy URL, */publicblob/lorem.txt* a přidá */publicblob* do začátku. Výsledkem je požadavek na */publicblob/publicblob/lorem.txt* od počátku.  Pokud tato cesta nebude přeložená na skutečný soubor, vrátí původní stav 404.  Správná adresa URL pro načtení lorem.txt v tomto příkladu by byla ve skutečnosti https: \/ /cdndocdemo.azureedge.net/lorem.txt.  Všimněte si, že nezahrnujeme cestu */publicblob* vůbec, protože část žádosti adresy URL je */lorem.txt* a koncový bod přidá */publicblob*, což vede k tomu, že se v */publicblob/lorem.txt* požadavek předává zdroji.

