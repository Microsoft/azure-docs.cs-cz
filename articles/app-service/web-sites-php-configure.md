---
title: Konfigurace modulu runtime PHP
description: Přečtěte si, jak nakonfigurovat výchozí instalaci PHP nebo přidat vlastní instalaci PHP pro Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c73fb55e485d0c92d27eac2ac197a81337b9d5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016795"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurace PHP ve službě Azure App Service

## <a name="introduction"></a>Úvod

Tato příručka ukazuje, jak nakonfigurovat vestavěný běh ový čas PHP pro webové aplikace, mobilní back-endy a aplikace rozhraní API ve [službě Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), poskytnout vlastní běhový čas PHP a povolit rozšíření. Chcete-li používat službu App Service, zaregistrujte si [bezplatnou zkušební verzi]. Chcete-li získat co nejvíce z této příručky, měli byste nejprve vytvořit aplikaci PHP v App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Postup: Změna vestavěné verze PHP

Ve výchozím nastavení je PHP 5.6 nainstalováno a okamžitě k dispozici pro použití při vytváření aplikace App Service. Nejlepší způsob, jak zobrazit dostupnou revizi vydání, její výchozí konfiguraci a povolené rozšíření, je nasadit skript, který volá funkci [phpinfo().]

Verze PHP 7.0 a PHP 7.2 jsou také k dispozici, ale ve výchozím nastavení nejsou povoleny. Chcete-li aktualizovat verzi PHP, postupujte podle jedné z těchto metod:

### <a name="azure-portal"></a>portál Azure

1. Přejděte do své aplikace na [webu Azure Portal](https://portal.azure.com) a přejděte na stránku **Konfigurace.**

2. V **části Konfigurace**vyberte Obecné **nastavení** a zvolte novou verzi PHP.

3. Klepněte na tlačítko **Uložit** v horní části okna **Obecné nastavení.**

### <a name="azure-cli"></a>Azure CLI 

Chcete-li používat rozhraní příkazového řádku Azure, musíte do počítače [nainstalovat rozhraní příkazového řádku Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

1. Otevřete terminál a přihlaste se ke svému účtu.

        az login

1. Zkontrolujte, zda chcete zobrazit seznam podporovaných modulů runtimes.

        az webapp list-runtimes | grep php

2. Nastavte verzi PHP pro aplikaci.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. Verze PHP je nyní nastavena. Můžete potvrdit tato nastavení:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Postup: Změna vestavěných konfigurací PHP

U jakéhokoli vestavěného běhu PHP můžete změnit některou z možností konfigurace pomocí následujících kroků. (Informace o směrnicích php.ini naleznete v [seznamu směrnic php.ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Změna\_PHP\_INI\_USER,\_PHP INI PERDIR, PHP\_INI\_VŠECHNA nastavení konfigurace

1. Přidejte soubor [.user.ini] do kořenového adresáře.
1. Přidejte nastavení `.user.ini` konfigurace do souboru pomocí stejné `php.ini` syntaxe, jakou byste použili v souboru. Pokud byste například chtěli zapnout `display_errors` nastavení `upload_max_filesize` a nastavit nastavení `.user.ini` na 10 M, soubor by obsahoval tento text:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Nasaďte aplikaci.
3. Restartujte aplikaci. (Restartování je nezbytné, protože frekvence, `.user.ini` s jakou `user_ini.cache_ttl` PHP čte soubory, se řídí nastavením, což je nastavení systémové úrovně a ve výchozím nastavení je 300 sekund (5 minut). Restartování aplikace nutí PHP číst nové nastavení `.user.ini` v souboru.)

Jako alternativu k `.user.ini` použití souboru můžete použít funkci [ini_set()] ve skriptech k nastavení možností konfigurace, které nejsou direktivy na úrovni systému.

### <a name="changing-php_ini_system-configuration-settings"></a>Změna\_nastavení\_konfigurace SYSTÉMU PHP INI

1. Přidání nastavení aplikace do aplikace `PHP_INI_SCAN_DIR` s klíčem a hodnotou`d:\home\site\ini`
1. Vytvořte `settings.ini` soubor pomocí konzoly&lt;Kudu&gt;(http:// název `d:\home\site\ini` webu .scm.azurewebsite.net) v adresáři.
1. Přidejte nastavení `settings.ini` konfigurace do souboru pomocí stejné `php.ini` syntaxe, jakou byste použili v souboru. Pokud byste například chtěli `curl.cainfo` nasměrovat `*.crt` nastavení na soubor a nastavit nastavení wincache.maxfilesize `settings.ini` na 512 kM, soubor by obsahoval tento text:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Chcete-li změny znovu načíst, restartujte aplikaci.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Postup: Povolení rozšíření ve výchozím běhu PHP

Jak je uvedeno v předchozí části, nejlepší způsob, jak vidět výchozí verzi PHP, jeho výchozí konfiguraci a povolené rozšíření, je nasadit skript, který volá [phpinfo()]. Chcete-li povolit další rozšíření, postupujte takto:

### <a name="configure-via-ini-settings"></a>Konfigurace pomocí nastavení ini

1. Přidejte `ext` do `d:\home\site` adresáře adresáře adresář.
1. Vložte `.dll` soubory `ext` rozšíření do adresáře `php_xdebug.dll`(například). Ujistěte se, že rozšíření jsou kompatibilní s výchozí verzí PHP a jsou kompatibilní s VC9 a nepro přístup k vláknům (nts).
1. Přidání nastavení aplikace do aplikace `PHP_INI_SCAN_DIR` s klíčem a hodnotou`d:\home\site\ini`
1. Vytvořte `ini` soubor `d:\home\site\ini` `extensions.ini`s názvem .
1. Přidejte nastavení `extensions.ini` konfigurace do souboru pomocí stejné `php.ini` syntaxe, jakou byste použili v souboru. Pokud byste například chtěli povolit přípony MongoDB a `extensions.ini` XDebug, soubor by obsahoval tento text:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Chcete-li načíst změny, restartujte aplikaci.

### <a name="configure-via-app-setting"></a>Konfigurace pomocí nastavení aplikace

1. Přidejte `bin` adresář do kořenového adresáře.
2. Vložte `.dll` soubory `bin` rozšíření do adresáře `php_xdebug.dll`(například). Ujistěte se, že rozšíření jsou kompatibilní s výchozí verzí PHP a jsou kompatibilní s VC9 a nepro přístup k vláknům (nts).
3. Nasaďte aplikaci.
4. Přejděte do své aplikace na webu Azure Portal a klikněte na **část Konfigurace** uvedená **pod** nastavením.
5. V okně **Konfigurace** vyberte **Nastavení aplikace**.
6. V části **Nastavení aplikace** klikněte na + Nové **nastavení aplikace** a vytvořte **PHP_EXTENSIONS** klíč. Hodnota tohoto klíče by byla cesta vzhledem ke kořenu webu: **bin\your-ext-file**.
7. Klikněte na tlačítko **Aktualizovat** dole a potom klikněte na **Uložit** nad kartou **Nastavení aplikace.**

Zend rozšíření jsou také podporovány pomocí **PHP_ZENDEXTENSIONS** klíč. Chcete-li povolit více rozšíření, zahrňte seznam souborů oddělených `.dll` čárkami pro hodnotu nastavení aplikace.

## <a name="how-to-use-a-custom-php-runtime"></a>Postup: Použití vlastního runtime PHP

Namísto výchozího runtime PHP může služba App Service použít runtime PHP, který poskytnete ke spuštění skriptů PHP. Za běhu, který zadáte, lze `php.ini` nakonfigurovat souborem, který také zadáte. Chcete-li použít vlastní php runtime se službou App Service, postupujte podle těchto kroků.

1. Získejte verzi PHP PHP pro Windows kompatibilní s technologií VC9 nebo VC11, která není bezpečná pro přístup z více vláken. Nedávné verze PHP pro Windows lze [https://windows.php.net/download/]nalézt zde: . Starší verze naleznete v archivu [https://windows.php.net/downloads/releases/archives/]zde: .
2. Upravte `php.ini` soubor pro váš runtime. Všechna nastavení konfigurace, které jsou pouze na úrovni systému direktivy jsou ignorovány službou App Service. (Informace o směrnicích pouze na úrovni systému naleznete v [seznamu směrnic php.ini]).
3. Volitelně můžete přidat rozšíření do běhu PHP a `php.ini` povolit je v souboru.
4. Přidejte `bin` adresář do kořenového adresáře a vložte do něj adresář, `bin\php`který obsahuje váš runtime PHP (například ).
5. Nasaďte aplikaci.
6. Přejděte do své aplikace na webu Azure Portal a klikněte na okno **Konfigurace.**
8. V okně **Konfigurace** vyberte **mapování cesty**. 
9. Klepněte na **tlačítko + Nová obslužná rutina** a přidejte `*.php` do pole Rozšíření a přidejte cestu ke spustitelnému souboru `php-cgi.exe` v **procesoru Skript**. Pokud vložíte runtime PHP `bin` do adresáře v kořenovém `D:\home\site\wwwroot\bin\php\php-cgi.exe`adresáři aplikace, cesta je .
10. V dolní části klikněte na **Aktualizovat** a dokončete přidávání mapování obslužné rutiny.
11. Uložte změny kliknutím na **Uložit**.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Postup: Povolení automatizace skladatelů v Azure

Ve výchozím nastavení app service nedělá nic s composer.json, pokud máte jeden v projektu PHP. Pokud používáte [nasazení Git](deploy-local-git.md), můžete povolit `git push` composer.json zpracování během povolením composer rozšíření.

> [!NOTE]
> Můžete [hlasovat pro prvotřídní podporu Composer v App Service zde!](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)
>

1. V okně aplikace PHP na [webu Azure Portal](https://portal.azure.com)klikněte na**Rozšíření** **nástrojů** > .

    ![Okno nastavení portálu Azure, které umožňuje automatizaci composerv azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klepněte na tlačítko **Přidat**a potom klepněte na tlačítko **Skladatel .**

    ![Přidání rozšíření Composer pro povolení automatizace skladatelů v Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Chcete-li přijmout zákonné podmínky, klepněte na tlačítko **OK.** Dalším klepnutím na **tlačítko OK** přidáte rozšíření.

    Okno **Nainstalované rozšíření** zobrazuje rozšíření Composer.
    ![Přijměte právní podmínky, které umožní automatizaci composer v Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Nyní v okně terminálu v místním `git add` `git commit`počítači `git push` proveďte , a do aplikace. Všimněte si, že Composer instaluje závislosti definované v souboru composer.json.

    ![Nasazení Gitu s automatizací composer v Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Další kroky

Další informace naleznete v [centru pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[bezplatná zkušební verze]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Seznam směrnic php.ini]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
