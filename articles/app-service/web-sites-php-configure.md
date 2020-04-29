---
title: Konfigurace modulu runtime PHP
description: Přečtěte si, jak nakonfigurovat výchozí instalaci PHP nebo přidat vlastní instalaci PHP pro Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 208f4f7b4c2d8562d5237a40f52e4774ea5c5606
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272470"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurace PHP v Azure App Service

## <a name="introduction"></a>Úvod

V této příručce se dozvíte, jak nakonfigurovat integrovaný modul runtime PHP pro webové aplikace a aplikace API v [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), poskytnout vlastní modul runtime php a povolit rozšíření. Pokud chcete použít App Service, zaregistrujte se do [bezplatné zkušební verze]. Pokud chcete z této příručky získat maximum, měli byste nejdřív vytvořit aplikaci PHP v App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Postupy: Změna předdefinované verze PHP

Při vytváření webové aplikace můžete zvolit verzi PHP, která se nakonfiguruje. Aktuální informace o aktuálně podporovaných verzích najdete v tématu [php on App Service](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md) .

Chcete-li zjistit existující verzi aplikace runtime, můžete nasadit skript, který volá funkci [phpinfo ()] .

K aktualizaci verze PHP použijte jednu z těchto metod:

### <a name="azure-portal"></a>portál Azure

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

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. Verze PHP je teď nastavená. Můžete potvrdit tato nastavení:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Postupy: Změna předdefinovaných konfigurací PHP

Pro jakýkoli integrovaný modul runtime PHP můžete změnit kteroukoli z možností konfigurace pomocí následujících kroků. (Informace o direktivách php. ini najdete v tématu [seznam direktiv php. ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Změna uživatele\_php\_ini, PHP\_ini\_PERDIR, php\_ini\_všechna nastavení konfigurace

1. Přidejte soubor [. User. ini] do kořenového adresáře.
1. Přidejte konfigurační nastavení do `.user.ini` souboru pomocí stejné syntaxe, jakou byste použili v `php.ini` souboru. Pokud jste například chtěli zapnout `display_errors` nastavení a nastavit `upload_max_filesize` nastavení na 10 milionů, `.user.ini` soubor by obsahoval tento text:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Nasaďte aplikaci.
3. Restartujte aplikaci. (Restart je nezbytný, protože frekvence, se kterou `.user.ini` se čte php souborů, řídí `user_ini.cache_ttl` nastavení, což je nastavení na úrovni systému a ve výchozím nastavení je 300 sekund (5 minut). Restartování aplikace vynutí, aby PHP načetla nová nastavení `.user.ini` v souboru.)

Jako alternativu k používání `.user.ini` souboru můžete použít funkci [ini_set ()] ve skriptech k nastavení možností konfigurace, které nejsou direktivami na úrovni systému.

### <a name="changing-php_ini_system-configuration-settings"></a>Změna nastavení\_konfigurace\_systému php ini

1. Přidání nastavení aplikace do aplikace s klíčovým `PHP_INI_SCAN_DIR` a hodnotou`d:\home\site\ini`
1. Vytvořte `settings.ini` soubor pomocí konzoly Kudu (http://&lt;site-name&gt;. SCM.azurewebsite.NET) v `d:\home\site\ini` adresáři.
1. Přidejte konfigurační nastavení do `settings.ini` souboru pomocí stejné syntaxe, jakou byste použili v `php.ini` souboru. Pokud byste například chtěli nasměrovat `curl.cainfo` nastavení na `*.crt` soubor a nastavit ' WinCache. MaxFileSize ' na 512 k, váš `settings.ini` soubor by obsahoval tento text:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Pokud chcete změny znovu načíst, restartujte aplikaci.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Postupy: povolení rozšíření ve výchozím modulu runtime PHP

Jak je uvedeno v předchozí části, nejlepší způsob, jak zobrazit výchozí verzi PHP, její výchozí konfiguraci a povolená rozšíření, je nasazení skriptu, který volá [phpinfo ()]. Pokud chcete povolit další rozšíření, postupujte podle těchto kroků:

### <a name="configure-via-ini-settings"></a>Konfigurace prostřednictvím nastavení ini

1. Přidejte `ext` adresář do `d:\home\site` adresáře.
1. Umístěte `.dll` soubory rozšíření do `ext` adresáře (například `php_xdebug.dll`). Ujistěte se, že jsou rozšíření kompatibilní s výchozí verzí PHP a že jsou kompatibilní s VC9 a bez NTS (non-Thread-Safe).
1. Přidání nastavení aplikace do aplikace s klíčovým `PHP_INI_SCAN_DIR` a hodnotou`d:\home\site\ini`
1. Vytvoří `ini` soubor s `d:\home\site\ini` názvem. `extensions.ini`
1. Přidejte konfigurační nastavení do `extensions.ini` souboru pomocí stejné syntaxe, jakou byste použili v `php.ini` souboru. Pokud jste například chtěli povolit rozšíření MongoDB a nástroje Xdebug, bude `extensions.ini` soubor obsahovat tento text:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Pokud chcete načíst změny, restartujte aplikaci.

### <a name="configure-via-app-setting"></a>Konfigurace prostřednictvím nastavení aplikace

1. Přidejte `bin` adresář do kořenového adresáře.
2. Umístěte `.dll` soubory rozšíření do `bin` adresáře (například `php_xdebug.dll`). Ujistěte se, že jsou rozšíření kompatibilní s výchozí verzí PHP a že jsou kompatibilní s VC9 a bez NTS (non-Thread-Safe).
3. Nasaďte aplikaci.
4. V Azure Portal přejděte do své aplikace a klikněte na oddíl **Konfigurace** umístěný pod **nastavením** .
5. V okně **Konfigurace** vyberte **nastavení aplikace**.
6. V části **nastavení aplikace** klikněte na **+ nové nastavení aplikace** a vytvořte **PHP_EXTENSIONS** klíč. Hodnota tohoto klíče by byla cesta relativní k kořenu webu: **bin\your-EXT-File**.
7. V dolní části klikněte na tlačítko **aktualizovat** a pak klikněte na **Uložit** nad kartou **nastavení aplikace** .

Rozšíření Zend jsou také podporována pomocí **PHP_ZENDEXTENSIONSho** klíče. Pokud chcete povolit více rozšíření, zahrňte čárkami oddělený `.dll` seznam souborů pro hodnotu nastavení aplikace.

## <a name="how-to-use-a-custom-php-runtime"></a>Postupy: použití vlastního modulu runtime PHP

Místo výchozího modulu runtime PHP může App Service použít modul runtime PHP, který zadáte pro spouštění skriptů PHP. Modul runtime, který poskytnete, se dá nakonfigurovat `php.ini` pomocí souboru, který zadáte zároveň. Pokud chcete použít vlastní modul runtime PHP s App Service, postupujte podle těchto kroků.

1. Získejte VC9 nebo VC11 kompatibilní verzi PHP pro Windows, která není bezpečná pro přístup z více vláken. Poslední verze PHP pro Windows najdete tady: [https://windows.php.net/download/]. Starší verze najdete v archivu tady: [https://windows.php.net/downloads/releases/archives/].
2. Upravte `php.ini` soubor pro modul runtime. Všechna nastavení konfigurace, která jsou direktivami pouze pro systém, jsou App Service ignorována. (Informace o direktivách týkajících se pouze systémové úrovně naleznete v tématu [seznam direktiv php. ini]).
3. Volitelně můžete přidat rozšíření pro modul runtime PHP a povolit je v `php.ini` souboru.
4. Přidejte `bin` adresář do kořenového adresáře a vložte do něj adresář, který obsahuje modul runtime php (například `bin\php`).
5. Nasaďte aplikaci.
6. Přejděte do aplikace v Azure Portal a klikněte na okno **Konfigurace** .
8. V okně **Konfigurace** vyberte **mapování cest**. 
9. Klikněte na **+ Nová obslužná rutina** a přidejte `*.php` do pole přípona a přidejte cestu `php-cgi.exe` ke spustitelnému souboru do **skriptovacího procesoru**. Pokud zadáte modul runtime PHP do `bin` adresáře v kořenovém adresáři vaší aplikace, cesta je. `D:\home\site\wwwroot\bin\php\php-cgi.exe`
10. V dolní části klikněte na **aktualizovat** , aby se dokončilo přidávání mapování obslužných rutin.
11. Uložte změny kliknutím na **Uložit**.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Postupy: povolení automatizace skladatele v Azure

Ve výchozím nastavení App Service nedělá cokoli s skladatel. JSON, pokud ho máte v projektu PHP. Pokud používáte [nasazení Git](deploy-local-git.md), můžete povolit zpracování `git push` pomocí skladatele. JSON tím, že povolíte rozšíření skladatele.

> [!NOTE]
> Můžete [hlasovat o podpoře prvotřídního skladatele v App Service sem](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip).
>

1. V okně aplikace PHP v [Azure Portal](https://portal.azure.com)klikněte na**rozšíření** **nástrojů** > .

    ![Okno nastavení Azure Portal umožňující automatizaci skladatele v Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klikněte na **Přidat**a pak na **skladatel**.

    ![Přidání rozšíření skladatele pro povolení automatizace skladatele v Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Kliknutím na **OK** přijměte právní podmínky. Opětovným kliknutím na tlačítko **OK** rozšíření přidejte.

    V okně **instalované přípony** se zobrazuje rozšíření skladatele.
    ![Přijmout právní podmínky pro povolení automatizace skladatele v Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Nyní můžete v okně terminálu na místním počítači provádět `git add`, `git commit`a `git push` do své aplikace. Všimněte si, že skladatel instaluje závislosti definované v skladatele. JSON.

    ![Nasazení Git s automatizací skladatele v Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[bezplatná zkušební verze]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
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
