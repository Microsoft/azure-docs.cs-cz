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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016795"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurace PHP v Azure App Service

## <a name="introduction"></a>Představení

V této příručce se dozvíte, jak nakonfigurovat integrovaný modul runtime PHP pro webové aplikace, mobilní back-endy a aplikace API v [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), poskytují vlastní modul runtime php a rozšíření. Pokud chcete použít App Service, zaregistrujte se do [bezplatné zkušební verze]. Pokud chcete z této příručky získat maximum, měli byste nejdřív vytvořit aplikaci PHP v App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Postupy: Změna předdefinované verze PHP

Ve výchozím nastavení je PHP 5,6 nainstalovaný a hned se k dispozici pro použití při vytváření aplikace App Service. Nejlepším způsobem, jak zobrazit dostupnou revizi verze, její výchozí konfiguraci a povolená rozšíření, je nasadit skript, který volá funkci [phpinfo ()] .

K dispozici jsou také verze PHP 7,0 a PHP 7,2, ale nejsou ve výchozím nastavení povolené. K aktualizaci verze PHP použijte jednu z těchto metod:

### <a name="azure-portal"></a>Portál Azure

1. Přejděte do aplikace v [Azure Portal](https://portal.azure.com) a přejděte na stránku **Konfigurace** .

2. V části **Konfigurace**vyberte **Obecné nastavení** a zvolte novou verzi PHP.

3. Klikněte na tlačítko **Uložit** v horní části okna **Obecné nastavení** .

### <a name="azure-cli"></a>Azure CLI 

Pokud chcete používat rozhraní příkazového řádku Azure, musíte na svém počítači [nainstalovat Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

1. Otevřete terminál a přihlaste se ke svému účtu.

        az login

1. Zkontrolujte, zda se zobrazí seznam podporovaných modulů runtime.

        az webapp list-runtimes | grep php

2. Nastavte verzi PHP pro aplikaci.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. Verze PHP je teď nastavená. Můžete potvrdit tato nastavení:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Postupy: Změna předdefinovaných konfigurací PHP

Pro jakýkoli integrovaný modul runtime PHP můžete změnit kteroukoli z možností konfigurace pomocí následujících kroků. (Informace o direktivách php. ini najdete v tématu [seznam direktiv php. ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Změna uživatele\_PHP\_INI, PHP\_INI\_PERDIR, PHP\_INI\_všechna nastavení konfigurace

1. Přidejte soubor [. User. ini] do kořenového adresáře.
1. Do souboru `.user.ini` Přidejte konfigurační nastavení pomocí stejné syntaxe, kterou použijete v souboru `php.ini`. Pokud jste například chtěli zapnout nastavení `display_errors` a nastavit `upload_max_filesize` nastavení na 10 milionů, bude soubor `.user.ini` obsahovat tento text:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Nasaďte aplikaci.
3. Restartujte aplikaci. (Restartování je nezbytné, protože frekvence, s jakou PHP čte `.user.ini` soubory, se řídí nastavením `user_ini.cache_ttl`, což je nastavení na úrovni systému a ve výchozím nastavení je 300 sekund (5 minut). Restartování aplikace vynutí, aby PHP načetla nové nastavení v souboru `.user.ini`.)

Jako alternativu k používání souboru `.user.ini` můžete použít funkci [ini_set ()] ve skriptech k nastavení možností konfigurace, které nejsou direktivami na úrovni systému.

### <a name="changing-php_ini_system-configuration-settings"></a>Změna nastavení konfigurace systému v\_formátu PHP\_

1. Přidejte do aplikace nastavení aplikace s klíčovým `PHP_INI_SCAN_DIR` a hodnotou `d:\home\site\ini`
1. Vytvořte soubor `settings.ini` pomocí konzoly Kudu (http://&lt;název lokality&gt;. scm.azurewebsite.net) v adresáři `d:\home\site\ini`.
1. Do souboru `settings.ini` Přidejte konfigurační nastavení pomocí stejné syntaxe, kterou použijete v souboru `php.ini`. Pokud byste například chtěli nasměrovat nastavení `curl.cainfo` na `*.crt` soubor a nastavit nastavení ' WinCache. MaxFileSize ' na hodnotu 512K, bude soubor `settings.ini` obsahovat tento text:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Pokud chcete změny znovu načíst, restartujte aplikaci.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Postupy: povolení rozšíření ve výchozím modulu runtime PHP

Jak je uvedeno v předchozí části, nejlepší způsob, jak zobrazit výchozí verzi PHP, její výchozí konfiguraci a povolená rozšíření, je nasazení skriptu, který volá [phpinfo ()]. Pokud chcete povolit další rozšíření, postupujte podle těchto kroků:

### <a name="configure-via-ini-settings"></a>Konfigurace prostřednictvím nastavení ini

1. Přidejte `ext` adresář do adresáře `d:\home\site`.
1. Vložte `.dll` soubory rozšíření do adresáře `ext` (například `php_xdebug.dll`). Ujistěte se, že jsou rozšíření kompatibilní s výchozí verzí PHP a že jsou kompatibilní s VC9 a bez NTS (non-Thread-Safe).
1. Přidejte do aplikace nastavení aplikace s klíčovým `PHP_INI_SCAN_DIR` a hodnotou `d:\home\site\ini`
1. Vytvoří soubor `ini` v `d:\home\site\ini` s názvem `extensions.ini`.
1. Do souboru `extensions.ini` Přidejte konfigurační nastavení pomocí stejné syntaxe, kterou použijete v souboru `php.ini`. Pokud jste například chtěli povolit rozšíření MongoDB a nástroje Xdebug, bude soubor `extensions.ini` obsahovat tento text:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Pokud chcete načíst změny, restartujte aplikaci.

### <a name="configure-via-app-setting"></a>Konfigurace prostřednictvím nastavení aplikace

1. Přidejte `bin` adresář do kořenového adresáře.
2. Vložte `.dll` soubory rozšíření do adresáře `bin` (například `php_xdebug.dll`). Ujistěte se, že jsou rozšíření kompatibilní s výchozí verzí PHP a že jsou kompatibilní s VC9 a bez NTS (non-Thread-Safe).
3. Nasaďte aplikaci.
4. V Azure Portal přejděte do své aplikace a klikněte na oddíl **Konfigurace** umístěný pod **nastavením** .
5. V okně **Konfigurace** vyberte **nastavení aplikace**.
6. V části **nastavení aplikace** klikněte na **+ nové nastavení aplikace** a vytvořte **PHP_EXTENSIONS** klíč. Hodnota tohoto klíče by byla cesta relativní k kořenu webu: **bin\your-EXT-File**.
7. V dolní části klikněte na tlačítko **aktualizovat** a pak klikněte na **Uložit** nad kartou **nastavení aplikace** .

Rozšíření Zend jsou také podporována pomocí **PHP_ZENDEXTENSIONSho** klíče. Pokud chcete povolit více rozšíření, zahrňte čárkami oddělený seznam souborů `.dll` pro hodnotu nastavení aplikace.

## <a name="how-to-use-a-custom-php-runtime"></a>Postupy: použití vlastního modulu runtime PHP

Místo výchozího modulu runtime PHP může App Service použít modul runtime PHP, který zadáte pro spouštění skriptů PHP. Modul runtime, který poskytnete, se dá nakonfigurovat pomocí souboru `php.ini`, který zadáte zároveň. Pokud chcete použít vlastní modul runtime PHP s App Service, postupujte podle těchto kroků.

1. Získejte VC9 nebo VC11 kompatibilní verzi PHP pro Windows, která není bezpečná pro přístup z více vláken. Poslední verze PHP pro Windows najdete tady: [https://windows.php.net/download/]. Starší verze najdete v archivu tady: [https://windows.php.net/downloads/releases/archives/].
2. Upravte soubor `php.ini` pro modul runtime. Všechna nastavení konfigurace, která jsou direktivami pouze pro systém, jsou App Service ignorována. (Informace o direktivách týkajících se pouze systémové úrovně naleznete v tématu [seznam direktiv php. ini]).
3. Volitelně můžete přidat rozšíření pro modul runtime PHP a povolit je v souboru `php.ini`.
4. Přidejte adresář `bin` do kořenového adresáře a vložte do něj adresář, který obsahuje modul runtime PHP (například `bin\php`).
5. Nasaďte aplikaci.
6. Přejděte do aplikace v Azure Portal a klikněte na okno **Konfigurace** .
8. V okně **Konfigurace** vyberte **mapování cest**. 
9. Klikněte na **+ Nová obslužná rutina** a přidejte `*.php` do pole přípona a přidejte cestu ke spustitelnému souboru `php-cgi.exe` ve **skriptovacím procesoru**. Pokud zadáte modul runtime PHP do adresáře `bin` v kořenovém adresáři vaší aplikace, bude cesta `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. V dolní části klikněte na **aktualizovat** , aby se dokončilo přidávání mapování obslužných rutin.
11. Uložte změny kliknutím na **Uložit**.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Postupy: povolení automatizace skladatele v Azure

Ve výchozím nastavení App Service nedělá cokoli s skladatel. JSON, pokud ho máte v projektu PHP. Pokud používáte [nasazení Git](deploy-local-git.md), můžete povolit zpracování pomocí skladatele. json během `git push` povolením rozšíření skladatele.

> [!NOTE]
> Můžete [hlasovat o podpoře prvotřídního skladatele v App Service sem](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip).
>

1. V okně aplikace PHP v [Azure Portal](https://portal.azure.com)klikněte na **nástroje** > **rozšíření**.

    ![Okno nastavení Azure Portal umožňující automatizaci skladatele v Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klikněte na **Přidat**a pak na **skladatel**.

    ![Přidání rozšíření skladatele pro povolení automatizace skladatele v Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Kliknutím na **OK** přijměte právní podmínky. Opětovným kliknutím na tlačítko **OK** rozšíření přidejte.

    V okně **instalované přípony** se zobrazuje rozšíření skladatele.
    ![přijmout právní podmínky pro povolení automatizace skladatele v Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Nyní v okně terminálu na místním počítači proveďte `git add`, `git commit`a `git push` do své aplikace. Všimněte si, že skladatel instaluje závislosti definované v skladatele. JSON.

    ![Nasazení Git s automatizací skladatele v Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[bezplatné zkušební verze]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo ()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Seznam direktiv php. ini]: https://www.php.net/manual/en/ini.list.php
[. User. ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set ()]: https://www.php.net/manual/en/function.ini-set.php
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
