---
title: Konfigurace webových aplikací v prostředí Azure App Service
description: Postup konfigurace webové aplikace v Azure App Services
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
ms.openlocfilehash: 0c1cea1646c71698318e94932248e08955359b9e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234512"
---
# <a name="configure-web-apps-in-azure-app-service"></a>Konfigurace webových aplikací v prostředí Azure App Service

Toto téma vysvětluje, jak nakonfigurovat webovou aplikaci pomocí [Azure Portal].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Nastavení aplikace
1. V [Azure Portal], otevře se okno pro webovou aplikaci.
3. Klikněte na možnost **Nastavení aplikace**.

![Nastavení aplikace][configure01]

**Nastavení aplikace** okno obsahuje nastavení, které jsou seskupené v rámci několika kategorií.

### <a name="general-settings"></a>Obecná nastavení
**Verze Framework**. Pokud vaše aplikace používá tyto architektury, nastavte tyto možnosti: 

* **Rozhraní .NET framework**: nastavení verze rozhraní .NET framework. 
* **PHP**: nastavte verzi PHP, nebo **OFF** zakázat PHP. 
* **Java**: Vyberte verzi jazyka Java nebo **OFF** zakázat Java. Použití **webový kontejner** možnost si vybrat mezi verzí Tomcat a Jetty.
* **Python**: Vyberte verzi jazyka Python, nebo **OFF** zakázat Python.

Technické z důvodů povolení pro aplikace Java zakáže možnosti .NET, PHP a Python.

<a name="platform"></a>
**Platforma**. Vybere, zda běží vaše webová aplikace v prostředí 32bitové nebo 64bitové verze. 64bitová verze prostředí vyžaduje režimu Basic nebo Standard. Uvolněte a režimy sdílené vždy spustit v prostředí 32-bit.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Webové sokety**. Nastavit **ON** povolit protokol WebSocket; například, pokud vaše webová aplikace používá [Funkce SignalR technologie ASP.NET] nebo [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Always On**. Ve výchozím nastavení jsou uvolněna webové aplikace, pokud jsou některé dobu nečinnosti. To umožňuje ušetřit prostředky systému. V režimu Basic nebo Standard, povolit **Always On** udržovat aplikaci načíst vždy. Pokud vaše aplikace běží nepřetržité webové úlohy nebo běží webové úlohy aktivaci pomocí výrazu CRON, měli byste povolit **Always On**, nebo nemusí spolehlivě spuštění webové úlohy.

**Spravované verze kanálu**. Nastaví služby IIS [režim kanálů]. Pokud nemáte aplikaci ze starší verze, která vyžaduje starší verze služby IIS, ponechte této sady na integrovaný (výchozí).

**Automatické prohození**. Pokud povolíte automatické prohození pro slot nasazení, služby App Service automaticky Prohodit webové aplikace do produkčního prostředí, při nabízené aktualizace pro tento slot. Další informace najdete v tématu [nasadit do přípravné sloty pro webové aplikace v Azure App Service](web-sites-staged-publishing.md).

### <a name="debugging"></a>Ladění
**Vzdálené ladění**. Umožňuje vzdálené ladění. Když je povolené, můžete v sadě Visual Studio vzdáleného ladicího programu pro připojení přímo k vaší webové aplikace. Vzdálené ladění zůstane zapnutá 48 hodin. 

### <a name="app-settings"></a>Nastavení aplikací
Tato část obsahuje dvojice název/hodnota, které webové aplikace se načte při spuštění. 

* Pro aplikace .NET, tato nastavení jsou vloženy do vaší konfigurace .NET `AppSettings` v době běhu přepsání stávajícího nastavení. 
* Aplikace PHP, Python, Java a uzel mají přístup k tato nastavení jako proměnné prostředí za běhu. Pro každé nastavení aplikace jsou vytvořeny dvou proměnných prostředí; jednu s názvem zadaným v položce nastavení aplikace a druhý s předponou APPSETTING_. Oba obsahují stejnou hodnotu.

Nastavení aplikace se vždy šifrují, pokud uložená (šifrované na rest).

### <a name="connection-strings"></a>Připojovací řetězce
Připojovací řetězce pro odkazované zdroje. 

Pro aplikace .NET, tyto připojovací řetězce jsou vloženy do konfiguraci .NET `connectionStrings` nastavení v době běhu přepsání existujících položek, kde klíč rovná název propojené databáze. 

Pro aplikace PHP, Python, Java a uzel budou tato nastavení k dispozici jako proměnné prostředí v době běhu předponu typ připojení. Předpony proměnné prostředí jsou následující: 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Databáze SQL: `SQLAZURECONNSTR_`
* Vlastní: `CUSTOMCONNSTR_`

Například, pokud byly s názvem připojovací řetězec databáze MySql `connectionstring1`, by přístupná prostřednictvím proměnné prostředí `MYSQLCONNSTR_connectionString1`.

Připojovací řetězce jsou zašifrované vždy, když uložené (šifrované na rest).

### <a name="default-documents"></a>Výchozí dokumenty
Výchozí dokument je webové stránky, která se zobrazí na adresy URL kořenového adresáře pro web.  První odpovídající soubor v seznamu se používá. 

Webové aplikace může použít modulů na adresu URL na základě trasy, že místo obsluhující statický obsah, v takovém případě že je jako takový výchozí dokument.    

### <a name="handler-mappings"></a>Mapování obslužných rutin
Pomocí této oblasti můžete přidat vlastní skript procesorů pro zpracování požadavků pro konkrétní přípony souborů. 

* **Rozšíření**. Přípona souboru ke zpracování, jako je například *.php nebo handler.fcgi. 
* **Skript procesoru cesta**. Absolutní cesta mapě skriptů. Požadavky na soubory, které odpovídají přípona souboru budou zpracovány procesorem skriptu. Použijte cestu `D:\home\site\wwwroot` odkazovat na kořenový adresář vaší aplikace.
* **Další argumenty**. Volitelné argumenty příkazového řádku pro mapě skriptů 

### <a name="virtual-applications-and-directories"></a>Virtuální aplikace a adresáře
Chcete-li nakonfigurovat virtuální aplikace a adresáře, zadejte každý virtuální adresář a jeho odpovídající fyzická cesta relativní vůči kořenovému adresáři webu. Volitelně můžete vybrat **aplikace** zaškrtávací políčko k označení virtuální adresář jako aplikace.

## <a name="enabling-diagnostic-logs"></a>Povolení diagnostických protokolů
Povolení diagnostických protokolů:

1. V okně vaší webové aplikace, klikněte na **všechna nastavení**.
2. Klikněte na tlačítko **diagnostické protokoly**. 

Možnosti pro zápis z webové aplikace, která podporuje protokolování diagnostických protokolů: 

* **Protokolování aplikací**. Zapisuje protokoly aplikace do systému souborů. Protokolování vydrží po dobu 12 hodin. 

**Úroveň**. Pokud je povoleno protokolování aplikací, tato možnost určuje, že množství informací, které budou zaznamenány (chyba, upozornění, informace nebo Verbose).

**Protokolování webového serveru**. Protokoly se ukládají do formátu souboru rozšířené protokolu W3C. 

**Podrobné chybové zprávy**. Uloží podrobné chybové zprávy soubory .htm. Soubory jsou uložena pod /LogFiles/DetailedErrors. 

**Trasování neúspěšných žádostí**. Protokoly neúspěšné požadavky na soubory XML. Soubory uložené pod/LogFiles/W3SVC*xxx*, kde xxx je jedinečný identifikátor. Tato složka obsahuje soubor XSL a jeden nebo více souborů XML. Zajistěte, aby se stáhnout soubor XSL, protože poskytuje funkce pro formátování a filtrování obsah souborů XML.

Chcete-li zobrazit soubory protokolů, musíte vytvořit přihlašovací údaje serveru FTP, následujícím způsobem:

1. V okně vaší webové aplikace, klikněte na **všechna nastavení**.
2. Klikněte na tlačítko **přihlašovací údaje nasazení**.
3. Zadejte uživatelské jméno a heslo.
4. Klikněte na **Uložit**.

![Nastavení přihlašovacích údajů nasazení][configure03]

Úplné uživatelské jméno FTP je "app\username", kde *aplikace* je název vaší webové aplikace. Uživatelské jméno je uvedena v okně webové aplikace v části **Essentials**.

![Přihlašovací údaje pro nasazení serveru FTP][configure02]

## <a name="other-configuration-tasks"></a>Další konfigurační úlohy
### <a name="ssl"></a>SSL
V režimu Basic nebo Standard můžete nahrát certifikáty SSL pro vlastní doménu. Další informace najdete v tématu [povolit HTTPS pro webovou aplikaci](app-service-web-tutorial-custom-ssl.md). 

Chcete-li zobrazit nahrané certifikáty, klikněte na tlačítko **všechna nastavení** > **vlastní domény a SSL**.

### <a name="domain-names"></a>Názvy domén
Přidáte vlastní názvy domén pro vaši webovou aplikaci. Další informace najdete v tématu [konfigurace vlastního názvu domény pro webovou aplikaci v Azure App Service](app-service-web-tutorial-custom-domain.md).

Chcete-li zobrazit názvy domén, klikněte na tlačítko **všechna nastavení** > **vlastní domény a SSL**.

### <a name="deployments"></a>Nasazení
* Nastavte průběžné nasazování. V tématu [pomocí Git, jak nasadit webové aplikace v Azure App Service](app-service-deploy-local-git.md).
* Nasazovací sloty. V tématu [Nasazení do pracovní prostředí pro webové aplikace v Azure App Service].

Chcete-li zobrazit nasazovací sloty, klikněte na tlačítko **všechna nastavení** > **nasazovací sloty**.

### <a name="monitoring"></a>Monitorování
V režimu Basic nebo Standard můžete otestovat dostupnost protokolu HTTP nebo HTTPS koncových bodů, z umístění až tři zeměpisné polohy. Monitorování test se nezdaří, pokud kód odpovědi HTTP dojde k chybě (4xx nebo 5xx) nebo odpověď trvá déle než 30 sekund. Koncový bod je považován za dostupný, pokud monitorovací testy úspěšné z určitých umístění. 

Další informace najdete v tématu [Postupy: sledování stavu webových koncový bod].

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service], kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 

## <a name="next-steps"></a>Další postup
* [Konfigurace vlastní domény ve službě Azure App Service]
* [Povolit HTTPS pro aplikace v Azure App Service]
* [Škálování webové aplikace v Azure App Service]
* [Základní informace o monitorování pro webové aplikace v Azure App Service]

<!-- URL List -->

[Funkce SignalR technologie ASP.NET]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurace vlastní domény ve službě Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Nasazení do pracovní prostředí pro webové aplikace v Azure App Service]: ./web-sites-staged-publishing.md
[Povolit HTTPS pro aplikace v Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Postupy: sledování stavu webových koncový bod]: http://go.microsoft.com/fwLink/?LinkID=279906
[Základní informace o monitorování pro webové aplikace v Azure App Service]: ./web-sites-monitor.md
[režim kanálů]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Škálování webové aplikace v Azure App Service]: ./web-sites-scale.md
[Vyzkoušet službu App Service]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
