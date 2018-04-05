---
title: Webové aplikace technologie Open source časté otázky k Azure | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se technologie open-source ve funkci Web Apps služby Azure App Service.
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
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 4fb443691e216169dd1322b96d77139ffde752d4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Technologie Open source nejčastější dotazy pro webové aplikace v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o problémech s nástrojem technologie open source pro [funkce Web Apps služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-cleardb-database-is-down-how-do-i-resolve-this"></a>Databázi ClearDB je mimo provoz. Jak to lze vyřešit?

Databáze s problémy, kontaktujte [ClearDB podporu](https://www.cleardb.com/developers/help/support). 

Odpovědi na časté otázky o ClearDB, naleznete v části [cleardb – nejčastější dotazy k](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="why-wasnt-my-cleardb-database-migrated-during-my-subscription-migration"></a>Proč nebyl migrován databázi ClearDB během migrace Moje předplatné?

Když provádíte migraci prostředků ve předplatných, platí určitá omezení. Databáze ClearDB MySQL je služba třetích stran a nemigruje během migrace předplatnému Azure.

Pokud nespravujete migraci databáze MySQL před zahájením migrace vašich prostředků Azure, může být databáze MySQL cleardb – není k dispozici. Abyste tomu předešli, nejprve ručně přenést databázi ClearDB, a potom přenést předplatné Azure pro vaši webovou aplikaci.

Další informace najdete v tématu [nejčastější dotazy pro databáze ClearDB MySQL službou Azure App Service](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Jak zapnout na PHP protokolování potíží PHP?

Zapnutí protokolování PHP:

1. Přihlaste se k vaší [Kudu webu](https://*yourwebsitename*.scm.azurewebsites.net).
2. V nabídce nahoře vyberte **ladění konzoly** > **CMD**.
3. Vyberte **lokality** složky.
4. Vyberte **wwwroot** složky.
5. Vyberte **+** ikonu a potom vyberte **nový soubor**.
6. Nastavte název souboru na **. user.ini**.
7. Vyberte ikonu tužky vedle **. user.ini**.
8. V souboru přidejte tento kód: `log_errors=on`
9. Vyberte **Uložit**.
10. Vyberte ikonu tužky vedle **wp-config.php**.
11. Změňte text na následující kód:
   ```
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Supress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Supress PHP errors to screenini_set('display_errors', 0);
   ```
12. Na portálu Azure, v nabídce webové aplikace restartování webové aplikace.

Další informace najdete v tématu [protokoly chyb povolit WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Jak se přihlásit chyb aplikací Python v aplikacích, které jsou hostované ve službě App Service?

K zachycení chyb aplikací Python:

1. Na portálu Azure ve vaší webové aplikaci, vyberte **nastavení**.
2. Na **nastavení** vyberte **nastavení aplikace**.
3. V části **nastavení aplikace**, zadejte následující dvojice klíč/hodnota:
    * Key : WSGI_LOG
    * Hodnota: D:\home\site\wwwroot\logs.txt (zadejte vaši volbu názvu souboru)

Teď byste měli vidět chyby v souboru logs.txt ve složce wwwroot.

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Změna verze aplikace Node.js, který je hostován ve službě App Service

Chcete-li změnit verzi aplikace Node.js, můžete použít jednu z následujících možností:

*   Na portálu Azure použijte **nastavení aplikace**.
    1. Na portálu Azure přejděte do vaší webové aplikace.
    2. Na **nastavení** vyberte **nastavení aplikace**.
    3. V **nastavení aplikace**, můžete zahrnout WEBSITE_NODE_DEFAULT_VERSION jako klíč a verze Node.js, které chcete používat jako hodnota.
    4. Přejděte na vaše [Kudu konzoly](https://*yourwebsitename*.scm.azurewebsites.net).
    5. Kontrola verze Node.js, zadejte následující příkaz:  
   ```
   node -v
   ```
*   Upravte soubor iisnode.yml. Změna verze Node.js v souboru iisnode.yml nastaví běhové prostředí pouze při, jemuž modul iisnode používá. Vaše Kudu cmd a jiné dál používat verze Node.js, který je nastavený v **nastavení aplikace** na portálu Azure.

    Pokud chcete nastavit iisnode.yml ručně, vytvořte soubor iisnode.yml v kořenové složce aplikace. V souboru zadejte následující řádek:
   ```
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Nastavte soubor iisnode.yml pomocí package.json během nasazení zdroj ovládacího prvku.
    Proces řízení nasazení Azure zdroj zahrnuje následující kroky:
    1. Obsah se přesune do webové aplikace Azure.
    2. Vytvoří výchozí skript nasazení, pokud není k dispozici (deploy.cmd, soubory .deployment) v kořenové složce webové aplikace.
    3. Spustí skript nasazení, ve kterém se vytvoří soubor iisnode.yml Pokud zmínili verze Node.js v souboru package.json > modul `"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. Soubor iisnode.yml má následující řádek kódu:
        ```
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>V mé aplikace WordPress, který je hostován ve službě App Service se zobrazují zpráva "Chyba při navazování připojení k databázi". Jak odstranit to?

Pokud tato chyba se zobrazí v aplikaci WordPress v Azure, aby php_errors.log a debug.log, dokončení kroky popsané v [protokoly chyb povolit WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Pokud jsou povolené protokoly, tomu chybu reprodukovat a v protokolech zjistit, zda jsou spuštěny mimo připojení:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Pokud se zobrazí tato chyba v souborech debug.log nebo php_errors.log, vaše aplikace překračuje počet připojení. Pokud máte na ClearDB, ověřte počet připojení, které jsou k dispozici ve vaší [plán služby](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Jak mohu ladit aplikace Node.js, který je hostován ve službě App Service?

1.  Přejděte na vaše [Kudu konzoly](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Přejděte do složky protokolů aplikace (D:\home\LogFiles\Application).
3.  V souboru logging_errors.txt kontrolovat obsahu.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Instalace nativních modulů Python v webové aplikace App Service nebo aplikace API

Některé balíčky nemusí nainstalovat pomocí nástroje pip v Azure. Balíček nemusí být k dispozici v indexu balíčků Pythonu nebo může být potřeba kompilátor (kompilátor není k dispozici v počítači, na kterém běží webová aplikace ve službě App Service). Informace o instalaci nativních modulů ve webové aplikace aplikační služby a aplikace API najdete v tématu [moduly instalaci jazyka Python ve službě App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Jak nasadit aplikace Django do služby App Service pomocí Git a nová verze jazyka Python?

Informace o instalaci Django najdete v tématu [nasazení aplikace Django do služby App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Kde jsou umístěny soubory protokolu Tomcat?

Pro Azure Marketplace a vlastní nasazení:

* Umístění složky: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Soubory, které vás zajímají:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log


Pro portál **nastavení aplikace** nasazení:

* Umístění složky: D:\home\LogFiles
* Soubory, které vás zajímají:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Jak odstranit chyb připojení ovladač JDBC?

Tuto zprávu můžete vidět v Tomcat protokolů:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Chcete-li vyřešit chyby:

1. Odeberte soubor sqljdbc*.jar ze složky aplikace/lib.
2. Pokud používáte vlastní webový server Tomcat nebo Azure Marketplace Tomcat, zkopírujte tento soubor .jar do složky lib Tomcat.
3. Pokud chcete povolit Java z portálu Azure (vyberte **Java 1.8** > **Tomcat server**), zkopírovat soubor jar sqljdbc.* ve složce, která je paralelní do vaší aplikace. Pak přidejte následující nastavení cesty pro třídy v souboru web.config:

    ```
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Proč se při pokusu o kopírování souborů za provozu protokolu zobrazí chyby?

Pokud se pokusíte zkopírovat za provozu soubory protokolu pro aplikace v jazyce Java (například Tomcat), může se zobrazit tato chyba FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Chybová zpráva se může lišit v závislosti na klient FTP.

Všechny aplikace v jazyce Java mít potíže uzamčení. Pouze Kudu podporuje stahování tohoto souboru, když aplikace běží.

Zastavení aplikace umožňuje k těmto souborům přístup FTP.

Jiným řešením je zápis webové úlohy, který spouští podle plánu a zkopíruje tyto soubory do jiného adresáře. Ukázkový projekt, najdete v článku [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projektu.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Kde soubory protokolu pro Jetty

Pro vlastní nasazení a Marketplace soubor protokolu je ve složce D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Všimněte si, že umístění složky závisí na verzi Jetty, kterou používáte. Například cesta k dispozici zde je pro Jetty 9.1.2. Vyhledejte jetty_*YYYY_MM_DD*. stderrout.log.

Pro nasazení portálu nastavení aplikace soubor protokolu je v D:\home\LogFiles. Vyhledejte jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Můžete odesílat e-maily z Azure webová aplikace?

Služby App Service nemá předdefinované e-mailové funkce. Některé funkční alternativami pro odesílání e-mailu z vaší aplikace, najdete v tématu to [Stack Overflow diskusní](http://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Proč můj web WordPress přesměrovat na jinou adresu URL?

Pokud jste nedávno migrovali do Azure, WordPress může přesměrovat na původní adresu URL domény. To je způsobeno nastavení v databázi MySQL.

WordPress kamarád + je rozšíření lokality Azure, které můžete použít k aktualizaci adresu URL pro přesměrování přímo v databázi. Další informace o používání WordPress kamarád + najdete v tématu [WordPress nástroje a migraci databáze MySQL s WordPress kamarád +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Případně, pokud chcete ručně aktualizovat přesměrování adresu URL pomocí dotazů SQL nebo PHPMyAdmin, najdete v části [WordPress: přesměrování na adresy URL je chybný](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Změna hesla přihlášení WordPress

Pokud jste WordPress přihlašovací heslo zapomněli, můžete WordPress kamarád + jej aktualizovat. Pokud chcete resetovat heslo, nainstalujte příponou webu WordPress kamarád + Azure a potom postupujte podle pokynů popsaných v [WordPress nástroje a migraci databáze MySQL s WordPress kamarád +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nemůžete se přihlásit k WordPress. Jak to lze vyřešit?

Pokud se přistihnete zamknout z WordPress po instalaci nedávno o modul plug-in, můžete mít vadný modulu plug-in. WordPress kamarád + je rozšíření webu Azure, které vám pomůžou zakažte moduly plug-in v WordPress. Další informace najdete v tématu [WordPress nástroje a migraci databáze MySQL s WordPress kamarád +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Jak migraci databáze WordPress?

Máte několik možností pro migraci databáze MySQL, která je připojena k webu WordPress:

* Vývojáři: Pomocí [příkazový řádek nebo PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Jiný vývojáři: Použít [WordPress kamarád +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Jak pomoci, zabezpečit WordPress?

Další informace o doporučeném zabezpečení WordPress najdete v tématu [osvědčené postupy pro zabezpečení WordPress v Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Chci používat PHPMyAdmin a zobrazit zpráva "Přístup byl odepřen." Jak to lze vyřešit?

Tomuto problému může dojít, pokud funkci v aplikaci MySQL ještě neběží v této instanci služby App Service. Chcete-li vyřešit tento problém, pokusu o přístup k webu. Tím se spustí požadované procesů, včetně proces MySQL v aplikaci. K ověřte, zda je spuštěna MySQL v aplikaci, v Průzkumníka procesů, zajistěte, že mysqld.exe je uvedena v procesy.

Po zajištění, že je spuštěn tento MySQL v aplikaci, zkuste použít PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Při pokusu o import nebo export databáze MySQL v aplikaci s použitím PHPMyadmin zobrazila chybu HTTP 403. Jak to lze vyřešit?

Pokud používáte starší verzi Chrome, může se jednat známého problému. Chcete-li problém vyřešit, upgradujte na novější verzi Chrome. Zkuste taky pomocí jiného prohlížeče, jako je Internet Explorer nebo Edge, kde problému nedojde.
