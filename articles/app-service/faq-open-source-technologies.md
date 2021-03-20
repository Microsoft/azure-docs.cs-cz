---
title: Nejčastější dotazy k technologiím Open Source
description: Získejte odpovědi na nejčastější dotazy týkající se Open Source technologií v Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831835"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy týkající se technologie Open source pro Web Apps v Azure

V tomto článku najdete odpovědi na nejčastější dotazy týkající se potíží s technologiemi open source pro [funkci Web Apps Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Návody zapnout protokolování PHP a vyřešit problémy s PHP?

Zapnutí protokolování PHP:

1. Přihlaste se k **webu Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. V horní nabídce vyberte **ladit konzolu**  >  **cmd**.
3. Vyberte složku **webu** .
4. Vyberte složku **wwwroot** .
5. Vyberte **+** ikonu a pak vyberte **nový soubor**.
6. Nastavte název souboru na **.user.ini**.
7. Vyberte ikonu tužky vedle **.user.ini**.
8. Do souboru přidejte tento kód: `log_errors=on`
9. Vyberte **Uložit**.
10. Vyberte ikonu tužky vedle **wp-config. php**.
11. Změňte text na následující kód:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. V Azure Portal v nabídce webové aplikace restartujte svou webovou aplikaci.

Další informace najdete v tématu [Povolení protokolů chyb služby WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Návody protokolovat chyby aplikace Python v aplikacích, které jsou hostovány v App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Návody změnit verzi aplikace Node.js, která je hostovaná v App Service?

Chcete-li změnit verzi aplikace Node.js, můžete použít jednu z následujících možností:

* V Azure Portal použijte **nastavení aplikace**.
  1. V Azure Portal přejdete do své webové aplikace.
  2. V okně **Nastavení** vyberte **nastavení aplikace**.
  3. Do **nastavení aplikace** můžete zahrnout WEBSITE_NODE_DEFAULT_VERSION jako klíč a Node.js, kterou chcete jako hodnotu.
  4. Přejít na **konzolu Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
  5. Chcete-li zjistit verzi Node.js, zadejte následující příkaz:  
     ```
     node -v
     ```
* Upravte soubor iisnode. yml. Změna verze Node.js v souboru iisnode. yml nastaví pouze běhové prostředí, které iisnode používá. Vaše Kudu cmd a jiné pořád používají verzi Node.js, která je nastavená v **nastavení aplikace** v Azure Portal.

  Pokud chcete ručně nastavit iisnode. yml, vytvořte v kořenové složce aplikace soubor iisnode. yml. Do souboru zadejte následující řádek:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Nastavte soubor iisnode. yml pomocí package.jsv průběhu nasazení správy zdrojového kódu.
  Proces nasazení správy zdrojových kódů Azure zahrnuje následující kroky:
  1. Přesune obsah do webové aplikace Azure.
  2. Vytvoří výchozí skript nasazení, pokud v kořenové složce webové aplikace není jeden (nasadit soubory. cmd,. nasazení).
  3. Spustí skript nasazení, ve kterém vytvoří soubor iisnode. yml, pokud zmiňujete Node.js verzi v modulu package.jssouboru >. `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Soubor iisnode. yml má následující řádek kódu:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>V aplikaci WordPress se zobrazí zpráva "Chyba při navazování připojení k databázi", která je hostovaná v App Service. Návody problém vyřešit?

Pokud se tato chyba zobrazí v aplikaci Azure WordPress a povolí php_errors. log a Debug. log, dokončete postup popsaný v tématu [Povolení protokolů chyb WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

Když jsou protokoly povolené, reprodukuje chybu a pak zkontrolujte protokoly, abyste zjistili, jestli nedochází k připojení:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Pokud se tato chyba zobrazuje v souborech Debug. log nebo php_errors. log, vaše aplikace překračuje počet připojení. Pokud jste hostitelem na ClearDB, ověřte počet připojení, která jsou k dispozici v [plánu služby](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Návody ladit aplikaci Node.js, která je hostovaná v App Service?

1.  Přejít na **konzolu Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole` ).
2.  Přejít do složky protokoly aplikací (D:\home\LogFiles\Application).
3.  V souboru logging_errors.txt vyhledejte obsah.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Návody nainstalovat nativní moduly Pythonu do webové aplikace App Service nebo do aplikace API?

Některé balíčky se nemusí nainstalovat pomocí PIP v Azure. Balíček nemusí být k dispozici v indexu balíčku Pythonu, nebo může být vyžadován kompilátor (kompilátor není k dispozici v počítači, na kterém běží webová aplikace v App Service). Informace o instalaci nativních modulů v App Service Web Apps a API Apps najdete v tématu [Instalace modulů Pythonu v App Service](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Návody nasadit aplikaci Django pro App Service pomocí Gitu a nové verze Pythonu?

Informace o instalaci Django najdete v tématu [nasazení aplikace Django pro App Service](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python).

## <a name="where-are-the-tomcat-log-files-located"></a>Kde se nacházejí soubory protokolu Tomcat?

Pro Azure Marketplace a vlastní nasazení:

* Umístění složky: D:\home\site\wwwroot\bin\apache-Tomcat-8.0.33\logs
* Zajímavé soubory:
    * catalina. *RRRR-MM-DD*. log
    * Správce hostitele. *RRRR-MM-DD*. log
    * místního. *RRRR-MM-DD*. log
    * programu. *RRRR-MM-DD*. log
    * site_access_log. *RRRR-MM-DD*. log


Pro nasazení portálových **nastavení aplikace** :

* Umístění složky: D:\home\LogFiles
* Zajímavé soubory:
    * catalina. *RRRR-MM-DD*. log
    * Správce hostitele. *RRRR-MM-DD*. log
    * místního. *RRRR-MM-DD*. log
    * programu. *RRRR-MM-DD*. log
    * site_access_log. *RRRR-MM-DD*. log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Návody řešit potíže s připojením ovladače JDBC?

V protokolech Tomcat se může zobrazit následující zpráva:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Řešení této chyby:

1. Odstraňte soubor Sqljdbc*. jar ze složky App/lib.
2. Pokud používáte vlastní webový server Tomcat nebo Azure Marketplace Tomcat, zkopírujte tento soubor. jar do složky Tomcat lib.
3. Pokud povolíte Java z Azure Portal (vyberte Tomcat server **Java 1,8**  >  ), zkopírujte soubor Sqljdbc. * jar do složky, která je pro vaši aplikaci rovnoběžná. Pak přidejte následující nastavení classpath do souboru web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Proč se při pokusu o zkopírování souborů protokolu v reálném čase zobrazí chyby?

Pokud se pokusíte zkopírovat soubory protokolu pro aplikaci Java (například Tomcat), může se zobrazit tato chyba FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Chybová zpráva se může lišit v závislosti na klientovi FTP.

Tento problém se zámkem mají všechny aplikace Java. Jenom Kudu podporuje stažení tohoto souboru, když je aplikace spuštěná.

Zastavování aplikace umožňuje přístup k těmto souborům přes FTP.

Dalším řešením je psaní webové úlohy, která běží podle plánu, a kopíruje tyto soubory do jiného adresáře. Ukázkový projekt naleznete v projektu [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) .

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Kde najdu soubory protokolů pro Jetty?

Pro Marketplace a vlastní nasazení se soubor protokolu nachází ve složce D:\home\site\wwwroot\bin\jetty-Distribution-9.1.2.v20140210\logs. Všimněte si, že umístění složky závisí na verzi Jetty, kterou používáte. Zde uvedená cesta je například určena pro Jetty 9.1.2. Vyhledejte jetty_ *YYYY_MM_DD*. stderr. log.

V případě nasazení portálových aplikací je soubor protokolu v D:\home\LogFiles.. Hledat jetty_ *YYYY_MM_DD*. stderr. log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Můžu poslat e-mail z webové aplikace Azure?

App Service nemá vestavěnou e-mailovou funkci. Některé dobré alternativy pro posílání e-mailů z vaší aplikace najdete v této [Stack Overflow diskuzi](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Proč je můj web WordPress přesměrován na jinou adresu URL?

Pokud jste v poslední době migrovali do Azure, WordPress se může přesměrovat na starou adresu URL domény. To je způsobeno nastavením v databázi MySQL.

WordPress kamarád + je rozšířením webu Azure, které můžete použít k aktualizaci adresy URL pro přesměrování přímo v databázi. Další informace o používání programu WordPress kamarád + najdete v tématu věnovaném [nástrojům WordPress a migraci MySQL pomocí programu WordPress kamarád +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

Případně, pokud upřednostňujete ruční aktualizaci adresy URL pro přesměrování pomocí dotazů SQL nebo PHPMyAdmin, podívejte se na článek [WordPress: přesměrování na špatnou adresu URL](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Návody změnit přihlašovací heslo pro WordPress?

Pokud jste zapomněli heslo pro přihlášení ke službě WordPress, můžete ho aktualizovat pomocí služby WordPress kamarád +. Pokud chcete resetovat heslo, nainstalujte rozšíření WordPress kamarád + Azure a pak dokončete postup popsaný v tématu [nástroje a migrace MySQL pomocí programu WordPress kamarád +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nemůžu se přihlásit k WordPress. Jak to můžu vyřešit?

Pokud se po poslední instalaci modulu plug-in v programu WordPress nacházíte uzamčeno, může být možné, že máte vadný modul plug-in. WordPress kamarád + je rozšířením webu Azure, které vám může pomáhat s zakázáním modulů plug-in v WordPress. Další informace najdete v tématech [nástroje WordPress a migrace MySQL pomocí programu WordPress kamarád +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Návody migrovat databázi WordPress?

K migraci databáze MySQL, která je připojená k webu WordPress, máte několik možností:

* Vývojáři: použijte [příkazový řádek nebo phpMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service)
* Nevývojáři: použití [WordPress kamarád +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Návody pomůžete WordPress lépe zabezpečit?

Další informace o osvědčených postupech zabezpečení pro WordPress najdete v tématu [osvědčené postupy pro zabezpečení WordPress v Azure](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Snažím se používat PHPMyAdmin a zobrazí se zpráva "přístup byl odepřen". Jak to můžu vyřešit?

K tomuto problému může dojít, pokud funkce MySQL v aplikaci v této instanci App Service ještě není spuštěná. Problém vyřešíte tak, že se pokusíte o přístup k webu. Tím se spustí požadované procesy, včetně procesu MySQL v aplikaci. Chcete-li ověřit, zda je spuštěn MySQL v aplikaci, v Průzkumníkovi procesů ověřte, zda je v rámci procesů uvedena mysqld.exe.

Až se ujistíte, že je spuštěný MySQL v aplikaci, zkuste použít PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Při pokusu o import nebo export databáze MySQL v aplikaci pomocí PHPMyadmin se zobrazí chyba HTTP 403. Jak to můžu vyřešit?

Pokud používáte starší verzi Chrome, může se stát, že se vyskytla známá chyba. Pokud chcete tento problém vyřešit, upgradujte na novější verzi Chrome. Zkuste také použít jiný prohlížeč, jako je Internet Explorer nebo Microsoft Edge, kde k problému nedochází.