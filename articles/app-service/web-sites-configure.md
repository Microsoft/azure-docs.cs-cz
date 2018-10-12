---
title: Konfigurace webových aplikací v prostředí Azure App Service
description: Jak nakonfigurovat webovou aplikaci ve službě Azure App Services
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.openlocfilehash: cf3a6fe24082a10db6a5b1267b70435d9e36b720
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115518"
---
# <a name="configure-web-apps-in-azure-app-service"></a>Konfigurace webových aplikací v prostředí Azure App Service

Toto téma vysvětluje, jak nakonfigurovat webovou aplikaci pomocí [Azure Portal].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Nastavení aplikace
1. V [Azure Portal], otevře se okno pro webovou aplikaci.
3. Klikněte na možnost **Nastavení aplikace**.

![Nastavení aplikace][configure01]

**Nastavení aplikace** seskupené pod nadpisem několik kategorií nastavení obsahuje okno.

### <a name="general-settings"></a>Obecná nastavení
**Verze rozhraní Framework**. Pokud vaše aplikace používá některé tyto architektury, nastavte tyto možnosti: 

* **Rozhraní .NET framework**: nastavte verze rozhraní .NET framework. 
* **PHP**: nastavení verze PHP nebo **OFF** zakázat PHP. 
* **Java**: Vyberte verzi jazyka Java nebo **OFF** zakázat Java. Použití **webový kontejner** možnost si vybrat mezi verzí Tomcat a Jetty.
* **Python**: Vyberte verzi Pythonu, nebo **OFF** zakázat Python.

Povolení Java pro vaši aplikaci z technických důvodů, zakáže možnosti .NET, PHP a Pythonu.

<a name="platform"></a>
**Platforma**. Vybere, jestli vaše webová aplikace běží v 32bitové nebo 64bitové prostředí. 64bitové prostředí vyžaduje úroveň Basic nebo Standard. Free a úroveň Shared vždy spustit v 32bitovém prostředí.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Webové sokety**. Nastavte **ON** povolit protokol WebSocket; například, pokud vaše webová aplikace používá [funkce SignalR technologie ASP.NET] nebo [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Always On**. Ve výchozím nastavení webové aplikace jsou uvolněna, pokud jsou některé dobu nečinnosti. To umožňuje ušetřit prostředky systému. V režimu Basic nebo Standard, můžete povolit **Always On** do aplikace načtena celou dobu. Pokud nepřetržitých WebJobs běží vaše aplikace nebo spuštění WebJobs aktivované pomocí výrazů CRON, měli byste povolit **Always On**, nebo webové úlohy nebude fungovat spolehlivě.

**Spravovaná verze kanálu**. Nastaví IIS [režim kanálů]. Pokud máte starší verzi aplikace, která vyžaduje starší verzi služby IIS, ponechte tuto sadu integrovaný režim (výchozí).

**Verze protokolu HTTP**. Nastavte na **2.0** povolení podpory pro [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokolu. 

> [!NOTE]
> Většina moderních prohlížečů podporují protokol HTTP/2 přes protokol TLS pouze, zatímco stále nešifrované přenosy pomocí protokolu HTTP/1.1. Chcete-li zajistit, že klientské prohlížeče připojit k vaší aplikace pomocí protokolu HTTP/2, buď [koupit certifikát App Service](web-sites-purchase-ssl-web-site.md) pro vlastní doménu vaší aplikace nebo [vytvoření vazby certifikátu třetích stran](app-service-web-tutorial-custom-ssl.md).

**Spřažení směrování žádostí na aplikace**. V aplikaci, která je ji škálovat do několika instancí virtuálních počítačů, směrování žádostí na aplikace spřažení souborů cookie zaručit, že klient je směrovat do stejné instance po dobu trvání relace. Pokud chcete zlepšit výkon bezstavových aplikací, nastavte tuto možnost na **vypnout**.   

**Automatické prohození**. Pokud povolíte automatické prohození slotu nasazení, App Service automaticky prohození webové aplikace do produkčního prostředí, při nahrání aktualizace do tohoto slotu. Další informace najdete v tématu [nasazení do přípravných slotů pro web apps ve službě Azure App Service](web-sites-staged-publishing.md).

### <a name="debugging"></a>Ladění
**Vzdálené ladění**. Umožňuje vzdálené ladění. Pokud povolená, můžete použít vzdálený ladicí program v sadě Visual Studio pro připojení přímo do vaší webové aplikace. Vzdálené ladění zůstane povolena po dobu 48 hodin. 

### <a name="app-settings"></a>Nastavení aplikací
Tato část obsahuje dvojice název/hodnota, které webové aplikace se načtou při spuštění nahoru. 

* Pro aplikace .NET, tato nastavení jsou vloženy do vaší konfigurace .NET `AppSettings` za běhu, přepíše stávající nastavení. 
* Pro službu App Service v Linuxu a Web App for Containers, je-li mít člověk vnořené struktury klíče json v názvu jako `ApplicationInsights:InstrumentationKey` budete muset mít `ApplicationInsights__InstrumentationKey` jako název klíče. Všimněte si, že všechny tak `:` by měl být nahrazen `__` (tj. dvojitým podtržítkem).
* Aplikace PHP, Python, Java nebo Node můžou k tato nastavení jako proměnné prostředí za běhu. Pro každé nastavení aplikace se vytvoří dvě proměnné prostředí jeden s názvem zadaným v nastavení vstupní aplikace a druhý s předponou APPSETTING_. Oba obsahují stejnou hodnotu.

Nastavení aplikace se vždy šifrují, pokud uložená (šifrovaná at-rest).

### <a name="connection-strings"></a>Připojovací řetězce
Připojovací řetězce pro odkazované zdroje. 

Pro aplikace .NET, jsou tyto připojovací řetězce vloženy do vaší konfigurace .NET `connectionStrings` nastavení v době běhu přepsání existující položky, jejichž klíč rovná hodnotě názvu propojené databáze. 

Tato nastavení budou k dispozici jako proměnné prostředí za běhu, před kterou je připojený s typem připojení pro aplikace PHP, Python, Java nebo Node. Předpony proměnné prostředí jsou následující: 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* Vlastní: `CUSTOMCONNSTR_`

Například, pokud se název připojovacího řetězce MySql `connectionstring1`, by být přístupné prostřednictvím proměnné prostředí `MYSQLCONNSTR_connectionString1`.

Připojovací řetězce se vždy šifrují, pokud uložená (šifrovaná at-rest).

### <a name="default-documents"></a>Výchozí dokumenty
Výchozí dokument je webová stránka, která se zobrazí na adrese URL kořenového webu.  V seznamu první odpovídající soubor se používá. 

Webové aplikace může používat moduly, směrování na základě adresy URL, spíše než obsluhuje statický obsah, v takovém případě že je jako takové žádná výchozí dokument.    

### <a name="handler-mappings"></a>Mapování obslužných rutin
Pomocí této oblasti můžete přidat vlastní skript procesory zpracovávat požadavky pro konkrétní přípony souborů. 

* **Rozšíření**. Přípona souboru ke zpracování, jako je například *.php nebo handler.fcgi. 
* **Cesta k procesoru skriptu**. Absolutní cesta mapě skriptů. V mapě skriptů bude zpracovávat požadavky na soubory, které odpovídají příponu souboru. Použijte cestu `D:\home\site\wwwroot` k odkazování na kořenovém adresáři vaší aplikace.
* **Další argumenty**. Volitelné argumenty příkazového řádku pro procesor skriptů. 

### <a name="virtual-applications-and-directories"></a>Virtuální aplikace a adresáře
Pokud chcete nakonfigurovat virtuální aplikace a adresáře, zadejte každý virtuální adresář a jeho odpovídající fyzickou cestu vzhledem ke kořenové složky webu. Volitelně můžete vybrat **aplikace** zaškrtávacího políčka označíte virtuální adresář jako aplikace.

## <a name="enabling-diagnostic-logs"></a>Povolení diagnostických protokolů
Povolení diagnostických protokolů:

1. V okně pro vaši webovou aplikaci, klikněte na tlačítko **všechna nastavení**.
2. Klikněte na **Diagnostické protokoly**. 

Možnosti pro zápis diagnostické protokoly z webové aplikace, která podporuje protokolování: 

* **Protokolování aplikací**. Zapisuje protokoly aplikace do systému souborů. Protokolování má platnost po dobu 12 hodin. 

**Úroveň**. Pokud je povoleno protokolování aplikace, tato možnost určuje množství informací, které budou zaznamenány (chyba, upozornění, informace nebo podrobné).

**Protokolování webového serveru**. Protokoly jsou uloženy ve formátu souboru rozšířené protokolu W3C. 

**Podrobné chybové zprávy**. Uloží podrobné chybové zprávy souborů HTM. V části /LogFiles/DetailedErrors uložením souborů. 

**Trasování neúspěšných žádostí**. Protokoly neúspěšných požadavků do souborů XML. Soubory uložené v oblasti/LogFiles/W3SVC*xxx*, kde xxx je jedinečný identifikátor. Tato složka obsahuje soubor XSL a jeden nebo více souborů XML. Ujistěte se, že chcete stáhnout soubor XSL, protože poskytuje funkce pro formátování a filtrování obsahu souborů XML.

Chcete-li zobrazit soubory protokolů, musíte vytvořit přihlašovací údaje protokolu FTP, následujícím způsobem:

1. V okně pro vaši webovou aplikaci, klikněte na tlačítko **všechna nastavení**.
2. Klikněte na tlačítko **přihlašovací údaje pro nasazení**.
3. Zadejte uživatelské jméno a heslo.
4. Klikněte na **Uložit**.

![Nastavení přihlašovacích údajů nasazení][configure03]

Úplné uživatelské jméno FTP je "app\username" kde *aplikace* je název vaší webové aplikace. Uživatelské jméno je uvedena v okně webové aplikace v části **Essentials**.

![Přihlašovací údaje pro nasazení FTP][configure02]

## <a name="other-configuration-tasks"></a>Další konfigurační úlohy
### <a name="ssl"></a>SSL
V režimu Basic nebo Standard můžete nahrát certifikáty SSL pro vlastní doménu. Další informace najdete v tématu [povolení HTTPS pro webovou aplikaci](app-service-web-tutorial-custom-ssl.md). 

Chcete-li zobrazit odeslané certifikáty, klikněte na tlačítko **všechna nastavení** > **vlastní domény a SSL**.

### <a name="domain-names"></a>Názvy domén
Přidání vlastních názvů domén pro vaši webovou aplikaci. Další informace najdete v tématu [konfigurace vlastního názvu domény pro webovou aplikaci ve službě Azure App Service](app-service-web-tutorial-custom-domain.md).

Chcete-li zobrazit názvy domén, klikněte na tlačítko **všechna nastavení** > **vlastní domény a SSL**.

### <a name="deployments"></a>Nasazení
* Nastavení průběžného nasazování. Zobrazit [pomocí Git, jak nasadit webové aplikace ve službě Azure App Service](app-service-deploy-local-git.md).
* Sloty nasazení. Zobrazit [nasazení do přípravných prostředí pro Web Apps ve službě Azure App Service].

Chcete-li zobrazit nasazovacích slotů, klikněte na tlačítko **všechna nastavení** > **sloty nasazení**.

### <a name="monitoring"></a>Monitorování
V režimu Basic nebo Standard můžete otestovat dostupnost protokolu HTTP nebo HTTPS koncových bodů, z až tři geograficky distribuované umístění. Monitorování test selže, pokud se o chybu (4xx nebo 5xx), kód HTTP odpovědi nebo odpovědi trvá déle než 30 sekund. Koncový bod se považuje za k dispozici v případě úspěšné testy monitorování z určitých umístění. 

Další informace najdete v tématu [postupy: sledování stavu koncových bodů webové].

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service], kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 

## <a name="next-steps"></a>Další postup
* [Konfigurace vlastní domény ve službě Azure App Service]
* [Povolení HTTPS pro aplikaci ve službě Azure App Service]
* [Škálování webové aplikace ve službě Azure App Service]
* [Základy monitorování pro webové aplikace ve službě Azure App Service]

<!-- URL List -->

[Funkce SignalR technologie ASP.NET]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurace vlastní domény ve službě Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Nasazení do přípravných prostředí pro Web Apps ve službě Azure App Service]: ./web-sites-staged-publishing.md
[Povolení HTTPS pro aplikaci ve službě Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Postupy: sledování stavu koncových bodů webové]: http://go.microsoft.com/fwLink/?LinkID=279906
[Základy monitorování pro webové aplikace ve službě Azure App Service]: ./web-sites-monitor.md
[režim kanálů]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Škálování webové aplikace ve službě Azure App Service]: ./web-sites-scale.md
[Vyzkoušet službu App Service]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
