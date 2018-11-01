---
title: Technologie Open source nejčastější dotazy k Azure web apps | Dokumentace Microsoftu
description: Získejte odpovědi na nejčastější dotazy týkající se open source technologií ve funkci Web Apps služby Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b34597c604160cc5c0880561a6c3afb70816f9b3
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417323"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Technologie Open source nejčastější dotazy k Web Apps v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o problémech s technologiemi open source pro [funkce Web Apps služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Jak můžu zapnout protokolování pro řešení potíží s PHP PHP?

Zapnutí protokolování PHP:

1. Přihlaste se k vaší [webu Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. V horní nabídce vyberte **ladění konzoly** > **CMD**.
3. Vyberte **lokality** složky.
4. Vyberte **wwwroot** složky.
5. Vyberte **+** ikonu a pak vyberte **nový soubor**.
6. Nastavte název souboru **. user.ini**.
7. Vyberte ikonu tužky vedle **. user.ini**.
8. V souboru přidejte tento kód: `log_errors=on`
9. Vyberte **Uložit**.
10. Vyberte ikonu tužky vedle **wp config.php**.
11. Změní celý text na následující kód:
   ```php
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Supress PHP errors to screenini_set('display_errors', 0);
   ```
12. Na webu Azure Portal, v nabídce webové aplikace restartujte webovou aplikaci.

Další informace najdete v tématu [protokoly chyb pro WordPress povolit](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Jak protokolování chyb aplikace Python v aplikacích, které jsou hostované ve službě App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Jak mohu změnit verzi aplikace Node.js, která je hostována ve službě App Service?

Pokud chcete změnit verzi aplikace Node.js, můžete použít jednu z následujících možností:

*   Na webu Azure Portal, použijte **nastavení aplikace**.
    1. Na webu Azure Portal přejděte do své webové aplikace.
    2. Na **nastavení** okně vyberte **nastavení aplikace**.
    3. V **nastavení aplikace**, můžete zahrnout WEBSITE_NODE_DEFAULT_VERSION jako klíči a verzi Node.js chcete použít jako hodnotu.
    4. Přejděte k vaší [konzola Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    5. Pokud chcete zkontrolovat verze Node.js, zadejte následující příkaz:  
   ```
   node -v
   ```
*   Upravte soubor iisnode.yml poskytovaný rozhraním. Změna verze Node.js v souboru iisnode.yml poskytovaný rozhraním nastaví běhové prostředí pouze při, jemuž modul iisnode používá. Vaše Kudu cmd a dalšími lidmi dál používat verze Node.js, která je nastavena v **nastavení aplikace** na webu Azure Portal.

    Nastavit soubor iisnode.yml ručně, vytvořte soubor iisnode.yml v kořenové složce aplikace. V souboru přidejte následující řádek:
   ```yml
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Nastavte soubor iisnode.yml poskytovaný rozhraním prostřednictvím souboru package.json během nasazení ovládacího prvku zdroje.
    Proces nasazení Azure zdrojového ovládacího prvku zahrnuje následující kroky:
    1. Obsah přesune do webové aplikace Azure.
    2. Vytvoří výchozí skript nasazení, pokud není k dispozici (deploy.cmd, .deployment souborů) v kořenové složce webové aplikace.
    3. Spustí skript nasazení, ve kterém se vytvoří soubor iisnode.yml poskytovaný rozhraním Pokud zmíníte verze Node.js v souboru package.json > modul `"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. Soubor iisnode.yml poskytovaný rozhraním má následující řádek kódu:
        ```yml
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Zobrazí zpráva "Chyba při navazování připojení k databázi" ve své aplikaci WordPress, který je hostovaný ve službě App Service. Jak to můžu vyřešit?

Pokud tato chyba se zobrazí v aplikaci WordPress v Azure, umožňuje php_errors.log a debug.log, dokončení kroky popsané v [protokoly chyb pro WordPress povolit](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Pokud jsou povolené protokoly, reprodukovat chybu a pak zkontrolujte protokoly a zjistit, zda jsou spuštěny z připojení:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Pokud tato chyba se zobrazí ve vašich souborech debug.log nebo php_errors.log, aplikace je vyšší než počet připojení. Pokud máte na ClearDB, zjistit, kolik připojení, které jsou k dispozici v vaše [tarifu](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Jak mohu ladit aplikace v Node.js, která je hostována ve službě App Service?

1.  Přejděte k vaší [konzola Kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Přejděte do složky protokolů aplikace (D:\home\LogFiles\Application).
3.  V souboru logging_errors.txt vyhledejte obsah.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Jak nainstalovat nativních modulů Python v App Service webovou aplikaci nebo aplikaci API?

Některé balíčky nemusí nainstalovat pomocí pip v Azure. Balíček nemusí být k dispozici v indexu balíčků Pythonu, nebo může být vyžadováno kompilátor (kompilátor není k dispozici v počítači, na kterém běží webová aplikace ve službě App Service). Informace o instalaci nativních modulů ve službě App Service web apps a API apps, najdete v části [moduly nainstalujte Python ve službě App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Jak nasadím aplikaci Django do služby App Service s využitím Gitu a nová verze Pythonu?

Informace o instalaci Django, naleznete v tématu [nasazení aplikace Django do služby App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Kde se nacházejí soubory protokolů Tomcat?

Pro vlastní nasazení a webu Azure Marketplace:

* Umístění složky: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Soubory, které vás zajímají:
    * catalina.*yyyy-mm-dd*.log
    * Správce hostitele. *rrrr mm-dd*.log
    * localhost. *rrrr mm-dd*.log
    * správce. *rrrr mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log


Pro portál **nastavení aplikace** nasazení:

* Umístění složky: D:\home\LogFiles
* Soubory, které vás zajímají:
    * catalina.*yyyy-mm-dd*.log
    * Správce hostitele. *rrrr mm-dd*.log
    * localhost. *rrrr mm-dd*.log
    * správce. *rrrr mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Jak řešit chyby připojení ovladač JDBC

V protokolech Tomcat může se zobrazit následující zpráva:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Chcete-li vyřešit chybu:

1. Odeberte soubor sqljdbc*.jar ze složky aplikace/lib.
2. Pokud používáte vlastní webový server Tomcat nebo Azure Marketplace Tomcat, zkopírujte tento soubor .jar do složky lib Tomcat.
3. Chcete-li povolit Java z portálu Azure portal (vyberte **Java 1.8** > **serveru Tomcat**), zkopírovat soubor jar sqljdbc.* ve složce, která je paralelní do vaší aplikace. Potom přidejte následující cesta k třídě nastavení v souboru web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Proč se při pokusu o kopírování souborů za provozu protokolu zobrazí chyby?

Pokud se pokusíte zkopírovat živé soubory protokolu pro aplikace Java (například Tomcat), může se zobrazit tato chyba FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Chybová zpráva se může lišit v závislosti na klientovi FTP.

Všechny aplikace v Javě máte potíže zamykání. Pouze Kudu podporuje stahování tohoto souboru, když aplikace běží.

Zastavuje se aplikace umožňuje FTP přístup k těmto souborům.

Jiné řešením je napsat webovou úlohu, která spouští podle plánu a zkopíruje soubory do jiného adresáře. Ukázkový projekt, naleznete v tématu [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projektu.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Kde najdu soubory protokolu pro Jetty?

Pro vlastní nasazení a Marketplace soubor protokolu je ve složce D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Všimněte si, že umístění složky závisí na verzi Jetty, kterou používáte. Například cesta zde uvedené pro Jetty 9.1.2. Vyhledejte jetty_*YYYY_MM_DD*. stderrout.log.

Pro nasazení portálu nastavení aplikace soubor protokolu je v D:\home\LogFiles. Vyhledejte jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Může odesílat e-maily z Moje webové aplikace Azure?

Služba App Service nemá integrované e-mailové funkce. Pro některé dobré alternativy k odesílání e-mailu z vaší aplikace, najdete v tomto [Stack Overflow diskuse](http://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Proč můj web WordPress přesměrovat na jinou adresu URL?

Pokud jste nedávno migrovali do Azure, může být původní adresu URL domény přesměrování WordPress. To je způsobeno nastavením v databázi MySQL.

WordPress Buddy + se rozšíření webu Azure, který vám pomůže aktualizovat adresu URL přesměrování přímo v databázi. Další informace o používání WordPress Buddy +, naleznete v tématu [WordPress nástroje a migrace MySQL s Wordpressem Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Případně, pokud budete chtít ručně aktualizovat přesměrování URL pomocí dotazů SQL nebo phpmyadmin zobrazuje, naleznete v tématu [WordPress: přesměrování na nesprávnou adresu URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Jak změnit přihlašovací heslo WordPress?

Pokud jste zapomněli přihlašovací heslo WordPress, můžete použít WordPress Buddy + ji aktualizovat. K resetování hesla, instalovat WordPress Buddy + lokality rozšíření Azure a potom postupujte podle pokynů popsaných v [WordPress nástroje a migrace MySQL s Wordpressem Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nemůžu se přihlásit k WordPress. Jak to můžu vyřešit?

Pokud se uzamčen a vyloučen z Wordpressu po instalaci nedávno modul plug-in, bude pravděpodobně chybné modulu plug-in. WordPress Buddy + se rozšíření webu Azure, které vám může pomoct zakázat moduly plug-in ve Wordpressu. Další informace najdete v tématu [WordPress nástroje a migrace MySQL s Wordpressem Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Jak migrovat databázi WordPress?

Máte několik možností pro migraci databáze MySQL, který je připojen na váš web WordPress:

* : Vývojáři [příkazový řádek nebo phpmyadmin zobrazuje](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Nevývojáře: Použít [WordPress Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Jak můžu pomáhají zabezpečit WordPress?

Další informace o osvědčené postupy zabezpečení pro WordPress, naleznete v tématu [osvědčené postupy pro zabezpečení WordPress v Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Provést pomocí phpmyadmin zobrazuje a zobrazí zpráva "Přístup byl odepřen." Jak to můžu vyřešit?

Tomuto problému může dojít, pokud funkce MySQL v aplikaci ještě neběží v této instanci služby App Service. Chcete-li vyřešit tento problém, zkuste přístup k vašemu webu. Tím se spustí požadované procesy, včetně procesu MySQL v aplikaci. Ověřte, zda je spuštěna MySQL v aplikaci, v Process Explorer, zajistěte, že mysqld.exe je uvedena v procesu.

Po zajištění, že tento MySQL v aplikaci je spuštěná, zkuste použít phpmyadmin zobrazuje.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Při pokusu o import a export databázi MySQL v aplikaci prostřednictvím phpmyadmin zobrazuje zobrazí chybu HTTP 403. Jak to můžu vyřešit?

Pokud používáte starší verzi Chrome, může dojít k známého problému. Chcete-li vyřešit tento problém, upgradujte na novější verzi Chrome. Zkuste taky pomocí jiného prohlížeče, jako je Internet Explorer nebo Microsoft Edge, kde problému nedojde.
