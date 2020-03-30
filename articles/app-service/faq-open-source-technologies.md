---
title: Časté otázky k otevřeným zdrojům
description: Získejte odpovědi na nejčastější dotazy týkající se open source technologií ve službě Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 33590f9fc7e6c4d46123cbc7088086a3197d52ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672426"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy k technologiím s otevřeným zdrojovým kódem pro webové aplikace v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se problémů s open source technologiemi pro [funkci Webové aplikace služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Jak zapnu protokolování PHP, abych mohl řešit problémy s PHP?

Zapnutí protokolování PHP:

1. Přihlaste se na [své webové stránky Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. V horní nabídce vyberte **možnost Ladění konzoly** > **CMD**.
3. Vyberte složku **Web.**
4. Vyberte složku **wwwroot.**
5. Vyberte **+** ikonu a pak vyberte **Nový soubor**.
6. Nastavte název souboru na **soubor .user.ini**.
7. Vyberte ikonu tužky vedle **souboru .user.ini**.
8. Do souboru přidejte tento kód:`log_errors=on`
9. Vyberte **Uložit**.
10. Vyberte ikonu tužky vedle **wp-config.php**.
11. Změňte text na následující kód:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Na webu Azure Portal v nabídce webové aplikace restartujte webovou aplikaci.

Další informace naleznete v [tématu Enable WordPress error logs](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Jak lze protokolovat chyby aplikací Pythonu v aplikacích, které jsou hostované ve službě App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Jak změním verzi aplikace Node.js, která je hostovaná ve službě App Service?

Chcete-li změnit verzi aplikace Node.js, můžete použít jednu z následujících možností:

* Na webu Azure Portal použijte **nastavení aplikací**.
  1. Na webu Azure Portal přejděte do webové aplikace.
  2. V okně **Nastavení** vyberte **Nastavení aplikace**.
  3. V **nastavení aplikace**můžete jako klíč zahrnout WEBSITE_NODE_DEFAULT_VERSION a jako hodnotu požadovanou verzi souboru Node.js.
  4. Přejděte do [konzole Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
  5. Chcete-li zkontrolovat verzi souboru Node.js, zadejte následující příkaz:  
     ```
     node -v
     ```
* Upravte soubor iisnode.yml. Změna verze Node.js v souboru iisnode.yml pouze nastaví prostředí runtime, které používá iisnode. Vaše Kudu cmd a další stále používají verzi Node.js, která je nastavená v **nastavení aplikace** na webu Azure Portal.

  Chcete-li soubor iisnode.yml nastavit ručně, vytvořte soubor iisnode.yml v kořenové složce aplikace. Do souboru zahrňte následující řádek:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Nastavte soubor iisnode.yml pomocí souboru package.json během nasazení správy zdrojového kódu.
  Proces nasazení správy zdrojového kódu Azure zahrnuje následující kroky:
  1. Přesune obsah do webové aplikace Azure.
  2. Vytvoří výchozí skript nasazení, pokud není jeden (deploy.cmd, .deployment files) v kořenové složce webové aplikace.
  3. Spustí skript nasazení, ve kterém vytvoří soubor iisnode.yml, pokud zmíníte verzi Node.js v souboru package.json > motoru`"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Soubor iisnode.yml má následující řádek kódu:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>V aplikaci WordPress, která je hostovaná v app service, se zobrazuje zpráva "Chyba při navazování připojení k databázi". Jak lze tento problém vyřešit?

Pokud se tato chyba zobrazí v aplikaci Azure WordPress, chcete-li povolit php_errors.log a debug.log, proveďte kroky popsané v [povolit protokoly chyb WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Pokud jsou protokoly povoleny, reprodukovat chybu a potom zkontrolujte protokoly, zda jsou spuštěny připojení:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Pokud se tato chyba zobrazí v souborech debug.log nebo php_errors.log, vaše aplikace překračuje počet připojení. Pokud hostujete na ClearDB, ověřte počet připojení, která jsou k dispozici ve vašem [plánu služeb](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Jak lze ladit aplikaci Node.js, která je hostovaná ve službě App Service?

1.  Přejděte do [konzole Kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Přejděte do složky protokolů aplikace (D:\home\LogFiles\Application).
3.  V souboru logging_errors.txt zkontrolujte obsah.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Jak nainstaluji nativní moduly Pythonu do webové aplikace App Service nebo api?

Některé balíčky se nemusí nainstalovat pomocí pipu v Azure. Balíček nemusí být k dispozici v indexu balíčků Pythonu nebo může být vyžadován kompilátor (kompilátor není k dispozici v počítači, ve který chodwebové aplikace ve službě App Service). Informace o instalaci nativních modulů ve webových aplikacích Služby App Service a aplikacích rozhraní API najdete [v tématu Instalace modulů Pythonu ve službě App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Jak nasadím aplikaci Django do služby App Service pomocí Gitu a nové verze Pythonu?

Informace o instalaci Django najdete [v tématu nasazení aplikace Django do služby App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Kde jsou umístěny soubory protokolu Tomcat?

Pro Azure Marketplace a vlastní nasazení:

* Umístění složky: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Spisy zájmu:
    * Catalina. *yyyy-mm-dd*.log
    * host-manager. *yyyy-mm-dd*.log
    * Localhost. *yyyy-mm-dd*.log
    * Správce. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log


Pro nasazení **nastavení portálových aplikací:**

* Umístění složky: D:\home\LogFiles
* Spisy zájmu:
    * Catalina. *yyyy-mm-dd*.log
    * host-manager. *yyyy-mm-dd*.log
    * Localhost. *yyyy-mm-dd*.log
    * Správce. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Jak lze odstranit chyby připojení ovladače JDBC?

V protokolech Tomcat se může zobrazit následující zpráva:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Chcete-li chybu vyřešit:

1. Odeberte soubor sqljdbc*.jar ze složky aplikace/lib.
2. Pokud používáte vlastní webový server Tomcat nebo Azure Marketplace Tomcat, zkopírujte tento soubor .jar do složky Lib Tomcat.
3. Pokud povolujete Jazyk Java z webu Azure (vyberte **Java 1.8** > **Tomcat server**), zkopírujte soubor sqljdbc.* jar ve složce, která je rovnoběžná s vaší aplikací. Potom přidejte do souboru web.config následující nastavení cesty pro třídy:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Proč se při pokusu o kopírování živých souborů protokolu zobrazují chyby?

Pokud se pokusíte zkopírovat živé soubory protokolu pro aplikaci Java (například Tomcat), může se zobrazit tato chyba FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Chybová zpráva se může lišit v závislosti na klientovi FTP.

Všechny java aplikace mají tento problém se zamykáním. Pouze Kudu podporuje stahování tohoto souboru, když je aplikace spuštěna.

Zastavení aplikace umožňuje přístup FTP k těmto souborům.

Dalším zástupem je napsat webovou úlohu, která běží podle plánu, a zkopíruje tyto soubory do jiného adresáře. Ukázkový projekt naleznete v projektu [CopyLogsJob.](https://github.com/kamilsykora/CopyLogsJob)

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Kde najdu soubory protokolu pro Jetty?

Pro vlastní nasazení marketplace a vlastní nasazení je soubor protokolu ve složce D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Všimněte si, že umístění složky závisí na verzi Jetty, kterou používáte. Zde uvedená cesta je například pro molo 9.1.2. Vyhledejte jetty_*YYYY_MM_DD*.stderrout.log.

Pro nasazení nastavení aplikace portálu je soubor protokolu v souboru D:\home\LogFiles. Vyhledejte jetty_*YYYY_MM_DD*.stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Můžu posílat e-maily ze své webové aplikace Azure?

Služba App Service nemá integrovanou funkci e-mailu. Některé dobré alternativy pro odesílání e-mailů z aplikace najdete v této [diskusi přetečení zásobníku](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Proč můj web WordPress přesměruje na jinou adresu URL?

Pokud jste nedávno migrovali do Azure, WordPress může přesměrovat na starou adresu URL domény. To je způsobeno nastavením v databázi MySQL.

WordPress Buddy+ je rozšíření webu Azure, které můžete použít k aktualizaci adresy URL přesměrování přímo v databázi. Další informace o používání WordPress Buddy+ najdete v [tématu WordPress nástroje a migrace MySQL s WordPress Buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

Případně, pokud dáváte přednost ruční aktualizaci adresy URL přesměrování pomocí dotazů SQL nebo PHPMyAdmin, viz [WordPress: Přesměrování na nesprávnou adresu URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Jak změním přihlašovací heslo wordpressu?

Pokud jste zapomněli své přihlašovací heslo WordPress, můžete jej aktualizovat pomocí WordPress Buddy +. Chcete-li obnovit heslo, nainstalujte rozšíření WordPress Buddy+ Azure Site Extension a pak proveďte kroky popsané v [nástrojích WordPress a migraci MySQL pomocí aplikace WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nemohu se přihlásit k WordPress. Jak to můžu vyřešit?

Pokud se ocitnete uzamčen z WordPress po nedávné instalaci pluginu, můžete mít vadný plugin. WordPress Buddy + je rozšíření webu Azure, které vám pomůže zakázat pluginy ve WordPressu. Další informace naleznete v [tématu WordPress tools and MySQL migration with WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Jak mohu migrovat svou databázi WordPress?

Máte několik možností pro migraci databáze MySQL, která je připojena k webu WordPress:

* Vývojáři: Použijte [příkazový řádek nebo PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Nevývojáři: Použijte [WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Jak mohu pomoci, aby wordpress bezpečnější?

Informace o doporučených postupech zabezpečení pro WordPress najdete [v tématu Doporučené postupy pro zabezpečení WordPressu v Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Snažím se používat PHPMyAdmin, a vidím zprávu "Přístup odepřen." Jak to můžu vyřešit?

K tomuto problému může dojít, pokud funkce MySQL v aplikaci ještě není spuštěna v této instanci služby App Service. Chcete-li problém vyřešit, zkuste získat přístup k webu. Tím se spustí požadované procesy, včetně procesu MySQL v aplikaci. Chcete-li ověřit, že MySQL v aplikaci je spuštěna, v Průzkumníku procesů, ujistěte se, že mysqld.exe je uveden v procesech.

Poté, co se uvidíte, že MySQL in-app je spuštěn, zkuste použít PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Při pokusu o import nebo export databáze aplikace MySQL pomocí PHPMyadmin se zobrazuje chyba HTTP 403. Jak to můžu vyřešit?

Pokud používáte starší verzi Chromu, může se jednat o známou chybu. Chcete-li tento problém vyřešit, upgradujte na novější verzi Chromu. Zkuste také použít jiný prohlížeč, například Internet Explorer nebo Microsoft Edge, kde k problému nedochází.
